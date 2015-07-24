<properties 
	pageTitle="如何選擇參數來最佳化 Azure Machine Learning 中的演算法 | Microsoft Azure" 
	description="說明如何為 Azure Machine Learning 中的演算法選擇最佳的參數設定。" 
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
	ms.date="04/21/2015" 
	ms.author="bradsev;garye" />


# 如何選擇參數來最佳化 Azure Machine Learning 中的演算法

本主題說明如何為 Azure Machine Learning 中的演算法選擇正確的超參數 (hyperparameter) 集。大部分的機器學習演算法取決於各種不同的參數。當我們訓練一個模型時，必須提供這些參數的值。訓練過的模型的效率是依據我們選擇的模型參數而定。找出最佳參數集的過程稱為模型選擇。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

有許多方法可以進行模型選擇。在機器學習中，交叉驗證是其中一種最廣泛使用的模型選擇方法，在 Azure Machine Learning 中是預設的模型選擇機制。由於 Azure Machine Learning 支援 R 和 Python 兩者，所以使用者可以使用 R 或 Python 實作自己的模型選擇機制。

找出最佳參數集的過程有四個步驟。

1.	**定義參數空間**：對於演算法，我們首先決定我們想要考慮的確切參數值。 
2.	**定義交叉驗證設定**：對於資料集，我們需要決定如何選擇交叉驗證折數。 
3.	**定義計量**：然後我們決定要使用哪一種計量來判斷最佳的參數集，例如正確度、均方根誤差、精確度、召回率或 f 分數。 
4.	**訓練、評估和比較**：對於每個唯一的參數值組合，執行交叉驗證並根據使用者所定義的錯誤計量，就可以選擇最佳的執行模型。

下列實驗顯示在 Azure Machine Learning 中如何達到這個目標。

![image1](./media/machine-learning-algorithm-parameters-optimize/fig1.png)
 
## 定義參數空間
在進行模型初始化步驟時，可以定義參數集。所有機器學習演算法的參數窗格都有兩種訓練模式：[**單一參數**] 和 [**參數範圍**]。我們必須選擇 [**參數範圍**] 模式 (圖 1)。這個選項可讓您為每個參數輸入多個值：在文字方塊中輸入以逗號分隔的值。或者，使用 [**使用範圍產生器**] 可以定義網格的最大點數與最小點數，以及要產生的總點數。參數值預設會以線性刻度產生。但如果核取了 [**對數刻度**] 方塊，則值會以對數刻度產生 (也就是相鄰兩點的比率而不是其差異為常數)。對於整數參數，可以使用連字號 "-" 定義範圍，例如："1-10" 表示介於 1 到 10 (兩者皆含) 之間的所有整數會構成參數集。也支援使用混合的模式，例如"1-10, 20, 50"。在此情況下，除了 1-10 的整數，20 和 50 也會加入參數集。
  
![image2](./media/machine-learning-algorithm-parameters-optimize/fig2.png) ![image3](./media/machine-learning-algorithm-parameters-optimize/fig3.png)

## 交叉驗證折疊定義
使用[分割和樣本][partition-and-sample]模組可對資料隨機指派折數。在下圖中，我們會看到該模組的範例組態，其中我們定義 5 個折數，並且對樣本實例隨機指派折疊數目。

![image4](./media/machine-learning-algorithm-parameters-optimize/fig4.png)


## 定義計量
[掃描參數][sweep-parameters]模組支援對指定的演算法和資料集根據經驗選擇一組最佳的參數集。此模組的屬性窗格除了有關訓練模型的其他資訊之外，還包括用來判斷最佳參數集的計量。分類和迴歸演算法分別有兩個不同的下拉式清單。如果考慮使用分類演算法，則會忽略迴歸計量，反之亦然。在此特定範例中，我們選擇 [**正確度**] 做為計量。
 
![image5](./media/machine-learning-algorithm-parameters-optimize/fig5.png)

## 訓練、評估和比較  
相同的[掃描參數][sweep-parameters]模組會訓練對應到參數集的所有模型、評估各種計量，然後根據使用者選擇的計量輸出訓練得最好的模型。此模組有兩個必要的輸入項目

* 未訓練過的學習者 
* 資料集 

以及選擇性的資料集輸入。我們將具有折疊資訊的資料集連接到必要的資料集輸入。如果資料集未被指派任何折疊資訊，則預設會自動執行 10 折交叉驗證。如果尚未進行折疊指派，且在選擇性資料集區域提供了驗證資料集，則會使用選擇的訓練-測試模式和第一個資料集針對每一個參數組合訓練模型，然後會針對驗證資料集進行評估。模組的左側輸出區域將不同的計量顯示為參數值的函數。右側的輸出區域提供訓練過的模型，根據使用者選擇的計量 (在此案例中為正確度)對應到最佳的執行模型。

![image6](./media/machine-learning-algorithm-parameters-optimize/fig6a.png) ![image7](./media/machine-learning-algorithm-parameters-optimize/fig6b.png)
 
將右側輸出區域具體呈現之後，我們可以看到所選擇的確切參數。此模型在儲存為訓練過的模型之後，可用來對測試集計分，或者用在可運作的 Web 服務。


<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[sweep-parameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
 

<!---HONumber=July15_HO2-->