
- SuccessStatForm
```C#

private void RedrawChart()
{
    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(fMain.cmsConnStr);

    if (search_comboBox.SelectedIndex == 0)
    {
        try
        {
            item_comboBox.Items.Clear();
            item_comboBox.Items.Add("성공률");

            sqlConn.Open();
            Console.WriteLine("데이터베이스 연결 성공...");

            strFindQuery = "SELECT * FROM ( ";
            strFindQuery += "SELECT '01'+0 AS ID, '전체' AS TBS, REG_DT, ROUND(SUM(SEND_SUCC) * 100.00 / SUM(SEND_CNT), 2) AS SEND_AVRG ";
            strFindQuery += "FROM DAY_MODEM_INFO ";
            strFindQuery += "WHERE SEND_AVRG IS NOT NULL AND ";
            strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            if (fMain.filtOnOff)
            {
                strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
            }
            strFindQuery += "AND SUCC_EXCEPT IS NULL ";
            strFindQuery += "GROUP BY REG_DT ";
            strFindQuery += "UNION ALL ";

            strFindQuery += "SELECT '99'+0 AS ID, RECV_TBS AS TBS, REG_DT, ROUND(SUM(SEND_SUCC) * 100.00 / SUM(SEND_CNT), 2) AS SEND_AVRG ";
            strFindQuery += "FROM DAY_MODEM_INFO ";
            strFindQuery += "WHERE SEND_AVRG IS NOT NULL AND ";
            strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            if (fMain.filtOnOff)
            {
                strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
            }
            strFindQuery += "AND SUCC_EXCEPT IS NULL ";
            strFindQuery += "GROUP BY REG_DT, RECV_TBS ) AS TBL ";
            strFindQuery += "ORDER BY REG_DT, ID, TBS";

            ds = new DataSet();
            SqlDataAdapter adapter = new SqlDataAdapter(strFindQuery, sqlConn);
            adapter.Fill(ds, "RESULT");

        }
        catch (SqlException sqlEx)
        {
            Console.WriteLine(sqlEx.Message);
        }
        finally
        {
            if (sqlConn != null)
            {
                sqlConn.Close(); sqlConn = null;
                Console.WriteLine("데이터베이스 연결 해제...");
            }
        }

        item_comboBox.SelectedIndex = 0;
    }
    else if (search_comboBox.SelectedIndex == 1)
    {
        try
        {
            item_comboBox.Items.Clear();
            
            sqlConn.Open();
            Console.WriteLine("데이터베이스 연결 성공...");

            strFindQuery = "SELECT '99'+0 AS ID, RECV_TBS AS TBS ";
            strFindQuery += "FROM DAY_MODEM_INFO ";
            strFindQuery += "WHERE REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            strFindQuery += "GROUP BY RECV_TBS ";
            strFindQuery += "ORDER BY RECV_TBS";

            sqlCmd = new SqlCommand(strFindQuery, sqlConn);
            sqlRdr = sqlCmd.ExecuteReader();

            item_comboBox.Items.Add("전체");

            while (sqlRdr.Read())
            {
                item_comboBox.Items.Add(sqlRdr[1].ToString().Trim());
            }

            if (sqlRdr != null && !sqlRdr.IsClosed)
            {
                sqlRdr.Close(); sqlRdr = null;
            }
            
            strFindQuery = "SELECT ITEM, REG_DT, ";
            for (int i = 0; i < item_comboBox.Items.Count; i++)
            {
                strFindQuery += "SUM(TBS_" + (i + 1).ToString() + ") AS '" + item_comboBox.Items[i].ToString() + "'";
                if (i == item_comboBox.Items.Count - 1)
                {
                    strFindQuery += " FROM ( ";
                }
                else
                {
                    strFindQuery += ", ";
                }
            }
            for (int i = 0; i < item_comboBox.Items.Count; i++)
            {
                strFindQuery += "SELECT '성공률' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "ROUND(SUM(SEND_SUCC) * 100.00 / SUM(SEND_CNT), 2) AS TBS_" + (j + 1).ToString();
                    }
                    else
                    {
                        strFindQuery += "'0'+0 AS TBS_" + (j + 1).ToString();
                    }

                    if (j == item_comboBox.Items.Count - 1)
                    {
                        strFindQuery += " ";
                    }
                    else
                    {
                        strFindQuery += ", ";
                    }
                }
                strFindQuery += "FROM DAY_MODEM_INFO ";
                strFindQuery += "WHERE SEND_AVRG IS NOT NULL AND ";
                if (i > 0)
                {
                    strFindQuery += "RECV_TBS = '" + item_comboBox.Items[i].ToString() + "' AND ";
                }
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                if (fMain.filtOnOff)
                {
                    strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                }
                strFindQuery += "AND SUCC_EXCEPT IS NULL ";
                if (i > 0)
                {
                    strFindQuery += "GROUP BY REG_DT, RECV_TBS ";
                }
                else
                {
                    strFindQuery += "GROUP BY REG_DT ";
                }

                if (i == item_comboBox.Items.Count - 1)
                {
                    strFindQuery += ") AS TBL ";
                }
                else
                {
                    strFindQuery += "UNION ALL ";
                }
            }
            strFindQuery += "GROUP BY ITEM, REG_DT ";
            strFindQuery += "ORDER BY REG_DT, ITEM";

            ds = new DataSet();
            SqlDataAdapter adapter = new SqlDataAdapter(strFindQuery, sqlConn);
            adapter.Fill(ds, "RESULT");

        }
        catch (SqlException sqlEx)
        {
            Console.WriteLine(sqlEx.Message);
        }
        finally
        {
            if (sqlRdr != null && !sqlRdr.IsClosed)
            {
                sqlRdr.Close(); sqlRdr = null;
            }

            if (sqlConn != null)
            {
                sqlConn.Close(); sqlConn = null;
                Console.WriteLine("데이터베이스 연결 해제...");
            }
        }

        item_comboBox.SelectedIndex = 0;
    }
}

```

