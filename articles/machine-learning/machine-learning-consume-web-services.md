<properties 
	pageTitle="如何使用已從機器學習實驗發佈的機器學習 Web 服務 | Azure" 
	description="機器學習服務發佈之後，就可以使用 RESTFul Web 服務做為要求-回應服務或批次執行服務。" 
	services="machine-learning" 
	solutions="big-data" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/20/2015" 
	ms.author="bradsev" />


# 如何使用已發佈的 Azure Machine Learning Web 服務

## 簡介

發佈為 Web 服務時，Azure Machine Learning 實驗所提供的 REST API，可供各種裝置和平台使用。這是因為簡單的 REST API 可接受並回應 JSON 格式化的訊息。Azure Machine Learning 入口網站提供的程式碼可用來呼叫 R、C# 和 Python 的 Web 服務。但是要能夠使用任何程式設計語言並從任何裝置呼叫這些服務，必須滿足三個準則：

* 具備網路連線
* 具備執行 HTTPS 要求的 SSL 功能
* 具備剖析 JSON 的能力 (藉由手動或支援程式庫)

這表示可從 Web 應用程式、行動應用程式、自訂桌面應用程式和甚至從 Excel 使用服務！  

使用 Azure Machine Learning Web 服務有兩種不同方式，可以做為要求-回應服務，或者批次執行服務。在這兩個案例中，一旦發佈實驗之後，就可以使用透過 RESTFul Web 服務提供的功能。在 Azure 中使用 Azure Web 服務端點部署機器學習 Web 服務，服務會根據使用量自動調整，您可以避免預先且持續付出硬體資源成本。

<!-- 請在發佈本文章時修正連結並取消註解
如需如何使用 REST API 管理 Azure Machine Learning Web 服務端點的詳細資訊，請參閱 **Azure Machine Learning Web 服務端點**。 
-->

如需有關如何建立和發佈 Azure Machine Learning Web 服務的資訊， 
請參閱[發佈 Azure Machine Learning Web 服務][發佈]。如需建立機器學習實驗和發佈實驗的逐步解說，請參閱[使用 Azure Machine Learning 開發預測解決方案][逐步解說]。

[發佈]: machine-learning-publish-a-machine-learning-web-service.md
[逐步解說]: machine-learning-walkthrough-develop-predictive-solution.md


## 要求回應服務 (RRS)

「要求回應服務 (RRS)」是一種低延遲、高度可擴充的 Web 服務，可用來為從 Azure Machine Learning Studio 實驗建立並發佈的無狀態模型提供介面。

RRS 接受單一資料列的輸入參數，並會產生單一資料列作為輸出內容。輸出資料列可包含多個資料行。

RRS 範例驗證應用程式的真確性。您可以將絕大多數應用程式安裝為此種狀況。應用程式啟動時，它會使用相關輸入呼叫 RRS 服務。然後應用程式會接收來自服務的驗證回應，允許或封鎖應用程式執行。


## 批次執行服務 (BES)
 
「批次執行服務 (BES)」是一種可為大量的一批資料記錄進行非同步計分的服務。BES 的輸入包含來自各種來源 (例如 blob、Azure 中的表格、SQL Azure、HDInsight (例如 Hive 查詢的結果) 和 HTTP 來源) 的一批記錄。BES 的輸出則包含計分的結果。結果會輸出至位於 Azure blob 儲存體中的檔案，並在回應中傳回儲存體端點的資料。

當您不需要立即收到回應時 (例如為個人或物聯網 (IOT) 裝置定期排程計分)，BES 就很有用。

## 範例
為了同時顯示 RRS 和 BES 兩者如何運作，我們使用 Azure Web 服務範例。此服務將用於 IOT (Internet Of Things) 案例。為了簡單起見，我們的裝置只會傳送一個值， `cog_speed`，並取得單一的回應。 

