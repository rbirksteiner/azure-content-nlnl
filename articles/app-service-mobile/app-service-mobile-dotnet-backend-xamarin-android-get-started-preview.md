<properties
	pageTitle="在 Xamarin Android 應用程式中開始使用 Azure 行動應用程式 - Azure 行動應用程式"
	description="遵循此教學課程，可開始使用 Azure 行動應用程式進行 Xamarin Android 開發。"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="chrisanderson"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="05/01/2015"
	ms.author="chrande"/>

# <a name="getting-started"> </a>建立 Xamarin Android 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

本教學課程示範如何使用 Azure Mobile Apps，將雲端型後端服務新增到 Xamarin Android 應用程式。在本教學課程中，您將建立新的 .NET 服務，並建立可在 .NET 後端中儲存應用程式資料的簡單_待辦事項_ 應用程式。

以下是完成應用程式的螢幕擷取畫面：

![][0]

完成本教學課程是 Xamarin Android 應用程式所有其他 Azure 行動應用程式教學課程的先決條件。

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得多達 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[嘗試 App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)，即可在 App Service 中立即建立短期入門行動應用程式。不需要信用卡；沒有承諾。

## 建立新的行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 建立新的 Xamarin Android 應用程式

建立行動應用程式後端之後，您可以依照 [Azure 入口網站]中的簡易快速入門，建立新的應用程式或修改現有的應用程式，以連接到您的行動應用程式後端。

在本節中，您將為行動應用程式下載新的 Xamarin Android 應用程式和 .NET 後端服務專案。

1. 在 Azure 入口網站中，依序按一下 [**瀏覽**]、[**行動應用程式**]，然後按一下您剛建立的行動應用程式。

2. 在刀鋒視窗頂端，按一下 [**加入用戶端**] 並展開 **Xamarin.Android**。

    ![][6]

    這將顯示三個簡單步驟，可用來建立連接到您行動應用程式後端的 Xamarin Android 應用程式。


3. 在您的本機電腦或虛擬機器下載並安裝 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> (如果您尚未這麼做)。

4. 下載並安裝 [Xamarin Studio] (如果您尚未這麼做)。您也可以使用 Xamarin for Visual Studio。

5. 在 [**下載您的服務並發佈至雲端**] 下，按一下 [**下載**]。

  	這會下載一個方案，其中包含行動應用程式後端程式碼的專案，以及與行動應用程式後端連接的範例_待辦事項_用戶端應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

6. 下載您的發行設定檔、將下載的檔案儲存至本機電腦，並記下儲存位置。

## 測試行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## 發佈行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## 執行 Xamarin Android 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1. 在 Visual Studio 或 Xamarin Studio 中，導覽至行動應用程式方案內的用戶端專案。

	![][8]

	![][9]

2. 按 [執行] 按鈕，以建立專案並啟動應用程式。系統將要求您選取模擬器或連接的 USB 裝置。

	> [AZURE.NOTE]若要能夠在 Android 模擬器中執行此專案，您必須至少定義一個 Android 虛擬裝置 (AVD)。使用 AVD Manager 來建立與管理這些裝置。

3. 在應用程式中輸入有意義的文字 (例如 _Complete the tutorial_)，然後按一下加號 (**+**) 圖示。

	![][10]

	如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。要求中的資料會插入 TodoItem 資料表中。行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

	> [AZURE.NOTE]您可以檢閱存取行動應用程式後端以查詢與插入資料的程式碼，您可在 ToDoActivity.cs C# 檔案中找到此程式碼。



<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure 入口網站]: https://azure.portal.com/
[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 

<!---HONumber=62-->