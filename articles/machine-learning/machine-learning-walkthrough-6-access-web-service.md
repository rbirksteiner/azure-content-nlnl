<properties 
	pageTitle="步驟 6：存取 Machine Learning Web 服務 | Microsoft Azure" 
	description="開發預測解決方案逐步解說步驟 6：存取使用中的 Azure Machine Learning Web 服務" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="garye"/>


# 逐步解說步驟 6：存取 Azure Machine Learning Web 服務

這是逐步解說[使用 Azure ML 開發預測方案](machine-learning-walkthrough-develop-predictive-solution.md)的最後一個步驟


1.	[建立機器學習服務工作區](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[上傳現有資料](machine-learning-walkthrough-2-upload-data.md)
3.	[建立新實驗](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[訓練及評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[發佈 Web 服務](machine-learning-walkthrough-5-publish-web-service.md)
6.	**存取 Web 服務**

----------

為了讓 Web 服務發揮效用，使用者必須能夠將資料傳送至服務並接收結果。此 Web 服務是一個可用兩種方式來傳送和傳回資料的 Azure Web 服務：

-	**要求/回應** - 使用者以 HTTP 通訊協定將一組信用資料傳送給服務，然後服務回應一組結果。
-	**批次執行** - 使用者將 Azure Blob (含有一或多列的信用資料) 的 URL 傳送給服務。服務會將結果儲存在另一個 Blob 中，再傳回該容器的 URL。  

在 Web 服務的 [儀表板] 索引標籤上，有兩個資訊連結可協助開發人員撰寫程式碼來存取此服務。在 [**要求/回應**] 資料列按一下 [**API 說明頁面**] 連結，將會開啟一個頁面，其中包含範例程式碼來使用服務的要求/回應通訊協定。同樣地，[批次執行] 資料列也提供範例程式碼來向服務提出批次要求。

API 說明頁面包含 R、C# 和 Python 程式設計語言的範例。

<!-- Add link to Derrick's articles on web services -->

<!---HONumber=July15_HO1-->