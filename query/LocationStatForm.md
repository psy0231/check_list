- LocationStatForm
```C#

private void modem_selectBtn_Click(object sender, EventArgs e)
{
    if (cmsConnStr == "") return;

    WaitMsg.ShowWait(this, WaitMsg.waitMsg);

    string fromDate = from_date.Value.ToString("yyyy-MM-dd");
    string toDate = to_date.Value.ToString("yyyy-MM-dd");

    modemstat_columnsorter.previousColumn = -1;
    modemstat_flag = 0;
    modem_statList.ListViewItemSorter = null;

    modem_statList.Items.Clear();
    modem_statList.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT REG_DT, MODEM_CNT, SEND_CNT, SEND_AVRG, SEND_SUCC, SEND_FAIL, RECV_CNT, AVRG_LMT, AVRG_CNT, RDBM_LMT, RDBM_CNT, FILT_ONOFF, EXCEPT_CNT ";
        strFindQuery += "FROM DAY_MODEM_STAT ";
        strFindQuery += "WHERE REG_DT >= '" + fromDate + "' ";
        strFindQuery += "AND REG_DT <= '" + toDate + "' ";
        strFindQuery += "ORDER BY REG_DT ";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            Item.SubItems.Add(sqlRdr[0].ToString().Trim());
            Item.SubItems.Add(sqlRdr[1].ToString().Trim());
            Item.SubItems.Add(sqlRdr[2].ToString().Trim());
            Item.SubItems.Add(sqlRdr[3].ToString().Trim());
            Item.SubItems.Add(sqlRdr[4].ToString().Trim());
            Item.SubItems.Add(sqlRdr[5].ToString().Trim());
            Item.SubItems.Add(sqlRdr[6].ToString().Trim());
            Item.SubItems.Add(sqlRdr[7].ToString().Trim());
            Item.SubItems.Add(sqlRdr[8].ToString().Trim());
            Item.SubItems.Add(sqlRdr[9].ToString().Trim());
            Item.SubItems.Add(sqlRdr[10].ToString().Trim());
            if (sqlRdr[11].ToString().Trim() == "1") Item.SubItems.Add("배전");
            else Item.SubItems.Add("전체");
            Item.SubItems.Add(sqlRdr[12].ToString().Trim());
            modem_statList.Items.Add(Item);
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

    int col2Cnt = 0, col2Val = 0;
    int col3Cnt = 0, col3Val = 0;
    int col4Cnt = 0; double col4Val = 0.00d;
    int col5Cnt = 0, col5Val = 0;
    int col6Cnt = 0, col6Val = 0;
    int col7Cnt = 0, col7Val = 0;
    int col9Cnt = 0, col9Val = 0;
    int col11Cnt = 0, col11Val = 0;
    int col13Cnt = 0, col13Val = 0;

    for (int cnt = 0; cnt < modem_statList.Items.Count; cnt++)
    {
        if (modem_statList.Items[cnt].SubItems[2].Text != "-")
        {
            col2Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[2].Text);
            col2Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[3].Text != "-")
        {
            col3Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[3].Text);
            col3Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[4].Text != "-")
        {
            col4Val += Convert.ToDouble(modem_statList.Items[cnt].SubItems[4].Text);
            col4Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[5].Text != "-")
        {
            col5Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[5].Text);
            col5Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[6].Text != "-")
        {
            col6Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[6].Text);
            col6Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[7].Text != "-")
        {
            col7Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[7].Text);
            col7Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[9].Text != "-")
        {
            col9Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[9].Text);
            col9Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[11].Text != "-")
        {
            col11Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[11].Text);
            col11Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[13].Text != "-")
        {
            col13Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[13].Text);
            col13Cnt++;
        }
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("합계 / 평균");
    if (col2Cnt != 0) tItem.SubItems.Add(col2Val.ToString() + " / " + (col2Val / col2Cnt).ToString());
    else tItem.SubItems.Add("- / -");
    if (col3Cnt != 0) tItem.SubItems.Add(col3Val.ToString());
    else tItem.SubItems.Add("-");
    if (col4Cnt != 0) tItem.SubItems.Add((col5Val * 100.00d / col3Val).ToString("N2"));
    else tItem.SubItems.Add("-");
    if (col5Cnt != 0) tItem.SubItems.Add(col5Val.ToString());
    else tItem.SubItems.Add("-");
    if (col6Cnt != 0) tItem.SubItems.Add(col6Val.ToString());
    else tItem.SubItems.Add("-");
    if (col7Cnt != 0) tItem.SubItems.Add(col7Val.ToString());
    else tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    if (col9Cnt != 0) tItem.SubItems.Add(col9Val.ToString() + " / " + (Convert.ToDouble(col9Val) / col9Cnt).ToString("N2"));
    else tItem.SubItems.Add("- / -");
    tItem.SubItems.Add("-");
    if (col11Cnt != 0) tItem.SubItems.Add(col11Val.ToString() + " / " + (Convert.ToDouble(col11Val) / col11Cnt).ToString("N2"));
    else tItem.SubItems.Add("- / -");
    tItem.SubItems.Add("-");
    if (col13Cnt != 0) tItem.SubItems.Add(col13Val.ToString() + " / " + (Convert.ToDouble(col13Val) / col13Cnt).ToString("N2"));
    else tItem.SubItems.Add("- / -");
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    modem_statList.Items.Add(tItem);

    modem_statList.EndUpdate();

    location_text.Text = location_comboBox.Text;

    WaitMsg.HideWait(this);
}

private void modem_deleteBtn_Click(object sender, EventArgs e)
{
    if (cmsConnStr == "") return;

    WaitMsg.ShowWait(this, WaitMsg.delMsg);

    string fromDate = from_date.Value.ToString("yyyy-MM-dd");
    string toDate = to_date.Value.ToString("yyyy-MM-dd");

    string strSaveQuery = "";
    SqlCommand sqlCmd = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스[TRS-CMS] 연결 성공...");

        strSaveQuery = "DELETE FROM DAY_MODEM_STAT ";
        strSaveQuery += "WHERE REG_DT >= '" + fromDate + "'";
        strSaveQuery += "AND REG_DT <= '" + toDate + "'";

        sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
        sqlCmd.ExecuteReader().Close();
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
            Console.WriteLine("데이터베이스[TRS-CMS] 연결 해제...");
        }
    }

    location_text.Text = location_comboBox.Text;

    WaitMsg.HideWait(this);
}

private void modem_searchBtn_Click(object sender, EventArgs e)
{
    if (cmsConnStr == "") return;

    WaitMsg.ShowWait(this, WaitMsg.waitMsg);

    string fromDate = from_date.Value.ToString("yyyy-MM-dd");
    string toDate = to_date.Value.ToString("yyyy-MM-dd");

    modemstat_columnsorter.previousColumn = -1;
    modemstat_flag = 0;
    modem_statList.ListViewItemSorter = null;

    modem_statList.Items.Clear();
    modem_statList.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT REG_DT, COUNT(*) CNT, SUM(SEND_CNT) SND, ROUND(AVG(SEND_AVRG), 2) AVR, ";
        strFindQuery += "SUM(SEND_SUCC) SUC, SUM(SEND_FAIL) FIL, SUM(RECV_CNT) RCV, ";
        strFindQuery += "COUNT(CASE WHEN(SEND_AVRG <= " + avrgLimit + ") THEN 1 ELSE NULL END) AVRG_LMT, ";
        strFindQuery += "COUNT(CASE WHEN(RDBM_AVRG <= " + rdbmLimit + ") THEN 1 ELSE NULL END) RDBM_LMT ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE REG_DT >= '" + fromDate + "' ";
        strFindQuery += "AND REG_DT <= '" + toDate + "' ";
        if (filtOnOff)
        {
            strFindQuery += "AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
        }
        strFindQuery += "AND SUCC_EXCEPT IS NULL ";
        strFindQuery += "GROUP BY REG_DT ";
        strFindQuery += "ORDER BY REG_DT ";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            Item.SubItems.Add(sqlRdr[0].ToString().Trim());
            Item.SubItems.Add(sqlRdr[1].ToString().Trim());
            Item.SubItems.Add(sqlRdr[2].ToString().Trim());
            //Item.SubItems.Add(sqlRdr[3].ToString().Trim());
            Item.SubItems.Add((Convert.ToInt32(sqlRdr[4].ToString().Trim())*100.00d/Convert.ToInt32(sqlRdr[2].ToString().Trim())).ToString("N2"));
            Item.SubItems.Add(sqlRdr[4].ToString().Trim());
            Item.SubItems.Add(sqlRdr[5].ToString().Trim());
            Item.SubItems.Add(sqlRdr[6].ToString().Trim());
            Item.SubItems.Add(avrgLimit.ToString());
            Item.SubItems.Add(sqlRdr[7].ToString().Trim());
            Item.SubItems.Add(rdbmLimit.ToString());
            Item.SubItems.Add(sqlRdr[8].ToString().Trim());
            if (filtOnOff) Item.SubItems.Add("배전");
            else Item.SubItems.Add("전체");
            Item.SubItems.Add("0");
            modem_statList.Items.Add(Item);
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT REG_DT, COUNT(*) CNT ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE REG_DT >= '" + fromDate + "' ";
        strFindQuery += "AND REG_DT <= '" + toDate + "' ";
        if (filtOnOff)
        {
            strFindQuery += "AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
        }
        strFindQuery += "AND SUCC_EXCEPT IS NOT NULL ";
        strFindQuery += "GROUP BY REG_DT ";
        strFindQuery += "ORDER BY REG_DT ";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int cnt = 0; cnt < modem_statList.Items.Count; cnt++)
            {
                if (modem_statList.Items[cnt].SubItems[1].Text == sqlRdr[0].ToString().Trim())
                {
                    modem_statList.Items[cnt].SubItems[13].Text = sqlRdr[1].ToString().Trim();
                    break;
                }
            }
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

    int col2Cnt = 0, col2Val = 0;
    int col3Cnt = 0, col3Val = 0;
    int col4Cnt = 0; double col4Val = 0.00d;
    int col5Cnt = 0, col5Val = 0;
    int col6Cnt = 0, col6Val = 0;
    int col7Cnt = 0, col7Val = 0;
    int col9Cnt = 0, col9Val = 0;
    int col11Cnt = 0, col11Val = 0;
    int col13Cnt = 0, col13Val = 0;

    for (int cnt = 0; cnt < modem_statList.Items.Count; cnt++)
    {
        if (modem_statList.Items[cnt].SubItems[2].Text != "-")
        {
            col2Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[2].Text);
            col2Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[3].Text != "-")
        {
            col3Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[3].Text);
            col3Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[4].Text != "-")
        {
            col4Val += Convert.ToDouble(modem_statList.Items[cnt].SubItems[4].Text);
            col4Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[5].Text != "-")
        {
            col5Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[5].Text);
            col5Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[6].Text != "-")
        {
            col6Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[6].Text);
            col6Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[7].Text != "-")
        {
            col7Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[7].Text);
            col7Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[9].Text != "-")
        {
            col9Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[9].Text);
            col9Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[11].Text != "-")
        {
            col11Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[11].Text);
            col11Cnt++;
        }
        if (modem_statList.Items[cnt].SubItems[13].Text != "-")
        {
            col13Val += Convert.ToInt32(modem_statList.Items[cnt].SubItems[13].Text);
            col13Cnt++;
        }
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("합계 / 평균");
    if (col2Cnt != 0) tItem.SubItems.Add(col2Val.ToString() + " / " + (col2Val / col2Cnt).ToString());
    else tItem.SubItems.Add("- / -");
    if (col3Cnt != 0) tItem.SubItems.Add(col3Val.ToString());
    else tItem.SubItems.Add("-");
    if (col4Cnt != 0) tItem.SubItems.Add((col5Val*100.00d/col3Val).ToString("N2"));
    else tItem.SubItems.Add("-");
    if (col5Cnt != 0) tItem.SubItems.Add(col5Val.ToString());
    else tItem.SubItems.Add("-");
    if (col6Cnt != 0) tItem.SubItems.Add(col6Val.ToString());
    else tItem.SubItems.Add("-");
    if (col7Cnt != 0) tItem.SubItems.Add(col7Val.ToString());
    else tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    if (col9Cnt != 0) tItem.SubItems.Add(col9Val.ToString() + " / " + (Convert.ToDouble(col9Val) / col9Cnt).ToString("N2"));
    else tItem.SubItems.Add("- / -");
    tItem.SubItems.Add("-");
    if (col11Cnt != 0) tItem.SubItems.Add(col11Val.ToString() + " / " + (Convert.ToDouble(col11Val) / col11Cnt).ToString("N2"));
    else tItem.SubItems.Add("- / -");
    if (col13Cnt != 0) tItem.SubItems.Add(col13Val.ToString() + " / " + (Convert.ToDouble(col13Val) / col13Cnt).ToString("N2"));
    else tItem.SubItems.Add("- / -");
    tItem.SubItems.Add("-");
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    modem_statList.Items.Add(tItem);

    modem_statList.EndUpdate();

    location_text.Text = location_comboBox.Text;

    WaitMsg.HideWait(this);
}

private void modem_insertBtn_Click(object sender, EventArgs e)
{
    if (cmsConnStr == "") return;
    if (modem_statList.Items.Count == 0) return;

    WaitMsg.ShowWait(this, WaitMsg.dbMsg);

    string strSaveQuery = "";
    SqlCommand sqlCmd = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스[TRS-CMS] 연결 성공...");

        for (int cnt = 0; cnt < modem_statList.Items.Count; cnt++)
        {
            if (modem_statList.Items[cnt].SubItems[1].Text != "-" || modem_statList.Items[cnt].SubItems[1].Text != "합계 / 평균")
            {
                strSaveQuery = "INSERT INTO DAY_MODEM_STAT(REG_DT, MODEM_CNT, SEND_CNT, SEND_AVRG, SEND_SUCC, SEND_FAIL, RECV_CNT, AVRG_LMT, AVRG_CNT, RDBM_LMT, RDBM_CNT, FILT_ONOFF, EXCEPT_CNT) VALUES(";
                strSaveQuery += "'" + modem_statList.Items[cnt].SubItems[1].Text + "', ";
                strSaveQuery += "" + modem_statList.Items[cnt].SubItems[2].Text + ", ";
                strSaveQuery += "" + modem_statList.Items[cnt].SubItems[3].Text + ", ";
                strSaveQuery += "" + modem_statList.Items[cnt].SubItems[4].Text + ", ";
                strSaveQuery += "" + modem_statList.Items[cnt].SubItems[5].Text + ", ";
                strSaveQuery += "" + modem_statList.Items[cnt].SubItems[6].Text + ", ";
                strSaveQuery += "" + modem_statList.Items[cnt].SubItems[7].Text + ", ";
                strSaveQuery += "" + modem_statList.Items[cnt].SubItems[8].Text + ", ";
                strSaveQuery += "" + modem_statList.Items[cnt].SubItems[9].Text + ", ";
                strSaveQuery += "" + modem_statList.Items[cnt].SubItems[10].Text + ", ";
                strSaveQuery += "" + modem_statList.Items[cnt].SubItems[11].Text + ", ";
                if (modem_statList.Items[cnt].SubItems[12].Text == "배전")
                {
                    strSaveQuery += "'1', ";
                }
                else
                {
                    strSaveQuery += "'0', ";
                }
                strSaveQuery += "" + modem_statList.Items[cnt].SubItems[13].Text + ")";

                sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
                sqlCmd.ExecuteReader().Close();
            }
        }
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
            Console.WriteLine("데이터베이스[TRS-CMS] 연결 해제...");
        }
    }

    location_text.Text = location_comboBox.Text;

    WaitMsg.HideWait(this);
}

private void location_select(string connStr, int listCnt)
{
    string statDate = stat_to_date.Value.ToString("yyyy-MM-dd");
    string fromMonth = stat_from_date.Value.ToString("yyyy-MM");
    string toMonth = stat_to_date.Value.AddMonths(1).ToString("yyyy-MM");

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(connStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT MODEM_CNT, SEND_AVRG, AVRG_CNT, RDBM_CNT, EXCEPT_CNT ";
        strFindQuery += "FROM DAY_MODEM_STAT ";
        strFindQuery += "WHERE REG_DT = '" + statDate + "'";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            location_statList.Items[listCnt].SubItems[2].Text = sqlRdr[0].ToString().Trim();
            location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 5].Text = sqlRdr[1].ToString().Trim();
            location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 3].Text = sqlRdr[2].ToString().Trim();
            location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 2].Text = sqlRdr[3].ToString().Trim();
            location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 1].Text = sqlRdr[4].ToString().Trim();
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT '" + location_statList.Items[listCnt].SubItems[1].Text + "' LOC, ";
        strFindQuery += "SUBSTRING(REG_DT, 1, 7) RDT, ";
        strFindQuery += "SUM(SEND_CNT) SND, SUM(SEND_SUCC) SUC, ROUND(SUM(SEND_SUCC) * 100.00 / SUM(SEND_CNT), 2) AVG ";
        strFindQuery += "FROM DAY_MODEM_STAT ";
        strFindQuery += "WHERE REG_DT > '" + fromMonth + "' ";
        strFindQuery += "AND REG_DT < '" + toMonth + "' ";
        strFindQuery += "GROUP BY SUBSTRING(REG_DT, 1, 7)";

        SqlDataAdapter adapter = new SqlDataAdapter(strFindQuery, sqlConn);
        adapter.Fill(ds, "RESULT");

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        int sendCnt = 0, sendSucc = 0;
        while (sqlRdr.Read())
        {
            for (int col = 3; col < location_statList.Columns.Count - 6; col++)
            {
                if (col == location_statList.Columns.Count - 8) continue;

                if (location_statList.Columns[col].Tag.ToString() == sqlRdr[1].ToString().Trim())
                {
                    location_statList.Items[listCnt].SubItems[col].Text = sqlRdr[4].ToString().Trim().Substring(0, 5);
                        //= (Convert.ToInt32(sqlRdr[2].ToString().Trim()) * 100.00d / Convert.ToInt32(sqlRdr[1].ToString().Trim())).ToString("N2");

                    sendCnt += Convert.ToInt32(sqlRdr[2].ToString().Trim());
                    sendSucc += Convert.ToInt32(sqlRdr[3].ToString().Trim());
                }
            }
        }

        if (sendCnt != 0) location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 8].Text = (sendSucc * 100.00d / sendCnt).ToString("N2");
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

    if (location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 8].Text != "-" && location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 7].Text != "-")
    {
        location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 6].Text
            = (Convert.ToDouble(location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 7].Text)
            - Convert.ToDouble(location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 8].Text)).ToString("N2");

        if (Convert.ToDouble(location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 6].Text) > 0) location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 6].ForeColor = Color.Blue;
        if (Convert.ToDouble(location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 6].Text) < 0) location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 6].ForeColor = Color.Red;
    }

    if (location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 8].Text != "-" && location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 5].Text != "-")
    {
        location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 4].Text
            = (Convert.ToDouble(location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 5].Text)
            - Convert.ToDouble(location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 8].Text)).ToString("N2");

        if (Convert.ToDouble(location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 4].Text) > 0) location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 4].ForeColor = Color.Blue;
        if (Convert.ToDouble(location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 4].Text) < 0) location_statList.Items[listCnt].SubItems[location_statList.Columns.Count - 4].ForeColor = Color.Red;
    }
}

```

