<properties
	pageTitle="開始使用驗證 (Xamarin.iOS) - 行動服務"
	description="了解如何在 Xamarin.iOS 的 Azure 行動服務應用程式中使用驗證。"
	documentationCenter="xamarin"
	services="mobile-services"
	manager="dwrede"
	authors="lindydonna"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/14/2015"
	ms.author="donnam"/>

# 在您的行動服務應用程式中新增驗證

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [限制只有經驗證的使用者具有資料表的權限]
3. [將驗證新增至應用程式]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務]。

完成本教學課程需使用 [Xamarin.iOS]、XCode 6.0 及 iOS 7.0 或更新版本。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. 在 Xcode 中，開啟您完成教學課程[開始使用行動服務]時建立的專案。

4. 按下 [執行] 按鈕以建置專案並在 iPhone 模擬器中啟動應用程式。確認應用程式啟動之後會引發無法處理的例外狀況，狀態碼為 401 (未授權)。

   	這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 _TodoItem_ 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

1. 開啟 **ToDoService** 專案檔案，並新增下列變數。

		// Mobile Service logged in user
		private MobileServiceUser user;
		public MobileServiceUser User { get { return user; } }

2. 接著將名稱為 **Authenticate** 的新方法新增至 **ToDoService**，定義如下：

        private async Task Authenticate(MonoTouch.UIKit.UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

	> [AZURE.NOTE]如果您使用的身分識別提供者不是 Microsoft 帳戶，請將傳給上述 **LoginAsync** 的值變更為下列其中一個：_Facebook_、_Twitter_、_Google_ 或 _WindowsAzureActiveDirectory_。

3. 將 **ToDoItem** 的要求從 **ToDoService** 建構函式移至名稱為 **CreateTable** 的新方法中：

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the ToDoItem table
            todoTable = client.GetSyncTable<ToDoItem>();
        }

4. 建立名稱為 **LoginAndGetData** 的新非同步公用方法，定義如下：

        public async Task LoginAndGetData(MonoTouch.UIKit.UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5. 在 **TodoListViewController** 中覆寫 **ViewDidAppear** 方法，並且按照下列方式定義該方法。如果 **ToDoService** 在使用者上還沒有控點，如此會將使用者登入。

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (QSToDoService.DefaultService.User == null)
            {
                await QSToDoService.DefaultService.LoginAndGetData(this);
            }

            if (QSToDoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            }

            RefreshAsync();
        }
6. 從 **TodoListViewController.ViewDidLoad** 移除 **RefreshAsync** 的原始呼叫。

7. 按 [執行] 按鈕以建置專案並在 iPhone 模擬器中啟動應用程式，然後使用您選擇的身分識別提供者登入。

   	成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

## 取得完成的範例
下載[完成的範例專案]。務必以您自己的 Azure 設定更新 **applicationURL** 和 **applicationKey** 變數。

## <a name="next-steps"></a>接續步驟

在下一堂教學課程[使用指令碼授權使用者]中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有經驗證的使用者具有資料表的權限]: #permissions
[將驗證新增至應用程式]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. TODO:: update completed example project link with project download -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[開始使用行動服務]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /develop/mobile/tutorials/-get-started-with-push-xamarin-ios
[使用指令碼授權使用者]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios

[Azure Management Portal]: https://manage.windowsazure.com/
[完成的範例專案]: http://go.microsoft.com/fwlink/p/?LinkId=331328
[Xamarin.iOS]: http://xamarin.com/download
 

<!---HONumber=July15_HO1-->