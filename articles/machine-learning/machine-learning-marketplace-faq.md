<properties 
	pageTitle="常見問題集：在 Azure Marketplace 中發佈和使用 Machine Learning 應用程式 | Microsoft Azure" 
	description="常見問題集" 
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
	ms.date="05/05/2015" 
	ms.author="luisca"/>

#在 Azure Marketplace 中發佈和使用 Machine Learning 應用程式：常見問題集

##從 Marketplace 取用的相關問題


**1.為什麼我輸入 Web 服務的輸入之後，得到下列錯誤訊息：**

**要求導致後端逾時或後端錯誤。小組正在調查這個問題。很抱歉造成您的不便。(500)**

您的輸入參數可能不符合特定 Web 服務所需的格式。請參閱對應的文件連結，來尋找輸入參數的正確格式以及此 Web 服務的限制。


[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

**2.如果我將 [探索這個資料集] 頁面上顯示之 Web 服務的 API 連結，複製並貼到另一個瀏覽器視窗中，我應該使用哪些認證來存取結果，又該如何查看這些結果？**

您應該使用您的 Marketplace 帳戶作為使用者名稱，並使用主要帳戶金鑰作為密碼。您可以在 Web 服務描述下的 [探索這個資料集] 頁面上，找到主要帳戶金鑰 (按一下 [顯示] 按鈕)。視您所使用的瀏覽器而定，結果可能會顯示在瀏覽器中或可供下載。

**3.為什麼我輸入 Web 服務的輸入之後，在 [探索這個資料集] 頁面上得到下列錯誤訊息：**

**處理您的要求時發生未預期的錯誤。請再試一次。**

在 Marketplace 的 [探索這個資料集] 頁面上取用您的 Web 服務時，該服務的一個或多個輸入參數可能超過長度限制。您可以透過 HTTP POST 方法，使用較長的輸入長度呼叫服務。例如，請參閱[使用 R 在 Machine Learning 上建置並發佈至 Marketplace 的範例解決方案](machine-learning-r-csharp-web-service-examples.md)。

**4.為什麼 Azure 管理入口網站之 [存放區] 的 [API 總管] 索引標籤中未顯示任何項目？**

這是 Azure Marketplace 管理入口網站的已知問題。小組正著手解決這個問題。


##透過 Azure Machine Learning 在 Marketplace 上發佈的相關問題

**1.為什麼我的 Web 服務不會重新整理標誌或影像的異動？**

標誌和影像是在發佈入口網站中快取，新的標誌或影像最多可能需要 10 天才能在入口網站上更新。

**2.為什麼在 Marketplace 上，我的 Web 服務的 [詳細資料] 索引標籤顯示錯誤訊息？**

在連接到 Azure Machine Learning 以取得服務詳細資料時，已知有一個 Marketplace 問題。小組正著手解決這個問題。

**3.為什麼在取用 Marketplace 中的 Web 服務時，無法使用 Azure Machine Learning Web 服務中的 R 範例程式碼？**

直接連接到 Azure Machine Learning Web 服務，相較於透過 Marketplace 連接到這些 Web 服務，有不同的驗證系統。Marketplace 中的服務是 OData 服務，而且可以使用 GET 或 POST 方法進行呼叫。

**4.為何我的 Web 服務產品的支援連結無法正確更新我的部分產品？**

支援連結是依照發佈者而非依照產品的全域連結。

**5.如何使用 Marketplace 中的批次輸入模式來發佈 Web 服務？**

Marketplace Web 服務目前不支援批次輸入模式。

**6.如果我有關於成為資料發行者的問題，或在發佈過程中發生問題，我應該與誰連絡以取得協助？**

如需詳細資訊，請連絡 Azure Marketplace 小組 (<datamarketbd@microsoft.com>)。





 

<!---HONumber=July15_HO2-->