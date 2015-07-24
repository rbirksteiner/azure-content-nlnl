<properties 
	pageTitle="開始在 Xamarin Android 中使用行動應用程式的驗證" 
	description="了解如何使用行動應用程式透過眾多識別提供者驗證 Xamarin Android 應用程式使用者，包括 AAD、Google、Facebook、Twitter 和 Microsoft。" 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/23/2015" 
	ms.author="mahender"/>

# 將驗證新增至 Xamarin.Android 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

本主題說明如何從用戶端應用程式驗證應用程式服務行動應用程式的使用者。在本教學課程中，您將使用應用程式服務支援的身分識別提供者，將驗證新增至快速入門專案。由行動應用程式成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程以行動應用程式快速入門為基礎。您也必須先完成[建立 Xamarin.Android 應用程式教學課程]。

##<a name="register"></a>註冊應用程式進行驗證，並設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="7">
<li><p>在 Visual Studio 或 Xamarin Studio 中，在裝置或模擬器上執行用戶端專案。確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。</p>
   
   	<p>這是因為應用程式嘗試以未驗證的使用者身分存取您的行動應用程式程式碼，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求應用程式服務的資源之前必須驗證使用者。

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

		// Get the Mobile App Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

	此呼叫會啟動驗證程序再非同步等候它。


4. 從 [執行] 功能表，按一下 [執行] 來啟動應用程式，並以您選擇的身分識別提供者登入。

   	當您成功登入後，應用程式將會顯示 todo 項目的清單，您可以對資料進行更新。


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[建立 Xamarin.Android 應用程式教學課程]: app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=62-->