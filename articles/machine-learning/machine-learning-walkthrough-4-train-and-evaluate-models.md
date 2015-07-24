<properties 
	pageTitle="步驟 4：定型和評估預測分析模型 | Microsoft Azure" 
	description="開發預測解決方案逐步解說的步驟 4：在 Azure Machine Learning Studio 中定型、計分和評估多個模型。" 
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


# 逐步解說步驟 4：定型和評估預測分析模型

這是[使用 Azure ML 開發預測解決方案](machine-learning-walkthrough-develop-predictive-solution.md)逐步解說的第四個步驟


1.	[建立機器學習服務工作區](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[上傳現有資料](machine-learning-walkthrough-2-upload-data.md)
3.	[建立新實驗](machine-learning-walkthrough-3-create-new-experiment.md)
4.	**訓練及評估模型**
5.	[發佈 Web 服務](machine-learning-walkthrough-5-publish-web-service.md)
6.	[存取 Web 服務](machine-learning-walkthrough-6-access-web-service.md)

----------

在這項實驗中，我們嘗試將不同的演算法運用到我們的預測模型。我們將建立兩種不同的模型，然後比較其計分結果，以決定要用於最終實驗的演算法。

有許多模型可供我們選擇。若要查看可用的模型，請在模組選用區展開 [機器學習] 節點，然後展開 [初始化模型]，再選擇其下方的節點。基於本實驗的目的，我們將選取「支援向量機器 (SVM)」和「二元促進式決策樹」模組。我們將使用適當的模組來初始化學習演算法，並使用 [訓練模型][][train-model] 模組來訓練模型。

##訓練模型
首先，讓我們設定推進式決策樹模型：

1.	在模組選擇區中找到[二元促進式決策樹][two-class-boosted-decision-tree]模組，並將其拖曳到畫布上。
2.	找出 [訓練模型][][train-model] 模組，將它拖曳到畫布上，然後將推進式決策樹模組的輸出連接到 [訓練模型][][train-model] 模組的右側輸入埠 ([Untrained model])。
3.	將左邊 [Execute R Script][][execute-r-script] 模組的輸出，連接到 [訓練模型][][train-model] 模組的右側輸入埠 ([資料集])。

	> [AZURE.TIP]在這項實驗中，我們不需要[執行 R 指令碼][execute-r-script]模組的其中兩個輸入和一個輸出，因此讓它們保持未連接。對某些模組來說，這也很平常。


4.	選取 [訓練模型][][train-model] 模組。在 [屬性] 窗格中，按一下 [啟動資料行選取器]，在第一個下拉式清單中選取 [包含]，在第二個下拉式清單中選取 [資料行索引]，然後在文字欄位中輸入 "21" (您也可以選取 [資料行名稱]，再輸入「信用風險」)。這樣可將資料行 21 (信用風險值) 當做模型要預測的資料行。


實驗的這部分目前看起來如下：

![Training a model][1]
 
接下來，我們設定 SVM 模型。

強化的決策樹適合處理任何類型的特性。不過，因為 SVM 模組會產生線性分類器，而它所產生的模型在所有數值特性都有相同的尺度時，將具有最佳檢定誤差。為了將所有數值特徵轉換成相同的尺度，我們使用[標準化資料][normalize-data]模組，並搭配 Tanh 轉換，將特徵轉換成 [0,1] 範圍。請注意，字串特性會由 SVM 模組轉換為類別特性，再轉換為二進位 0/1 特性，因此我們無須手動轉換字串特性。此外，我們不想要轉換 [信用風險] 資料行 (資料行 21) - 它是數值，但這是我們定型模型來預測的值，因此必須維持原狀。

1.	在模組選用區找出 [Two-Class Support Vector Machine][][two-class-support-vector-machine] 模組，將它推曳到畫布上。
2.	以滑鼠右鍵按一下[定型模型][train-model]模組，選取 [複製]，然後以滑鼠右鍵按一下畫布並選取 [貼上]。請注意，[訓練模型][][train-model] 模組複本的資料行選擇與原始模組相同。
3.	將 SVM 模組的輸出連接到 [訓練模型][][train-model] 模組的左側輸入埠 ([Untrained model])。
4.	找到[標準化資料][normalize-data]模組，並將其拖曳到畫布上。
5.	將此轉換模組的輸入連接到左側 [Execute R Script][][execute-r-script] 模組的輸出。
6.	將轉換模組的左側輸出埠 ([Transformed Dataset]) 連接到 [訓練模型][][train-model] 模組的右側輸入埠 ([資料集])。
7.	在轉換模組的 [屬性] 窗格中，選取 [Tanh] 做為 [轉換方法] 參數。
8.	按一下 [啟動資料行選取器]，在第一個下拉式清單中選取 [包含]，在第二個下拉式清單中選取 [資料行類型]，然後在第三個下拉式清單中選取 [數值]。這樣會指定轉換所有數值資料行 (且僅限數值)。
9.	按一下加號 (+)，建立一列新的下拉式清單。在第一個下拉式清單中選取 [排除]，在第二個下拉式清單中選取 [資料行索引]，然後在文字欄位中輸入 "21"。這樣會忽略資料行 21 ([信用風險] 資料行)。
10.	按一下 [確定]。  


[標準化資料][normalize-data]模組現在已設定為在所有數值資料行上執行 Tanh 轉換 ([信用風險] 資料行除外)。

實驗的這部分目前看起來如下：

![Training the second model][2]

##計分及評估模型
我們將使用由 [分割] 模組所分開的計分資料，給我們訓練的模型評分。然後，我們就可以比較兩個模型的結果，了解何者產生的結果較佳。

1.	找出 [評分模型][][score-model] 模組並拖曳到畫布上。
2.	將此模組的左側輸入埠連接到推進式決策樹模型 (亦即，連接到 [訓練模型][][train-model] 模組的輸出埠，而後者又連接到 [Two-Class Boosted Decision Tree][][two-class-boosted-decision-tree] 模組)。
3.	將 [評分模型][][score-model] 模組的右側輸入埠連接到右邊 [Execute R Script][][execute-r-script] 模組的輸出。請注意，模組的輸出可以送往多個地方。
4.	複製並貼上 [評分模型][][score-model] 模組來建立第二個複本，或將新的模組拖曳到畫布上。
5.	將此模組的左側輸入埠連接到 SVM 模型 (亦即，連接到 [訓練模型][][train-model] 模組的輸出埠，而後者又連接到 [Two-Class Support Vector Machine][][two-class-support-vector-machine] 模組)。
6.	在 SVM 模型中，我們必須像是轉換定型資料一樣，對測試資料進行相同的轉換。因此，請複製並貼上[標準化資料][normalize-data]模組來建立第二個複本，再將其連接到右邊[執行 R 指令碼][execute-r-script]模組的輸出。
7.	將[計分模型][score-model]模組的右側輸入連接埠連接到[標準化資料][normalize-data]模組的輸出。  

為了評估兩個計分結果，我們使用 [評估模型][][evaluate-model] 模組。

1.	找出 [評估模型][][evaluate-model] 模組並拖曳到畫布上。
2.	將左側輸入埠連接到與推進式決策樹模型相關聯的 [評分模型][][score-model] 模組的輸出埠。
3.	將右側輸入埠連接到另一個 [評分模型][][score-model] 模組。  

實驗目前看起來如下：

![Evaluating both models][3]
 
按一下畫布下方的 [執行] 按鈕來執行實驗。可能需要數分鐘的時間。您會看到旋轉指示器，表示正在執行，而模組完成時會出現綠色核取記號。

當所有模組都出現核取記號時，表示實驗執行完成。若要檢查結果，請以滑鼠右鍵按一下 [評估模型][][evaluate-model] 模組的輸出埠，然後選取 [視覺化]。

[評估模型][][evaluate-model] 模組會產生一對曲線和度量，讓您比較兩個評分模型的結果。您可以將結果顯示成「受測者操作特徵 (ROC)」曲線、「正確性/召回」曲線或「升力」曲線。其他顯示的資料還包括混淆矩陣、曲線下面積 (AUC) 的累計值，以及其他度量。您可以將滑動軸左右移動來變更臨界值，觀察這樣如何影響度量組。

按一下 [已計分的資料集] 或 [要比較的已計分資料集]，以醒目提示相關聯的曲線，並在下方顯示相關聯的度量。在曲線的圖例中，"Scored dataset" 對應至 [評估模型][][evaluate-model] 模組的左側輸入埠 - 在我們的案例中，這是推進式決策樹模型。「要比較的已計分資料集」會對應至右側輸入連接埠 (在本例中為 SVM 模型)。按一下其中一個標籤時，該模型的曲線會反白顯示，並在下方顯示對應的度量。

![ROC curves for models][4]
 
您可以檢查這些值，以判斷哪個模型最可能提供您想要的結果。您可以返回並變更不同模型中的值，以反覆執行實驗。

> [AZURE.TIP]每次執行實驗，[執行歷程記錄] 中就會保留該筆逐一查看的記錄。您可以檢視這些反覆運算，按一下畫布下方的 [檢視執行歷程記錄] 即可回到其中任何一個。您也可以按一下 [屬性] 窗格中的 [Prior Run]，回到您目前開啟的反覆運算之前的那一個反覆運算。如需詳細資訊，請參閱[在 Azure Machine Learning Studio 中管理實驗逐一查看](machine-learning-manage-experiment-iterations.md)。

您也可以按一下畫布下方的 [另存新檔]，將實驗的任何反覆運算製作一個複本。這樣會製作實驗的副本，並建立新的執行歷程記錄來追蹤此版本的反覆運算。新的複本與原始版本會一起出現在 [實驗] 清單中。想要啟動實驗反覆運算的新分支時，這會很有用。

您可以在實驗畫布上的任何模組中加入註解，以輔助追蹤您對模組參數所做的變更。請按兩下模組，或按一下滑鼠右鍵來選取 [編輯註解]。這些註解會與實驗反覆運算一起儲存，可協助您給工作加上註解。


----------

**下一步：[發佈 Web 服務](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
 

<!---HONumber=July15_HO2-->