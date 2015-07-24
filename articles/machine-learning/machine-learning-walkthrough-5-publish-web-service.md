<properties 
	pageTitle="步驟 5：發佈 Machine Learning Web 服務 | Microsoft Azure" 
	description="開發預測解決方案逐步解說的步驟 5：在 Machine Learning Studio 中將計分實驗發佈為 Web 服務。" 
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


# 逐步解說步驟 5：發佈 Azure Machine Learning Web 服務

這是[使用 Azure ML 開發預測解決方案](machine-learning-walkthrough-develop-predictive-solution.md)逐步解說的第五個步驟


1.	[建立機器學習服務工作區](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[上傳現有資料](machine-learning-walkthrough-2-upload-data.md)
3.	[建立新實驗](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[訓練及評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	**發佈 Web 服務**
6.	[存取 Web 服務](machine-learning-walkthrough-6-access-web-service.md)

----------

為了讓其他人可以使用此預測模型，我們將它發行到 Azure 上當作 Web 服務。

截至目前為止，我們一直在試驗如何定型模型。但發行後的服務不會再進行訓練 - 而是會給使用者的輸入評分。因此，我們將做些準備，再將這項實驗發佈為運作正常的 Web 服務，以供使用者存取。使用者將可以傳送一組信用貸款申請資料給服務，而服務會傳回信用風險的預測。

為了這樣做，我們需要：

- 將我們所建立的「定型實驗」轉換成「計分實驗」
- 將評分實驗當做 Web 服務發行

但首先，我們需要更精簡這項實驗。我們的實驗目前有兩個不同的模型，但現在需要選取一個要發佈的模型。假設我們已判斷出推進式樹狀模型是較適合使用的模型。因此，首要之務是移除[二元支援向量機器][two-class-support-vector-machine]模組，以及用於定型該模組的其他模組。您可能需要按一下實驗畫布底部的 [另存新檔]，先建立一份實驗複本。

我們必須刪除下列模組：

1.	[二元支援向量機器][two-class-support-vector-machine]
2.	連接到這個模組的[定型模型][train-model]和[計分模型][score-model]模組
3.	[標準化資料][normalize-data] (兩者)
4.	[評估模型][evaluate-model]

現在我們已經準備好要發佈這個模型。

##將訓練實驗轉換為評分實驗

轉換成計分實驗的作業包含三個步驟：

1. 儲存已定型的模型，並使用這個模型取代我們的定型模型
2. 精簡實驗，移除只有定型才需要的模組
3. 定義 Web 服務輸入和輸出節點的位置

幸好上述三個步驟只要按一下實驗畫布底部的 [建立計分實驗]，即可完成。

當您按一下 [建立計分實驗] 時，會發生幾件事：

- 我們所定型的模型會另存為實驗畫布左側模組選擇區中的**定型模型**模組 (您可以在選擇區的 [定型模型] 下找到這個模組)。
- 用於定型的模組會遭到移除。具體而言：
  - [二元促進式決策樹][two-class-boosted-decision-tree]
  - [定型模型][train-model] 
  - [分割][split]
  - 用於測試資料的第二個[執行 R 指令碼][execute-r-script]模組
- 儲存的定型模型會加入實驗。
- 加入 **Web 服務輸入**和 **Web 服務輸出**模組。

> [AZURE.NOTE]實驗已分為兩個部分儲存：原始定型實驗和新的計分實驗。您可以使用實驗畫布頂端的索引標籤，存取其中一項實驗。

我們還必須對實驗執行額外的步驟。當 Machine Learning Studio 移除[分割][split]模組時，會移除其中一個[執行 R 指令碼][execute-r-script]模組，但留下另一個[執行 R 指令碼][execute-r-script]模組。由於該模組只用於定型和測試 (提供樣本資料的加權函數)，因此我們現在可以將其移除，並將[中繼資料編輯器][metadata-editor]連接到[計分模型][score-model]。

實驗現在看起來如下：

![Scoring the trained model][4]


您可能驚訝我們為什麼要在計分實驗中留下「UCI 德國信用卡資料」資料集。服務使用的是使用者的資料，而不是原始資料集，為什麼放任它們繼續連接？

服務的確不需要原始信用卡資料。但卻需要該資料的結構描述，例如，有多少個資料行及哪些資料行是數值等資訊。需要此結構描述資訊才能解譯使用者的資料。我們保持連接這些元件，以便服務執行時，評分模型才會有資料集結構描述。不會使用資料，只是使用結構描述。

最後一次執行實驗 (按一下 [執行])。如果要驗證模型仍然有效，請以滑鼠右鍵按一下 [評分模型][][score-model] 模組，並選取 [視覺化]。您會看到原始資料出現，也會看到信用風險值 ("Scored Labels") 和評分機率值 ("Scored Probabilities").

##發佈 Web 服務

若要發行從實驗中導出的 Web 服務，請按一下畫布下方的 [PUBLISH WEB SERVICE]，並於提示時按一下 [是]。Machine Learning Studio 會將實驗發佈為 Machine Learning 預備伺服器上的 Web 服務，並帶您前往服務儀表板。

> [AZURE.TIP]您可以在發佈 Web 服務之後進行更新。例如，如果想要變更模型，只要編輯定型實驗、調整模型參數，然後按一下 [更新計分實驗] 即可。重新發行實驗時，將會取代 Web 服務 (現在是使用已更新的模型)。

您可以按一下 [設定] 索引標籤來設定服務。您可以在這裡修改服務名稱 (預設會指定實驗名稱) 並輸入描述。您也可以為輸入和輸出資料行指定更好記的標籤。

##測試 Web 服務
在 [儀表板] 頁面上，按一下 [預設端點] 下的 [測試] 連結。即會顯示對話方塊，要求您提供服務的輸入資料。這些就是在原始的德國信用風險資料集中出現的資料行。

輸入一組資料，然後按一下 [確定]。

Web 服務產生的結果會顯示在儀表板底部。我們如何設定服務，評分模型就會產生什麼結果。


----------

**下一步：[存取 Web 服務](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
 

<!---HONumber=July15_HO2-->