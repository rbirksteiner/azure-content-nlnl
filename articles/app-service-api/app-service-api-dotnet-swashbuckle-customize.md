
<properties 
	pageTitle="自訂 Swashbuckle 產生的 API 定義" 
	description="深入了解在 Azure App Service 中為 API 應用程式自訂由 Swashbuckle 產生的 Swagger API 定義。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2015" 
	ms.author="bradyg"/>

# 自訂 Swashbuckle 產生的 API 定義 

## 概觀

本文將說明如何自訂 Swashbuckle 來處理您可能想改變預設行為的常見案例：

* Swashbuckle 針對控制器方法的多載產生重複作業辨識碼
* Swashbuckle 假設來自方法的唯一有效回應是 HTTP 200 (OK) 
 
## 自訂作業識別碼產生

Swashbuckle 會藉由串連控制器名稱與方法名稱來產生 Swagger 作業識別碼。當一個方法的多個多載時，此模式會產生問題：Swashbuckle 會產生重複的作業識別碼，這是無效的 Swagger JSON。

例如，下列控制器程式碼會導致 Swashbuckle 產生三個 Contact_Get 作業識別碼。

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

您可藉由提供唯一的方法名稱 (如下所示)，以手動方式解決此問題：

* 取得
* GetById
* GetPage

替代方法是擴充 Swashbuckle 使其自動產生唯一的作業識別碼。

下列步驟示範如何使用 Visual Studio API 應用程式預覽專案範本包含在專案中的檔案 *SwaggerConfig.cs* 來自訂 Swashbuckle。您也可以在您設定以供部署為 API 應用程式的 Web API 專案中自訂 Swashbuckle。

1. 建立自訂 `IOperationFilter` 實作 

	`IOperationFilter` 介面為想要自訂 Swagger 中繼資料程序的各個層面的 Swashbuckle 使用者提供了擴充點。下列程式碼示範一種變更作業識別碼產生行為的方法。此程式碼會將參數名稱附加至作業識別碼名稱。

		using Swashbuckle.Swagger;
		using System.Web.Http.Description;
		
		namespace ContactsList
		{
		    public class MultipleOperationsWithSameVerbFilter : IOperationFilter
		    {
		        public void Apply(
		            Operation operation,
		            SchemaRegistry schemaRegistry,
		            ApiDescription apiDescription)
		        {
		            if (operation.parameters != null)
		            {
		                operation.operationId += "By";
		                foreach (var parm in operation.parameters)
		                {
		                    operation.operationId += string.Format("{0}",parm.name);
		                }
		            }
		        }
		    }
		}

2. 在 *App_Start\SwaggerConfig.cs* 檔案中，呼叫 `OperationFilter` 方法，讓 Swashbuckle 使用新的 `IOperationFilter` 實作。

		c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

	![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

	Swashbuckle NuGet 封裝所置放的 *SwaggerConfig.cs* 檔案包含許多擴充點註解排除範例。此處未顯示其他註解。

	當您進行這項變更之後，會使用 `IOperationFilter` 實作並產生唯一的作業識別碼。
 
	![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

<a id="multiple-response-codes" name="multiple-response-codes"></a>
	
## 允許 200 以外的回應碼

根據預設，Swashbuckle 會假設來自 Web API 方法的「唯一」合法回應是 HTTP 200 (OK)。在某些情況下，您可能想要傳回其他回應碼，而不造成用戶端引發例外狀況。例如，下列的 Web API 程式碼示範您想要用戶端接受 200 或 404 做為有效回應的案例。

	[ResponseType(typeof(Contact))]
    public HttpResponseMessage Get(int id)
    {
        var contacts = GetContacts();

        var requestedContact = contacts.FirstOrDefault(x => x.Id == id);

        if (requestedContact == null)
        {
            return Request.CreateResponse(HttpStatusCode.NotFound);
        }
        else
        {
            return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
        }
    }

在此案例中，根據預設所指定 Swashbuckle 產生的 Swagger 只有一個合法的 HTTP 狀態碼 (HTTP 200)。

![](./media/app-service-api-dotnet-swashbuckle-customize/http-200-output-only.png)

由於 Visual Studio 使用 Swagger API 定義來為用戶端產生程式碼，因此任何 HTTP 200 以外的回應所建立的用戶端程式碼將會引發例外狀況。以下程式碼是來自 C# 用戶端為此範例 Web API 方法所產生。

	if (statusCode != HttpStatusCode.OK)
    {
        HttpOperationException<object> ex = new HttpOperationException<object>();
        ex.Request = httpRequest;
        ex.Response = httpResponse;
        ex.Body = null;
        if (shouldTrace)
        {
            ServiceClientTracing.Error(invocationId, ex);
        }
        throw ex;
    } 

Swashbuckle 提供兩種方法 (使用 XML 註解或 `SwaggerResponse` 屬性) 來自訂其產生之預期的 HTTP 回應碼清單。使用屬性較為容易，但僅可在 Swashbuckle 5.1.5 或更新版本使用。在 Visual Studio 2013 中 API Apps 預覽新專案範本包含 Swashbuckle 5.0.0 版，因此若您使用範本，而不想更新 Swashbuckle，唯一的選項是使用 XML 註解。

### 使用 XML 註解自訂預期的回應碼

如果您的 Swashbuckle 版本是 5.1.5 以前的版本，請使用這個方法指定回應碼。

1. 首先，請逐一新增 XML 文件註解到您想指定 HTTP 回應碼的方法中。取得如上所示之範例 Web API 動作，並套用 XML 文件到其中，將使其產生如下列範例的程式碼。 

		/// <summary>
		/// Returns the specified contact.
		/// </summary>
		/// <param name="id">The ID of the contact.</param>
		/// <returns>A contact record with an HTTP 200, or null with an HTTP 404.</returns>
		/// <response code="200">OK</response>
		/// <response code="404">Not Found</response>
		[ResponseType(typeof(Contact))]
		public HttpResponseMessage Get(int id)
		{
		    var contacts = GetContacts();
		
		    var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
		
		    if (requestedContact == null)
		    {
		        return Request.CreateResponse(HttpStatusCode.NotFound);
		    }
		    else
		    {
		        return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
		    }
		}

1. 新增在 *SwaggerConfig.cs* 檔案中的指示，以引導 Swashbuckle 使用 XML 文件檔案。

	* 開啟 *SwaggerConfig.cs* 並在 *SwaggerConfig* 類別上建立方法，來指定 XML 文件檔案的路徑。 

			private static string GetXmlCommentsPath()
			{
			    return string.Format(@"{0}\XmlComments.xml", 
			        System.AppDomain.CurrentDomain.BaseDirectory);
			}

	* 在 *SwaggerConfig.cs* 檔案中向下捲動，直到看見如以下螢幕擷取畫面所示以註解排除的程式碼行。

		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-commented-out.png)
	
	* 取消註解該行以在 Swagger 產生期間中啟用 XML 註解處理程序。
	
		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-uncommented.png)
	
