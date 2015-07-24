<properties
	pageTitle="將行動服務新增至現有的應用程式 (Xamarin.iOS) - Azure 行動服務"
	description="了解如何儲存和存取 Azure 行動服務 Xamarin.iOS 應用程式的資料。"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="ggailey777"/>

# 將行動服務新增至現有的應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

本主題將示範如何使用 Azure 行動服務，進而運用 Xamarin.iOS 應用程式中的資料。在本教學課程中，您將下載應用程式，並在記憶體中儲存資料、建立新的行動服務、將行動服務與該應用程式整合，然後登入 Azure 管理入口網站查看執行應用程式時所做的資料變更。

> [AZURE.NOTE]本教學課程是為了協助您充分了解行動服務如何讓您從 Xamarin.iOS 應用程式中利用 Azure 來儲存和擷取資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮首先完成教學課程[開始使用行動服務](/develop/mobile/tutorials/get-started-xamarin-ios)。

本教學課程將逐步引導您完成下列基本步驟：

1. [下載 Xamarin.iOS 應用程式專案][GitHub]
2. [建立行動服務]
3. [新增用於儲存的資料表]
4. [更新應用程式以使用行動服務]
5. [針對行動服務進行應用程式測試]

本教學課程需要 [Azure 行動服務元件]、[XCode 6.0][Install Xcode]、[Xamarin.iOS] 以及 iOS 7.0 或更新版本。

> [AZURE.IMPORTANT]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F"%20target="_blank)。

## <a name="download-app"></a>下載 GetStartedWithData 專案

本教學課程以 [GetStartedWithData 應用程式][GitHub] (Xamarin.iOS 應用程式) 為基礎而建立。此應用程式的 UI 與 Mobile Services Xamarin.iOS 快門入口所產生的應用程式相同，差別在於新增的項目會儲存在本機的記憶體。

1. 下載 [GetStartedWithData 應用程式][GitHub]。

2. 在 Xamarin Studio 中，開啟下載的專案並檢查 **TodoService** 類別。

   	請注意，若干 **// TODO** 註解會指定讓此應用程式可與您的行動服務搭配使用的必要步驟。

3. 移至 [執行] 功能表，並選擇 [啟動但不偵錯] 來啟動應用程式。

4. 在應用程式的文字方塊中鍵入一些文字，然後按一下 **+** 按鈕。

   	![][0]

   	請注意，儲存的文件會顯示在清單下方。

## <a name="create-service"></a>在管理入口網站中建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a>將新資料表新增至行動服務

若要能夠在新行動服務中儲存應用程式資料，您必須先在相關聯的 SQL Database 執行個體中建立新的資料表。

1. 在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2. 按一下 **[資料]** 索引標籤，然後按一下 **[建立]**。

   	![][5]

   	這樣做會顯示 [建立新資料表] 對話方塊。

3. 在 **[資料表名稱]** 中輸入 _TodoItem_，然後按一下核取按鈕。

  	![][6]

  	這會建立含預設權限集的新儲存資料表 **TodoItem**，這表示應用程式的任何使用者都可以存取與變更該資料表中的資料。

    > [AZURE.NOTE]行動服務快速入門中使用相同的資料表名稱。However, each table is created in a schema that is specific to a given mobile service.目的是為了防止多個行動服務使用相同資料庫時產生資料衝突。

4. 按一下新的 **TodoItem** 資料表，並驗證其中不含資料列。

5. 按一下 [資料行] 索引標籤，確認只有一個自動為您建立的 [id] 資料行。

	  此為行動服務資料表的最低需求。

    > [AZURE.NOTE]若您的行動服務啟用動態結構描述，當行動服務透過插入或更新操作收到 JSON 物件時，便會自動建立新資料行。

現在您已準備好將新的行動服務作為應用程式的資料儲存區使用。

## <a name="update-app"></a>更新應用程式以使用行動服務進行資料存取

您的行動服務已準備就緒，現在可以更新應用程式以便在行動服務 (而非本機收集) 中儲存項目。

1. 如果 [元件] 資料夾中並未列出 [Azure Mobile Services]，您能夠以滑鼠右鍵按一下 [元件]，並選擇 [Get More Components]，然後搜尋 [Azure Mobile Services] 來取得它。

2. 在 Xamarin Studio 的 [方案] 檢視中，開啟 **TodoService** 類別，並取消註解下列 `using` 陳述式：

        using Microsoft.WindowsAzure.MobileServices;

