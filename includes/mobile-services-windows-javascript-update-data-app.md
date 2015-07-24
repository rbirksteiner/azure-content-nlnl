

1. 接著，取消註解或加入以下程式碼行，然後將 `<yourClient>` 取代為連接專案與行動服務時新增至 service.js 檔案中的變數：

		var todoTable = <yourClient>.getTable('TodoItem');

   	此程式碼會使用快取篩選器來為新的資料庫資料表建立 Proxy 物件 (**todoTable**)。

2. 使用下列程式碼來取代 **InsertTodoItem** 函數：

		var insertTodoItem = function (todoItem) {
		    // Inserts a new row into the database. When the operation completes
		    // and Mobile Services has assigned an id, the item is added to the binding list.
		    todoTable.insert(todoItem).done(function (item) {
		        todoItems.push(item);
		    });
		};

	此程式碼會將新的項目插入資料表中。

3. 使用下列程式碼來取代 **RefreshTodoItems** 函數：

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the table.
            // Results are filtered to remove completed items.
            todoTable.where({ complete: false })
                .read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   	這會設定 todoTable 中項目集合的繫結，其中包含從行動服務中傳回的所有 **TodoItem** 物件。

4. 使用下列程式碼來取代 **UpdateCheckedTodoItem** 函數：
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
            // Remove the completed item from the filtered list.
            todoItems.splice(todoItems.indexOf(todoItem), 1);
        };

   	This sends an item update to the mobile service.

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。

<!---HONumber=July15_HO2-->