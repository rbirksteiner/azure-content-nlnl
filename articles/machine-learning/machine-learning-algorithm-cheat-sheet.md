<properties 
	pageTitle="機器學習演算法小祕技 | Microsoft Azure" 
	description="可列印的機器學習演算法小密技可協助您在 Azure Machine Learning Studio 中選擇適合您預測模型的演算法。"
	keywords="algorithm cheat sheet,cheat sheet,machine learning algorithm"	
	services="machine-learning"
	documentationCenter="" 
	authors="brohrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="brohrer;garye" />


# 適用於 Microsoft Azure Machine Learning Studio 的機器學習演算法小祕技

**Microsoft Azure Machine Learning 演算法小祕技**可協助您從 Microsoft Azure Machine Learning 演算法資源庫中選擇適合您預測性分析解決方案的機器學習演算法。小秘技會詢問有關您的資料性質與您正在處理的問題，然後建議您可嘗試的演算法。

[Azure Machine Learning Studio](https://studio.azureml.net/) 隨附大量適用於您預測性分析解決方案的機器學習演算法。這些演算法可以分為一般機器學習服務類別：***迴歸***、***分類***、***叢集***和***異常偵測***，而每個類別均設計用來處理不同類型的機器學習服務問題。



## 下載機器學習演算法小祕技

下載機器學習演算法小祕技，有助於了解如何選擇適合您的機器學習演算法。若要就近保留它，您可以將小祕技列印於 Tabloid 大小 (11 x 17 英吋) 的紙張上。

**在此下載小祕技：[Microsoft Azure Machine Learning 演算法小祕技](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v2.pdf)**

![機器學習演算法小祕技：了解如何選擇機器學習演算法。][cheat-sheet]

[cheat-sheet]: ./media/machine-learning-algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-microsoft-azure.png



<!-- This is now covered in the first footnote below
[Azure Machine Learning Studio](https://studio.azureml.net/) gives you the flexibility to experiment - 
try one algorithm, and if you're not satisfied with the results, try another.
Here's an example from the [Azure Machine Learning Gallery](http://gallery.azureml.net) of an experiment that tries several algorithms against the same data and compares the results:
[Compare Multi-class Classifiers: Letter recognition](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).
-->

## 更多演算法的詳細說明 

* 如需不同機器學習演算法類型及其使用方式的深入討論，請參閱[如何在 Azure Machine Learning 中選擇演算法](machine-learning-algorithm-choice.md)。
* 如需 Machine Learning Studio 中依類別排序的所有可用機器學習演算法的清單，請參閱＜Machine Learning Studio 演算法和模組說明＞中的[起始模型][initialize-model]。
* 如需 Machine Learning Studio 中演算法和模組的完整清單，請參閱＜Machine Learning Studio 演算法和模組說明＞中的 [Machine Learning Studio 模組的 A-Z 清單](https://msdn.microsoft.com/library/azure/dn906033.aspx)。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

## 機器學習演算法的注意事項與詞彙定義

* 這份演算法小祕技中提供的建議是近似經驗法則。您可以屈從有些建議，也可以公然違反有些建議。其主要目的是建議起點。別害怕您的資料上會出現數種演算法間勢均力敵的競爭。唯有了解每個演算法的原則，以及了解產生您的資料的系統。 

* 每個機器學習演算法都有自己的風格或*歸納偏向*。對於特定問題，適合的演算法可能有數個，但其中一個演算法可能會比其他演算法更適合。但不一定永遠都能事先知道哪一個演算法最適合。在這類情況下，小祕技中會一起列出數個演算法。適當策略就是嘗試一個演算法，而如果結果還不滿意，就嘗試其他演算法。以下是 [Azure 機器學習庫](http://gallery.azureml.net/)中的實驗範例，該實驗對相同的資料嘗試數種演算法並比較其結果：[比較多類別分類器：字母辨識](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92)。

* 機器學習有三個主要類別：**經過指導的學習**、**未經指導的學習**和**增強式學習**。

  * 在**經過指導的學習**中，每個資料點都會加上標籤或與感興趣的類別或值產生關聯。分類標籤的範例就是將影像指派為「貓」或「狗」。值標籤的範例則是與中古汽車相關聯的銷售價格。經過指導的學習的目標在於研究許多這類加上標籤的範例，然後能夠預測未來的資料點 - 例如，找出具有正確動物的新相片或指派精確的銷售價格給其他中古汽車。這是常見且實用的機器學習服務類型。除了 [K-Means 叢集][k-means-clustering]以外，Azure 機器學習中的所有模組都是經過指導的學習演算法。

  * 在**未經指導的學習**中，資料點沒有與其相關聯的標籤。然而，未經指導的學習演算法的目標在於以某種方式組織資料或描述其結構。這可能表示將資料劃分為叢集 (如 K-Means 所為)，或尋找各種查看複雜資料的方式，使其變得更簡單。

  * 在**增強式學習**中，演算法需選擇一個動作來回應每個資料點。這是在機器人領域中的常見方法，其中在某個時間點的感應器讀數集就是一個資料點，而演算法必須選擇機器人的下一個動作。它也很適合用於物聯網 (Internet of Things) 應用程式。此學習演算法也會在短時間內收到獎勵訊號，指出決策的好壞程度。演算法會據此修改其策略，以達到最高的獎勵。Azure ML 中目前沒有增強式學習演算法模組。

* **貝氏法**可進行統計上獨立的資料點假設。這表示在一個資料點中的非模型化變異不會與其他資料點相關，也就是，無法加以預測。例如，如果要記錄的資料是下一班地鐵到達前的分鐘數，則相隔一天取得的兩個測量值屬於統計獨立。不過，相隔一分鐘取得的兩個測量值則不屬於統計獨立 - 其中一個值可高度預測另一個值。

* **促進式決策樹迴歸**會利用特徵重疊或特徵間的互動。這表示，在任何給定的資料點中，一項特徵的值有點能夠預測另一項特徵的值。例如，在每日高/低溫資料中，得知當天的低溫可讓您合理猜測高溫。兩個特徵中包含的資訊有點冗餘。

* 使用原本就是多類別的分類器，或將一組雙類別的分類器合併成一個**整體** (Ensemble)，即可將資料分類成兩個以上的類別。在整體方法中，每個類別有個別的雙類別分類器 - 每個分類器會將資料分為兩類：「這一類」和「非這一類」。 然後這些分類器會表決資料點的正確指派。這是[一對多的多類別][one-vs-all-multiclass]背後的運作原則。

* 數種方法 (包括羅吉斯迴歸和貝氏機率分類點機器) 會採用**線性類別界限**，也就是類別之間的界限近乎直線 (在更一般情況下是超平面)。通常這是您嘗試劃分之後才會知道的資料特性，但通常可以透過事先視覺化來學習。如果類別界限看起來非常不規則，請堅守決策樹、決策叢林、支援向量機器或類神經網路。

* 為每個類別建立**虛擬變數**，並將它設定為 1 (適用此類別時) 或 0 (不適用此類別時)，即可搭配類別變數使用類神經網路。


<!-- This is how you can add a link to the image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-content-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
 

<!---HONumber=58_postMigration-->