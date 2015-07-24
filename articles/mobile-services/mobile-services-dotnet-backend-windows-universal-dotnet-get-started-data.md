<properties 
	pageTitle="將行動服務新增至現有的應用程式 (Windows 市集) | 行動開發人員中心" 
	description="了解如何開始使用行動服務在 Windows Store 應用程式中使用資料。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/02/2015" 
	ms.author="wesmc"/>

# 將行動服務新增至現有的應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##概觀

本主題將說明如何以 Azure 行動服務作為 Windows 市集應用程式的後端資料來源。在本教學課程中，您將下載 Visual Studio 2013 專案，以取得在記憶體中儲存資料的應用程式、建立新的行動服務、整合行動服務與該應用程式，然後檢視執行應用程式時所做的資料變更。

您在本教學課程中建立的行動服務是 .NET 後端行動服務。.Net 後端讓您可以在行動服務中，針對伺服器端的商業邏輯，使用 .NET 語言和 Visual Studio。您還可以在本機電腦上執行和偵錯行動服務。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 JavaScript 後端版本。

>[AZURE.NOTE]本主題說明如何使用 Visual Studio Professional 2013 Update 3 的工具，將新的行動服務連接到通用 Windows 應用程式。要將行動服務連接到 Windows 市集或 Windows Phone 市集 8.1 應用程式時，也可使用相同的步驟進行。若要將行動服務連接至 Windows Phone 8.0 或 Windows Phone Silverlight 8.1 應用程式，請參閱[開始使用 Windows Phone 的資料](mobile-services-dotnet-backend-windows-phone-get-started-data.md)。

> 如果您無法升級至 Visual Studio Professional 2013 Update 3 或是您偏好將行動服務專案手動新增到 Windows 市集應用程式解決方案，請參閱[此版本](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md)的主題。

##必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a> (Update 3 或更新版本)。 

##下載 GetStartedWithData 專案

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##從 Visual Studio 建立新的行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="7"><li><p>在 [解決方案總管] 中，於 [GetStartedWithData.Shared] 專案資料夾中開啟 App.xaml.cs 程式碼檔案，並注意在 Windows 市集應用程式條件式編譯區塊裡，新增至 <strong>App</strong> 類別的全新靜態欄位，如以下範例所示：</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>本程式碼透過使用 <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient 類別</a>的執行個體，提供對應用程式中新行動服務的存取權。用戶端是利用提供新行動服務的 URI 和應用程式金鑰來建立。應用程式中的所有頁面都可以使用此靜態資料行。</p>
</li>
<li><p>以滑鼠右鍵按一下 [Windows Phone] 應用程式專案，然後依據按一下 [新增]、[已連接服務]<strong></strong><strong></strong>，並選取您剛建立的行動服務，然後按一下 [確定]<strong></strong>。</p>
<p>相同的程式碼會新增到共用 App.xaml.cs 檔案，但是這次是新增到 Windows Phone 應用程式條件式編譯區塊。</p></li>
</ol>

這時，Windows 市集與 Windows Phone 市集應用程式都會同時連接到新的行動服務。下個步驟是測試新的行動服務專案。


##在本機測試行動服務專案

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##更新應用程式以使用行動服務

在本節中，您將會更新通用 Windows 應用程式，以使用行動服務作為應用程式的後端服務。您只需要變更 [GetStartedWithData.Shared] 專案資料夾的 MainPage.xaml.cs 專案檔案。

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]


##將行動服務發佈至 Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


##測試 Azure 代管的行動服務

現在我們可以將兩個通用的 Windows 應用程式同時與 Azure 代管的行動服務一起測試。

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

##檢視儲存在 SQL Database 裡的資料

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../../includes/mobile-services-dotnet-backend-view-sql-data.md)]
 
本教學課程到此結束。

##後續步驟

本教學課程示範讓通用 Windows 應用程式專案能夠在行動服務中處理資料的基本概念。接著，請考慮閱讀下列其他主題：

* [開始使用驗證] <br/>了解如何驗證應用程式的使用者。

* [開始使用推播通知] <br/>了解如何將非常基本的推播通知傳送到應用程式。

* [行動服務 C# 做法概念性參考](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>深入了解如何搭配 .NET使用行動服務。


<!-- Images. -->



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[開始使用驗證]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[開始使用推播通知]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

[Get started with offline data sync]: mobile-services-windows-store-dotnet-get-started-offline-data.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkID=510826
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  

<!---HONumber=July15_HO2-->