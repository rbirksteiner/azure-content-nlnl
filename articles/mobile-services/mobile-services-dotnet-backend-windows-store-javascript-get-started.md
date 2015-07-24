<properties
	pageTitle="開始使用 Windows 市集應用程式的行動服務 | 行動開發人員中心"
	description="遵循此教學課程，可開始使用 Azure 行動服務進行 C#、VB 或 JavaScript 的 Windows 市集開發。"
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.date="04/24/2015"
	ms.author="glenga"/>


# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到通用 Windows 應用程式。在本教學課程中，您將以 HTML 和 JavaScript 建立新的行動服務，以及可在新的行動服務中儲存應用程式資料的簡易「待辦事項」應用程式。您建立的行動服務會使用受支援的 .NET 語言，使用 Visual Studio 來處理伺服器端商務邏輯，以及管理行動服務。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 JavaScript 版本。

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>。您可以使用免費試用版。


## 建立新的行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 建立新的通用 Windows 應用程式

在建立您的行動服務之後，您可以依照 [管理入口網站] 中的簡易快速入門，來建立新的應用程式或修改現有的應用程式，以連接到您的行動服務。

在本節中，您將建立與行動服務連線的新通用 Windows 應用程式。

1. 在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2. 在快速入門索引標籤中，按一下 [Choose platform] 下的 [Windows]，並展開 [Create a new Windows Store app]。

   	![][6]

   	這將顯示三個簡單步驟，可用來建立連接到您行動服務的 Windows 市集應用程式。

  	![][7]

3. 在您的本機電腦或虛擬機器下載並安裝 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> (如果您尚未這麼做)。

4. 在 [Download and run your app and service locally] 中，選取 Windows 市集應用程式的語言，然後按一下 [下載]。

  	這會下載一個方案，其中包含行動服務的專案，以及與行動服務連接的範例_待辦事項_應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。


## 對本機行動服務進行應用程式測試

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[AZURE.NOTE]您可以檢閱造成存取您行動服務來進行查詢和插入資料的程式碼，該程式碼位於 default.js 檔案中。

## 發佈行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

<ol start="4">
<li><p>在共用程式碼專案中，開啟 default.js 檔案，找出建立 <a href="http://msdn.microsoft.com/library/azure/jj554219.aspx" target="_blank">WindowsAzure.MobileServiceClient</a> 執行個體的程式碼，使用 <em>localhost</em> 將建立此用戶端的程式碼註解化，然後使用遠端行動服務 URL，將建立用戶端的程式碼取消註解，如下所示：</p>

        <pre><code>var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXXXX-APPLICATION-KEY-XXXXXX"
        );</code></pre>

	<p>此時，用戶端會存取發佈至 Azure 的行動服務。</p></li>

<li><p>按 <strong>F5</strong> 鍵，以重建專案並啟動應用程式。</p></li>

<li><p>在應用程式的 [Insert a TodoItem]<em></em> 中輸入有意義的文字，例如<strong>完成教學課程</strong>，然後按一下 [儲存]<strong></strong>。</p>

<p>如此會傳送 POST 要求到 Azure 中代管的新行動服務。</p>
</li>
<li><p>(選用) 在通用 Windows 方案中，將預設的啟動專案變更為其他應用程式，再按一次 <strong>F5</strong> 鍵。</p>

	<p>請注意，從先前步驟中儲存的資料，會在應用程式啟動後從行動服務載入。</p></li>
</ol>

如需通用 Windows 應用程式的詳細資訊，請參閱[從單一行動服務支援多個裝置平台](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs) (英文)。

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-publish.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: ../mobile-services-dotnet-backend-windows-universal-javascript-get-started-data.md
[Get started with authentication]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Management Portal]: https://manage.windowsazure.com/
[JavaScript version]: ../mobile-services-windows-store-get-started.md
[Get started with data in Mobile Services using Visual Studio 2012]: ../mobile-services-windows-store-dotnet-get-started-data-vs2012.md
 

<!---HONumber=July15_HO1-->