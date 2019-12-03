- HistorySearchForm
- MainForm - 조치 이력 관리 -> HistorySearchForm

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
        string reg_dt = sqlRdr[0].ToString().Trim();

        if (sqlRdr != null && !sqlRdr.IsClosed)
        {
            sqlRdr.Close(); sqlRdr = null;
        }

        searchDate.Value = DateTime.Parse(reg_dt);

        strFindQuery = "SELECT GROUP_NAME, NODE_NAME, MODEM_ISSI ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE REG_DT = '" + reg_dt + "' "; ;
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


private void insertBtn_Click(object sender, EventArgs e)
{
    if (modemISSI.Text == "전체" || modemISSI.Text == "") return;

    if (insertBtn.Text == "등록")
    {
        measureDate.Enabled = true;
        hourTime.Enabled = true;
        minuteTime.Enabled = true;
        measureText.Enabled = true;
        exceptOn.Enabled = true;
        exceptOff.Enabled = true;

        measureDate.Value = DateTime.Today;
        hourTime.SelectedIndex = DateTime.Now.Hour;
        minuteTime.SelectedIndex = DateTime.Now.Minute;
        measureText.Text = "";
        exceptOn.Checked = false;
        exceptOff.Checked = false;

        updateBtn.Enabled = false;
        deleteBtn.Enabled = false;

        insertBtn.Text = "저장";
    }
    else if (insertBtn.Text == "저장")
    {
        measureDate.Enabled = false;
        hourTime.Enabled = false;
        minuteTime.Enabled = false;
        measureText.Enabled = false;
        exceptOn.Enabled = false;
        exceptOff.Enabled = false;

        updateBtn.Enabled = true;
        deleteBtn.Enabled = true;

        insertBtn.Text = "등록";

        if (measureText.Text == "") return;

        string strSaveQuery = "";
        string strFindQuery = "";
        SqlCommand sqlCmd = null;
        SqlDataReader sqlRdr = null;
        SqlConnection sqlConn = new SqlConnection(fMain.cmsConnStr);

        try
        {
            sqlConn.Open();
            Console.WriteLine("데이터베이스[TRS-CMS] 연결 성공...");

            strSaveQuery = "INSERT INTO MODEM_HISTORY(REG_DT, MODEM_ISSI, GROUP_NAME, NODE_NAME, TEXT, SUCC_EXCEPT) VALUES(";
            strSaveQuery += "'" + measureDate.Value.ToString("yyyy-MM-dd ") + hourTime.Text + ":" + minuteTime.Text + "', ";
            strSaveQuery += "'" + modemISSI.Text + "', ";
            strSaveQuery += "'" + groupName.Text + "', ";
            strSaveQuery += "'" + nodeName.Text + "', ";
            strSaveQuery += "'" + measureText.Text + "', ";
            if (exceptOn.Checked) strSaveQuery += "'1')";
            else if (exceptOff.Checked) strSaveQuery += "'0')";
            else strSaveQuery += "NULL)";

            sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
            sqlCmd.ExecuteReader().Close();

            if (exceptOn.Checked || exceptOff.Checked)
            {
                string afterRegdt = "";
                string afterExcept = "";

                strFindQuery = "SELECT TOP(1) SUBSTRING(REG_DT, 1, 10), SUCC_EXCEPT ";
                strFindQuery += "FROM MODEM_HISTORY ";
                strFindQuery += "WHERE REG_DT > '" + measureDate.Value.ToString("yyyy-MM-dd ") + hourTime.Text + ":" + minuteTime.Text + "' AND ";
                strFindQuery += "SUCC_EXCEPT IS NOT NULL AND ";
                strFindQuery += "MODEM_ISSI = '" + modemISSI.Text + "' ";
                strFindQuery += "ORDER BY REG_DT";

                sqlCmd = new SqlCommand(strFindQuery, sqlConn);
                sqlRdr = sqlCmd.ExecuteReader();

                while (sqlRdr.Read())
                {
                    afterRegdt = sqlRdr[0].ToString().Trim();
                    afterExcept = sqlRdr[1].ToString().Trim();
                }
                Console.WriteLine("after = " + afterRegdt + " = " + afterExcept);

                if (sqlRdr != null && !sqlRdr.IsClosed)
                {
                    sqlRdr.Close(); sqlRdr = null;
                }

                strSaveQuery = "UPDATE DAY_MODEM_INFO SET ";
                if (exceptOn.Checked) strSaveQuery += "SUCC_EXCEPT = '1' ";
                else if (exceptOff.Checked) strSaveQuery += "SUCC_EXCEPT = NULL ";
                strSaveQuery += "WHERE ";
                strSaveQuery += "REG_DT >= '" + measureDate.Value.ToString("yyyy-MM-dd") + "' AND ";
                if (afterRegdt != "")
                {
                    strSaveQuery += "REG_DT < '" + afterRegdt + "' AND ";
                }
                strSaveQuery += "MODEM_ISSI = '" + modemISSI.Text + "'";

                sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
                sqlCmd.ExecuteReader().Close();
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

        selectModemHistory(modemISSI.Text);
    }
}


private void selectModemHistory(string issi)
{
    measure_list.Items.Clear();
    measure_list.BeginUpdate();

    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(fMain.cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스 연결 성공...");

        strFindQuery = "SELECT REG_DT, GROUP_NAME, NODE_NAME, MODEM_ISSI, TEXT, SUCC_EXCEPT ";
        strFindQuery += "FROM MODEM_HISTORY ";
        if (issi != "전체")
        {
            strFindQuery += "WHERE MODEM_ISSI = '" + issi + "' "; ;
        }
        strFindQuery += "ORDER BY REG_DT DESC";

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
            if (sqlRdr[5].ToString().Trim() == "1") Item.SubItems.Add("설정");
            else if (sqlRdr[5].ToString().Trim() == "0") Item.SubItems.Add("해제");
            else Item.SubItems.Add("");

            measure_list.Items.Add(Item);
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

    measure_list.EndUpdate();
}

private void updateBtn_Click(object sender, EventArgs e)
{
    if (modemISSI.Text == "전체" || modemISSI.Text == "" || measureText.Text == "") return;

    if (updateBtn.Text == "수정")
    {
        measureText.Enabled = true;
        //exceptOn.Enabled = true;
        //exceptOff.Enabled = true;

        insertBtn.Enabled = false;
        deleteBtn.Enabled = false;

        updateBtn.Text = "저장";
    }
    else if (updateBtn.Text == "저장")
    {
        measureText.Enabled = false;
        //exceptOn.Enabled = false;
        //exceptOff.Enabled = false;

        insertBtn.Enabled = true;
        deleteBtn.Enabled = true;

        updateBtn.Text = "수정";

        string strSaveQuery = "";
        SqlCommand sqlCmd = null;
        SqlConnection sqlConn = new SqlConnection(fMain.cmsConnStr);

        try
        {
            sqlConn.Open();
            Console.WriteLine("데이터베이스[TRS-CMS] 연결 성공...");

            strSaveQuery = "UPDATE MODEM_HISTORY SET TEXT = ";
            strSaveQuery += "'" + measureText.Text + "' ";
            strSaveQuery += "WHERE ";
            strSaveQuery += "REG_DT = '" + measureDate.Value.ToString("yyyy-MM-dd ") + hourTime.Text + ":" + minuteTime.Text + "' AND ";
            strSaveQuery += "MODEM_ISSI = '" + modemISSI.Text + "'";

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

        selectModemHistory(modemISSI.Text);
    }
}

private void deleteBtn_Click(object sender, EventArgs e)
{
    if (modemISSI.Text == "전체" || modemISSI.Text == "" || measureText.Text == "") return;

    insertBtn.Enabled = true;
    updateBtn.Enabled = false;
    deleteBtn.Enabled = false;

    string strSaveQuery = "";
    string strFindQuery = "";
    SqlCommand sqlCmd = null;
    SqlDataReader sqlRdr = null;
    SqlConnection sqlConn = new SqlConnection(fMain.cmsConnStr);

    try
    {
        sqlConn.Open();
        Console.WriteLine("데이터베이스[TRS-CMS] 연결 성공...");

        strSaveQuery = "DELETE FROM MODEM_HISTORY ";
        strSaveQuery += "WHERE REG_DT = '" + measureDate.Value.ToString("yyyy-MM-dd ") + hourTime.Text + ":" + minuteTime.Text + "' AND ";
        strSaveQuery += "MODEM_ISSI = '" + modemISSI.Text + "'";

        Console.WriteLine(strSaveQuery);

        sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
        sqlCmd.ExecuteReader().Close();

        if (exceptOn.Checked || exceptOff.Checked)
        {
            string beforeRegdt = "";
            string beforeExcept = "";
            string afterRegdt = "";
            string afterExcept = "";

            strFindQuery = "SELECT TOP(1) SUBSTRING(REG_DT, 1, 10), SUCC_EXCEPT ";
            strFindQuery += "FROM MODEM_HISTORY ";
            strFindQuery += "WHERE REG_DT < '" + measureDate.Value.ToString("yyyy-MM-dd ") + hourTime.Text + ":" + minuteTime.Text + "' AND ";
            strFindQuery += "SUCC_EXCEPT IS NOT NULL AND ";
            strFindQuery += "MODEM_ISSI = '" + modemISSI.Text + "' ";
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

            strFindQuery = "SELECT TOP(1) SUBSTRING(REG_DT, 1, 10), SUCC_EXCEPT ";
            strFindQuery += "FROM MODEM_HISTORY ";
            strFindQuery += "WHERE REG_DT > '" + measureDate.Value.ToString("yyyy-MM-dd ") + hourTime.Text + ":" + minuteTime.Text + "' AND ";
            strFindQuery += "SUCC_EXCEPT IS NOT NULL AND ";
            strFindQuery += "MODEM_ISSI = '" + modemISSI.Text + "' ";
            strFindQuery += "ORDER BY REG_DT";

            sqlCmd = new SqlCommand(strFindQuery, sqlConn);
            sqlRdr = sqlCmd.ExecuteReader();

            while (sqlRdr.Read())
            {
                afterRegdt = sqlRdr[0].ToString().Trim();
                afterExcept = sqlRdr[1].ToString().Trim();
            }
            Console.WriteLine("after = " + afterRegdt + " = " + afterExcept);

            if (sqlRdr != null && !sqlRdr.IsClosed)
            {
                sqlRdr.Close(); sqlRdr = null;
            }

            strSaveQuery = "UPDATE DAY_MODEM_INFO SET ";
            if (beforeExcept == "1")
            {
                strSaveQuery += "SUCC_EXCEPT = '1' ";
            }
            else if (beforeExcept == "0" || beforeExcept == "")
            {
                strSaveQuery += "SUCC_EXCEPT = NULL ";
            }
            strSaveQuery += "WHERE ";
            strSaveQuery += "REG_DT >= '" + measureDate.Value.ToString("yyyy-MM-dd") + "' AND ";
            if (afterRegdt != "")
            {
                strSaveQuery += "REG_DT < '" + afterRegdt + "' AND ";
            }
            strSaveQuery += "MODEM_ISSI = '" + modemISSI.Text + "'";

            sqlCmd = new SqlCommand(strSaveQuery, sqlConn);
            sqlCmd.ExecuteReader().Close();
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

    selectModemHistory(modemISSI.Text);
}

private void modemSearchBtn_Click(object sender, EventArgs e)
{
    modem_columnsorter.previousColumn = -1;
    modem_flag = 0;
    modem_list.ListViewItemSorter = null;

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

        strFindQuery = "SELECT GROUP_NAME, NODE_NAME, MODEM_ISSI ";
        strFindQuery += "FROM DAY_MODEM_INFO ";
        strFindQuery += "WHERE REG_DT = '" + searchDate.Value.ToString("yyyy-MM-dd") + "' "; ;
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

```sql
------------------------------------------
-- modem_list_search
-- search_comboBox.SelectedIndex == 0
------------------------------------------

SELECT MAX(REG_DT) 
FROM DAY_MODEM_INFO

-- regdt = 2019-11-24
SELECT
    GROUP_NAME
    , NODE_NAME
    , MODEM_ISSI
FROM
    DAY_MODEM_INFO
WHERE
    REG_DT = '2019-11-24'
ORDER BY
    MODEM_ISSI

--------------------------------------------
--insertBtn_Click

--------------------------------------------

--------------------------------------------
--selectModemHistory(string issi)
--------------------------------------------
SELECT
    REG_DT
    , GROUP_NAME
    , NODE_NAME
    , MODEM_ISSI
    , TEXT
    , SUCC_EXCEPT
FROM
          MODEM_HISTORY
-- WHERE
--           MODEM_ISSI = '1234567'
ORDER BY
          REG_DT DESC

-------------------------------------------
--updateBtn_Click
-------------------------------------------

-------------------------------------------
--deleteBtn_Click
-------------------------------------------

-------------------------------------------
--modemSearchBtn_Click
--pick date
-------------------------------------------

SELECT
          GROUP_NAME
        , NODE_NAME
        , MODEM_ISSI
FROM
          DAY_MODEM_INFO
WHERE
          REG_DT = '2019-11-24'
ORDER BY
          MODEM_ISSI
```
