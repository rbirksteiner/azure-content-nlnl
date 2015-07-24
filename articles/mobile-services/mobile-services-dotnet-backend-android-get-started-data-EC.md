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

##概觀

本主題將說明如何以 Azure 行動服務做為 Android 應用程式的後端資料來源。在本教學課程中，您將建立新的行動服務、下載 Eclipse Android 專案以取得在記憶體中儲存資料的應用程式、整合行動服務與該應用程式，然後檢視執行應用程式時所做的資料變更。

您將在本教學課程中建立的行動服務，將可在行動服務中支援 .NET 執行階段。如此，您即可在行動服務中使用 .NET 語言和 Visual Studio 來撰寫伺服器端商務邏輯。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [JavaScript 後端版本](mobile-services-android-get-started-data-EC.md)。

若要完成此教學課程，您需要下列項目：

+ <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a> (Update 3 或更新版本)。 

+ Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-android-get-started-data-EC%2F)。

##<a name="create-service"></a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]


##<a name="download-the-service"></a>將服務下載至您的本機電腦

[AZURE.INCLUDE [mobile-services-download-service-locally](../../includes/mobile-services-download-service-locally.md)]

##<a name="test-the-service"></a>測試行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-the-service"></a>將行動服務發佈至 Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="download-app"></a>下載 GetStartedWithData 專案

###取得範例程式碼

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/download-android-sample-code-EC.md)]

###驗證 Android SDK 版本

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version-EC.md)]


###檢查和執行範例程式碼

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code-EC.md)]

##<a name="update-app"></a>更新應用程式以使用行動服務進行資料存取

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data-EC.md)]

##<a name="test-app"></a>對已發佈的行動服務進行應用程式測試

現在，應用程式已更新為使用行動服務做為後端儲存，您可以使用 Android 模擬器或 Android 手機，在行動服務上測試應用程式。

1. 從 [執行] 功能表，按一下 [執行] 來啟動專案。

	這樣會執行您的應用程式 (以 Android SDK 建立)，該應用程式利用用戶端程式庫傳送查詢，然後從您的行動服務傳回項目。

5. 同樣地，輸入有意義的文字，然後按一下 [加入]。

   	這會傳送新項目以插入至行動服務。

    您可以重新啟動應用程式，以確認變更持續存留至 Azure 中的資料庫。您也可以使用 Azure 管理入口網站來檢查資料庫：後續的兩個步驟將執行此動作以檢視您資料庫中的變更。


4. 在 Azure 管理入口網站中，對您行動服務的相關資料庫按一下 [管理]。

    ![](./media/mobile-services-dotnet-backend-android-get-started-data-EC/manage-sql-azure-database.png)

5. 在管理入口網站中執行查詢，以檢視 Windows 市集應用程式所做的變更。您的查詢會與下列查詢類似，但使用您的資料庫名稱，而非 `todolist`。

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-android-get-started-data-EC/sql-azure-query.png)

Android 的**開始使用資料**教學課程到此結束。


## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 Android 應用程式在行動服務中使用資料的基礎。

完成以下其中一項教學課程：

* [開始使用驗證] <br/>了解如何驗證應用程式的使用者。

* [開始使用推播通知] <br/>了解如何將非常基本的推播通知傳送到應用程式。

* [行動服務 Android 作法概念參考資料](mobile-services-android-how-to-use-client-library.md) <br/>深入了解如何搭配 Android 使用行動服務。
  
<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[開始使用驗證]: mobile-services-dotnet-backend-android-get-started-users.md
[開始使用推播通知]: mobile-services-dotnet-backend-android-get-started-push-EC.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030

<!---HONumber=July15_HO2-->