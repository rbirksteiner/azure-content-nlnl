<properties
	pageTitle="Machine Learning 應用程式：情感分析 |Microsoft Azure"
	description="「文字分析 API」是一個搭配 Azure Machine Learning 建置的文字分析套件。此 API 可用來分析工作的非結構化文字，例如情感分析和關鍵片語擷取。"
	services="machine-learning"
	documentationCenter=""
	authors="LuisCabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="luisca"/>


# Machine Learning 應用程式：分析情感的文字分析服務#
##概觀
文字分析 API 是一個搭配 Azure Machine Learning 建置的文字分析 [Web 服務](https://datamarket.azure.com/dataset/amla/text-analytics)套件。此 API 可用來分析工作的非結構化文字，例如情感分析和關鍵片語擷取。使用此 API 不需要任何訓練資料，只要將文字資料帶入即可。我們目前只支援英文。此 API 底層使用的是進階自然語言處理技術。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]
 
## 情感分析##
API 會傳回一個 0 到 1 之間的分數。接近 1 的分數表示正面的情感，而接近 0 的分數則表示負面的情感。情感分數使用分類技術產生。輸入分類器的特徵包括 n-grams、從 part-of-speech 標記產生的特徵以及字詞內嵌。
 
## 關鍵片語擷取##
API 會傳回輸入文字中代表說話重點的字串清單。我們採用的技術來自 Microsoft Office 複雜的自然語言處理工具組。

## API 定義##

###GetSentiment###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**範例要求**

在下面的 GET 呼叫中，我們要求片語 *Hello World* 的情感

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

標頭：

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

您可以在[這裡](https://datamarket.azure.com/account/keys)取得您的帳戶金鑰。

**範例回應**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

###GetKeyPhrases###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**範例要求**

在下面的 GET 呼叫中，我們要求 這段文字中關鍵片語的情感*這是很棒的旅館，裝潢獨樹一幟，工作人員服務態度甚佳*

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

標頭：

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

您可以在[這裡](https://datamarket.azure.com/account/keys)取得您的帳戶金鑰。


**範例回應**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 
---

###GetSentimentBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**範例要求**

在下面的 POST 呼叫中，我們正在要求要求的主體中下列片語的情感：Hello World, Hello Foo World, Hello My World

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

主體：

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	{"Id":"2","Text":"hello foo world"},
    	{"Id":"3","Text":"hello my world"},
	]}


標頭：

	Authorization: Basic <creds>
	Accept: application/json

	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  


您可以在[這裡](https://datamarket.azure.com/account/keys)取得您的帳戶金鑰。

**範例回應**

在以下回應中，您會取得與文字識別碼相關聯的分數的清單：

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", "SentimentBatch":
		[{"Score":0.9549767,"Id":"1"},
		 {"Score":0.7767222,"Id":"2"},
		 {"Score":0.8988889,"Id":"3"}
		],  
		"Errors":[] 
	}


---

###GetKeyPhrasesBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**範例要求**

在下面的 POST 呼叫中，我們要求下列文字中關鍵片語的情感清單：

*這是很棒的旅館，裝潢獨樹一幟，工作人員服務態度甚佳*
 
*這是令人讚嘆的 Build 研討會，有非常有趣的討論*

*交通很恐怖，我花了三個小時才到達機場*



	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

主體：

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

標頭：

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

您可以在[這裡](https://datamarket.azure.com/account/keys)取得您的帳戶金鑰。


**範例回應**

在以下回應中，您會取得與文字識別碼相關聯的片語的清單：

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[ ]
	}

---

**與批次處理相關的注意事項**

在系統中輸入的識別碼是由系統傳回的識別碼。Web 服務不會檢查識別碼是否為唯一。呼叫端必須負責驗證唯一性。
 

<!---HONumber=July15_HO1-->