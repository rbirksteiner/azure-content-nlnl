<properties 
	pageTitle="將行動服務新增至現有的應用程式 (Windows 通用) | 行動開發人員中心" 
	description="了解如何開始使用行動服務，在您的通用 Windows 應用程式中使用資料。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/02/2015" 
	ms.author="glenga"/>

# 將行動服務新增至現有的應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##概觀

本主題將示範如何使用 Azure 行動服務，充分運用通用 Windows 應用程式裡的資料。通用 Windows 應用程式解決方案包括 Windows 市集 8.1 和 Windows Phone 市集 8.1 應用程式的專案，以及一般共用專案。如需詳細資訊，請參閱[建置目標為 Windows 和 Windows Phone 的通用 Windows 應用程式](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx)。

在本教學課程中，您將下載 Visual Studio 2013 專案，以取得在記憶體中儲存資料的通用 Windows 應用程式、建立新的行動服務、整合行動服務與該應用程式，然後登入 Azure 管理入口網站來檢視執行應用程式時所做的資料變更。

>[AZURE.NOTE]本主題說明如何使用 Visual Studio Professional 2013 Update 3 的工具，將新的行動服務連接到通用 Windows 應用程式。要將行動服務連接到 Windows 市集或 Windows Phone 市集 8.1 應用程式時，也可使用相同的步驟進行。若要將行動服務連接至 Windows Phone 8.0 或 Windows Phone Silverlight 8.1 應用程式，請參閱[開始使用 Windows Phone 的資料](mobile-services-windows-phone-get-started-data.md)。

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Express 2013 for Windows</a> (Update 2 或更新版本)。 

##<a name="download-app"></a>下載 GetStartedWithData 專案

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]
 

##<a name="create-service"></a>從 Visual Studio 建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../../includes/mobile-services-create-new-service-vs2013.md)]

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

這時，Windows 市集與 Windows Phone 市集應用程式都會同時連接到新的行動服務。下一步則是在行動服務中建立新的 TodoItem 資料表。

##<a name="add-table"></a>將新資料表新增至行動服務

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../../includes/mobile-services-create-new-table-vs2013.md)]

##<a name="update-app"></a>更新應用程式以使用行動服務

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]

##<a name="test-azure-hosted"></a>測試 Azure 代管的行動服務

現在我們可以將兩個通用的 Windows 應用程式同時與 Azure 代管的行動服務一起測試。

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p>在<a href="https://manage.windowsazure.com/" target="_blank">管理入口網站</a>中按一下 [行動服務]<strong></strong>，然後按一下您的行動服務。<p></li>
<li><p>按一下 [資料]<strong></strong> 索引標籤，然後按一下 [瀏覽]<strong></strong>。</p>
<p>請注意，[TodoItem]<strong></strong> 資料表現在包含資料和行動服務產生的識別碼值，且資料行已自動新增至資料表，以符合應用程式中的 TodoItem 類別。</p></li>
</ol>
本教學課程到此結束。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓通用 Windows 應用程式能夠在行動服務中處理資料的基本概念。接著，請考慮閱讀下列其他主題：

* [開始使用驗證] <br/>了解如何驗證應用程式的使用者。

* [開始使用推播通知] <br/>了解如何將非常基本的推播通知傳送到應用程式。

* [行動服務 C# 做法概念性參考](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>深入了解如何搭配 .NET使用行動服務。
  
<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service from Visual Studio]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use the mobile service]: #update-app
[Test the app against Mobile Services]: #test-app
[View uploaded data in the Azure Management Portal]: #view-data
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Validate and modify data with scripts]: ../mobile-services-windows-store-dotnet-validate-modify-data-server-scripts.md
[Refine queries with paging]: ../mobile-services-windows-store-dotnet-add-paging-data.md
[Get started with Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-windows-store-dotnet-get-started-data.md
[開始使用驗證]: ../mobile-services-windows-store-dotnet-get-started-users.md
[開始使用推播通知]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkID=510826

[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!---HONumber=July15_HO2-->