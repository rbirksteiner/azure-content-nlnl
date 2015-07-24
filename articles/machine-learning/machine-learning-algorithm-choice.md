<properties 
	title="" 
	pageTitle="如何選擇機器學習演算法 | Microsoft Azure" 
	description="如何在叢集、分類或迴歸實驗中選擇適用於經過指導和未經指導之學習的 Azure Machine Learning 演算法。" 
	keywords="machine learning algorithms,types of machine learning algorithms,types of machine learning,clustering,clustering algorithm"	
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
	ms.date="06/01/2015" 
	ms.author="bradsev;garye" />


# 如何選擇適用於叢集、分類或迴歸的 Azure Machine Learning 演算法

本主題說明一些機器學習方法的基本層面。特別是，您將了解如何選擇適當的機器學習演算法，來分析指定類型的資料、解決所造成的問題、完成指定的工作，或提供進行決策的準則。

> [AZURE.TIP][Microsoft Azure Machine Learning 演算法小密技](machine-learning-algorithm-cheat-sheet.md)是本文隨附的簡便參考。

使用機器學習進行分析時，我們通常會面臨兩個問題：

* 我們需要對可用的資料進行何種分析來達成我們的目的？ 
* 進行這項分析最適當的演算法或模型為何？

以下將討論三種類型的機器學習分析，並比較它們的使用案例：

* **叢集**
* **分類** 
* **迴歸** 

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]


<a name="anchor-1"></a>
## 從資料中學習的機器學習演算法

「機器學習」是一門研究演算法類別的專業領域，這些演算法專門設計來從資料中學習，不會利用特定、預先決定的模型來測試資料。其概念是要藉由檢查資料集中的模式，以更為歸納的方式取得知識，而不是使用「假設-演繹」方法，嘗試先猜測適合整個資料集的模型，然後再對資料集進行實證測試。

機器學習的類型 (也被視為從資料中學習) 有兩種：「經過指導的學習」和「未經指導的學習」。

<a name="anchor-2"></a>
## 經過指導的學習  

經過指導的學習需要妥善定義目標變數，並提供足夠數目的變數值。

經過指導的學習是要輸入的訓練實例已知是正確的輸出結果 (或目標變數) 時所進行的機器學習類型。訓練機器學習演算法的目標在於尋找可將輸入對應到已知輸出值的模型 (也就是規則或函數)。這類似有一位指導者能告訴演算法代理程式，它是否正確將輸入對應到輸出。一旦學習程序完成且有可用的模型時，它就可以套用到新的輸入資料來預測預期的輸出，這時和訓練資料集不同，目標值事先是未知的。

模型類型是由目標變數的本質來決定。

![經過指導的學習圖表：來自加上標示之資料的模型，並使用它來從新資料預測結果。](./media/machine-learning-algorithm-choice/supervised-learning-using-known-data-to-model-solution.png)

有兩大類分析採用經過指導的學習：「分類」和「迴歸」。經過指導的學習在分類問題中相當常見，因為目標往往是要讓電腦學習我們所建立的分類系統。回應通常只是幾個已知的值 (標籤)，例如 'true' 或 'false'，或者「高」、「中」或「低」。分類演算法適用名目值，而非序數的回應值。數字辨識是分類學習的常見範例。更廣泛來說，分類學習適用於很有用且分類很容易決定的任何問題。

在經過指導的學習中，要調查的變項可以分成兩個群組：解釋變項 (也稱為預測項) 和依變項 (也稱為應變項)。分析的目標是定義解釋變數和依變數之間的關係，就像它在「迴歸分析」中所做的一樣。依變數的值已知必須是資料集中夠大的一部分。在迴歸中，回應或輸出變項要採用連續值，例如某台汽車每加侖汽油所跑的英哩數，一個人的年齡等等。

經過指導的學習也是訓練類神經網路和決策樹最常見的技術：

> 這兩種技術都高度依賴預先決定的分類所提供的資訊。在類神經網路的情況中，分類可用來判斷網路的錯誤，然後調整網路以使其最小化，而在決策樹中，分類可用來判斷哪些屬性會提供可用來解決分類拼圖的大部分資訊。...這兩個範例都會使一些具有預先決定之分類形式的「指導」蓬勃發展。

