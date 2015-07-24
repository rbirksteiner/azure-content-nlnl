<properties
	pageTitle="開始在 Windows 中使用行動應用程式的驗證 | Azure App Service"
	description="了解如何使用行動應用程式透過眾多識別提供者驗證 Windows 應用程式使用者，包括 AAD、Google、Facebook、Twitter 和 Microsoft。"
	services="app-service\mobile"
	documentationCenter="windows"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/19/2015"
	ms.author="mahender"/>

# 將驗證新增至您的 Windows 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

本主題說明如何從用戶端應用程式驗證應用程式服務行動應用程式的使用者。在本教學課程中，您將使用應用程式服務支援的身分識別提供者，將驗證新增至快速入門專案。由行動應用程式成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程以行動應用程式快速入門為基礎。您也必須先完成[開始使用行動應用程式]教學課程。

##<a name="register"></a>註冊應用程式進行驗證，並設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="7">
<li>在 Visual Studio 中，開啟用戶端應用程式專案，確定 App.xaml.cs 中的 <b>MobileServiceClient</b> 執行個體設定為使用行動應用程式資源的雲端 URL。</li>
<li><p>按 F5 鍵執行此快速入門型應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。</p>

   	<p>這是因為應用程式嘗試以未驗證的使用者身分存取您的行動應用程式程式碼，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求應用程式服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)]


>[AZURE.NOTE]如果您已向 App Service 註冊您的 Windows 市集應用程式套件資訊，則應該為 <em>useSingleSignOn</em> 參數提供 <strong>true</strong> 值來呼叫 <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> 方法。若您沒有這麼做，則每次呼叫登入方法時，您的使用者會繼續看到登入提示。


##<a name="tokens"></a>將驗證權杖儲存在用戶端上

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)]


<!-- Anchors. -->
[Register your app for authentication and configure the App Service]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動應用程式]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
 

<!---HONumber=62-->