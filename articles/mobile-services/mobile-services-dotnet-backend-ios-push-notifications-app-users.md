<properties
	pageTitle="傳送推播通知給已驗證的使用者 (.NET 後端)"
	description="了解如何將推播通知傳送給特定"
	services="mobile-services,notification-hubs"
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

# 傳送推播通知給已驗證的使用者

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

在本主題中，您將了解如何將推播通知傳送給 iOS 上已驗證的使用者。開始本教學課程之前，請先完成[開始使用驗證]和[開始使用推播通知]。

在本教學課程中，您會要求使用者先進行驗證、向通知中心註冊進行推播通知，以及更新伺服器指令碼以傳送這些通知給已驗證的使用者。

##<a name="register"></a>更新服務以要求註冊所需的驗證

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>更新應用程式以在註冊前先登入

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>測試應用程式

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[開始使用驗證]: mobile-services-dotnet-backend-ios-get-started-users.md
[開始使用推播通知]: mobile-services-dotnet-backend-ios-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!---HONumber=July15_HO2-->