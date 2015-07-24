<properties
	pageTitle="開始使用 Xamarin iOS 中的行動應用程式"
	description="開始使用 Xamarin iOS 以利用 Azure App Service 建置 Azure 行動應用程式。"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>建立新的 Xamarin iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

本教學課程將示範如何使用 Azure 行動應用程式，將雲端後端服務新增至 Xamarin iOS 應用程式。在本教學課程中，您將建立新的 .NET 服務，並建立可在 .NET 後端中儲存應用程式資料的簡單_待辦事項_ 應用程式。

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得多達 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[嘗試 App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)，即可在 App Service 中立即建立短期入門行動應用程式。不需要信用卡；沒有承諾。

## 建立新的行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 建立新的 Xamarin iOS 應用程式

建立行動應用程式後端之後，您可以依照 Azure 入口網站中的簡易快速入門，建立新的應用程式或修改現有的應用程式，以連接到您的行動應用程式後端。

在本節中，您將為行動應用程式後端下載新的 Xamarin iOS 應用程式和服務專案。

1. 在 Azure 入口網站中，按一下 [**行動應用程式**]，然後按一下您剛建立的行動應用程式後端。

2. 在刀鋒視窗頂端，按一下 [**加入用戶端**] 並展開 **Xamarin iOS**。

	![][6]

	這將顯示三個簡單步驟，可用來建立連接到您行動應用程式後端的 Xamarin iOS 應用程式。

3. 在您的本機電腦或虛擬機器下載並安裝 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> (如果您尚未這麼做)。

4. 下載並安裝 [Xcode] v4.4 或更新版本和 [Xamarin Studio]。您也可以使用 Xamarin for Visual Studio。

5. 在 [**下載您的服務並發佈至雲端**] 下，按一下 [**下載**]。

 這會下載一個方案，其中包含行動應用程式後端的專案，以及與行動應用程式後端連接的範例_待辦事項_應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

6. 下載您的發行設定檔、將下載的檔案儲存至本機電腦，並記下儲存位置。

## 測試行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## 發佈行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## 執行 Xamarin iOS 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1. 在 Visual Studio 或 Xamarin Studio 中，導覽至行動應用程式後端方案內的用戶端專案。

	![][8]

	![][9]

2. 按 [執行] 按鈕以建置用戶端專案，並在 iPhone 模擬器中啟動應用程式。

3. 在應用程式中輸入有意義的文字 (例如 _Complete the tutorial_)，然後按一下加號 (**+**) 圖示。

	![][10]

	如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。要求中的資料會插入 TodoItem 資料表中。行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

>[AZURE.NOTE]您可以在 QSTodoService.cs C# 檔案中檢閱用來存取行動應用程式後端以查詢和插入資料的程式碼。


<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Get started with offline data sync]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Get started with authentication]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Get started with push notifications]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
[JavaScript backend version]: ../partner-xamarin-mobile-services-ios-get-started.md
[Get started with data in app services using Visual Studio 2012]: ../app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Troubleshoot a mobile app .NET backend]: ../app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 

<!---HONumber=62-->