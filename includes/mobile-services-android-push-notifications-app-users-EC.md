
接下來，您需要變更您註冊通知的時間，以確認使用者在嘗試註冊前已經驗證。


1. 在 Eclipse 的 [封裝總管] 中，開啟 ToDoActivity.java 檔案並尋找 `onCreate` 方法。將下列程式碼從 `onCreate` 方法移至 `createTable` 方法的開頭。

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

     系統會在 `authenticate` 方法完成時呼叫 `createTable` 方法。您的完整 `createTable` 方法應會如下所示：

        private void createTable() {
        
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        
            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);
            
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
            
            // Create an adapter to bind the items with the view
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
            
            // Load the items from the Mobile Service
            refreshItemsFromTable();
        }	

<!---HONumber=July15_HO2-->