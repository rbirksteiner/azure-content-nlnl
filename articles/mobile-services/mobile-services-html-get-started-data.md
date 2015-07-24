<properties 
	pageTitle="將行動服務新增至現有的應用程式 (HTML 5) | 行動開發人員中心" 
	description="了解如何在現有的 HTML 應用程式中開始使用行動服務。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="05/02/2015" 
	ms.author="glenga"/>

# 將行動服務新增至現有的應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##概觀 

本主題將示範如何使用 Azure 行動服務，以在 HTML 應用程式中運用資料。在本教學課程中，您將下載應用程式，並在記憶體中儲存資料、建立新的行動服務、將行動服務與該應用程式整合，然後登入 Azure 管理入口網站檢視執行應用程式時所做的資料變更。

>[AZURE.NOTE]本教學課程旨在協助您深入了解行動服務可以如何讓您使用 Azure，以儲存與擷取 HTML 應用程式的資料。因此，本主題將逐步引導您完成在行動服務快速入門中已為您完成的許多步驟。如果這是您第一次接觸行動服務，請考慮首先完成教學課程[開始使用行動服務]。

> [AZURE.IMPORTANT]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2documentation%2Farticles%2Fmobile-services-html-get-started-data)。

###其他需求

您可以在任何網頁伺服器上裝載 GetStartedWithData 應用程式。不過，為了方便起見，我們提供可讓您在 `http://localhost:8000` 上執行應用程式的指令碼。
 
+ 若要使用 localhost，本教學課程需要您在本機電腦上執行下列其中一部網頁伺服器：

	+  **在 Windows 上**：IIS Express。IIS Express 是透過 [Microsoft Web Platform Installer] 所安裝。   
	+  **在 MacOS X 上**：Python (應該已安裝)。
	+  **在 Linux 上**：Python。您必須安裝 [最新版本的 Python]。 
	
	您可以使用任何網頁伺服器來裝載應用程式，但是這些是已下載指令碼所支援的網頁伺服器。

+ 支援 HTML5 的網頁瀏覽器。

##<a name="download-app"></a>下載 GetStartedWithData 專案

本教學課程是採用 [GetStartedWithData 應用程式] (HTML5 應用程式) 所建立。除了會將新增項目儲存在本機記憶體中之外，此應用程式的 UI 與行動服務快速入門所產生的應用程式完全相同。

1. [下載 HTML 應用程式專案檔案][GetStartedWithData app]。

2. 在 HTML 編輯器中，開啟下載的專案並檢查 app.js 檔案。

   	請注意，新增的項目會儲存在記憶體內部的 **Array** 物件 (**staticItems**) 中。Refresh the page, and the data disappears.此資料不會保留下來。

3. 從 **server** 子資料夾中啟動下列其中一個命令檔案。

	+ **launch-windows** (Windows 電腦) 
	+ **launch-mac.command** (Mac OS X 電腦)
	+ **launch-linux.sh** (Linux 電腦)

	> [AZURE.NOTE]在 Windows 電腦上，PowerShell 要求您確認是否要執行指令碼時，請輸入 `R`。因為指令碼是從網際網路中下載，所以您的網頁瀏覽器可能會警告您不要執行指令碼。發生此情況時，您必須要求瀏覽器繼續載入指令碼。
	
	如此會在本機電腦上啟動網頁伺服器來裝載新的應用程式。

4. 在網頁瀏覽器中開啟 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> 以啟動應用程式。

5. 在應用程式中，於 [Enter new task] 中輸入有意義的文字 (例如 **Complete the tutorial**)，然後按一下 [新增]。

   	![][0]

   	請注意，儲存的文字會新增至 **staticItems** 陣列，頁面會重新整理，以顯示新的項目。

##<a name="create-service"></a>在管理入口網站中建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>將新資料表新增至行動服務

若要能夠在新行動服務中儲存應用程式資料，您必須先在相關聯的 SQL Database 執行個體中建立新的資料表。

1. 在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2. 按一下 [資料] 索引標籤，然後按一下 [建立]。

   	這樣做會顯示 [建立新資料表] 對話方塊。

3. 在 [資料表名稱] 中輸入 _TodoItem_，然後按一下核取按鈕。

  	這會建立包含預設權限集的新儲存資料表 **TodoItem**。這表示擁有應用程式金鑰 (隨應用程式散佈) 的人都可以存取與變更資料表中的資料。資料表會建立在指定行動服務的特定結構描述中。目的是為了防止多個行動服務使用相同資料庫時產生資料衝突。

4. 按一下新的 **TodoItem** 資料表，並驗證其中不含資料列。

	>[AZURE.NOTE]新的資料表是使用 Id、__createdAt__、updatedAt 和 __version 等資料行建立而成。啟用動態結構描述時，行動服務會根據插入或更新要求中的 JSON 物件自動產生新資料行。如需詳細資訊，請參閱[動態結構描述](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx)。

6. 在 [設定] 索引標籤中，確認 `localhost` 已列在 [跨原始資源共用 (CORS)] 的 [允許來自主機名稱的要求] 清單中。否則，請在 [主機名稱] 欄位中輸入 `localhost`，然後按一下 [儲存]。


	> [AZURE.IMPORTANT]如果您將快速入門應用程式部署至 localhost 以外的 Web 伺服器，您必須將該 Web 伺服器的主機名稱新增至 [允許提出要求的主機名稱] 清單。如需詳細資訊，請參閱[跨原始來源資源分享](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank) (英文)。

現在您已準備好將新的行動服務作為應用程式的資料儲存區使用。

##<a name="update-app"></a>更新應用程式以使用行動服務進行資料存取

您的行動服務已準備就緒，現在可以更新應用程式以便在行動服務 (而非本機收集) 中儲存項目。

