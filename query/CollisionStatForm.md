- CollisionStatForm
- MainForm - 통계관리 - Collision 통계 -> CollisionStatForm

```c#
private void RedrawChart()
{
    string strFindQuery = "";
    SqlConnection sqlConn = new SqlConnection(fMain.cmsConnStr);

    if (search_comboBox.SelectedIndex == 0)
    {
        try
        {
            sqlConn.Open();
            Console.WriteLine("데이터베이스 연결 성공...");

            strFindQuery = "SELECT * FROM ( ";
            strFindQuery += "SELECT COLL.TBS_ID+0 AS ID, RTRIM(INFO.TBS_NAME)+'('+RTRIM(COLL.TBS_ID)+')' AS TBS, ";
            strFindQuery += "SUBSTRING(COLL.REG_DT, 0, 11) AS REG_DT, SUM(COLL.TBS_COLL) AS TBS_COLL ";
            strFindQuery += "FROM DAY_TBS_COLL AS COLL CROSS JOIN ( ";
            strFindQuery += "SELECT TBS_ID, TBS_NAME FROM DAY_TBS_INFO GROUP BY TBS_ID, TBS_NAME ) AS INFO ";
            strFindQuery += "WHERE COLL.TBS_COLL IS NOT NULL AND ";
            strFindQuery += "COLL.TBS_ID = INFO.TBS_ID AND ";
            strFindQuery += "COLL.REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "COLL.REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            strFindQuery += "GROUP BY COLL.TBS_ID, INFO.TBS_NAME, SUBSTRING(COLL.REG_DT, 0, 11) ";
            strFindQuery += "UNION ALL ";
            strFindQuery += "SELECT '99'+0 AS ID, '전체' AS TBS, SUBSTRING(REG_DT, 0, 11) AS REG_DT, SUM(TBS_COLL) ";
            strFindQuery += "FROM DAY_TBS_COLL ";
            strFindQuery += "WHERE TBS_COLL IS NOT NULL AND ";
            strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            strFindQuery += "GROUP BY SUBSTRING(REG_DT, 0, 11) ) AS TBL ";
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

        try
        {
            chart.Data.Clear();
            chart.DataSourceSettings.Fields.Clear();

            //chart.AxisY.CustomGridLines[0].Value = 96;
            //chart.AxisY.CustomGridLines[1].Value = 97;
            //chart.AxisY.CustomGridLines[2].Value = 98;

            // Create and configure the Crosstab data provider
            DataTableProvider dt = new DataTableProvider(ds.Tables[0]);
            CrosstabDataProvider cfxCT = new CrosstabDataProvider();
            cfxCT.DataSource = dt;

            // Instruct Chart FX how to use the fields in the Crosstab. Refer to the Resource Center documentation for further details.
            chart.DataSourceSettings.Fields.Add(new FieldMap("TBS", FieldUsage.ColumnHeading)); // 항목:레전드
            chart.DataSourceSettings.Fields.Add(new FieldMap("REG_DT", FieldUsage.RowHeading)); // X축:라벨
            chart.DataSourceSettings.Fields.Add(new FieldMap("TBS_COLL", FieldUsage.Value)); // Y축:값
            chart.DataSource = cfxCT;
        }
        catch (Exception ex) { Console.WriteLine(ex.Message); }
    }
    else if (search_comboBox.SelectedIndex == 1)
    {
        try
        {
            sqlConn.Open();
            Console.WriteLine("데이터베이스 연결 성공...");

            strFindQuery = "SELECT * FROM ( ";
            strFindQuery += "SELECT COLL.TBS_ID+0 AS ID, RTRIM(INFO.TBS_NAME)+'('+RTRIM(COLL.TBS_ID)+')' AS TBS, ";
            strFindQuery += "SUBSTRING(COLL.REG_DT, 12, 2) AS REG_DT, SUM(COLL.TBS_COLL) AS TBS_COLL ";
            strFindQuery += "FROM DAY_TBS_COLL AS COLL CROSS JOIN ( ";
            strFindQuery += "SELECT TBS_ID, TBS_NAME FROM DAY_TBS_INFO GROUP BY TBS_ID, TBS_NAME ) AS INFO ";
            strFindQuery += "WHERE COLL.TBS_COLL IS NOT NULL AND ";
            strFindQuery += "COLL.TBS_ID = INFO.TBS_ID AND ";
            strFindQuery += "COLL.REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "COLL.REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            strFindQuery += "GROUP BY COLL.TBS_ID, INFO.TBS_NAME, SUBSTRING(COLL.REG_DT, 12, 2) ";
            strFindQuery += "UNION ALL ";
            strFindQuery += "SELECT '99'+0 AS ID, '전체' AS TBS, SUBSTRING(REG_DT, 12, 2) AS REG_DT, SUM(TBS_COLL) ";
            strFindQuery += "FROM DAY_TBS_COLL ";
            strFindQuery += "WHERE TBS_COLL IS NOT NULL AND ";
            strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
            strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
            strFindQuery += "GROUP BY SUBSTRING(REG_DT, 12, 2) ) AS TBL ";
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

        try
        {
            chart.Data.Clear();
            chart.DataSourceSettings.Fields.Clear();

            //chart.AxisY.CustomGridLines[0].Value = 96;
            //chart.AxisY.CustomGridLines[1].Value = 97;
            //chart.AxisY.CustomGridLines[2].Value = 98;

            // Create and configure the Crosstab data provider
            DataTableProvider dt = new DataTableProvider(ds.Tables[0]);
            CrosstabDataProvider cfxCT = new CrosstabDataProvider();
            cfxCT.DataSource = dt;

            // Instruct Chart FX how to use the fields in the Crosstab. Refer to the Resource Center documentation for further details.
            chart.DataSourceSettings.Fields.Add(new FieldMap("TBS", FieldUsage.ColumnHeading)); // 항목:레전드
            chart.DataSourceSettings.Fields.Add(new FieldMap("REG_DT", FieldUsage.RowHeading)); // X축:라벨
            chart.DataSourceSettings.Fields.Add(new FieldMap("TBS_COLL", FieldUsage.Value)); // Y축:값
            chart.DataSource = cfxCT;
        }
        catch (Exception ex) { Console.WriteLine(ex.Message); }
    }
}
```