```sql
--today-30 ~ today
--if (search_comboBox.SelectedIndex == 0)

SELECT *
FROM
(
    SELECT
        '01'+0 AS ID
        , '전체'   AS TBS
        , REG_DT
        , ROUND(SUM(SEND_SUCC) * 100.00 / SUM(SEND_CNT), 2) AS SEND_AVRG
    FROM
        DAY_MODEM_INFO
    WHERE
        SEND_AVRG IS NOT NULL
        AND REG_DT         >= '2019-10-24'
        AND REG_DT         <= '2019-11-24'
        -- AND
        -- (
        --     GROUP_CODE    = 'BJ'
        --     OR GROUP_CODE = 'TD'
        -- )
        AND SUCC_EXCEPT IS NULL
    GROUP BY
        REG_DT

    UNION ALL
    
    SELECT
        '99'+0   AS ID
        , RECV_TBS AS TBS
        , REG_DT
        , ROUND(SUM(SEND_SUCC) * 100.00 / SUM(SEND_CNT), 2) AS SEND_AVRG
    FROM
        DAY_MODEM_INFO
    WHERE
        SEND_AVRG IS NOT NULL
        AND REG_DT         >= '2019-10-24'
        AND REG_DT         <= '2019-11-24'
        -- AND
        -- (
        --     GROUP_CODE    = 'BJ'
        --     OR GROUP_CODE = 'TD'
        -- )
        AND SUCC_EXCEPT IS NULL
    GROUP BY
        REG_DT
        , RECV_TBS
) AS TBL
ORDER BY
    REG_DT
    , ID
    , TBS

--if (search_comboBox.SelectedIndex == 1)
else if (search_comboBox.SelectedIndex == 1)
            {
                try
                {
                    item_comboBox.Items.Clear();
                    
                    sqlConn.Open();
                    Console.WriteLine("데이터베이스 연결 성공...");

                    strFindQuery = "SELECT '99'+0 AS ID, RECV_TBS AS TBS ";
                    strFindQuery += "FROM DAY_MODEM_INFO ";
                    strFindQuery += "WHERE REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                    strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                    strFindQuery += "GROUP BY RECV_TBS ";
                    strFindQuery += "ORDER BY RECV_TBS";

                    sqlCmd = new SqlCommand(strFindQuery, sqlConn);
                    sqlRdr = sqlCmd.ExecuteReader();

                    item_comboBox.Items.Add("전체");

                    while (sqlRdr.Read())
                    {
                        item_comboBox.Items.Add(sqlRdr[1].ToString().Trim());
                    }

                    if (sqlRdr != null && !sqlRdr.IsClosed)
                    {
                        sqlRdr.Close(); sqlRdr = null;
                    }
                    
                    strFindQuery = "SELECT ITEM, REG_DT, ";
                    for (int i = 0; i < item_comboBox.Items.Count; i++)
                    {
                        strFindQuery += "SUM(TBS_" + (i + 1).ToString() + ") AS '" + item_comboBox.Items[i].ToString() + "'";
                        if (i == item_comboBox.Items.Count - 1)
                        {
                            strFindQuery += " FROM ( ";
                        }
                        else
                        {
                            strFindQuery += ", ";
                        }
                    }
                    for (int i = 0; i < item_comboBox.Items.Count; i++)
                    {
                        strFindQuery += "SELECT '성공률' AS ITEM, REG_DT, ";
                        for (int j = 0; j < item_comboBox.Items.Count; j++)
                        {
                            if (i == j)
                            {
                                strFindQuery += "ROUND(SUM(SEND_SUCC) * 100.00 / SUM(SEND_CNT), 2) AS TBS_" + (j + 1).ToString();
                            }
                            else
                            {
                                strFindQuery += "'0'+0 AS TBS_" + (j + 1).ToString();
                            }

                            if (j == item_comboBox.Items.Count - 1)
                            {
                                strFindQuery += " ";
                            }
                            else
                            {
                                strFindQuery += ", ";
                            }
                        }
                        strFindQuery += "FROM DAY_MODEM_INFO ";
                        strFindQuery += "WHERE SEND_AVRG IS NOT NULL AND ";
                        if (i > 0)
                        {
                            strFindQuery += "RECV_TBS = '" + item_comboBox.Items[i].ToString() + "' AND ";
                        }
                        strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                        strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                        if (fMain.filtOnOff)
                        {
                            strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                        }
                        strFindQuery += "AND SUCC_EXCEPT IS NULL ";
                        if (i > 0)
                        {
                            strFindQuery += "GROUP BY REG_DT, RECV_TBS ";
                        }
                        else
                        {
                            strFindQuery += "GROUP BY REG_DT ";
                        }

                        if (i == item_comboBox.Items.Count - 1)
                        {
                            strFindQuery += ") AS TBL ";
                        }
                        else
                        {
                            strFindQuery += "UNION ALL ";
                        }
                    }
                    strFindQuery += "GROUP BY ITEM, REG_DT ";
                    strFindQuery += "ORDER BY REG_DT, ITEM";
```


