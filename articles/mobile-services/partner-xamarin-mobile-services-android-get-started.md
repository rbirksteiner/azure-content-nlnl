<properties 
	pageTitle="開始使用 Xamarin.Android 的行動服務" 
	writer="craigd" 
	description="了解如何透過 Xamarin.Android 應用程式使用 Azure 行動服務。" 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="06/09/2015" 
	ms.author="lindydonna"/>

# <a name="getting-started"></a>開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>本教學課程將示範如何使用 Azure 行動服務，以將雲端後端服務新增至 Xamarin.Android 應用程式。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單<em>待辦事項</em>應用程式。</p>
<p>以下是完成應用程式的螢幕擷取畫面：</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-xamarin-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">10:05</span></div>
</div>

![][0]

完成本教學課程需要 [Xamarin.Android]，Xamarin.Android 將安裝 Xamarin Studio 和 Visual Studio 外掛程式 (在 Windows 上) 以及最新的 Android 平台。需要 Android 4.2 SDK 或更新版本。

下載的快速入門專案包含 Xamarin.Android 的 Azure 行動服務元件。雖然這個專案的目標是 Android 4.2 或以上的版本，不過行動服務 SDK 只需要 Android 2.2 或以上的版本。

> [AZURE.IMPORTANT]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，您可以註冊 Azure 試用版並取得高達 10 項的免費行動服務。此外，在試用期間結束後您仍可繼續使用這些服務。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5"%20target="_blank)。

## <a name="create-new-service"> </a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的 Xamarin.Android 應用程式

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將建立可連接到您行動服務的新 Xamarin.Android 應用程式。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2. 在快速入門索引標籤中，按一下 [Choose platform] 下的 [Xamarin.Android]，並展開 [Create a new Android app]。

	![][6]

	這將顯示三個簡單步驟，可用來建立連接到您行動服務的 Xamarin.Android 應用程式。

	![][7]

3. 按一下 [Create TodoItem table] 以建立儲存應用程式資料的資料表。

4. 按一下 [Download and run app] 下的 [下載]。

	這將會下載與行動服務連接的範例_待辦事項_應用程式專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

## 執行您的 Android 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1. 瀏覽至您儲存此壓縮專案檔案的位置，並在您的電腦上展開檔案。

2. 在 Xamarin Studio 或 Visual Studio 中，依序按一下 [檔案]、[開啟]，瀏覽至未壓縮的範例檔案，並選取 **XamarinTodoQuickStart.Android.sln** 以將其開啟。

 	![][8]

	![][9]

3. 按 [執行] 按鈕，以建立專案並啟動應用程式。系統將要求您選取模擬器或連接的 USB 裝置。

	> [AZURE.NOTE]若要能夠在 Android 模擬器中執行此專案，您必須至少定義一個 Android 虛擬裝置 (AVD)。請使用 AVD 管理員來建立和管理這些裝置。

4. 在應用程式中輸入有意義的文字 (例如 _Complete the tutorial_)，然後按一下 [新增]。

	![][10]

	如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。

	> [AZURE.NOTE]您可以檢閱存取行動服務以查詢與插入資料的程式碼，您可在 ToDoActivity.cs C# 檔案中找到此程式碼。

6. 回到管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItems] 資料表。

	![][11]

	如此可讓您瀏覽由應用程式插入資料表中的資料。

	![][12]

## <a name="next-steps"> </a>後續步驟
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
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[開始使用離線資料同步]: mobile-services-xamarin-android-get-started-offline-data.md
[開始使用驗證]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[開始使用推播通知]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[Xamarin.Android]: http://xamarin.com/download
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure]: http://azure.microsoft.com/
[Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO1-->