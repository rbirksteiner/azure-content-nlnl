<properties
	pageTitle="將驗證加入現有 Azure 行動服務應用程式 (iOS) | 行動開發人員中心"
	description="了解如何使用行動服務透過眾多識別提供者驗證 iOS 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="krisragh"/>

# 將驗證加入現有的 Azure 行動服務應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

在本教學課程中，您可以使用支援的身分識別提供者，將驗證加入快速入門專案。本教學課程以[行動服務快速入門教學課程]為基礎，您必須先完成該教學課程。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

在 Xcode 中，開啟專案。按 [執行] 按鈕，以啟動應用程式。確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的例外狀況。這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 _TodoItem_ 資料表現在需要驗證。

##<a name="add-authentication"></a>將驗證加入應用程式

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>將驗證權杖儲存在應用程式中

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>接續步驟

在下一個教學課程[行動服務使用者的服務端授權中]，您將使用使用者識別碼值篩選傳回的資料。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- URLs. -->
[行動服務使用者的服務端授權中]: mobile-services-dotnet-backend-service-side-authorization.md
[行動服務快速入門教學課程]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with data]: mobile-services-dotnet-backend-ios-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-ios-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=July15_HO2-->