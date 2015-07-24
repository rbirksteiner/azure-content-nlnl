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
	ms.date="05/15/2015" 
	ms.author="ricksal"/>

# 將行動服務新增至現有的 Android 應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

## 摘要

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>本主題說明如何使用 Azure 行動服務，將持續性資料新增至 Android 應用程式。在本教學課程中，您將下載可在記憶體中儲存資料的應用程式、建立新的行動服務、整合此應用程式與行動服務，以便在 Azure 行動服務中 (而非在本機) 儲存和更新資料，然後使用 Azure 管理入口網站來檢視執行應用程式所產生的資料變更。</p>

</div>


<div class="dev-onpage-video-wrapper">
<a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a><span class="time">下午 03:32:00</span></div>
</div>


<p>本教學課程可協助您更詳細了解 Azure 行動服務從 Android 應用程式存放和擷取資料的方式。因此會逐步說明已在行動服務快速入門教學課程中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮首先完成教學課程<a href="/zh-tw/develop/mobile/tutorials/get-started-android">開始使用行動服務</a>。</p>

## 必要條件

若要完成此教學課程，您需要下列項目：

- 一個 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資料，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Azure 免費試用</a>。


- [Azure 行動服務 Android SDK]；
- <a  href="https://developer.android.com/sdk/index.html" target="_blank">Android Studio 整合式開發環境</a>，其中包括 Android SDK 以及 Android 4.2 或更新版本。下載的 GetStartedWithData 專案需要 Android 4.2 或更新版本。不過，Mobile Services SDK 只需要 Android 2.2 或更新版本。

## 下載 GetStartedWithData 專案

###取得範例程式碼

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code.md)]

### 檢查和執行範例程式碼

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

## 在管理入口網站中建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## 將新資料表新增至行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

## 更新應用程式以使用行動服務進行資料存取

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]


## 對新的行動服務進行應用程式測試

現在，應用程式已更新為使用行動服務做為後端儲存，您可以使用 Android 模擬器或 Android 手機，在行動服務上測試應用程式。

1. 在 [執行] 功能表中，按一下 [執行應用程式] 來啟動專案。

	這樣會執行您的應用程式 (以 Android SDK 建立)，該應用程式利用用戶端程式庫傳送查詢，然後從您的行動服務傳回項目。

5. 同樣地，輸入有意義的文字，然後按一下 [加入]。

   	這會傳送新項目以插入至行動服務。

3. 在[管理入口網站]中按一下 [行動服務]，然後按一下您的行動服務。

4. 按一下 [資料] 索引標籤，然後按一下 [瀏覽]。

   	![][9]
  
   	請注意，**TodoItem** 資料表現在包含資料和行動服務產生的一些值，且資料行已自動加入至資料表，以符合應用程式中的 TodoItem 類別。

Android 的**開始使用資料**教學課程到此結束。

## 疑難排解

###驗證 Android SDK 版本

[AZURE.INCLUDE [確認 SDK](../../includes/mobile-services-verify-android-sdk-version.md)]


## 舊版程式碼

如果您想要查看本教學課程的 Eclipse 版本，請移至：[開始透過 Eclipse 使用資料](mobile-services-android-get-started-data-EC.md)。

若要查看 Eclipse 專案中完整版的原始程式碼，請移至<a href="https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/Android">這裡</a>。

如果想要取得前一版 Azure Mobile Services Android SDK 中使用的範例檔案，您可以在[這裡](http://go.microsoft.com/fwlink/p/?LinkID=282122)取得。

## 後續步驟

本教學課程示範讓 Android 應用程式在行動服務中使用資料的基礎。

接下來，可考慮進入下列其中一個教學課程，這些教學課程都是以您在本教學課程中建立的 GetStartedWithData 應用程式為基礎：

* [使用指令檔驗證與修改資料] <br/>深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

* [使用分頁縮小查詢範圍] <br/>了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料系列後，可試著閱讀這些另外的 Android 教學課程：

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
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[使用指令檔驗證與修改資料]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[使用分頁縮小查詢範圍]: /develop/mobile/tutorials/add-paging-to-data-android
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-android
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-android
[Get started with data (Eclipse)]: /documentation/articles/mobile-services-android-get-started-data-EC/
[開始使用驗證]: /develop/mobile/tutorials/get-started-with-users-android
[開始使用推送通知]: /develop/mobile/tutorials/get-started-with-push-android

[Azure Management Portal]: https://manage.windowsazure.com/
[管理入口網站]: https://manage.windowsazure.com/
[Azure 行動服務 Android SDK]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
 

<!---HONumber=July15_HO2-->