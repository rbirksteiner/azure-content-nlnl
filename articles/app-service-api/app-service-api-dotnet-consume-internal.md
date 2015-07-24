<properties 
	pageTitle="從 .NET 用戶端使用 Azure App Service 中的內部 API 應用程式" 
	description="了解如何使用 App Service SDK，從相同資源群組中的 .NET 用戶端取用 API 應用程式。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# 從 .NET 用戶端使用 Azure App Service 中的內部 API 應用程式 

## 概觀

本教學課程示範如何撰寫 ASP.NET [API 應用程式](app-service-api-apps-why-best-platform.md)的程式碼，以呼叫針對 [**內部**] 存取層級設定的另一個 API 應用程式。這兩個 API 應用程式必須在相同的資源群組中。相同的程式碼可用來從[行動裝置應用程式](../app-service-mobile/app-service-mobile-value-prop-preview.md)呼叫內部 API 應用程式。

您將建置 ContactNames Web API。Web API 的 Get 方法會呼叫 ContactsList API 應用程式，並傳回 ContactsList API 應用程式所提供的連絡人資訊中的名稱。以下是成功呼叫 ContactNames Get 方法的 Swagger UI 畫面。

![](./media/app-service-api-dotnet-consume-internal/tryitout.png)

如需如何呼叫針對 [**公用 (匿名)**] 或 [**公用 (已驗證)**] 存取層級設定之 API 應用程式的相關資訊，請參閱[從 Azure App Service 中的 .NET 用戶端取用 API 應用程式](app-service-api-dotnet-consume.md)。

## 必要條件

此教學課程假設您已熟悉如何建立專案，並在 Visual Studio 中加入程式碼，以及如何 [在 Azure Preview 入口網站中管理 API 應用程式](../app-service-api-apps-manage-in-portal.md)。

本文中的專案和程式碼範例是以您在下列文章中建立和部署的 API 應用程式專案為基礎：

