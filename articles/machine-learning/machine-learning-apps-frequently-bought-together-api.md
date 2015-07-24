<properties 
	pageTitle="機器學習範例應用程式：人氣組合 | Microsoft Azure" 
	description="機器學習 Web 服務會執行線上購物車分析，從使用者提供的歷史交易產生人氣組合項目的產品建議。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="CoromT" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/12/2015" 
	ms.author="luisca"/>

# 機器學習範例應用程式：人氣組合


## 重要事項：此服務已被取代

此服務提供的人氣組合功能，現在已整合至我們更廣泛的[建議 API](http://gallery.azureml.net/MachineLearningAPI/3574432384684cac9cc766e57729ea4c)。我們鼓勵您使用建議項目來代替此服務。

##概觀

機器學習中的[人氣組合 Web 服務](https://datamarket.azure.com/dataset/amla/mba)會對線上購物車進行分析，從過去的交易產生人氣組合項目的產品建議。人氣組合建議可幫助購物者在購買特定項目時，找出目錄中最相關的產品。以醒目的方式顯示這些建議已證明可以有效提升線上零售商的銷售業績。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]
  
##開始使用 

訂閱人氣組合 Web 服務之後，您就可以使用 [Market Basket Analysis Service 範例 Web 應用程式](https://marketbasket.cloudapp.net/)，輕鬆將您的資料上傳至模型，並且探索人氣產品組合。若要使用應用程式或 API，首先需要 API 金鑰，您可以從 [Azure 資料市場帳戶頁面](https://datamarket.azure.com/account)取得。

##使用 Web 服務 

這項服務包含的 API 可建立人氣組合模型、上傳歷史交易，以及擷取指定產品的最佳排行人氣產品組合。您可以在 GitHub 上的 [Azure-MachineLearning-DataScience](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether) 存放庫中找到說明如何使用這些 API 的範例。

 

<!---HONumber=July15_HO2-->