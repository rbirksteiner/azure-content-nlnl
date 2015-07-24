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
	ms.date="04/24/2015"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

本教學課程說明如何使用 Azure 行動服務在 iOS 應用程式中新增雲端型後端服務。

在本教學課程中，您將建立新的行動服務和簡單的_待辦事項清單_應用程式，後者會在前者儲存應用程式資料。您將建立的行動服務會使用 JavaScript 建立伺服器端商務邏輯。若要以 .NET 中伺服器端商務邏輯建立行動服務，請參閱本主題中的 [.NET 後端版本]。

> [AZURE.NOTE]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得[免費的行動服務，即使在試用期結束之後仍可繼續使用這些服務](http://azure.microsoft.com/pricing/details/mobile-services/)。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20 target="_blank")。

## <a name="create-new-service"> </a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的 iOS 應用程式

您可以依照「管理入口網站」中輕鬆的「快速入門」，來建立連接到行動服務的新應用程式：

1. 在管理入口網站中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

2. 在 [快速入門] 索引標籤中，按一下 [**選擇平台**] 下的 [**iOS**]，然後展開 [**建立新的 iOS 應用程式**]。這會顯示步驟，用來建立與您行動服務連線的 iOS 應用程式。

3. 按一下 [Create TodoItem table] 以建立儲存應用程式資料的資料表。

4. 在 [Download and run your app] 下，按 [下載]。如此會下載與您行動服務連線之範例應用程式 _To do list_ 的專案，以及行動服務 iOS SDK。請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

## 執行新的 iOS 應用程式

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

<ol start="4"> <li><p>回到管理入口網站中，按一下 [<strong>資料</strong>] 索引標籤，然後按一下 [<strong>TodoItem</strong>] 資料表。如此可讓您瀏覽由應用程式插入資料表中的資料。<p></li></ol></p>

## <a name="next-steps"> </a>後續步驟
了解如何在行動服務中執行其他重要工作：

* [將行動服務新增至現有的應用程式] <br/>深入了解使用行動服務來儲存與查詢資料。

* [開始使用離線資料同步] <br/>了解如何使用離線資料同步，讓您的應用程式更穩健及具備回應力。

* [將驗證新增至現有應用程式] <br/>了解如何向身分識別提供者驗證應用程式的使用者。

* [將推播通知新增至現有應用程式] <br/>了解如何將非常基本的推播通知傳送至應用程式。


<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[將行動服務新增至現有的應用程式]: mobile-services-dotnet-backend-ios-get-started-data.md
[開始使用離線資料同步]: mobile-services-ios-get-started-offline-data.md
[將驗證新增至現有應用程式]: mobile-services-dotnet-backend-ios-get-started-users.md
[將推播通知新增至現有應用程式]: mobile-services-dotnet-backend-ios-get-started-push.md


[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET 後端版本]: mobile-services-dotnet-backend-ios-get-started.md
 

<!---HONumber=July15_HO2-->