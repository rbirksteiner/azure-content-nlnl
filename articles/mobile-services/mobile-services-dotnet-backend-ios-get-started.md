<properties
	pageTitle="在 iOS 應用程式中開始使用 Azure 行動服務"
	description="遵循此教學課程，可開始使用 Azure 行動服務進行 iOS 開發。"
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
	ms.date="05/13/2015"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

本教學課程說明如何使用 Azure 行動服務在 iOS 應用程式中新增雲端型後端服務。在本教學課程中，您將建立新的行動服務和簡單的_待辦事項清單_應用程式，後者會在前者儲存應用程式資料。行動服務會使用 .NET 和 Visual Studio 於伺服器端商務邏輯。若要以 JavaScript 中伺服器端商務邏輯建立行動服務，請參閱本主題中的 [JavaScript 後端版本]。

> [AZURE.NOTE]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得[免費的行動服務，即使在試用期結束之後仍可繼續使用這些服務](http://azure.microsoft.com/pricing/details/mobile-services/)。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-ios-get-started%2F)。

## <a name="create-new-service"> </a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 將行動服務與應用程式下載至您的本機電腦

現在您已建立行動服務，下載可以在本機執行的專案。

1. 按一下您剛剛建立的行動服務，然後在 [快速入門] 索引標籤中，按一下 [**選擇平台**] 下的 [**iOS**]，並展開 [**建立新的 iOS 應用程式**]。

2. 在您的 Windows 電腦上，按一下 [**下載您的服務並發佈至雲端**] 下的 [**下載**]。這會下載實作行動服務的 Visual Studio 專案。請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

3. 在您的 Mac 上，按一下 [**下載並執行您的應用程式**] 下的 [**下載**]。如此會下載與您行動服務連線之範例應用程式 _To do list_ 的專案，以及行動服務 iOS SDK。請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

## 測試行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## 發佈行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


## 執行新的 iOS 應用程式

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]


## <a name="next-steps"> </a>後續步驟

這會說明如何對執行於 Azure 中的行動服務執行新的用戶端應用程式。您必須設定 Web 伺服器和防火牆以允許來自您 iOS 開發電腦的存取，才能對執行於本機電腦上的行動服務測試 iOS 應用程式。如需詳細資訊，請參閱[設定本機 Web 伺服器以允許連接到本機行動服務](mobile-services-dotnet-backend-how-to-configure-iis-express.md)。

了解如何在行動服務中執行其他重要工作：

* [將行動服務新增至現有的應用程式] <br/>深入了解使用行動服務來儲存與查詢資料。

* [開始使用離線資料同步] <br/>了解如何使用離線資料同步，讓您的應用程式更穩健及具備回應力。

* [將驗證新增至現有應用程式] <br/>了解如何向身分識別提供者驗證應用程式的使用者。

* [將推播通知新增至現有應用程式] <br/>了解如何將非常基本的推播通知傳送至應用程式。

* [行動服務 .NET 後端疑難排解]<br/> 了解如何診斷及修復行動服務 .NET 後端可能發生的問題。

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[將行動服務新增至現有的應用程式]: mobile-services-dotnet-backend-ios-get-started-data.md
[開始使用離線資料同步]: mobile-services-ios-get-started-offline-data.md
[將驗證新增至現有應用程式]: mobile-services-dotnet-backend-ios-get-started-users.md
[將推播通知新增至現有應用程式]: mobile-services-dotnet-backend-ios-get-started-push.md
[行動服務 .NET 後端疑難排解]: mobile-services-dotnet-backend-how-to-troubleshoot.md

[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[JavaScript 後端版本]: mobile-services-ios-get-started.md
 

<!---HONumber=July15_HO2-->