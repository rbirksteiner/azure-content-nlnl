
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
	ms.date="05/13/2015" 
	ms.author="ricksal"/>


# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到 Android 應用程式。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單_待辦事項_應用程式。您所將建立的行動服務，會使用 Visual Studio 與支援的 .NET 語言撰寫伺服器端商務邏輯，並管理行動服務。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [JavaScript 後端版本](mobile-services-android-get-started.md)。

以下是完成應用程式的螢幕擷取畫面：

![][0]

完成本教學課程需要 [Android Developer Tools][Android Studio]，其中包括 Android Studio 整合式開發環境，以及最新的 Android 平台。需要 Android 4.2 或以上的版本。

下載的快速入門專案包含 Mobile Services SDK for Android。

> [AZURE.IMPORTANT]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，您可以註冊 Azure 試用版並取得高達 10 項的免費行動服務。此外，在試用期間結束後您仍可繼續使用這些服務。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28)。

<!-- -->

> [AZURE.NOTE]如果您想要查看本教學課程的 Eclipse 版本，請移至：[開始使用 (Eclipse)]。

## <a name="create-new-service"> </a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 將行動服務下載至您的本機電腦

您已建立行動服務，接著請下載可在您的本機電腦或虛擬機器上執行的個人化行動服務專案。

1. 按一下您剛剛建立的行動服務，然後在 [快速入門] 索引標籤中，按一下 [選擇平台] 下的 [Android]，並展開 [Create a new Android app]。

	![][1]

2. 下載並安裝 [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) 或更新版本 (若您尚未這麼做)。

3. 在步驟 2 中，按一下 [下載您的服務並發佈至雲端] 下的 [下載]。

	這會下載實作行動服務的 Visual Studio 專案。請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

## 測試行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## 發佈行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## 建立新的 Android 應用程式

在本節中，您將建立與行動服務連線的新 Android 應用程式。

1. 在[管理入口網站]中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

2. 在快速入門索引標籤中，按一下 [Choose platform] 下的 [Android]，並展開 [Create a new Android app]。
 
	![][2]

3. 如果您尚未這麼做，請在您的本機電腦或虛擬機器下載並安裝 [Android Developer Tools][Android SDK]。

4. 在 [Download and run your app] 下，按 [下載]。

  	這將會下載與行動服務連接的範例_待辦事項_應用程式專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

## 執行您的 Android 應用程式

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]

## <a name="next-steps"> </a>後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* [開始使用驗證] <br/>了解如何向身分識別提供者驗證應用程式的使用者。

* [開始使用推播通知] <br/>了解如何將非常基本的推播通知傳送到應用程式。

* [行動服務 .NET 後端疑難排解]<br/> 了解如何診斷及修復行動服務 .NET 後端可能發生的問題。

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs-AS.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android-AS.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[開始使用 (Eclipse)]: mobile-services-dotnet-backend-android-get-started-EC.md
[Get started with data]: mobile-services-dotnet-backend-android-get-started-data.md
[開始使用驗證]: mobile-services-dotnet-backend-android-get-started-users.md
[開始使用推播通知]: mobile-services-dotnet-backend-android-get-started-push.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[行動服務 .NET 後端疑難排解]: mobile-services-dotnet-backend-how-to-troubleshoot.md

[管理入口網站]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO1-->