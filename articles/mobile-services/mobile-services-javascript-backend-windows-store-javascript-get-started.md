<properties 
	pageTitle="開始使用 Windows 市集應用程式的行動服務 | 行動開發人員中心" 
	description="遵循此教學課程，可開始使用 Azure 行動服務進行 C# 或 JavaScript 的 Windows 市集開發。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="hero-article" 
	ms.date="06/03/2015" 
	ms.author="glenga"/>

# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到通用 Windows 應用程式。

在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單*待辦事項*應用程式。您將建立的行動服務會使用 JavaScript 建立伺服器端商務邏輯。若要建立可讓您使用 Visual Studio 並以支援的 .NET 語言來撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 .NET 後端版本。

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F)。
* [Visual Studio 2013 Express for Windows] 

## 建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的通用 Windows 應用程式

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的通用 Windows 應用程式或修改現有的 Windows 市集或 Windows Phone 應用程式專案，以連接到您的行動服務。

在本節中，您將建立與行動服務連線的新通用 Windows 應用程式。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

   
2. 在快速入門索引標籤中，按一下 [Choose platform] 下的 [Windows]，並展開 [Create a new Windows Store app]。

   	![](./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png)

   	這將顯示三個簡單步驟，可用來建立連接到您行動服務的 Windows 市集應用程式。

  	![](./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png)

3. 在您的本機電腦或虛擬機器下載並安裝 [Visual Studio 2013][Visual Studio 2013 Express for Windows] (如果您尚未這麼做)。

4. 按一下 [Create TodoItem table] 以建立儲存應用程式資料的資料表。

5. 在 [Download and run your app] 中，選取您的應用程式語言，然後按一下 [下載]。

  	這將會下載與行動服務連接的範例*待辦事項*應用程式專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

## 執行您的 Windows 應用程式

[AZURE.INCLUDE [mobile-services-javascript-backend-run-app](../../includes/mobile-services-javascript-backend-run-app.md)]

>[AZURE.NOTE]您可以檢閱造成存取您行動服務來進行查詢和插入資料的程式碼，該程式碼位於 default.js 檔案中。

## 後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* [將驗證新增至應用程式][Get started with authentication] <br/>了解如何向身分識別提供者驗證應用程式的使用者。

* [將推播通知新增至您的應用程式][Get started with push notifications] <br/>了解如何將非常基本的推播通知傳送至應用程式。

如需通用 Windows 應用程式的詳細資訊，請參閱[從單一行動服務支援多個裝置平台](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs) (英文)。

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Get started with data]: ../mobile-services-javascript-backend-windows-universal-javascript-get-started-data.md
[Get started with authentication]: mobile-services-windows-store-javascript-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-windows-store-javascript-get-started-push.md
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO1-->