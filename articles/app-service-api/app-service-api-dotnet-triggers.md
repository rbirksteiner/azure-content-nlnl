<properties 
	pageTitle="Azure App Service API 應用程式觸發程序" 
	description="本文會示範如何在 API 應用程式中實作觸發程序" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="guayan;tarcher"/>

# Azure App Service API 應用程式觸發程序

## 概觀

本文說明如何實作 API 應用程式觸發程序，並從邏輯應用程式加以使用。

如果您不熟悉 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中的 [API 應用程式](app-service-api-apps-why-best-platform.md)，則建議您閱讀[建立 API 應用程式](app-service-dotnet-create-api-app.md)上的多組件系列。

此外，此主題中所有程式碼片段的複製來源為 [FileWatcher API 應用程式程式碼範例](http://go.microsoft.com/fwlink/?LinkId=534802)。

請注意，您將需要下載下列 nuget 封裝，以取得本文中建置與執行所需的程式碼：[http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/)。

## 何謂 API 應用程式觸發程序？

API 應用程式常會需要引發事件，以讓 API 應用程式用戶端採取適當的動作來回應事件。支援此案例的REST API 型機制稱為 API 應用程式觸發程序。

例如，假設用戶端程式碼使用 [Twitter 連接器 API 應用程式](../app-service-logic/app-service-logic-connector-twitter.md)，且您的程式碼必須根據其中包含特定文字的新推文來執行動作。在此情況下，您可以設定輪詢或推入觸發程序來加速處理這項需求。

## 輪詢觸發程序與推入觸發程序

目前支援兩種類型的觸發程序：

- 輪詢觸發程序 - 用戶端會輪詢 API 應用程式以取得已引發事件的通知 
- 推入觸發程序 - 當事件引發時，API 應用程式會通知用戶端 

### 輪詢觸發程序

輪詢觸發程序實作為一般的 REST API，並預期它的用戶端 (例如邏輯應用程式) 輪詢它以取得通知。用戶端可能會維持狀態，而輪詢觸發程序本身是無狀態的。

有關要求和回應封包的下列資訊，說明輪詢觸發程序合約的一些重要層面：

- 要求
    - HTTP 方法：GET
    - 參數
        - triggerState - 此選用參數可讓用戶端指定其狀態，以便輪詢觸發程序可以根據指定的狀態，正確決定是否要傳回通知。
        - API 特有的參數
- Response
    - 狀態碼 **200** - 要求有效，而且沒有觸發程序的通知。通知的內容成為回應主體。回應中的 "Retry-After" 標頭會指出，必須透過後續要求呼叫擷取其他通知資料。
    - 狀態碼 **202** - 要求有效，但沒有新的觸發程序通知。
    - 狀態碼 **4xx** - 要求無效。用戶端不應該重試要求。
    - 狀態碼 **5xx** - 要求導致內部伺服器錯誤及/或暫時性問題。用戶端應該重試要求。

下列程式碼片段是如何實作輪詢觸發程序的範例。

    // Implement a poll trigger.
    [HttpGet]
    [Route("api/files/poll/TouchedFiles")]
    public HttpResponseMessage TouchedFilesPollTrigger(
        // triggerState is a UTC timestamp
        string triggerState,
        // Additional parameters
        string searchPattern = "*")
    {
        // Check to see whether there is any file touched after the timestamp.
        var lastTriggerTimeUtc = DateTime.Parse(triggerState).ToUniversalTime();
        var touchedFiles = Directory.EnumerateFiles(rootPath, searchPattern, SearchOption.AllDirectories)
            .Select(f => FileInfoWrapper.FromFileInfo(new FileInfo(f)))
            .Where(fi => fi.LastAccessTimeUtc > lastTriggerTimeUtc);

        // If there are files touched after the timestamp, return their information.
        if (touchedFiles != null && touchedFiles.Count() != 0)
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventTriggered(new { files = touchedFiles });
        }
        // If there are no files touched after the timestamp, tell the caller to poll again after 1 mintue.
        else
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventWaitPoll(new TimeSpan(0, 1, 0));
        }
    }

