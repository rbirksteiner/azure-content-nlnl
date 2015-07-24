<properties 
	pageTitle="在 Xamarin Android 應用程式中開始使用行動服務中的驗證 - Azure 行動服務" 
	description="了解如何使用行動服務透過眾多識別提供者驗證 Xamarin Android 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" 
	services="mobile-services" 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="donnam"/>

# 在行動服務中開始使用驗證

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [限制只有經驗證的使用者具有資料表的權限]
3. [將驗證新增至應用程式]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務]。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

<ol start="6">
<li><p>在 Visual Studio 或 Xamarin Studio 中，在裝置或模擬器上執行用戶端專案。確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。</p>
   
   	<p>這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

1. 將下列屬性新增至 **TodoActivity** 類別：

			private MobileServiceUser user;

2. 將下列方法加入 **TodoActivity** 類別：

	        private async Task Authenticate()
	        {
	            try
	            {
	                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	        }

    這會建立新的方法來處理驗證程序。使用者透過 Facebook 登入來驗證。將出現對話方塊來顯示已驗證的使用者的識別碼。

    > [AZURE.NOTE]如果您使用的身分識別提供者不是 Facebook，請將傳給上述 **LoginAsync** 的值變更為下列其中一個：_MicrosoftAccount_、_Twitter_、_Google_ 或 _WindowsAzureActiveDirectory_。

3. 在 **onCreate** 方法中，在具現化 `MobileServiceClient` 物件的程式碼後面加入下列這一行程式碼。

		// Get the Mobile Service Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

	此呼叫會啟動驗證程序再非同步等候它。


4. 從 [執行] 功能表，按一下 [執行] 來啟動應用程式，並以您選擇的身分識別提供者登入。

   	成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。


<!-- ## <a name="next-steps"> </a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. 
 -->
<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有經驗證的使用者具有資料表的權限]: #permissions
[將驗證新增至應用程式]: #add-authentication
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動服務]: mobile-services-dotnet-backend-xamarin-android-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-xamarin-android-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-xamarin-android-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO2-->