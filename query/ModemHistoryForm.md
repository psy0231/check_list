
- ModemHistoryForm
```C#

public void RedrawChart(string modem)
{
    chart.Titles[0].Text = modem;
    string issi = modem.Substring(0, 7);

    string strFindQuery = "";
    SqlConnection sqlConn = new SqlConnection(fMain.cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT * FROM ( ";
        strFindQuery += "SELECT '평균' AS ITEM, REG_DT, RDBM_AVRG AS VALUE ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE RDBM_AVRG IS NOT NULL AND ";
        strFindQuery += "MODEM_ISSI = '" + issi + "' AND ";
        strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
        strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
        strFindQuery += "UNION ALL ";
        strFindQuery += "SELECT '편차' AS ITEM, REG_DT, RDBM_DIFF AS VALUE ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE RDBM_DIFF IS NOT NULL AND ";
        strFindQuery += "MODEM_ISSI = '" + issi + "' AND ";
        strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
        strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
        strFindQuery += "UNION ALL ";
        strFindQuery += "SELECT '최대' AS ITEM, REG_DT, RDBM_MAX AS VALUE ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE RDBM_MAX IS NOT NULL AND ";
        strFindQuery += "MODEM_ISSI = '" + issi + "' AND ";
        strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
        strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
        strFindQuery += "UNION ALL ";
        strFindQuery += "SELECT '최소' AS ITEM, REG_DT, RDBM_MIN AS VALUE ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE RDBM_MIN IS NOT NULL AND ";
        strFindQuery += "MODEM_ISSI = '" + issi + "' AND ";
        strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
        strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
        strFindQuery += "UNION ALL ";
        strFindQuery += "SELECT '성공률' AS ITEM, REG_DT, SEND_AVRG AS VALUE ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE SEND_AVRG IS NOT NULL AND ";
        strFindQuery += "MODEM_ISSI = '" + issi + "' AND ";
        strFindQuery += "REG_DT >= '" + from_date.Value.ToString("yyyy-MM-dd") + "' AND ";
        strFindQuery += "REG_DT <= '" + to_date.Value.ToString("yyyy-MM-dd") + "' ";
        strFindQuery += ") AS TBL ";
        strFindQuery += "ORDER BY REG_DT, ITEM DESC";

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
        chart.DataSourceSettings.Fields.Add(new FieldMap("ITEM", FieldUsage.ColumnHeading)); // 항목:레전드
        chart.DataSourceSettings.Fields.Add(new FieldMap("REG_DT", FieldUsage.RowHeading)); // X축:라벨
        chart.DataSourceSettings.Fields.Add(new FieldMap("VALUE", FieldUsage.Value)); // Y축:값
        chart.DataSource = cfxCT;
    }
    catch (Exception ex) { Console.WriteLine(ex.Message); }
}
```

```sql
--7000001 : issi

SELECT *
FROM
(
    SELECT
        '평균' AS ITEM
        , REG_DT
        , RDBM_AVRG AS VALUE
    FROM
        DAY_MODEM_INFO
    WHERE
        RDBM_AVRG IS NOT NULL
        AND MODEM_ISSI      = '7000001'
        AND REG_DT         >= '2000-01-01'
        AND REG_DT         <= '2019-11-24'
                    
    UNION ALL
                    
    SELECT
        '편차' AS ITEM
        , REG_DT
        , RDBM_DIFF AS VALUE
    FROM
        DAY_MODEM_INFO
    WHERE
        RDBM_DIFF IS NOT NULL
        AND MODEM_ISSI      = '7000001'
        AND REG_DT         >= '2000-01-01'
        AND REG_DT         <= '2019-11-24'

    UNION ALL
                    
    SELECT
        '최대' AS ITEM
        , REG_DT
        , RDBM_MAX AS VALUE
    FROM
        DAY_MODEM_INFO
    WHERE
        RDBM_MAX IS NOT NULL
        AND MODEM_ISSI     = '7000001'
        AND REG_DT        >= '2000-01-01'
        AND REG_DT        <= '2019-11-24'
                    
    UNION ALL
                
    SELECT
        '최소' AS ITEM
        , REG_DT
        , RDBM_MIN AS VALUE
    FROM
        DAY_MODEM_INFO
    WHERE
        RDBM_MIN IS NOT NULL
        AND MODEM_ISSI     = '7000001'
        AND REG_DT        >= '2000-01-01'
        AND REG_DT        <= '2019-11-24'
    
    UNION ALL
    
    SELECT
        '성공률' AS ITEM
        , REG_DT
        , SEND_AVRG AS VALUE
    FROM
        DAY_MODEM_INFO
    WHERE
        SEND_AVRG IS NOT NULL
        AND MODEM_ISSI      = '7000001'
        AND REG_DT         >= '2000-01-01'
        AND REG_DT         <= '2019-11-24'
) AS TBL
ORDER BY
    REG_DT
    , ITEM DESC

```