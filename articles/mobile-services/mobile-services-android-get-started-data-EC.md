<properties 
	pageTitle="開始使用資料 (Android) | 行動開發人員中心" 
	description="了解如何開始使用行動服務，在您的 Android 應用程式中使用資料。" 
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
	ms.date="05/06/2015" 
	ms.author="ricksal"/>

# 將行動服務新增至現有的應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data-EC.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>本主題說明如何在 Android 應用程式中使用 Azure 行動服務來處理資料。在本教學課程中，您將下載應用程式，並在記憶體中儲存資料、建立新的行動服務、將行動服務與該應用程式整合，然後登入 Azure 管理入口網站查看執行應用程式時所做的資料變更。</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a><span class="time">15:32</span></div>
</div>

> [AZURE.NOTE]本教學課程是為了協助您充分了解行動服務如何讓您從 Android 應用程式中利用 Azure 來儲存和擷取資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮首先完成教學課程[開始使用行動服務](../get-started-android-EC.md)。
> 
> 如果您想要查看已完成的應用程式的原始程式碼，請到[這裡](https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData)。

若要完成此教學課程，您需要下列項目：

+ 一個 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-android-get-started-data-EC%2F)。 

+ 本教學課程需要[行動服務 Android SDK]、<a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">Android SDK</a> (含 Eclipse 整合式開發環境 (IDE) 和 Android Developer Tools (ADT) 外掛程式)，以及 Android 4.2 或更新版本。

> [AZURE.NOTE]本教學課程提供提示來安裝 Android SDK 和 Mobile Services Android SDK。下載的 GetStartedWithData 專案需要 Android 4.2 或更新版本。不過，Mobile Services SDK 只需要 Android 2.2 或更新版本。

<!-- -->

> [AZURE.NOTE]本教學課程使用最新版的行動服務 SDK。基於回溯相容性，您可以在<a href="http://go.microsoft.com/fwlink/p/?LinkID=280126">這裡</a>找到舊版，但是這些教學課程中包含的程式碼與舊版不相容。

##<a name="download-app"></a>下載 GetStartedWithData 專案

###取得範例程式碼

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code-EC.md)]

###驗證 Android SDK 版本

[AZURE.INCLUDE [確認 SDK](../../includes/mobile-services-verify-android-sdk-version-EC.md)]


###檢查和執行範例程式碼

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code-EC.md)]

##<a name="create-service"></a>在管理入口網站中建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>將新資料表新增至行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>更新應用程式以使用行動服務進行資料存取

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data-EC.md)]

##<a name="test-app"></a>對新的行動服務進行應用程式測試

現在，應用程式已更新為使用行動服務做為後端儲存，您可以使用 Android 模擬器或 Android 手機，在行動服務上測試應用程式。

1. 從 [執行] 功能表，按一下 [執行] 來啟動專案。

	這樣會執行您的應用程式 (以 Android SDK 建立)，該應用程式利用用戶端程式庫傳送查詢，然後從您的行動服務傳回項目。

5. 同樣地，輸入有意義的文字，然後按一下 [加入]。

   	這會傳送新項目以插入至行動服務。

3. 在[管理入口網站]中按一下 [行動服務]，然後按一下您的行動服務。

4. 按一下 [資料] 索引標籤，然後按一下 [瀏覽]。

   	![][9]
  
   	請注意，**TodoItem** 資料表現在包含資料和行動服務產生的一些值，且資料行已自動加入至資料表，以符合應用程式中的 TodoItem 類別。

Android 的**開始使用資料**教學課程到此結束。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 Android 應用程式在行動服務中使用資料的基礎。

接著，請嘗試這些其他的 Android 教學課程：

* [開始使用驗證] <br/>了解如何驗證應用程式的使用者。

* [開始使用推送通知] <br/>了解如何使用行動服務將非常基本的推送通知傳送到應用程式。

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data-EC/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[開始使用驗證]: mobile-services-android-get-started-users.md
[開始使用推送通知]: mobile-services-javascript-backend-android-get-started-push-EC.md

[Azure Management Portal]: https://manage.windowsazure.com/
[管理入口網站]: https://manage.windowsazure.com/
[行動服務 Android SDK]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
 

<!---HONumber=July15_HO2-->