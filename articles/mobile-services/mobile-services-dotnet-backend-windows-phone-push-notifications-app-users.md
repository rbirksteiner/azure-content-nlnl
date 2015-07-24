<properties 
	pageTitle="傳送推播通知給已驗證的使用者" 
	description="了解如何將推播通知傳送給特定" 
	services="mobile-services,notification-hubs" 
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
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# 傳送推播通知給已驗證的使用者

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

本主題說明如何將推播通知傳送給任何已註冊之 iOS 裝置上的驗證使用者。不同於先前的[將推播通知新增至您的應用程式]教學課程，本教學課程會變更您的行動服務，要求使用者必須先完成驗證，用戶端才能向通知中心註冊推播通知。此外也會修改註冊，以根據指派的使用者識別碼新增標記。最後會更新伺服器程式碼，而僅將通知傳送給已驗證的使用者，而不是所有的註冊。

本教學課程支援 Windows Phone 8.0 和 Windows Phone 8.1 Silverlight 應用程式。如需 Windows Phone 8.1 市集應用程式，請參閱[本主題的 Windows 市集版本](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md)。

##必要條件 

在開始本教學課程之前，您必須已完成下列行動服務教學課程：

+ [將驗證新增至您的應用程式]<br/>將登入需求新增至 TodoList 範例應用程式。

+ [將推播通知新增至您的應用程式]<br/>使用通知中心，設定 TodoList 範例應用程式的推播通知。

在您完成這兩個教學課程後，您即可防止未經授權的使用者從您的行動服務註冊推播通知。

##<a name="register"></a>更新服務以要求註冊所需的驗證

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>更新應用程式以在註冊前先登入

[AZURE.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](mobile-services-windows-phone-push-notifications-app-users.md)]


##<a name="test"></a>測試應用程式

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[將驗證新增至您的應用程式]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[將推播通知新增至您的應用程式]: mobile-services-dotnet-backend-windows-phone-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO1-->