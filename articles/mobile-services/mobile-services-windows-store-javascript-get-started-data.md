<properties 
	pageTitle="將行動服務新增至現有的應用程式 (Windows 市集 JavaScript) | 行動開發人員中心" 
	description="了解如何開始使用行動服務，在您的 Windows 市集 JavaScript 應用程式中使用資料。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>


# 將行動服務新增至現有的應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../../includes/mobile-services-selector-get-started-data-legacy.md)]

##概觀
本主題將示範如何使用 Azure 行動服務，以在 Windows 市集應用程式中運用資料。在本教學課程中，您將下載 Visual Studio 2013 專案，以取得在記憶體中儲存資料的應用程式、建立新的行動服務、整合行動服務與該應用程式，然後登入 Azure 管理入口網站來檢視執行應用程式時所做的資料變更。

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F)。
* 您將使用 Visual Studio 2013 連接 Windows 市集應用程式專案與行動服務。

##下載 GetStartedWithData 專案

本教學課程以 [GetStartedWithMobileServices 應用程式][Developer Code Samples site] (這是 Visual Studio 2013 中的 Windows 市集應用程式專案) 做為建置基礎。除了會將新增項目儲存在本機記憶體中之外，由此應用程式與行動服務快速入門所產生的應用程式兩者的 UI 完全相同。

1. 從[開發人員程式碼範例網站] (英文) 下載 JavaScript 版本的 GetStartedWithData 範例應用程式。 

2. 在 Visual Studio 中，開啟下載的專案，並展開 **js** 資料夾檢查 default.js 檔案。

   	請注意，新增的 **TodoItem** 物件儲存在記憶體內部的 **List** 物件中。

3. 按 **F5** 鍵，以重建專案並啟動應用程式。

4. 在應用程式的 [Insert a TodoItem] 中輸入一些文字，然後按一下 [儲存]。

   	![][0]

   	請注意儲存的文字會顯示在 [查詢和更新資料] 下方的第二個資料行中。

##<a name="create-service"></a>從 Visual Studio 建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>在 [方案總管] 中展開 **services**、**mobile services**、**&lt;your_service>** 資料夾，然後開啟 service.js 指令碼檔案，並注意新的全域變數看起來會如下列範例所示：</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>本程式碼透過使用全域變數，提供對應用程式中新行動服務的存取權。用戶端是利用提供新行動服務的 URI 和應用程式金鑰來建立。由於此指令碼的參考已新增到 default.html 檔案，因此此變數可供所有也從此頁面受參考的指令碼檔案使用。</p>
</li>
</ol>

##新增用於儲存資料的資料表

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../../includes/mobile-services-create-new-table-vs2013.md)]

##更新應用程式以使用行動服務

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../../includes/mobile-services-windows-javascript-update-data-app.md)]

##對新的行動服務進行應用程式測試

1. 在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2. 和之前一樣，在 [Insert a TodoItem] 中鍵入文字，然後按一下 [儲存]。

   	這會傳送新項目以插入至行動服務。

3. 在[管理入口網站]中按一下 [行動服務]，然後按一下您的行動服務。

4. 按一下 [資料] 索引標籤，然後按一下 [瀏覽]。
  
   	請注意，[TodoItem] 資料表現在包含資料和行動服務產生的識別碼值，且資料行已自動新增至資料表，以符合應用程式中的 TodoItem 類別。

5. 在應用程式中檢查清單中的其中一個項目，然後返回入口網站中的 [瀏覽] 索引標籤，並按一下 [重新整理]。

  	請注意，完成值已從 [false] 變更為 [true]。

6. 在 default.js 專案檔案中，使用下列可篩選出已完成項目的程式碼，來取代現有的 **RefreshTodoItems** 函數：

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7. 在應用程式中檢查清單中的其中一個項目，然後按一下 [重新整理] 按鈕。

   	請注意，已檢查的項目此時會從清單中消失。Each refresh results in a round-trip to the mobile service, which now returns filtered data.

這將結束**開始使用資料**教學課程。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 Windows 市集應用程式能夠在行動服務中處理資料的基本概念。接著，請考慮閱讀下列其他主題：

* [將驗證新增至您的應用程式](mobile-services-windows-store-javascript-get-started-users.md) <br/>了解如何驗證應用程式的使用者。

* [將推播通知新增至您的應用程式](mobile-services-javascript-backend-windows-store-javascript-get-started-push.md) <br/>了解如何將非常基本的推播通知傳送至應用程式。

* [行動服務 HTML/JavaScript 作法概念參考](mobile-services-html-how-to-use-client-library.md) <br/>深入了解如何將行動服務搭配 HTML 及 JavaScript 使用。

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Azure Management Portal]: https://manage.windowsazure.com/
[管理入口網站]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[開發人員程式碼範例網站]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md
 

<!---HONumber=July15_HO1-->