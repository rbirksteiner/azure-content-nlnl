<properties 
	pageTitle="連線到 Azure Machine Learning Web 服務 | Microsoft Azure" 
	description="透過 C# 或 Python，使用授權金鑰連線到 Azure Machine Learning Web 服務。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="derrickv" />


# 連線到 Azure Machine Learning Web 服務 
Azure Machine Learning 開發人員體驗是一個 Web 服務 API，可即時或以批次模式從輸入資料進行預測。您可以使用 Azure Machine Learning Studio 來建立預測及發佈 Azure Machine Learning Web 服務。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

若要深入了解如何使用 Studio 建立和發佈 Azure Machine Learning Web 服務，請參閱：

- [發佈機器學習 Web 服務](machine-learning-publish-a-machine-learning-web-service.md)
- [開始使用 ML Studio](http://azure.microsoft.com/documentation/videos/getting-started-with-ml-studio/)
- [Azure Machine Learning 預覽](https://studio.azureml.net/)
- [Machine Learning 文件中心](http://azure.microsoft.com/documentation/services/machine-learning/)

## Azure Machine Learning Web 服務 ##

使用 Azure Machine Learning (ML) Web 服務，外部應用程式會即時與 ML 工作流程計分模型通訊。ML Web 服務呼叫會將預測結果傳回外部應用程式。若要進行 ML Web 服務呼叫，您可以傳遞發佈預測時所建立的 API 金鑰。ML Web 服務以 REST 為基礎，這是一種常見的 Web 程式設計專案架構。

Azure Machine Learning 有兩種類型的服務：

- 要求-回應服務 (RRS) – 這是一種低延遲、高擴充性的服務，針對從 ML Studio 建立和發佈的無狀態模型提供介面。
- 批次執行服務 (BES) – 這是一種非同步的服務，為一批資料記錄進行計分。

如需 Azure Machine Learning Web 服務的詳細資訊，請參閱[發佈 Azure Machine Learning Web 服務](machine-learning-publish-a-machine-learning-web-service.md)。

## 取得 Azure Machine Learning 授權金鑰 ##
您可以從 ML Web 服務取得 Web 服務 API 金鑰。您可以從 Microsoft Azure Machine Learning Studio 或 Azure 管理入口網站取得該金鑰。
### Microsoft Azure Machine Learning Studio ###
1. 在 Microsoft Azure Machine Learning Studio 中，按一下左側的 [Web 服務]。
2. 按一下某個 Web 服務。[API 金鑰] 位於 [儀表板] 索引標籤上。

### Azure 管理入口網站 ###

1. 按一下左側的 [機器學習]。
2. 按一下工作區。
3. 按一下 [Web 服務]。
4. 按一下某個 Web 服務。
5. 按一下某個端點。[API 金鑰] 位於右下角。

## <a id="connect"></a>連線到 Azure Machine Learning Web 服務

您可以使用任何支援 HTTP 要求和回應的程式設計語言，連線到 Azure Machine Learning Web 服務。您可以從 Azure ML Web 服務說明頁面檢視 C#、Python 和 R 的範例，。

### 檢視 Azure ML Web 服務 API 說明頁面 ###
當您發佈 Web 服務時，會建立 Azure ML API 說明頁面。請參閱 [Azure Machine Learning 逐步解說 - 發佈 Web 服務](machine-learning-walkthrough-5-publish-web-service.md)。


**檢視 Azure ML API 說明頁面** 在 Microsoft Azure Machine Learning Studio 中：

1. 選擇 [Web 服務]。
2. 選擇某個 Web 服務。
3. 依序選擇 [API 說明頁面] - [要求/回應] 或 [批次執行]。


**Azure ML API 說明頁面** Azure ML API 說明頁面包含有關預測 Web 服務的詳細資料，包括


<table>
	<tr>
		<td>&#160;</td>
		<td>範例 </td>
	</tr>
	<tr>
		<td>要求 POST URI </td>

		<td>https://ussouthcentral.services.azureml.net/workspaces/{WorkspaceId}/services/{ServiceId}/score
		</td>
	</tr>
	<tr>
		<td>範例要求 </td>
		<td>{ <br/> 
			&#160;&#160; "Id": "score00001",  <br/>
			&#160;&#160; "Instance": <br/>
			&#160;&#160;&#160;&#160; {  <br/>  
 			&#160;&#160;&#160;&#160; &#160;&#160; "FeatureVector": { <br/>
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col1": "0",<br/>      
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col2": "0",<br/>      
			&#160;&#160;&#160;&#160; &#160;&#160;  "Col3": "0",<br/>  
			&#160;&#160;&#160;&#160; &#160;&#160;  ... },  <br/>
			&#160;&#160;&#160;&#160;   "GlobalParameters": {}   <br/>
			&#160;&#160;&#160;&#160; } <br/>
		}</td>
	</tr>
	<tr>
		<td>回應本文 </td>
		<td>
		<table style="width: 100%">

			<tr>
				<td><B>Name</B></td>
				<td><B>資料類型</B></td>
			</tr>
	
			<tr>
				<td>功能</td>
				<td>String</td>
			</tr>
			<tr>
				<td>計數</td>
				<td>數值</td>
			</tr>
			<tr>
				<td>唯一值計數 </td>
				<td>數值 </td>
			</tr>
			<tr>
				<td>...</td>
				<td>...</td>
			</tr>
		</table>
		</td>
	</tr>
	<tr>
		<td>範例回應 </td>
		<td>["Col1","1","1",…] </td>
	</tr>
	<tr>
		<td>範例程式碼 </td>
		<td>(C#、Python 和 R 的範例程式碼) </td>
	</tr>
</table>

**注意** 範例是來自 Azure ML 範例集合中的 Sample 1: Download dataset from UCI: Adult 2 class dataset (範例 1：從 UCI 下載資料集：成人 2 類別資料集)。

### C# 範例 ###

若要連線到 Azure ML Web 服務，請使用 **HttpClient** 傳遞 ScoreData。ScoreData 包含 FeatureVector，這是代表 ScoreData 的數值特徵 N 維向量。您要使用 API 金鑰驗證 Azure ML 服務。

若要連線到 ML Web 服務，必須安裝 **Microsoft.AspNet.WebApi.Client** Nuget 封裝。

**在 Visual Studio 中安裝 Microsoft.AspNet.WebApi.Client Nuget**

1. 發佈 Download dataset from UCI: Adult 2 class dataset 的 Web 服務。
2. 按一下 [工具] > [NuGet 封裝管理員] > [封裝管理員主控台]。
2. 選擇 [Install-package Microsoft.AspNet.WebApi.Client]。

**執行程式碼範例**

1. 發佈 Azure ML 範例集合中的 "Sample 1: Download dataset from UCI: Adult 2 class dataset" 實驗。
2. 使用來自 Web 服務的金鑰指派 apikey。請了解如何取得 Azure ML 授權金鑰。
3. 使用要求 URI 指派 serviceUri。請了解如何取得要求 URI。

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
	    public class ScoreData
	    {
	        public Dictionary<string, string> FeatureVector { get; set; }
	        public Dictionary<string, string> GlobalParameters { get; set; }
	    }
	
	    public class ScoreRequest
	    {
	        public string Id { get; set; }
	        public ScoreData Instance { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	
	            Console.ReadLine();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            //Assign apiKey with the key from a web service.
	            const string apiKey = "{ApiKey}";
	
	            //Assign serviceUri with the Request URI. See How to get a Request URI.
	            const string serviceUri = "{ServiceUri}";
	            
	            using (var client = new HttpClient())
	            {
	                ScoreData scoreData = new ScoreData()
	                {
	                    //Input data
	                    FeatureVector = new Dictionary<string, string>() 
	                    {
	                        { "Col1", "0" },
	                        { "Col2", "0" },
	                        { "Col3", "0" },
	                        { "Col4", "0" },
	                        { "Col5", "0" },
	                        { "Col6", "0" },
	                        { "Col7", "0" },
	                        { "Col8", "0" },
	                        { "Col9", "0" },
	                        { "Col10", "0" },
	                        { "Col11", "0" },
	                        { "Col12", "0" },
	                        { "Col13", "0" },
	                        { "Col14", "0" },
	                        { "Col15", "0" },
	                    },
	                    GlobalParameters = 
	                        new Dictionary<string, string>() {}
	                };
	
	                ScoreRequest scoreRequest = new ScoreRequest()
	                {
	                    Id = "score00001",
	                    Instance = scoreData
	                };
	
	                //Set authorization header
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	             
	                client.BaseAddress = new Uri(serviceUrl);
	
	                //Post HTTP response message
	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    //Read result string
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


### Python 範例 ###

若要連線到 Azure ML Web 服務，請使用 **urllib2** 程式庫傳遞 ScoreData。ScoreData 包含 FeatureVector，這是代表 ScoreData 的數值特徵 N 維向量。您要使用 API 金鑰驗證 Azure ML 服務。


**執行程式碼範例**

1. 發佈 Azure ML 範例集合中的 "Sample 1: Download dataset from UCI: Adult 2 class dataset" 實驗。
2. 使用來自 Web 服務的金鑰指派 apikey。請了解如何取得 Azure ML 授權金鑰。
3. 使用要求 URI 指派 serviceUri。請了解如何取得要求 URI。

		import urllib2
		# If you are using Python 3+, import urllib instead of urllib2
	
		import json 
	
		data =  {
	            "Id": "score00001",
	            "Instance": {
	                "FeatureVector": {
	                    "Col1": "0",
	                    "Col2": "0",
	                    "Col3": "0",
	                    "Col4": "0",
	                    "Col5": "0",
	                    "Col6": "0",
	                    "Col7": "0",
	                    "Col8": "0",
	                    "Col9": "0",
	                    "Col10": "0",
	                    "Col11": "0",
	                    "Col12": "0",
	                    "Col13": "0",
	                    "Col14": "0",
	                    "Col15": "0",
	                },
	                "GlobalParameters": { }
	            }
	        }
	
		body = str.encode(json.dumps(data))
	
		#Assign serviceUrl with the Request URI. See How to get a Request URI.
		uri = '{ServiceUri}'
	
		#Assign apiKey with the key from a web service.
		api_key = '{ApiKey}'
		headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	
		req = urllib2.Request(uri, body, headers) 
		response = urllib2.urlopen(req)
	
		#If you are using Python 3+, replace urllib2 with urllib.request in the above code:
		#req = urllib.request.Request(uri, body, headers) 
		#response = urllib.request.urlopen(req)
	
		result = response.read()
		print(result) 
 

<!---HONumber=July15_HO2-->