3. 在管理入口網站中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

4. 按一下 [儀表板] 索引標籤並記下 [網站 URL]，然後按一下 [管理金鑰] 並記下 [應用程式金鑰]。

   	![][8]

5. 在 **Constants** 類別中，取消註解下列常數：

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6. 使用前面在管理入口網站中找出的值取代以上常數中的 **AppUrl** 和 **AppKey**。

7. 在 **TodoService** 類別中，取消註解下列程式碼行：

        private MobileServiceClient client;

   	這將建立可連接服務的 MobileServiceClient 代表屬性。

8. 在 **TodoService** 類別中，取消註解下列程式碼行：

        private IMobileServiceTable<TodoItem> todoTable;  

   	這將建立行動服務資料表的代表屬性。

9. 在 **TodoService** 建構函式中取消註解下列數行：

		// TODO:: Uncomment these lines to use Mobile Services
		client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this);
		todoTable = client.GetTable<TodoItem>();

    這會建立行動服務用戶端的執行個體，並建立 TodoItem 資料表執行個體。

10. 在 **TodoService** 的 **RefreshDataAsync** 方法中取消註解下列數行：

			// TODO:: Uncomment these lines to use Mobile Services
			try
	    {
				// This code refreshes the entries in the list view by querying the TodoItems table.
				// The query excludes completed TodoItems
				Items = await todoTable
					.Where (todoItem => todoItem.Complete == false).ToListAsync();
			}
	    catch (MobileServiceInvalidOperationException e)
	    {
				Console.Error.WriteLine (@"ERROR {0}", e.Message);
				return null;
			}

    This creates a query to return all tasks that have not yet been completed.

11. 找出 **InsertTodoItemAsync** 方法，並取消註解下列一行：

		await todoTable.InsertAsync(todoItem);

    此程式碼會傳送插入要求給行動服務。

13. 找出 **CompleteItemAsync** 方法，並取消註解下列一行：

		await todoTable.UpdateAsync(item);

   	此程式碼會在 TodoItems 被標示為已完成時將它移除。

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。

## <a name="test-app"></a>對新的行動服務進行應用程式測試

1. 在 Xamarin Studio 中，從其中一個主要下拉式方塊選取要部署的目的地模擬器或裝置，然後移至 [執行] 功能表，並選取 [啟動但不偵錯] 來啟動應用程式。

   	這將執行您的 Azure 行動服務用戶端，其使用 Xamarin.iOS 建置，可從您的行動服務查詢項目。

2. 和之前一樣，在文字方塊中鍵入文字，然後按一下 **+** 按鈕。

   	這會傳送新項目以插入至行動服務。

3. 在[管理入口網站]中按一下 [行動服務]，然後按一下您的行動服務。

4. 按一下 [資料] 索引標籤，然後按一下 [瀏覽]。

   	![][9]

   	請注意，[TodoItem] 資料表現在包含資料和行動服務產生的識別碼值，且資料行已自動新增至資料表，以符合應用程式中的 TodoItem 類別。

這將結束 Xamarin.iOS 的**開始使用資料**教學課程。

## 取得完成的範例
下載[完成的範例專案]。務必以您自己的 Azure 設定更新 **applicationURL** 和 **applicationKey** 變數。

## <a name="next-steps"> </a>後續步驟

本教學課程將示範啟用 iOS 應用程式來使用行動服務資料的基本概念。

接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

* [使用指令檔驗證與修改資料] <br/>深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

* [使用分頁縮小查詢範圍] <br/>了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料數列之後，請嘗試下列其他 iOS 教學課程：

* [開始使用驗證] <br/>了解如何驗證應用程式的使用者。

* [開始使用推送通知] <br/>了解如何使用行動服務將非常基本的推送通知傳送到應用程式。

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[建立行動服務]: #create-service
[新增用於儲存的資料表]: #add-table
[更新應用程式以使用行動服務]: #update-app
[針對行動服務進行應用程式測試]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->
[使用指令檔驗證與修改資料]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[使用分頁縮小查詢範圍]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[開始使用驗證]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[開始使用推送通知]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Azure Management Portal]: https://manage.windowsazure.com/
[管理入口網站]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784
[Azure 行動服務元件]: http://components.xamarin.com/view/azure-mobile-services/

[完成的範例專案]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Xamarin.iOS]: http://xamarin.com/download
 

<!---HONumber=62-->