<properties 
	pageTitle="使用 Active Directory Authentication Library 單一登入驗證您的應用程式 (Windows 市集) | 行動開發人員中心" 
	description="了解如何在 Windows 市集應用程式中驗證使用者的單一登入。" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="wesmc"/>

# 使用 Active Directory Authentication Library 單一登入驗證您的應用程式

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##概觀

在本教學課程中，您使用 Active Directory Authentication Library 將驗證新增至快速入門專案，以支援使用 Azure Active Directory 的[用戶端主導登入作業](http://msdn.microsoft.com/library/azure/jj710106.aspx)。若要支援使用 Azure Active Directory 的[服務主導登入作業](http://msdn.microsoft.com/library/azure/dn283952.aspx)，請從[將驗證新增至您的行動服務應用程式](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md)教學課程開始。

要能夠驗證使用者，您必須向 Azure Active Directory (AAD) 註冊您的應用程式。此作業有兩個階段。第一，您必須註冊行動服務，並公開其權限。第二，您必須註冊 Windows 市集應用程式，並為其授與對這些權限的存取權


>[AZURE.NOTE]本教學課程旨在協助您深入了解如何透過行動服務，使用[用戶端主導登入作業](http://msdn.microsoft.com/library/azure/jj710106.aspx)對 Windows 市集應用程式執行單一登入 Azure Active Directory 驗證。如果這是您第一次接觸行動服務，請先完成教學課程[開始使用行動服務]。


##必要條件

本教學課程需要下列各項：

* 執行於 Windows 8.1 的 Visual Studio 2013。
* 完成[開始使用行動服務]或[開始使用資料]教學課程。
* Microsoft Azure 行動服務 SDK NuGet 封裝
* Active Directory Authentication Library NuGet 封裝 

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../../includes/mobile-services-dotnet-adal-register-service.md)]

##向 Azure Active Directory 註冊您的應用程式

若要向 Azure Active Directory 註冊應用程式，您必須將它與 Windows 市集產生關聯，並具有該應用程式的封裝安全性識別碼 (SID)。封裝 SID 可透過 Azure Active Directory 中的原生應用程式設定進行註冊。


###將應用程式與新的市集應用程式名稱產生關聯

1. 在 Visual Studio 中，以滑鼠右鍵按一下用戶端應用程式專案，然後依序按一下 [市集] 和 [將應用程式與市集建立關聯]。

    ![][1]

2. 登入您的開發人員中心帳戶。

3. 輸入您要為應用程式保留的應用程式名稱，然後按一下 [保留]。

    ![][2]

4. 選取新的應用程式名稱，然後按 [下一步]。

5. 按一下 [關聯]，將應用程式與市集名稱產生關聯。


###擷取應用程式的封裝 SID。

現在，您必須擷取將使用原生應用程式設定進行設定的封裝 SID。

1. 登入您的 [Windows 開發人員中心儀表板]，然後在應用程式上按一下 [編輯]。

    ![][3]

2. 然後按一下 [服務]

    ![][4]

3. 然後按一下 [Live 服務網站]。

    ![][5]

4. 從頁面頂端複製您的封裝 SID。

    ![][6]

###建立原生應用程式註冊

1. 導覽至 **Azure 管理入口網站**中的 [Active Directory]，然後按一下您的目錄。

    ![][7]

2. 按一下頂端的 [應用程式] 索引標籤，然後按一下以**新增**應用程式。

    ![][8]

3. 按一下 [Add an application my organization is developing]。

4. 在 [新增應用程式精靈] 中，輸入應用程式的 [名稱]，然後按一下 [原生用戶端應用程式] 類型。接著，按一下以繼續。

    ![][9]

5. 在 [重新導向 URI] 方塊中，貼上您先前複製的應用程式封裝 SID，然後按一下以完成原生應用程式註冊。

    ![][10]

6. 按一下原生應用程式的 [設定] 索引標籤，並複製 [用戶端識別碼]。稍後您將會需要此資訊。

    ![][11]

7. 將頁面向下捲動至 [其他應用程式的權限] 區段，並對稍早註冊的行動服務應用程式授與完整存取權。然後按一下 [儲存]

    ![][12]

已在 AAD 中設定您的行動服務接收來自應用程式的單一登入。



##將行動服務設定為需要驗證

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##將驗證程式碼新增至用戶端應用程式

1. 在 Visual Studio 中開啟您的 Windows 市集用戶端應用程式專案。

[AZURE.INCLUDE [mobile-services-dotnet-adal-install-nuget](../../includes/mobile-services-dotnet-adal-install-nuget.md)]

4. 在 Visual Studio 的 [方案總管] 視窗中開啟 MainPage.xaml.cs 檔案，然後使用陳述式新增下列項目。

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;


5. 將下列程式碼新增至 MainPage 類別，以宣告 `AuthenticateAsync` 方法。

        private MobileServiceUser user; 
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>"; 
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID, (Uri) null);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                } 
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            } 
        }

6. 在上述 `AuthenticateAsync` 方法的程式碼中，將 **INSERT-AUTHORITY-HERE** 取代成您佈建應用程式的租用戶名稱，其格式應為 https://login.windows.net/tenant-name.onmicrosoft.com。此值可從 [Azure 管理入口網站]複製到 Azure Active Directory 的 [網域] 索引標籤以外。

7. 在上述 `AuthenticateAsync` 方法的程式碼中，將 **INSERT-RESOURCE-URI-HERE** 替換為您行動服務的「應用程式識別碼 URI」。如果您依照[如何向 Azure Active Directory 註冊]主題的內容操作，您的應用程式識別碼 URI 應類似於 https://todolist.azure-mobile.net/login/aad。

8. 在上述 `AuthenticateAsync` 方法的程式碼中，將 **INSERT-CLIENT-ID-HERE** 取代為您從原生用戶端應用程式中複製的用戶端識別碼。

9. 在 Visual Studio 的 [方案總管] 視窗中，開啟用戶端專案中的 Package.appxmanifest 檔案。按一下 [功能] 索引標籤，然後啟用 [企業應用程式] 和 [私人網路 (用戶端和伺服器)]。儲存檔案。

    ![][14]

10. 在 MainPage.cs 檔案中更新 `OnNavigatedTo` 事件處理常式，以呼叫 `AuthenticateAsync` 方法，如下所示。

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            await RefreshTodoItems();
        }


##使用驗證測試用戶端

1. 在 Visual Studio 中，執行用戶端應用程式。
2. 您會看見登入 Azure Active Directory 的提示。  
3. 應用程式會驗證並傳回 todo 項目。

    ![][15]




<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-app-manage-manifest.png
[1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-vs-associate-app.png
[2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-vs-reserve-store-appname.png
[3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-edit.png
[4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-services.png
[5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-live-services-site.png
[6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-package-sid.png
[7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-select-aad.png
[8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-aad-applications-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-selection.png
[10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-sid-redirect-uri.png
[11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-client-id.png
[12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-add-permissions.png
[14]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-package-appxmanifest.png
[15]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-app-run.png

<!-- URLs. -->
[如何向 Azure Active Directory 註冊]: mobile-services-how-to-register-active-directory-authentication.md
[Active Directory]: https://manage.windowsazure.com/
[Azure 管理入口網站]: https://manage.windowsazure.com/
[開始使用資料]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[開始使用行動服務]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Windows 開發人員中心儀表板]: http://go.microsoft.com/fwlink/p/?LinkID=266734

<!---HONumber=July15_HO2-->