
- RdbmStatForm
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
            item_comboBox.Items.Add("평균 수신감도");
            item_comboBox.Items.Add("편차 수신감도");
            item_comboBox.Items.Add("최대 수신감도");
            item_comboBox.Items.Add("최소 수신감도");

            sqlConn.Open();
            Console.WriteLine("데이터베이스 연결 성공...");

            strFindQuery = "SELECT * FROM ( ";
            strFindQuery += "SELECT '01'+0 AS ID, RECV_TBS AS TBS, REG_DT, AVG(RDBM_AVRG) AS RDBM_AVRG, AVG(RDBM_DIFF) AS RDBM_DIFF, AVG(RDBM_MAX) AS RDBM_MAX, AVG(RDBM_MIN) AS RDBM_MIN ";
            strFindQuery += "FROM DAY_MODEM_INFO ";
            strFindQuery += "WHERE RDBM_AVRG IS NOT NULL AND RDBM_DIFF IS NOT NULL AND RDBM_MAX IS NOT NULL AND RDBM_MIN IS NOT NULL AND ";
            strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            if (fMain.filtOnOff)
            {
                strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
            }
            strFindQuery += "GROUP BY REG_DT, RECV_TBS ";
            strFindQuery += "UNION ALL ";

            strFindQuery += "SELECT '99'+0 AS ID, '전체' AS TBS, REG_DT, AVG(RDBM_AVRG) AS RDBM_AVRG, AVG(RDBM_DIFF) AS RDBM_DIFF, AVG(RDBM_MAX) AS RDBM_MAX, AVG(RDBM_MIN) AS RDBM_MIN ";
            strFindQuery += "FROM DAY_MODEM_INFO ";
            strFindQuery += "WHERE RDBM_AVRG IS NOT NULL AND RDBM_DIFF IS NOT NULL AND RDBM_MAX IS NOT NULL AND RDBM_MIN IS NOT NULL AND ";
            strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            if (fMain.filtOnOff)
            {
                strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
            }
            strFindQuery += "GROUP BY REG_DT ) AS TBL ";
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

            strFindQuery = "SELECT '01'+0 AS ID, RECV_TBS AS TBS ";
            strFindQuery += "FROM DAY_MODEM_INFO ";
            strFindQuery += "WHERE REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            strFindQuery += "GROUP BY RECV_TBS ";
            strFindQuery += "ORDER BY RECV_TBS";

            sqlCmd = new SqlCommand(strFindQuery, sqlConn);
            sqlRdr = sqlCmd.ExecuteReader();

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
                strFindQuery += "SELECT '평균 수신감도' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "AVG(RDBM_AVRG) AS TBS_" + (j + 1).ToString();
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
                strFindQuery += "WHERE RDBM_AVRG IS NOT NULL AND ";
                strFindQuery += "RECV_TBS = '" + item_comboBox.Items[i].ToString() + "' AND ";
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                if (fMain.filtOnOff)
                {
                    strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                }
                strFindQuery += "GROUP BY REG_DT, RECV_TBS ";
                strFindQuery += "UNION ALL ";

                strFindQuery += "SELECT '편차 수신감도' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "AVG(RDBM_DIFF) AS TBS_" + (j + 1).ToString();
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
                strFindQuery += "WHERE RDBM_DIFF IS NOT NULL AND ";
                strFindQuery += "RECV_TBS = '" + item_comboBox.Items[i].ToString() + "' AND ";
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                if (fMain.filtOnOff)
                {
                    strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                }
                strFindQuery += "GROUP BY REG_DT, RECV_TBS ";
                strFindQuery += "UNION ALL ";

                strFindQuery += "SELECT '최대 수신감도' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "AVG(RDBM_MAX) AS TBS_" + (j + 1).ToString();
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
                strFindQuery += "WHERE RDBM_MAX IS NOT NULL AND ";
                strFindQuery += "RECV_TBS = '" + item_comboBox.Items[i].ToString() + "' AND ";
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                if (fMain.filtOnOff)
                {
                    strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                }
                strFindQuery += "GROUP BY REG_DT, RECV_TBS ";
                strFindQuery += "UNION ALL ";

                strFindQuery += "SELECT '최소 수신감도' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "AVG(RDBM_MIN) AS TBS_" + (j + 1).ToString();
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
                strFindQuery += "WHERE RDBM_MIN IS NOT NULL AND ";
                strFindQuery += "RECV_TBS = '" + item_comboBox.Items[i].ToString() + "' AND ";
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                if (fMain.filtOnOff)
                {
                    strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                }
                strFindQuery += "GROUP BY REG_DT, RECV_TBS ";

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
-- if (search_comboBox.SelectedIndex == 0)
-- today-30 ~ today
SELECT *
FROM
(
    SELECT
        '01'+0   AS ID
        , RECV_TBS AS TBS
        , REG_DT
        , AVG(RDBM_AVRG) AS RDBM_AVRG
        , AVG(RDBM_DIFF) AS RDBM_DIFF
        , AVG(RDBM_MAX)  AS RDBM_MAX
        , AVG(RDBM_MIN)  AS RDBM_MIN
    FROM
        DAY_MODEM_INFO
    WHERE
        RDBM_AVRG     IS NOT NULL
        AND RDBM_DIFF IS NOT NULL
        AND RDBM_MAX  IS NOT NULL
        AND RDBM_MIN  IS NOT NULL
        AND REG_DT             >= '2019-10-24'
        AND REG_DT             <= '2019-11-24'
        -- AND
        -- (
        --     GROUP_CODE    = 'BJ'
        --     OR GROUP_CODE = 'TD'
        -- )
    GROUP BY
        REG_DT
        , RECV_TBS
    
    UNION ALL
    
    SELECT
        '99'+0 AS ID
        , '전체'   AS TBS
        , REG_DT
        , AVG(RDBM_AVRG) AS RDBM_AVRG
        , AVG(RDBM_DIFF) AS RDBM_DIFF
        , AVG(RDBM_MAX)  AS RDBM_MAX
        , AVG(RDBM_MIN)  AS RDBM_MIN
    FROM
        DAY_MODEM_INFO
    WHERE
        RDBM_AVRG     IS NOT NULL
        AND RDBM_DIFF IS NOT NULL
        AND RDBM_MAX  IS NOT NULL
        AND RDBM_MIN  IS NOT NULL
        AND REG_DT             >= '2019-10-24'
        AND REG_DT             <= '2019-11-24'
        -- AND
        -- (
        --     GROUP_CODE    = 'BJ'
        --     OR GROUP_CODE = 'TD'
        -- )
    GROUP BY
        REG_DT
) AS TBL
ORDER BY
    REG_DT
    , ID
    , TBS

