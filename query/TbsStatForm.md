- TbsStatForm
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
            item_comboBox.Items.Add("본부 등록 모뎀");
            item_comboBox.Items.Add("본부 최대 모뎀");
            item_comboBox.Items.Add("전체 최대 모뎀");

            sqlConn.Open();
            Console.WriteLine("데이터베이스 연결 성공...");

            strFindQuery = "SELECT * FROM ( ";
            strFindQuery += "SELECT TBS_ID+0 AS ID, RTRIM(TBS_NAME)+'('+RTRIM(TBS_ID)+')' AS TBS, REG_DT, MODEM_CNT, MODEM_MAX, MODEM_TOT ";
            strFindQuery += "FROM DAY_TBS_INFO ";
            strFindQuery += "WHERE MODEM_CNT IS NOT NULL AND ";
            strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            strFindQuery += "UNION ALL ";
            strFindQuery += "SELECT '99'+0 AS ID, '전체' AS TBS, REG_DT, SUM(MODEM_CNT), SUM(MODEM_MAX), SUM(MODEM_TOT) ";
            strFindQuery += "FROM DAY_TBS_INFO ";
            strFindQuery += "WHERE MODEM_CNT IS NOT NULL AND ";
            strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            strFindQuery += "GROUP BY REG_DT ) AS TBL ";
            strFindQuery += "ORDER BY REG_DT, ID";

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

            strFindQuery = "SELECT TBS_ID+0 AS ID, RTRIM(TBS_NAME)+'('+RTRIM(TBS_ID)+')' AS TBS ";
            strFindQuery += "FROM DAY_TBS_INFO ";
            strFindQuery += "WHERE REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            strFindQuery += "GROUP BY TBS_ID, TBS_NAME ";
            strFindQuery += "ORDER BY ID";

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
                strFindQuery += "SUM(TBS_" + (i+1).ToString() + ") AS '" + item_comboBox.Items[i].ToString() + "'";
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
                strFindQuery += "SELECT '본부 등록 모뎀' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "MODEM_CNT AS TBS_" + (j + 1).ToString();
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
                strFindQuery += "FROM DAY_TBS_INFO ";
                strFindQuery += "WHERE RTRIM(TBS_NAME)+'('+RTRIM(TBS_ID)+')' = '" + item_comboBox.Items[i].ToString() + "' AND ";
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                strFindQuery += "UNION ALL ";
                
                strFindQuery += "SELECT '본부 최대 모뎀' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "MODEM_MAX AS TBS_" + (j + 1).ToString();
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
                strFindQuery += "FROM DAY_TBS_INFO ";
                strFindQuery += "WHERE RTRIM(TBS_NAME)+'('+RTRIM(TBS_ID)+')' = '" + item_comboBox.Items[i].ToString() + "' AND ";
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                strFindQuery += "UNION ALL ";
                
                strFindQuery += "SELECT '전체 최대 모뎀' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "MODEM_TOT AS TBS_" + (j + 1).ToString();
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
                strFindQuery += "FROM DAY_TBS_INFO ";
                strFindQuery += "WHERE RTRIM(TBS_NAME)+'('+RTRIM(TBS_ID)+')' = '" + item_comboBox.Items[i].ToString() + "' AND ";
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";

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
```SQL
-- if (search_comboBox.SelectedIndex == 0)
SELECT *
FROM
(
    SELECT
        TBS_ID+0                              AS ID
        , RTRIM(TBS_NAME)+'('+RTRIM(TBS_ID)+')' AS TBS
        , REG_DT
        , MODEM_CNT
        , MODEM_MAX
        , MODEM_TOT
    FROM
        DAY_TBS_INFO
    WHERE
        MODEM_CNT IS NOT NULL
        AND REG_DT         >= '2019-10-24'
        AND REG_DT         <= '2019-11-24'
    
    UNION ALL
    
    SELECT
        '99'+0 AS ID
        , '전체'   AS TBS
        , REG_DT
        , SUM(MODEM_CNT)
        , SUM(MODEM_MAX)
        , SUM(MODEM_TOT)
    FROM
        DAY_TBS_INFO
    WHERE
        MODEM_CNT IS NOT NULL
        AND REG_DT         >= '2019-10-24'
        AND REG_DT         <= '2019-11-24'
    GROUP BY
        REG_DT
) AS TBL
ORDER BY
    REG_DT
    , ID
-- else if (search_comboBox.SelectedIndex == 1)



```
