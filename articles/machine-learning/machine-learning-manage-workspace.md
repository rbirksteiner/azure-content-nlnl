<properties 
	pageTitle="管理 Machine Learning 工作區 | Microsoft Azure" 
	description="管理 Azure 機器學習工作區的存取權，並部署和管理 ML API Web 服務" 
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
	ms.date="04/29/2015" 
	ms.author="garye"/>


# 管理 Azure Machine Learning 工作區 
您可以使用 Azure 管理入口網站來管理您的機器學習服務工作區，以便：

- 監視工作區的使用方式
- 設定工作區以允許或拒絕存取
- 管理工作區中建立的 Web 服務
- 刪除工作區

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

此外，儀表板索引標籤會提供工作區使用方式的概觀和工作區資訊的快速概覽。

> [AZURE.TIP]在 Azure Machine Learning Studio 中，您可以在 [**Web 服務**] 索引標籤上加入、更新或刪除 Machine Learning Web 服務。

若要管理工作區：

1.	登入 Microsoft Azure 帳戶 - 使用與 Azure 訂用帳戶相關聯的帳戶。
2.	在 [Azure 管理入口網站](https://manage.windowsazure.com/)中，在 Microsoft Azure 服務面板中，按一下 [**機器學習服務**]。
3.	按一下您想要管理的工作區。

工作區頁面有三個索引標籤：

- **儀表板** - 可讓您檢視工作區使用方式和資訊
- **設定** - 可讓您管理對工作區的存取
- **Web 服務** - 可讓您管理從此工作區發佈的 Web 服務

  
## 監視工作區的使用方式

按一下 [**儀表板**] 索引標籤。

從儀表板，您可以檢視工作區的整體使用量，並取得工作區資訊的快速概覽。

- **計算**圖表會顯示工作區正在使用的計算資源。您可以變更檢視來顯示相對或絕對值，並且可以變更圖表中顯示的時間範圍。
- **使用量概觀**顯示工作區正在使用的 Azure 儲存體。附註：Azure Machine Learning 公用預覽版期間無法使用儲存體監視。
- **快速概覽**提供工作區資訊和實用連結的摘要。

> [AZURE.NOTE][**在 Studio 中開啟**] 連結會使用目前登入的 Microsoft 帳戶來開啟 Machine Learning Studio。您用來登入 Azure 入口網站以建立工作區的 Microsoft 帳戶，不會自動具備開啟該工作區的權限。若要開啟工作區，您必須使用定義為工作區擁有者的 Microsoft 帳戶登入，或者您需要收到來自擁有者的邀請，才能加入工作區。


## 授與或暫停使用者的存取權 ##

按一下 [設定] 索引標籤。

您可以從設定索引標籤
 
- 按一下 [拒絕] 來擱置對機器學習服務工作區的存取。使用者將不再能在 Machine Learning Studio 中開啟工作區。若要還原存取，請按一下 [允許]。
- 藉由指定不同的 Microsoft 帳戶來變更工作區擁有者。 

若要管理誰可以存取 Machine Learning Studio 中的工作區，請按一下 [**在 Studio 中開啟**] (請參閱上述有關**在 Studio 中開啟**的附註)。這會在 Machine Learning Studio 中開啟工作區。從這裡按一下 [**設定**] 索引標籤，然後按一下 [**使用者**]。您可以按一下 [**邀請使用者**]，讓使用者存取工作區，或選取使用者，並按一下 [**移除**]。


## 管理此工作區中的 Web 服務

按一下 [**Web 服務**] 索引標籤。

這會顯示從此工作區發佈的 Web 服務的清單。若要管理 Web 服務，請按一下清單中的名稱以開啟 Web 服務頁面。

Web 服務可能會有一個或多個定義的端點。

- 除了「預設」端點以外，您可以定義其他端點。若要加入端點，請按一下頁面底部的 [**加入端點**]。

- 若要刪除端點 (您無法刪除「預設」端點)，請按一下端點列上的任何位置 (名稱除外)，然後在頁面的底部按一下 [**刪除端點**]。這會從 Web 服務移除端點。
 
    > [AZURE.NOTE]如果刪除端點時應用程式使用 Web 服務端點，應用程式會在下一次嘗試存取服務時收到錯誤。

按一下 Web 服務端點的名稱以開啟它。使用量圖表會顯示 Web 服務端點正在使用的運算和預測資源。您可以變更檢視來顯示相對或絕對值，並且可以變更圖表中顯示的時間範圍。

這個頁面也可提供讓您能夠使用 Web 服務 REST API 存取端點所需的資訊。如需詳細資訊，請參閱[如何使用 Azure Machine Learning Web 服務][consume]。

您也可以從這個頁面將 Web 服務發佈至 Azure 資料 Marketplace。如需詳細資訊，請參閱[將 Azure Machine Learning Web 服務發佈至 Azure Marketplace][marketplace]。

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md

 

<!---HONumber=July15_HO1-->