-- else if (search_comboBox.SelectedIndex == 1)
else if (search_comboBox.SelectedIndex == 1)
            {
                try
                {
                    item_comboBox.Items.Clear();

                    sqlConn.Open();
                    Console.WriteLine("데이터베이스 연결 성공...");

                    strFindQuery = "SELECT '01'+0 AS ID, RECV_TBS AS TBS ";
                    strFindQuery += "FROM DAY_MODEM_INFO ";
                    strFindQuery += "WHERE REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                    strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                    strFindQuery += "GROUP BY RECV_TBS ";
                    strFindQuery += "ORDER BY RECV_TBS";

                    sqlCmd = new SqlCommand(strFindQuery, sqlConn);
                    sqlRdr = sqlCmd.ExecuteReader();

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
                        strFindQuery += "SELECT '평균 수신감도' AS ITEM, REG_DT, ";
                        for (int j = 0; j < item_comboBox.Items.Count; j++)
                        {
                            if (i == j)
                            {
                                strFindQuery += "AVG(RDBM_AVRG) AS TBS_" + (j + 1).ToString();
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
                        strFindQuery += "WHERE RDBM_AVRG IS NOT NULL AND ";
                        strFindQuery += "RECV_TBS = '" + item_comboBox.Items[i].ToString() + "' AND ";
                        strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                        strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                        if (fMain.filtOnOff)
                        {
                            strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                        }
                        strFindQuery += "GROUP BY REG_DT, RECV_TBS ";
                        strFindQuery += "UNION ALL ";

                        strFindQuery += "SELECT '편차 수신감도' AS ITEM, REG_DT, ";
                        for (int j = 0; j < item_comboBox.Items.Count; j++)
                        {
                            if (i == j)
                            {
                                strFindQuery += "AVG(RDBM_DIFF) AS TBS_" + (j + 1).ToString();
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
                        strFindQuery += "WHERE RDBM_DIFF IS NOT NULL AND ";
                        strFindQuery += "RECV_TBS = '" + item_comboBox.Items[i].ToString() + "' AND ";
                        strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                        strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                        if (fMain.filtOnOff)
                        {
                            strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                        }
                        strFindQuery += "GROUP BY REG_DT, RECV_TBS ";
                        strFindQuery += "UNION ALL ";

                        strFindQuery += "SELECT '최대 수신감도' AS ITEM, REG_DT, ";
                        for (int j = 0; j < item_comboBox.Items.Count; j++)
                        {
                            if (i == j)
                            {
                                strFindQuery += "AVG(RDBM_MAX) AS TBS_" + (j + 1).ToString();
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
                        strFindQuery += "WHERE RDBM_MAX IS NOT NULL AND ";
                        strFindQuery += "RECV_TBS = '" + item_comboBox.Items[i].ToString() + "' AND ";
                        strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                        strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                        if (fMain.filtOnOff)
                        {
                            strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                        }
                        strFindQuery += "GROUP BY REG_DT, RECV_TBS ";
                        strFindQuery += "UNION ALL ";

                        strFindQuery += "SELECT '최소 수신감도' AS ITEM, REG_DT, ";
                        for (int j = 0; j < item_comboBox.Items.Count; j++)
                        {
                            if (i == j)
                            {
                                strFindQuery += "AVG(RDBM_MIN) AS TBS_" + (j + 1).ToString();
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
                        strFindQuery += "WHERE RDBM_MIN IS NOT NULL AND ";
                        strFindQuery += "RECV_TBS = '" + item_comboBox.Items[i].ToString() + "' AND ";
                        strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                        strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                        if (fMain.filtOnOff)
                        {
                            strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                        }
                        strFindQuery += "GROUP BY REG_DT, RECV_TBS ";

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
