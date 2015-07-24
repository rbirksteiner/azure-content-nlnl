<properties 
    pageTitle="什麼是 Azure 機器學習服務？| Microsoft Azure" 
    description="Azure 機器學習服務概觀" 
    services="machine-learning" 
    documentationCenter="" 
    authors="tedway" 
    manager="neerajkh" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/22/2015" 
    ms.author="tedway;olgali"/>


# 什麼是機器學習？
您的生活周遭四處都是機器學習。當您線上購物時，機器學習可根據您已經購買的東西協助推薦其他產品。當您的信用卡被刷過時，機器學習可協助銀行偵測詐騙，當有可疑的交易時便會通知您。機器學習是建立從現有資料學習，並對未來資料作預測分析之模型的程序

## 什麼是 Azure 機器學習服務？
Azure 機器學習服務是強大的雲端型預測分析服務，讓快速建立分析解決方案能夠實現。我們建立了 Azure 機器學習服務讓機器學習大眾化：我們消除了建置和部署機器學習技術過程中的繁重工作，讓每個人都能存取這項技術。它是完全受管理的服務，這表示您不需要購買任何硬體，或手動管理虛擬機器。

使用以瀏覽器為基礎的工具 [Machine Learning Studio](machine-learning-what-is-ml-studio.md)，您就可以快速建立和自動化機器學習工作流程。您可以拖放使用數百個現有的[機器學習庫](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7)，以快速開始您的預測分析解決方案，然後選擇性加入自訂的 [R](machine-learning-r-quickstart.md) 和 [Python](machine-learning-execute-python-scripts.md) 指令碼來延伸解它們。Studio 可在任何瀏覽器運作，讓您可以快速開發並反覆修改解決方案。 ![在雲端使用 Azure Machine Learning Studio 的預測分析實驗](./media/machine-learning-what-is-machine-learning/AzureMLStudio.png)

您輕鬆就能探索和建立 [Web 服務](machine-learning-publish-a-machine-learning-web-service.md)、[透過 API 訓練和重新訓練您的模型](machine-learning-retrain-models-programmatically.md)、根據各客戶[管理端點](machine-learning-create-endpoint.md)和[延伸 Web 服務](machine-learning-scaling-endpoints.md)，以及設定服務監視和偵錯的診斷。最新的功能包括：

- 建立可設定的自訂 R 模型、整合自己的訓練/預測 R 指令碼，以及使用龐大的程式庫 (如 numpy、scipy、pandas 或 scikit-learn) 生態系統新增 Python 指令碼。您現在可以使用[以計數學習][learning-with-counts]來透過數 TB 的資料進行訓練、使用 PCA 或單一類別 SVM 進行異常偵測，以及輕鬆地使用熟悉的 SQLite 修改、篩選和清理資料。 
- [機器學習社群展示庫](machine-learning-gallery-how-to-use-contribute-publish.md)可讓您探索和使用其他人製作的有趣實驗。您可以在[展示庫](http://gallery.azureml.net)中對實驗發問或張貼評論，或是發佈自己的實驗。您可以透過 LinkedIn 和 Twitter 等社交管道分享有趣實驗的連結。展示庫是使用者入門 Azure 機器學習服務，並從社群中其他人學習的好方法。 ![在 Azure 機器學習展示庫中試做預測分析範本或提供您自己的範本](./media/machine-learning-what-is-machine-learning/AzureMLGallery.png)
- 您可以透過 Azure 訂閱購買 [Marketplace app](https://datamarket.azure.com/browse?query=machine+learning)，並直接從 Azure Marketplace 使用已完成的「建議」、「文字分析」及「異常偵測」 Web 服務。 
- 「資料科學」之旅的逐步指南，從原始資料到實用的 Web 服務，讓前進雲端型資料科學的路程更輕鬆。我們在 Azure 機器學習服務加入可以使用  iPython Notebook 和 Python Tools for Visual Studio 等熱門工具的功能

## 立即開始使用
您可以使用[逐步指南](machine-learning-create-experiment.md)和[以範例建立](machine-learning-sample-experiments.md)來學習預測分析和機器學習的基礎知識。當您在 Studio 試做實驗使用機器學習服務時，不需要 Azure 訂閱或信用卡。


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/

<!--HONumber=54--> 