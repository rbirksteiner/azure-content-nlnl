<properties
	pageTitle="將行動服務新增至現有的應用程式 (iOS) | 行動開發人員中心"
	description="了解如何開始使用行動服務，在您的 iOS 應用程式中使用資料。"
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

# 將行動服務新增至現有的應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

在本教學課程中，您要下載一個將資料儲存在記憶體中的現有應用程式，然後變更為使用 Azure 行動服務。

在開始本教學課程之前，必須先完成[快速入門]。您將會重複使用在＜快速入門＞中所建立的行動服務。

##<a name="download-app"></a>下載 GetStartedWithData 專案

本教學課程以 [GetStartedWithData iOS 應用程式]為基礎。該應用程式與[快速入門]的相同，差別只在於新增的項目會儲存在記憶體中。

下載 [GetStartedWithData iOS 應用程式]。在 Xcode 中開啟專案，並檢查 **TodoService.m**。有八行 **// TODO** 註解，指定讓此應用程式運作的步驟。

##<a name="update-app"></a>更新應用程式以使用行動服務進行資料存取

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>測試應用程式

1. 在 Xcode 中，按一下 [執行] 啟動應用程式。輸入文字並且按一下 [+] 新增項目至 todo 清單。

2. 在 Azure 管理入口網站中，按一下 [行動服務]，然後按一下您的行動服務。按一下 [資料] 索引標籤，然後按一下 [瀏覽]。請注意，[TodoItem] 資料表現在包含資料和行動服務產生的識別碼值，且資料行已自動新增至資料表，以符合應用程式中的 TodoItem 類別。

<!-- Anchors. -->
[Download the iOS app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784


[快速入門]: ../mobile-services-javascript-backend-ios-get-started.md
[GetStartedWithData iOS 應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=268622

<!---HONumber=July15_HO2-->