```sql
-----------------------------
--modem_selectBtn_Click
--select 2 date 
-----------------------------
SELECT
  REG_DT
  , MODEM_CNT
  , SEND_CNT
  , SEND_AVRG
  , SEND_SUCC
  , SEND_FAIL
  , RECV_CNT
  , AVRG_LMT
  , AVRG_CNT
  , RDBM_LMT
  , RDBM_CNT
  , FILT_ONOFF
  , EXCEPT_CNT
FROM
  DAY_MODEM_STAT
WHERE
  REG_DT     >= '2010-11-24'
  AND REG_DT <= '2019-11-24'
ORDER BY
  REG_DT
-----------------------------
--modem_deleteBtn_Click
-----------------------------

-----------------------------
--modem_searchBtn_Click
--AVRG_LIMIT = 0
--RDBM_LIMIT = -100
-----------------------------
SELECT
    REG_DT
    , COUNT(*)                 CNT
    , SUM(SEND_CNT)            SND
    , ROUND(AVG(SEND_AVRG), 2) AVR
    , SUM(SEND_SUCC)           SUC
    , SUM(SEND_FAIL)           FIL
    , SUM(RECV_CNT)            RCV
    , COUNT
    (
        CASE
            WHEN( SEND_AVRG <= 0)
                THEN 1
            ELSE 
                NULL
        END
    ) AVRG_LMT
    , COUNT
    (
        CASE
            WHEN( RDBM_AVRG <= -100)
                THEN 1
            ELSE 
                NULL
        END
    ) RDBM_LMT
FROM
    DAY_MODEM_INFO
WHERE
    REG_DT     >= '2010-11-24'
    AND REG_DT <= '2019-11-24'
--   AND
--   (
--     GROUP_CODE    = 'BJ'
--     OR GROUP_CODE = 'TD'
--   )
    AND SUCC_EXCEPT IS NULL
GROUP BY
    REG_DT
ORDER BY
    REG_DT

--
SELECT
    REG_DT
    , COUNT(*) CNT
FROM
    DAY_MODEM_INFO
WHERE
    REG_DT     >= '2010-11-24'
    AND REG_DT <= '2019-11-24'
    --AND
    --(
    --          GROUP_CODE    = 'BJ'
    --          OR GROUP_CODE = 'TD'
    --)
    AND SUCC_EXCEPT IS NOT NULL
GROUP BY
    REG_DT
ORDER BY
    REG_DT
-----------------------------
--modem_insertBtn_Click
-----------------------------

-----------------------------
--location_select
--select day 
-----------------------------
SELECT
    MODEM_CNT
    , SEND_AVRG
    , AVRG_CNT
    , RDBM_CNT
    , EXCEPT_CNT
FROM
    DAY_MODEM_STAT
WHERE
    REG_DT = '2019-11-21'

--
SELECT
    '서울'                                              LOC
    , SUBSTRING(REG_DT, 1, 7)                           RDT
    , SUM(SEND_CNT)                                     SND
    , SUM(SEND_SUCC)                                    SUC
    , ROUND(SUM(SEND_SUCC) * 100.00 / SUM(SEND_CNT), 2) AVG
FROM
    DAY_MODEM_STAT
WHERE
    REG_DT     > '2010-11-24'
    AND REG_DT < '2019-11-24'
GROUP BY
    SUBSTRING(REG_DT, 1, 7)

```

