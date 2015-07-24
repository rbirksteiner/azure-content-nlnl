<properties 
	pageTitle="使用 Live Connect (Windows Phone) | 行動開發人員中心驗證您的應用程式" 
	description="了解如何從 Windows Phone 應用程式在 Azure 行動服務中使用 Live Connect 單一登入。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="glenga"/>

# 使用 Microsoft 帳戶以用戶端管理的驗證方式驗證您的 Windows Phone 應用程式

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)]
##概觀
本主題示範如何使用 Live SDK 從 Windows Phone 8 或 Windows Phone 8.1 Silverlight 應用程式取得 Microsoft 帳戶的驗證權杖。然後您可以使用此權杖來驗證使用 Azure 行動服務的使用者。在本教學課程中，您將使用 Live SDK 將 Microsoft 帳戶驗證新增至現有的專案。成功驗證後，畫面會顯示名稱和使用者識別碼值來歡迎登入的使用者。

>[AZURE.NOTE]本教學課程示範使用用戶端管理的驗證和 Live SDK 的優點。這可讓您更輕鬆地使用行動服務來驗證已登入使用者。您也可以要求額外的範圍，讓您的應用程式也能存取如 OneDrive 的資源。服務管理的驗證則提供更一般化的體驗，並支援多種驗證提供者。如需有關服務管理的驗證的詳細資訊，請參閱[將驗證新增至您的應用程式](mobile-services-windows-phone-get-started-users.md)主題。

本教學課程需要下列各項：

+ [Live SDK]
+ Microsoft Visual Studio 2013 Update 3 或更新版本
+ 您還必須先完成[將行動服務新增至現有的應用程式]教學課程。

##註冊應用程式來使用 Microsoft 帳戶 

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

5. 開啟專案檔案 mainpage.xaml.cs 並新增下列 using 陳述式：

        using Microsoft.Live;      

6. 將下列程式碼片段新增至 MainPage 類別：
	
        private LiveConnectSession session;
        private static string clientId = "<microsoft-account-client-id>";
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            // Create the authentication client using the client ID of the registration.
            LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

            while (session == null)
            {
                LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;
                    LiveConnectClient client = new LiveConnectClient(result.Session);
                    LiveOperationResult meResult = await client.GetAsync("me");
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    MessageBox.Show(message, title, MessageBoxButton.OK);
                }
                else
                {
                    session = null;
                    MessageBox.Show("You must log in.", "Login Required", MessageBoxButton.OK);
                }
            }
        }

    這會建立儲存目前 Live Connect 工作階段的成員變數，以及處理驗證程序的方法。LiveLoginResult 包含提供給行動服務用來驗證使用者的驗證權杖。

7. 在上述的程式碼片段中，將預留位置 `<microsoft-account-client-id>` 取代為您為您的應用程式從 Microsoft 帳戶註冊取得的用戶端識別碼。

5. 將現有 **OnNavigatedTo** 方法覆寫中的 **RefreshTodoItems** 方法呼叫註解化或刪除。

	如此能避免在使用者通過驗證之前載入資料。接著，您要新增按鈕以啟動登入程序。

6. 將下列程式碼片段新增至 MainPage 類別：

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = System.Windows.Visibility.Collapsed;
            RefreshTodoItems();
        }
		
7. 在應用程式專案中，開啟 MainPage.xaml 專案檔案，然後在 **TitlePanel** 中 **TextBlock** 元素的後面新增 **Button** 元素：

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>
		
9. 按 F5 鍵執行應用程式，並以您的 Microsoft 帳戶登入。

   成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

現在，由您其中一個註冊的識別提供者驗證的任何使用者都能存取 *TodoItem* 資料表。若要更善加保護使用者專屬的資料，您還必須實作授權。若要進行，您要取得指定使用者的使用者識別碼，然後用來判斷使用者對於指定資源具備何種層級的存取權。

## <a name="next-steps"> </a>後續步驟

在下一堂教學課程[使用指令碼授權使用者]中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。如需關於如何使用其他識別提供者以進行驗證的詳細資訊，請參閱[開始使用驗證]。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[將行動服務新增至現有的應用程式]: mobile-services-windows-phone-get-started-data.md
[開始使用驗證]: mobile-services-windows-phone-get-started-users.md
[使用指令碼授權使用者]: ../mobile-services-windows-phone-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO2-->