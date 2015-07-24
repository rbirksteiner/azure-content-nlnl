
##<a name="update-app"></a>更新應用程式以呼叫自訂 API

1. 在 Visual Studio 中，開啟快速入門專案的 default.html 檔案，尋找名為 `buttonRefresh` 的 **button** 元素，並緊接其後新增下列新元素： 

		<button id="buttonCompleteAll" style="margin-left: 5px">Complete All</button>

	這會將新按鈕新增至頁面。

2. 在 `js` 專案資料夾中開啟 default.js 程式碼檔案，尋找 **refreshTodoItems** 函數，並確定此函數包含下列程式碼：

	    todoTable.where({ complete: false })
	       .read()
	       .done(function (results) {
	           todoItems = new WinJS.Binding.List(results);
	           listItems.winControl.itemDataSource = todoItems.dataSource;
	       });            

	這會篩選項目，如此一來，查詢就不會傳回已完成的項目。

3. 在 **refreshTodoItems** 函數後面新增下列程式碼：

		var completeAllTodoItems = function () {
		    var okCommand = new Windows.UI.Popups.UICommand("OK");
		
		    // Asynchronously call the custom API using the POST method. 
		    mobileService.invokeApi("completeall", {
		        body: null,
		        method: "post"
		    }).done(function (results) {
		        var message = results.result.count + " item(s) marked as complete.";
		        var dialog = new Windows.UI.Popups.MessageDialog(message);
		        dialog.commands.append(okCommand);
		        dialog.showAsync().done(function () {
		            refreshTodoItems();
		        });
		    }, function (error) {
		        var dialog = new Windows.UI.Popups
		            .MessageDialog(error.message);
		        dialog.commands.append(okCommand);
		        dialog.showAsync().done();
		    });
		};

        buttonCompleteAll.addEventListener("click", function () {
            completeAllTodoItems();
        });

	此方法會處理新按鈕的 **Click** 事件。**InvokeApiAsync** 方法是在用戶端上呼叫，可將 POST 要求傳送給新的自訂 API。如有任何錯誤，自訂 API 傳回的結果會顯示在訊息對話方塊中。

## <a name="test-app"></a>測試應用程式

1. 在 Visual Studio 中按 **F5** 鍵，以重建專案並啟動應用程式。

2. 在應用程式的 [Insert a TodoItem] 中鍵入一些文字，然後按一下 [儲存]。

3. 重複前一個步驟，直到將數個 Todo 項目新增至清單為止。

4. 按一下 [Complete All] 按鈕。

  	![](./media/mobile-services-windows-store-javascript-call-custom-api/mobile-custom-api-windows-store-completed.png)

	出現訊息對話方塊，指出標示為完成的項目數，並重新執行篩選查詢，以便清除清單的所有項目。

<!---HONumber=July15_HO2-->