3. 在管理入口網站中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

4. 按一下 [儀表板] 索引標籤並記下 [行動服務 URL]，然後按一下 [管理金鑰] 並記下 [應用程式金鑰]。

  	從應用程式程式碼存取行動服務時，您將會用到這些值。

1. 在您的 Web 編輯器中，開啟 index.html 專案檔案，並將下列內容新增至頁面的指令碼參考：

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.5.min.js"></script>

5. 在此編輯器中，開啟檔案 app.js，取消註解下列定義 **MobileServiceClient** 變數的程式碼，並在 **MobileServiceClient** 建構函式中依序提供行動服務的 URL 和應用程式金鑰：

	    var MobileServiceClient = WindowsAzure.MobileServiceClient,
			client = new MobileServiceClient('AppUrl', 'AppKey'),   		    

  	這會建立 MobileServiceClient 的新執行個體，可用來存取您的行動服務。

6. 註解化下列程式碼行：

		var itemCount = 0;
		var staticItems = [];

	資料會被儲存在行動服務，而非儲存在記憶體內部陣列中。

6. 取消註解下列程式碼行：

        todoItemTable = client.getTable('todoitem');

   	此程式碼會為 SQL Database **TodoItem** 建立 Proxy 物件 (**todoItemTable**)。

7. 以下列程式碼取代 **$('#add-item').submit** 事件處理常式：

		$('#add-item').submit(function(evt) {
			var textbox = $('#new-item-text'),
				itemText = textbox.val();
			if (itemText !== '') {
				todoItemTable.insert({ text: itemText, complete: false })
					.then(refreshTodoItems);
			}
			textbox.val('').focus();
			evt.preventDefault();
		});


  	此程式碼會將新的項目插入資料表中。

8. 以下列程式碼取代 **refreshTodoItems** 方法：

		function refreshTodoItems() {

			var query = todoItemTable;

			query.read().then(function(todoItems) {
				listItems = $.map(todoItems, function(item) {
					return $('<li>')
						.attr('data-todoitem-id', item.id)
						.append($('<button class="item-delete">Delete</button>'))
						.append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
						.append($('<div>').append($('<input class="item-text">').val(item.text)));
				});
					   
				$('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
				$('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
			});
		}
	   

   這會傳送可傳回所有項目的查詢至行動服務。重複列舉結果並在頁面上顯示資料。

9. 以下列程式碼取代 **$(document.body).on('change', '.item-text')** 和 **$(document.body).on('change', '.item-complete')** 事件處理常式：
        
		$(document.body).on('change', '.item-text', function() {
			var newText = $(this).val();
			todoItemTable.update({ id: getTodoItemId(this), text: newText });
		});

		$(document.body).on('change', '.item-complete', function() {
			var isComplete = $(this).prop('checked');
			todoItemTable.update({ id: getTodoItemId(this), complete: isComplete })
				.then(refreshTodoItems);
		});
 
   	在變更文字或勾選方塊時，這會將項目更新傳送到行動服務。

10. 以下列程式碼取代 **$(document.body).on('click', '.item-delete')** 事件處理常式：

		$(document.body).on('click', '.item-delete', function () {
			todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});

	當按一下 [刪除] 按鈕時，系統會傳送刪除到行動服務。

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。

##<a name="test-app"></a>對新的行動服務進行應用程式測試

4. 在網頁瀏覽器中重新載入 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>，以啟動應用程式。

    > [AZURE.NOTE]如果您需要重新啟動網頁伺服器，請重複第一部分中的步驟。

2. 和之前一樣，在 [Enter new task] 中輸入文字，然後按一下 [新增]。

   	這會傳送新項目以插入至行動服務。

3. 在[管理入口網站]中按一下 [行動服務]，然後按一下您的行動服務。

4. 按一下 [資料] 索引標籤，然後按一下 [瀏覽]。
  
   	請注意，[TodoItem] 資料表現在包含資料和行動服務產生的識別碼值，且資料行已自動新增至資料表，以符合應用程式中的 TodoItem 類別。

5. 在應用程式中檢查清單中的其中一個項目，然後返回入口網站中的 [瀏覽] 索引標籤，並按一下 [重新整理]。

  	請注意，完成值已從 [false] 變更為 [true]。

6. 在 app.js 專案檔案中，找出 **RefreshTodoItems** 方法，並用以下程式碼取代定義 `query` 的程式碼行：

   		var query = todoItemTable.where({ complete: false });

7. 再次載入此頁面，並勾選清單中的另一個項目。

   	請注意，所有已核取的項目都會從清單中消失。Each update results in a round-trip to the mobile service, which now returns filtered data.

這將結束**開始使用資料**教學課程。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 HTML 應用程式能夠在行動服務中處理資料的基本概念。接著請嘗試另一個教學課程，完成[將驗證新增至您的應用程式]，了解如何驗證應用程式的使用者。如果使用 Cordova 或 PhoneGap 應用程式專案，請在[使用 Microsoft Azure 將推播通知傳送到 Cordova 應用程式](https://msdn.microsoft.com/magazine/dn879353.aspx)中，深入了解推播通知。

<!-- Anchors. -->
[Download the HTML app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png

<!-- URLs. -->
[開始使用行動服務]: mobile-services-html-get-started.md
[將驗證新增至您的應用程式]: mobile-services-html-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[管理入口網站]: https://manage.windowsazure.com/
[GetStartedWithData app]: http://go.microsoft.com/fwlink/?LinkID=286345
[GetStartedWithData 應用程式]: http://go.microsoft.com/fwlink/?LinkID=286345

[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md

[Cross-origin resource sharing]: http://msdn.microsoft.com/library/azure/dn155871.aspx

 

<!---HONumber=62-->