>  使用隱藏的 Markov 模型和貝氏網路的語音辨識也會依賴指導的一些項目來調整參數，如往常般，將指定輸入的錯誤降至最低。[[機器學習第二部分：經過指導和未經指導的學習](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm)，[AI Horizon](http://www.aihorizon.com/)]


<a name="anchor-3"></a>
##未經指導的學習

在機器學習中，未經指導的學習問題是判斷未標記的資料中是否有模式或隱藏的結構。模型事先並不會獲得要訓練的資料集的「正確結果」。由於已為學員提供未標記的範例，因此沒有任何意見反應 (錯誤或獎勵) 可用來評估可能的解決方案。目標是要讓電腦學習，儘管我們未明確告訴它要如何完成該工作，一樣也能完成。在未經指導的學習的情況中，可以相同方式對待所有變項。解釋變數和依變數之間沒有任何區別。不過，仍有某些目標需要達成，而這可能是一般的目標，例如，減少資料，或像是尋找叢集的更特定目標。

在 Azure Machine Learning 中，我們可以透過**叢集**、**分類**及**迴歸**來執行未經指導和經過指導的學習。

   ![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help2.png)

<a name="anchor-4"></a>
##群集
群集是未經指導的學習的一個範例。在這種學習中，目標是要在訓練資料中尋找相似之處，並將資料集分割成幾個可由這些相似特性區分的子集。我們預期，透過這些資料導向的程序所發現的最重要群集，可以經常 (但未必永遠) 與我們直覺的分類一致。

雖然叢集演算法不會為這些叢集指派適當的名稱，但它還是可以產生叢集，然後將這些叢集分類至最適合的叢集，使用它們來預測預期會在新範例中出現的相似性。這種資料導向的方法即使沒有足夠的資料可供使用，也能夠運作得很好。舉例來說，社交資訊篩選演算法 (例如 Amazon.com 用來建議書籍的演算法)，就是根據尋找相似人員的群組，然後將新的使用者指派給這些群組以提供建議。

Azure Machine Learning 中可用的叢集演算法是 [K-Means 叢集][k-means-clustering]。

![K-Means 叢集演算法實驗：螢幕擷取畫面](./media/machine-learning-algorithm-choice/k-means-clustering-algorithm-menu.png)

K-means 是最簡單的未經指導學習演算法之一，可用來解決已知的叢集問題。KMeans 演算法為資料設定叢集的方法是，嘗試將樣本分成 N 個相等變異的群組，以獲得最小的「慣量」或「叢集內平方總和」準則。此演算法必須指定群集數目。K-means 適用大量樣本數的情況，在許多不同的領域中也有非常廣泛的應用。

[K-Means 叢集][k-means-clustering]演算法模組會傳回未訓練的 K-means 叢集模型，可傳遞至[訓練叢集模型][train-clustering-model]模組進行訓練。

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/k4.png)

本圖顯示使用 K-Means 叢集時要設定的選項。K-means 方法會對一組 D 維度的資料點尋找指定的群集數目。從「一組最初的 K 質量中心」開始，此方法會使用 Lloyd 的演算法，反覆調整質量中心的位置。演算法會在質量中心穩定時或完成「特定次數的反覆運作」時終止。模組會使用最終的質量中心 (定義在 N 個資料點中找到的 K 個群集) 初始化一個 K x D 的陣列。演算法也會使用一個長度為 N 的向量，將每個資料點指派到 K 個群集中的一個群集。如果要尋找特定的群集數 (K)，模組會依序將前 K 個資料點指派到 K 個群集。


此模組也接受使用或產生初始點來定義其初始群集組態。「度量」定義用來測量資料點與質量中心之間距離的方法。每個資料點都會指派到其質量中心最接近資料點的群集。根據預設，此方法會使用「歐幾里得度量」。您可以指定「餘弦度量」做為此方法的替代度量。請注意，K-means 方法只會針對資料集尋找本機最佳的群集組態。指定不同的初始組態，方法可能會找到不同的 (可能更好) 的組態。

<a name="anchor-5"></a>
##分類 
在分類分析中，我們將樣本分成幾個類別，然後使用一組先前加上標記且訓練過的資料。此技術是用來預測資料實例的群組成員資格。在 Azure Machine Learning 中，可使用下列分類演算法。

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help3.png)

「雙類別演算法」用於二進位應變數 (是或否、0 或 1，true 或 false 等等)，而「多類別演算法」可用於將實例分類成兩個以上類別的任何名目應變數

### 選取分類演算法的指導方針
這一長串的演算法產生了一系列問題：

