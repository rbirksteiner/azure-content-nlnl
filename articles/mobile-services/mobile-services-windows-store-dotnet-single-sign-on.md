<properties 
	pageTitle="使用 Live Connect 驗證 Windows 市集應用程式" 
	description="了解如何從 Windows 市集應用程式在 Azure 行動服務中使用 Live Connect 單一登入。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="glenga"/>

# 使用 Microsoft 帳戶以用戶端管理的驗證方式驗證您的 Windows 市集應用程式

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)]

##概觀
本主題示範如何使用 Live SDK 從通用的 Windows 應用程式取得 Microsoft 帳戶的驗證權杖。然後您可以使用此權杖來驗證使用 Azure 行動服務的使用者。在本教學課程中，您將使用 Live SDK 將 Microsoft 帳戶驗證新增至現有的專案。成功驗證後，畫面會顯示名稱和使用者識別碼值來歡迎登入的使用者。

>[AZURE.NOTE]本教學課程示範使用用戶端主導的驗證和 Live SDK 的優點。這可讓您更輕鬆地使用行動服務來驗證已登入使用者。您也可以要求額外的範圍，讓您的應用程式也能存取如 OneDrive 的資源。服務主導的驗證則提供更一般化的體驗，並支援多種驗證提供者。如需有關服務主導的驗證的詳細資訊，請參閱[將驗證新增至您的應用程式](mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md)主題。

本教學課程需要下列各項：

+ [Live SDK]
+ Microsoft Visual Studio 2013 Update 3 或更新版本。
+ 您還必須先完成[開始使用行動服務](mobile-services-javascript-backend-windows-store-dotnet-get-started.md)或[將行動服務新增至現有應用程式]教學課程。

##註冊應用程式來使用 Microsoft 帳戶進行驗證

若要能夠驗證使用者，您必須在 Microsoft 帳戶開發人員中心註冊您的應用程式。然後您必須將此註冊連接到您的行動服務。請完成下列主題中的步驟以建立 Microsoft 帳戶註冊，並將註冊連接到您的行動服務。

+ [註冊應用程式來使用 Microsoft 帳戶登入](mobile-services-how-to-register-microsoft-authentication.md)

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

接著您必須限制資源的存取權，在此案例中，要確保 *TodoItems* 資料表只能被登入的使用者存取。

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]

##<a name="add-authentication"></a>將驗證新增至應用程式

最後，您要新增 Live SDK 並用它來驗證您應用程式中的使用者。

1. 在 [方案總管] 中，以滑鼠右鍵按一下方案，然後選取 [管理 NuGet 封裝]。

2. 在左窗格中選取 [線上] 類別、搜尋 **LiveSDK**，按一下 [Live SDK] 封裝上的 [安裝]，然後接受授權合約。

  	這樣會將 Live SDK 新增至方案中。

3. 開啟共用的專案檔案 MainPage.xaml.cs 並新增下列 using 陳述式：

        using Microsoft.Live;        

4. 將下列程式碼片段新增至 **MainPage** 類別：
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {

            // Get the URL the mobile service.
            var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

            // Create the authentication client using the mobile service URL.
            LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);

            while (session == null)
            {
                // Request the authentication token from the Live authentication service.
				// The wl.basic scope is requested.
                LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;

                    // Get information about the logged-in user.
                    LiveConnectClient client = new LiveConnectClient(session);
                    LiveOperationResult meResult = await client.GetAsync("me");

                    // Use the Microsoft account auth token to sign in to Mobile Services.
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    // Display a personalized sign-in greeting.
                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    var dialog = new MessageDialog(message, title);
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
                else
                {
                    session = null;
                    var dialog = new MessageDialog("You must log in.", "Login Required");
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
            }
        }
    
    這會建立儲存目前 Live Connect 工作階段的成員變數，以及處理驗證程序的方法。

	>[AZURE.NOTE]您在向服務要求新的權杖之前，應該先嘗試使用快取的 Live 驗證權杖或行動服務授權權杖。如果不這麼做，一旦同時間有許多客戶嘗試啟動您的應用程式，您可能會遇到使用量相關的問題。如需如何快取此權杖的範例，請參閱[開始使用驗證](../mobile-services-windows-store-dotnet-get-started-users.md#tokens)

5. 將現有 **OnNavigatedTo** 方法覆寫中的 **RefreshTodoItems** 方法呼叫註解化或刪除。

	如此能避免在使用者通過驗證之前載入資料。接著，您要新增按鈕以啟動登入程序。

6. 將下列程式碼片段新增至 MainPage 類別：

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
7. 在 Windows 市集應用程式專案中，開啟 MainPage.xaml 專案檔案，並將以下 **Button** 元素新增至定義 **Save** 按鈕的元素前方：

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

8. 針對 Windows Phone 市集應用程式專案重複上一個步驟，不過這次請將 **Button** 新增至 **TitlePanel** 中，使其位於 **TextBlock** 元素後方。
		
9. 按 F5 鍵執行應用程式，並以您的 Microsoft 帳戶登入 Live Connect。

	成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

10. 以滑鼠右鍵按一下 Windows Phone 市集應用程式專案、按一下 [設定為啟始專案]，然後重複上一個步驟以驗證 Windows Phone 市集應用程式是否也能正常運作。

現在，由您其中一個註冊的識別提供者驗證的任何使用者都能存取 *TodoItem* 資料表。若要更善加保護使用者專屬的資料，您還必須實作授權。若要進行，您要取得指定使用者的使用者識別碼，然後用來判斷使用者對於指定資源具備何種層級的存取權。

## <a name="next-steps"> </a>後續步驟

在下一堂教學課程[使用指令碼授權使用者]中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。如需關於如何使用其他識別提供者以進行驗證的詳細資訊，請參閱[開始使用驗證]。您可以在[行動服務 .NET 作法概念參考資料]中深入了解如何使用搭配 .NET 的行動服務。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[將行動服務新增至現有應用程式]: ../mobile-services-windows-store-dotnet-get-started-data.md
[開始使用驗證]: ../mobile-services-windows-store-dotnet-get-started-users.md
[使用指令碼授權使用者]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[行動服務 .NET 作法概念參考資料]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
 

<!---HONumber=July15_HO2-->