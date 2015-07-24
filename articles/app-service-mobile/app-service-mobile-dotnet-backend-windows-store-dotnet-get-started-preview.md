<properties
	pageTitle="開始使用 Windows 市集應用程式的行動應用程式後端 | 行動開發人員中心"
	description="遵循此教學課程，可開始使用 Azure 行動應用程式後端進行 C#、VB 或 JavaScript 的 Windows 市集開發。"
	services="app-service\mobile"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="06/16/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>建立 Windows 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

本教學課程說明如何使用 Azure 行動應用程式後端，將雲端型後端服務新增到通用 Windows 應用程式。通用 Windows 應用程式解決方案包括 Windows 市集 8.1 和 Windows Phone 市集 8.1 應用程式的專案，以及一般共用專案。

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started-preview](../../includes/app-service-mobile-windows-universal-get-started-preview.md)]

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得多達 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[嘗試 App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)，即可在 App Service 中立即建立短期入門行動應用程式。不需要信用卡；沒有承諾。

## <a name="create-new-service"> </a>建立新的行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 建立新的通用 Windows 應用程式

建立行動應用程式後端之後，您可以依照 Azure 入口網站中的簡易快速入門，建立新的應用程式或修改現有的應用程式，以連接到您的行動應用程式後端。

在本節中，您將建立與行動應用程式後端連線的新通用 Windows 應用程式。

1. 在 Azure 入口網站中，按一下 [**行動應用程式**]，然後按一下您剛建立的行動應用程式。

2. 在刀鋒視窗頂端，按一下 [**加入用戶端**] 並展開 **Windows (C#)**。

    ![行動應用程式快速入門步驟](./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/windows-quickstart.png)

    這將顯示三個簡單步驟，可用來建立連接到您行動應用程式後端的 Windows 市集應用程式。

3. 在您的本機電腦或虛擬機器下載並安裝 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> (如果您尚未這麼做)。

4. 在 [Download and run your app and service locally] 中，選取 Windows 市集應用程式的語言，然後按一下 [下載]。

    這會下載一個方案，其中包含行動應用程式後端的專案，以及與行動應用程式後端連接的範例_待辦事項_應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

## 測試行動應用程式

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## 發佈行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## 執行 Windows 應用程式

現在行動應用程式後端已發佈，且用戶端已連接至 Azure 代管的遠端行動應用程式後端，我們就可以使用 Azure 來執行應用程式，以儲存項目。

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-app-preview](../../includes/app-service-mobile-windows-universal-test-app-preview.md)]

<!-- Anchors. -->

[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Define the mobile app backend instance]: #define-mobile-app-backend-instance
[Next Steps]: #next-steps

<!-- Images. -->



<!-- URLs. -->
[Get started with authentication]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
 

<!---HONumber=62-->