* 您如何得知這當中的哪一個分類器對於某個特定資料集是最好的？ 
* 是否有任何情況可以「自然」選擇某一種分類器？ 
* 進行選擇的原則有哪些？

建議的方法之一是測試幾種不同的分類器，以及在每個演算法內測試不同的參數集，然後使用交叉驗證選取最佳的演算法。

> [AZURE.TIP] [Azure Machine Learning Studio](https://studio.azureml.net/) 可讓您針對相同的資料逐一嘗試多種演算法，並比較結果。以下範例來自 [Azure Machine Learning 資源庫](http://gallery.azureml.net/)：[比較多類別分類器：字母辨識](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92)。

以下是一些通用的指導方針，提供您思考的起點。選擇要使用的演算法時，請考慮下列問題：[由[選擇機器學習分類器](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/)，Edwin Chen 所建議的大綱]

**有多少訓練資料？** 如果訓練集很小，而且您要訓練經過指導的分類器，則機器學習理論建議您使用高偏差/低變異 (例如單純貝氏 (Naive Bayes)) 的分類器。這些演算法優於低偏差/高變異的分類器，例如 kNN，因為後者傾向過度擬合。但是如果您具有較大型訓練集，則低偏差/高變異的分類器更適合，因為它們的漸進錯誤較少；在這些情況下，高偏差分類器並未強大到足以提供精確的模型。有理論和實際結果指出，單純貝氏 (Naive Bayes) 在這類情況下也運作得很好。但請注意，比起擁有更好的演算法，擁有更好的資料和良好的功能，通常能為您提供更大的優勢。此外，如果您的資料集非常龐大，則分類效能受到您所使用之演算法的影響可能很有限，因此，在此情況下，最好是根據其擴充性、速度或方便使用等項目來選擇演算法。

**您需要以遞增方式或在批次模式中進行訓練嗎？** 如果您有大量資料，或者需要經常更新資料，就可能想要使用容易更新的貝氏演算法。類神經網路和 SVM 都需要在批次模式中運用訓練資料。

**您的資料專門屬於某些分類，或者專門屬於數值資料，或者混合這兩種類型嗎？** 貝氏對於分類/二項式資料效果最佳。決策樹無法預測數值。

**您或您的對象需要了解分類器的運作方式嗎？** 更輕鬆地說明貝氏或決策樹。查看或說明類神經網路和 SVM 如何分類資料將更為困難。

**產生分類所需的速度？** 若樹狀結構相當複雜，決策樹可能會很慢。換句話說，SVM 分類的速度比較快，因為它們只需要判斷您的資料位於某條「界線」的哪一側。

**呈現問題或所需的複雜程度為何？** 類神經網路和 SVM 可以處理複雜的非線性分類。

### 分類演算法的優點和缺點
這其中每一個分類演算法都有一些優缺點：

<a name="anchor-5a"></a> **羅吉斯迴歸的優點和缺點：**「羅吉斯迴歸分析的基礎是以有結果的可能性除以沒有結果之可能性的比率來計算結果的可能性」。[[用來評估 10 到 12 歲兒童之間與氣喘普遍程度有關因素的羅吉斯迴歸與線性判別分析：兩種統計方法的差異與相似度](http://www.hindawi.com/journals/ijpedi/2009/952042/)，George Antonogeorgos 等作者 (International Journal of Pediatrics，2009) 文章 ID 952042]
 
羅吉斯模型是參數化模型，因此，優點是它可讓人了解每個預測變數對於應變數的影響。


有了自然的機率詮釋 (不同於決策樹或 SVM)，您可以輕鬆地更新您的模型來納入新的資料。有很多方式可將您的模型正規化，與單純貝氏不同，您不需要太擔心您正在進行關聯的特徵。如果您想要執行下列動作，羅吉斯迴歸就非常有用：

* 用來調整分類臨界值的機率架構
* 快速納入其他訓練資料  

邏輯迴歸能夠比高維度資料的決策樹運作得更好。例如，在文字分類中，您有超過十萬份文件，有五十萬個相異的字詞 (特徵)。此時最好能夠有一個簡單的規則，像是線性超平面的學習，因為決策樹有太多的自由度，容易過度擬合。您可以對文字資料選擇特徵來使用決策樹，但是若挑選太過精簡的特徵，文字分類將會遺失許多寶貴的資訊。將學習模型與高維度的資料搭配使用時，非常容易讓變異型的錯誤變多。在此情況下，最好是使用有較高偏差的簡單模型。

羅吉斯迴歸的一個缺點是，當某個預測變數幾乎能夠解釋應變數時會變得很不穩定，因為這個變數的係數會變得很大。

<a name="anchor-5b"></a> **決策樹的優點和缺點：**[決策樹](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf)很容易理解和說明。

> [決策樹] 可輕鬆處理特徵互動，而且它們不是參數化模型，所以您不需擔心極端值，或者資料是否以線性方式分隔 (例如，決策樹可輕鬆處理某個特徵 x 的低端有類別 A，特徵 x 的中間範圍有類別 B，以及高端又有 A 的狀況)。有一個缺點是，它們不支援線上學習，因此當出現新的範例時，您必須重新建立您的樹狀結構。另一個缺點是，決策樹很容易過度擬合，但這正是如隨機森林 (或推進式決策樹) 可產生效用之處。此外，隨機森林通常能夠處理分類中的眾多問題 (我相信通常略勝於 SVM)，它們是快速且可調整的，而且您不需擔心要像使用 SVM 時微調一堆參數。[[選擇機器學習分類器](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/)，Edwin Chen]

決策樹會產生如規則的輸出，以及如「支持度」、「信心」和「增益」等度量。


<a name="anchor-5c"></a> **SVM 的優點和缺點：**支援向量機器 (SVM) 在高維度空間中非常有效。即使維度數目大於樣本數目，SVM 仍然有效。不過，如果特徵數目遠大於樣本數目，方法執行的結果會比較差。SVM 使用記憶體的方式也很有效率，因為它在決策函數 (又稱為支援向量) 中使用訓練點的子集。它非常靈活：決策函數可以指定不同的核心函數 (一般和自訂)。核心函數可用來將低維度的訓練樣本轉換到較高的維度，這對於線性可分性問題很好用。

不過，SVM 並不直接提供機率估計值。這些估計值要使用昂貴的五折交叉驗證計算。

>「如果有」高度正確性、對於過度擬合有可信賴的理論保證，以及適當的核心，即使資料在基底特徵空間中沒有以線性方式分隔，SVM 也可以運作得很好。在極高維度空間的文字分類問題中，[SVM] 特別受到歡迎。[[選擇機器學習分類器](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/)，Edwin Chen]

與森林不同的地方在於，SVM 一開始就是雙類別分類器，不過最近已能搭配多個類別運作。我們可以使用類似「一對多」(one-vs-rest) 的訓練來產生不太理想的多類別分類器。因為 SVM 只能處理雙類別輸出 (亦即 2 種的類別輸出變項)，使用 N 類別，它就會學習 N 個 SVM (SVM 1 學習 "Output==1" 與 "Output != 1"、SVM 2 學習 "Output==2" 與"Output != 2"、SVM N 學習 "Output==N" 與"Output != N")。然後，若要預測新輸入的輸出，它只要使用每個 SVM 進行預測，接著找出哪一個 SVM 可以將最遠的預測放入正數區域。[[支援向量機器](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt)，Andrew W. Moore (Carnegie Mellon University 2001)]

<a name="anchor-5d"></a> **單純貝氏 (Naive Bayes) 的優點和缺點：**[單純貝氏 (NB)](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) 分類器是分類問題的常見選項。它們假設特徵都是獨立無關的 - 這是讓技術變得單純的方法。

> 如果 NB 的條件式獨立性假設能真正滿足，單純貝氏分類器將會比區別模型 (例如羅吉斯迴歸) 還要容易收斂，如此一來，您所需的訓練資料會更少。因此，即使 NB 假設無法滿足，NB 分類器在實務上依然很有威力。...其主要缺點是它無法了解特徵之間的互動 (例如，它無法得知您雖然分別喜愛演員 A 和演員 B 所主演的電影，但卻不喜歡他們兩人同時主演的電影)。[[選擇機器學習分類器](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/)，Edwin Chen]


<a name="anchor-5e"></a> **一對多：** 「一對多」(One-vs-all) 是一種策略，將多類別分類的問題縮減為一組二進位分類的問題。這個策略包括針對每個類別訓練單一分類器，並使用該類別的樣本做為正數樣本，而所有其他樣本則做為負數樣本。這個策略要求基底分類器對其決策產生真實值的信賴分數，而不是只有類別標籤。在對單一樣本預測多個類別時，只有抽象的類別標籤可能會導致模稜兩可。[[多類別分類](http://en.wikipedia.org/wiki/Multiclass_classification) (Wikipedia 2006)]


<a name="anchor-6"></a>
##迴歸
 
在迴歸分析中，我們根據過去的推理預測新值。依變項的新值會根據一或多個測量屬性的值來計算。Azure Machine Learning 中提供下列各種迴歸演算法：

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help4.png)

根據使用案例和既有的資料，我們會選擇其中一個演算法。下面我們將說明一些迴歸演算法及其主要使用案例。

<a name="anchor-6b"></a> **[貝氏線性迴歸][bayesian-linear-regression]**貝氏線性迴歸是一種在貝氏推理脈絡中執行統計分析的線性迴歸方法。當迴歸模型具有常態分佈的錯誤，而且特別是可以事先假設分佈時，模型參數的事後機率分佈可以獲得明確的結果。[[線性迴歸](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org))]

<a name="anchor-6f"></a> **[推進式決策樹迴歸][boosted-decision-tree-regression]** 推進式決策樹迴歸會計算預測變數和應變數之間的關係。迴歸樹狀結構很類似分類樹狀結構。終端節點是預測的函數 (模型) 值。預測的值僅限於終端節點中的值。使用決策樹的優點包括：

* 很容易解釋決策規則 
* 不使用參數，因此很容易使用某個數值或類別範圍的資料層，而且不需要單一形式的訓練資料
* 不會受到訓練資料中極端值的影響 
* 一旦開發規則之後，分類很快就能完成 

不過，使用決策樹有一些缺點：

* 它們傾向過度擬合訓練資料，因而在套用到完整的資料集時，會產生不良的結果
* 不可能預測訓練資料中超過應變數的最小限制和最大限制。


<a name="anchor-6g"></a> **[決策森林迴歸][decision-forest-regression]** 決策森林可應用在分類 (類別變數) 或迴歸 (連續變數)。迴歸森林可用於提供獨立的輸入時依變數的非線性迴歸，而且輸入和輸出兩者可以是多維度的。比起分類森林，迴歸森林較不常使用。主要的差異在於決策森林中與輸入資料相關聯的輸出標籤 (以及訓練標籤) 必須是連續的。這表示必須適當地採用目標函數。迴歸森林同時擁有許多分類森林的優點，例如效率和彈性。

<a name="anchor-6a"></a> **[線性迴歸][linear-regression]** 線性迴歸經常用來對純量依變數 Y 與一或多個解釋變數 X 之間的關係建構模型。它可以 (而且通常) 用來進行預測、預報或簡化。它可以用來對 Y 和 X 值的觀察資料集擬合出一個預測的模型。線性迴歸假設 Y 的基礎結構是 X 變項的線性組合。如果指定另一個 X 值卻沒有伴隨的 y 值，則擬合的線性迴歸模型就可用來預測該 Y 值。線性迴歸模型通常是使用最小平方法來進行擬合，但也有其他選項可用來測量哪一種擬合為最佳結果。[[線性迴歸](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org))]

<a name="anchor-6c"></a> **[類神經網路迴歸][neural-network-regression]** 類神經網路對於非參數型迴歸是很有用的統計工具。非參數型迴歸解決問題的方式是，嘗試擬合出變數 Y 的模型 (針對一組可能的解釋變數 X1; : : : ;Xp，其中 X 和 Y 之間的關係可能比簡單的線性關係更複雜。[[使用類神經網路的非參數型迴歸架構](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf)，Herbert K. H.Lee (ISDS，Duke University)]

<a name="anchor-6d"></a> **[序數迴歸][ordinal-regression]** 序數迴歸是一種用來模型化或預測序數依變數的迴歸分析類型。對於序數依變項，您可以將值排序，但是類別之間的實際距離則未知。不同值之間，只有相對順序才重要。由於標籤或目標值有自然的順序或等級，任何數值資料行都可以做為序數目標。數字的自然順序可用來將數字排序。例如，將疾病分等級時，可以從最不嚴重到最嚴重。問卷受訪者選擇答案時可以從「強烈同意」到「強烈不同意」。學生成績可以從 A 到 F。儘管基本上，序數迴歸是一種延伸的邏輯迴歸，並以「成比例的機率」模型為根據。


<a name="anchor-6e"></a> **[Poisson 迴歸][poisson-regression]** Poisson 迴歸通常用於建構計數資料的模型。Poisson 迴歸假設應變項都具有 Poisson 分佈。Poisson 分佈的資料在本質上為整數值 (分散且為正數)，因此可用於計數資料。提供訓練資料集，Poisson 迴歸會嘗試尋找最佳的值，讓輸入參數的對數可能值最大化。參數的可能值是從使用這些參數的分佈取樣訓練資料的機率。例如，Poisson 迴歸會適用於：

* 建構與飛機航班數相關聯的冷天日數模型 
* 估計與事件或促銷相關的通話數目 
* 建立列聯表

## 參考

如需 Machine Learning Studio 中可用機器學習演算法類型的完整清單，請參閱 [Machine Learning Studio：演算法和模組說明](https://msdn.microsoft.com/library/azure/dn905974.aspx)中的[初始化模型](https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/)。

您可以在下列參考資料中找到關於所有機器學習演算法類型的更詳細資訊，當我們在撰寫本文時已參考了其中許多資訊。

* [選擇機器學習分類器](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/)，Edwin Chen。

* [適用於分類、迴歸、密度估計、流形學習及半指導式學習的決策樹](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf)，A. Criminisi1, J. Shotton2 和 E. Konukoglu (Microsoft Research，2011) 技術報告 TR-2011-114。

* [使用類神經網路的非參數型迴歸架構](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf)，Herbert K. H.Lee (ISDS，Duke University)。

* 計算統計學手冊：概念與方法，作者：James E. Gentle、Wolfgang Karl Härdle、Yuichi Mori (Springer-Verlag Berlin Heidelberg New York，2004)。

* [用來評估 10 到 12 歲兒童之間與氣喘普遍程度有關因素的羅吉斯迴歸與線性判別分析：兩種統計方法的差異與相似度](http://www.hindawi.com/journals/ijpedi/2009/952042/)，作者：George Antonogeorgos、Demosthenes B. Panagiotakos、Kostas N. Priftis 及 Anastasia Tzonou (International Journal of Pediatrics，2009) 文章 ID 952042。

* [單純貝氏最適化](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) (University of New Brunswick 2004) Harry Zhang。

* [支援向量機器](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt)，Andrew W.Moore (Carnegie Mellon University 2001)。

* [機器學習第二部分：經過指導和未經指導的學習](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm)，[AI Horizon](http://www.aihorizon.com/)。

* [決策樹上的羅吉斯迴歸優點有哪些？](http://www.quora.com/What-are-the-advantages-of-logistic-regression-over-decision-trees) ([Quora](http://www.quora.com/))。

* [經過指導的學習和未經指導的學習間有哪些差異？](http://stackoverflow.com/questions/1832076/what-is-the-difference-between-supervised-learning-and-unsupervised-learning) ([Stackoverflow](http://stackoverflow.com/))。

* [何時選擇哪些機器學習分類器？](http://stackoverflow.com/questions/2595176/when-to-choose-which-machine-learning-classifier) ([Stackoverflow](http://stackoverflow.com/))。

* [Wikipedia](http://en.wikipedia.org)：
	* [貝氏線性迴歸](http://en.wikipedia.org/wiki/Bayesian_linear_regression)
	* [線性迴歸](http://en.wikipedia.org/wiki/Linear_regression)
	* [多類別分類](http://en.wikipedia.org/wiki/Multiclass_classification)
	* [未經指導的學習](http://en.wikipedia.org/wiki/Unsupervised_learning)

另請參閱：

* [Microsoft Azure Machine Learning 演算法小祕技](machine-learning-algorithm-cheat-sheet.md) (Microsoft)。

* [選擇正確的估算器](http://scikit-learn.org/stable/tutorial/machine_learning_map/) ([scikit-learn](http://scikit-learn.org/stable/index.html))。


<!-- Module References -->
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[decision-forest-regression]: https://msdn.microsoft.com/library/azure/562988b2-e740-4e3a-8131-358391bad755/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[neural-network-regression]: https://msdn.microsoft.com/library/azure/d7ee222c-669f-4200-a576-a761a9c1a928/
[ordinal-regression]: https://msdn.microsoft.com/library/azure/ffb557f8-dc7f-44bd-8fd0-b25666dd23f1/
[poisson-regression]: https://msdn.microsoft.com/library/azure/80e21b9d-3827-40d8-b733-b53148becbc2/

 

<!---HONumber=58_postMigration-->