若要測試此輪詢觸發程序，請遵循下列步驟：

1. 部署驗證設定為**匿名公用**的 API 應用程式。
2. 呼叫**接觸**作業以接觸檔案。下圖顯示透過 Postman 的範例要求。![透過 Postman 呼叫接觸作業](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
3. 以在步驟 2 之前設定為時間戳記的 **triggerState** 參數，呼叫輪詢觸發程序。下圖顯示透過 Postman 的範例要求。![透過 Postman 呼叫輪詢觸發程序](./media/app-service-api-dotnet-triggers/callpolltriggerfrompostman.PNG)

### 推入觸發程序

推入觸發程序會實作為一般的 REST API，將通知推入已註冊為希望在引發特定事件時收到通知的用戶端。

下列資訊關於要求和回應封包，說明推入觸發程序合約的一些重要層面。

- 要求
    - HTTP 方法：PUT
    - 參數
        - 觸發程式識別碼：必要項 - 不透明字串 (例如 GUID)，表示推入觸發程序的註冊。
        - callbackUrl：必要項 - 當事件引發時所叫用回呼的 URL。叫用是一個簡單的 POST HTTP 呼叫。
        - API 特有的參數
- Response
    - 狀態碼 **200** - 註冊用戶端的要求成功。
    - 狀態碼 **4xx** - 要求無效。用戶端不應該重試要求。
    - 狀態碼 **5xx** - 要求導致內部伺服器錯誤及/或暫時性問題。用戶端應該重試要求。
- 回呼
    - HTTP 方法：POST
    - 要求內文： 通知內容。

下列程式碼片段是如何實作推入觸發程序的範例：

    // Implement a push trigger.
    [HttpPut]
    [Route("api/files/push/TouchedFiles/{triggerId}")]
    public HttpResponseMessage TouchedFilesPushTrigger(
        // triggerId is an opaque string.
        string triggerId,
        // A helper class provided by the AppService service SDK.
        // Here it defines the input of the push trigger is a string and the output to the callback is a FileInfoWrapper object.
        [FromBody]TriggerInput<string, FileInfoWrapper> triggerInput)
    {
        // Register the trigger to some trigger store.
        triggerStore.RegisterTrigger(triggerId, rootPath, triggerInput);

        // Extension method provided by the AppService service SDK indicating the registration is completed.
        return this.Request.PushTriggerRegistered(triggerInput.GetCallback());
    }

    // A simple in-memory trigger store.
    public class InMemoryTriggerStore
    {
        private static InMemoryTriggerStore instance;

        private IDictionary<string, FileSystemWatcher> _store;

        private InMemoryTriggerStore()
        {
            _store = new Dictionary<string, FileSystemWatcher>();
        }

        public static InMemoryTriggerStore Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new InMemoryTriggerStore();
                }
                return instance;
            }
        }

        // Register a push trigger.
        public void RegisterTrigger(string triggerId, string rootPath,
            TriggerInput<string, FileInfoWrapper> triggerInput)
        {
            // Use FileSystemWatcher to listen to file change event.
            var filter = string.IsNullOrEmpty(triggerInput.inputs) ? "*" : triggerInput.inputs;
            var watcher = new FileSystemWatcher(rootPath, filter);
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            watcher.NotifyFilter = NotifyFilters.LastAccess;

            // When some file is changed, fire the push trigger.
            watcher.Changed +=
                (sender, e) => watcher_Changed(sender, e,
                    Runtime.FromAppSettings(),
                    triggerInput.GetCallback());

            // Assoicate the FileSystemWatcher object with the triggerId.
            _store[triggerId] = watcher;

        }

        // Fire the assoicated push trigger when some file is changed.
        void watcher_Changed(object sender, FileSystemEventArgs e,
            // AppService runtime object needed to invoke the callback.
            Runtime runtime,
            // The callback to invoke.
            ClientTriggerCallback<FileInfoWrapper> callback)
        {
            // Helper method provided by AppService service SDK to invoke a push trigger callback.
            callback.InvokeAsync(runtime, FileInfoWrapper.FromFileInfo(new FileInfo(e.FullPath)));
        }
    }

