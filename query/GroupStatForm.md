- GroupStatForm

```c#
private void RedrawChart()
{
    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(fMain.cmsConnStr);

    if (search_comboBox.SelectedIndex == 0)
    {
        item_comboBox.Items.Clear();
        item_comboBox.Items.Add("그룹 등록 모뎀");
        item_comboBox.Items.Add("그룹 발신 모뎀");
        item_comboBox.Items.Add("그룹 수신 모뎀");

        try
        {
            sqlConn.Open();
            Console.WriteLine("데이터베이스 연결 성공...");

            strFindQuery = "SELECT * FROM ( ";
            strFindQuery += "SELECT GROUP_CODE CD, GROUP_ID ID, RTRIM(GROUP_NAME) AS GRP, REG_DT, MODEM_CNT, SEND_CNT, RECV_CNT ";
            strFindQuery += "FROM DAY_GROUP_INFO ";
            strFindQuery += "WHERE MODEM_CNT IS NOT NULL AND ";
            strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            if (fMain.filtOnOff)
            {
                strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
            }
            strFindQuery += "UNION ALL ";
            strFindQuery += "SELECT 'ZZ' AS CD, '99' AS ID, '전체' AS GRP, REG_DT, SUM(MODEM_CNT), SUM(SEND_CNT), SUM(RECV_CNT) ";
            strFindQuery += "FROM DAY_GROUP_INFO ";
            strFindQuery += "WHERE MODEM_CNT IS NOT NULL AND ";
            strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            if (fMain.filtOnOff)
            {
                strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
            }
            strFindQuery += "GROUP BY REG_DT ) AS TBL ";
            strFindQuery += "ORDER BY REG_DT, CD, ID";

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

            strFindQuery = "SELECT GROUP_CODE CD, GROUP_ID ID, RTRIM(GROUP_NAME) AS GRP ";
            strFindQuery += "FROM DAY_GROUP_INFO ";
            strFindQuery += "WHERE REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            if (fMain.filtOnOff)
            {
                strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
            }
            strFindQuery += "GROUP BY GROUP_CODE, GROUP_ID, GROUP_NAME ";
            strFindQuery += "ORDER BY CD, ID";

            sqlCmd = new SqlCommand(strFindQuery, sqlConn);
            sqlRdr = sqlCmd.ExecuteReader();

            while (sqlRdr.Read())
            {
                item_comboBox.Items.Add(sqlRdr[2].ToString().Trim());
            }

            if (sqlRdr != null && !sqlRdr.IsClosed)
            {
                sqlRdr.Close(); sqlRdr = null;
            }

            strFindQuery = "SELECT ITEM, REG_DT, ";
            for (int i = 0; i < item_comboBox.Items.Count; i++)
            {
                strFindQuery += "SUM(GRP_" + (i + 1).ToString() + ") AS '" + item_comboBox.Items[i].ToString() + "'";
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
                strFindQuery += "SELECT '그룹 등록 모뎀' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "MODEM_CNT AS GRP_" + (j + 1).ToString();
                    }
                    else
                    {
                        strFindQuery += "'0'+0 AS GRP_" + (j + 1).ToString();
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
                strFindQuery += "FROM DAY_GROUP_INFO ";
                strFindQuery += "WHERE RTRIM(GROUP_NAME) = '" + item_comboBox.Items[i].ToString() + "' AND ";
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                if (fMain.filtOnOff)
                {
                    strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                }
                strFindQuery += "UNION ALL ";

                strFindQuery += "SELECT '그룹 발신 모뎀' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "SEND_CNT AS GRP_" + (j + 1).ToString();
                    }
                    else
                    {
                        strFindQuery += "'0'+0 AS GRP_" + (j + 1).ToString();
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
                strFindQuery += "FROM DAY_GROUP_INFO ";
                strFindQuery += "WHERE RTRIM(GROUP_NAME) = '" + item_comboBox.Items[i].ToString() + "' AND ";
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                if (fMain.filtOnOff)
                {
                    strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                }
                strFindQuery += "UNION ALL ";

                strFindQuery += "SELECT '그룹 수신 모뎀' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "RECV_CNT AS GRP_" + (j + 1).ToString();
                    }
                    else
                    {
                        strFindQuery += "'0'+0 AS GRP_" + (j + 1).ToString();
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
                strFindQuery += "FROM DAY_GROUP_INFO ";
                strFindQuery += "WHERE RTRIM(GROUP_NAME) = '" + item_comboBox.Items[i].ToString() + "' AND ";
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                if (fMain.filtOnOff)
                {
                    strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
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


```SQL
------------------------------------------
-- RedrawChart
-- search_comboBox.SelectedIndex == 0
------------------------------------------
SELECT *
FROM
(
    SELECT
            GROUP_CODE             CD
            , GROUP_ID             ID
            , RTRIM(GROUP_NAME) AS GRP
            , REG_DT
            , MODEM_CNT
            , SEND_CNT
            , RECV_CNT
    FROM
            DAY_GROUP_INFO
    WHERE
            MODEM_CNT IS NOT NULL
            AND REG_DT >= '2000-01-01'
            AND REG_DT <= '2019-12-31'
            -- AND
            -- (
            --     GROUP_CODE    = 'BJ'
            --     OR GROUP_CODE = 'TD'
            -- )
    
    UNION ALL
    
    SELECT
            'ZZ' AS CD
            , '99' AS ID
            , '전체' AS GRP
            , REG_DT
            , SUM(MODEM_CNT)
            , SUM(SEND_CNT)
            , SUM(RECV_CNT)
    FROM
            DAY_GROUP_INFO
    WHERE
            MODEM_CNT IS NOT NULL
            AND REG_DT >= '2000-01-01'
            AND REG_DT <= '2019-12-31'
            -- AND
            -- (
            --     GROUP_CODE    = 'BJ'
            --     OR GROUP_CODE = 'TD'
            -- )
    GROUP BY REG_DT
)AS TBL 
ORDER BY REG_DT, CD, ID

