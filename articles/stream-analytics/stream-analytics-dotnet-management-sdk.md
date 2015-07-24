<properties 
	pageTitle="了解如何使用串流分析管理 .NET SDK | Microsoft Azure" 
	description="Azure 串流分析管理 .NET SDK 入門。了解如何設定及執行分析工作：建立專案、輸入、輸出及轉換。" 
	keywords=".net skd,analytics jobs,event hub"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="06/17/2015" 
	ms.author="jeffstok"/>


# 使用 Azure 串流分析管理 .NET SDK 設定及執行分析工作

了解如何使用 Stream Analytics Management .NET SDK 設定及執行分析工作設定專案，建立輸入與輸出來源、轉換，以及開始和停止工作。對於您的分析工作，您可以從 Blob 儲存體或從事件中樞串流資料。

Azure 資料流分析是完全受管理的服務，可用來對雲端中的串流資料進行低延遲、高可用性、可延展的複雜事件處理。串流分析可讓客戶設定串流工作以分析資料流，並可讓客戶以接近即時的方式進行分析。

對於 .NET API 參照，請參閱 [Stream Analytics Management .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx)。


## 必要條件
開始閱讀本文之前，您必須符合下列必要條件：

- 安裝 Visual Studio 2012 或 2013。
- 下載並安裝 [Azure .NET SDK](http://azure.microsoft.com/downloads/)。 
- 在您的訂閱中建立 Azure 資源群組。下列是 PowerShell 指令碼範例。如需 Azure PowerShell 資訊，請參閱[安裝並設定 Azure PowerShell](../install-configure-powershell.md)。  


		# Configure the Azure PowerShell session to access Azure Resource Manager
		Switch-AzureMode AzureResourceManager

		# Log in to your Azure account
		Add-AzureAccount

		# Select the Azure subscription you want to use to create the resource group
		Select-AzureSubscription -SubscriptionName <subscription name>

		# Create an Azure resource group	
		New-AzureResourceGroup -Name <YOUR RESORUCE GROUP NAME> -Location <LOCATION>


-	設定要使用的輸入來源和輸出目標。請參閱[開始使用 Azure 資料流分析 (stream-analytics-get-started.md)](stream-analytics-get-started.md)，以設定要在本文中使用的範例輸入及/或輸出。


## 設定專案

若要建立分析工作，請先設定您的專案。

1. 建立 Visual Studio C# .NET 主控台應用程式。
2. 在 Package Manager Console 中，執行下列命令以安裝 NuGet 封裝。第一個是 Azure 串流分析管理 .NET SDK。第二個是驗證要使用的 Azure Active Directory 用戶端。

		Install-Package Microsoft.Azure.Management.StreamAnalytics -Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. 將下列 **appSettings** 區段加入 App.config 檔案：

		<appSettings>
		  <!--CSM Prod related values-->
		  <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		  <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		  <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		  <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		  <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		  <add key="SubscriptionId" value="<YOUR AZURE SUBSCRIPTION>" />
		  <add key="ActiveDirectoryTenantId" value="<YOU TENANT ID>" />
		</appSettings>


	以您的 Azure 訂用帳戶與租用戶識別碼取代 **SubscriptionId** 和 **ActiveDirectoryTenantId** 的值。您可以藉由執行下列 Azure PowerShell Cmdlet 來取得這些值：

		Get-AzureAccount

5. 將下列 **using** 陳述式加入專案的原始程式檔 (Program.cs) 中。

		using System;
		using System.Configuration;
		using System.Threading;
		using Microsoft.Azure;
		using Microsoft.Azure.Management.StreamAnalytics;
		using Microsoft.Azure.Management.StreamAnalytics.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.	新增驗證協助程式方法：

		public static string GetAuthorizationHeader()
		{
		    AuthenticationResult result = null;
		    var thread = new Thread(() =>
		    {
		        try
		        {
		            var context = new AuthenticationContext(
						ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
						ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
		
		            result = context.AcquireToken(
		                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
		                clientId: ConfigurationManager.AppSettings["AsaClientId"],
		                redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
		                promptBehavior: PromptBehavior.Always);
		        }
		        catch (Exception threadEx)
		        {
		            Console.WriteLine(threadEx.Message);
		        }
		    });
		
		    thread.SetApartmentState(ApartmentState.STA);
		    thread.Name = "AcquireTokenThread";
		    thread.Start();
		    thread.Join();
		
		    if (result != null)
		    {
		        return result.AccessToken;
		    }
		
		    throw new InvalidOperationException("Failed to acquire token");
		}  


## 建立資料流分析管理用戶端

**StreamAnalyticsManagementClient** 物件可讓您管理工作和工作元件，例如輸入、輸出和轉換。

在 **Main** 方法的開頭加入下列程式碼：

	string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
	string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
	string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
	string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
	string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";
	
	// Get authentication token
	TokenCloudCredentials aadTokenCredentials =
	    new TokenCloudCredentials(
	        ConfigurationManager.AppSettings["SubscriptionId"],
	        GetAuthorizationHeader());
	
	// Create Stream Analytics management client
	StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

**resourceGroupName** 變數的值應該會與您在先決條件步驟中建立或選取的資源群組名稱相同。

本文的其餘章節會假設 **Main** 方法的開頭已有這段程式碼。

## 建立串流分析工作

下列程式碼會在您已定義的資源群組下方建立一個串流分析工作。您稍後可以在工作中加入輸入、輸出和轉換。

	// Create a Stream Analytics job
	JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
	{
	    Job = new Job()
	    {
	        Name = streamAnalyticsJobName,
	        Location = "<LOCATION>",
	        Properties = new JobProperties()
	        {
	            EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
	            Sku = new Sku()
	            {
	                Name = "Standard"
	            }
	        }
	    }
	};
	
	JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## 建立資料流分析輸入來源

下列程式碼會使用 Blob 輸入來源類型和 CSV 序列化，來建立資料流分析輸入來源。若要建立事件中心輸入來源，請使用 **EventHubStreamInputDataSource**，而不是 **BlobStreamInputDataSource**。同樣地，您可以自訂輸入來源的序列化類型。

	// Create a Stream Analytics input source
	InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
	{
	    Input = new Input()
	    {
	        Name = streamAnalyticsInputName,
	        Properties = new StreamInputProperties()
	        {
	            Serialization = new CsvSerialization
	            {
	                Properties = new CsvSerializationProperties
	                {
	                    Encoding = "UTF8",
	                    FieldDelimiter = ","
	                }
	            },
	            DataSource = new BlobStreamInputDataSource
	            {
	                Properties = new BlobStreamInputDataSourceProperties
	                {
	                    StorageAccounts = new StorageAccount[]
	                    {
	                        new StorageAccount()
	                        {
	                            AccountName = "<YOUR STORAGE ACCOUNT NAME>",
	                            AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
	                        }
	                    },
	                    Container = "samples",
	                    PathPattern = ""
	                }
	            }
	        }
	    }
	};
	
	InputCreateOrUpdateResponse inputCreateResponse = 
		client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

輸入來源 (來自 Blob 儲存體或事件中樞) 受限於特定工作。若要在不同的工作中使用相同的輸入來源，您必須重新呼叫此方法，並指定不同的工作名稱。


## 測試資料流分析輸入來源

**TestConnection** 方法可測試資料流分析作業是否能夠連接到輸入來源，以及測試輸入來源類型特定的其他層面。例如，在您在先前步驟中建立的 Blob 輸入來源中，此方法會檢查可用來連接到儲存體帳戶的儲存體帳戶名稱和金鑰組，以及檢查指定的容器是否存在。

	// Test input source connection
	DataSourceTestConnectionResponse inputTestResponse = 
		client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## 建立資料流分析輸出目標

建立輸出目標與建立資料流分析輸入來源非常類似。和輸入來源一樣，輸出目標會繫結至特定工作。若要在不同的工作中使用相同的輸出目標，您必須重新呼叫此方法，並指定不同的工作名稱。

下列程式碼會建立輸出目標 (Azure SQL Database)。您可以自訂輸出目標的資料類型和/或序列化類型。

	// Create a Stream Analytics output target
	OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
	{
	    Output = new Output()
	    {
	        Name = streamAnalyticsOutputName,
	        Properties = new OutputProperties()
	        {
	            DataSource = new SqlAzureOutputDataSource()
	            {
	                Properties = new SqlAzureOutputDataSourceProperties()
	                {
	                    Server = "<YOUR DATABASE SERVER NAME>",
	                    Database = "<YOUR DATABASE NAME>",
	                    User = "<YOUR DATABASE LOGIN>",
	                    Password = "<YOUR DATABASE LOGIN PASSWORD>",
	                    Table = "<YOUR DATABASE TABLE NAME>"
	                }
	            }
	        }
	    }
	};
	
	OutputCreateOrUpdateResponse outputCreateResponse = 
		client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## 測試資料流分析輸出目標

資料流分析輸出目標也有可測試連線的 **TestConnection** 方法。

	// Test output target connection
	DataSourceTestConnectionResponse outputTestResponse = 
		client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## 建立資料流分析轉換

下列程式碼會使用 "select * from Input" 查詢來建立串流分析轉換，並指定為串流分析工作配置一個串流單位。如需有關如何調整資料流單位的詳細資訊，請參閱[調整 Azure 資料流分析工作](stream-analytics-scale-jobs.md)。


	// Create a Stream Analytics transformation
	TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
	{
	    Transformation = new Transformation()
	    {
	        Name = streamAnalyticsTransformationName,
	        Properties = new TransformationProperties()
	        {
	            StreamingUnits = 1,
	            Query = "select * from Input"
	        }
	    }
	};
	
	var transformationCreateResp = 
		client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

和輸入和輸出一樣，轉換也會繫結至建立該轉換的特定串流分析工作。

## 啟動資料流分析工作
建立資料流分析工作及其輸入、輸出和轉換之後，您可以藉由呼叫 **Start** 方法來啟動工作。

下列範例程式碼會啟動自訂輸出開始時間設為 2012 年 12 月 12 日 12:12:12 UTC 的資料流分析工作：

	// Start a Stream Analytics job
	JobStartParameters jobStartParameters = new JobStartParameters
	{
	    OutputStartMode = OutputStartMode.CustomTime,
	    OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
	};
	
	LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## 停止資料流分析工作
您可以藉由呼叫 **Stop** 方法來停止執行中的資料流分析工作。

	// Stop a Stream Analytics job
	LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## 刪除資料流分析工作
**Delete** 方法將會刪除作業及其基礎子資源，包括輸入、輸出，以及轉換工作。

	// Delete a Stream Analytics job
	LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## 取得支援
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-tw/home?forum=AzureStreamAnalytics)。


## 後續步驟

您已經學到使用 .NET SDK 建立及執行分析工作的基本知識。若要深入了解，請參閱下列文章：

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure 串流分析管理 .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx)。
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics 管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=62-->