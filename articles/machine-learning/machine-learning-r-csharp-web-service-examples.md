<properties 
	pageTitle="以 R 程式碼在 Azure ML 上建置並發行至 Marketplace 的範例 Web 服務 |Azure" 
	description="以 R 程式碼在 Azure ML 上建置並發行至 Marketplace 的範例 Web 服務" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="jaymathe"/>


#以 R 程式碼在 Azure ML 上建置並發行至 Marketplace 的範例 Web 服務




透過 Azure Machine Learning Studio，使用者可以撰寫 R 程式碼，而且只要按幾下按鈕，就可以將其發行為 Web 服務，以便其他個人和裝置在世界各地取用。從產生可提供統計功能的簡單計算器，以至建立自訂文字採礦情感分析預測工具，全新與有經驗的 R 使用者均可輕鬆使用，而 Azure ML 的使用者可以實際運用 R 程式碼。雖然這些 Web 服務可由使用者取用 (可能透過行動應用程式或網站)，但這些 Web 服務的目的也要做為 Azure ML 如何將 R 指令碼實際運用於分析以及根據 R 程式碼用於建立 Web 服務的範例。

在此頁面中，您可以找到一組使用 Azure ML 建立並發行至 Microsoft Azure Marketplace 的範例 Web 服務。每項 Web 服務都已附加完整文件、內嵌可供測試服務的樣本資料集，並解釋使用者如何才能自行建立類似的服務。 

![Workflow][1]

請考慮下列情況：

####案例 1：泛型模型 
使用者會使用可套用至新使用者資料的泛型模型，例如時間序列資料的基本預測或具備進階分析的自建 R 方法。此使用者會將此模型發行為一項 Web 服務，以便他人取用其資料。

* [二元分類器](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-binary-classifier/)
* [叢集模型](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-cluster-model/)
* [多變量線性迴歸](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-multivariate-linear-regression/)
* [預測 - 指數平滑法](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-forecasting-exponential-smoothing/)
* [ETS + STL 預測](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-retail-demand-forecasting/)
* [預測 - 自我迴歸整合移動平均模型 (ARIMA)](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-arima/)
* [存活分析](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-survival-analysis/)

####案例 2：定型模型 - 特定資料 
使用者具有可透過 R 程式碼提供實用預測的資料，例如透過 k-means 演算法叢集的大量性格問卷樣本可預測使用者的性格類型，或是健康調查資料可用來透過存活分析 R 套件預測個人罹患肺癌的風險。使用者會透過可預測新使用者結果的 Web 服務來發行資料。

####案例 3：定型模型 - 泛型資料 
使用者具有能夠建立 Web 服務的泛型資料 (例如文字主體)，而該 Web 服務可廣泛套用於不同類型的使用個案和案例。

* [語彙型情感分析](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-lexicon-based-sentiment-analysis/)

####案例 4：進階計算器 
使用者提供進階運算或模擬，而不需要任何定型模型或讓模型配適使用者資料。

* [比例差異測試](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-difference-in-two-proportions/)
* [常態分配套件](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-normal-distribution/)
* [二項分配套件](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-binomial-distribution/)

##常見問題集
如需使用 Web 服務或發佈至 Marketplace 的常見問題集，請參閱[此處](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq)。

[1]: ./media/machine-learning-r-csharp-web-service-examples/base1.png



<!--HONumber=46--> 
 