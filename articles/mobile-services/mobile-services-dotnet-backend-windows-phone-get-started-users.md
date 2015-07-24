<properties 
	pageTitle="開始使用驗證 (Windows Phone) | 行動開發人員中心" 
	description="了解如何使用行動服務透過眾多識別提供者驗證 Windows Phone 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" 
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
	ms.date="04/29/2015" 
	ms.author="glenga"/>

# 在您的行動服務應用程式中新增驗證

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

## 概觀

本主題說明如何從您的 Windows Phone 應用程式在 Azure 行動服務中驗證使用者。在本教學課程中，您會使用行動服務所支援的身份識別提供者將驗證新增至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

>[AZURE.NOTE]本主題僅支援 Windows Phone 8.0 和 Windows Phone 8.1 Silverlight 應用程式。如果您有 Windows Phone 市集 8.1 或通用 Windows 應用程式，請改為依照[本主題的通用 Windows 應用程式版本](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md)進行。

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務]。

## 註冊您的應用程式以驗證與設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##  限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

<ol start="6">
<li>在 Visual Studio 中，開啟用戶端應用程式專案，確定 App.xaml.cs 中的 <strong>MobileServiceClient</strong> 執行個體設定為使用行動服務的雲端 URL。</li> 
<li><p>按 F5 鍵執行此快速入門型應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。</p>
   
   	<p>這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

## 將驗證新增至應用程式

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../../includes/mobile-services-windows-phone-authenticate-app.md)]

## 將授權權杖儲存在用戶端上

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../../includes/mobile-services-windows-phone-authenticate-app-with-token.md)]

##  後續步驟

在下一個[行動服務使用者的伺服器端授權][Authorize users with scripts]教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。您可以在[行動服務 .NET 作法概念參考資料]中深入了解如何使用搭配 .NET 的行動服務。

<!-- Anchors. -->


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Phone apps by using Live Connect]: mobile-services-windows-phone-single-sign-on.md
[開始使用行動服務]: ../mobile-services-dotnet-backend-windows-phone-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-windows-phone-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[行動服務 .NET 作法概念參考資料]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md
 

<!---HONumber=July15_HO2-->