```sql
------------------------------------------
-- CollisionStatForm
-- search_comboBox.SelectedIndex == 0
------------------------------------------
SELECT *
FROM
(
    SELECT
            COLL.TBS_ID+0                                     AS ID
            , RTRIM(INFO.TBS_NAME)+'('+RTRIM(COLL.TBS_ID)+')' AS TBS
            , SUBSTRING(COLL.REG_DT, 0, 11)                   AS REG_DT
            , SUM(COLL.TBS_COLL)                              AS TBS_COLL
    FROM
            DAY_TBS_COLL AS COLL
            CROSS JOIN
                        ( 
                            SELECT TBS_ID, TBS_NAME 
                            FROM DAY_TBS_INFO 
                            GROUP BY TBS_ID, TBS_NAME
                        ) AS INFO
    WHERE
            COLL.TBS_COLL IS NOT NULL
            AND COLL.TBS_ID  = INFO.TBS_ID
            AND COLL.REG_DT >= '2000-01-01'
            AND COLL.REG_DT <= '2019-12-31'
    GROUP BY
            COLL.TBS_ID
            , INFO.TBS_NAME
            , SUBSTRING(COLL.REG_DT, 0, 11)
    
    UNION ALL
    
    SELECT
            '99'+0                       AS ID
            , '전체'                     AS TBS
            , SUBSTRING(REG_DT, 0, 11)   AS REG_DT
            , SUM(TBS_COLL)
    FROM
            DAY_TBS_COLL
    WHERE
            TBS_COLL IS NOT NULL
            AND REG_DT        >= '2000-01-01'
            AND REG_DT        <= '2019-12-31'
    GROUP BY
            SUBSTRING(REG_DT, 0, 11)
)AS TBL 
ORDER BY REG_DT, ID


------------------------------------------
-- CollisionStatForm
-- search_comboBox.SelectedIndex == 1
------------------------------------------
SELECT *
FROM
(
    SELECT  
            COLL.TBS_ID+0                                     AS ID
            , RTRIM(INFO.TBS_NAME)+'('+RTRIM(COLL.TBS_ID)+')' AS TBS
            , SUBSTRING(COLL.REG_DT, 12, 2)                   AS REG_DT
            , SUM(COLL.TBS_COLL)                              AS TBS_COLL
    FROM
            DAY_TBS_COLL AS COLL
            CROSS JOIN
                        ( 
                            SELECT TBS_ID, TBS_NAME 
                            FROM DAY_TBS_INFO 
                            GROUP BY TBS_ID, TBS_NAME
                        )AS INFO
    WHERE
            COLL.TBS_COLL IS NOT NULL
            AND COLL.TBS_ID  = INFO.TBS_ID
            AND COLL.REG_DT >= '2000-01-01'
            AND COLL.REG_DT <= '2019-12-31'
    GROUP BY
            COLL.TBS_ID
            , INFO.TBS_NAME
            , SUBSTRING(COLL.REG_DT, 12, 2)
    
    UNION ALL
    
    SELECT
            '99'+0                       AS ID
            , '전체'                     AS TBS
            , SUBSTRING(REG_DT, 12, 2)   AS REG_DT
            , SUM(TBS_COLL)
    FROM
            DAY_TBS_COLL
    WHERE
            TBS_COLL IS NOT NULL
            AND REG_DT  >= '2000-01-01'
            AND REG_DT  <= '2019-12-31'
    GROUP BY
            SUBSTRING(REG_DT, 12, 2)
)AS TBL 
ORDER BY REG_DT, ID

```