呼叫 RRS 或 BES 服務需要四種資訊。發佈實驗之後，就可以在 [Azure Machine Learning 服務頁面](https://studio.azureml.net)中取得這項資訊。按一下畫面左側的 [Web 服務] 連結，您會看到已發佈的服務。若要尋找特定服務的相關資訊，RRS 和 BES 兩者都有 API 說明頁面連結。

1.	**服務 API 金鑰**位於服務主要頁面
2.	**服務 URI** 位於所選服務的 API 說明頁面
3.	預期的 **API 要求本文**位於所選服務的 API 說明頁面
4.	預期的 **API 回應本文**位於所選服務的 API 說明頁面

在下列兩個範例中，我們使用 C# 語言說明所需的程式碼，目標平台是 Windows 8 桌面。 

### RRS 範例
在 API 說明頁面上，除了 URI，您要輸入和輸出定義和程式碼範例。API 輸入是特別針對這項服務呼叫，且為 API 呼叫的內容。 

**範例要求**

	{
	  "Inputs": {
	    "input1": {
	      "ColumnNames": [
	        "cog_speed"
	      ],
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}


同樣地，API 回應也是特別針對這項服務呼叫。

**範例回應**

	{
	  "Results": {
	    "output1": {
	      "type": "DataTable",
	      "value": {
	        "ColumnNames": [
	          "cog_speed"
	        ],
	        "ColumnTypes": [
	          "Numeric"
	        ].
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}

您會在頁面底部找到程式碼範例。下面是 C# 實作的程式碼範例 
                   
**範例程式碼**

	using System;
	using System.Collections.Generic;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Formatting;
	using System.Net.Http.Headers;
	using System.Text;
	using System.Threading.Tasks;
	
	namespace CallRequestResponseService
	{
	    public class StringTable
	    {
	        public string[] ColumnNames { get; set; }
	        public string[,] Values { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            using (var client = new HttpClient())
	            {
	                var scoreRequest = new
	                {
	                    Inputs = new Dictionary<string, StringTable> () { 
	                        { 
	                            "input1", 
	                            new StringTable() 
	                            {
	                                ColumnNames = new string[] {"cog_speed"},
	                                Values = new string[,] {  { "0"},  { "1"}  }
	                            }
	                        },
	                    GlobalParameters = new Dictionary<string, string>() { }
	                };
	                
	                const string apiKey = "abc123"; // Replace this with the API key for the web service
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	
	                client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");
	                
	                // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
	                // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
	                // For instance, replace code such as:
	                //      result = await DoSomeTask()
	                // with the following:
	                //      result = await DoSomeTask().ConfigureAwait(false)

	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Result: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	}

### BES 範例
在 API 說明頁面上，除了 URI，您會發現幾個可用呼叫的相關資訊。與 RRS 服務不同，BES 服務是非同步的。這表示 BES API 只是將要執行的工作排入佇列。但它在收到 API 回應之前不會實際執行工作。開發人員可以使用 BES 服務執行三項工作，如下所述。

1. 提交批次執行工作
1. 取得批次執行工作的狀態或結果
1. 刪除批次執行工作  

**1.提交批次執行工作**

提交批次執行工作的方式，是提供儲存批次資料位置的相關資訊。此範例中，我們將假設我們想批次計分的記錄位於儲存體帳戶中的 blob 檔案。

批次工作的回應是工作識別碼，因為工作以非同步方式執行。我們稍後將使用工作識別碼取得工作狀態和結果。

**範例要求**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Output": null,
	  "GlobalParameters": {}
	}

**範例回應**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**範例程式碼**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	
	using System;
	using System.Collections.Generic;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Net.Http.Headers;
	
	namespace CallBatchExecutionService
	{
	    internal class Program
	    {
	        private static void Main(string[] args)
	        {
	            InvokeBatchExecutionService().Wait();
	        }
	
	        private static async Task InvokeBatchExecutionService()
	        {
	            // API Information
	            const string BESUrl = "[BES URI]";
	            const string ApiKey = "abc123"; 
	            // The storage account information
	            const string StorageAccountName = @"mystorageacct"; 
	            const string StorageAccountKey = @"Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==";
	            // Storage file with the batch of records
	            const string StorageInputFile = @"/mycontainermydatablob.csv"; 
	
	
	            String connString = String.Format(
	                "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
	                StorageAccountName,
	                StorageAccountKey);
	
	            BatchRequest request = new BatchRequest();
	            request.Input.RelativeLocation = StorageInputFile;
	            request.Input.ConnectionString = connString;
	
	            using (var client = new HttpClient())
	            {
	                client.BaseAddress = new Uri(BESUrl);
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.PostAsJsonAsync("", request);
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Job ID: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	
	    public class BatchInput
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	
	        public BatchInput()
	        {
	            ConnectionString = null;
	            RelativeLocation = null;
	            BaseLocation = null;
	            SasBlobToken = null;
	        }
	    }
	
	    public class BatchRequest
	    {
	        public BatchInput Input { get; set; }
	
	        public Object Output { get; set; }
	
	        public Dictionary<string, string> GlobalParameters { get; set; }
	
	        public BatchRequest()
	        {
	            this.GlobalParameters = new Dictionary<string, string>();
	            Input = new BatchInput();
	            Output = null;
	        }
	    }
	}
	
**2.取得批次執行工作的狀態或結果**

若要取得工作的結果，您必須先擁有工作識別碼，此識別碼位於對工作提交的回應中。此 API 呼叫沒有任何實際的輸入內容。它使用的 BES URI 和要求方法稍有不同。它不是使用 POST 要求，而是使用 GET 要求，後面接著 API 說明頁面中定義的 URI。
 
不過，回應有分層。

**回應內容**

	{
	    "StatusCode": STATUS_CODE,
	    "Result": RESULT,
	    "Details": DETAILS
	}

`StatusCode` 可能的值為 0、1、2、3 或 4，語法如下：

* 0	尚未啟動
* 1	執行中
* 2	失敗
* 3	已取消
* 4	已完成

如果工作未完成， `Result` 為 **null**。如果工作已完成， `Result` 的格式會是： 

	{
	  "ConnectionString": null,
	  "RelativeLocation": "RELATIVE_LOCATION",
	  "BaseLocation": "BASE_LOCATION",
	  "SasBlobToken": "SAS_BLOB_TOKEN"
	}

詳細資料會顯示錯誤詳細資料 (如果有)。

**範例程式碼**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	//
	// Also, add a reference to Microsoft.WindowsAzure.Storage.dll for reading from and writing to the Azure blob storage
	
	using System;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Headers;
	using System.Threading.Tasks;
	using Newtonsoft.Json;
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;
	
	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            String jobId = "123";
	            InvokeBatchExecutionService(jobId).Wait();
	        }
	
	        static async Task InvokeBatchExecutionService(String JobId)
	        {
	            Console.WriteLine(String.Format("Getting job status for job {0}", JobId));
	
	            // BES Information
	            const string BaseUrl = @"[BES Job Id]/{0}";
	            const string ApiKey = "abc123"; 
	            // Replace this with the location you would like to use for your output file
	            const string OutputFileLocation = @"myresults.csv"; 
	
	            using (var client = new HttpClient())
	            {
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.GetAsync(String.Format(BaseUrl, JobId));
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    BatchResponseStructure responseStruct = JsonConvert.DeserializeObject<BatchResponseStructure>(result);
	
	                    switch (responseStruct.StatusCode)
	                    {
	                        case (int)BatchScoreStatusCode.NotStarted:
	                            Console.WriteLine("Not started...");
	                            break;
	                        case (int)BatchScoreStatusCode.Running:
	                            Console.WriteLine("Running...");
	                            break;
	                        case (int)BatchScoreStatusCode.Failed:
	                            Console.WriteLine("Failed!");
	                            Console.WriteLine(string.Format(@"Error details: {0}", status.Details));
	                            break;
	                        case (int)BatchScoreStatusCode.Cancelled:
	                            Console.WriteLine("Cancelled!");
	                            break;
	                        case (int)BatchScoreStatusCode.Finished:
	                            Console.WriteLine("Finished!");
	                            var credentials = new StorageCredentials(status.Result.SasBlobToken);
	                            var cloudBlob = new CloudBlockBlob(new Uri(new Uri(responseStruct.Result.BaseLocation), 
	                                                                                               responseStruct.Result.RelativeLocation), credentials);
	                            cloudBlob.DownloadToFile(OutputFileLocation, FileMode.Create);
	                            Console.WriteLine(string.Format(@"The results have been written to the file {0}", OutputFileLocation));
	                            break;
	                    }
	                }
	                else
	                {
	                    Console.WriteLine(String.Format("Batch Result : Failed with status code: {0}", response.StatusCode));
	                }
	            }
	        }
	    }
	
	    public enum BatchScoreStatusCode : int
	    {
	        NotStarted = 0,
	        Running = 1,
	        Failed = 2,
	        Cancelled = 3,
	        Finished = 4
	    }
	
	    public class BatchResult
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	    }
	
	    public class BatchResponseStructure
	    {
	        public int StatusCode { get; set; }
	        public BatchResult Result { get; set; }
	        public String Details { get; set; }
	        public BatchResponseStructure()
	        {
	            this.Result = new BatchResult();
	        }
	    }
	}

**3.刪除批次執行工作**              
工作一旦啟動，也可以刪除。我們可能基於各種原因而這麼做，例如工作花太長時間而無法完成。若要刪除工作，您必須先擁有工作識別碼，此識別碼位於對工作提交的回應中。

此 API 呼叫沒有任何實際的輸入內容。BES URI 和要求方法稍有不同。它不是使用 POST 要求，而是使用 DELETE 要求，後面接著 API 說明頁面中定義的 URI。此程式碼範例非常容易理解。


<!--HONumber=49--> 