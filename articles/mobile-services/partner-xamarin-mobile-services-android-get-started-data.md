<properties 
	pageTitle="將行動服務新增至現有的應用程式 (Xamarin.Android) - Azure 行動服務" 
	description="了解如何儲存和存取 Azure 行動服務 Xamarin.Android 應用程式的資料。" 
	documentationCenter="xamarin" 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="ggailey777"/>

# 將行動服務新增至現有的應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

本主題將示範如何使用 Azure 行動服務，進而運用 Xamarin.Android 應用程式中的資料。在本教學課程中，您將下載應用程式，並在記憶體中儲存資料、建立新的行動服務、將行動服務與該應用程式整合，然後登入 Azure 管理入口網站查看執行應用程式時所做的資料變更。

> [AZURE.NOTE]本教學課程是為了協助您充分了解行動服務如何讓您從 Xamarin.Android 應用程式中利用 Azure 來儲存和擷取資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮首先完成教學課程[開始使用行動服務](/develop/mobile/tutorials/get-started-xamarin-android)。

本教學課程將逐步引導您完成下列基本步驟：

1. [下載 Xamarin.Android 應用程式專案][GitHub] 
2. [建立行動服務]
3. [新增用於儲存的資料表]
4. [更新應用程式以使用行動服務]
5. [針對行動服務進行應用程式測試]

> [AZURE.IMPORTANT]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5"%20target="_blank)。

本教學課程需要 [Azure 行動服務元件]、[Xamarin.Android] 及 Android SDK 4.2 或更新版本。

> [AZURE.NOTE]下載的 GetStartedWithData 專案需要以 Android 4.2 或更新版本為目標。不過，Mobile Services SDK 只需要 Android 2.2 或更新版本。

## <a name="download-app"></a>下載 GetStartedWithData 專案

本教學課程以 [GetStartedWithData 應用程式][GitHub] (Xamarin.Android 應用程式) 為基礎而建立。此應用程式的 UI 與 Mobile Services Android 快門入口所產生的應用程式相同，差別在於新增的項目會儲存在本機的記憶體。

1. 將 `GetStartedWithData` 範例應用程式下載到電腦並解壓縮檔案。 

2. 在 Xamarin Studio 中，依序按一下 [檔案]、[開啟]，瀏覽至 GetStartedWithData sample 專案解壓縮的位置，並選取 **XamarinTodoQuickStart.Android.sln** 將其開啟。

3. 找出並開啟 **TodoActivity** 類別

   	請注意，`// TODO::` 註解會指定讓此應用程式可與您的行動服務搭配使用的必要步驟。

5. 從 [執行] 功能表，按一下 [啟動但不偵錯]，接著系統將要求您選擇模擬器或連接的 USB Android 裝置。

	> [AZURE.IMPORTANT]您可以使用 Android 手機或使用 Android 模擬器來執行此專案。使用 Android 手機來執行需要先下載手機專用的 USB 驅動程式。
	> 
	> 若要在 Android 模擬器中執行專案，您至少必須定義一個 Android 虛擬裝置 (AVD)。使用 AVD Manager 來建立與管理這些裝置。

6. 在應用程式中輸入有意義的文字 (例如 _Complete the tutorial_)，然後按一下 [新增]。

   	![][13]

   	Notice that the saved text is stored in an in-memory collection and displayed in the list below.

## <a name="create-service"></a>在管理入口網站中建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a>將新資料表新增至行動服務

若要能夠在新行動服務中儲存應用程式資料，您必須先建立新的資料表。

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

  	這會將 Mobile Services SDK 元件新增至專案。

2. 開啟 **AndroidManifest.xml** 檔案，並確定下列權限行存在：

		<uses-permission android:name="android.permission.INTERNET" />

  	這使得應用程式能夠存取 Azure 中的行動服務。

3. 從 [方案] 視窗，開啟 **TodoActivity** 類別，並取消註解下列程式碼行：

		using Microsoft.WindowsAzure.MobileServices;
 
4. 我們將會移除應用程式目前使用的記憶體中清單，所以可以將它換成行動服務。在 **TodoActivity** 類別中，下列這一行程式碼定義現有的 **todoItemList** 清單，請將它變成註解。

		public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5. 完成上述步驟時，專案會指出建置錯誤。請搜尋其餘三個用到 `todoItemList` 變數的位置，並將指出的區段變成註解。

6. 現在開始加入我們的行動服務。將下列幾行程式碼取消註解：

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7. 在管理入口網站中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

8. 按一下 [儀表板] 索引標籤並記下 [網站 URL]，然後按一下 [管理金鑰] 並記下 [應用程式金鑰]。

   	![][8]

  	從應用程式程式碼存取行動服務時，您將會用到這些值。