若要測試此輪詢觸發程序，請遵循下列步驟：

1. 部署驗證設定為**匿名公用**的 API 應用程式。
2. 瀏覽至 [http://requestb.in/](http://requestb.in/) 建立 RequestBin 作為回呼 URL。
3. 以 GUID 為 **triggerId** 和 RequestBin URL 為 **callbackUrl** 來呼叫推入觸發程序。![透過 Postman 呼叫推入觸發程序](./media/app-service-api-dotnet-triggers/callpushtriggerfrompostman.PNG)
4. 呼叫**接觸**作業以接觸檔案。下圖顯示透過 Postman 的範例要求。![透過 Postman 呼叫接觸作業](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
5. 請檢查 RequestBin，以確認屬性輸出會叫用推入觸發程序回呼。![透過 Postman 呼叫輪詢觸發程序](./media/app-service-api-dotnet-triggers/pushtriggercallbackinrequestbin.PNG)

### 在 API 定義中描述觸發程序

實作觸發程序，並將您的 API 應用程式部署至 Azure 之後，瀏覽至 Azure Preview 入口網站中的 [**API 定義**] 刀鋒視窗，然後您會看到 UI 已自動辨識觸發程序 (這是由 API 應用程式的 Swagger 2.0 API 定義所驅動)。

![API 定義刀鋒視窗](./media/app-service-api-dotnet-triggers/apidefinitionblade.PNG)

如果您按一下 [**下載 Swagger**] 按鈕並開啟 JSON 檔案，您會看到類似下列的結果：

    "/api/files/poll/TouchedFiles": {
      "get": {
        "operationId": "Files_TouchedFilesPollTrigger",
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    },
    "/api/files/push/TouchedFiles/{triggerId}": {
      "put": {
        "operationId": "Files_TouchedFilesPushTrigger",
        ...
        "x-ms-scheduler-trigger": "push"
      }
    }

延伸模組屬性 **x-ms-schedular-trigger** 是 API 定義中所描述的觸發程序，而且當您要求透過閘道要求 API 定義時，若要求符合以下其中一個準則時，API 應用程式閘道會自動加入它。(您也可以手動加入這個屬性。)

- 輪詢觸發程序
    - 如果 HTTP 方法為 **GET**。
    - 如果 **operationId** 屬性包含字串 **trigger**。
    - 如果 **parameters** 屬性所包含參數的 **name** 屬性設定為 **triggerState**。
- 推入觸發程序
    - 如果 HTTP 方法為 **PUT**。
    - 如果 **operationId** 屬性包含字串 **trigger**。
    - 如果 **parameters** 屬性所包含參數的 **name** 屬性設定為 **triggerId**。

## 在邏輯應用程式中使用 API 應用程式觸發程序

### 在邏輯應用程式設計工具中，列出與設定 API 應用程式觸發程序

如果您在 API 應用程式的相同資源群組中建立邏輯應用程式，您只要按一下它，即可將它加入至設計工具的畫布中。請見下圖說明：

![邏輯應用程式設計工具中的觸發程序](./media/app-service-api-dotnet-triggers/triggersinlogicappdesigner.PNG)

![在邏輯應用程式設計工具中設定輸詢觸發程序](./media/app-service-api-dotnet-triggers/configurepolltriggerinlogicappdesigner.PNG)

![在邏輯應用程式設計工具中設定推入觸發程序](./media/app-service-api-dotnet-triggers/configurepushtriggerinlogicappdesigner.PNG)

## 為邏輯應用程式最佳化 API 應用程式觸發程序

將觸發程序加入至 API 應用程式之後，您可以透過幾種方式來改善在邏輯應用程式中使用 API 應用程式的體驗。

比方說，輪詢觸發程序的 **triggerState** 參數應該在邏輯應用程式中設定為下列運算式。此運算式應該評估邏輯應用程式之觸發程序的最後一個叫用，並傳回該值。

	@coalesce(triggers()?.outputs?.body?['triggerState'], '')

注意：如需上述運算式中所使用函式的說明，請參閱[邏輯應用程式工作流程定義語言](https://msdn.microsoft.com/library/azure/dn948512.aspx)的文件。

使用觸發程序時，邏輯應用程式使用者需要為 **triggerState** 參數提供上述運算式。邏輯應用程式設計工具可能透過延伸模組屬性 **x-ms-scheduler-recommendation** 預先設定此值。**x-ms-visibility** 延伸模組屬性的值可以設定為 *internal*，如此參數本身不會顯示在設計工具上。請見下列程式碼片段說明。

    "/api/Messages/poll": {
      "get": {
	    "operationId": "Messages_NewMessageTrigger",
        "parameters": [
          {
            "name": "triggerState",
            "in": "query",
            "required": true,
            "x-ms-visibility": "internal",
            "x-ms-scheduler-recommendation": "@coalesce(triggers()?.outputs?.body?['triggerState'], '')",
            "type": "string"
          }
        ]
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    }

推入觸發程序的 **triggerId** 參數必須為邏輯應用程式的唯一識別碼。建議的最佳作法是使用下列運算式，將此屬性設定為工作流程的名稱：

    @workflow().name

在其 API 定義中使用 **x-ms-scheduler-recommendation** 和 **x-ms-visibility** 延伸模組屬性，API 應用程式可以傳達給邏輯應用程式設計工具，來自動為使用者設定此運算式。

        "parameters":[  
          {  
            "name":"triggerId",
            "in":"path",
            "required":true,
            "x-ms-visibility":"internal",
            "x-ms-scheduler-recommendation":"@workflow().name",
            "type":"string"
          },


### 在 API 定義中加入延伸模組屬性

其他中繼資料資訊 (例如延伸模組屬性 **x-ms-scheduler-recommendation** 和 **x-ms-visibility**) 可以透過以下兩種方式加入 API 定義：靜態或動態。

對於靜態的中繼資料，您可以直接編輯專案中的 */metadata/apiDefinition.swagger.json* 檔案，並手動加入屬性。

針對使用動態中繼資料的 API 應用程式，您可以編輯 SwaggerConfig.cs 檔案來加入作業篩選條件，以加入這些延伸模組。

    GlobalConfiguration.Configuration 
        .EnableSwagger(c =>
            {
                ...
                c.OperationFilter<TriggerStateFilter>();
                ...
            }


以下是如何實作這個類別，以協助處理動態中繼資料案例的範例。

    // Add extension properties on the triggerState parameter
    public class TriggerStateFilter : IOperationFilter
    {

        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
        {
            if (operation.operationId.IndexOf("Trigger", StringComparison.InvariantCultureIgnoreCase) >= 0)
            {
                // this is a possible trigger
                var triggerStateParam = operation.parameters.FirstOrDefault(x => x.name.Equals("triggerState"));
                if (triggerStateParam != null)
                {
                    if (triggerStateParam.vendorExtensions == null)
                    {
                        triggerStateParam.vendorExtensions = new Dictionary<string, object>();
                    }

                    // add 2 vendor extensions
                    // x-ms-visibility: set to 'internal' to signify this is an internal field
                    // x-ms-scheduler-recommendation: set to a value that logic app can use
                    triggerStateParam.vendorExtensions.Add("x-ms-visibility", "internal");
                    triggerStateParam.vendorExtensions.Add("x-ms-scheduler-recommendation",
                                                           "@coalesce(triggers()?.outputs?.body?['triggerState'], '')");
                }
            }
        }
    }
 

<!---HONumber=62-->