- [建立 API 應用程式](app-service-dotnet-create-api-app.md)
- [部署 API 應用程式](app-service-dotnet-deploy-api-app.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

本教學課程需要 2.6 版或更新版本的 Azure SDK for.NET。

### 設定目標 API 應用程式

1. 如果您尚未這麼做，請依照[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)教學課程，將 ContactsList 範例專案部署至 Azure 訂用帳戶中的 API 應用程式。

2. 在 [Azure 預覽入口網站](https://portal.azure.com/)的 [**API 應用程式**] 刀鋒視窗中，針對先前部署的 ContactsList API 應用程式，按一下 **[設定] > [應用程式設定]** 並將 [**存取層級**] 設定為 [**內部**]，然後按一下 [**儲存**]。

	![](./media/app-service-api-dotnet-consume-internal/setinternal.png)
 
## 建立會呼叫現有 API 應用程式的新 API 應用程式

- 在 Visual Studio 中，使用 Azure API 應用程式專案範本建立名為 ContactNames 的 API 應用程式專案。

	這是您在[建立 API 應用程式](app-service-dotnet-create-api-app.md)中遵循的相同程序，但您稍後會在本教學課程中將不同的程式碼加入至專案。
 
## 加入 App Service SDK 產生的用戶端程式碼

下列步驟會在[ Azure App Service 中的 .NET 用戶端取用 API 應用程式](app-service-api-dotnet-consume.md)中更詳細地說明。

3. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案 (而非方案)，並依序選取 [**加入 > Azure API 應用程式用戶端**]。 

3. 在 [**加入 Azure API 應用程式用戶端**] 對話方塊中，按一下 [**從 Azure API 應用程式下載**]。

5. 在下拉式清單中，選取要呼叫的 API 應用程式。在本教學課程中選擇您稍早建立的 ContactsList API 應用程式。

7. 按一下 [確定]。

## 啟用 Swagger UI

根據預設，API 應用程式專案會透過自動 [Swagger](http://swagger.io/ "官方 Swagger 資訊") 中繼資料產生啟用，但 Azure API 應用程式 new-project 範本會停用 API 測試頁。在本節中，您會啟用測試頁。

1. 開啟 *App_Start/SwaggerConfig.cs* 檔案，並搜尋 **EnableSwaggerUI**：

2. 將下列幾行程式碼取消註解：

	        })
	    .EnableSwaggerUi(c =>
	        {

## 建置控制器

5. 以滑鼠右鍵按一下 [**控制器**] 資料夾，然後選取 [**新增 > 控制器**]。 

6. 在 [**新增 Scaffold**] 對話方塊中，選取 [**Web API 2 控制器 - 空的**] 選項，然後按一下 [**新增**]。

7. 將控制器命名為 **ContactNamesController**，然後按一下 [**新增**]。

## 加入呼叫 API 應用程式的程式碼

若要呼叫因為其存取層級設定為 [**內部**] 而受到保護的 API 應用程式，您必須將內部驗證標頭加入至 HTTP 要求。這些標頭會通知目標 API 應用程式，而其呼叫來源是從相同資源群組內呼叫的對等 API 應用程式。

App Service SDK 會產生用戶端類別，以簡化您撰寫來呼叫 API 應用程式的程式碼。若要呼叫 [**公用 (匿名)**] API 應用程式，您只需要建立用戶端物件，然後對它呼叫方法，如下列所示：

		var client = new ContactsList();
		var contacts = await client.Contacts.GetAsync();

不過，若要新增驗證標頭，您需要存取 `HttpRequestMessage` 物件，但您沒有其存取權。若要取得要求的存取權和新增標頭，您必須建立 `DelegatingHandler` 類別，並將它的執行個體傳遞至所產生用戶端的建構函式。

1. 在專案中加入名為 *InternalCredentialHandler.cs* 的類別檔案，並以下列程式碼取代範本程式碼。

		using Microsoft.Azure.AppService.ApiApps.Service;
		using System.Net.Http;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace ContactNames
		{
		    public class InternalCredentialHandler : DelegatingHandler
		    {
		        protected override Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		        {
		            Runtime.FromAppSettings(request).SignHttpRequest(request);
		            return base.SendAsync(request, cancellationToken);
		        }
		    }
		}

	此程式碼會呼叫 `SignHttpRequest`，將驗證標頭新增至產生的用戶端類別所傳送的每一個要求：

		Runtime.FromAppSettings(this.Request).SignHttpRequest

1. 在 *ContactNamesController.cs* 中，以下列程式碼取代範本程式碼：

		using ContactNames.Models;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactNames.Controllers
		{
		    public class ContactNamesController : ApiController
		    {
		        [HttpGet]
		        public async Task<IEnumerable<string>> Get()
		        {
		            var names = new List<string>();

		            var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });
		            var contacts = await client.Contacts.GetAsync();
		
		            foreach (Contact contact in contacts)
		            {
		                names.Add(contact.Name);
		            }		
		            return names;
		        }
		    }
		}

	此程式碼會將處理常式傳遞至所產生用戶端類別的建構函式：

		var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });

### 部署

您無法在本機執行測試。您必須部署程式碼並在 Azure API 應用程式中執行；否則您將無法新增正確的驗證標頭，而呼叫將會遭到拒絕。

下列部署步驟會在[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)中更詳細地說明。

1. 建立 ContactNames API 應用程式。

	* 在 [**方案總管**] 中，以滑鼠右鍵按一下專案 (而非方案)，然後按一下 [**發佈**]。 

	* 按一下 [**設定檔**] 索引標籤，然後按一下 [**Microsoft Azure API 應用程式**]。

	* 按一下 [**新增**] 以在 Azure 訂閱中佈建新的 API 應用程式。

	* 在 [**建立 API 應用程式**] 對話方塊中，輸入 ContactNames 作為 [**API 應用程式名稱**]。

	* 對於 [**建立 API 應用程式**] 對話方塊中的其他值，輸入您先前針對[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)輸入的相同值。

		最重要的是，請確定您在與您即將呼叫之 API 應用程式相同的資源群組中建立新的 API 應用程式。

	* 按一下 [確定]。

2. 將您的程式碼部署到新的 API 應用程式。

	* 佈建 API 應用程式後，以滑鼠右鍵按一下 [**方案總管**] 中的專案，並按一下 [**發行**] 即可重新開啟發行對話方塊。

	* 在 [**發行 Web**] 對話方塊中，按一下 [**發行**] 開始部署程序。

### 測試

在本節中，您可使用 Swagger UI 測試新的 API 應用程式，並確認它可以呼叫您稍早建立的 API 應用程式。

