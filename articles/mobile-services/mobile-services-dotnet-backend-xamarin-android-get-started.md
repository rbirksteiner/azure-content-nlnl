<properties
	pageTitle="在 Xamarin Android 應用程式中開始使用行動服務 - Azure 行動服務"
	description="遵循此教學課程，可開始使用 Azure 行動服務進行 Xamarin Android 開發。"
	services="mobile-services"
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="donnam"/>

# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到 Xamarin Android 應用程式。在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單_待辦事項_應用程式。您所將建立的行動服務，會使用 Visual Studio 與支援的 .NET 語言撰寫伺服器端商務邏輯，並管理行動服務。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [JavaScript 後端版本]。

>[AZURE.NOTE]本主題將說明如何使用 Azure 管理入口網站建立新的行動服務專案。使用 Visual Studio 2013 Update 2，可讓您將新的行動服務專案新增至現有的 Visual Studio 方案。如需詳細資訊，請參閱[快速入門：新增行動服務 (.NET 後端)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

以下是完成應用程式的螢幕擷取畫面：

![][0]

完成本教學課程是 Xamarin Android 應用程式所有其他行動服務教學課程的先決條件。

>[AZURE.NOTE]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，您可以註冊 Azure 試用版並取得高達 10 項的免費行動服務。此外，在試用期間結束後您仍可繼續使用這些服務。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started" target="_blank">Azure 免費試用</a>。<br />本教學課程需使用 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>。您可以使用免費試用版。

## 建立新的行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 建立新的 Xamarin Android 應用程式

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將為行動服務下載新的 Xamarin Android 應用程式和服務專案。

1. 在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2. 在快速入門索引標籤中，按一下 [Choose platform] 下的 [Xamarin]，並展開 [Create a new Xamarin app]。

   	![][6]

   	這將顯示三個簡單步驟，可用來建立連接到您行動服務的 Xamarin Android 應用程式。

  	![][7]

3. 在您的本機電腦或虛擬機器下載並安裝 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> (如果您尚未這麼做)。

4. 下載並安裝 [Xamarin Studio] 或 Xamarin for Visual Studio (若您尚未這麼做)。

5. 在 [Download and publish your service to the cloud] 下選取 [Android]，然後按一下 [下載]。

  	這會下載一個方案，其中包含行動服務的專案，以及與行動服務連接的範例_待辦事項_應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

6. 下載您的發行設定檔、將下載的檔案儲存至本機電腦，並記下儲存位置。

## 測試行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## 發佈行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## 執行 Xamarin Android 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1. 在 Visual Studio 或 Xamarin Studio 中，導覽至行動服務方案內的用戶端專案。

	![][8]

	![][9]

2. 按 [執行] 按鈕，以建立專案並啟動應用程式。系統將要求您選取模擬器或連接的 USB 裝置。

	> [AZURE.NOTE]若要能夠在 Android 模擬器中執行此專案，您必須至少定義一個 Android 虛擬裝置 (AVD)。使用 AVD Manager 來建立與管理這些裝置。

3. 在應用程式中輸入有意義的文字 (例如 _Complete the tutorial_)，然後按一下加號 (**+**) 圖示。

	![][10]

	如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。

	> [AZURE.NOTE]您可以檢閱存取行動服務以查詢與插入資料的程式碼，您可在 ToDoActivity.cs C# 檔案中找到此程式碼。

## 後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* [開始使用離線資料同步] <br/>了解快速入門如何使用離線資料同步，讓應用程式更穩健及具備回應力。

* [開始使用驗證] <br/>了解如何向身分識別提供者驗證應用程式的使用者。

* [開始使用推播通知] <br/>了解如何將非常基本的推播通知傳送到應用程式。

* [行動服務 .NET 後端疑難排解]<br/> 了解如何診斷及修復行動服務 .NET 後端可能發生的問題。

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[開始使用離線資料同步]: mobile-services-xamarin-android-get-started-offline-data.md
[開始使用驗證]: mobile-services-dotnet-backend-xamarin-android-get-started-users.md
[開始使用推播通知]: mobile-services-dotnet-backend-xamarin-android-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Management Portal]: https://manage.windowsazure.com/
[JavaScript 後端版本]: mobile-services-android-get-started.md
[Get started with data in Mobile Services using Visual Studio 2012]: ../mobile-services-windows-store-dotnet-get-started-data-vs2012.md
[行動服務 .NET 後端疑難排解]: mobile-services-dotnet-backend-how-to-troubleshoot.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 

<!---HONumber=July15_HO1-->