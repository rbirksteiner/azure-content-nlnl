<properties 
	pageTitle="發佈 Machine Learning Web 服務 | Microsoft Azure" 
	description="如何將訓練實驗轉換為評分實驗，準備它以進行發佈，然後當做 Azure Machine Learning Web 服務發佈。" 
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
	ms.date="05/27/2015" 
	ms.author="garye"/>

#發佈 Azure Machine Learning Web 服務

Azure Machine Learning 可讓您建置、測試以及部署預測分析解決方案。

從高階觀點而言，由下列三個步驟完成這個動作：

- **[建立訓練實驗]** - Azure Machine Learning Studio 是共同作業的視覺化開發環境，您使用所提供的訓練資料來訓練和測試預測分析模型。
- **[將其轉換為評分實驗]** - 一旦您的模型已使用現有資料訓練，並做好使用該模型為新資料評分的準備之後，您準備並簡化用於評分的實驗。
- **[將其發佈為 Web 服務]** - 只要按一下，您就可以將評分實驗當做 Azure Web 服務發佈。使用者可以將資料傳送到您的模型以及接收您的模型的預測。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

發佈 Web 服務之後，您可以：

- 透過 Web 服務 API **[存取]**它
- 透過 Azure 管理入口網站**[管理]**它，以及
- 當您的模型變更時**[更新]**它

[建立訓練實驗]: #create-a-training-experiment
[將其轉換為評分實驗]: #convert-the-training-experiment-to-a-scoring-experiment
[將其發佈為 Web 服務]: #publish-the-scoring-experiment-as-a-web-service
[存取]: #access-the-web-service
[管理]: #manage-the-web-service-in-the-azure-management-portal
[更新]: #update-the-web-service


##建立訓練實驗

若要訓練預測分析模型，您使用 Azure Machine Learning Studio 以建立訓練實驗，在其中包含各種模組以載入訓練資料、視需要準備資料、套用機器學習演算法，以及評估結果。您可以逐一查看實驗，並且嘗試不同的機器學習演算法以比較及評估結果。

建立和管理訓練實驗的處理，在其他地方有更詳盡的說明 - 請參閱這些文章以取得詳細資訊和範例：

- [在 Azure Machine Learning Studio 中建立簡單實驗](machine-learning-create-experiment.md)
- [使用 Azure Machine Learning 開發預測解決方案](machine-learning-walkthrough-develop-predictive-solution.md)
- [將訓練資料匯入 Azure Machine Learning Studio](machine-learning-import-data.md)
- [在 Azure Machine Learning Studio 中管理實驗逐一查看](machine-learning-manage-experiment-iterations.md)

##將訓練實驗轉換為評分實驗

訓練模型之後，您即準備就緒可以使用它來評分新資料。若要這麼做，請將訓練實驗轉換為評分實驗。轉換為評分實驗之後，您就準備好訓練模型，可以當做評分 Web 服務發行。Web 服務的使用者會將輸入資料傳送到您的模型，然後您的模型就會傳送回預測結果。因此，當您轉換為評分實驗時，您必須記住您預期其他人使用您模型的方式。

![轉換為評分實驗](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

如需如何進行此轉換的詳細資料，請參閱[將 Machine Learning 訓練實驗轉換成評分實驗](machine-learning-convert-training-experiment-to-scoring-experiment.md)


##將評分實驗當做 Web 服務發佈

既然已經為評分實驗做好充分的準備，您可以將它當做 Azure Web 服務發佈。使用者可以使用 Web 服務，將資料傳送到您的模型，模型就會傳回其預測。

若要發佈您的評分實驗，請按一下實驗畫布底端的 [**執行**]，然後按一下 [**發佈 WEB 服務**]。系統會設定 Web 服務，且會將您帶往 Web 服務儀表板。

![發佈 Web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

若要測試 Web 服務，請按一下 [**儀表板**] 索引標籤中的 [**測試**] 連結。對話方塊隨即顯示，要求您提供服務的輸入資料。這些是評分實驗預期的資料行。輸入一組資料，然後按一下 [確定]。Web 服務產生的結果會顯示在儀表板底部。

![測試 Web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

在 [**組態**] 索引標籤上，您可以變更服務的顯示名稱，並且給予說明。名稱和說明會顯示在 Azure 管理入口網站中，您在其中管理您的 Web 服務。您也可以啟用記錄以診斷當您點擊 Web 服務時看到的任何錯誤。如需詳細資訊，請參閱[為 Machine Learning Web 服務啟用記錄](machine-learning-web-services-logging.md)。

![設定 Web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

您也可以為您的輸入資料、輸出資料及 Web 服務參數提供說明，方法是為**輸入結構描述**、**輸出結構描述**及 **WEB 服務**參數底下的每個資料行輸入字串。這些說明會用於為 Web 服務提供的範例程式碼文件。

##存取 Web 服務

從 Machine Learning Studio 發佈您的 Web 服務之後，您可以傳送資料給服務以及以程式設計方式接收回應。

儀表板提供您存取 Web 服務所需的所有資訊。例如，提供 API 金鑰以允許服務的授權存取權，以及提供 API 說明頁面以協助您開始撰寫程式碼。

如需存取 Machine Learning Web 服務的詳細資訊，請參閱[如何使用已發佈的 Azure Machine Learning Web 服務](machine-learning-consume-web-services.md)。


##在 Azure 管理入口網站中管理 Web 服務

在 Azure 管理入口網站中，您可以管理您的 Web 服務，方法是按一下 [**機器學習**] 服務，開啟您的機器學習工作區，然後從 [**WEB 服務**] 索引標籤開啟 Web 服務。您可以從這個頁面監視 Web 服務、更新及刪除它。您也可以為 Web 服務新增預設端點 (在您發佈服務時建立) 之外的第二個端點。

如需詳細資訊，請參閱[管理 Azure Machine Learning 工作區](machine-learning-manage-workspace.md)。<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**. 
-->


##更新 Web 服務

您可以對您的 Web 服務進行變更，例如使用其他訓練資料更新模型，以及再次發佈、覆寫原始 Web 服務。

若要更新 Web 服務，請開啟您用來發佈原始 Web 服務的原始評分實驗，然後按一下 [**另存新檔**] 以製作可編輯的複本。進行變更，然後按一下 [**發佈 WEB 服務**]。因為您以前已發佈過這個實驗，所以 Machine Learning Studio 會提示您以詢問是否要覆寫現有服務。如果您按一下 [**是**]，則現有 Web 服務會停止，新的評分實驗會在其位置發佈。

> [AZURE.NOTE]如果您在原始 Web 服務中進行組態變更，例如輸入新的顯示名稱或說明，則您必須再次輸入這些值。

更新 Web 服務的一個選擇是以程式設計方式重新定型模型。如需詳細資訊，請參閱[以程式設計方式重新定型機器學習服務模型](machine-learning-retrain-models-programmatically.md)。

<!---HONumber=July15_HO2-->