9. 在 **Constants** 類別中，取消註解下列成員變數：

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";
        
10. 使用從以上的管理入口網站擷取的值取代以上變數中的 **AppUrl** 和 **AppKey**。

11. 在 **OnCreate** 方法中，下列幾行程式碼定義 **MobileServiceClient** 變數，請將它們取消註解：

		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
		client = new MobileServiceClient(
			Constants.ApplicationURL,
			Constants.ApplicationKey).WithFilter(filter);

		// Get the Mobile Service Table instance to use
		todoTable = client.GetTable<TodoItem>();    

  	這會建立 MobileServiceClient 的新執行個體，可用來存取您的行動服務。也會建立 MobileServiceTable 執行個體，用於代理行動服務中的資料儲存。

12. 在檔案底部找出 ProgressFilter 類別並取消註解。此類別會在 MobileServiceClient 執行網路作業時顯示 'loading' 指示。

13. 將 **CheckItem** 方法的這幾行取消註解：

		try {
			await todoTable.UpdateAsync(item);
			if (item.Complete)
				adapter.Remove(item);
		} catch (Exception e) {
			CreateAndShowDialog(e, "Error");
		}

   	這會將項目更新傳送至行動服務，並從配接器中移除已核取的項目。
    
14. 將 **AddItem** 方法的這幾行取消註解：
	
		try 
		{
			// Insert the new item
			await todoTable.InsertAsync(item);

			if (!item.Complete) 
				adapter.Add(item);			
		} 
		catch (Exception e) 
		{
			CreateAndShowDialog(e, "Error");
		}   		

  	此程式碼會建立新的項目，並將此項目插入至遠端行動服務中的資料表。

15. 將 **RefreshItemsFromTableAsync** 方法的這幾行取消註解：

		try {
			// Get the items that weren't marked as completed and add them in the adapter
			var list = await todoTable.Where(item => item.Complete == false).ToListAsync ();

			adapter.Clear();

			foreach (TodoItem current in list)
				adapter.Add(current);
		} 
        catch (Exception e) 
        {
			CreateAndShowDialog(e, "Error");
		}

	這會查詢行動服務，並傳回所有未標示為完成的項目。項目會加入至配接器來繫結。
		

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。

## <a name="test-app"></a>對新的行動服務進行應用程式測試

1. 從 [執行] 功能表，按一下 [啟動但不偵錯] 來啟動專案。系統將要求您選擇現有的模擬器映像或連接的 USB Android 裝置。

	這樣會執行您的應用程式 (以 Xamarin.Android 建立)，該應用程式利用用戶端程式庫傳送查詢，然後從您的行動服務傳回項目。

5. 同樣地，輸入有意義的文字，然後按一下 [加入]。

   	這會傳送新項目以插入至行動服務。

3. 在[管理入口網站]中按一下 [行動服務]，然後按一下您的行動服務。

4. 按一下 [資料] 索引標籤，然後按一下 [瀏覽]。

   	![][9]
  
   	請注意，[TodoItem] 資料表現在包含資料和行動服務產生的識別碼值，且資料行已自動新增至資料表，以符合應用程式中的 TodoItem 類別。

這將結束 Xamarin.Android 的**開始使用資料**教學課程。

## 取得完成的範例
下載[完成的範例專案]。務必以您自己的 Azure 設定更新 **applicationURL** 和 **applicationKey** 變數。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 Xamarin.Android 應用程式在行動服務中使用資料的基礎。

接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

* [使用指令檔驗證與修改資料] 深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

* [使用分頁縮小查詢範圍] 了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料系列後，可試著閱讀這些另外的 Xamarin.Android 教學課程：

* [開始使用驗證] 了解如何驗證應用程式的使用者。

* [開始使用推播通知] 了解如何使用行動服務將非常基本的推播通知傳送到應用程式。

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[建立行動服務]: #create-service
[新增用於儲存的資料表]: #add-table
[更新應用程式以使用行動服務]: #update-app
[針對行動服務進行應用程式測試]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->





[5]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png

<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[使用指令檔驗證與修改資料]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[使用分頁縮小查詢範圍]: /develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[開始使用驗證]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[開始使用推播通知]: /develop/mobile/tutorials/get-started-with-push-xamarin-android

[Azure Management Portal]: https://manage.windowsazure.com/
[管理入口網站]: https://manage.windowsazure.com/
[Azure 行動服務元件]: http://components.xamarin.com/view/azure-mobile-services/
[Download the Android app project]: http://www.google.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

[完成的範例專案]: http://go.microsoft.com/fwlink/p/?LinkId=331302
 

<!---HONumber=July15_HO2-->