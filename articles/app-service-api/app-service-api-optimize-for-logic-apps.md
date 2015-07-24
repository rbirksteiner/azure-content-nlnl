
<properties
	pageTitle="增強 Logic Apps 的 API 應用程式"
	description="本文示範如何裝飾 API 應用程式，以與 Logic Apps 運作良好。"
	services="app-service\api"
	documentationCenter=".net"
	authors="sameerch"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="sameerch;guayan;tarcher"/>

# 增強 Logic Apps 的 API 應用程式 #

在本文中，您將學習如何定義 API 應用程式的 API 定義，讓它與 Logic Apps 運作良好。在 Logic Apps 設計工具中使用 API 應用程式時，如此可改善其使用者體驗。

## 必要條件

如果您不熟悉 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中的 [API 應用程式](app-service-api-apps-why-best-platform.md)，則建議您閱讀[建立 API 應用程式](app-service-dotnet-create-api-app.md)上的多組件系列。


## 加入顯示名稱 ##
Logic Apps 設計工具會顯示作業、欄位和參數的名稱，但有時很難讀取透過程式設計方式產生的名稱。若要改善可讀性，Logic Apps 設計工具可以在可用時顯示更容易閱讀的文字值 (稱為「*顯示名稱*」)，而非作業、欄位和參數預設名稱。為了完成這項作業，Logic Apps 設計工具會掃描 API 應用程式所提供的 swagger 中繼資料中是否有特定屬性。下列屬性用做顯示名稱：

* 作業 (動作和觸發程序) **summary** 屬性的值 (存在時)；否則為 **operationId** 屬性的值。請注意，Swagger 2.0 規格允許 **summary** 屬性最多有 120 個字元。

* 參數 (輸入) **x-ms-summary** 擴充功能屬性的值 (存在時)；否則為 **name** 屬性的值。必須在程式碼中動態設定 **x-ms-summary** 擴充功能屬性。本主題的「使用自訂屬性加上擴充功能屬性的註解」小節會描述該程序。您可以使用 /// 註解來設定 **name** 屬性。本主題的「在 API 定義產生中使用 XML 註解」小節會描述該程序。

* 結構描述欄位 (輸出回應) **x-ms-summary** 擴充功能屬性的值 (存在時)；否則為 **name** 屬性的值。必須在程式碼中動態設定 **x-ms-summary** 擴充功能屬性。本主題的「使用自訂屬性加上擴充功能屬性的註解」小節會描述該程序。您可以使用 /// 註解來設定 **name** 屬性。本主題的「在 API 定義產生中使用 XML 註解」小節會描述該程序。

**附註：**建議將顯示名稱的長度保持為小於 30 個 (含) 字元。

### 在 API 定義產生中使用 XML 註解