1. 若要產生 XML 文件檔案，請進入專案的屬性，然後啟用 XML 文件檔案，如以下螢幕擷取畫面所示。

	![](./media/app-service-api-dotnet-swashbuckle-customize/enable-xml-documentation-file.png)

一旦您執行這些步驟，由 Swashbuckle 產生的 Swagger JSON 將會反映您在 XML 註解中指定的 HTTP 回應碼。以下螢幕擷取畫面示範此新的 JSON 承載。

![](./media/app-service-api-dotnet-swashbuckle-customize/swagger-multiple-responses.png)

當您使用 Visual Studio 為 REST API 重新產生用戶端程式碼時，C# 程式碼同時接受 HTTP「 OK」和「Not Found」狀態碼，並且不會引發例外狀況，讓您可以使用程式碼來決定如何處理傳回 null 的 Contact 記錄。

		if (statusCode != HttpStatusCode.OK && statusCode != HttpStatusCode.NotFound)
		{
		    HttpOperationException<object> ex = new HttpOperationException<object>();
		    ex.Request = httpRequest;
		    ex.Response = httpResponse;
		    ex.Body = null;
		    if (shouldTrace)
		    {
		        ServiceClientTracing.Error(invocationId, ex);
		    }
        	    throw ex;
		}

本示範的程式碼可以在[此 GitHub 存放庫](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes)中找到。隨 Web API 專案以 XML 文件註解標記的是主控台應用程式專案，其包含針對此 API 產生的用戶端。

### 使用 SwaggerResponse 屬性自訂預期的回應碼

[SwaggerResponse](https://github.com/domaindrivendev/Swashbuckle/blob/master/Swashbuckle.Core/Swagger/Annotations/SwaggerResponseAttribute.cs) 屬性可在 Swashbuckle 5.1.5 和更新的版本中使用。為了避免您在專案中使用較舊的版本，本章節將由說明如何更新 Swashbuckle NuGet 封裝開始，讓您可以使用這個屬性。

1. 在 [方案總管] 中，以滑鼠右鍵按一下您的專案並選擇 [管理 NuGet 封裝]。 

	![](./media/app-service-api-dotnet-swashbuckle-customize/manage-nuget-packages.png)

1. 按一下在 *Swashbuckle* NuGet 封裝旁的 [更新]。

	![](./media/app-service-api-dotnet-swashbuckle-customize/update-nuget-dialog.png)

1. 新增 *SwaggerResponse* 屬性到您想指定有效 HTTP 回應碼的 Web API 動作方法中。

		[SwaggerResponse(HttpStatusCode.OK)]
		[SwaggerResponse(HttpStatusCode.NotFound)]
		[ResponseType(typeof(Contact))]
		public HttpResponseMessage Get(int id)
		{
		    var contacts = GetContacts();

		    var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
		    if (requestedContact == null)
		    {
		        return Request.CreateResponse(HttpStatusCode.NotFound);
		    }
		    else
		    {
		        return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
		    }
		}

2. 為屬性的命名空間新增 `using` 陳述式：

		using Swashbuckle.Swagger.Annotations;
		
1. 瀏覽至在 Swagger JSON 中可看見您專案和各種不同 HTTP 回應碼的 URL */swagger/docs/v1*。

	![](./media/app-service-api-dotnet-swashbuckle-customize/multiple-responses-post-attributes.png)

本示範的程式碼可以在[此 GitHub 存放庫](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes-With-Attributes)中找到。隨 Web API 專案以 *SwaggerResponse* 屬性裝飾的是主控台應用程式專案，其包含針對此 API 產生的用戶端。

## 後續步驟

本文已經示範如何自訂 Swashbuckle 產生作業識別碼和有效回應碼的方式。如需詳細資訊，請參閱 [GitHub 上的 Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)。
 

<!---HONumber=62-->