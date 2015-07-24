<properties
	pageTitle="開始使用 Xamarin iOS 應用程式的行動服務"
	description="遵循此教學課程，可開始使用 Azure 行動服務進行 Xamarin iOS 開發。"
	services="mobile-services"
	documentationCenter="xamarin"
	authors="conceptdev"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/16/2015"
	ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

本教學課程將示範如何使用 Azure 行動服務，以將雲端後端服務新增至 Xamarin.iOS 應用程式。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單<em>待辦事項清單</em>應用程式。

如果您想要看影片，下方片段播放的步驟與本教學課程相同。

影片：「開始使用 Xamarin 和 Azure 行動服務」(Getting Started with Xamarin and Azure Mobile Services)，示範者為 Xamarin 推廣開發人員 Craig Dunn (片長：10 分 05 秒)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]



以下是完成應用程式的螢幕擷取畫面：

![][0]

完成本教學課程需要 XCode 和 [Xamarin Studio] for OS X，或 Windows 上 Visual Studio 的 Visual Studio 外掛程式。範例將在 iOS 5.0 和更新版本上執行。

> [AZURE.IMPORTANT]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，您可以註冊 Azure 試用版並取得高達 10 項的免費行動服務。此外，在試用期間結束後您仍可繼續使用這些服務。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-new-service"> </a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的 Xamarin.iOS 應用程式

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將建立可連接到您行動服務的新 Xamarin.iOS 應用程式。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2. 在快速入門索引標籤中，按一下 [Choose platform] 下的 [Xamarin.iOS]，並展開 [Create a new Xamarin.iOS app]。

	![][6]

	這將顯示三個簡單步驟，可用來建立連接到您行動服務的 Xamarin.iOS 應用程式。

  	![][7]

3. 若您尚未這麼做，請下載和安裝 Xcode (我們建議最新版 Xcode 6.0 或更新版本) 和 [Xamarin Studio]。

4. 按一下 [Create TodoItems table] 以建立儲存應用程式資料的資料表。

5. 按一下 [Download and run app] 下的 [下載]。

	這會下載與行動服務連接之範例_待辦事項清單_應用程式的專案，以及參考 Xamarin.iOS 的 Azure 行動服務元件。請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

## 執行新的 Xamarin.iOS 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1. 瀏覽至您儲存此壓縮專案檔案的位置、在您的電腦上展開檔案，並使用 Xamarin Studio 或 Visual Studio 來開啟 **XamarinTodoQuickStart.iOS.sln** 解決方案檔案。

	![][8]

	![][9]

2. 按 [執行] 按鈕以建立專案，並在 iPhone 模擬器中啟動應用程式 (此專案的預設選項)。

3. 在應用程式中輸入有意義的文字 (例如 _Complete the tutorial_)，然後按一下加號 (**+**) 圖示。

	![][10]

	如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。

	> [AZURE.NOTE]您可以檢閱存取行動服務以查詢與插入資料的程式碼，您可在 TodoService.cs C# 檔案中找到此程式碼。

4. 回到管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItems] 資料表。

	![][11]

	如此可讓您瀏覽由應用程式插入資料表中的資料。

	![][12]


## 後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* [開始使用離線資料同步] <br/>了解快速入門如何使用離線資料同步，讓應用程式更穩健及具備回應力。

* [開始使用驗證] <br/>了解如何向身分識別提供者驗證應用程式的使用者。

* [開始使用推播通知] <br/>了解如何將極為基本的推播通知傳送到應用程式。

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[開始使用離線資料同步]: /develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
[開始使用驗證]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[開始使用推播通知]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO1-->