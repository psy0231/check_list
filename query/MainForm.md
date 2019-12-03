- MainForm

```C#

private void data_list_search()
{
    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT REG_DT ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "GROUP BY REG_DT ";
        strFindQuery += "ORDER BY REG_DT DESC";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        day_columnsorter.previousColumn = -1;
        day_flag = 0;
        day_list.ListViewItemSorter = null;

        day_list.Items.Clear();
        day_list.BeginUpdate();

        while (sqlRdr.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.SubItems.Add(sqlRdr[0].ToString().Trim());
            day_list.Items.Add(Item);
        }

        day_list.EndUpdate();

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT SUBSTRING(REG_DT, 1, 7) ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "GROUP BY SUBSTRING(REG_DT, 1, 7) ";
        strFindQuery += "ORDER BY SUBSTRING(REG_DT, 1, 7) DESC";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        month_columnsorter.previousColumn = -1;
        month_flag = 0;
        month_list.ListViewItemSorter = null;

        month_list.Items.Clear();
        month_list.BeginUpdate();

        while (sqlRdr.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.SubItems.Add(sqlRdr[0].ToString().Trim());
            month_list.Items.Add(Item);
        }

        month_list.EndUpdate();

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT SUBSTRING(REG_DT, 1, 4) ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "GROUP BY SUBSTRING(REG_DT, 1, 4) ";
        strFindQuery += "ORDER BY SUBSTRING(REG_DT, 1, 4) DESC";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        year_columnsorter.previousColumn = -1;
        year_flag = 0;
        year_list.ListViewItemSorter = null;

        year_list.Items.Clear();
        year_list.BeginUpdate();

        while (sqlRdr.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.SubItems.Add(sqlRdr[0].ToString().Trim());
            year_list.Items.Add(Item);
        }

        year_list.EndUpdate();
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

    if (data_tab.SelectedIndex == 0)
    {
        for (int cnt = 0; cnt < day_list.Items.Count; cnt++)
        {
            day_list.Items[cnt].BackColor = SystemColors.Window;
            day_list.Items[cnt].ForeColor = SystemColors.WindowText;
        }

        ListViewItem dItem = null;
        if (day_list.Items.Count > 0)
        {
            dItem = day_list.FindItemWithText(search_date.Value.ToString("yyyy-MM-dd"), true, 0);
        }

        if (dItem != null)
        {
            dItem.BackColor = SystemColors.MenuHighlight;
            dItem.ForeColor = SystemColors.Window;
            dItem.Selected = true;
        }
        else day_list.SelectedItems.Clear();
    }
    else if (data_tab.SelectedIndex == 1)
    {
        for (int cnt = 0; cnt < month_list.Items.Count; cnt++)
        {
            month_list.Items[cnt].BackColor = SystemColors.Window;
            month_list.Items[cnt].ForeColor = SystemColors.WindowText;
        }

        ListViewItem mItem = null;
        if (month_list.Items.Count > 0)
        {
            mItem = month_list.FindItemWithText(search_date.Value.ToString("yyyy-MM"), true, 0);
        }

        if (mItem != null)
        {
            mItem.BackColor = SystemColors.MenuHighlight;
            mItem.ForeColor = SystemColors.Window;
            mItem.Selected = true;
        }
        else month_list.SelectedItems.Clear();
    }
    else if (data_tab.SelectedIndex == 2)
    {
        for (int cnt = 0; cnt < year_list.Items.Count; cnt++)
        {
            year_list.Items[cnt].BackColor = SystemColors.Window;
            year_list.Items[cnt].ForeColor = SystemColors.WindowText;
        }

        ListViewItem yItem = null;
        if (year_list.Items.Count > 0)
        {
            yItem = year_list.FindItemWithText(search_date.Value.ToString("yyyy"), true, 0);
        }

        if (yItem != null)
        {
            yItem.BackColor = SystemColors.MenuHighlight;
            yItem.ForeColor = SystemColors.Window;
            yItem.Selected = true;
        }
        else year_list.SelectedItems.Clear();
    }
}


private void day_modem_list_search(string strDate)
{
    modem_columnsorter.previousColumn = -1;
    modem_flag = 0;
    modem_list.ListViewItemSorter = null;

    modem_list.Items.Clear();
    if (modem_list.Columns.Count > 18)
    {
        for (int cnt = modem_list.Columns.Count; cnt > 18; cnt--)
        {
            modem_list.Columns.RemoveAt(cnt - 1);
        }
    }
    rdbmCriCnt = 0; rdbmMajCnt = 0; rdbmMinCnt = 0;
    diffCriCnt = 0; diffMajCnt = 0; diffMinCnt = 0;
    avrgCriCnt = 0; avrgMajCnt = 0; avrgMinCnt = 0;

    modem_list.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...1");

        strFindQuery = "SELECT TBS_NAME, TBS_ID+0 TBS ";
        strFindQuery += "FROM DAY_TBS_INFO ";
        strFindQuery += "WHERE REG_DT = '" + strDate + "' ";
        strFindQuery += "ORDER BY TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            modem_list.Columns.Add(sqlRdr[0].ToString().Trim());
            modem_list.Columns[modem_list.Columns.Count - 1].Tag = sqlRdr[1].ToString().Trim();
            modem_list.Columns[modem_list.Columns.Count - 1].TextAlign = HorizontalAlignment.Center;
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        string[] sItem = new string[modem_list.Columns.Count - 18];
        for (int cnt = 0; cnt < modem_list.Columns.Count - 18; cnt++)
        {
            sItem[cnt] = "-";
        }

        strFindQuery = "SELECT GROUP_CODE, GROUP_NAME, NODE_NAME, NODE_ID, MODEM_ISSI, RDBM_AVRG, RDBM_DIFF, RDBM_MAX, RDBM_MIN, ";
        strFindQuery += "SEND_CNT, SEND_AVRG, SEND_SUCC, SEND_FAIL, RECV_CNT, RECV_TBS, HO_CNT, SUCC_EXCEPT ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE REG_DT = '" + strDate + "' ";
        if (filtOnOff)
        {
            strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
        }
        strFindQuery += "ORDER BY MODEM_ISSI";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            Item.SubItems.Add((modem_list.Items.Count + 1).ToString());
            if (sqlRdr[0].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[0].ToString().Trim());
            if (sqlRdr[1].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[1].ToString().Trim());
            if (sqlRdr[2].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[2].ToString().Trim());
            if (sqlRdr[3].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[3].ToString().Trim());
            if (sqlRdr[4].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[4].ToString().Trim());
            if (sqlRdr[5].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[5].ToString().Trim());
            if (sqlRdr[6].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[6].ToString().Trim());
            if (sqlRdr[7].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[7].ToString().Trim());
            if (sqlRdr[8].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[8].ToString().Trim());
            if (sqlRdr[9].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[9].ToString().Trim());
            if (sqlRdr[10].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[10].ToString().Trim());
            if (sqlRdr[11].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[11].ToString().Trim());
            if (sqlRdr[12].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[12].ToString().Trim());
            if (sqlRdr[13].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[13].ToString().Trim());
            if (sqlRdr[14].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[14].ToString().Trim());
            if (sqlRdr[15].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[15].ToString().Trim());
            if (sqlRdr[16].ToString().Trim() == "") Item.Checked = false;
            else Item.Checked = true;
            Item.SubItems.AddRange(sItem);

            if (Item.SubItems[7].Text == "-")
            {
                Item.SubItems[7].BackColor = Color.Gray;
            }
            else if (Convert.ToInt32(Item.SubItems[7].Text) < rdbmCritical)
            {
                Item.SubItems[7].BackColor = Color.Red;
                rdbmCriCnt++;
            }
            else if (Convert.ToInt32(Item.SubItems[7].Text) < rdbmMajor)
            {
                Item.SubItems[7].BackColor = Color.Orange;
                rdbmMajCnt++;
            }
            else if (Convert.ToInt32(Item.SubItems[7].Text) < rdbmMinor)
            {
                Item.SubItems[7].BackColor = Color.Yellow;
                rdbmMinCnt++;
            }
            else
            {
                Item.SubItems[7].BackColor = Color.White;
            }

            if (Item.SubItems[8].Text == "-")
            {
                Item.SubItems[8].BackColor = Color.Gray;
            }
            else if (Convert.ToInt32(Item.SubItems[8].Text) < pdbmCritical)
            {
                Item.SubItems[8].BackColor = Color.Red;
                diffCriCnt++;
            }
            else if (Convert.ToInt32(Item.SubItems[8].Text) < pdbmMajor)
            {
                Item.SubItems[8].BackColor = Color.Orange;
                diffMajCnt++;
            }
            else if (Convert.ToInt32(Item.SubItems[8].Text) < pdbmMinor)
            {
                Item.SubItems[8].BackColor = Color.Yellow;
                diffMinCnt++;
            }
            else
            {
                Item.SubItems[8].BackColor = Color.White;
            }

            if (Item.SubItems[12].Text == "-")
            {
                Item.SubItems[12].BackColor = Color.Gray;
            }
            else if (Convert.ToDouble(Item.SubItems[12].Text) < Convert.ToDouble(avrgCritical))
            {
                Item.SubItems[12].BackColor = Color.Red;
                avrgCriCnt++;
            }
            else if (Convert.ToDouble(Item.SubItems[12].Text) < Convert.ToDouble(avrgMajor))
            {
                Item.SubItems[12].BackColor = Color.Orange;
                avrgMajCnt++;
            }
            else if (Convert.ToDouble(Item.SubItems[12].Text) < Convert.ToDouble(avrgMinor))
            {
                Item.SubItems[12].BackColor = Color.Yellow;
                avrgMinCnt++;
            }
            else
            {
                Item.SubItems[12].BackColor = Color.White;
            }

            if (Item.Checked)
            {
                Item.SubItems[11].BackColor = Color.Gray;
                Item.SubItems[12].BackColor = Color.Gray;
                Item.SubItems[13].BackColor = Color.Gray;
                Item.SubItems[14].BackColor = Color.Gray;
                Item.SubItems[15].BackColor = Color.Gray;
            }

            modem_list.Items.Add(Item);
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT MODEM_ISSI, TBS_ID+0 TBS, RECV_CNT ";
        strFindQuery += "FROM DAY_MODEM_RECV ";
        strFindQuery += "WHERE REG_DT = '" + strDate + "' ";
        strFindQuery += "ORDER BY MODEM_ISSI, TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem fItem = null;
            if (modem_list.Items.Count > 0)
            {
                fItem = modem_list.FindItemWithText(sqlRdr[0].ToString().Trim(), true, 0);
            }

            if (fItem != null)
            {
                for (int cnt = 18; cnt < modem_list.Columns.Count; cnt++)
                {
                    if (modem_list.Columns[cnt].Tag.ToString() == sqlRdr[1].ToString().Trim())
                    {
                        fItem.SubItems[cnt].Text = sqlRdr[2].ToString().Trim();
                    }
                    else if (fItem.SubItems[cnt].Text == "-")
                    {
                        fItem.SubItems[cnt].Text = "0";
                    }
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
            Console.WriteLine("데이터베이스 연결 해제...1");
        }
    }

    int avgdbmCnt = 0, avgdbmVal = 0;
    int difdbmCnt = 0, difdbmVal = 0;
    int maxdbmCnt = 0, maxdbmVal = 0;
    int mindbmCnt = 0, mindbmVal = 0;
    int sendCnt = 0, sendVal = 0;
    int avgsendCnt = 0; double avgsendVal = 0.00d;
    int succsendCnt = 0, succsendVal = 0;
    int failsendCnt = 0, failsendVal = 0;
    int recvCnt = 0, recvVal = 0;
    int[] tbsCnt = new int[modem_list.Columns.Count - 18];
    int[] tbsVal = new int[modem_list.Columns.Count - 18];

    for (int cnt = 0; cnt < modem_list.Items.Count; cnt++)
    {
        if (modem_list.Items[cnt].SubItems[7].Text != "-") { avgdbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[7].Text); avgdbmCnt++; }
        if (modem_list.Items[cnt].SubItems[8].Text != "-") { difdbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[8].Text); difdbmCnt++; }
        if (modem_list.Items[cnt].SubItems[9].Text != "-") { maxdbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[9].Text); maxdbmCnt++; }
        if (modem_list.Items[cnt].SubItems[10].Text != "-") { mindbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[10].Text); mindbmCnt++; }
        if (modem_list.Items[cnt].SubItems[11].Text != "-" && !modem_list.Items[cnt].Checked) { sendVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[11].Text); sendCnt++; }
        if (modem_list.Items[cnt].SubItems[12].Text != "-" && !modem_list.Items[cnt].Checked) { avgsendVal += Convert.ToDouble(modem_list.Items[cnt].SubItems[12].Text); avgsendCnt++; }
        if (modem_list.Items[cnt].SubItems[13].Text != "-" && !modem_list.Items[cnt].Checked) { succsendVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[13].Text); succsendCnt++; }
        if (modem_list.Items[cnt].SubItems[14].Text != "-" && !modem_list.Items[cnt].Checked) { failsendVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[14].Text); failsendCnt++; }
        if (modem_list.Items[cnt].SubItems[15].Text != "-" && !modem_list.Items[cnt].Checked) { recvVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[15].Text); recvCnt++; }
        for (int idx = 18; idx < modem_list.Columns.Count; idx++)
        {
            if (modem_list.Items[cnt].SubItems[idx].Text != "-") { tbsVal[idx - 18] += Convert.ToInt32(modem_list.Items[cnt].SubItems[idx].Text); tbsCnt[idx - 18]++; }
        }
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("합계 / 평균");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    if (avgdbmCnt != 0) tItem.SubItems.Add((avgdbmVal / avgdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (difdbmCnt != 0) tItem.SubItems.Add((difdbmVal / difdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (maxdbmCnt != 0) tItem.SubItems.Add((maxdbmVal / maxdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (mindbmCnt != 0) tItem.SubItems.Add((mindbmVal / mindbmCnt).ToString());
    else tItem.SubItems.Add("0");
    tItem.SubItems.Add(sendVal.ToString());
    if (avgsendCnt != 0) tItem.SubItems.Add((succsendVal * 100.00d / sendVal).ToString("N2"));
    else tItem.SubItems.Add("0.00");
    tItem.SubItems.Add(succsendVal.ToString());
    tItem.SubItems.Add(failsendVal.ToString());
    tItem.SubItems.Add(recvVal.ToString());
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    for (int idx = 18; idx < modem_list.Columns.Count; idx++)
    {
        tItem.SubItems.Add(tbsVal[idx - 18].ToString());
    }
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    modem_list.Items.Add(tItem);

    if (item_comboBox.SelectedIndex == 2)
    {
        if (modem_list.Items.Count == 0)
        {
            modem_total.Text = modem_list.Items.Count.ToString();
        }
        else
        {
            modem_total.Text = (modem_list.Items.Count - 1).ToString();
        }
        modem_critical.Text = avrgCriCnt.ToString();
        modem_major.Text = avrgMajCnt.ToString();
        modem_minor.Text = avrgMinCnt.ToString();
    }
    else
    {
        item_comboBox.SelectedIndex = 2;
    }

    modem_list.EndUpdate();
}


private void day_tbs_list_search(string strDate)
{
    tbs_columnsorter.previousColumn = -1;
    tbs_flag = 0;
    tbs_list.ListViewItemSorter = null;

    tbs_list.Items.Clear();
    tbs_list.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT TBS_ID+0 TBS, TBS_NAME, MODEM_CNT, MODEM_MAX, MODEM_TOT ";
        strFindQuery += "FROM DAY_TBS_INFO ";
        strFindQuery += "WHERE REG_DT = '" + strDate + "' ";
        strFindQuery += "ORDER BY TBS";

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
            Item.SubItems.Add("-");
            Item.SubItems[6].BackColor = Color.Gray;
            Item.SubItems[6].Tag = "-";
            Item.SubItems.Add("0");
            Item.SubItems[7].Tag = "-";
            tbs_list.Items.Add(Item);
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }
        
        strFindQuery = "SELECT TBS_ID+0 TBS, SUM(TBS_COLL) ";
        strFindQuery += "FROM DAY_TBS_COLL ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_ID ";
        strFindQuery += "ORDER BY TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == sqlRdr[0].ToString().Trim())
                {
                    tbs_list.Items[i].SubItems[6].Text = sqlRdr[1].ToString().Trim();

                    if (Convert.ToInt32(tbs_list.Items[i].SubItems[6].Text) >= collCritical)
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.Red;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[6].Text) >= collMajor)
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.Orange;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[6].Text) >= collMinor)
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.Yellow;
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.White;
                    }

                    break;
                }
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }
        
        strFindQuery = "SELECT TBS_ID+0 TBS, REG_DT, TBS_COLL ";
        strFindQuery += "FROM DAY_TBS_COLL ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "ORDER BY TBS, REG_DT";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == sqlRdr[0].ToString().Trim())
                {
                    if (tbs_list.Items[i].SubItems[6].Tag.ToString() == "-")
                    {
                        tbs_list.Items[i].SubItems[6].Tag = sqlRdr[1].ToString().Trim() + " : " + sqlRdr[2].ToString().Trim();
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[6].Tag += "\r\n" + sqlRdr[1].ToString().Trim() + " : " + sqlRdr[2].ToString().Trim();
                    }

                    break;
                }
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT TBS_ID+0 TBS, SUM(TTRX0_JAMM) + SUM(TTRX1_JAMM) + SUM(TTRX2_JAMM) ";
        strFindQuery += "FROM DAY_TBS_JAMM ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_ID ";
        strFindQuery += "ORDER BY TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == sqlRdr[0].ToString().Trim())
                {
                    tbs_list.Items[i].SubItems[7].Text = sqlRdr[1].ToString().Trim();

                    if (Convert.ToInt32(tbs_list.Items[i].SubItems[7].Text) >= jammCritical)
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.Red;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[7].Text) >= jammMajor)
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.Orange;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[7].Text) >= jammMinor)
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.Yellow;
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.White;
                    }

                    break;
                }
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT TBS_ID+0 TBS, REG_DT, TTRX0_JAMM, TTRX1_JAMM, TTRX2_JAMM ";
        strFindQuery += "FROM DAY_TBS_JAMM ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "ORDER BY TBS, REG_DT";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == sqlRdr[0].ToString().Trim())
                {
                    if (tbs_list.Items[i].SubItems[7].Tag.ToString() == "-")
                    {
                        tbs_list.Items[i].SubItems[7].Tag = sqlRdr[1].ToString().Trim() + " : " + sqlRdr[2].ToString().Trim() + " / " + sqlRdr[3].ToString().Trim() + " / " + sqlRdr[4].ToString().Trim();
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[7].Tag += "\r\n" + sqlRdr[1].ToString().Trim() + " : " + sqlRdr[2].ToString().Trim() + " / " + sqlRdr[3].ToString().Trim() + " / " + sqlRdr[4].ToString().Trim();
                    }

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

    int modemCnt = 0, maxCnt = 0, totCnt = 0, rcCnt = 0, jamCnt = 0;
    for (int cnt = 0; cnt < tbs_list.Items.Count; cnt++)
    {
        if (tbs_list.Items[cnt].SubItems[3].Text != "-") modemCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[3].Text);
        if (tbs_list.Items[cnt].SubItems[4].Text != "-") maxCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[4].Text);
        if (tbs_list.Items[cnt].SubItems[5].Text != "-") totCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[5].Text);
        if (tbs_list.Items[cnt].SubItems[6].Text != "-") rcCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[6].Text);
        if (tbs_list.Items[cnt].SubItems[7].Text != "-") jamCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[7].Text);
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("합계");
    tItem.SubItems.Add(modemCnt.ToString());
    tItem.SubItems.Add(maxCnt.ToString());
    tItem.SubItems.Add(totCnt.ToString());
    tItem.SubItems.Add(rcCnt.ToString());
    tItem.SubItems.Add(jamCnt.ToString());
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    tbs_list.Items.Add(tItem);

    tbs_list.EndUpdate();
}


private void day_group_list_search(string strDate)
{
    group_columnsorter.previousColumn = -1;
    group_flag = 0;
    group_list.ListViewItemSorter = null;

    group_list.Items.Clear();
    group_list.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT GROUP_CODE CODE, GROUP_ID ID, GROUP_NAME, MODEM_CNT, SEND_CNT, RECV_CNT ";
        strFindQuery += "FROM DAY_GROUP_INFO ";
        strFindQuery += "WHERE REG_DT = '" + strDate + "' ";
        if (filtOnOff)
        {
            strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
        }
        strFindQuery += "ORDER BY CODE, ID";

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
            group_list.Items.Add(Item);
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

    int modemCnt = 0, sendCnt = 0, recvCnt = 0;
    for (int cnt = 0; cnt < group_list.Items.Count; cnt++)
    {
        modemCnt += Convert.ToInt32(group_list.Items[cnt].SubItems[4].Text);
        sendCnt += Convert.ToInt32(group_list.Items[cnt].SubItems[5].Text);
        recvCnt += Convert.ToInt32(group_list.Items[cnt].SubItems[6].Text);
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("합계");
    tItem.SubItems.Add(modemCnt.ToString());
    tItem.SubItems.Add(sendCnt.ToString());
    tItem.SubItems.Add(recvCnt.ToString());
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    group_list.Items.Add(tItem);

    group_list.EndUpdate();
}


private void month_modem_list_search(string strDate)
{
    modem_columnsorter.previousColumn = -1;
    modem_flag = 0;
    modem_list.ListViewItemSorter = null;

    modem_list.Items.Clear();
    if (modem_list.Columns.Count > 18)
    {
        for (int cnt = modem_list.Columns.Count; cnt > 18; cnt--)
        {
            modem_list.Columns.RemoveAt(cnt - 1);
        }
    }
    rdbmCriCnt = 0; rdbmMajCnt = 0; rdbmMinCnt = 0;
    diffCriCnt = 0; diffMajCnt = 0; diffMinCnt = 0;
    avrgCriCnt = 0; avrgMajCnt = 0; avrgMinCnt = 0;

    modem_list.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...1");

        strFindQuery = "SELECT TBS_NAME, TBS_ID+0 TBS ";
        strFindQuery += "FROM DAY_TBS_INFO ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_NAME, TBS_ID ";
        strFindQuery += "ORDER BY TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        string buffer = "";
        while (sqlRdr.Read())
        {
            if (buffer != sqlRdr[1].ToString().Trim())
            {
                modem_list.Columns.Add(sqlRdr[0].ToString().Trim());
                modem_list.Columns[modem_list.Columns.Count - 1].Tag = sqlRdr[1].ToString().Trim();
                modem_list.Columns[modem_list.Columns.Count - 1].TextAlign = HorizontalAlignment.Center;
                buffer = sqlRdr[1].ToString().Trim();
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        string[] sItem = new string[modem_list.Columns.Count - 18];
        for (int cnt = 0; cnt < modem_list.Columns.Count - 18; cnt++)
        {
            sItem[cnt] = "-";
        }

        strFindQuery = "SELECT GROUP_CODE, GROUP_NAME, NODE_NAME, NODE_ID, MODEM_ISSI, SUM(SEND_CNT)+SUM(RECV_CNT) CNT ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        if (filtOnOff)
        {
            strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
        }
        strFindQuery += "GROUP BY GROUP_CODE, GROUP_NAME, NODE_NAME, NODE_ID, MODEM_ISSI ";
        strFindQuery += "ORDER BY MODEM_ISSI, CNT DESC";
        Console.WriteLine(strFindQuery);

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        string issi = "";
        while (sqlRdr.Read())
        {
            if (issi != sqlRdr[4].ToString().Trim())
            {
                ListViewItem Item = new ListViewItem("");
                Item.UseItemStyleForSubItems = false;
                Item.SubItems.Add((modem_list.Items.Count + 1).ToString());
                if (sqlRdr[0].ToString().Trim() == "") Item.SubItems.Add("-");
                else Item.SubItems.Add(sqlRdr[0].ToString().Trim());
                if (sqlRdr[1].ToString().Trim() == "") Item.SubItems.Add("-");
                else Item.SubItems.Add(sqlRdr[1].ToString().Trim());
                if (sqlRdr[2].ToString().Trim() == "") Item.SubItems.Add("-");
                else Item.SubItems.Add(sqlRdr[2].ToString().Trim());
                if (sqlRdr[3].ToString().Trim() == "") Item.SubItems.Add("-");
                else Item.SubItems.Add(sqlRdr[3].ToString().Trim());
                if (sqlRdr[4].ToString().Trim() == "") Item.SubItems.Add("-");
                else Item.SubItems.Add(sqlRdr[4].ToString().Trim());
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.AddRange(sItem);

                Item.SubItems[7].BackColor = Color.Gray;
                Item.SubItems[8].BackColor = Color.Gray;
                Item.SubItems[12].BackColor = Color.Gray;

                modem_list.Items.Add(Item);

                issi = sqlRdr[4].ToString().Trim();
            }
            else if (modem_list.Items[modem_list.Items.Count - 1].SubItems[3].Text == "-")
            {
                if (sqlRdr[0].ToString().Trim() == "") modem_list.Items[modem_list.Items.Count - 1].SubItems[2].Text = "-";
                else modem_list.Items[modem_list.Items.Count - 1].SubItems[2].Text = sqlRdr[0].ToString().Trim();
                if (sqlRdr[1].ToString().Trim() == "") modem_list.Items[modem_list.Items.Count - 1].SubItems[3].Text = "-";
                else modem_list.Items[modem_list.Items.Count - 1].SubItems[3].Text = sqlRdr[1].ToString().Trim();
                if (sqlRdr[2].ToString().Trim() == "") modem_list.Items[modem_list.Items.Count - 1].SubItems[4].Text = "-";
                else modem_list.Items[modem_list.Items.Count - 1].SubItems[4].Text = sqlRdr[2].ToString().Trim();
                if (sqlRdr[3].ToString().Trim() == "") modem_list.Items[modem_list.Items.Count - 1].SubItems[5].Text = "-";
                else modem_list.Items[modem_list.Items.Count - 1].SubItems[5].Text = sqlRdr[3].ToString().Trim();
                if (sqlRdr[4].ToString().Trim() == "") modem_list.Items[modem_list.Items.Count - 1].SubItems[6].Text = "-";
                else modem_list.Items[modem_list.Items.Count - 1].SubItems[6].Text = sqlRdr[4].ToString().Trim();

                issi = sqlRdr[4].ToString().Trim();
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT MODEM_ISSI, AVG(RDBM_AVRG), MIN(RDBM_MIN)-MAX(RDBM_MAX), ";
        strFindQuery += "MAX(RDBM_MAX), MIN(RDBM_MIN), SUM(SEND_CNT), ROUND(AVG(SEND_AVRG), 2), SUM(SEND_SUCC), SUM(SEND_FAIL), SUM(RECV_CNT) ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        if (filtOnOff)
        {
            strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
        }
        strFindQuery += "AND SUCC_EXCEPT IS NULL ";
        strFindQuery += "GROUP BY MODEM_ISSI ";
        strFindQuery += "ORDER BY MODEM_ISSI";
        Console.WriteLine(strFindQuery);

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem fItem = null;
            if (modem_list.Items.Count > 0)
            {
                fItem = modem_list.FindItemWithText(sqlRdr[0].ToString().Trim(), true, 0);
            }

            if (fItem != null)
            {
                if (sqlRdr[1].ToString().Trim() == "") fItem.SubItems[7].Text = "-";
                else fItem.SubItems[7].Text = sqlRdr[1].ToString().Trim();
                if (sqlRdr[2].ToString().Trim() == "") fItem.SubItems[8].Text = "-";
                else fItem.SubItems[8].Text = sqlRdr[2].ToString().Trim();
                if (sqlRdr[3].ToString().Trim() == "") fItem.SubItems[9].Text = "-";
                else fItem.SubItems[9].Text = sqlRdr[3].ToString().Trim();
                if (sqlRdr[4].ToString().Trim() == "") fItem.SubItems[10].Text = "-";
                else fItem.SubItems[10].Text = sqlRdr[4].ToString().Trim();
                if (sqlRdr[5].ToString().Trim() == "") fItem.SubItems[11].Text = "-";
                else fItem.SubItems[11].Text = sqlRdr[5].ToString().Trim();
                if (sqlRdr[6].ToString().Trim() == "") fItem.SubItems[12].Text = "-";
                //else fItem.SubItems[12].Text = sqlRdr[6].ToString().Trim();
                else fItem.SubItems[12].Text = (Convert.ToInt32(sqlRdr[7].ToString().Trim()) * 100.00d / Convert.ToInt32(sqlRdr[5].ToString().Trim())).ToString("N2");
                if (sqlRdr[7].ToString().Trim() == "") fItem.SubItems[13].Text = "-";
                else fItem.SubItems[13].Text = sqlRdr[7].ToString().Trim();
                if (sqlRdr[8].ToString().Trim() == "") fItem.SubItems[14].Text = "-";
                else fItem.SubItems[14].Text = sqlRdr[8].ToString().Trim();
                if (sqlRdr[9].ToString().Trim() == "") fItem.SubItems[15].Text = "-";
                else fItem.SubItems[15].Text = sqlRdr[9].ToString().Trim();

                if (fItem.SubItems[7].Text == "-")
                {
                    fItem.SubItems[7].BackColor = Color.Gray;
                }
                else if (Convert.ToInt32(fItem.SubItems[7].Text) < rdbmCritical)
                {
                    fItem.SubItems[7].BackColor = Color.Red;
                    rdbmCriCnt++;
                }
                else if (Convert.ToInt32(fItem.SubItems[7].Text) < rdbmMajor)
                {
                    fItem.SubItems[7].BackColor = Color.Orange;
                    rdbmMajCnt++;
                }
                else if (Convert.ToInt32(fItem.SubItems[7].Text) < rdbmMinor)
                {
                    fItem.SubItems[7].BackColor = Color.Yellow;
                    rdbmMinCnt++;
                }
                else
                {
                    fItem.SubItems[7].BackColor = Color.White;
                }

                if (fItem.SubItems[8].Text == "-")
                {
                    fItem.SubItems[8].BackColor = Color.Gray;
                }
                else if (Convert.ToInt32(fItem.SubItems[8].Text) < pdbmCritical)
                {
                    fItem.SubItems[8].BackColor = Color.Red;
                    diffCriCnt++;
                }
                else if (Convert.ToInt32(fItem.SubItems[8].Text) < pdbmMajor)
                {
                    fItem.SubItems[8].BackColor = Color.Orange;
                    diffMajCnt++;
                }
                else if (Convert.ToInt32(fItem.SubItems[8].Text) < pdbmMinor)
                {
                    fItem.SubItems[8].BackColor = Color.Yellow;
                    diffMinCnt++;
                }
                else
                {
                    fItem.SubItems[8].BackColor = Color.White;
                }

                if (fItem.SubItems[12].Text == "-")
                {
                    fItem.SubItems[12].BackColor = Color.Gray;
                }
                else if (Convert.ToDouble(fItem.SubItems[12].Text) < Convert.ToDouble(avrgCritical))
                {
                    fItem.SubItems[12].BackColor = Color.Red;
                    avrgCriCnt++;
                }
                else if (Convert.ToDouble(fItem.SubItems[12].Text) < Convert.ToDouble(avrgMajor))
                {
                    fItem.SubItems[12].BackColor = Color.Orange;
                    avrgMajCnt++;
                }
                else if (Convert.ToDouble(fItem.SubItems[12].Text) < Convert.ToDouble(avrgMinor))
                {
                    fItem.SubItems[12].BackColor = Color.Yellow;
                    avrgMinCnt++;
                }
                else
                {
                    fItem.SubItems[12].BackColor = Color.White;
                }
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT MODEM_ISSI, TBS_ID+0 TBS, SUM(RECV_CNT) ";
        strFindQuery += "FROM DAY_MODEM_RECV ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY MODEM_ISSI, TBS_ID ";
        strFindQuery += "ORDER BY MODEM_ISSI, TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem fItem = null;
            if (modem_list.Items.Count > 0)
            {
                fItem = modem_list.FindItemWithText(sqlRdr[0].ToString().Trim(), true, 0);
            }

            if (fItem != null)
            {
                int max = 0, col = 0, ho = 0;
                for (int cnt = 18; cnt < modem_list.Columns.Count; cnt++)
                {
                    if (modem_list.Columns[cnt].Tag.ToString() == sqlRdr[1].ToString().Trim())
                    {
                        fItem.SubItems[cnt].Text = sqlRdr[2].ToString().Trim();
                    }
                    else if (fItem.SubItems[cnt].Text == "-")
                    {
                        fItem.SubItems[cnt].Text = "0";
                    }

                    if (Convert.ToInt32(fItem.SubItems[cnt].Text) > max)
                    {
                        max = Convert.ToInt32(fItem.SubItems[cnt].Text);
                        col = cnt;
                    }

                    if (Convert.ToInt32(fItem.SubItems[cnt].Text) > 0)
                    {
                        ho++;
                    }
                }
                if (col > 0) fItem.SubItems[16].Text = modem_list.Columns[col].Text;
                if (ho > 0) fItem.SubItems[17].Text = ho.ToString();
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
            Console.WriteLine("데이터베이스 연결 해제...1");
        }
    }

    int avgdbmCnt = 0, avgdbmVal = 0;
    int difdbmCnt = 0, difdbmVal = 0;
    int maxdbmCnt = 0, maxdbmVal = 0;
    int mindbmCnt = 0, mindbmVal = 0;
    int sendCnt = 0, sendVal = 0;
    int avgsendCnt = 0; double avgsendVal = 0.00d;
    int succsendCnt = 0, succsendVal = 0;
    int failsendCnt = 0, failsendVal = 0;
    int recvCnt = 0, recvVal = 0;
    int[] tbsCnt = new int[modem_list.Columns.Count - 18];
    int[] tbsVal = new int[modem_list.Columns.Count - 18];

    for (int cnt = 0; cnt < modem_list.Items.Count; cnt++)
    {
        if (modem_list.Items[cnt].SubItems[7].Text != "-") { avgdbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[7].Text); avgdbmCnt++; }
        if (modem_list.Items[cnt].SubItems[8].Text != "-") { difdbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[8].Text); difdbmCnt++; }
        if (modem_list.Items[cnt].SubItems[9].Text != "-") { maxdbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[9].Text); maxdbmCnt++; }
        if (modem_list.Items[cnt].SubItems[10].Text != "-") { mindbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[10].Text); mindbmCnt++; }
        if (modem_list.Items[cnt].SubItems[11].Text != "-") { sendVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[11].Text); sendCnt++; }
        if (modem_list.Items[cnt].SubItems[12].Text != "-") { avgsendVal += Convert.ToDouble(modem_list.Items[cnt].SubItems[12].Text); avgsendCnt++; }
        if (modem_list.Items[cnt].SubItems[13].Text != "-") { succsendVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[13].Text); succsendCnt++; }
        if (modem_list.Items[cnt].SubItems[14].Text != "-") { failsendVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[14].Text); failsendCnt++; }
        if (modem_list.Items[cnt].SubItems[15].Text != "-") { recvVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[15].Text); recvCnt++; }
        for (int idx = 18; idx < modem_list.Columns.Count; idx++)
        {
            if (modem_list.Items[cnt].SubItems[idx].Text != "-") { tbsVal[idx - 18] += Convert.ToInt32(modem_list.Items[cnt].SubItems[idx].Text); tbsCnt[idx - 18]++; }
        }
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("합계 / 평균");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    if (avgdbmCnt != 0) tItem.SubItems.Add((avgdbmVal / avgdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (difdbmCnt != 0) tItem.SubItems.Add((difdbmVal / difdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (maxdbmCnt != 0) tItem.SubItems.Add((maxdbmVal / maxdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (mindbmCnt != 0) tItem.SubItems.Add((mindbmVal / mindbmCnt).ToString());
    else tItem.SubItems.Add("0");
    tItem.SubItems.Add(sendVal.ToString());
    if (avgsendCnt != 0) tItem.SubItems.Add((succsendVal * 100.00d / sendVal).ToString("N2"));
    else tItem.SubItems.Add("0.00");
    tItem.SubItems.Add(succsendVal.ToString());
    tItem.SubItems.Add(failsendVal.ToString());
    tItem.SubItems.Add(recvVal.ToString());
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    for (int idx = 18; idx < modem_list.Columns.Count; idx++)
    {
        tItem.SubItems.Add(tbsVal[idx - 18].ToString());
    }
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    modem_list.Items.Add(tItem);

    if (item_comboBox.SelectedIndex == 2)
    {
        if (modem_list.Items.Count == 0)
        {
            modem_total.Text = modem_list.Items.Count.ToString();
        }
        else
        {
            modem_total.Text = (modem_list.Items.Count - 1).ToString();
        }

        modem_critical.Text = avrgCriCnt.ToString();
        modem_major.Text = avrgMajCnt.ToString();
        modem_minor.Text = avrgMinCnt.ToString();
    }
    else
    {
        item_comboBox.SelectedIndex = 2;
    }

    modem_list.EndUpdate();
}


private void month_tbs_list_search(string strDate)
{
    tbs_columnsorter.previousColumn = -1;
    tbs_flag = 0;
    tbs_list.ListViewItemSorter = null;

    tbs_list.Items.Clear();
    tbs_list.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT TBS_ID+0 TBS, TBS_NAME, AVG(MODEM_CNT), MAX(MODEM_MAX), AVG(MODEM_TOT) ";
        strFindQuery += "FROM DAY_TBS_INFO ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_ID, TBS_NAME ";
        strFindQuery += "ORDER BY TBS";

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
            Item.SubItems.Add("-");
            Item.SubItems[6].BackColor = Color.Gray;
            Item.SubItems[6].Tag = "-";
            Item.SubItems.Add("0");
            Item.SubItems[7].Tag = "-";
            tbs_list.Items.Add(Item);
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT TBS_ID+0 TBS, SUM(TBS_COLL) ";
        strFindQuery += "FROM DAY_TBS_COLL ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_ID ";
        strFindQuery += "ORDER BY TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == sqlRdr[0].ToString().Trim())
                {
                    tbs_list.Items[i].SubItems[6].Text = sqlRdr[1].ToString().Trim();

                    if (Convert.ToInt32(tbs_list.Items[i].SubItems[6].Text) >= collCritical)
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.Red;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[6].Text) >= collMajor)
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.Orange;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[6].Text) >= collMinor)
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.Yellow;
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.White;
                    }

                    break;
                }
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT TBS_ID+0 TBS, SUBSTRING(REG_DT, 1, 10), SUM(TBS_COLL) ";
        strFindQuery += "FROM DAY_TBS_COLL ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_ID, SUBSTRING(REG_DT, 1, 10) ";
        strFindQuery += "ORDER BY TBS, SUBSTRING(REG_DT, 1, 10)";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == sqlRdr[0].ToString().Trim())
                {
                    if (tbs_list.Items[i].SubItems[6].Tag.ToString() == "-")
                    {
                        tbs_list.Items[i].SubItems[6].Tag = sqlRdr[1].ToString().Trim() + " : " + sqlRdr[2].ToString().Trim();
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[6].Tag += "\r\n" + sqlRdr[1].ToString().Trim() + " : " + sqlRdr[2].ToString().Trim();
                    }

                    break;
                }
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT TBS_ID+0 TBS, SUM(TTRX0_JAMM) + SUM(TTRX1_JAMM) + SUM(TTRX2_JAMM) ";
        strFindQuery += "FROM DAY_TBS_JAMM ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_ID ";
        strFindQuery += "ORDER BY TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == sqlRdr[0].ToString().Trim())
                {
                    tbs_list.Items[i].SubItems[7].Text = sqlRdr[1].ToString().Trim();

                    if (Convert.ToInt32(tbs_list.Items[i].SubItems[7].Text) >= jammCritical)
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.Red;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[7].Text) >= jammMajor)
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.Orange;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[7].Text) >= jammMinor)
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.Yellow;
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.White;
                    }

                    break;
                }
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT TBS_ID+0 TBS, SUBSTRING(REG_DT, 1, 10), SUM(TTRX0_JAMM), SUM(TTRX1_JAMM), SUM(TTRX2_JAMM) ";
        strFindQuery += "FROM DAY_TBS_JAMM ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_ID, SUBSTRING(REG_DT, 1, 10) ";
        strFindQuery += "ORDER BY TBS, SUBSTRING(REG_DT, 1, 10)";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == sqlRdr[0].ToString().Trim())
                {
                    if (tbs_list.Items[i].SubItems[7].Tag.ToString() == "-")
                    {
                        tbs_list.Items[i].SubItems[7].Tag = sqlRdr[1].ToString().Trim() + " : " + sqlRdr[2].ToString().Trim() + " / " + sqlRdr[3].ToString().Trim() + " / " + sqlRdr[4].ToString().Trim();
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[7].Tag += "\r\n" + sqlRdr[1].ToString().Trim() + " : " + sqlRdr[2].ToString().Trim() + " / " + sqlRdr[3].ToString().Trim() + " / " + sqlRdr[4].ToString().Trim();
                    }

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

    int modemCnt = 0, maxCnt = 0, totCnt = 0, rcCnt = 0, jamCnt = 0;
    for (int cnt = 0; cnt < tbs_list.Items.Count; cnt++)
    {
        if (tbs_list.Items[cnt].SubItems[3].Text != "-") modemCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[3].Text);
        if (tbs_list.Items[cnt].SubItems[4].Text != "-") maxCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[4].Text);
        if (tbs_list.Items[cnt].SubItems[5].Text != "-") totCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[5].Text);
        if (tbs_list.Items[cnt].SubItems[6].Text != "-") rcCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[6].Text);
        if (tbs_list.Items[cnt].SubItems[7].Text != "-") jamCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[7].Text);
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("합계");
    tItem.SubItems.Add(modemCnt.ToString());
    tItem.SubItems.Add(maxCnt.ToString());
    tItem.SubItems.Add(totCnt.ToString());
    tItem.SubItems.Add(rcCnt.ToString());
    tItem.SubItems.Add(jamCnt.ToString());
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    tbs_list.Items.Add(tItem);

    tbs_list.EndUpdate();
}

private void month_group_list_search(string strDate)
{
    group_columnsorter.previousColumn = -1;
    group_flag = 0;
    group_list.ListViewItemSorter = null;

    group_list.Items.Clear();
    group_list.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT GROUP_CODE CODE, GROUP_ID ID, GROUP_NAME, AVG(MODEM_CNT), AVG(SEND_CNT), AVG(RECV_CNT) ";
        strFindQuery += "FROM DAY_GROUP_INFO ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        if (filtOnOff)
        {
            strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
        }
        strFindQuery += "GROUP BY GROUP_CODE, GROUP_ID, GROUP_NAME ";
        strFindQuery += "ORDER BY CODE, ID";

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
            group_list.Items.Add(Item);
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

    int modemCnt = 0, sendCnt = 0, recvCnt = 0;
    for (int cnt = 0; cnt < group_list.Items.Count; cnt++)
    {
        modemCnt += Convert.ToInt32(group_list.Items[cnt].SubItems[4].Text);
        sendCnt += Convert.ToInt32(group_list.Items[cnt].SubItems[5].Text);
        recvCnt += Convert.ToInt32(group_list.Items[cnt].SubItems[6].Text);
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("합계");
    tItem.SubItems.Add(modemCnt.ToString());
    tItem.SubItems.Add(sendCnt.ToString());
    tItem.SubItems.Add(recvCnt.ToString());
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    group_list.Items.Add(tItem);

    group_list.EndUpdate();
}


private void year_modem_list_search(string strDate)
{
    modem_columnsorter.previousColumn = -1;
    modem_flag = 0;
    modem_list.ListViewItemSorter = null;

    modem_list.Items.Clear();
    if (modem_list.Columns.Count > 18)
    {
        for (int cnt = modem_list.Columns.Count; cnt > 18; cnt--)
        {
            modem_list.Columns.RemoveAt(cnt - 1);
        }
    }
    rdbmCriCnt = 0; rdbmMajCnt = 0; rdbmMinCnt = 0;
    diffCriCnt = 0; diffMajCnt = 0; diffMinCnt = 0;
    avrgCriCnt = 0; avrgMajCnt = 0; avrgMinCnt = 0;

    modem_list.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...1");

        strFindQuery = "SELECT TBS_NAME, TBS_ID+0 TBS ";
        strFindQuery += "FROM DAY_TBS_INFO ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_NAME, TBS_ID ";
        strFindQuery += "ORDER BY TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        string buffer = "";
        while (sqlRdr.Read())
        {
            if (buffer != sqlRdr[1].ToString().Trim())
            {
                modem_list.Columns.Add(sqlRdr[0].ToString().Trim());
                modem_list.Columns[modem_list.Columns.Count - 1].Tag = sqlRdr[1].ToString().Trim();
                modem_list.Columns[modem_list.Columns.Count - 1].TextAlign = HorizontalAlignment.Center;
                buffer = sqlRdr[1].ToString().Trim();
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        string[] sItem = new string[modem_list.Columns.Count - 18];
        for (int cnt = 0; cnt < modem_list.Columns.Count - 18; cnt++)
        {
            sItem[cnt] = "-";
        }

        strFindQuery = "SELECT GROUP_CODE, GROUP_NAME, NODE_NAME, NODE_ID, MODEM_ISSI, SUM(SEND_CNT)+SUM(RECV_CNT) CNT ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        if (filtOnOff)
        {
            strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
        }
        strFindQuery += "GROUP BY GROUP_CODE, GROUP_NAME, NODE_NAME, NODE_ID, MODEM_ISSI ";
        strFindQuery += "ORDER BY MODEM_ISSI, CNT DESC";
        Console.WriteLine(strFindQuery);

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        string issi = "";
        while (sqlRdr.Read())
        {
            if (issi != sqlRdr[4].ToString().Trim())
            {
                ListViewItem Item = new ListViewItem("");
                Item.UseItemStyleForSubItems = false;
                Item.SubItems.Add((modem_list.Items.Count + 1).ToString());
                if (sqlRdr[0].ToString().Trim() == "") Item.SubItems.Add("-");
                else Item.SubItems.Add(sqlRdr[0].ToString().Trim());
                if (sqlRdr[1].ToString().Trim() == "") Item.SubItems.Add("-");
                else Item.SubItems.Add(sqlRdr[1].ToString().Trim());
                if (sqlRdr[2].ToString().Trim() == "") Item.SubItems.Add("-");
                else Item.SubItems.Add(sqlRdr[2].ToString().Trim());
                if (sqlRdr[3].ToString().Trim() == "") Item.SubItems.Add("-");
                else Item.SubItems.Add(sqlRdr[3].ToString().Trim());
                if (sqlRdr[4].ToString().Trim() == "") Item.SubItems.Add("-");
                else Item.SubItems.Add(sqlRdr[4].ToString().Trim());
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.AddRange(sItem);

                Item.SubItems[7].BackColor = Color.Gray;
                Item.SubItems[8].BackColor = Color.Gray;
                Item.SubItems[12].BackColor = Color.Gray;

                modem_list.Items.Add(Item);

                issi = sqlRdr[4].ToString().Trim();
            }
            else if (modem_list.Items[modem_list.Items.Count - 1].SubItems[3].Text == "-")
            {
                if (sqlRdr[0].ToString().Trim() == "") modem_list.Items[modem_list.Items.Count - 1].SubItems[2].Text = "-";
                else modem_list.Items[modem_list.Items.Count - 1].SubItems[2].Text = sqlRdr[0].ToString().Trim();
                if (sqlRdr[1].ToString().Trim() == "") modem_list.Items[modem_list.Items.Count - 1].SubItems[3].Text = "-";
                else modem_list.Items[modem_list.Items.Count - 1].SubItems[3].Text = sqlRdr[1].ToString().Trim();
                if (sqlRdr[2].ToString().Trim() == "") modem_list.Items[modem_list.Items.Count - 1].SubItems[4].Text = "-";
                else modem_list.Items[modem_list.Items.Count - 1].SubItems[4].Text = sqlRdr[2].ToString().Trim();
                if (sqlRdr[3].ToString().Trim() == "") modem_list.Items[modem_list.Items.Count - 1].SubItems[5].Text = "-";
                else modem_list.Items[modem_list.Items.Count - 1].SubItems[5].Text = sqlRdr[3].ToString().Trim();
                if (sqlRdr[4].ToString().Trim() == "") modem_list.Items[modem_list.Items.Count - 1].SubItems[6].Text = "-";
                else modem_list.Items[modem_list.Items.Count - 1].SubItems[6].Text = sqlRdr[4].ToString().Trim();

                issi = sqlRdr[4].ToString().Trim();
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT MODEM_ISSI, AVG(RDBM_AVRG), MIN(RDBM_MIN)-MAX(RDBM_MAX), ";
        strFindQuery += "MAX(RDBM_MAX), MIN(RDBM_MIN), SUM(SEND_CNT), ROUND(AVG(SEND_AVRG), 2), SUM(SEND_SUCC), SUM(SEND_FAIL), SUM(RECV_CNT) ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        if (filtOnOff)
        {
            strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
        }
        strFindQuery += "AND SUCC_EXCEPT IS NULL ";
        strFindQuery += "GROUP BY MODEM_ISSI ";
        strFindQuery += "ORDER BY MODEM_ISSI";
        Console.WriteLine(strFindQuery);

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem fItem = null;
            if (modem_list.Items.Count > 0)
            {
                fItem = modem_list.FindItemWithText(sqlRdr[0].ToString().Trim(), true, 0);
            }

            if (fItem != null)
            {
                if (sqlRdr[1].ToString().Trim() == "") fItem.SubItems[7].Text = "-";
                else fItem.SubItems[7].Text = sqlRdr[1].ToString().Trim();
                if (sqlRdr[2].ToString().Trim() == "") fItem.SubItems[8].Text = "-";
                else fItem.SubItems[8].Text = sqlRdr[2].ToString().Trim();
                if (sqlRdr[3].ToString().Trim() == "") fItem.SubItems[9].Text = "-";
                else fItem.SubItems[9].Text = sqlRdr[3].ToString().Trim();
                if (sqlRdr[4].ToString().Trim() == "") fItem.SubItems[10].Text = "-";
                else fItem.SubItems[10].Text = sqlRdr[4].ToString().Trim();
                if (sqlRdr[5].ToString().Trim() == "") fItem.SubItems[11].Text = "-";
                else fItem.SubItems[11].Text = sqlRdr[5].ToString().Trim();
                if (sqlRdr[6].ToString().Trim() == "") fItem.SubItems[12].Text = "-";
                //else fItem.SubItems[12].Text = sqlRdr[6].ToString().Trim();
                else fItem.SubItems[12].Text = (Convert.ToInt32(sqlRdr[7].ToString().Trim()) * 100.00d / Convert.ToInt32(sqlRdr[5].ToString().Trim())).ToString("N2");
                if (sqlRdr[7].ToString().Trim() == "") fItem.SubItems[13].Text = "-";
                else fItem.SubItems[13].Text = sqlRdr[7].ToString().Trim();
                if (sqlRdr[8].ToString().Trim() == "") fItem.SubItems[14].Text = "-";
                else fItem.SubItems[14].Text = sqlRdr[8].ToString().Trim();
                if (sqlRdr[9].ToString().Trim() == "") fItem.SubItems[15].Text = "-";
                else fItem.SubItems[15].Text = sqlRdr[9].ToString().Trim();

                if (fItem.SubItems[7].Text == "-")
                {
                    fItem.SubItems[7].BackColor = Color.Gray;
                }
                else if (Convert.ToInt32(fItem.SubItems[7].Text) < rdbmCritical)
                {
                    fItem.SubItems[7].BackColor = Color.Red;
                    rdbmCriCnt++;
                }
                else if (Convert.ToInt32(fItem.SubItems[7].Text) < rdbmMajor)
                {
                    fItem.SubItems[7].BackColor = Color.Orange;
                    rdbmMajCnt++;
                }
                else if (Convert.ToInt32(fItem.SubItems[7].Text) < rdbmMinor)
                {
                    fItem.SubItems[7].BackColor = Color.Yellow;
                    rdbmMinCnt++;
                }
                else
                {
                    fItem.SubItems[7].BackColor = Color.White;
                }

                if (fItem.SubItems[8].Text == "-")
                {
                    fItem.SubItems[8].BackColor = Color.Gray;
                }
                else if (Convert.ToInt32(fItem.SubItems[8].Text) < pdbmCritical)
                {
                    fItem.SubItems[8].BackColor = Color.Red;
                    diffCriCnt++;
                }
                else if (Convert.ToInt32(fItem.SubItems[8].Text) < pdbmMajor)
                {
                    fItem.SubItems[8].BackColor = Color.Orange;
                    diffMajCnt++;
                }
                else if (Convert.ToInt32(fItem.SubItems[8].Text) < pdbmMinor)
                {
                    fItem.SubItems[8].BackColor = Color.Yellow;
                    diffMinCnt++;
                }
                else
                {
                    fItem.SubItems[8].BackColor = Color.White;
                }

                if (fItem.SubItems[12].Text == "-")
                {
                    fItem.SubItems[12].BackColor = Color.Gray;
                }
                else if (Convert.ToDouble(fItem.SubItems[12].Text) < Convert.ToDouble(avrgCritical))
                {
                    fItem.SubItems[12].BackColor = Color.Red;
                    avrgCriCnt++;
                }
                else if (Convert.ToDouble(fItem.SubItems[12].Text) < Convert.ToDouble(avrgMajor))
                {
                    fItem.SubItems[12].BackColor = Color.Orange;
                    avrgMajCnt++;
                }
                else if (Convert.ToDouble(fItem.SubItems[12].Text) < Convert.ToDouble(avrgMinor))
                {
                    fItem.SubItems[12].BackColor = Color.Yellow;
                    avrgMinCnt++;
                }
                else
                {
                    fItem.SubItems[12].BackColor = Color.White;
                }
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT MODEM_ISSI, TBS_ID+0 TBS, SUM(RECV_CNT) ";
        strFindQuery += "FROM DAY_MODEM_RECV ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY MODEM_ISSI, TBS_ID ";
        strFindQuery += "ORDER BY MODEM_ISSI, TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem fItem = null;
            if (modem_list.Items.Count > 0)
            {
                fItem = modem_list.FindItemWithText(sqlRdr[0].ToString().Trim(), true, 0);
            }

            if (fItem != null)
            {
                int max = 0, col = 0, ho = 0;
                for (int cnt = 18; cnt < modem_list.Columns.Count; cnt++)
                {
                    if (modem_list.Columns[cnt].Tag.ToString() == sqlRdr[1].ToString().Trim())
                    {
                        fItem.SubItems[cnt].Text = sqlRdr[2].ToString().Trim();
                    }
                    else if (fItem.SubItems[cnt].Text == "-")
                    {
                        fItem.SubItems[cnt].Text = "0";
                    }

                    if (Convert.ToInt32(fItem.SubItems[cnt].Text) > max)
                    {
                        max = Convert.ToInt32(fItem.SubItems[cnt].Text);
                        col = cnt;
                    }

                    if (Convert.ToInt32(fItem.SubItems[cnt].Text) > 0)
                    {
                        ho++;
                    }
                }
                if (col > 0) fItem.SubItems[16].Text = modem_list.Columns[col].Text;
                if (ho > 0) fItem.SubItems[17].Text = ho.ToString();
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
            Console.WriteLine("데이터베이스 연결 해제...1");
        }
    }

    int avgdbmCnt = 0, avgdbmVal = 0;
    int difdbmCnt = 0, difdbmVal = 0;
    int maxdbmCnt = 0, maxdbmVal = 0;
    int mindbmCnt = 0, mindbmVal = 0;
    int sendCnt = 0, sendVal = 0;
    int avgsendCnt = 0; double avgsendVal = 0.00d;
    int succsendCnt = 0, succsendVal = 0;
    int failsendCnt = 0, failsendVal = 0;
    int recvCnt = 0, recvVal = 0;
    int[] tbsCnt = new int[modem_list.Columns.Count - 16];
    int[] tbsVal = new int[modem_list.Columns.Count - 16];

    for (int cnt = 0; cnt < modem_list.Items.Count; cnt++)
    {
        if (modem_list.Items[cnt].SubItems[7].Text != "-") { avgdbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[7].Text); avgdbmCnt++; }
        if (modem_list.Items[cnt].SubItems[8].Text != "-") { difdbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[8].Text); difdbmCnt++; }
        if (modem_list.Items[cnt].SubItems[9].Text != "-") { maxdbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[9].Text); maxdbmCnt++; }
        if (modem_list.Items[cnt].SubItems[10].Text != "-") { mindbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[10].Text); mindbmCnt++; }
        if (modem_list.Items[cnt].SubItems[11].Text != "-") { sendVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[11].Text); sendCnt++; }
        if (modem_list.Items[cnt].SubItems[12].Text != "-") { avgsendVal += Convert.ToDouble(modem_list.Items[cnt].SubItems[12].Text); avgsendCnt++; }
        if (modem_list.Items[cnt].SubItems[13].Text != "-") { succsendVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[13].Text); succsendCnt++; }
        if (modem_list.Items[cnt].SubItems[14].Text != "-") { failsendVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[14].Text); failsendCnt++; }
        if (modem_list.Items[cnt].SubItems[15].Text != "-") { recvVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[15].Text); recvCnt++; }
        for (int idx = 18; idx < modem_list.Columns.Count; idx++)
        {
            if (modem_list.Items[cnt].SubItems[idx].Text != "-") { tbsVal[idx - 18] += Convert.ToInt32(modem_list.Items[cnt].SubItems[idx].Text); tbsCnt[idx - 18]++; }
        }
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("합계 / 평균");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    if (avgdbmCnt != 0) tItem.SubItems.Add((avgdbmVal / avgdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (difdbmCnt != 0) tItem.SubItems.Add((difdbmVal / difdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (maxdbmCnt != 0) tItem.SubItems.Add((maxdbmVal / maxdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (mindbmCnt != 0) tItem.SubItems.Add((mindbmVal / mindbmCnt).ToString());
    else tItem.SubItems.Add("0");
    tItem.SubItems.Add(sendVal.ToString());
    if (avgsendCnt != 0) tItem.SubItems.Add((succsendVal * 100.00d / sendVal).ToString("N2"));
    else tItem.SubItems.Add("0.00");
    tItem.SubItems.Add(succsendVal.ToString());
    tItem.SubItems.Add(failsendVal.ToString());
    tItem.SubItems.Add(recvVal.ToString());
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    for (int idx = 18; idx < modem_list.Columns.Count; idx++)
    {
        tItem.SubItems.Add(tbsVal[idx - 18].ToString());
    }
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    modem_list.Items.Add(tItem);

    if (item_comboBox.SelectedIndex == 2)
    {
        if (modem_list.Items.Count == 0)
        {
            modem_total.Text = modem_list.Items.Count.ToString();
        }
        else
        {
            modem_total.Text = (modem_list.Items.Count - 1).ToString();
        }

        modem_critical.Text = avrgCriCnt.ToString();
        modem_major.Text = avrgMajCnt.ToString();
        modem_minor.Text = avrgMinCnt.ToString();
    }
    else
    {
        item_comboBox.SelectedIndex = 2;
    }

    modem_list.EndUpdate();
}

private void year_tbs_list_search(string strDate)
{
    tbs_columnsorter.previousColumn = -1;
    tbs_flag = 0;
    tbs_list.ListViewItemSorter = null;

    tbs_list.Items.Clear();
    tbs_list.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT TBS_ID+0 TBS, TBS_NAME, AVG(MODEM_CNT), MAX(MODEM_MAX), AVG(MODEM_TOT) ";
        strFindQuery += "FROM DAY_TBS_INFO ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_ID, TBS_NAME ";
        strFindQuery += "ORDER BY TBS";

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
            Item.SubItems.Add("-");
            Item.SubItems[6].BackColor = Color.Gray;
            Item.SubItems[6].Tag = "-";
            Item.SubItems.Add("0");
            Item.SubItems[7].Tag = "-";
            tbs_list.Items.Add(Item);
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT TBS_ID+0 TBS, SUM(TBS_COLL) ";
        strFindQuery += "FROM DAY_TBS_COLL ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_ID ";
        strFindQuery += "ORDER BY TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == sqlRdr[0].ToString().Trim())
                {
                    tbs_list.Items[i].SubItems[6].Text = sqlRdr[1].ToString().Trim();

                    if (Convert.ToInt32(tbs_list.Items[i].SubItems[6].Text) >= collCritical)
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.Red;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[6].Text) >= collMajor)
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.Orange;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[6].Text) >= collMinor)
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.Yellow;
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.White;
                    }

                    break;
                }
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT TBS_ID+0 TBS, SUBSTRING(REG_DT, 1, 7), SUM(TBS_COLL) ";
        strFindQuery += "FROM DAY_TBS_COLL ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_ID, SUBSTRING(REG_DT, 1, 7) ";
        strFindQuery += "ORDER BY TBS, SUBSTRING(REG_DT, 1, 7)";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == sqlRdr[0].ToString().Trim())
                {
                    if (tbs_list.Items[i].SubItems[6].Tag.ToString() == "-")
                    {
                        tbs_list.Items[i].SubItems[6].Tag = sqlRdr[1].ToString().Trim() + " : " + sqlRdr[2].ToString().Trim();
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[6].Tag += "\r\n" + sqlRdr[1].ToString().Trim() + " : " + sqlRdr[2].ToString().Trim();
                    }

                    break;
                }
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT TBS_ID+0 TBS, SUM(TTRX0_JAMM) + SUM(TTRX1_JAMM) + SUM(TTRX2_JAMM) ";
        strFindQuery += "FROM DAY_TBS_JAMM ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_ID ";
        strFindQuery += "ORDER BY TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == sqlRdr[0].ToString().Trim())
                {
                    tbs_list.Items[i].SubItems[7].Text = sqlRdr[1].ToString().Trim();

                    if (Convert.ToInt32(tbs_list.Items[i].SubItems[7].Text) >= jammCritical)
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.Red;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[7].Text) >= jammMajor)
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.Orange;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[7].Text) >= jammMinor)
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.Yellow;
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.White;
                    }

                    break;
                }
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT TBS_ID+0 TBS, SUBSTRING(REG_DT, 1, 7), SUM(TTRX0_JAMM), SUM(TTRX1_JAMM), SUM(TTRX2_JAMM) ";
        strFindQuery += "FROM DAY_TBS_JAMM ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY TBS_ID, SUBSTRING(REG_DT, 1, 7) ";
        strFindQuery += "ORDER BY TBS, SUBSTRING(REG_DT, 1, 7)";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == sqlRdr[0].ToString().Trim())
                {
                    if (tbs_list.Items[i].SubItems[7].Tag.ToString() == "-")
                    {
                        tbs_list.Items[i].SubItems[7].Tag = sqlRdr[1].ToString().Trim() + " : " + sqlRdr[2].ToString().Trim() + " / " + sqlRdr[3].ToString().Trim() + " / " + sqlRdr[4].ToString().Trim();
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[7].Tag += "\r\n" + sqlRdr[1].ToString().Trim() + " : " + sqlRdr[2].ToString().Trim() + " / " + sqlRdr[3].ToString().Trim() + " / " + sqlRdr[4].ToString().Trim();
                    }

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

    int modemCnt = 0, maxCnt = 0, totCnt = 0, rcCnt = 0, jamCnt = 0;
    for (int cnt = 0; cnt < tbs_list.Items.Count; cnt++)
    {
        if (tbs_list.Items[cnt].SubItems[3].Text != "-") modemCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[3].Text);
        if (tbs_list.Items[cnt].SubItems[4].Text != "-") maxCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[4].Text);
        if (tbs_list.Items[cnt].SubItems[5].Text != "-") totCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[5].Text);
        if (tbs_list.Items[cnt].SubItems[6].Text != "-") rcCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[6].Text);
        if (tbs_list.Items[cnt].SubItems[7].Text != "-") jamCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[7].Text);
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("합계");
    tItem.SubItems.Add(modemCnt.ToString());
    tItem.SubItems.Add(maxCnt.ToString());
    tItem.SubItems.Add(totCnt.ToString());
    tItem.SubItems.Add(rcCnt.ToString());
    tItem.SubItems.Add(jamCnt.ToString());
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    tbs_list.Items.Add(tItem);

    tbs_list.EndUpdate();
}

private void year_group_list_search(string strDate)
{
    group_columnsorter.previousColumn = -1;
    group_flag = 0;
    group_list.ListViewItemSorter = null;

    group_list.Items.Clear();
    group_list.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT GROUP_CODE CODE, GROUP_ID ID, GROUP_NAME, AVG(MODEM_CNT), AVG(SEND_CNT), AVG(RECV_CNT)";
        strFindQuery += "FROM DAY_GROUP_INFO ";
        strFindQuery += "WHERE REG_DT LIKE '" + strDate + "%' ";
        if (filtOnOff)
        {
            strFindQuery += " AND (GROUP_CODE = 'BJ' OR GROUP_CODE = 'TD') ";
        }
        strFindQuery += "GROUP BY GROUP_CODE, GROUP_ID, GROUP_NAME ";
        strFindQuery += "ORDER BY CODE, ID";

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
            group_list.Items.Add(Item);
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

    int modemCnt = 0, sendCnt = 0, recvCnt = 0;
    for (int cnt = 0; cnt < group_list.Items.Count; cnt++)
    {
        modemCnt += Convert.ToInt32(group_list.Items[cnt].SubItems[4].Text);
        sendCnt += Convert.ToInt32(group_list.Items[cnt].SubItems[5].Text);
        recvCnt += Convert.ToInt32(group_list.Items[cnt].SubItems[6].Text);
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("합계");
    tItem.SubItems.Add(modemCnt.ToString());
    tItem.SubItems.Add(sendCnt.ToString());
    tItem.SubItems.Add(recvCnt.ToString());
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    group_list.Items.Add(tItem);

    group_list.EndUpdate();
}


private void modem_list_search(string strDate)
{
    modem_columnsorter.previousColumn = -1;
    modem_flag = 0;
    modem_list.ListViewItemSorter = null;

    modem_list.Items.Clear();
    if (modem_list.Columns.Count > 18)
    {
        for (int cnt = modem_list.Columns.Count; cnt > 18; cnt--)
        {
            modem_list.Columns.RemoveAt(cnt - 1);
        }
    }
    rdbmCriCnt = 0; rdbmMajCnt = 0; rdbmMinCnt = 0;
    diffCriCnt = 0; diffMajCnt = 0; diffMinCnt = 0;
    avrgCriCnt = 0; avrgMajCnt = 0; avrgMinCnt = 0;

    modem_list.BeginUpdate();

    string queryString = "";
    OracleCommand command = null;
    OracleDataReader read = null;
    OracleConnection conn = new OracleConnection(nmsConnStr);

    try
    {
        conn.Open();
        Console.WriteLine("데이터베이스 연결 성공...1");

        queryString = "SELECT TBS_NM, REPLACE(TBS_ID, 'TBS-', '')+0 TBS_ORDER ";
        queryString += "FROM T_CM_TBS_MNG ";
        queryString += "ORDER BY TBS_ORDER";

        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        while (read.Read())
        {
            modem_list.Columns.Add(read[0].ToString().Trim());
            modem_list.Columns[modem_list.Columns.Count - 1].Tag = read[1].ToString().Trim();
            modem_list.Columns[modem_list.Columns.Count - 1].TextAlign = HorizontalAlignment.Center;
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
    finally
    {
        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }

        if (conn != null)
        {
            conn.Close(); conn = null;
            Console.WriteLine("데이터베이스 연결 해제...1");
        }
    }

    string[] sItem = new string[modem_list.Columns.Count - 18];
    for (int cnt = 0; cnt < modem_list.Columns.Count - 18; cnt++)
    {
        sItem[cnt] = "-";
    }

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(itConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...2");

        strFindQuery = "SELECT mo.system_code, gr.group_name, mo.modem_name, mo.frtu_addr, mo.modem_issi ";
        strFindQuery += "FROM modem_info AS mo CROSS JOIN ";
        strFindQuery += "group_info AS gr ";
        strFindQuery += "WHERE mo.use_yn = 'Y' AND ";
        strFindQuery += "mo.group_id = gr.group_id ";
        strFindQuery += "ORDER BY mo.modem_issi";

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
            Item.SubItems.Add(sqlRdr[4].ToString().Trim());
            Item.SubItems.Add("-");
            Item.SubItems.Add("-");
            Item.SubItems.Add("-");
            Item.SubItems.Add("-");
            Item.SubItems.Add("-");
            Item.SubItems.Add("-");
            Item.SubItems.Add("-");
            Item.SubItems.Add("-");
            Item.SubItems.Add("-");
            Item.SubItems.Add("-");
            Item.SubItems.Add("-");
            Item.SubItems.AddRange(sItem);

            Item.SubItems[7].BackColor = Color.Gray;
            Item.SubItems[8].BackColor = Color.Gray;
            Item.SubItems[12].BackColor = Color.Gray;

            modem_list.Items.Add(Item);
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT modem_issi, ";
        strFindQuery += "AVG(CONVERT(int, recv_sens)), ";
        strFindQuery += "MIN(CONVERT(int, recv_sens)) - MAX(CONVERT(int, recv_sens)), ";
        strFindQuery += "MAX(CONVERT(int, recv_sens)), ";
        strFindQuery += "MIN(CONVERT(int, recv_sens)) ";
        strFindQuery += "FROM modem_sens_his ";
        strFindQuery += "WHERE recv_dtime LIKE '" + strDate + "%' ";
        strFindQuery += "GROUP BY modem_issi, SUBSTRING(recv_dtime, 1, 10) ";
        strFindQuery += "ORDER BY modem_issi";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem fItem = null;
            if (modem_list.Items.Count > 0)
            {
                fItem = modem_list.FindItemWithText(sqlRdr[0].ToString().Trim(), true, 0);
            }

            if (fItem != null)
            {
                fItem.SubItems[7].Text = sqlRdr[1].ToString().Trim();
                fItem.SubItems[8].Text = sqlRdr[2].ToString().Trim();
                fItem.SubItems[9].Text = sqlRdr[3].ToString().Trim();
                fItem.SubItems[10].Text = sqlRdr[4].ToString().Trim();

                if (Convert.ToInt32(fItem.SubItems[7].Text) < rdbmCritical)
                {
                    fItem.SubItems[7].BackColor = Color.Red;
                    rdbmCriCnt++;
                }
                else if (Convert.ToInt32(fItem.SubItems[7].Text) < rdbmMajor)
                {
                    fItem.SubItems[7].BackColor = Color.Orange;
                    rdbmMajCnt++;
                }
                else if (Convert.ToInt32(fItem.SubItems[7].Text) < rdbmMinor)
                {
                    fItem.SubItems[7].BackColor = Color.Yellow;
                    rdbmMinCnt++;
                }
                else
                {
                    fItem.SubItems[7].BackColor = Color.White;
                }

                if (Convert.ToInt32(fItem.SubItems[8].Text) < pdbmCritical)
                {
                    fItem.SubItems[8].BackColor = Color.Red;
                    diffCriCnt++;
                }
                else if (Convert.ToInt32(fItem.SubItems[8].Text) < pdbmMajor)
                {
                    fItem.SubItems[8].BackColor = Color.Orange;
                    diffMajCnt++;
                }
                else if (Convert.ToInt32(fItem.SubItems[8].Text) < pdbmMinor)
                {
                    fItem.SubItems[8].BackColor = Color.Yellow;
                    diffMinCnt++;
                }
                else
                {
                    fItem.SubItems[8].BackColor = Color.White;
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
            Console.WriteLine("데이터베이스 연결 해제...2");
        }
    }

    queryString = "";
    command = null;
    read = null;
    conn = new OracleConnection(nmsConnStr);

    try
    {
        conn.Open();
        Console.WriteLine("데이터베이스 연결 성공...3");
        
        queryString = "SELECT CALLED_SSI_PART ISSI, COUNT(*) TOTAL, ";
        queryString += "ROUND(SUM(DECODE(TRIM(SDS_RESULT), 'OK 개별 전달', 1,0))/COUNT(*)*100, 2) AVR, ";
        queryString += "SUM(DECODE(TRIM(SDS_RESULT), 'OK 개별 전달', 1,0)) SUCC, ";
        queryString += "SUM(DECODE(TRIM(SDS_RESULT), 'OK 개별 전달', 0,1)) FAIL ";
        queryString += "FROM T_PM_SDSTO_HIS ";
        queryString += "WHERE REG_DT LIKE '" + strDate + "%' AND (SERVED_SSI_PART = '" + siteISSI1 + "' OR SERVED_SSI_PART = '" + siteISSI2 + "' OR SERVED_SSI_PART = '" + siteISSI3 + "') ";
        queryString += "GROUP BY CALLED_SSI_PART ";
        queryString += "ORDER BY ISSI";
        /*
        queryString = "SELECT OTHER_SSI_PART ISSI, COUNT(*) TOTAL, ";
        queryString += "ROUND(SUM(DECODE(TRIM(SDS_RESULT), 'OK 개별 전달', 1,0))/COUNT(*)*100, 2) AVR, ";
        queryString += "SUM(DECODE(TRIM(SDS_RESULT), 'OK 개별 전달', 1,0)) SUCC, ";
        queryString += "SUM(DECODE(TRIM(SDS_RESULT), 'OK 개별 전달', 0,1)) FAIL ";
        queryString += "FROM T_PM_CONTROL_CHA_HIS ";
        queryString += "WHERE REG_DT LIKE '" + strDate + "%' AND (SSI_PART = '" + siteISSI1 + "' OR SSI_PART = '" + siteISSI2 + "' OR SSI_PART = '" + siteISSI3 + "') ";
        queryString += "GROUP BY OTHER_SSI_PART ";
        queryString += "ORDER BY ISSI";
        */
        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        while (read.Read())
        {
            ListViewItem fItem = null;
            if (modem_list.Items.Count > 0)
            {
                fItem = modem_list.FindItemWithText(read[0].ToString().Trim(), true, 0);
            }

            if (fItem != null)
            {
                fItem.SubItems[11].Text = read[1].ToString().Trim();
                fItem.SubItems[12].Text = read[2].ToString().Trim();
                fItem.SubItems[13].Text = read[3].ToString().Trim();
                fItem.SubItems[14].Text = read[4].ToString().Trim();

                if (Convert.ToDouble(fItem.SubItems[12].Text) < Convert.ToDouble(avrgCritical))
                {
                    fItem.SubItems[12].BackColor = Color.Red;
                    avrgCriCnt++;
                }
                else if (Convert.ToDouble(fItem.SubItems[12].Text) < Convert.ToDouble(avrgMajor))
                {
                    fItem.SubItems[12].BackColor = Color.Orange;
                    avrgMajCnt++;
                }
                else if (Convert.ToDouble(fItem.SubItems[12].Text) < Convert.ToDouble(avrgMinor))
                {
                    fItem.SubItems[12].BackColor = Color.Yellow;
                    avrgMinCnt++;
                }
                else
                {
                    fItem.SubItems[12].BackColor = Color.White;
                }
            }
            else
            {
                ListViewItem Item = new ListViewItem("");
                Item.UseItemStyleForSubItems = false;
                Item.SubItems.Add((modem_list.Items.Count + 1).ToString());
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add(read[0].ToString().Trim());
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add(read[1].ToString().Trim());
                Item.SubItems.Add(read[2].ToString().Trim());
                Item.SubItems.Add(read[3].ToString().Trim());
                Item.SubItems.Add(read[4].ToString().Trim());
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.AddRange(sItem);

                Item.SubItems[7].BackColor = Color.Gray;
                Item.SubItems[8].BackColor = Color.Gray;

                if (Convert.ToDouble(Item.SubItems[12].Text) < Convert.ToDouble(avrgCritical))
                {
                    Item.SubItems[12].BackColor = Color.Red;
                    avrgCriCnt++;
                }
                else if (Convert.ToDouble(Item.SubItems[12].Text) < Convert.ToDouble(avrgMajor))
                {
                    Item.SubItems[12].BackColor = Color.Orange;
                    avrgMajCnt++;
                }
                else if (Convert.ToDouble(Item.SubItems[12].Text) < Convert.ToDouble(avrgMinor))
                {
                    Item.SubItems[12].BackColor = Color.Yellow;
                    avrgMinCnt++;
                }
                else
                {
                    Item.SubItems[12].BackColor = Color.White;
                }

                modem_list.Items.Add(Item);
            }
        }
        
        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }
        
        queryString = "SELECT SERVED_SSI_PART ISSI, COUNT(*) TOTAL, ";
        for (int cnt = 18; cnt < modem_list.Columns.Count; cnt++)
        {
            queryString += "SUM(DECODE(SUBSTR(TRIM(LOCATION_AREA), 2, 2)+0, " + modem_list.Columns[cnt].Tag.ToString() + ", 1, 0)) TBS" + modem_list.Columns[cnt].Tag.ToString();
            if (cnt != modem_list.Columns.Count - 1) queryString += ", ";
            else queryString += " ";
        }
        queryString += "FROM T_PM_SDSTO_HIS ";
        queryString += "WHERE REG_DT LIKE '" + strDate + "%' AND (CALLED_SSI_PART = '" + siteISSI1 + "' OR CALLED_SSI_PART = '" + siteISSI2 + "'OR CALLED_SSI_PART = '" + siteISSI3 + "') AND LOCATION_AREA != 'SERVER' ";
        queryString += "GROUP BY SERVED_SSI_PART ";
        queryString += "ORDER BY ISSI";
        /*
        queryString = "SELECT SSI_PART ISSI, COUNT(*) TOTAL, ";
        for (int cnt = 18; cnt < modem_list.Columns.Count; cnt++)
        {
            queryString += "SUM(DECODE(SUBSTR(TRIM(LOCATION_AREA), 2, 2)+0, " + modem_list.Columns[cnt].Tag.ToString() + ", 1, 0)) TBS" + modem_list.Columns[cnt].Tag.ToString();
            if (cnt != modem_list.Columns.Count - 1) queryString += ", ";
            else queryString += " ";
        }
        queryString += "FROM T_PM_CONTROL_CHA_HIS ";
        queryString += "WHERE REG_DT LIKE '" + strDate + "%' AND (OTHER_SSI_PART = '" + siteISSI1 + "' OR OTHER_SSI_PART = '" + siteISSI2 + "'OR OTHER_SSI_PART = '" + siteISSI3 + "') AND LOCATION_AREA != 'SERVER' ";
        queryString += "GROUP BY SSI_PART ";
        queryString += "ORDER BY ISSI";
        */
        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        while (read.Read())
        {
            ListViewItem fItem = null;
            if (modem_list.Items.Count > 0)
            {
                fItem = modem_list.FindItemWithText(read[0].ToString().Trim(), true, 0);
            }

            int max = 0, col = 0, ho = 0;
            if (fItem != null)
            {
                fItem.SubItems[15].Text = read[1].ToString().Trim();
                for (int cnt = 18; cnt < modem_list.Columns.Count; cnt++)
                {
                    fItem.SubItems[cnt].Text = read[cnt - 16].ToString().Trim();
                    if (Convert.ToInt32(read[cnt - 16]) > max)
                    {
                        max = Convert.ToInt32(read[cnt - 16]);
                        col = cnt;
                    }

                    if (Convert.ToInt32(read[cnt - 16]) > 0)
                    {
                        ho++;
                    }
                }
                if (col > 0) fItem.SubItems[16].Text = modem_list.Columns[col].Text;
                if (ho > 0) fItem.SubItems[17].Text = ho.ToString();
            }
            else
            {
                ListViewItem Item = new ListViewItem("");
                Item.UseItemStyleForSubItems = false;
                Item.SubItems.Add((modem_list.Items.Count + 1).ToString());
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add(read[0].ToString().Trim());
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                Item.SubItems.Add(read[1].ToString().Trim());
                Item.SubItems.Add("-");
                Item.SubItems.Add("-");
                for (int cnt = 18; cnt < modem_list.Columns.Count; cnt++)
                {
                    Item.SubItems.Add(read[cnt - 16].ToString().Trim());
                    if (Convert.ToInt32(read[cnt - 16]) > max)
                    {
                        max = Convert.ToInt32(read[cnt - 16]);
                        col = cnt;
                    }

                    if (Convert.ToInt32(read[cnt - 16]) > 0)
                    {
                        ho++;
                    }
                }
                if (col > 0) Item.SubItems[16].Text = modem_list.Columns[col].Text;
                if (ho > 0) Item.SubItems[17].Text = ho.ToString();

                Item.SubItems[7].BackColor = Color.Gray;
                Item.SubItems[8].BackColor = Color.Gray;
                Item.SubItems[12].BackColor = Color.Gray;

                modem_list.Items.Add(Item);
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
    finally
    {
        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }

        if (conn != null)
        {
            conn.Close(); conn = null;
            Console.WriteLine("데이터베이스 연결 해제...3");
        }
    }

    int avgdbmCnt = 0, avgdbmVal = 0;
    int difdbmCnt = 0, difdbmVal = 0;
    int maxdbmCnt = 0, maxdbmVal = 0;
    int mindbmCnt = 0, mindbmVal = 0;
    int sendCnt = 0, sendVal = 0;
    int avgsendCnt = 0; double avgsendVal = 0.00d;
    int succsendCnt = 0, succsendVal = 0;
    int failsendCnt = 0, failsendVal = 0;
    int recvCnt = 0, recvVal = 0;
    int[] tbsCnt = new int[modem_list.Columns.Count - 18];
    int[] tbsVal = new int[modem_list.Columns.Count - 18];

    for (int cnt = 0; cnt < modem_list.Items.Count; cnt++)
    {
        if (modem_list.Items[cnt].SubItems[7].Text != "-") { avgdbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[7].Text); avgdbmCnt++; }
        if (modem_list.Items[cnt].SubItems[8].Text != "-") { difdbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[8].Text); difdbmCnt++; }
        if (modem_list.Items[cnt].SubItems[9].Text != "-") { maxdbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[9].Text); maxdbmCnt++; }
        if (modem_list.Items[cnt].SubItems[10].Text != "-") { mindbmVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[10].Text); mindbmCnt++; }
        if (modem_list.Items[cnt].SubItems[11].Text != "-") { sendVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[11].Text); sendCnt++; }
        if (modem_list.Items[cnt].SubItems[12].Text != "-") { avgsendVal += Convert.ToDouble(modem_list.Items[cnt].SubItems[12].Text); avgsendCnt++; }
        if (modem_list.Items[cnt].SubItems[13].Text != "-") { succsendVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[13].Text); succsendCnt++; }
        if (modem_list.Items[cnt].SubItems[14].Text != "-") { failsendVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[14].Text); failsendCnt++; }
        if (modem_list.Items[cnt].SubItems[15].Text != "-") { recvVal += Convert.ToInt32(modem_list.Items[cnt].SubItems[15].Text); recvCnt++; }
        for (int idx = 18; idx < modem_list.Columns.Count; idx++)
        {
            if (modem_list.Items[cnt].SubItems[idx].Text != "-") { tbsVal[idx - 18] += Convert.ToInt32(modem_list.Items[cnt].SubItems[idx].Text); tbsCnt[idx - 18]++; }
        }
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("합계 / 평균");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    if (avgdbmCnt != 0) tItem.SubItems.Add((avgdbmVal / avgdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (difdbmCnt != 0) tItem.SubItems.Add((difdbmVal / difdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (maxdbmCnt != 0) tItem.SubItems.Add((maxdbmVal / maxdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (mindbmCnt != 0) tItem.SubItems.Add((mindbmVal / mindbmCnt).ToString());
    else tItem.SubItems.Add("0");
    tItem.SubItems.Add(sendVal.ToString());
    if (avgsendCnt != 0) tItem.SubItems.Add((succsendVal * 100.00d / sendVal).ToString("N2"));
    else tItem.SubItems.Add("0.00");
    tItem.SubItems.Add(succsendVal.ToString());
    tItem.SubItems.Add(failsendVal.ToString());
    tItem.SubItems.Add(recvVal.ToString());
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    for (int idx = 18; idx < modem_list.Columns.Count; idx++)
    {
        tItem.SubItems.Add(tbsVal[idx - 18].ToString());
    }
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    modem_list.Items.Add(tItem);

    if (item_comboBox.SelectedIndex == 2)
    {
        if (modem_list.Items.Count == 0)
        {
            modem_total.Text = modem_list.Items.Count.ToString();
        }
        else
        {
            modem_total.Text = (modem_list.Items.Count - 1).ToString();
        }

        modem_critical.Text = avrgCriCnt.ToString();
        modem_major.Text = avrgMajCnt.ToString();
        modem_minor.Text = avrgMinCnt.ToString();
    }
    else
    {
        item_comboBox.SelectedIndex = 2;
    }

    modem_list.EndUpdate();
}

private void tbs_list_search(string strDate)
{
    tbs_columnsorter.previousColumn = -1;
    tbs_flag = 0;
    tbs_list.ListViewItemSorter = null;

    tbs_list.Items.Clear();
    tbs_list.BeginUpdate();

    string queryString = "";
    OracleCommand command = null;
    OracleDataReader read = null;
    OracleConnection conn = new OracleConnection(nmsConnStr);

    try
    {
        conn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        queryString = "SELECT REPLACE(TBS_ID, 'TBS-', '')+0 TBS_ORDER, TBS_NM ";
        queryString += "FROM T_CM_TBS_MNG ";
        queryString += "ORDER BY TBS_ORDER";

        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        while (read.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            Item.SubItems.Add(read[0].ToString().Trim());
            Item.SubItems.Add(read[1].ToString().Trim());
            int modCnt = 0;
            for (int cnt = 0; cnt < modem_list.Items.Count; cnt++)
            {
                if (modem_list.Items[cnt].SubItems[16].Text == read[1].ToString().Trim()) modCnt++;
            }
            Item.SubItems.Add(modCnt.ToString());
            int modMax = 0;
            for (int idx = 18; idx < modem_list.Columns.Count; idx++)
            {
                if (modem_list.Columns[idx].Text == read[1].ToString().Trim())
                {
                    for (int cnt = 0; cnt < modem_list.Items.Count; cnt++)
                    {
                        if (modem_list.Items[cnt].SubItems[6].Text != "-" &&
                            modem_list.Items[cnt].SubItems[idx].Text != "-" &&
                            modem_list.Items[cnt].SubItems[idx].Text != "0") modMax++;
                    }
                }
            }
            Item.SubItems.Add(modMax.ToString());
            Item.SubItems.Add("0");
            Item.SubItems.Add("-");
            Item.SubItems[6].BackColor = Color.Gray;
            Item.SubItems[6].Tag = "-";
            Item.SubItems.Add("0");
            Item.SubItems[7].Tag = "-";
            tbs_list.Items.Add(Item);
        }

        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }
        
        queryString = "SELECT SUBSTR(TRIM(rt.LOCATION_AREA), 2, 2)+0, COUNT(rt.SERVED_SSI_PART) FROM ";
        queryString += "(SELECT LOCATION_AREA, SERVED_SSI_PART ";
        queryString += "FROM TRS_NMS.T_PM_SDSTO_HIS ";
        queryString += "WHERE REG_DT LIKE '" + strDate + "%' AND LOCATION_AREA != 'SERVER' ";
        queryString += "GROUP BY LOCATION_AREA, SERVED_SSI_PART) rt ";
        queryString += "GROUP BY rt.LOCATION_AREA ";
        queryString += "ORDER BY rt.LOCATION_AREA";
        /*
        queryString = "SELECT SUBSTR(TRIM(rt.LOCATION_AREA), 2, 2)+0, COUNT(rt.SSI_PART) FROM ";
        queryString += "(SELECT LOCATION_AREA, SSI_PART ";
        queryString += "FROM TRS_NMS.T_PM_CONTROL_CHA_HIS ";
        queryString += "WHERE REG_DT LIKE '" + strDate + "%' AND LOCATION_AREA != 'SERVER' ";
        queryString += "GROUP BY LOCATION_AREA, SSI_PART) rt ";
        queryString += "GROUP BY rt.LOCATION_AREA ";
        queryString += "ORDER BY rt.LOCATION_AREA";
        */
        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        while (read.Read())
        {
            for(int cnt = 0; cnt < tbs_list.Items.Count; cnt++)
            {
                if (tbs_list.Items[cnt].SubItems[1].Text == read[0].ToString().Trim())
                {
                    tbs_list.Items[cnt].SubItems[5].Text = read[1].ToString().Trim();
                    break;
                }
            }
        }

        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }

        queryString = "SELECT REPLACE(TBS_ID, 'TBS-', '')+0 TBS, SUM(RANDOM_ACCESS) RC ";
        queryString += "FROM T_PM_TBS_RDO_NET ";
        queryString += "WHERE TO_CHAR(PERIOD_START,'yyyy-mm-dd') = '" + strDate + "' ";
        queryString += "GROUP BY TBS_ID ";
        queryString += "ORDER BY TBS";

        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        while (read.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == read[0].ToString().Trim())
                {
                    tbs_list.Items[i].SubItems[6].Text = read[1].ToString().Trim();

                    if (Convert.ToInt32(tbs_list.Items[i].SubItems[6].Text) >= collCritical)
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.Red;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[6].Text) >= collMajor)
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.Orange;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[6].Text) >= collMinor)
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.Yellow;
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[6].BackColor = Color.White;
                    }

                    break;
                }
            }
        }

        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }

        queryString = "SELECT REPLACE(TBS_ID, 'TBS-', '')+0 TBS, ";
        queryString += "TO_CHAR(PERIOD_START,'yyyy-mm-dd HH24') PER, SUM(RANDOM_ACCESS) RC ";
        queryString += "FROM T_PM_TBS_RDO_NET ";
        queryString += "WHERE TO_CHAR(PERIOD_START,'yyyy-mm-dd') = '" + strDate + "' ";
        queryString += "GROUP BY TBS_ID, TO_CHAR(PERIOD_START,'yyyy-mm-dd HH24') ";
        queryString += "ORDER BY TBS, PER";

        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        while (read.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == read[0].ToString().Trim())
                {
                    if (tbs_list.Items[i].SubItems[6].Tag.ToString() == "-")
                    {
                        tbs_list.Items[i].SubItems[6].Tag = read[1].ToString().Trim() + " : " + read[2].ToString().Trim();
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[6].Tag += "\r\n" + read[1].ToString().Trim() + " : " + read[2].ToString().Trim();
                    }

                    break;
                }
            }
        }

        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }

        queryString = "SELECT REPLACE(DEVICE_ID, 'TBS-', '')+0 TBS, COUNT(*) RC ";
        queryString += "FROM T_FM_FAULT_HIS ";
        //queryString += "WHERE ALARM_OBJ LIKE 'TTRX%' ";
        queryString += "WHERE ALARM_NUM = '6851' ";
        queryString += "AND SUBSTR(REG_DT, 0, 10) = '" + strDate + "' ";
        queryString += "GROUP BY DEVICE_ID ";
        queryString += "ORDER BY TBS";

        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        while (read.Read())
        {
            for (int i = 0; i < tbs_list.Items.Count; i++)
            {
                if (tbs_list.Items[i].SubItems[1].Text == read[0].ToString().Trim())
                {
                    tbs_list.Items[i].SubItems[7].Text = read[1].ToString().Trim();

                    if (Convert.ToInt32(tbs_list.Items[i].SubItems[7].Text) >= jammCritical)
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.Red;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[7].Text) >= jammMajor)
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.Orange;
                    }
                    else if (Convert.ToInt32(tbs_list.Items[i].SubItems[7].Text) >= jammMinor)
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.Yellow;
                    }
                    else
                    {
                        tbs_list.Items[i].SubItems[7].BackColor = Color.White;
                    }

                    break;
                }
            }
        }

        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }

        queryString = "SELECT REPLACE(DEVICE_ID, 'TBS-', '')+0 TBS, ";
        queryString += "REPLACE(SUBSTR(REG_DT, 0, 14), '  ', ' ') PER, ALARM_OBJ OBJ, COUNT(*) RC ";
        queryString += "FROM T_FM_FAULT_HIS ";
        //queryString += "WHERE ALARM_OBJ LIKE 'TTRX%' ";
        queryString += "WHERE ALARM_NUM = '6851' ";
        queryString += "AND SUBSTR(REG_DT, 0, 10) = '" + strDate + "' ";
        queryString += "GROUP BY DEVICE_ID, REPLACE(SUBSTR(REG_DT, 0, 14), '  ', ' '), ALARM_OBJ ";
        queryString += "ORDER BY TBS, PER, OBJ";

        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        string[,,] jamming = new string[tbs_list.Items.Count, 24, 3];
        for (int cnt1 = 0; cnt1 < tbs_list.Items.Count; cnt1++)
        {
            for (int cnt2 = 0; cnt2 < 24; cnt2++)
            {
                for (int cnt3 = 0; cnt3 < 3; cnt3++)
                {
                    jamming[cnt1, cnt2, cnt3] = "0";
                }
            }
        }

        while (read.Read())
        {
            for (int tbs = 0; tbs < tbs_list.Items.Count; tbs++)
            {
                if (tbs_list.Items[tbs].SubItems[1].Text == read[0].ToString().Trim())
                {
                    int time = Convert.ToInt32(read[1].ToString().Trim().Substring(read[1].ToString().Trim().Length - 2));
                    int jamm = Convert.ToInt32(read[2].ToString().Trim().Substring(read[2].ToString().Trim().Length - 1));

                    jamming[tbs, time, jamm] = read[3].ToString().Trim();
                }
            }
        }

        for (int tbs = 0; tbs < tbs_list.Items.Count; tbs++)
        {
            for (int time = 0; time < 24; time++)
            {
                if (tbs_list.Items[tbs].SubItems[7].Tag.ToString() == "-")
                {
                    if (time < 10)
                    {
                        tbs_list.Items[tbs].SubItems[7].Tag = strDate + " 0" + time.ToString() + " : " + jamming[tbs, time, 0].ToString() + " / " + jamming[tbs, time, 1].ToString() + " / " + jamming[tbs, time, 2].ToString();
                    }
                    else
                    {
                        tbs_list.Items[tbs].SubItems[7].Tag = strDate + " " + time.ToString() + " : " + jamming[tbs, time, 0].ToString() + " / " + jamming[tbs, time, 1].ToString() + " / " + jamming[tbs, time, 2].ToString();
                    }
                }
                else
                {
                    if (time < 10)
                    {
                        tbs_list.Items[tbs].SubItems[7].Tag += "\r\n" + strDate + " 0" + time.ToString() + " : " + jamming[tbs, time, 0].ToString() + " / " + jamming[tbs, time, 1].ToString() + " / " + jamming[tbs, time, 2].ToString();
                    }
                    else
                    {
                        tbs_list.Items[tbs].SubItems[7].Tag += "\r\n" + strDate + " " + time.ToString() + " : " + jamming[tbs, time, 0].ToString() + " / " + jamming[tbs, time, 1].ToString() + " / " + jamming[tbs, time, 2].ToString();
                    }
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
    finally
    {
        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }

        if (conn != null)
        {
            conn.Close(); conn = null;
            Console.WriteLine("데이터베이스 연결 해제...");
        }
    }

    int modemCnt = 0, maxCnt = 0, totCnt = 0, rcCnt = 0, jamCnt = 0;
    for (int cnt = 0; cnt < tbs_list.Items.Count; cnt++)
    {
        if (tbs_list.Items[cnt].SubItems[3].Text != "-") modemCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[3].Text);
        if (tbs_list.Items[cnt].SubItems[4].Text != "-") maxCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[4].Text);
        if (tbs_list.Items[cnt].SubItems[5].Text != "-") totCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[5].Text);
        if (tbs_list.Items[cnt].SubItems[6].Text != "-") rcCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[6].Text);
        if (tbs_list.Items[cnt].SubItems[7].Text != "-") jamCnt += Convert.ToInt32(tbs_list.Items[cnt].SubItems[7].Text);
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("합계");
    tItem.SubItems.Add(modemCnt.ToString());
    tItem.SubItems.Add(maxCnt.ToString());
    tItem.SubItems.Add(totCnt.ToString());
    tItem.SubItems.Add(rcCnt.ToString());
    tItem.SubItems.Add(jamCnt.ToString());
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    tbs_list.Items.Add(tItem);

    tbs_list.EndUpdate();
}

private void group_list_serch(string strDate)
{
    group_columnsorter.previousColumn = -1;
    group_flag = 0;
    group_list.ListViewItemSorter = null;

    group_list.Items.Clear();
    group_list.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(itConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT mo.system_code, mo.group_id, gr.group_name, COUNT(*) ";
        strFindQuery += "FROM modem_info AS mo CROSS JOIN ";
        strFindQuery += "group_info AS gr ";
        strFindQuery += "WHERE mo.use_yn = 'Y' AND ";
        strFindQuery += "mo.group_id = gr.group_id ";
        strFindQuery += "GROUP BY mo.system_code, mo.group_id, gr.group_name ";
        strFindQuery += "ORDER BY mo.group_id";

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
            Item.SubItems.Add("0");
            Item.SubItems.Add("0");
            group_list.Items.Add(Item);
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT id, COUNT(*) ";
        strFindQuery += "FROM (SELECT group_id id, modem_issi issi ";
        strFindQuery += "FROM modem_data_his ";
        strFindQuery += "WHERE his_save_dtime LIKE '" + strDate + "%' AND send_recv_yn = 'S' ";
        strFindQuery += "GROUP BY group_id, modem_issi) AS RESULT ";
        strFindQuery += "GROUP BY id ";
        strFindQuery += "ORDER BY id";

        Console.WriteLine(strFindQuery);

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int cnt = 0; cnt < group_list.Items.Count; cnt++)
            {
                Console.WriteLine(sqlRdr[0].ToString().Trim() + " == " + group_list.Items[cnt].SubItems[2].Text);

                if (sqlRdr[0].ToString().Trim() == group_list.Items[cnt].SubItems[2].Text)
                {
                    group_list.Items[cnt].SubItems[5].Text = sqlRdr[1].ToString().Trim();

                    Console.WriteLine(group_list.Items[cnt].SubItems[5].Text);
                }
            }
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        strFindQuery = "SELECT id, COUNT(*) ";
        strFindQuery += "FROM (SELECT group_id id, modem_issi issi ";
        strFindQuery += "FROM modem_data_his ";
        strFindQuery += "WHERE his_save_dtime LIKE '" + strDate + "%' AND send_recv_yn = 'R' ";
        strFindQuery += "GROUP BY group_id, modem_issi) AS RESULT ";
        strFindQuery += "GROUP BY id ";
        strFindQuery += "ORDER BY id";

        Console.WriteLine(strFindQuery);

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            for (int cnt = 0; cnt < group_list.Items.Count; cnt++)
            {
                Console.WriteLine(sqlRdr[0].ToString().Trim() + " == " + group_list.Items[cnt].SubItems[2].Text);

                if (sqlRdr[0].ToString().Trim() == group_list.Items[cnt].SubItems[2].Text)
                {
                    group_list.Items[cnt].SubItems[6].Text = sqlRdr[1].ToString().Trim();

                    Console.WriteLine(group_list.Items[cnt].SubItems[6].Text);
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

    int modemCnt = 0, sendCnt = 0, recvCnt = 0;
    for (int cnt = 0; cnt < group_list.Items.Count; cnt++)
    {
        modemCnt += Convert.ToInt32(group_list.Items[cnt].SubItems[4].Text);
        sendCnt += Convert.ToInt32(group_list.Items[cnt].SubItems[5].Text);
        recvCnt += Convert.ToInt32(group_list.Items[cnt].SubItems[6].Text);
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("합계");
    tItem.SubItems.Add(modemCnt.ToString());
    tItem.SubItems.Add(sendCnt.ToString());
    tItem.SubItems.Add(recvCnt.ToString());
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    group_list.Items.Add(tItem);

    group_list.EndUpdate();
}


private void alarm_search()
{
    WaitMsg.ShowWait(this, WaitMsg.waitMsg);

    string strDate = alarm_date.Value.ToString("yyyy-MM-dd");

    //status_label.Text = "[시스템별 장애 현황] 조회중";

    nms_columnsorter.previousColumn = -1;
    nms_flag = 0;
    nms_list.ListViewItemSorter = null;

    nms_list.Items.Clear();
    nms_list.BeginUpdate();

    string queryString = "";
    OracleCommand command = null;
    OracleDataReader read = null;
    OracleConnection conn = new OracleConnection(nmsConnStr);

    try
    {
        conn.Open();
        Console.WriteLine("데이터베이스[TRS-NMS] 연결 성공...");

        queryString = "SELECT URGENCY_LVL, SUBSTR(REPLACE(REG_DT, '  ', ' '), 1, 19), '-', DEVICE_TYPE, DEVICE_ID, ";
        queryString += "ALARM_EQ_TYPE, ALARM_OBJ, ALARM_NUM, TEXT ";
        queryString += "FROM T_FM_FAULT_MON ";
        queryString += "WHERE REG_DT LIKE '" + strDate + "%' ";
        queryString += "ORDER BY REG_DT";

        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        while (read.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            for (int i = 0; i < read.FieldCount; i++)
            {
                Item.SubItems.Add(read[i].ToString().Trim());
                if (i == 2)
                {
                    Item.SubItems.Add("-");
                }
            }

            if (Item.SubItems[1].Text.Equals("CRITICAL"))
            {
                Item.SubItems[1].BackColor = Color.Red;
            }
            else if (Item.SubItems[1].Text.Equals("MAJOR"))
            {
                Item.SubItems[1].BackColor = Color.Orange;
            }
            else if (Item.SubItems[1].Text.Equals("MINOR"))
            {
                Item.SubItems[1].BackColor = Color.Yellow;
            }
            else
            {
                Item.SubItems[1].BackColor = Color.Gray;
            }

            nms_list.Items.Add(Item);
        }

        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }

        queryString = "SELECT URGENCY_LVL, SUBSTR(REPLACE(REG_DT, '  ', ' '), 1, 19), SUBSTR(REPLACE(RECOV_DT, '  ', ' '), 1, 19), ";
        queryString += "DEVICE_TYPE, DEVICE_ID, ALARM_EQ_TYPE, ALARM_OBJ, ALARM_NUM, TEXT ";
        queryString += "FROM T_FM_FAULT_HIS ";
        queryString += "WHERE REG_DT LIKE '" + strDate + "%' AND PRINTOUT_TYPE = 'ALARM'";
        queryString += "ORDER BY REG_DT";

        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        while (read.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            for (int i = 0; i < read.FieldCount; i++)
            {
                Item.SubItems.Add(read[i].ToString().Trim());
                if (i == 2)
                {
                    Item.SubItems.Add((DateTime.Parse(Item.SubItems[3].Text) - DateTime.Parse(Item.SubItems[2].Text)).ToString());
                }
            }

            if (Item.SubItems[1].Text.Equals("CRITICAL"))
            {
                Item.SubItems[1].BackColor = Color.Red;
            }
            else if (Item.SubItems[1].Text.Equals("MAJOR"))
            {
                Item.SubItems[1].BackColor = Color.Orange;
            }
            else if (Item.SubItems[1].Text.Equals("MINOR"))
            {
                Item.SubItems[1].BackColor = Color.Yellow;
            }
            else
            {
                Item.SubItems[1].BackColor = Color.Gray;
            }

            nms_list.Items.Add(Item);
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
    finally
    {
        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }

        if (conn != null)
        {
            conn.Close(); conn = null;
            Console.WriteLine("데이터베이스[TRS-NMS] 연결 해제...");
        }
    }

    nms_list.EndUpdate();

    /**
    it_columnsorter.previousColumn = -1;
    it_flag = 0;
    it_list.ListViewItemSorter = null;

    it_list.Items.Clear();
    it_list.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(itConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스[TRS-IT] 연결 성공...");

        strFindQuery = "SELECT arm.Alarm_Grade, SUBSTRING(arm.Alarm_Time, 1, 19), SUBSTRING(arm.Recovery_Time, 1, 19), ";
        strFindQuery += "arm.Alarm_Check, it.IT_Main_System_Name, mo.Modem_Name, arm.Modem_ISSI, arm.Alarm_Text ";
        strFindQuery += "FROM Alarm_Log AS arm CROSS JOIN ";
        strFindQuery += "Modem AS mo CROSS JOIN ";
        strFindQuery += "IT_Main_System AS it ";
        strFindQuery += "WHERE Alarm_Time LIKE '" + strDate + "%' AND ";
        strFindQuery += "arm.KEPCO_IT_System_Code = mo.KEPCO_IT_System_Code AND ";
        strFindQuery += "arm.IT_Main_System_Seq = mo.IT_Main_System_Seq AND ";
        strFindQuery += "arm.Modem_ISSI = mo.Modem_ISSI AND ";
        strFindQuery += "arm.KEPCO_IT_System_Code = it.KEPCO_IT_System_Code AND ";
        strFindQuery += "arm.IT_Main_System_Seq = it.IT_Main_System_Seq ";
        strFindQuery += "ORDER BY arm.Alarm_Time";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            for (int i = 0; i < sqlRdr.FieldCount; i++)
            {
                if (sqlRdr[i].ToString().Trim() == "")
                {
                    Item.SubItems.Add("-");
                    if (i == 2)
                    {
                        Item.SubItems.Add("-");
                    }
                }
                else
                {
                    if (i == 0)
                    {
                        if (sqlRdr[i].ToString().Trim() == "0")
                        {
                            Item.SubItems.Add("CRITICAL");
                        }
                        else if (sqlRdr[i].ToString().Trim() == "1")
                        {
                            Item.SubItems.Add("MAJOR");
                        }
                        else if (sqlRdr[i].ToString().Trim() == "2")
                        {
                            Item.SubItems.Add("MINOR");
                        }
                        else
                        {
                            Item.SubItems.Add("UNKNOWN");
                        }
                    }
                    else if (i == 2)
                    {
                        Item.SubItems.Add(sqlRdr[i].ToString().Trim());
                        Item.SubItems.Add((DateTime.Parse(Item.SubItems[3].Text) - DateTime.Parse(Item.SubItems[2].Text)).ToString());
                    }
                    else if (i == 3)
                    {
                        if (sqlRdr[i].ToString().Trim() == "1")
                        {
                            Item.SubItems.Add("주장치");
                        }
                        else if (sqlRdr[i].ToString().Trim() == "2")
                        {
                            Item.SubItems.Add("G/W");
                        }
                        else if (sqlRdr[i].ToString().Trim() == "3")
                        {
                            Item.SubItems.Add("MODEM");
                        }
                        else
                        {
                            Item.SubItems.Add("-");
                        }
                    }
                    else
                    {
                        Item.SubItems.Add(sqlRdr[i].ToString().Trim());
                    }
                }
            }

            if (Item.SubItems[1].Text.Equals("CRITICAL"))
            {
                Item.SubItems[1].BackColor = Color.Red;
            }
            else if (Item.SubItems[1].Text.Equals("MAJOR"))
            {
                Item.SubItems[1].BackColor = Color.Orange;
            }
            else if (Item.SubItems[1].Text.Equals("MINOR"))
            {
                Item.SubItems[1].BackColor = Color.Yellow;
            }
            else
            {
                Item.SubItems[1].BackColor = Color.Gray;
            }
            
            it_list.Items.Add(Item);
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
            Console.WriteLine("데이터베이스[TRS-IT] 연결 해제...");
        }
    }
    it_list.EndUpdate();

    gw_columnsorter.previousColumn = -1;
    gw_flag = 0;
    gw_list.ListViewItemSorter = null;

    gw_list.Items.Clear();
    gw_list.BeginUpdate();

    sqlConn = new SqlConnection(gwConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스[TRS-GW] 연결 성공...");

        strFindQuery = "SELECT Alarm_Grade, SUBSTRING(Alarm_Time, 1, 19), SUBSTRING(Recovery_Time, 1, 19), ";
        strFindQuery += "Alarm_Check, Server_Name, Alarm_Text ";
        strFindQuery += "FROM Alarm_Log ";
        strFindQuery += "WHERE Alarm_Time LIKE '" + strDate + "%' ";
        strFindQuery += "ORDER BY Alarm_Time";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            for (int i = 0; i < sqlRdr.FieldCount; i++)
            {
                if (sqlRdr[i].ToString().Trim() == "")
                {
                    Item.SubItems.Add("-");
                    if (i == 2)
                    {
                        Item.SubItems.Add("-");
                    }
                }
                else
                {
                    if (i == 0)
                    {
                        if (sqlRdr[i].ToString().Trim() == "0")
                        {
                            Item.SubItems.Add("CRITICAL");
                        }
                        else if (sqlRdr[i].ToString().Trim() == "1")
                        {
                            Item.SubItems.Add("MAJOR");
                        }
                        else if (sqlRdr[i].ToString().Trim() == "2")
                        {
                            Item.SubItems.Add("MINOR");
                        }
                        else
                        {
                            Item.SubItems.Add("UNKNOWN");
                        }
                    }
                    else if (i == 2)
                    {
                        Item.SubItems.Add(sqlRdr[i].ToString().Trim());
                        Item.SubItems.Add((DateTime.Parse(Item.SubItems[3].Text) - DateTime.Parse(Item.SubItems[2].Text)).ToString());
                    }
                    else if (i == 3)
                    {
                        if (sqlRdr[i].ToString().Trim() == "1")
                        {
                            Item.SubItems.Add("연동서버");
                        }
                        else if (sqlRdr[i].ToString().Trim() == "2")
                        {
                            Item.SubItems.Add("TCS");
                        }
                        else
                        {
                            Item.SubItems.Add("-");
                        }
                    }
                    else
                    {
                        Item.SubItems.Add(sqlRdr[i].ToString().Trim());
                    }
                }
            }

            if (Item.SubItems[1].Text.Equals("CRITICAL"))
            {
                Item.SubItems[1].BackColor = Color.Red;
            }
            else if (Item.SubItems[1].Text.Equals("MAJOR"))
            {
                Item.SubItems[1].BackColor = Color.Orange;
            }
            else if (Item.SubItems[1].Text.Equals("MINOR"))
            {
                Item.SubItems[1].BackColor = Color.Yellow;
            }
            else
            {
                Item.SubItems[1].BackColor = Color.Gray;
            }

            gw_list.Items.Add(Item);
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
            Console.WriteLine("데이터베이스[TRS-GW] 연결 해제...");
        }
    }

    gw_list.EndUpdate();
    **/
    
    iems_columnsorter.previousColumn = -1;
    iems_flag = 0;
    iems_list.ListViewItemSorter = null;

    iems_list.Items.Clear();
    iems_list.BeginUpdate();

    conn = new OracleConnection(iemsConnStr);

    try
    {
        conn.Open();
        Console.WriteLine("데이터베이스[IEMS] 연결 성공...");

        queryString = "SELECT PFL.EVENT_CLASS, SUBSTR(ALM.START_DATE, 1, 19), SUBSTR(ALM.SOLVE_DATE, 1, 19), ";
        queryString += "OFC.OFFICE_NAME, SYS.SYSTEM_NAME, SVR.SERVER_NAME, PFL.PROFILE_EVENT_ALARM_NAME ";
        queryString += "FROM LOG_EVENT_ALARM ALM CROSS JOIN ";
        queryString += "SERVER_INFO SVR CROSS JOIN ";
        queryString += "SYSTEM_INFO SYS CROSS JOIN ";
        queryString += "OFFICE OFC CROSS JOIN ";
        queryString += "PROFILE_EVENT_ALARM PFL ";
        if (iemsCode1 != "" && iemsCode2 != "")
        {
            queryString += "WHERE SYS.OFFICE_ID >= " + iemsCode1 + " AND SYS.OFFICE_ID <= " + iemsCode2 + " AND ";
        }
        else
        {
            queryString += "WHERE ";
        }
        queryString += "ALM.START_DATE LIKE '" + strDate + "%' AND ";
        queryString += "ALM.SERVER_ID = SVR.SERVER_ID AND ";
        queryString += "SVR.SYSTEM_ID = SYS.SYSTEM_ID AND ";
        queryString += "SYS.OFFICE_ID = OFC.OFFICE_ID AND ";
        queryString += "ALM.PROFILE_EVENT_ALARM_ID = PFL.PROFILE_EVENT_ALARM_ID ";
        queryString += "ORDER BY ALM.START_DATE DESC";
    
        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        while (read.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            for (int i = 0; i < read.FieldCount; i++)
            {
                if (read[i].ToString().Trim() == "")
                {
                    Item.SubItems.Add("-");
                    if (i == 2)
                    {
                        Item.SubItems.Add("-");
                    }
                }
                else
                {
                    if (i == 0)
                    {
                        if (read[i].ToString().Trim() == "1")
                        {
                            Item.SubItems.Add("CRITICAL");
                        }
                        else if (read[i].ToString().Trim() == "2")
                        {
                            Item.SubItems.Add("MAJOR");
                        }
                        else if (read[i].ToString().Trim() == "3")
                        {
                            Item.SubItems.Add("MINOR");
                        }
                        else
                        {
                            Item.SubItems.Add("UNKNOWN");
                        }
                    }
                    else if (i == 2)
                    {
                        Item.SubItems.Add(read[i].ToString().Trim());
                        Item.SubItems.Add((DateTime.Parse(Item.SubItems[3].Text) - DateTime.Parse(Item.SubItems[2].Text)).ToString());
                    }
                    else
                    {
                        Item.SubItems.Add(read[i].ToString().Trim());
                    }
                }
            }

            if (Item.SubItems[1].Text.Equals("CRITICAL"))
            {
                Item.SubItems[1].BackColor = Color.Red;
            }
            else if (Item.SubItems[1].Text.Equals("MAJOR"))
            {
                Item.SubItems[1].BackColor = Color.Orange;
            }
            else if (Item.SubItems[1].Text.Equals("MINOR"))
            {
                Item.SubItems[1].BackColor = Color.Yellow;
            }
            else
            {
                Item.SubItems[1].BackColor = Color.Gray;
            }

            iems_list.Items.Add(Item);
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
    finally
    {
        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }

        if (conn != null)
        {
            conn.Close(); conn = null;
            Console.WriteLine("데이터베이스[IEMS] 연결 해제...");
        }
    }

    iems_list.EndUpdate();

    /**
    tbms_columnsorter.previousColumn = -1;
    tbms_flag = 0;
    tbms_list.ListViewItemSorter = null;

    tbms_list.Items.Clear();
    tbms_list.BeginUpdate();

    sqlConn = new SqlConnection(tbmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스[TBMS] 연결 성공...");

        strFindQuery = "SELECT ACD.ALARM_GRAD, CONVERT(CHAR(19), OCCR_DTM, 20), CONVERT(CHAR(19), RECR_DTM, 20), ";
        strFindQuery += "GRP.GROUP_NM, BAS.SITE_NM, COD.CODE_NM, ACD.ALARM_NM ";
        strFindQuery += "FROM SITE_ALARM ARM CROSS JOIN ";
        strFindQuery += "SITE_BAS BAS CROSS JOIN ";
        strFindQuery += "ALARM_CODE ACD CROSS JOIN ";
        strFindQuery += "CODE_BAS COD CROSS JOIN ";
        strFindQuery += "WK_GROUP GRP ";
        if (tbmsCode != "")
        {
            strFindQuery += "WHERE BAS.GROUP_ID = '" + tbmsCode + "' AND ";
        }
        else
        {
            strFindQuery += "WHERE ";
        }
        strFindQuery += "CONVERT(CHAR(10), OCCR_DTM, 23) = '" + strDate + "' AND ";
        strFindQuery += "ARM.SITE_ID = BAS.SITE_ID AND ";
        strFindQuery += "ARM.ALARM_CD = ACD.ALARM_CD AND ";
        strFindQuery += "COD.CLSS_CD = 'RTU_MK' AND  ";
        strFindQuery += "BAS.RTU_MK_CD = COD.CODE AND ";
        strFindQuery += "BAS.GROUP_ID = GRP.GROUP_ID ";
        strFindQuery += "ORDER BY OCCR_DTM";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            for (int i = 0; i < sqlRdr.FieldCount; i++)
            {
                if (sqlRdr[i].ToString().Trim() == "")
                {
                    Item.SubItems.Add("-");
                    if (i == 2)
                    {
                        Item.SubItems.Add("-");
                    }
                }
                else
                {
                    if (i == 0)
                    {
                        if (sqlRdr[i].ToString().Trim() == "C")
                        {
                            Item.SubItems.Add("CRITICAL");
                        }
                        else if (sqlRdr[i].ToString().Trim() == "M")
                        {
                            Item.SubItems.Add("MAJOR");
                        }
                        else if (sqlRdr[i].ToString().Trim() == "I")
                        {
                            Item.SubItems.Add("MINOR");
                        }
                        else
                        {
                            Item.SubItems.Add("UNKNOWN");
                        }
                    }
                    else if (i == 2)
                    {
                        Item.SubItems.Add(sqlRdr[i].ToString().Trim());
                        Item.SubItems.Add((DateTime.Parse(Item.SubItems[3].Text) - DateTime.Parse(Item.SubItems[2].Text)).ToString());
                    }
                    else
                    {
                        Item.SubItems.Add(sqlRdr[i].ToString().Trim());
                    }
                }
            }

            if (Item.SubItems[1].Text.Equals("CRITICAL"))
            {
                Item.SubItems[1].BackColor = Color.Red;
            }
            else if (Item.SubItems[1].Text.Equals("MAJOR"))
            {
                Item.SubItems[1].BackColor = Color.Orange;
            }
            else if (Item.SubItems[1].Text.Equals("MINOR"))
            {
                Item.SubItems[1].BackColor = Color.Yellow;
            }
            else
            {
                Item.SubItems[1].BackColor = Color.Gray;
            }

            tbms_list.Items.Add(Item);
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
            Console.WriteLine("데이터베이스[TBMS] 연결 해제...");
        }
    }

    tbms_list.EndUpdate();
    **/

    int critical = 0, major = 0, minor = 0;
    for (int cnt = 0; cnt < nms_list.Items.Count; cnt++)
    {
        if (nms_list.Items[cnt].SubItems[1].Text == "CRITICAL") critical++;
        else if (nms_list.Items[cnt].SubItems[1].Text == "MAJOR") major++;
        else if (nms_list.Items[cnt].SubItems[1].Text == "MINOR") minor++;
    }
    for (int cnt = 0; cnt < it_list.Items.Count; cnt++)
    {
        if (it_list.Items[cnt].SubItems[1].Text == "CRITICAL") critical++;
        else if (it_list.Items[cnt].SubItems[1].Text == "MAJOR") major++;
        else if (it_list.Items[cnt].SubItems[1].Text == "MINOR") minor++;
    }
    for (int cnt = 0; cnt < gw_list.Items.Count; cnt++)
    {
        if (gw_list.Items[cnt].SubItems[1].Text == "CRITICAL") critical++;
        else if (gw_list.Items[cnt].SubItems[1].Text == "MAJOR") major++;
        else if (gw_list.Items[cnt].SubItems[1].Text == "MINOR") minor++;
    }
    for (int cnt = 0; cnt < iems_list.Items.Count; cnt++)
    {
        if (iems_list.Items[cnt].SubItems[1].Text == "CRITICAL") critical++;
        else if (iems_list.Items[cnt].SubItems[1].Text == "MAJOR") major++;
        else if (iems_list.Items[cnt].SubItems[1].Text == "MINOR") minor++;
    }
    for (int cnt = 0; cnt < tbms_list.Items.Count; cnt++)
    {
        if (tbms_list.Items[cnt].SubItems[1].Text == "CRITICAL") critical++;
        else if (tbms_list.Items[cnt].SubItems[1].Text == "MAJOR") major++;
        else if (tbms_list.Items[cnt].SubItems[1].Text == "MINOR") minor++;
    }

    total_total.Text = (nms_list.Items.Count + it_list.Items.Count + gw_list.Items.Count + iems_list.Items.Count + tbms_list.Items.Count).ToString();
    total_critical.Text = critical.ToString();
    total_major.Text = major.ToString();
    total_minor.Text = minor.ToString();

    alarm_tab_SelectedIndexChanged(null, null);

    //status_label.Text = "대기중";

    if (total_total.Text == "0") alarm_saveBtn.Enabled = false;
    else alarm_saveBtn.Enabled = true;

    WaitMsg.HideWait(this);
}

private void db_save()
{
    WaitMsg.ShowWait(this, WaitMsg.dbMsg);

    string strSaveQuery = "";
    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스[TRS-CMS] 연결 성공...");

        for (int cnt = 0; cnt < tbs_list.Items.Count; cnt++)
        {
            if (tbs_list.Items[cnt].SubItems[1].Text != "-")
            {
                strSaveQuery = "INSERT INTO DAY_TBS_INFO(REG_DT, TBS_ID, TBS_NAME, MODEM_CNT, MODEM_MAX, MODEM_TOT) VALUES(";
                strSaveQuery += "'" + tbs_date.Value.ToString("yyyy-MM-dd") + "', ";
                strSaveQuery += "'" + tbs_list.Items[cnt].SubItems[1].Text + "', ";
                strSaveQuery += "'" + tbs_list.Items[cnt].SubItems[2].Text + "', ";
                strSaveQuery += "" + tbs_list.Items[cnt].SubItems[3].Text + ", ";
                strSaveQuery += "" + tbs_list.Items[cnt].SubItems[4].Text + ", ";
                strSaveQuery += "" + tbs_list.Items[cnt].SubItems[5].Text + ")";

                sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
                sqlCmd.ExecuteReader().Close();

                if (tbs_list.Items[cnt].SubItems[6].Tag.ToString() != "-")
                {
                    string[] tag = tbs_list.Items[cnt].SubItems[6].Tag.ToString().Split('\n');
                    for (int idx = 0; idx < tag.Length; idx++)
                    {
                        string regdt, coll;
                        string[] tmp = tag[idx].Trim().Split(':');
                        if (tmp.Length == 2)
                        {
                            regdt = tmp[0].Trim();
                            coll = tmp[1].Trim();

                            strSaveQuery = "INSERT INTO DAY_TBS_COLL(REG_DT, TBS_ID, TBS_COLL) VALUES(";
                            strSaveQuery += "'" + regdt + "', ";
                            strSaveQuery += "'" + tbs_list.Items[cnt].SubItems[1].Text + "', ";
                            strSaveQuery += "" + coll + ")";

                            sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
                            sqlCmd.ExecuteReader().Close();
                        }
                    }
                }

                if (tbs_list.Items[cnt].SubItems[7].Tag.ToString() != "-")
                {
                    string[] tag = tbs_list.Items[cnt].SubItems[7].Tag.ToString().Split('\n');
                    for (int idx = 0; idx < tag.Length; idx++)
                    {
                        string regdt, jamm1, jamm2, jamm3;
                        string[] tmp1 = tag[idx].Trim().Split(':');
                        if (tmp1.Length == 2)
                        {
                            regdt = tmp1[0].Trim();
                            string[] tmp2 = tmp1[1].Trim().Split('/');
                            if (tmp2.Length == 3)
                            {
                                jamm1 = tmp2[0].Trim();
                                jamm2 = tmp2[1].Trim();
                                jamm3 = tmp2[2].Trim();

                                strSaveQuery = "INSERT INTO DAY_TBS_JAMM(REG_DT, TBS_ID, TTRX0_JAMM, TTRX1_JAMM, TTRX2_JAMM) VALUES(";
                                strSaveQuery += "'" + regdt + "', ";
                                strSaveQuery += "'" + tbs_list.Items[cnt].SubItems[1].Text + "', ";
                                strSaveQuery += "" + jamm1 + ", " + jamm2 + ", " + jamm3 + ")";

                                sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
                                sqlCmd.ExecuteReader().Close();
                            }
                        }
                    }
                }
            }
        }

        for (int cnt = 0; cnt < group_list.Items.Count; cnt++)
        {
            if (group_list.Items[cnt].SubItems[1].Text != "-")
            {
                strSaveQuery = "INSERT INTO DAY_GROUP_INFO(REG_DT, GROUP_CODE, GROUP_ID, GROUP_NAME, MODEM_CNT, SEND_CNT, RECV_CNT) VALUES(";
                strSaveQuery += "'" + group_date.Value.ToString("yyyy-MM-dd") + "', ";
                strSaveQuery += "'" + group_list.Items[cnt].SubItems[1].Text + "', ";
                strSaveQuery += "'" + group_list.Items[cnt].SubItems[2].Text + "', ";
                strSaveQuery += "'" + group_list.Items[cnt].SubItems[3].Text + "', ";
                strSaveQuery += "" + group_list.Items[cnt].SubItems[4].Text + ", ";
                strSaveQuery += "" + group_list.Items[cnt].SubItems[5].Text + ", ";
                strSaveQuery += "" + group_list.Items[cnt].SubItems[6].Text + ")";

                sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
                sqlCmd.ExecuteReader().Close();
            }
        }

        for (int cnt = 0; cnt < modem_list.Items.Count; cnt++)
        {
            string beforeRegdt = "";
            string beforeExcept = "";

            strFindQuery = "SELECT TOP(1) SUBSTRING(REG_DT, 1, 10), SUCC_EXCEPT ";
            strFindQuery += "FROM MODEM_HISTORY ";
            strFindQuery += "WHERE SUCC_EXCEPT IS NOT NULL AND ";
            strFindQuery += "MODEM_ISSI = '" + modem_list.Items[cnt].SubItems[6].Text + "' ";
            strFindQuery += "ORDER BY REG_DT DESC";

            sqlCmd = new SqlCommand(strFindQuery, sqlConn);
            sqlRdr = sqlCmd.ExecuteReader();

            while (sqlRdr.Read())
            {
                beforeRegdt = sqlRdr[0].ToString().Trim();
                beforeExcept = sqlRdr[1].ToString().Trim();
            }
            Console.WriteLine("before = " + beforeRegdt + " = " + beforeExcept);

            if (sqlRdr != null && !sqlRdr.IsClosed)
            {
                sqlRdr.Close(); sqlRdr = null;
            }
            
            if (modem_list.Items[cnt].SubItems[1].Text != "-")
            {
                strSaveQuery = "INSERT INTO DAY_MODEM_INFO(REG_DT, MODEM_ISSI, GROUP_CODE, GROUP_NAME, NODE_NAME, NODE_ID, RDBM_AVRG, RDBM_DIFF, ";
                strSaveQuery += "RDBM_MAX, RDBM_MIN, SEND_CNT, SEND_AVRG, SEND_SUCC, SEND_FAIL, RECV_CNT, RECV_TBS, HO_CNT, SUCC_EXCEPT) VALUES(";
                strSaveQuery += "'" + modem_date.Value.ToString("yyyy-MM-dd") + "', ";
                strSaveQuery += "'" + modem_list.Items[cnt].SubItems[6].Text + "', ";
                strSaveQuery += "'" + modem_list.Items[cnt].SubItems[2].Text + "', ";
                strSaveQuery += "'" + modem_list.Items[cnt].SubItems[3].Text + "', ";
                strSaveQuery += "'" + modem_list.Items[cnt].SubItems[4].Text + "', ";
                strSaveQuery += "'" + modem_list.Items[cnt].SubItems[5].Text + "', ";
                if (modem_list.Items[cnt].SubItems[7].Text != "-")
                {
                    strSaveQuery += "" + modem_list.Items[cnt].SubItems[7].Text + ", ";
                }
                else strSaveQuery += "NULL, ";
                if (modem_list.Items[cnt].SubItems[8].Text != "-")
                {
                    strSaveQuery += "" + modem_list.Items[cnt].SubItems[8].Text + ", ";
                }
                else strSaveQuery += "NULL, ";
                if (modem_list.Items[cnt].SubItems[9].Text != "-")
                {
                    strSaveQuery += "" + modem_list.Items[cnt].SubItems[9].Text + ", ";
                }
                else strSaveQuery += "NULL, ";
                if (modem_list.Items[cnt].SubItems[10].Text != "-")
                {
                    strSaveQuery += "" + modem_list.Items[cnt].SubItems[10].Text + ", ";
                }
                else strSaveQuery += "NULL, ";
                if (modem_list.Items[cnt].SubItems[11].Text != "-")
                {
                    strSaveQuery += "" + modem_list.Items[cnt].SubItems[11].Text + ", ";
                }
                else strSaveQuery += "NULL, ";
                if (modem_list.Items[cnt].SubItems[12].Text != "-")
                {
                    strSaveQuery += "" + modem_list.Items[cnt].SubItems[12].Text + ", ";
                }
                else strSaveQuery += "NULL, ";
                if (modem_list.Items[cnt].SubItems[13].Text != "-")
                {
                    strSaveQuery += "" + modem_list.Items[cnt].SubItems[13].Text + ", ";
                }
                else strSaveQuery += "NULL, ";
                if (modem_list.Items[cnt].SubItems[14].Text != "-")
                {
                    strSaveQuery += "" + modem_list.Items[cnt].SubItems[14].Text + ", ";
                }
                else strSaveQuery += "NULL, ";
                if (modem_list.Items[cnt].SubItems[15].Text != "-")
                {
                    strSaveQuery += "" + modem_list.Items[cnt].SubItems[15].Text + ", ";
                }
                else strSaveQuery += "NULL, ";
                strSaveQuery += "'" + modem_list.Items[cnt].SubItems[16].Text + "', ";
                if (modem_list.Items[cnt].SubItems[17].Text != "-")
                {
                    strSaveQuery += "" + modem_list.Items[cnt].SubItems[17].Text + ", ";
                }
                else strSaveQuery += "NULL, ";
                if (beforeExcept == "1")
                {
                    strSaveQuery += "'1')";
                }
                else strSaveQuery += "NULL)";

                sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
                sqlCmd.ExecuteReader().Close();

                for (int idx = 18; idx < modem_list.Columns.Count; idx++)
                {
                    if (modem_list.Items[cnt].SubItems[idx].Text != "-" && modem_list.Items[cnt].SubItems[idx].Text != "0")
                    {
                        strSaveQuery = "INSERT INTO DAY_MODEM_RECV(REG_DT, MODEM_ISSI, TBS_ID, RECV_CNT) VALUES(";
                        strSaveQuery += "'" + modem_date.Value.ToString("yyyy-MM-dd") + "', ";
                        strSaveQuery += "'" + modem_list.Items[cnt].SubItems[6].Text + "', ";
                        strSaveQuery += "'" + modem_list.Columns[idx].Tag.ToString() + "', ";
                        strSaveQuery += "" + modem_list.Items[cnt].SubItems[idx].Text + ")";

                        sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
                        sqlCmd.ExecuteReader().Close();
                    }
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
            Console.WriteLine("데이터베이스[TRS-CMS] 연결 해제...");
        }
    }

    data_list_search();
    db_saveBtn.Enabled = false;
    db_deleteBtn.Enabled = false;

    WaitMsg.HideWait(this);
}

private void db_deleteBtn_Click(object sender, EventArgs e)
{
    WaitMsg.ShowWait(this, WaitMsg.delMsg);

    string strSaveQuery = "";
    SqlCommand sqlCmd = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스[TRS-CMS] 연결 성공...");

        strSaveQuery = "DELETE FROM DAY_TBS_INFO ";
        strSaveQuery += "WHERE REG_DT = '" + day_list.SelectedItems[0].SubItems[1].Text + "'";

        sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
        sqlCmd.ExecuteReader().Close();

        strSaveQuery = "DELETE FROM DAY_TBS_COLL ";
        strSaveQuery += "WHERE REG_DT LIKE '" + day_list.SelectedItems[0].SubItems[1].Text + "%'";

        sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
        sqlCmd.ExecuteReader().Close();

        strSaveQuery = "DELETE FROM DAY_TBS_JAMM ";
        strSaveQuery += "WHERE REG_DT LIKE '" + day_list.SelectedItems[0].SubItems[1].Text + "%'";

        sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
        sqlCmd.ExecuteReader().Close();

        strSaveQuery = "DELETE FROM DAY_GROUP_INFO ";
        strSaveQuery += "WHERE REG_DT = '" + day_list.SelectedItems[0].SubItems[1].Text + "'";

        sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
        sqlCmd.ExecuteReader().Close();

        strSaveQuery = "DELETE FROM DAY_MODEM_INFO ";
        strSaveQuery += "WHERE REG_DT = '" + day_list.SelectedItems[0].SubItems[1].Text + "'";

        sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
        sqlCmd.ExecuteReader().Close();

        strSaveQuery = "DELETE FROM DAY_MODEM_RECV ";
        strSaveQuery += "WHERE REG_DT = '" + day_list.SelectedItems[0].SubItems[1].Text + "'";

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

    data_list_search();
    db_deleteBtn.Enabled = false;

    ListViewItem fItem = null;
    if (day_list.Items.Count > 0)
    {
        fItem = day_list.FindItemWithText(modem_date.Value.ToString("yyyy-MM-dd"), true, 0);
    }

    if (fItem != null || 
        modem_list.Items.Count <= 0 ||
        DateTime.Today.ToString("yyyy-MM-dd") == modem_date.Value.ToString("yyyy-MM-dd"))
        db_saveBtn.Enabled = false;
    else db_saveBtn.Enabled = true;

    WaitMsg.HideWait(this);
}

private void modem_list_MouseDoubleClick(object sender, MouseEventArgs e)
{
    if (modem_list.SelectedItems.Count <= 0) return;

    string page = modem_list.SelectedItems[0].SubItems[6].Text + "(" + modem_list.SelectedItems[0].SubItems[4].Text + ")";
    for (int cnt = 0; cnt < modem_tab.TabPages.Count; cnt++)
    {
        if (modem_tab.TabPages[cnt].Text == page)
        {
            modem_tab.SelectedIndex = cnt;
            return;
        }
    }

    WaitMsg.ShowWait(this, WaitMsg.waitMsg);

    string strDate = DateTime.Today.AddDays(-365).ToString("yyyy-MM-dd");
    Console.WriteLine("modem_list_MouseDoubleClick = [" + strDate + "]");

    modem_tab.TabPages.Add(page);
    modem_tab.SelectedIndex = modem_tab.TabPages.Count - 1;

    ListView molist = new ListView();
    molist.Parent = modem_tab.TabPages[modem_tab.SelectedIndex];
    molist.Tag = page + " 통신 현황";
    molist.Location = new Point(2, 4);
    molist.Size = new Size(745, 264);
    molist.CheckBoxes = true;
    molist.FullRowSelect = true;
    molist.GridLines = true;
    molist.MultiSelect = false;
    molist.View = View.Details;

    molist.Columns.Add("", 0);
    molist.Columns.Add("일자", 105, HorizontalAlignment.Center);
    molist.Columns.Add("평균", 50, HorizontalAlignment.Center);
    molist.Columns.Add("편차", 50, HorizontalAlignment.Center);
    molist.Columns.Add("최대", 50, HorizontalAlignment.Center);
    molist.Columns.Add("최소", 50, HorizontalAlignment.Center);
    molist.Columns.Add("발신", 55, HorizontalAlignment.Center);
    molist.Columns.Add("성공률", 60, HorizontalAlignment.Center);
    molist.Columns.Add("성공", 55, HorizontalAlignment.Center);
    molist.Columns.Add("실패", 55, HorizontalAlignment.Center);
    molist.Columns.Add("수신", 55, HorizontalAlignment.Center);
    molist.Columns.Add("기지국", 90, HorizontalAlignment.Center);
    molist.Columns.Add("HO", 50, HorizontalAlignment.Center);

    molist.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...1");

        strFindQuery = "SELECT TBS_NAME, TBS_ID+0 TBS ";
        strFindQuery += "FROM DAY_TBS_INFO ";
        strFindQuery += "WHERE REG_DT > '" + strDate + "' ";
        strFindQuery += "GROUP BY TBS_ID, TBS_NAME ";
        strFindQuery += "ORDER BY TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            molist.Columns.Add(sqlRdr[0].ToString().Trim());
            molist.Columns[molist.Columns.Count - 1].Tag = sqlRdr[1].ToString().Trim();
            molist.Columns[molist.Columns.Count - 1].TextAlign = HorizontalAlignment.Center;
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        string[] sItem = new string[molist.Columns.Count - 13];
        for (int cnt = 0; cnt < molist.Columns.Count - 13; cnt++)
        {
            sItem[cnt] = "-";
        }

        strFindQuery = "SELECT REG_DT, RDBM_AVRG, RDBM_DIFF, RDBM_MAX, RDBM_MIN, ";
        strFindQuery += "SEND_CNT, SEND_AVRG, SEND_SUCC, SEND_FAIL, RECV_CNT, RECV_TBS, HO_CNT ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE REG_DT > '" + strDate + "' AND ";
        strFindQuery += "MODEM_ISSI = '" + modem_list.SelectedItems[0].SubItems[6].Text + "' ";
        strFindQuery += "ORDER BY REG_DT";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            Item.SubItems.Add(sqlRdr[0].ToString().Trim());
            if (sqlRdr[1].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[1].ToString().Trim());
            if (sqlRdr[2].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[2].ToString().Trim());
            if (sqlRdr[3].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[3].ToString().Trim());
            if (sqlRdr[4].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[4].ToString().Trim());
            if (sqlRdr[5].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[5].ToString().Trim());
            if (sqlRdr[6].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[6].ToString().Trim());
            if (sqlRdr[7].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[7].ToString().Trim());
            if (sqlRdr[8].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[8].ToString().Trim());
            if (sqlRdr[9].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[9].ToString().Trim());
            if (sqlRdr[10].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[10].ToString().Trim());
            if (sqlRdr[11].ToString().Trim() == "") Item.SubItems.Add("-");
            else Item.SubItems.Add(sqlRdr[11].ToString().Trim());
            Item.SubItems.AddRange(sItem);

            if (Item.SubItems[2].Text == "-")
            {
                Item.SubItems[2].BackColor = Color.Gray;
            }
            else if (Convert.ToInt32(Item.SubItems[2].Text) < rdbmCritical)
            {
                Item.SubItems[2].BackColor = Color.Red;
            }
            else if (Convert.ToInt32(Item.SubItems[2].Text) < rdbmMajor)
            {
                Item.SubItems[2].BackColor = Color.Orange;
            }
            else if (Convert.ToInt32(Item.SubItems[2].Text) < rdbmMinor)
            {
                Item.SubItems[2].BackColor = Color.Yellow;
            }
            else
            {
                Item.SubItems[2].BackColor = Color.White;
            }

            if (Item.SubItems[3].Text == "-")
            {
                Item.SubItems[3].BackColor = Color.Gray;
            }
            else if (Convert.ToInt32(Item.SubItems[3].Text) < pdbmCritical)
            {
                Item.SubItems[3].BackColor = Color.Red;
            }
            else if (Convert.ToInt32(Item.SubItems[3].Text) < pdbmMajor)
            {
                Item.SubItems[3].BackColor = Color.Orange;
            }
            else if (Convert.ToInt32(Item.SubItems[3].Text) < pdbmMinor)
            {
                Item.SubItems[3].BackColor = Color.Yellow;
            }
            else
            {
                Item.SubItems[3].BackColor = Color.White;
            }

            if (Item.SubItems[7].Text == "-")
            {
                Item.SubItems[7].BackColor = Color.Gray;
            }
            else if (Convert.ToDouble(Item.SubItems[7].Text) < Convert.ToDouble(avrgCritical))
            {
                Item.SubItems[7].BackColor = Color.Red;
            }
            else if (Convert.ToDouble(Item.SubItems[7].Text) < Convert.ToDouble(avrgMajor))
            {
                Item.SubItems[7].BackColor = Color.Orange;
            }
            else if (Convert.ToDouble(Item.SubItems[7].Text) < Convert.ToDouble(avrgMinor))
            {
                Item.SubItems[7].BackColor = Color.Yellow;
            }
            else
            {
                Item.SubItems[7].BackColor = Color.White;
            }

            molist.Items.Add(Item);
        }

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        ListViewItem aItem = new ListViewItem("");
        aItem.UseItemStyleForSubItems = false;
        aItem.SubItems.Add(DateTime.Today.ToString("yyyy-MM-dd"));
        aItem.SubItems.Add("-");
        aItem.SubItems.Add("-");
        aItem.SubItems.Add("-");
        aItem.SubItems.Add("-");
        aItem.SubItems.Add("-");
        aItem.SubItems.Add("-");
        aItem.SubItems.Add("-");
        aItem.SubItems.Add("-");
        aItem.SubItems.Add("-");
        aItem.SubItems.Add("-");
        aItem.SubItems.Add("-");
        aItem.SubItems.AddRange(sItem);

        aItem.SubItems[2].BackColor = Color.Gray;
        aItem.SubItems[3].BackColor = Color.Gray;
        aItem.SubItems[7].BackColor = Color.Gray;

        molist.Items.Add(aItem);

        strFindQuery = "SELECT REG_DT, TBS_ID+0 TBS, RECV_CNT ";
        strFindQuery += "FROM DAY_MODEM_RECV ";
        strFindQuery += "WHERE REG_DT > '" + strDate + "' AND ";
        strFindQuery += "MODEM_ISSI = '" + modem_list.SelectedItems[0].SubItems[6].Text + "' ";
        strFindQuery += "ORDER BY REG_DT, TBS";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem fItem = null;
            if (molist.Items.Count > 0)
            {
                fItem = molist.FindItemWithText(sqlRdr[0].ToString().Trim(), true, 0);
            }

            if (fItem != null)
            {
                for (int cnt = 13; cnt < molist.Columns.Count; cnt++)
                {
                    if (molist.Columns[cnt].Tag.ToString() == sqlRdr[1].ToString().Trim())
                    {
                        fItem.SubItems[cnt].Text = sqlRdr[2].ToString().Trim();
                    }
                    else if (fItem.SubItems[cnt].Text == "-")
                    {
                        fItem.SubItems[cnt].Text = "0";
                    }
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
            Console.WriteLine("데이터베이스 연결 해제...1");
        }
    }

    int avgdbmCnt = 0, avgdbmVal = 0;
    int difdbmCnt = 0, difdbmVal = 0;
    int maxdbmCnt = 0, maxdbmVal = 0;
    int mindbmCnt = 0, mindbmVal = 0;
    int sendCnt = 0, sendVal = 0;
    int avgsendCnt = 0; double avgsendVal = 0.00d;
    int succsendCnt = 0, succsendVal = 0;
    int failsendCnt = 0, failsendVal = 0;
    int recvCnt = 0, recvVal = 0;
    int[] tbsCnt = new int[molist.Columns.Count - 13];
    int[] tbsVal = new int[molist.Columns.Count - 13];

    for (int cnt = 0; cnt < molist.Items.Count; cnt++)
    {
        if (molist.Items[cnt].SubItems[2].Text != "-") { avgdbmVal += Convert.ToInt32(molist.Items[cnt].SubItems[2].Text); avgdbmCnt++; }
        if (molist.Items[cnt].SubItems[3].Text != "-") { difdbmVal += Convert.ToInt32(molist.Items[cnt].SubItems[3].Text); difdbmCnt++; }
        if (molist.Items[cnt].SubItems[4].Text != "-") { maxdbmVal += Convert.ToInt32(molist.Items[cnt].SubItems[4].Text); maxdbmCnt++; }
        if (molist.Items[cnt].SubItems[5].Text != "-") { mindbmVal += Convert.ToInt32(molist.Items[cnt].SubItems[5].Text); mindbmCnt++; }
        if (molist.Items[cnt].SubItems[6].Text != "-") { sendVal += Convert.ToInt32(molist.Items[cnt].SubItems[6].Text); sendCnt++; }
        if (molist.Items[cnt].SubItems[7].Text != "-") { avgsendVal += Convert.ToDouble(molist.Items[cnt].SubItems[7].Text); avgsendCnt++; }
        if (molist.Items[cnt].SubItems[8].Text != "-") { succsendVal += Convert.ToInt32(molist.Items[cnt].SubItems[8].Text); succsendCnt++; }
        if (molist.Items[cnt].SubItems[9].Text != "-") { failsendVal += Convert.ToInt32(molist.Items[cnt].SubItems[9].Text); failsendCnt++; }
        if (molist.Items[cnt].SubItems[10].Text != "-") { recvVal += Convert.ToInt32(molist.Items[cnt].SubItems[10].Text); recvCnt++; }
        for (int idx = 13; idx < molist.Columns.Count; idx++)
        {
            if (molist.Items[cnt].SubItems[idx].Text != "-") { tbsVal[idx - 13] += Convert.ToInt32(molist.Items[cnt].SubItems[idx].Text); tbsCnt[idx - 13]++; }
        }
    }

    ListViewItem tItem = new ListViewItem("");
    tItem.SubItems.Add("합계 / 평균");
    if (avgdbmCnt != 0) tItem.SubItems.Add((avgdbmVal / avgdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (difdbmCnt != 0) tItem.SubItems.Add((difdbmVal / difdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (maxdbmCnt != 0) tItem.SubItems.Add((maxdbmVal / maxdbmCnt).ToString());
    else tItem.SubItems.Add("0");
    if (mindbmCnt != 0) tItem.SubItems.Add((mindbmVal / mindbmCnt).ToString());
    else tItem.SubItems.Add("0");
    tItem.SubItems.Add(sendVal.ToString());
    if (avgsendCnt != 0) tItem.SubItems.Add((succsendVal * 100.00d / sendVal).ToString("N2"));
    else tItem.SubItems.Add("0.00");
    tItem.SubItems.Add(succsendVal.ToString());
    tItem.SubItems.Add(failsendVal.ToString());
    tItem.SubItems.Add(recvVal.ToString());
    tItem.SubItems.Add("-");
    tItem.SubItems.Add("-");
    for (int idx = 13; idx < molist.Columns.Count; idx++)
    {
        tItem.SubItems.Add(tbsVal[idx - 13].ToString());
    }
    tItem.BackColor = Color.Black;
    tItem.ForeColor = Color.White;
    molist.Items.Add(tItem);

    molist.EndUpdate();

    molist.MouseDoubleClick += new System.Windows.Forms.MouseEventHandler(molist_MouseDoubleClick);

    ListView itlist = new ListView();
    itlist.Parent = modem_tab.TabPages[modem_tab.SelectedIndex];
    itlist.Tag = page + " 전력IT 이력";
    itlist.Location = new Point(2, 273);
    itlist.Size = new Size(241, 264);
    itlist.CheckBoxes = true;
    itlist.FullRowSelect = true;
    itlist.GridLines = true;
    itlist.MultiSelect = false;
    itlist.View = View.Details;

    itlist.Columns.Add("", 0);
    itlist.Columns.Add("전력IT 시간", 170, HorizontalAlignment.Center);
    itlist.Columns.Add("감도", 50, HorizontalAlignment.Center);

    itlist.BeginUpdate();

    strDate = DateTime.Today.ToString("yyyy-MM-dd");
    Console.WriteLine("modem_list_MouseDoubleClick = [" + strDate + "]");

    strFindQuery = "";
    sqlCmd = null;
    sqlRdr = null;
    sqlConn = new SqlConnection(itConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...2");

        strFindQuery = "SELECT recv_dtime TIME, ";
        strFindQuery += "recv_sens DBM ";
        strFindQuery += "FROM modem_sens_his ";
        strFindQuery += "WHERE recv_dtime LIKE '" + strDate + "%' AND ";
        strFindQuery += "modem_issi = '" + modem_list.SelectedItems[0].SubItems[6].Text + "' ";
        strFindQuery += "ORDER BY TIME";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            Item.SubItems.Add(sqlRdr[0].ToString().Trim());
            Item.SubItems.Add(sqlRdr[1].ToString().Trim());

            if (Item.SubItems[2].Text == "-")
            {
                Item.SubItems[2].BackColor = Color.Gray;
            }
            else if (Convert.ToInt32(Item.SubItems[2].Text) < rdbmCritical)
            {
                Item.SubItems[2].BackColor = Color.Red;
            }
            else if (Convert.ToInt32(Item.SubItems[2].Text) < rdbmMajor)
            {
                Item.SubItems[2].BackColor = Color.Orange;
            }
            else if (Convert.ToInt32(Item.SubItems[2].Text) < rdbmMinor)
            {
                Item.SubItems[2].BackColor = Color.Yellow;
            }
            else
            {
                Item.SubItems[2].BackColor = Color.White;
            }

            itlist.Items.Add(Item);
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
            Console.WriteLine("데이터베이스 연결 해제...2");
        }
    }

    itlist.EndUpdate();

    ListView nmlist = new ListView();
    nmlist.Parent = modem_tab.TabPages[modem_tab.SelectedIndex];
    nmlist.Tag = page + " NMS 이력";
    nmlist.Location = new Point(248, 273);
    nmlist.Size = new Size(499, 264);
    nmlist.CheckBoxes = true;
    nmlist.FullRowSelect = true;
    nmlist.GridLines = true;
    nmlist.MultiSelect = false;
    nmlist.View = View.Details;

    nmlist.Columns.Add("", 0);
    nmlist.Columns.Add("NMS 시간", 170, HorizontalAlignment.Center);
    nmlist.Columns.Add("DXT 시간", 0, HorizontalAlignment.Center);
    nmlist.Columns.Add("구분", 50, HorizontalAlignment.Center);
    nmlist.Columns.Add("발신", 0, HorizontalAlignment.Center);
    nmlist.Columns.Add("수신", 0, HorizontalAlignment.Center);
    nmlist.Columns.Add("기지국", 85, HorizontalAlignment.Center);
    nmlist.Columns.Add("길이", 50, HorizontalAlignment.Center);
    nmlist.Columns.Add("결과", 123, HorizontalAlignment.Center);

    nmlist.BeginUpdate();

    strDate = DateTime.Today.ToString("yyyy-MM-dd");
    Console.WriteLine("modem_list_MouseDoubleClick = [" + strDate + "]");

    string queryString = "";
    OracleCommand command = null;
    OracleDataReader read = null;
    OracleConnection conn = new OracleConnection(nmsConnStr);

    try
    {
        conn.Open();
        Console.WriteLine("데이터베이스 연결 성공...3");
        
        queryString = "SELECT REG_DT, TIME_STAMP, SERVED_SSI_PART, CALLED_SSI_PART, LOCATION_AREA, SDS_LENGTH, SDS_RESULT ";
        queryString += "FROM T_PM_SDSTO_HIS ";
        queryString += "WHERE REG_DT LIKE '" + strDate + "%' AND ";
        queryString += "(SERVED_SSI_PART = '" + modem_list.SelectedItems[0].SubItems[6].Text + "' OR ";
        queryString += "CALLED_SSI_PART = '" + modem_list.SelectedItems[0].SubItems[6].Text + "') ";
        queryString += "ORDER BY REG_DT";
        /*
        queryString = "SELECT REG_DT, EVENT_TIME, SSI_PART, OTHER_SSI_PART, LOCATION_AREA, SDS_LENGTH, SDS_RESULT ";
        queryString += "FROM T_PM_CONTROL_CHA_HIS ";
        queryString += "WHERE REG_DT LIKE '" + strDate + "%' AND ";
        queryString += "(SSI_PART = '" + modem_list.SelectedItems[0].SubItems[6].Text + "' OR ";
        queryString += "OTHER_SSI_PART = '" + modem_list.SelectedItems[0].SubItems[6].Text + "') ";
        queryString += "ORDER BY REG_DT";
        */
        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        while (read.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            Item.SubItems.Add(read[0].ToString().Trim());
            Item.SubItems.Add(read[1].ToString().Trim());
            if (read[2].ToString().Trim() == modem_list.SelectedItems[0].SubItems[6].Text)
            {
                Item.SubItems.Add("수신");
            }
            else Item.SubItems.Add("발신");
            Item.SubItems.Add(read[2].ToString().Trim());
            Item.SubItems.Add(read[3].ToString().Trim());
            if (read[4].ToString().Trim() == "SERVER")
            {
                Item.SubItems.Add("-");
            }
            else
            {
                int tbs = Convert.ToInt32(read[4].ToString().Trim().Substring(1));
                for (int cnt = 13; cnt < molist.Columns.Count; cnt++)
                {
                    if (molist.Columns[cnt].Tag.ToString() == tbs.ToString())
                    {
                        Item.SubItems.Add(molist.Columns[cnt].Text);
                        break;
                    }
                }
            }
            Item.SubItems.Add(read[5].ToString().Trim());
            Item.SubItems.Add(read[6].ToString().Trim());

            if (Item.SubItems[8].Text == "OK 개별 전달")
            {
                Item.SubItems[8].BackColor = Color.White;
            }
            else
            {
                Item.SubItems[8].BackColor = Color.Red;
            }

            nmlist.Items.Add(Item);
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
    finally
    {
        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }

        if (conn != null)
        {
            conn.Close(); conn = null;
            Console.WriteLine("데이터베이스 연결 해제...3");
        }
    }

    nmlist.EndUpdate();

    ListView[] list = new ListView[] { molist, itlist, nmlist };
    modem_tab.TabPages[modem_tab.TabCount - 1].Tag = list;

    WaitMsg.HideWait(this);
}

private void molist_MouseDoubleClick(object sender, MouseEventArgs e)
{
    ListView[] list = (ListView[])modem_tab.TabPages[modem_tab.SelectedIndex].Tag;
    ListView molist = list[0];
    ListView itlist = list[1];
    ListView nmlist = list[2];

    if (molist.SelectedItems.Count <= 0) return;
    if (molist.SelectedItems[0].SubItems[1].Text == "합계 / 평균") return;

    WaitMsg.ShowWait(this, WaitMsg.waitMsg);

    string issi = modem_tab.TabPages[modem_tab.SelectedIndex].Text.Substring(0, 7);
    string strDate = molist.SelectedItems[0].SubItems[1].Text;
    Console.WriteLine("molist_MouseDoubleClick = [" + issi + "]");
    Console.WriteLine("molist_MouseDoubleClick = [" + strDate + "]");

    itlist.BeginUpdate();

    itlist.Items.Clear();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(itConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...2");

        strFindQuery = "SELECT recv_dtime TIME, ";
        strFindQuery += "recv_sens DBM ";
        strFindQuery += "FROM modem_sens_his ";
        strFindQuery += "WHERE recv_dtime LIKE '" + strDate + "%' AND ";
        strFindQuery += "modem_issi = '" + issi + "' ";
        strFindQuery += "ORDER BY TIME";

        sqlCmd = new SqlCommand(strFindQuery, sqlConn);
        sqlRdr = sqlCmd.ExecuteReader();

        while (sqlRdr.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            Item.SubItems.Add(sqlRdr[0].ToString().Trim());
            Item.SubItems.Add(sqlRdr[1].ToString().Trim());

            if (Item.SubItems[2].Text == "-")
            {
                Item.SubItems[2].BackColor = Color.Gray;
            }
            else if (Convert.ToInt32(Item.SubItems[2].Text) < rdbmCritical)
            {
                Item.SubItems[2].BackColor = Color.Red;
            }
            else if (Convert.ToInt32(Item.SubItems[2].Text) < rdbmMajor)
            {
                Item.SubItems[2].BackColor = Color.Orange;
            }
            else if (Convert.ToInt32(Item.SubItems[2].Text) < rdbmMinor)
            {
                Item.SubItems[2].BackColor = Color.Yellow;
            }
            else
            {
                Item.SubItems[2].BackColor = Color.White;
            }

            itlist.Items.Add(Item);
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
            Console.WriteLine("데이터베이스 연결 해제...2");
        }
    }

    itlist.EndUpdate();

    nmlist.BeginUpdate();

    nmlist.Items.Clear();

    string queryString = "";
    OracleCommand command = null;
    OracleDataReader read = null;
    OracleConnection conn = new OracleConnection(nmsConnStr);

    try
    {
        conn.Open();
        Console.WriteLine("데이터베이스 연결 성공...3");
        
        queryString = "SELECT REG_DT, TIME_STAMP, SERVED_SSI_PART, CALLED_SSI_PART, LOCATION_AREA, SDS_LENGTH, SDS_RESULT ";
        queryString += "FROM T_PM_SDSTO_HIS ";
        queryString += "WHERE REG_DT LIKE '" + strDate + "%' AND ";
        queryString += "(SERVED_SSI_PART = '" + issi + "' OR ";
        queryString += "CALLED_SSI_PART = '" + issi + "') ";
        queryString += "ORDER BY REG_DT";
        /*
        queryString = "SELECT REG_DT, EVENT_TIME, SSI_PART, OTHER_SSI_PART, LOCATION_AREA, SDS_LENGTH, SDS_RESULT ";
        queryString += "FROM T_PM_CONTROL_CHA_HIS ";
        queryString += "WHERE REG_DT LIKE '" + strDate + "%' AND ";
        queryString += "(SSI_PART = '" + issi + "' OR ";
        queryString += "OTHER_SSI_PART = '" + issi + "') ";
        queryString += "ORDER BY REG_DT";
        */
        command = new OracleCommand(queryString, conn);
        read = command.ExecuteReader();

        while (read.Read())
        {
            ListViewItem Item = new ListViewItem("");
            Item.UseItemStyleForSubItems = false;
            Item.SubItems.Add(read[0].ToString().Trim());
            Item.SubItems.Add(read[1].ToString().Trim());
            if (read[2].ToString().Trim() == modem_list.SelectedItems[0].SubItems[6].Text)
            {
                Item.SubItems.Add("수신");
            }
            else Item.SubItems.Add("발신");
            Item.SubItems.Add(read[2].ToString().Trim());
            Item.SubItems.Add(read[3].ToString().Trim());
            if (read[4].ToString().Trim() == "SERVER")
            {
                Item.SubItems.Add("-");
            }
            else
            {
                int tbs = Convert.ToInt32(read[4].ToString().Trim().Substring(1));
                for (int cnt = 13; cnt < molist.Columns.Count; cnt++)
                {
                    if (molist.Columns[cnt].Tag.ToString() == tbs.ToString())
                    {
                        Item.SubItems.Add(molist.Columns[cnt].Text);
                        break;
                    }
                }
            }
            Item.SubItems.Add(read[5].ToString().Trim());
            Item.SubItems.Add(read[6].ToString().Trim());

            if (Item.SubItems[8].Text == "OK 개별 전달")
            {
                Item.SubItems[8].BackColor = Color.White;
            }
            else
            {
                Item.SubItems[8].BackColor = Color.Red;
            }

            nmlist.Items.Add(Item);
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
    finally
    {
        if (read != null && !read.IsClosed)
        {
            read.Close(); read = null;
        }

        if (conn != null)
        {
            conn.Close(); conn = null;
            Console.WriteLine("데이터베이스 연결 해제...3");
        }
    }

    nmlist.EndUpdate();

    WaitMsg.HideWait(this);
}

```

