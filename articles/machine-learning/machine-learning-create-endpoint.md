<properties 
	pageTitle="在機器學習中建立 Web 服務端點 | Microsoft Azure" 
	description="在 Azure Machine Learning 中建立 Web 服務端點" 
	services="machine-learning" 
	documentationCenter="" 
	authors="hiteshmadan" 
	manager="padou" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="04/21/2015"
	ms.author="himad"/>


# 建立端點

Azure Machine Learning 可讓您為已發行的 Web 服務建立多個端點。每個端點都會獨立於該 Web 服務的其他端點，受到個別定址、節流以及管理。每個端點都有唯一的 URL 和授權金鑰。

這可讓 Azure Machine Learning 使用者建立 Web 服務，然後稍後將這些 Web 服務銷售給他們的客戶。每個端點都可以透過自己的定型模型來個別自訂，同時仍然連結到建立此 Web 服務的實驗。此外，實驗的任何更新都可以選擇性地套用到整個端點，而不會覆寫自訂項目。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

## 端點建立步驟
- 開啟 [http://manage.windowsazure.com](http://manage.windowsazure.com)，按一下左欄的 [機器學習]。按一下具有您感興趣的 Web 服務的工作區。![瀏覽到工作區](./media/machine-learning-create-endpoint/figure-1.png)


- 按一下 [Web 服務] 索引標籤。![瀏覽到 Web 服務](./media/machine-learning-create-endpoint/figure-2.png)


- 按一下您感興趣的 Web 服務，以查看可用端點的清單。![瀏覽到端點](./media/machine-learning-create-endpoint/figure-3.png)


- 按一下底部的 [新增端點] 按鈕。填寫名稱和描述，確定此 Web 服務中沒有名稱相同的其他端點。除非您有特殊需求，否則請保留節流層級的預設值。若要深入了解節流，請參閱[調整 API 端點](machine-learning-scaling-endpoints.md)。![建立端點](./media/machine-learning-create-endpoint/figure-4.png)


建立端點之後，您就可以透過同步 API、批次 API 以及 Excel 工作表來取用該端點。如需關於使用機器學習 Web 服務的詳細資訊，請參閱[如何使用已發行的 Azure Machine Learning Web 服務](machine-learning-consume-web-services.md)。
 

<!---HONumber=July15_HO2-->