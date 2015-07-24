
##<a name="update-app"></a>更新應用程式以呼叫自訂 API

1. 在 Visual Studio，開啟快速入門專案中的 MainPage.xaml 檔案，尋找名為 `ButtonRefresh` 的 **Button** 元素，並使用下列 XAML 程式碼取代它： 

		<StackPanel Orientation="Horizontal">
	        <Button Margin="72,0,0,0" Name="ButtonRefresh" 
	                Click="ButtonRefresh_Click">Refresh</Button>
	        <Button Margin="12,0,0,0" Name="ButtonCompleteAll" 
	                Click="ButtonCompleteAll_Click">Complete All</Button>
	    </StackPanel>

	這會將新按鈕新增至頁面。

2. 開啟 MainPage.xaml.cs 程式碼檔案，並新增下列類別定義程式碼：

	    public class MarkAllResult
	    {
	        public int Count { get; set; }
	    }

	此類別是用來保留自訂 API 傳回的資料列計數值。

3. 在 **MainPage** 類別中尋找 **RefreshTodoItems** 方法，並確定使用下列 **Where** 方法定義 `query`：

        .Where(todoItem => todoItem.Complete == false)

	這會篩選項目，如此一來，查詢就不會傳回已完成的項目。

3. 在 **MainPage** 類別中，新增下列方法：

		private async void ButtonCompleteAll_Click(object sender, RoutedEventArgs e)
		{
		    string message;
		    try
		    {
		        // Asynchronously call the custom API using the POST method. 
		        var result = await App.MobileService
		            .InvokeApiAsync<MarkAllResult>("completeAll", 
		            System.Net.Http.HttpMethod.Post, null);
		        message =  result.Count + " item(s) marked as complete.";
		        RefreshTodoItems();
		    }
		    catch (MobileServiceInvalidOperationException ex)
		    {
		        message = ex.Message;                
		    }
		
		    var dialog = new MessageDialog(message);
		    dialog.Commands.Add(new UICommand("OK"));
		    await dialog.ShowAsync();
		}

	此方法會處理新按鈕的 **Click** 事件。[InvokeApiAsync](http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx) 方法是在用戶端上呼叫，可將 POST 要求傳送給新的自訂 API。如有任何錯誤，自訂 API 傳回的結果會顯示在訊息對話方塊中。

## <a name="test-app"></a>測試應用程式

1. 在 Visual Studio 中按 **F5** 鍵，以重建專案並啟動應用程式。

2. 在應用程式的 [Insert a TodoItem] 中鍵入一些文字，然後按一下 [儲存]。

3. 重複前一個步驟，直到將數個 Todo 項目新增至清單為止。

4. 按一下 [Complete All] 按鈕。

  	![](./media/mobile-services-windows-store-dotnet-call-custom-api/mobile-custom-api-windows-store-completed.png)

	出現訊息對話方塊，指出標示為完成的項目數，並重新執行篩選查詢，以便清除清單的所有項目。

<!---HONumber=July15_HO2-->