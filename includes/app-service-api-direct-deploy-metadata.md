## API 應用程式中繼資料

本節提供您可自訂之 API 應用程式中繼資料的相關資訊。

*apiapp.json* 檔案中的大部分屬性，以及 *Metadata* 資料夾中的檔案，會影響 API 應用程式封裝在 Azure Marketplace 中的呈現方式。下列各節說明當您在 Azure 訂用帳戶中將程式碼部署到 API 應用程式時，哪些屬性和檔案會影響 API 應用程式。

### API 應用程式識別碼 

`id` 屬性決定 API 應用程式的名稱。例如：

		"id": "ContactsList",

![](./media/app-service-api-direct-deploy-metadata/apiappname.png)

### 命名空間

將 `namespace` 屬性設定為 Azure Active Directory 租用戶的網域。若要尋找您的網域，請將瀏覽器開啟到 [Azure 傳統入口網站](https://manage.windowsazure.com/)，並瀏覽 [**Active Directory**]，然後選取 [**網域**] 索引標籤。例如：

		"namespace": "contoso.onmicrosoft.com",

### 動態 Swagger API 定義

如果 API 應用程式可以傳回動態 [Swagger](http://swagger.io/) API 定義，則儲存 GET 要求的相對 URL，該要求會在 `endpoints.apiDefinition` 屬性中傳回 API 定義 JSON。例如：

		"endpoints": {
		    "apiDefinition": "/swagger/docs/v1"
		}

> **附註：**如果使用 Swashbuckle 來產生 Swagger API 定義，Web API 控制器中的 HTTP 方法多載會導致重複的作業識別碼。如需詳細資訊，請參閱[自訂 Swashbuckle 產生的作業識別碼](../article/app-service-api/app-service-api-dotnet-swashbuckle-customize.md)。
  
### 靜態 Swagger API 定義

若要提供靜態 [Swagger](http://swagger.io/) 2.0 API 定義檔，請將檔案儲存在 *Metadata* 資料夾中，並將檔案命名為 *apiDefinition.swagger.json*

![](./media/app-service-api-direct-deploy-metadata/apidefinmetadata.png)

將 `endpoints.apiDefinition` 保留在 *apiapp.json* 檔案外部，或將其值設定為 Null。如果您同時包含 `endpoints.apiDefinition` URL 和 *apiDefinition.swagger.json* 檔案，則會優先使用 URL，並忽略檔案。

<!---HONumber=62-->