```sql
-----------------------
--data_list_search
-----------------------
SELECT
    REG_DT
FROM
    DAY_MODEM_INFO
GROUP BY
    REG_DT
ORDER BY
    REG_DT DESC
-- 2019-11-24
--
SELECT
    SUBSTRING(REG_DT, 1, 7)
FROM
    DAY_MODEM_INFO
GROUP BY
    SUBSTRING(REG_DT, 1, 7)
ORDER BY
    SUBSTRING(REG_DT, 1, 7) DESC
-- 2019-11
--
SELECT
    SUBSTRING(REG_DT, 1, 4)
FROM
    DAY_MODEM_INFO
GROUP BY
    SUBSTRING(REG_DT, 1, 4)
ORDER BY
    SUBSTRING(REG_DT, 1, 4) DESC
--2019

-----------------------------------
--day_modem_list_search
--day list first item "2019-11-24"
-----------------------------------
SELECT
    TBS_NAME
    , TBS_ID+0 TBS
FROM
    DAY_TBS_INFO
WHERE
    REG_DT = '2019-11-24'
ORDER BY
    TBS

--
SELECT
    GROUP_CODE
    , GROUP_NAME
    , NODE_NAME
    , NODE_ID
    , MODEM_ISSI
    , RDBM_AVRG
    , RDBM_DIFF
    , RDBM_MAX
    , RDBM_MIN
    , SEND_CNT
    , SEND_AVRG
    , SEND_SUCC
    , SEND_FAIL
    , RECV_CNT
    , RECV_TBS
    , HO_CNT
    , SUCC_EXCEPT
FROM
    DAY_MODEM_INFO
WHERE
    REG_DT = '2019-11-24'
    --AND
    --(
    --          GROUP_CODE    = 'BJ'
    --          OR GROUP_CODE = 'TD'
    --)
ORDER BY
    MODEM_ISSI

--
SELECT
    MODEM_ISSI
    , TBS_ID+0 TBS
    , RECV_CNT
FROM
    DAY_MODEM_RECV
WHERE
    REG_DT = '2019-11-24'
ORDER BY
    MODEM_ISSI
    , TBS

-----------------------------------
--day_tbs_list_search
--day list first item "2019-11-24"
-----------------------------------
SELECT
    TBS_ID+0 TBS
    , TBS_NAME
    , MODEM_CNT
    , MODEM_MAX
    , MODEM_TOT
FROM
    DAY_TBS_INFO
WHERE
    REG_DT = '2019-11-24'
ORDER BY
    TBS

--
SELECT
    TBS_ID+0 TBS
    , SUM(TBS_COLL)
FROM
    DAY_TBS_COLL
WHERE
    REG_DT LIKE '2019-11-24%'
GROUP BY
    TBS_ID
ORDER BY
    TBS

--
SELECT
    TBS_ID+0 TBS
    , REG_DT
    , TBS_COLL
FROM
    DAY_TBS_COLL
WHERE
    REG_DT LIKE '2019-11-24%'
ORDER BY
    TBS
    , REG_DT    

--
SELECT
    TBS_ID+0 TBS
    , SUM(TTRX0_JAMM) + SUM(TTRX1_JAMM) + SUM(TTRX2_JAMM)
FROM
    DAY_TBS_JAMM
WHERE
    REG_DT LIKE '2019-11-24%'
GROUP BY
    TBS_ID
ORDER BY
    TBS

--
SELECT
    TBS_ID+0 TBS
    , REG_DT
    , TTRX0_JAMM
    , TTRX1_JAMM
    , TTRX2_JAMM
FROM
    DAY_TBS_JAMM
WHERE
    REG_DT LIKE '2019-11-24%'
ORDER BY
    TBS
    , REG_DT

---------------------------------
--day_group_list_search
---------------------------------
SELECT
    GROUP_CODE      CODE
    , GROUP_ID      ID
    , GROUP_NAME
    , MODEM_CNT
    , SEND_CNT
    , RECV_CNT
FROM
    DAY_GROUP_INFO
WHERE
    REG_DT = '2019-11-24'
    -- AND
    -- (
    --     GROUP_CODE    = 'BJ'
    --     OR GROUP_CODE = 'TD'
    -- )
ORDER BY
    CODE
    , ID
-----------------------------------
--month_modem_list_search
-----------------------------------
SELECT
    TBS_NAME
    , TBS_ID+0 TBS
FROM
    DAY_TBS_INFO
WHERE
    REG_DT LIKE '2019-11%'
GROUP BY
    TBS_NAME
    , TBS_ID
ORDER BY
    TBS

--
SELECT
    GROUP_CODE
    , GROUP_NAME
    , NODE_NAME
    , NODE_ID
    , MODEM_ISSI
    , SUM(SEND_CNT)+SUM(RECV_CNT) CNT
FROM
    DAY_MODEM_INFO
WHERE
    REG_DT LIKE '2019-11%'
    -- AND
    -- (
    --     GROUP_CODE    = 'BJ'
    --     OR GROUP_CODE = 'TD'
    -- )
GROUP BY
    GROUP_CODE
    , GROUP_NAME
    , NODE_NAME
    , NODE_ID
    , MODEM_ISSI
ORDER BY
    MODEM_ISSI
    , CNT DESC

--
SELECT
    MODEM_ISSI
    , AVG(RDBM_AVRG)
    , MIN(RDBM_MIN)-MAX(RDBM_MAX)
    , MAX(RDBM_MAX)
    , MIN(RDBM_MIN)
    , SUM(SEND_CNT)
    , ROUND(AVG(SEND_AVRG), 2)
    , SUM(SEND_SUCC)
    , SUM(SEND_FAIL)
    , SUM(RECV_CNT)
FROM
    DAY_MODEM_INFO
WHERE
    REG_DT LIKE '2019-11%'
    -- AND
    -- (
    --     GROUP_CODE    = 'BJ'
    --     OR GROUP_CODE = 'TD'
    -- )
    AND SUCC_EXCEPT IS NULL
GROUP BY
          MODEM_ISSI
ORDER BY
          MODEM_ISSI

--
SELECT
    MODEM_ISSI
    , TBS_ID+0 TBS
    , SUM(RECV_CNT)
FROM
    DAY_MODEM_RECV
WHERE
    REG_DT LIKE '2019-11%'
GROUP BY
    MODEM_ISSI
    , TBS_ID
ORDER BY
    MODEM_ISSI
    , TBS

---------------------------
--month_tbs_list_search
---------------------------
SELECT
    TBS_ID+0 TBS
    , TBS_NAME
    , AVG(MODEM_CNT)
    , MAX(MODEM_MAX)
    , AVG(MODEM_TOT)
FROM
    DAY_TBS_INFO
WHERE
    REG_DT LIKE '2019-11%'
GROUP BY
    TBS_ID
    , TBS_NAME
ORDER BY
    TBS

--
SELECT
    TBS_ID+0 TBS
    , SUM(TBS_COLL)
FROM
    DAY_TBS_COLL
WHERE
    REG_DT LIKE '2019-11%'
GROUP BY
    TBS_ID
ORDER BY
    TBS

--
SELECT
    TBS_ID+0 TBS
    , SUBSTRING(REG_DT, 1, 10)
    , SUM(TBS_COLL)
FROM
    DAY_TBS_COLL
WHERE
    REG_DT LIKE '2019-11%'
GROUP BY
    TBS_ID
    , SUBSTRING(REG_DT, 1, 10)
ORDER BY
    TBS
    , SUBSTRING(REG_DT, 1, 10)

--
SELECT
    TBS_ID+0 TBS
    , SUM(TTRX0_JAMM) + SUM(TTRX1_JAMM) + SUM(TTRX2_JAMM)
FROM
    DAY_TBS_JAMM
WHERE
    REG_DT LIKE '2019-11%'
GROUP BY
    TBS_ID
ORDER BY
    TBS

--
SELECT
    TBS_ID+0 TBS
    , SUBSTRING(REG_DT, 1, 10)
    , SUM(TTRX0_JAMM)
    , SUM(TTRX1_JAMM)
    , SUM(TTRX2_JAMM)
FROM
    DAY_TBS_JAMM
WHERE
    REG_DT LIKE '2019-11%'
GROUP BY
    TBS_ID
    , SUBSTRING(REG_DT, 1, 10)
ORDER BY
    TBS
    , SUBSTRING(REG_DT, 1, 10)


--------------------------------------
--month_group_list_search
--------------------------------------
SELECT
    GROUP_CODE CODE
    , GROUP_ID   ID
    , GROUP_NAME
    , AVG(MODEM_CNT)
    , AVG(SEND_CNT)
    , AVG(RECV_CNT)
FROM
    DAY_GROUP_INFO
WHERE
    REG_DT LIKE '2019-11%'
    -- AND
    -- (
    --     GROUP_CODE    = 'BJ'
    --     OR GROUP_CODE = 'TD'
    -- )
GROUP BY
    GROUP_CODE
    , GROUP_ID
    , GROUP_NAME
ORDER BY
    CODE
    , ID

-------------------------------------------
--year_modem_list_search
-------------------------------------------
SELECT
    TBS_NAME
    , TBS_ID+0 TBS
FROM
    DAY_TBS_INFO
WHERE
    REG_DT LIKE '2019%'
GROUP BY
    TBS_NAME
    , TBS_ID
ORDER BY
    TBS

--
SELECT
    GROUP_CODE
    , GROUP_NAME
    , NODE_NAME
    , NODE_ID
    , MODEM_ISSI
    , SUM(SEND_CNT)+SUM(RECV_CNT) CNT
FROM
    DAY_MODEM_INFO
WHERE
    REG_DT LIKE '2019%'
    -- AND
    -- (
    --     GROUP_CODE    = 'BJ'
    --     OR GROUP_CODE = 'TD'
    -- )
GROUP BY
    GROUP_CODE
    , GROUP_NAME
    , NODE_NAME
    , NODE_ID
    , MODEM_ISSI
ORDER BY
    MODEM_ISSI
    , CNT DESC

--
SELECT
    MODEM_ISSI
    , AVG(RDBM_AVRG)
    , MIN(RDBM_MIN)-MAX(RDBM_MAX)
    , MAX(RDBM_MAX)
    , MIN(RDBM_MIN)
    , SUM(SEND_CNT)
    , ROUND(AVG(SEND_AVRG), 2)
    , SUM(SEND_SUCC)
    , SUM(SEND_FAIL)
    , SUM(RECV_CNT)
FROM
    DAY_MODEM_INFO
WHERE
    REG_DT LIKE '2019%'
    -- AND
    -- (
    --     GROUP_CODE    = 'BJ'
    --     OR GROUP_CODE = 'TD'
    -- )
    AND SUCC_EXCEPT IS NULL
GROUP BY
    MODEM_ISSI
ORDER BY
    MODEM_ISSI

--
SELECT
    MODEM_ISSI
    , TBS_ID+0 TBS
    , SUM(RECV_CNT)
FROM
    DAY_MODEM_RECV
WHERE
    REG_DT LIKE '2019%'
GROUP BY
    MODEM_ISSI
    , TBS_ID
ORDER BY
    MODEM_ISSI
    , TBS

--------------------------------------
--year_tbs_list_search
--------------------------------------
SELECT
    TBS_ID+0 TBS
    , TBS_NAME
    , AVG(MODEM_CNT) 
    , MAX(MODEM_MAX) 
    , AVG(MODEM_TOT) 
FROM
    DAY_TBS_INFO
WHERE
    REG_DT LIKE '2019%'
GROUP BY
    TBS_ID
    , TBS_NAME
ORDER BY
    TBS

--
SELECT
    TBS_ID+0 TBS
    , SUM(TBS_COLL)
FROM
    DAY_TBS_COLL
WHERE
    REG_DT LIKE '2019%'
GROUP BY
    TBS_ID
ORDER BY
    TBS
		  
--
SELECT
    TBS_ID+0 TBS
    , SUBSTRING(REG_DT, 1, 7)
    , SUM(TBS_COLL)
FROM
    DAY_TBS_COLL
WHERE
    REG_DT LIKE '2019%'
GROUP BY
    TBS_ID
    , SUBSTRING(REG_DT, 1, 7)
ORDER BY
    TBS
    , SUBSTRING(REG_DT, 1, 7)

--
SELECT
    TBS_ID+0 TBS
    , SUM(TTRX0_JAMM) + SUM(TTRX1_JAMM) + SUM(TTRX2_JAMM)
FROM
    DAY_TBS_JAMM
WHERE
    REG_DT LIKE '2019%'
GROUP BY
    TBS_ID
ORDER BY
    TBS

--
SELECT
    TBS_ID+0 TBS
    , SUBSTRING(REG_DT, 1, 7)
    , SUM(TTRX0_JAMM)
    , SUM(TTRX1_JAMM)
    , SUM(TTRX2_JAMM)
FROM
    DAY_TBS_JAMM
WHERE
    REG_DT LIKE '2019%'
GROUP BY
    TBS_ID
    , SUBSTRING(REG_DT, 1, 7)
ORDER BY
    TBS
    , SUBSTRING(REG_DT, 1, 7)

--------------------------------------------------
--year_group_list_search
--------------------------------------------------
SELECT
    GROUP_CODE CODE
    , GROUP_ID   ID
    , GROUP_NAME
    , AVG(MODEM_CNT)
    , AVG(SEND_CNT)
    , AVG(RECV_CNT)
FROM
    DAY_GROUP_INFO
WHERE
    REG_DT LIKE '2019%'
    AND
    (
        GROUP_CODE    = 'BJ'
        OR GROUP_CODE = 'TD'
    )
GROUP BY
    GROUP_CODE
    , GROUP_ID
    , GROUP_NAME
ORDER BY
    CODE
    , ID

-----------------------------------------
-- modem_list_search
-- pick day ex)2019-11-24
--SITE_ISSI1 = 1931100
--SITE_ISSI2 = 1931200
--SITE_ISSI3 = 1111111
-----------------------------------------
--db1 nms
SELECT
    TBS_NM
    , REPLACE(TBS_ID, 'TBS-', '')+0 TBS_ORDER
FROM
    T_CM_TBS_MNG
ORDER BY
    TBS_ORDER

--trs
SELECT
    mo.system_code
    , gr.group_name
    , mo.modem_name
    , mo.frtu_addr
    , mo.modem_issi
FROM
    modem_info AS mo
    CROSS JOIN
    group_info AS gr
WHERE
    mo.use_yn       = 'Y'
    AND mo.group_id = gr.group_id
ORDER BY
    mo.modem_issi

--trs
SELECT
    modem_issi
    , AVG(CONVERT(INT, recv_sens))
    , MIN(CONVERT(INT, recv_sens)) - MAX(CONVERT(INT, recv_sens))
    , MAX(CONVERT(INT, recv_sens))
    , MIN(CONVERT(INT, recv_sens))
FROM
    modem_sens_his
WHERE
    recv_dtime LIKE '2019-11-24%'
GROUP BY
    modem_issi
    , SUBSTRING(recv_dtime, 1, 10)
ORDER BY
    modem_issi

--nms
SELECT
    CALLED_SSI_PART                                                           ISSI
    , COUNT(*)                                                                  TOTAL
    , ROUND(SUM(DECODE(TRIM(SDS_RESULT), 'OK 개별 전달', 1,0))/COUNT(*)*100, 2) AVR
    , SUM(DECODE(TRIM(SDS_RESULT), 'OK 개별 전달', 1,0))                        SUCC
    , SUM(DECODE(TRIM(SDS_RESULT), 'OK 개별 전달', 0,1))                        FAIL
FROM
    T_PM_SDSTO_HIS
WHERE
    REG_DT LIKE '2019-11-24%'
    AND
    (
        SERVED_SSI_PART    = '1931100'
        OR SERVED_SSI_PART = '1931200'
        OR SERVED_SSI_PART = '1111111'
    )
GROUP BY
    CALLED_SSI_PART
ORDER BY
    ISSI

--nms
    queryString = "SELECT SERVED_SSI_PART ISSI, COUNT(*) TOTAL, ";
    for (int cnt = 18; cnt < modem_list.Columns.Count; cnt++)
    {
    queryString += "SUM(DECODE(SUBSTR(TRIM(LOCATION_AREA), 2, 2)+0, " + modem_list.Columns[cnt].Tag.ToString() + ", 1, 0)) TBS" + modem_list.Columns[cnt].Tag.ToString();
        if (cnt != modem_list.Columns.Count - 1) queryString += ", ";
        else queryString += " ";
    }
    queryString += "FROM T_PM_SDSTO_HIS ";
    queryString += "WHERE REG_DT LIKE '" + strDate + "%' AND (CALLED_SSI_PART = '" + siteISSI1 + "' OR CALLED_SSI_PART = '" + siteISSI2 + "'OR CALLED_SSI_PART = '" + siteISSI3 + "') AND LOCATION_AREA != 'SERVER' ";
    queryString += "GROUP BY SERVED_SSI_PART ";
    queryString += "ORDER BY ISSI";

------------------------------------------
-- tbs_list_search
-- pick day ex)2019-11-24
------------------------------------------
-- nms
SELECT
    REPLACE(TBS_ID, 'TBS-', '')+0 TBS_ORDER
    , TBS_NM
FROM
    T_CM_TBS_MNG
ORDER BY
    TBS_ORDER

-- nms
SELECT
    SUBSTR(TRIM(rt.LOCATION_AREA), 2, 2)+0
    , COUNT(rt.SERVED_SSI_PART)
FROM
    (
        SELECT
            LOCATION_AREA
            , SERVED_SSI_PART
        FROM
            TRS_NMS.T_PM_SDSTO_HIS
        WHERE
            REG_DT          
                LIKE '2019-11-24%'
                AND LOCATION_AREA != 'SERVER'
            GROUP BY
                LOCATION_AREA
                , SERVED_SSI_PART
    ) rt
GROUP BY
    rt.LOCATION_AREA
ORDER BY
    rt.LOCATION_AREA

-- nms
SELECT
    REPLACE(TBS_ID, 'TBS-', '')+0 TBS
    , SUM(RANDOM_ACCESS)            RC
FROM
    T_PM_TBS_RDO_NET
WHERE
    TO_CHAR(PERIOD_START,'yyyy-mm-dd') = '2019-11-24'
GROUP BY
    TBS_ID
ORDER BY
    TBS

--nms
SELECT
    REPLACE(TBS_ID, 'TBS-', '')+0           TBS
    , TO_CHAR(PERIOD_START,'yyyy-mm-dd HH24') PER
    , SUM(RANDOM_ACCESS)                      RC
FROM
    T_PM_TBS_RDO_NET
WHERE
    TO_CHAR(PERIOD_START,'yyyy-mm-dd') = '2019-11-24'
GROUP BY
    TBS_ID
    , TO_CHAR(PERIOD_START,'yyyy-mm-dd HH24')
ORDER BY
    TBS
    , PER

-- nms
SELECT
    REPLACE(DEVICE_ID, 'TBS-', '')+0 TBS
    , COUNT(*)                         RC
FROM
    T_FM_FAULT_HIS
WHERE
    ALARM_NUM                 = '6851'
    AND SUBSTR(REG_DT, 0, 10) = '2019-11-24'
GROUP BY
    DEVICE_ID
ORDER BY
    TBS

-- nms
SELECT
    REPLACE(DEVICE_ID, 'TBS-', '')+0          TBS
    , REPLACE(SUBSTR(REG_DT, 0, 14), '  ', ' ') PER
    , ALARM_OBJ                                 OBJ
    , COUNT(*)                                  RC
FROM
    T_FM_FAULT_HIS
WHERE
    ALARM_NUM                 = '6851'
    AND SUBSTR(REG_DT, 0, 10) = '2019-11-24'
GROUP BY
    DEVICE_ID
    , REPLACE(SUBSTR(REG_DT, 0, 14), '  ', ' ')
    , ALARM_OBJ
ORDER BY
    TBS
    , PER
    , OBJ

--
------------------------------------------
-- group_list_serch
------------------------------------------
-- trs
SELECT
    mo.system_code
    , mo.group_id
    , gr.group_name
    , COUNT(*)
FROM
    modem_info AS mo
    CROSS JOIN
    group_info AS gr
WHERE
    mo.use_yn       = 'Y'
    AND mo.group_id = gr.group_id
GROUP BY
    mo.system_code
    , mo.group_id
    , gr.group_name
ORDER BY
    mo.group_id

-- trs
SELECT
    id
    , COUNT(*)
FROM
(
    SELECT
        group_id   id
        , modem_issi issi
    FROM
        modem_data_his
    WHERE
        his_save_dtime LIKE '2019-11-24%'
        AND send_recv_yn  = 'S'
    GROUP BY
        group_id
        , modem_issi
) AS RESULT
GROUP BY
    id
ORDER BY
    id

-- trs
SELECT
    id
    , COUNT(*)
FROM
(
    SELECT
        group_id   id
        , modem_issi issi
    FROM
        modem_data_his
    WHERE
        his_save_dtime LIKE '2019-11-24%'
        AND send_recv_yn  = 'R'
    GROUP BY
        group_id
        , modem_issi
) AS RESULT
GROUP BY
    id
ORDER BY
    id

-----------------------------
--alarm_search
--IEMS_CODE1 = 100
-- IEMS_CODE2 = 199
-----------------------------
-- nms
SELECT
    URGENCY_LVL
    , SUBSTR(REPLACE(REG_DT, '  ', ' '), 1, 19)
    , '-'
    , DEVICE_TYPE
    , DEVICE_ID
    , ALARM_EQ_TYPE
    , ALARM_OBJ
    , ALARM_NUM
    , TEXT
FROM
    T_FM_FAULT_MON
WHERE
    REG_DT LIKE '2019-11-24%'
ORDER BY
    REG_DT

-- nms 

SELECT
    URGENCY_LVL
    , SUBSTR(REPLACE(REG_DT, '  ', ' '), 1, 19)
    , SUBSTR(REPLACE(RECOV_DT, '  ', ' '), 1, 19)
    , DEVICE_TYPE
    , DEVICE_ID
    , ALARM_EQ_TYPE
    , ALARM_OBJ
    , ALARM_NUM
    , TEXT
FROM
    T_FM_FAULT_HIS
WHERE
    REG_DT         LIKE '2019-11-24%'
    AND PRINTOUT_TYPE = 'ALARM'
ORDER BY
    REG_DT

-- IEMS
SELECT
    PFL.EVENT_CLASS
    , SUBSTR(ALM.START_DATE, 1, 19)
    , SUBSTR(ALM.SOLVE_DATE, 1, 19)
    , OFC.OFFICE_NAME
    , SYS.SYSTEM_NAME
    , SVR.SERVER_NAME
    , PFL.PROFILE_EVENT_ALARM_NAME
FROM
    LOG_EVENT_ALARM ALM
    CROSS JOIN
    SERVER_INFO SVR
    CROSS JOIN
    SYSTEM_INFO SYS
    CROSS JOIN
    OFFICE OFC
    CROSS JOIN
    PROFILE_EVENT_ALARM PFL
WHERE
    -- SYS.OFFICE_ID                 >= 100
    -- AND SYS.OFFICE_ID             <= 199
    -- AND 
    ALM.START_DATE                 LIKE '2019-11-24%'
    AND ALM.SERVER_ID              = SVR.SERVER_ID
    AND SVR.SYSTEM_ID              = SYS.SYSTEM_ID
    AND SYS.OFFICE_ID              = OFC.OFFICE_ID
    AND ALM.PROFILE_EVENT_ALARM_ID = PFL.PROFILE_EVENT_ALARM_ID
ORDER BY
    ALM.START_DATE DESC

----------------------------------------
-- db_save(
----------------------------------------

----------------------------------------
--db_deleteBtn_Click
----------------------------------------

----------------------------------------
--modem_list_MouseDoubleClick
--modemlist double click - make new tab 
-- modemlist coluimn : "" 구분 CD 그룹 개소명 ID ISSI 평균 편차 최대 최소 발신 성공률 성공 실패 수신 기지국 HO
-- new tab name : 6(4)
-- start day = tosay-365
----------------------------------------
--cms
SELECT
          TBS_NAME
        , TBS_ID+0 TBS
FROM
          DAY_TBS_INFO
WHERE
          REG_DT > '2018-11-24'
GROUP BY
          TBS_ID
        , TBS_NAME
ORDER BY
          TBS

--cms
SELECT
    REG_DT
    , RDBM_AVRG
    , RDBM_DIFF
    , RDBM_MAX
    , RDBM_MIN
    , SEND_CNT
    , SEND_AVRG
    , SEND_SUCC
    , SEND_FAIL
    , RECV_CNT
    , RECV_TBS
    , HO_CNT
FROM
    DAY_MODEM_INFO
WHERE
    REG_DT         > '2019-11-24'
    AND MODEM_ISSI = '1234567'
ORDER BY
    REG_DT

--cms
SELECT
    REG_DT
    , TBS_ID+0 TBS
    , RECV_CNT
FROM
    DAY_MODEM_RECV
WHERE
    REG_DT         > '2019-11-24'
    AND MODEM_ISSI = '1234567'
ORDER BY
    REG_DT
    , TBS

--it
SELECT
    recv_dtime TIME
    , recv_sens  DBM
FROM
    modem_sens_his
WHERE
    recv_dtime  LIKE '2019-11-24%'
    AND modem_issi = '1234567'
ORDER BY
    TIME

--nms
SELECT
    REG_DT
    , TIME_STAMP
    , SERVED_SSI_PART
    , CALLED_SSI_PART
    , LOCATION_AREA
    , SDS_LENGTH
    , SDS_RESULT
FROM
    T_PM_SDSTO_HIS
WHERE
    REG_DT LIKE '2019-11-24%'
    AND
    (
        SERVED_SSI_PART    = '1234567'
        OR CALLED_SSI_PART = '1234567'
    )
ORDER BY
    REG_DT
----------------------------------------
--molist_MouseDoubleClick
--molist : "" 일자 평균 편차 최대 최소 발신 성공률 성공 실패 수신 기지국 HO
----------------------------------------
--it
SELECT
    recv_dtime TIME
    , recv_sens  DBM
FROM
    modem_sens_his
WHERE
    recv_dtime  LIKE '2019-11-24%'
    AND modem_issi = '1234567'
ORDER BY
    TIME

--nms
SELECT
    REG_DT
    , TIME_STAMP
    , SERVED_SSI_PART
    , CALLED_SSI_PART
    , LOCATION_AREA
    , SDS_LENGTH
    , SDS_RESULT
FROM
    T_PM_SDSTO_HIS
WHERE
    REG_DT LIKE '2019-11-24%'
    AND
    (
        SERVED_SSI_PART    = '1234567'
        OR CALLED_SSI_PART = '1234567'
    )
ORDER BY
    REG_DT































```