對於使用 Visual Studio 進行開發，常見做法是使用 [XML 註解](https://msdn.microsoft.com/library/b2s063f7.aspx)加上 API 控制器的註釋。使用 [/doc](https://msdn.microsoft.com/library/3260k4x7.aspx) 進行編譯時，編譯器會建立 XML 文件檔案。隨附於 API App SDK 的 Swashbuckle 工具組可以在產生 API 中繼資料時包括這些註解，而且您可以遵循下列步驟來設定 API 專案執行這項作業：

1. 在 Visual Studio 中，開啟您的專案。

2. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案，然後選取 [**屬性**]。

	![專案屬性](./media/app-service-api-optimize-for-logic-apps/project-properties.png)

3. 專案的屬性頁面出現時，請執行下列步驟：

	- 選取將套用設定的 [**組態**]。通常，您將選取 [所有組態]，讓您指定的設定同時套用至 [偵錯] 和 [發行] 組建。
	
	- 選取左側的 [**建置**] 索引標籤。
	
	- 確認已核取 [**XML 文件檔案**] 選項。Visual Studio 將根據專案名稱來提供預設檔案名稱。您可以將其值設為命名慣例所需的內容，或將它保持原狀。

	![設定 XML 文件屬性](./media/app-service-api-optimize-for-logic-apps/xml-documentation-file-property.png)

4. 開啟 *SwaggerConfig.cs* 檔案 (位於專案的 **App_Start** 資料夾中)。

5. 將 **using** 指示詞加入 **System** 和 **System.Globalization** 命名空間的 *SwaggerConfig.cs* 檔案頂端。

		using System;
		using System.Globalization;
 
6. 搜尋 *SwaggerConfig.cs* 檔案中是否有 **GetXmlCommentsPath** 呼叫，並取消該行的註解，使其執行。因為尚未實作這個方法，所以 Visual Studio 會強調 **GetXmlCommentsPath** 呼叫，並指出它未定義在目前內容中。沒有問題。您將在下一個步驟中進行實作。

7. 將下列 **GetXmlCommentsPath** 方法的實作加入 **SwaggerConfig** 類別 (定義於 *SwaggerConfig.cs* 檔案中)。這種方法只會傳回您稍早在專案設定中指定的 XML 文件檔案。

        public static string GetXmlCommentsPath()
        {
            return String.Format(CultureInfo.InvariantCulture, 
								 @"{0}\bin\ContactsList.xml", 
								 AppDomain.CurrentDomain.BaseDirectory);
        }

8. 最後，指定控制器方法的 XML 註解。若要這樣做，請開啟其中一個 API 應用程式的控制器檔案，並在您想要記載之控制器方法前面的空白行輸入 ///。Visual Studio 會自動插入註解的區段，您可以在其內指定方法摘要以及參數和傳回值資訊。

現在，建置和發佈 API 應用程式時，會看到文件檔案也會在裝載中，並與您 API 應用程式的其餘部分一起上傳。

## 分類進階作業和屬性

Logic Apps 設計工具具有有限的螢幕畫面來顯示作業、參數和屬性。此外，API 應用程式還可以定義一組廣泛的作業和屬性。在小區域中顯示太多資訊的結果可能會導致使用者很難使用設計工具。

若要減輕這類混亂，Logic Apps 設計工具可讓您將 API 應用程式的作業和屬性群組成使用者定義的目錄。使用作業和屬性的正確分類，API 應用程式可以透過先呈現最基本且實用的作業和屬性來改善使用者體驗。

若要提供這項功能，Logic Apps 設計工具會尋找 API 應用程式的 swagger API 定義中是否有特定自訂廠商擴充功能屬性。此屬性的名稱是 **x-ms-visibility**，而且可以有下列值：

* 空白或 "none"。使用者已可檢視這些作業和屬性。

* "advanced"。因為這些是進階作業和屬性，所以預設會予以隱藏。不過，使用者可以在需要時輕鬆地存取它們。

* "internal"。這些作業和屬性視為系統或內部屬性，因此使用者無法直接使用它們。因此，設計工具會隱藏它們，而且只能在程式碼檢視中使用。對於這類屬性，您也可以透過 Logic Apps 設計工具指定 **x-ms-scheduler-recommendation** 擴充功能屬性來設定值。如需範例，請參閱有關[將觸發程式加入 API 應用程式](app-service-api-dotnet-triggers.md)的文章。


## 使用自訂屬性加上擴充功能屬性的註解

如上所述，自訂廠商擴充功能屬性是用來加上 API 中繼資料的註解，以提供 Logic Apps 設計工具可以使用的更多資訊。如果您使用靜態中繼資料來描述 API 應用程式，則可以直接編輯專案中的 */metadata/apiDefinition.swagger.json* 檔案，手動加入必要的擴充功能屬性。

對於使用動態中繼資料的 API 應用程式，您可以使用自訂屬性來加上程式碼的註解。接著，您可以在 *SwaggerConfig.cs* 檔案中定義作業篩選來尋找自訂屬性，以及加入必要的廠商擴充功能。這種方式詳述如下，以動態產生 **x-ms-summary** 擴充功能屬性。

1. 定義稱為 **CustomSummaryAttribute** 而且將用來加上程式碼註解的屬性類別。

	    [AttributeUsage(AttributeTargets.All)]
	    public class CustomSummaryAttribute : Attribute
	    {
	        public string SummaryText { get; internal set; }

	        public CustomSummaryAttribute(string summaryText)
	        {
	            this.SummaryText = summaryText;
	        }
	    }

2. 定義稱為 **AddCustomSummaryFilter** 的作業篩選，而此作業篩選會在作業參數中尋找這個自訂屬性。


	    using Swashbuckle.Swagger;

		...

		public class AddCustomSummaryFilter : IOperationFilter
	    {
	        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
	        {
	            if (operation.parameters == null)
	            {
	                // no parameter
	                return;
	            }

	            foreach (var param in operation.parameters)
	            {
	                var summaryAttributes = apiDescription.ParameterDescriptions.First(x => x.Name.Equals(param.name))
	                                        .ParameterDescriptor.GetCustomAttributes<CustomSummaryAttribute>();

	                if (summaryAttributes != null && summaryAttributes.Count > 0)
	                {
	                    // add x-ms-summary extension
	                    if (param.vendorExtensions == null)
	                    {
	                        param.vendorExtensions = new Dictionary<string, object>();
	                    }
	                    param.vendorExtensions.Add("x-ms-summary", summaryAttributes[0].SummaryText);
	                }
	            }
	        }
	    }

3. 編輯 *SwaggerConfig.cs* 檔案，並加入上面定義的篩選類別。


            GlobalConfiguration.Configuration
                .EnableSwagger(c =>
                    {
                        ...
                        c.OperationFilter<AddCustomSummaryFilter>();
                        ...
                    }

4. 使用 **CustomSummaryAttribute** 類別加上程式碼的註解 (如下列程式碼片段所示)。

        /// <summary>
        /// Send Message
        /// </summary>
        /// <param name="queueName">The name of the Storage Queue</param>
        /// <param name="messageText">The message text to be sent</param>
        public void SendMessage(
            [CustomSummary("Queue Name")] string queueName,
            [CustomSummary("Message Text")] string messageText)
        {
             ...
        }

	建置上述 API 應用程式時，會產生下列 API 中繼資料：


			...
            "post": {
                ...
                "parameters": [
                    {
                        "name": "queueName",
                        "in": "query",
                        "description": "The name of the Storage Queue",
                        "required": true,
                        "x-ms-summary": "Queue Name",
                        "type": "string"
                    },
                    {
                        "name": "messageText",
                        "in": "query",
                        "description": "The message text to be sent",
                        "required": true,
                        "x-ms-summary": "Message Text",
                        "type": "string"
                    }
                ],
                ...

5. 同樣地，您可以定義結構描述篩選 **AddCustomSummarySchemaFilter**，自動加上結構描述模型中 **x-ms-summary** 擴充功能屬性的註解 (如下列範例所示)。

	    public class AddCustomSummarySchemaFilter: ISchemaFilter
	    {
	        public void Apply(Schema schema, SchemaRegistry schemaRegistry, Type type)
	        {
	            SetCustomSummary(schema, type.GetCustomAttribute<CustomSummaryAttribute>());

	            if (schema.properties != null)
	            {
	                foreach (var property in schema.properties)
	                {
	                    var summaryAttribute = type.GetProperty(property.Key).GetCustomAttribute<CustomSummaryAttribute>();
	                    SetCustomSummary(property.Value, summaryAttribute);
	                }
	            }
	        }

	        private static void SetCustomSummary(Schema schema, CustomSummaryAttribute summaryAttribute)
	        {
	            if (summaryAttribute != null)
	            {
	                if (schema.vendorExtensions == null)
	                {
	                    schema.vendorExtensions = new Dictionary<string, object>();
	                }
	                schema.vendorExtensions.Add("x-ms-summary", summaryAttribute.SummaryText);
	            }
	        }
	    }

## 摘要

在本文中，您已了解在 Logic Apps 設計工具中使用 API 應用程式時，如何改善 API 應用程式的使用者體驗。最佳做法是建議您提供所有作業 (動作和觸發程序)、參數和屬性的易記名稱。也建議您提供的基本作業不超過 5 個。對於輸入參數，建議將基本屬性數目限制為不超過 4 個，對於屬性，則建議 5 個以下。您作業和屬性的其餘部分應該標示為進階。
 

<!---HONumber=62-->