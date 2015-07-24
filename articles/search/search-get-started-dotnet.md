<properties
	pageTitle="開始在 .NET 中建立第一個 Azure 搜尋服務應用程式 | Microsoft Azure"
	description="如何從 Azure 搜尋服務 .NET SDK 使用 .NET 用戶端程式庫建置 Visual Studio 方案的教學課程。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

#開始在 .NET 中建立第一個 Azure 搜尋服務應用程式#

了解如何在 Visual Studio 2013 或更高版本中建置自訂 .NET 搜尋應用程式，並使用 Azure 搜尋服務提供搜尋體驗。本教學課程使用 [Azure 搜尋服務 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) 為此練習中所用的物件和作業建置類別，還使用了 Azure 搜尋服務 REST API。

若要執行此範例，必須要有 Azure Search 服務，您可以在 [Azure 入口網站](https://portal.azure.com)中註冊此服務。

> [AZURE.TIP]請到 [Azure 搜尋服務 .NET 範例](http://go.microsoft.com/fwlink/p/?LinkId=530196)下載本教學課程所需的原始程式碼。

##關於資料##

此範例應用程式使用的[美國地理服務中心 (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) 資料已依據羅德島州進行篩選，藉此減少資料集的大小。我們將使用此資料，建置可傳回地標建築物 (例如醫院和學校) 及地理特徵 (例如河流、湖泊和山峰) 等相關資料的搜尋應用程式。

在此應用程式中，**DataIndexer** 程式會使用[索引子](https://msdn.microsoft.com/library/azure/dn798918.aspx)建構來建置及載入索引，以從公用 Azure SQL Database 擷取篩選過的 USGS 資料集。程式碼中提供線上資料來源的認證和連接資訊。不需要進一步設定。

> [AZURE.NOTE]我們在此資料集套用了一個篩選，以維持不超過免費版定價層的 10,000 個文件的數量上限。如果使用標準版定價層，就不會套用此限制。如需各個定價層的容量詳細資料，請參閱[限制和條件約束](https://msdn.microsoft.com/library/azure/dn798934.aspx)。

##建立服務##

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 Jumpbar 中依序按一下 [新增] | [資料 + 儲存體] | [搜尋]。

     ![][1]

3. 設定服務名稱、定價層、資源群組、訂用帳戶及位置。這些設定都是必須設定的項目，且在佈建服務後就不能變更。

     ![][2]

	- **服務名稱**必須是唯一的、小寫、少於 15 個字元，且不得包含空格。此名稱會成為您 Azure 搜尋服務端點的一部分。如需命名慣例的詳細資訊，請參閱[命名規則](https://msdn.microsoft.com/library/azure/dn857353.aspx)。

	- **定價層**決定可用容量和計費方式。以下兩個定價層提供相同的功能，但所處的資源層級不同。

		- **免費版**是在與其他訂戶共用的叢集上執行。此版本所提供的容量，已足夠用來試驗教學課程以及撰寫用於證明概念的程式碼，但並不適用於生產應用程式。部署免費版服務通常只需要幾分鐘時間。
		- **標準版**是在專用的資源上執行作業，且具備高度可擴充性。標準版服務一開始是以一個複本和一個資料分割進行佈建，不過您可以在建立服務後調整容量。部署標準版服務需要較長的時間，通常需要大約 15 分鐘時間。

	- **資源群組**是適用於一般用途之服務和資源的容器。例如，如果您以 Azure 搜尋服務、Azure 網站和 Azure BLOB 儲存體為基礎建立自訂搜尋應用程式，那麼您可以建立資源群組，來將這些服務一起放在入口網站的管理頁面中。

	- **訂用帳戶**可讓您在多個訂用帳戶間選擇 (如果您有多個訂用帳戶)。

	- **位置**是資料中心區域。目前，所有資源都必須在同一個資料中心內執行。不支援將資源分配到多個資料中心內。

4. 按一下 [建立] 即可佈建服務。

注意 Jumpbar 中的通知。服務一旦準備就緒可供使用時就會顯示通知。

<a id="sub-2"></a>
##尋找 Azure 搜尋服務的服務名稱和 API 金鑰

建立服務之後，可以返回入口網站取得 URL 或 `api-key`。若要連接至搜尋服務，必須同時擁有 URL 和 `api-key` 才能驗證呼叫。

1. 在 Jumpbar 中按一下 [首頁]，然後按一下搜尋服務以開啟服務儀表板。

2. 您會在服務儀表板上看到基本資訊磚，以及存取系統管理金鑰的鑰匙圖示。

  	![][3]

3. 複製服務 URL 和系統管理金鑰，稍後會需要將它們加到 Visual Studio 專案的 app.config 和 web.config 檔案中。

##開啟新專案和方案##

此方案會含有兩個專案：

- **DataIndexer**：Visual C# 主控台應用程式，用於載入資料
- **SimpleSearchMVCApp**：Visual C# ASP.NET MVC Web 應用程式，用於查詢及傳回搜尋結果。

在此步驟中，您會建立這兩個專案。

1. 啟動 [Visual Studio] | [新增專案] | [Visual C#] | [主控台應用程式]。
2. 將專案命名為 **DataIndexer**，然後將方案命名為 **AzureSearchDotNetDemo**。
3. 在方案總管中，以滑鼠右鍵按一下方案 | [新增] | [新增專案] | [Visual C#] | [ASP.NET Web 應用程式]。
4. 將專案命名為 **SimpleSearchMVCApp**。
5. 在 [新增 ASP.NET 專案] 中，選擇 MVC 範本，然後清除選項，以避免建立本教學課程不會用到的程式成品。

   清除 [Azure 主機] 和 [單元測試] 的核取方塊，然後將 [驗證] 設為 [無]。

   ![][10]

完成建立專案時，方案看起來應該會類似以下範例。

   ![][4]

##安裝 .NET 用戶端程式庫及更新其他封裝

1. 在方案總管中，以滑鼠右鍵按一下方案，然後按一下 [管理 NuGet 封裝]。
2. 指定 [更新] | [僅限穩定] | [全部更新]。

   ![][11]

3. 請接受其他封裝的安裝，如此一來才能安裝所有相依性。

4. 接著安裝 Azure 搜尋服務 .NET 用戶端程式庫。請務必指定正確的搜尋，否則無法輕鬆找到封裝。再以滑鼠右鍵按一下 [管理 NuGet 封裝]。

5. 指定 [線上] | [nuget.org] | [包含發行前版本]，接著搜尋 *azure.search*，然後安裝程式庫。

   ![][12]

此範例使用的部分組件清單如下所示。

   ![][5]

##為 System.Configuration 新增組件參考

**DataIndexer** 使用 **System.Configuration** 讀取 app.config 中的組態設定。

1. 以滑鼠右鍵按一下 [DataIndexer] [新增]| | [參考] | [架構] | [System.Configuration]。選取核取方塊。
2. 按一下 [確定]。

##更新組態檔案

每個專案都包含指定服務名稱和 API 金鑰的組態檔案。

1. 在 **DataIndexer** 中，將 App.config 替換為以下範例，然後以適用您服務的有效值更新 [SERVICE NAME] 和 [SERVICE KEY]。

   服務名稱不是完整的 URL。例如，如果搜尋服務端點為 *https://mysearchsrv.search.microsoft.net*，則您要於 App.config 中輸入的服務名稱為 *mysearchsrv*。

	    <?xml version="1.0" encoding="utf-8"?>
	    <configuration>
	      <startup>
	         <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	      </startup>
	      <appSettings>
	        <add key="SearchServiceName" value="[SERVICE NAME]" />
	        <add key="SearchServiceApiKey" value="[SERVICE KEY]" />
	      </appSettings>
	    </configuration>

2. 在 **SimpleSearchMVCApp** 中，將 Web.config 替換為以下範例，然後再次以適用您服務的有效值更新 [SERVICE NAME] 和 [SERVICE KEY]。

		<?xml version="1.0" encoding="utf-8"?>
		<!--
		  For more information on how to configure your ASP.NET application, please visit
		  http://go.microsoft.com/fwlink/?LinkId=152368
		  -->
		<configuration>
		  <configSections>
		    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
		    <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=5.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
		  </configSections>
		  <connectionStrings>
		    <add name="DefaultConnection" providerName="System.Data.SqlClient" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-SimpleMVCApp-20150303114355;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-SimpleMVCApp-20150303114355.mdf" />
		  </connectionStrings>
		  <appSettings>
		    <add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
		    <add key="SearchServiceApiKey" value="[API KEY]" />

		    <add key="webpages:Version" value="2.0.0.0" />
		    <add key="webpages:Enabled" value="false" />
		    <add key="PreserveLoginUrl" value="true" />
		    <add key="ClientValidationEnabled" value="true" />
		    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
		  </appSettings>
		  <system.web>
		    <httpRuntime targetFramework="4.5" />
		    <compilation debug="true" targetFramework="4.5" />
		    <authentication mode="Forms">
		      <forms loginUrl="~/Account/Login" timeout="2880" />
		    </authentication>
		    <pages>
		      <namespaces>
		        <add namespace="System.Web.Helpers" />
		        <add namespace="System.Web.Mvc" />
		        <add namespace="System.Web.Mvc.Ajax" />
		        <add namespace="System.Web.Mvc.Html" />
		        <add namespace="System.Web.Optimization" />
		        <add namespace="System.Web.Routing" />
		        <add namespace="System.Web.WebPages" />
		      </namespaces>
		    </pages>
		    <profile defaultProvider="DefaultProfileProvider">
		      <providers>
		        <add name="DefaultProfileProvider" type="System.Web.Providers.DefaultProfileProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
		      </providers>
		    </profile>
		    <membership defaultProvider="DefaultMembershipProvider">
		      <providers>
		        <add name="DefaultMembershipProvider" type="System.Web.Providers.DefaultMembershipProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" enablePasswordRetrieval="false" enablePasswordReset="true" requiresQuestionAndAnswer="false" requiresUniqueEmail="false" maxInvalidPasswordAttempts="5" minRequiredPasswordLength="6" minRequiredNonalphanumericCharacters="0" passwordAttemptWindow="10" applicationName="/" />
		      </providers>
		    </membership>
		    <roleManager defaultProvider="DefaultRoleProvider">
		      <providers>
		        <add name="DefaultRoleProvider" type="System.Web.Providers.DefaultRoleProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
		      </providers>
		    </roleManager>
		    <!--
		            If you are deploying to a cloud environment that has multiple web server instances,
		            you should change session state mode from "InProc" to "Custom". In addition,
		            change the connection string named "DefaultConnection" to connect to an instance
		            of SQL Server (including SQL Azure and SQL  Compact) instead of to SQL Server Express.
		      -->
		    <sessionState mode="InProc" customProvider="DefaultSessionProvider">
		      <providers>
		        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
		      </providers>
		    </sessionState>
		  </system.web>
		  <system.webServer>
		    <validation validateIntegratedModeConfiguration="false" />
		    <handlers>
		      <remove name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" />
		      <remove name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" />
		      <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
		      <add name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness32" responseBufferLimit="0" />
		      <add name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness64" responseBufferLimit="0" />
		      <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
		      <remove name="OPTIONSVerbHandler" />
		      <remove name="TRACEVerbHandler" />
		    </handlers>
		  </system.webServer>
		  <entityFramework>
		    <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
		      <parameters>
		        <parameter value="v12.0" />
		      </parameters>
		    </defaultConnectionFactory>
		  </entityFramework>
		  <runtime>
		    <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
		      <dependentAssembly>
		        <assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" culture="neutral" />
		        <bindingRedirect oldVersion="0.0.0.0-6.0.0.0" newVersion="6.0.0.0" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" culture="neutral" />
		        <bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" culture="neutral" />
		        <bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
		        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
		        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
		        <bindingRedirect oldVersion="1.0.0.0-5.2.3.0" newVersion="5.2.3.0" />
		      </dependentAssembly>
		      <dependentAssembly>
		        <assemblyIdentity name="System.Web.WebPages.Razor" publicKeyToken="31bf3856ad364e35" />
		        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
		      </dependentAssembly>
		    </assemblyBinding>
		  </runtime>
		</configuration>


##修改 DataIndexer

此程式為主控台應用程式，會連接至您的搜尋服務 (如 app.config 中所指定) 並建立索引，然後用儲存在 Azure SQL Database 中的 USGS 資料集載入該索引。

目前用戶端程式庫的發行前版本不支援索引子，因此我們要在這部分的教學課程中透過 REST API 建立及使用索引子。

執行此程式之前，要先做兩個修改：

- 新增 **AzureSearchhelper.cs**，該項目用於呼叫 REST API 處理連接，以及序列化和還原序列化 JSON 要求和回應。

- 取代 **Program.cs**，該項目用於建立索引、索引子、載入資料及寫入訊息。

###建立 AzureSearchHelper.cs

呼叫 REST API 的程式碼應包含處理連接，以及序列化和還原序列化 JSON 要求和回應的類別。在與 Azure 搜尋服務一起提供的範例中，此類別通常稱為 **AzureSearchHelper.cs**。您可以使用以下程式碼，建立此類別並將其新增 **DataIndexer**。

1. 在方案總管中，以滑鼠右鍵按一下 [DataIndexer] | [新增] | [新增項目] | [程式碼] | [類別]。
2. 將類別命名為 **AzureSearchHelper**。
3. 使用以下程式碼取代預設程式碼。

		//Copyright 2015 Microsoft

		//Licensed under the Apache License, Version 2.0 (the "License");
		//you may not use this file except in compliance with the License.
		//You may obtain a copy of the License at

		//       http://www.apache.org/licenses/LICENSE-2.0

		//Unless required by applicable law or agreed to in writing, software
		//distributed under the License is distributed on an "AS IS" BASIS,
		//WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		//See the License for the specific language governing permissions and
		//limitations under the License.

		using System;
		using System.Net.Http;
		using System.Text;
		using Newtonsoft.Json;
		using Newtonsoft.Json.Converters;
		using Newtonsoft.Json.Serialization;

		namespace DataIndexer
		{
		    public class AzureSearchHelper
		    {
		        public const string ApiVersionString = "api-version=2015-02-28";

		        private static readonly JsonSerializerSettings _jsonSettings;

		        static AzureSearchHelper()
		        {
		            _jsonSettings = new JsonSerializerSettings
		            {
		                Formatting = Formatting.Indented, // for readability, change to None for compactness
		                ContractResolver = new CamelCasePropertyNamesContractResolver(),
		                DateTimeZoneHandling = DateTimeZoneHandling.Utc
		            };

		            _jsonSettings.Converters.Add(new StringEnumConverter());
		        }

		        public static string SerializeJson(object value)
		        {
		            return JsonConvert.SerializeObject(value, _jsonSettings);
		        }

		        public static T DeserializeJson<T>(string json)
		        {
		            return JsonConvert.DeserializeObject<T>(json, _jsonSettings);
		        }

		        public static HttpResponseMessage SendSearchRequest(HttpClient client, HttpMethod method, Uri uri, string json = null)
		        {
		            UriBuilder builder = new UriBuilder(uri);
		            string separator = string.IsNullOrWhiteSpace(builder.Query) ? string.Empty : "&";
		            builder.Query = builder.Query.TrimStart('?') + separator + ApiVersionString;

		            var request = new HttpRequestMessage(method, builder.Uri);

		            if (json != null)
		            {
		                request.Content = new StringContent(json, Encoding.UTF8, "application/json");
		            }

		            return client.SendAsync(request).Result;
		        }

		        public static void EnsureSuccessfulSearchResponse(HttpResponseMessage response)
		        {
		            if (!response.IsSuccessStatusCode)
		            {
		                string error = response.Content == null ? null : response.Content.ReadAsStringAsync().Result;
		                throw new Exception("Search request failed: " + error);
		            }
		        }
		    }
		}



###更新 Program.cs

1. 在方案總管中開啟 **DataIndexer** | **Program.cs**。
2. 以下列程式碼取代 Program.cs 的內容。

		using Microsoft.Azure;
		using Microsoft.Azure.Search;
		using Microsoft.Azure.Search.Models;
		using Microsoft.Spatial;
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Timers;

		namespace DataIndexer
		{
		    class Program
		    {
		        private static SearchServiceClient _searchClient;
		        private static SearchIndexClient _indexClient;

		        // This Sample shows how to delete, create, upload documents and query an index
		        static void Main(string[] args)
		        {
		            string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
		            string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

		            // Create an HTTP reference to the catalog index
		            _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
		            _indexClient = _searchClient.Indexes.GetClient("geonames");

		            Console.WriteLine("{0}", "Deleting index...\n");
		            if (DeleteIndex())
		            {
		                Console.WriteLine("{0}", "Creating index...\n");
		                CreateIndex();
		                Console.WriteLine("{0}", "Sync documents from Azure SQL...\n");
		                SyncDataFromAzureSQL();
		            }
		            Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
		            Console.ReadKey();
		        }

		        private static bool DeleteIndex()
		        {
		            // Delete the index if it exists
		            try
		            {
		                _searchClient.Indexes.Delete("geonames");
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine("Error deleting index: {0}\r\n", ex.Message.ToString());
		                Console.WriteLine("Did you remember to add your SearchServiceName and SearchServiceApiKey to the app.config?\r\n");
		                return false;
		            }

		            return true;
		        }

		        private static void CreateIndex()
		        {
		            // Create the Azure Search index based on the included schema
		            try
		            {
		                var definition = new Index()
		                {
		                    Name = "geonames",
		                    Fields = new[]
		                    {
		                        new Field("FEATURE_ID",     DataType.String)         { IsKey = true,  IsSearchable = false, IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
		                        new Field("FEATURE_NAME",   DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("FEATURE_CLASS",  DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("STATE_ALPHA",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("STATE_NUMERIC",  DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("COUNTY_NAME",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("COUNTY_NUMERIC", DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("ELEV_IN_M",      DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("ELEV_IN_FT",     DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("MAP_NAME",       DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
		                        new Field("DESCRIPTION",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
		                        new Field("HISTORY",        DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
		                        new Field("DATE_CREATED",   DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
		                        new Field("DATE_EDITED",    DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true}
		                    }
		                };

		                _searchClient.Indexes.Create(definition);
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine("Error creating index: {0}\r\n", ex.Message.ToString());
		            }

		        }

		        private static void SyncDataFromAzureSQL()
		        {
		            // This will use the Azure Search Indexer to synchronize data from Azure SQL to Azure Search
		            Uri _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
		            HttpClient _httpClient = new HttpClient();
		            _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);

		            Console.WriteLine("{0}", "Creating Data Source...\n");
		            Uri uri = new Uri(_serviceUri, "datasources/usgs-datasource");
		            string json = "{ 'name' : 'usgs-datasource','description' : 'USGS Dataset','type' : 'azuresql','credentials' : { 'connectionString' : 'Server=tcp:azs-playground.database.windows.net,1433;Database=usgs;User ID=reader;Password=EdrERBt3j6mZDP;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;' },'container' : { 'name' : 'GeoNamesRI' }} ";
		            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Put, uri, json);
		            if (response.StatusCode != HttpStatusCode.Created && response.StatusCode != HttpStatusCode.NoContent)
		            {
		                Console.WriteLine("Error creating data source: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            Console.WriteLine("{0}", "Creating Indexer...\n");
		            uri = new Uri(_serviceUri, "indexers/usgs-indexer");
		            json = "{ 'name' : 'usgs-indexer','description' : 'USGS data indexer','dataSourceName' : 'usgs-datasource','targetIndexName' : 'geonames','parameters' : { 'maxFailedItems' : 10, 'maxFailedItemsPerBatch' : 5, 'base64EncodeKeys': false }}";
		            response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Put, uri, json);
		            if (response.StatusCode != HttpStatusCode.Created && response.StatusCode != HttpStatusCode.NoContent)
		            {
		                Console.WriteLine("Error creating indexer: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            Console.WriteLine("{0}", "Syncing data...\n");
		            uri = new Uri(_serviceUri, "indexers/usgs-indexer/run");
		            response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Post, uri);
		            if (response.StatusCode != HttpStatusCode.Accepted)
		            {
		                Console.WriteLine("Error running indexer: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            bool running = true;
		            Console.WriteLine("{0}", "Synchronization running...\n");
		            while (running)
		            {
		                uri = new Uri(_serviceUri, "indexers/usgs-indexer/status");
		                response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
		                if (response.StatusCode != HttpStatusCode.OK)
		                {
		                    Console.WriteLine("Error polling for indexer status: {0}", response.Content.ReadAsStringAsync().Result);
		                    return;
		                }

		                var result = AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
		                if (result.lastResult != null)
		                {
		                    switch ((string)result.lastResult.status)
		                    {
		                        case "inProgress":
		                            Console.WriteLine("{0}", "Synchronization running...\n");
		                            Thread.Sleep(1000);
		                            break;

		                        case "success":
		                            running = false;
		                            Console.WriteLine("Synchronized {0} rows...\n", result.lastResult.itemsProcessed.Value);
		                            break;

		                        default:
		                            running = false;
		                            Console.WriteLine("Synchronization failed: {0}\n", result.lastResult.errorMessage);
		                            break;
		                    }
		                }
		            }
		        }
		    }
		}



##建置並執行 DataIndexer

1. 以滑鼠右鍵按一下 **DataIndexer** 專案。
2. 建置專案。
3. 按 **F5** 執行該專案。

您應該會看見含有這些訊息的主控台視窗。

![][6]

在入口網站中，您應該會看到新的 **geonames** 索引。入口網站要花數分鐘才能抓到資料，所以請稍候片刻再查看結果。

![][7]

##修改 SimpleSearchMVCApp

**SimpleSearchMVC** 是在 IIS Express 中本機執行的 Web 應用程式。它提供搜尋方塊，並以資料表顯示搜尋結果。

執行此程式之前，要先做三個修改：

- 取代用於接受使用者輸入內容的 **HomeController.cs**。在此範例中，搜尋字詞會儲存在名為 *q* 的變數中。

- 取代 **index.cshtml**，此網頁會提供搜尋字詞輸入內容以及顯示搜尋結果。

- 新增 **FeatureSearch.cs**，此類別會建立搜尋用戶端以及執行搜尋。

###更新 HomeController.cs

使用以下程式碼取代預設程式碼。

	using Microsoft.Azure.Search.Models;
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.Mvc;

	namespace SimpleSearchMVCApp.Controllers
	{
	    public class HomeController : Controller
	    {
	        //
	        // GET: /Home/
	        private FeaturesSearch _featuresSearch = new FeaturesSearch();

	        public ActionResult Index()
	        {
	            return View();
	        }

	        public ActionResult Search(string q = "")
	        {
	            // If blank search, assume they want to search everything
	            if (string.IsNullOrWhiteSpace(q))
	                q = "*";

	            return new JsonResult
	            {
	                JsonRequestBehavior = JsonRequestBehavior.AllowGet,
	                Data = _featuresSearch.Search(q)
	            };
	        }


	    }
	}


###更新 Index.cshtml

使用以下程式碼取代預設程式碼。

	@{
	    ViewBag.Title = "Azure Search - Feature Search";
	}

	<script src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2.min.js"></script>
	<script type="text/javascript">

	    $(function () {
	        // Execute search if user clicks enter
	        $("#q").keyup(function (event) {
	            if (event.keyCode == 13) {
	                Search();
	            }
	        });
	    });

	    function Search() {
	        // We will post to the MVC controller and parse the full results on the client side
	        // You may wish to do additional pre-processing on the data before sending it back to the client
	        var q = $("#q").val();

	        $.post('/home/search',
	        {
	            q: q
	        },
	        function (data) {
	            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
	            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
	            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
	            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
	            searchResultsHTML += "<td>DATE EDITED</td></tr>";
	            for (var i = 0; i < data.length; i++) {
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
	            }

	            $("#searchResults").html(searchResultsHTML);

	        });

	        function parseJsonDate(jsonDateString) {
	            if (jsonDateString != null)
	                return new Date(parseInt(jsonDateString.replace('/Date(', '')));
	            else
	                return "";
	        }
	    };

	</script>
	<h2>USGS Search for Rhode Island</h2>

	<div class="container">
	    <input type="search" name="q" id="q" autocomplete="off" size="100" /> <button onclick="Search();">Search</button>
	</div>
	<br />
	<div class="container">
	    <div class="row">
	        <table id="searchResults" border="1"></table>
	    </div>
	</div>


###新增 FeaturesSearch.cs

將提供搜尋功能的類別到您的應用程式中。

1. 在方案總管中，以滑鼠右鍵按一下 [SimpleSearchMVCApp] | [新增] | [新增項目] | [程式碼] | [類別]。
2. 將類別命名為 **FeaturesSearch**。
3. 使用以下程式碼取代預設程式碼。

		using Microsoft.Azure.Search;
		using Microsoft.Azure.Search.Models;
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.Linq;
		using System.Web;

		namespace SimpleSearchMVCApp
		{
		    public class FeaturesSearch
		    {
		        private static SearchServiceClient _searchClient;
		        private static SearchIndexClient _indexClient;

		        public static string errorMessage;

		        static FeaturesSearch()
		        {
		            try
		            {
		                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
		                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

		                // Create an HTTP reference to the catalog index
		                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
		                _indexClient = _searchClient.Indexes.GetClient("geonames");
		            }
		            catch (Exception e)
		            {
		                errorMessage = e.Message.ToString();
		            }
		        }

		        public DocumentSearchResponse Search(string searchText)
		        {
		            // Execute search based on query string
		            try
		            {
		                SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
		                return _indexClient.Documents.Search(searchText, sp);
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine("Error querying index: {0}\r\n", ex.Message.ToString());
		            }
		            return null;
		        }

		    }
		}

###將 SimpleSearchMVCApp 設為起始專案

以滑鼠右鍵按一下 **SimpleSearchMVCApp** 專案，將其設為起始專案。

##建置並執行 SimpleSearchMVCApp

建置並執行此程式時，您應該會在預設瀏覽器中看到一個提供搜尋方塊的網頁，您可以由此搜尋方塊存取儲存在 Azure 搜尋服務服務之索引中的 USGS 資料。

![][8]

###搜尋 USGS 資料

USGS 資料集包含與羅德島州相關的記錄。如果您在空白的搜尋方塊中按一下 [搜尋]，預設會出現前 50 個項目。

輸入搜尋字詞可提供縮小搜尋範圍的搜尋引擎準則。試著輸入區域名稱。*Roger Williams* 是羅德島州的第一任州長。許多公園、建築物和學校都是以他的姓名命名。

![][9]

您也可以試著查詢、新增或移除片語或運算子，以重新設定搜尋範圍：

- Pawtucket OR Pembroke
- goose +cape -neck


##後續步驟##

這是第一個以 .NET 和 USGS 資料集為基礎的 Azure 搜尋服務教學課程。我們會漸漸擴充本教學課程，以示範其他您可能會想用在自訂方案中的搜尋功能。

如果您已有一些 Azure 搜尋服務的背景知識，可以利用此範例做為試用建議工具 (預先輸入或自動完成查詢)、篩選及多面向導覽的跳板。您也可以新增計數和批次處理文件，讓使用者可以逐頁查看結果，藉此改進搜尋結果頁面。

不熟悉 Azure 搜尋服務嗎？ 建議您嘗試學習其他教學課程，深入了解您還可以建立哪些東西。請瀏覽我們的[文件頁面](http://azure.microsoft.com/documentation/services/search/)以尋找更多資源。您也可以查看我們[影片和教學課程清單](https://msdn.microsoft.com/library/azure/dn798933.aspx)中的連結，以存取更多資訊。

<!--Image references-->
[1]: ./media/search-get-started-dotnet/create-search-portal-1.PNG
[2]: ./media/search-get-started-dotnet/create-search-portal-2.PNG
[3]: ./media/search-get-started-dotnet/create-search-portal-3.PNG

[4]: ./media/search-get-started-dotnet/AzSearch-DotNet-VSSolutionExplorer.png
[5]: ./media/search-get-started-dotnet/AzSearch-DotNet-Assembly.png

[6]: ./media/search-get-started-dotnet/consolemessages.png
[7]: ./media/search-get-started-dotnet/portalindexstatus.png
[8]: ./media/search-get-started-dotnet/usgssearchbox.png
[9]: ./media/search-get-started-dotnet/rogerwilliamsschool.png

[10]: ./media/search-get-started-dotnet/AzSearch-DotNet-MVCOptions.PNG
[11]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-1.PNG
[12]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-2.PNG
 

<!---HONumber=July15_HO2-->