1. 開啟瀏覽器並瀏覽至新 API 應用程式的 URL。

	使用預設發行設定，當 Visual Studio 完成發行程序時，它會自動開啟瀏覽器並瀏覽至 API 應用程式的 URL。如果並未發生上述情況，或您已關閉該瀏覽器視窗，請遵循下列步驟來取得相同的 URL：

	* 在 Azure 預覽入口網站中，移至新 ContactsName API 應用程式的 API 應用程式刀鋒視窗。

	* 按一下 [**URL**]。

		![](./media/app-service-api-dotnet-consume-internal/clickurl.png)
  
5. 在瀏覽器位址列中，將 `/swagger` 新增至 URL 的結尾，然後按 Enter 鍵。

	例如，結果產生的 URL 如下所示：

		https://microsoft-apiapp214f26e673e5449a214f26e673e5449a.azurewebsites.net/swagger

1. 在 [Swagger UI] 頁面上，按一下 **[ContactNames] > [取得] > [立即試用！]**

	![](./media/app-service-api-dotnet-consume-internal/tryitout.png)
  
	此頁面會在 [回應主體] 區段中顯示連絡人名稱，確認 ContactNames API 應用程式已順利從 ContactsList API 應用程式擷取資料。

	如果您要確認 [**內部**] 設定目前保護 ContactsList API 應用程式，請在 *ContactNamesController.cs* 中註解移除 `SignHttpRequest` 呼叫，重新部署，再次執行 Swagger [**立即試用**]，就會出現錯誤訊息。


## 使用 HttpClient 加入用以呼叫 API 應用程式的程式碼
 
App Service SDK 取決於 Swagger API 定義來產生用戶端類別。如果您要呼叫尚未實作 Swagger API 定義的 API 應用程式，您可以使用 `HttpClient` 進行。在此情況下，您仍然使用 `SignHttpRequest` 方法，但您會直接在 `HttpRequestMessage` 物件上進行呼叫。

1. 在 *ContactNamesController.cs* 中，將下列程式碼新增至 `Get` 方法的 `return names;` 陳述式之前。

		var httpClient = new HttpClient();
		HttpRequestMessage httpRequest = new HttpRequestMessage();
		httpRequest.Method = HttpMethod.Get;
		httpRequest.RequestUri = new Uri("https://{yourapiappurl}/api/contacts");
		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);
		var response = await httpClient.SendAsync(httpRequest); 
		var contacts2 = await response.Content.ReadAsAsync<List<Contact>>();
		foreach (Contact contact in contacts2)
		{
		    names.Add(contact.Name);
		}

	此程式碼會將傳入要求物件傳遞至 `SignHttpRequest` 方法，以簽署傳出要求物件：

		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);

2. 在 Azure 預覽入口網站中，移至 ContactsList API 應用程式的 API 應用程式刀鋒視窗，然後複製 URL。

	![](./media/app-service-api-dotnet-consume-internal/clurl.png)
 
4. 使用實際 URL 取代控制器程式碼中的預留位置字串 "{yourapiappurl}"。當您完成時，該程式碼行看起來如下列範例所示。

		httpRequest.RequestUri = new Uri("https://microsoft-apiappd99e684d99e684d99e684d99e684.azurewebsites.net/api/contacts");

### 部署和測試

1. 請遵循您先前使用的相同程序來部署 API 應用程式程式碼。

	**提示：**您可以略過 [**發行 Web**] 對話方塊，以及按一下工具列中的單一按鈕即可重新部署。在 Visual Studio 中，按一下 **[檢視] > [工具列]**，然後啟用 [**Web 單鍵發行**] 工具列。
 
2. 請遵循您先前的程序來使用 Swagger UI。

	由於您留在 HttpClient 程式碼中，所以這次的輸出會顯示一組重複的名稱。

	![](./media/app-service-api-dotnet-consume-internal/dupnames.png)
  
## 後續步驟

本文說明如何從 .NET 用戶端取用內部 API 應用程式。如需如何取用已設定為 [**公用 (已驗證)**] 或 [**公用 (匿名)**] 存取層級之 API 應用程式的相關資訊，請參閱[從 Azure App Service 中的 .NET 用戶端取用 API 應用程式](app-service-api-dotnet-consume.md)。

如需其他從 .NET 用戶端呼叫 API 應用程式的程式碼範例，請下載 [Azure Cards](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample) 範例應用程式。
 

<!---HONumber=62-->