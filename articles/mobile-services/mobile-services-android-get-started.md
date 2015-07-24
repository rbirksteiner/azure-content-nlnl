<properties
	pageTitle="開始為 Android 應用程式使用 Azure 行動服務"
	description="遵循此教學課程，可開始使用 Azure 行動服務進行 Android 開發。"
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article" 
	ms.date="06/03/2015"
	ms.author="ricksal"/>

# 開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">


<p>本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到 Android 應用程式。在本教學課程中，您將建立新的行動服務和簡單的<em>待辦事項清單</em>應用程式，後者會在前者儲存應用程式資料。</p>
<p>以下是完成之應用程式的螢幕擷取畫面：</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a><span class="time">7:26</span></div>
</div>

![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

## 必要條件

完成本教學課程需要 [Android Developer Tools][Android Studio]，其中包括 Android Studio 整合式開發環境，以及最新的 Android 平台。需要 Android 4.2 或以上的版本。

下載的快速入門專案包含 Azure Mobile Services SDK for Android。

> [AZURE.IMPORTANT]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28)。


## 建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的 Android 應用程式

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將建立與行動服務連線的新 Android 應用程式。

1.  在管理入口網站中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

2. 在快速入門索引標籤中，按一下 [Choose platform] 下的 [Android]，並展開 [Create a new Android app]。

   	![](./media/mobile-services-android-get-started/mobile-portal-quickstart-android1.png)

   	此處顯示建立與行動服務連線的 Android 應用程式的 3 個簡單步驟。

  	![](./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png)

3. 如果您尚未這麼做，請在您的本機電腦或虛擬機器下載並安裝 [Android Developer Tools][Android SDK]。

4. 按一下 [Create TodoItem table] 以建立儲存應用程式資料的資料表。


5. 立即下載您的應用程式：
	- 最新的應用程式版本使用行動服務 Android SDK 2.0。您可以從<a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">這裡</a>下載該版本。按一下 [**下載 Zip**]，將它解壓縮，專案會出現在 Android 資料夾下的 GettingStarted 中。

	- 較早的版本會使用先前版本的 SDK。若要使用它，請在 [**下載及執行您的應用程式**] 下方，按一下 [**下載**]。這將會下載與行動服務連接的範例_待辦事項_應用程式專案。專案檔案已壓縮，請瀏覽到其位置，並將檔案解壓縮到您的電腦上。


## 執行您的 Android 應用程式

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]

### 看看程式碼 (選擇性)

如果您想要查看已完成的應用程式的原始程式碼，請到[這裡](https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStarted/AndroidStudio)。


如果您想要查看本教學課程的 Eclipse 版本，請移至：[開始使用 (Eclipse)](mobile-services-android-get-started-EC.md)。

## <a name="next-steps"> </a>後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* [開始使用資料] <br/>深入了解使用行動服務來儲存與查詢資料。

* [開始使用驗證] <br/>了解如何向身分識別提供者驗證應用程式的使用者。

* [開始使用推播通知] <br/>了解如何將極為基本的推播通知傳送到應用程式。




<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Get started (Eclipse)]: mobile-services-android-get-started-EC.md
[開始使用資料]: mobile-services-android-get-started-data.md
[開始使用驗證]: mobile-services-android-get-started-users.md
[開始使用推播通知]: mobile-services-javascript-backend-android-get-started-push.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=62-->