------------------------------------------
-- RedrawChart
-- search_comboBox.SelectedIndex == 1
------------------------------------------
 else if (search_comboBox.SelectedIndex == 1)
    {
        try
        {
            item_comboBox.Items.Clear();

            sqlConn.Open();
            Console.WriteLine("데이터베이스 연결 성공...");

            strFindQuery = "SELECT GROUP_CODE CD, GROUP_ID ID, RTRIM(GROUP_NAME) AS GRP ";
            strFindQuery += "FROM DAY_GROUP_INFO ";
            strFindQuery += "WHERE REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            if (fMain.filtOnOff)
            {
                strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
            }
            strFindQuery += "GROUP BY GROUP_CODE, GROUP_ID, GROUP_NAME ";
            strFindQuery += "ORDER BY CD, ID";

            sqlCmd = new SqlCommand(strFindQuery, sqlConn);
            sqlRdr = sqlCmd.ExecuteReader();

            while (sqlRdr.Read())
            {
                item_comboBox.Items.Add(sqlRdr[2].ToString().Trim());
            }

            if (sqlRdr != null && !sqlRdr.IsClosed)
            {
                sqlRdr.Close(); sqlRdr = null;
            }

            strFindQuery = "SELECT ITEM, REG_DT, ";
            for (int i = 0; i < item_comboBox.Items.Count; i++)
            {
                strFindQuery += "SUM(GRP_" + (i + 1).ToString() + ") AS '" + item_comboBox.Items[i].ToString() + "'";
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
                strFindQuery += "SELECT '그룹 등록 모뎀' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "MODEM_CNT AS GRP_" + (j + 1).ToString();
                    }
                    else
                    {
                        strFindQuery += "'0'+0 AS GRP_" + (j + 1).ToString();
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
                strFindQuery += "FROM DAY_GROUP_INFO ";
                strFindQuery += "WHERE RTRIM(GROUP_NAME) = '" + item_comboBox.Items[i].ToString() + "' AND ";
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                if (fMain.filtOnOff)
                {
                    strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                }
                strFindQuery += "UNION ALL ";

                strFindQuery += "SELECT '그룹 발신 모뎀' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "SEND_CNT AS GRP_" + (j + 1).ToString();
                    }
                    else
                    {
                        strFindQuery += "'0'+0 AS GRP_" + (j + 1).ToString();
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
                strFindQuery += "FROM DAY_GROUP_INFO ";
                strFindQuery += "WHERE RTRIM(GROUP_NAME) = '" + item_comboBox.Items[i].ToString() + "' AND ";
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                if (fMain.filtOnOff)
                {
                    strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
                }
                strFindQuery += "UNION ALL ";

                strFindQuery += "SELECT '그룹 수신 모뎀' AS ITEM, REG_DT, ";
                for (int j = 0; j < item_comboBox.Items.Count; j++)
                {
                    if (i == j)
                    {
                        strFindQuery += "RECV_CNT AS GRP_" + (j + 1).ToString();
                    }
                    else
                    {
                        strFindQuery += "'0'+0 AS GRP_" + (j + 1).ToString();
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
                strFindQuery += "FROM DAY_GROUP_INFO ";
                strFindQuery += "WHERE RTRIM(GROUP_NAME) = '" + item_comboBox.Items[i].ToString() + "' AND ";
                strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
                strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
                if (fMain.filtOnOff)
                {
                    strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
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
