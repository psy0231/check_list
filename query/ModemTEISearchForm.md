
- ModemTEISearchForm
```C#
private void modem_list_search()
{
    modem_list.Items.Clear();
    modem_list.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(fMain.cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT MAX(REG_DT) FROM DAY_MODEM_INFO";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        sqlRdr.Read();
        modem_date.Value = DateTime.Parse(sqlRdr[0].ToString().Trim());

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT GROUP_NAME, NODE_NAME, NODE_ID, MODEM_ISSI, SEND_AVRG ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE REG_DT = '" + modem_date.Value.ToString("yyyy-MM-dd") + "' "; ;
        strFindQuery += "ORDER BY MODEM_ISSI";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            Item.SubItems.Add((modem_list.Items.Count + 1).ToString());
            Item.SubItems.Add(sqlRdr[0].ToString().Trim());
            Item.SubItems.Add(sqlRdr[1].ToString().Trim());
            Item.SubItems.Add(sqlRdr[2].ToString().Trim());
            Item.SubItems.Add(sqlRdr[3].ToString().Trim());
            if (sqlRdr[4].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[4].ToString().Trim());

            if (Item.SubItems[6].Text == "-")
            {
                Item.SubItems[6].BackColor = Color.Gray;
            }
            else if (Convert.ToDouble(Item.SubItems[6].Text) < fMain.avrgCritical)
            {
                Item.SubItems[6].BackColor = Color.Red;
            }
            else if (Convert.ToDouble(Item.SubItems[6].Text) < fMain.avrgMajor)
            {
                Item.SubItems[6].BackColor = Color.Orange;
            }
            else if (Convert.ToDouble(Item.SubItems[6].Text) < fMain.avrgMinor)
            {
                Item.SubItems[6].BackColor = Color.Yellow;
            }
            else
            {
                Item.SubItems[6].BackColor = Color.White;
            }

            modem_list.Items.Add(Item);
        }
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

    modem_list.EndUpdate();
}
```
```SQL
SELECT
          MAX(REG_DT)
FROM
          DAY_MODEM_INFO
-- 2019-11-24
SELECT
          GROUP_NAME
        , NODE_NAME
        , NODE_ID
        , MODEM_ISSI
        , SEND_AVRG
FROM
          DAY_MODEM_INFO
WHERE
          REG_DT = '2019-11-24'
ORDER BY
          MODEM_ISSI
```

