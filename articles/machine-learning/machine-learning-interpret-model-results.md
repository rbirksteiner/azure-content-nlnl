<properties 
	pageTitle="如何在 Machine Learning 中解譯模型結果 | Microsoft Azure" 
	description="如何針對使用和視覺化評分模型輸出的演算法選擇最佳的參數設定。" 
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
	ms.author="bradsev" />


# 如何在 Azure Machine Learning 中解譯模型結果 
 
**了解和視覺化「評分模型」輸出**本主題說明如何視覺化和解譯 Azure Machine Learning Studio 中的預測結果。在您訓練模型並且完成模型最上層的預測 (「評分模型」) 之後，您必須了解和解譯您已取得的預測結果。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

Azure Machine Learning 中有四個主要的機器學習類型：

* 分類
* 叢集
* 迴歸
* 推薦系統

用來在這些模組上層進行預測的模組，稱為為其「評分」，指定一些測試資料，如下所示：

* [評分模型][score-model]模組，用於分類和迴歸， 
* [指派至叢集][assign-to-clusters]模組，用於叢集 
* [評分 Matchbox 推薦][score-matchbox-recommender]，用於推薦系統 
 
本文件說明如何針對每個模組解譯預測結果。如需這些類型模組的概觀，請參閱[如何選擇參數來最佳化 Azure Machine Learning 中的演算法](machine-learning-algorithm-parameters-optimize.md)。

本主題說明預測解譯，但是未說明模型評估。如需如何評估模型的詳細資訊，請參閱[如何評估 Azure Machine Learning 中的模型效能](machine-learning-evaluate-model-performance.md)。

如果您是 Azure Machine Learning 的新手，並且需要如何建立簡單實驗以開始使用的說明，請參閱[在 Azure Machine Learning Studio 中建立簡單實驗](machine-learning-create-experiment.md)。

##分類
分類問題方面有兩個子類別：

* 只有兩個分類的問題 (雙類別或二進位分類) 
* 兩個以上分類的問題 (多類別分類) 

Azure Machine Learning 有不同的模組可以處理各種類型的分類。但是解譯其預測結果的方式非常相似。我們將會首先談論雙類別分類問題，然後再說明多類別分類問題。

###雙類別分類
**範例實驗**

雙類別分類問題的範例是鳶尾花的分類：根據其特徵分類鳶尾花。請注意，Azure Machine Learning 中提供的鳶尾花資料集是熱門[鳶尾花資料集](http://en.wikipedia.org/wiki/Iris_flower_data_set)的子集，僅包含 2 個花卉物種 (類別 0 和 1) 的執行個體。每個花卉有四個特徵 (萼片長度、萼片寬度、花瓣長度及花瓣寬度)。

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/1.png)

圖 1 鳶尾花雙類別分類問題的實驗

已執行實驗以解決此問題，如「圖 1」所示。已訓練及評分雙類別促進式決策樹模型。現在我們可以從[評分模型][score-model]模組視覺化預測結果，方法是按一下[評分模型][score-model]模組的輸出連接埠，然後按一下功能表中的 [**視覺化**]。這樣會帶出評分結果，如「圖 2」所示。

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/1_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/2.png)

圖 2 視覺化雙類別分類中的評分模型結果

**結果解譯**

結果資料表中有六個資料行。左側四個資料行是四個特徵。右側兩個資料行，「評分標籤」和「評分機率」，是預測結果。「評分機率」資料行顯示花卉屬於正類別 (類別 1) 的機率。例如，資料行中第一個數字 0.028571 表示第一個花卉屬於類別 1 的機率有 0.028571。「評分標籤」資料行顯示每個花卉的預測類別。這是根據「評分機率」資料行。如果花卉的評分機率大於 0.5，則會預測為類別 1，否則會預測為類別 0。

**Web 服務發佈**

一旦了解預測結果並且完全評判之後，可以將實驗發佈為 Web 服務，讓我們可以在各種應用程式中部署以及呼叫，以取得任何新的鳶尾花的類別預測。如需如何將訓練實驗變更為評分實驗，並且發佈為 Web 服務的程序，請參閱[發佈 Azure Machine Learning Web 服務](machine-learning-walkthrough-5-publish-web-service.md)。遵循此程序可以提供您如「圖 3」所示的評分實驗。

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/3.png)

圖 3 鳶尾花雙類別分類問題的評分實驗

現在我們必須設定 Web 服務的輸入和輸出。很顯然的，輸入是[評分模型][score-model]的右側輸入連接埠，這是鳶尾花的特徵輸入。輸出的選擇取決於我們是對於預測類別 (評分標籤)、評分機率或兩者感到興趣。這裡假設我們對兩者都感到興趣。若要選取想要的輸出資料行，我們必須使用[專案資料行][project-columns]模組。按一下[專案資料行][project-columns]模組、按一下右窗格的 [**啟動資料行選取器**]，然後選取 [**評分標籤**] 和 [**評分機率**]。設定[專案資料行][project-columns]模組的輸出連接埠並且再次執行之後，應該就可以將評分實驗發佈為 Web 服務，方法是按一下底端的 [**發佈 WEB 服務**] 按鈕。最終實驗如「圖 4」所示。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/4.png)

圖 4 鳶尾花雙類別分類問題的最終評分實驗

執行 Web 服務並且輸入一些測試執行個體的特徵值之後，傳回結果會傳回兩個數字。第一個數字是評分標籤，第二個數字是評分機率。此花卉預測為類別 1，其機率為 0.9655。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/4_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/5.png)

圖 5 鳶尾花雙類別分類的 Web 服務結果

###多類別分類
**範例實驗**

在此實驗中，我們將會執行字母辨識工作，做為多類別分類的範例。我們嘗試預測特定字母 (類別) 的分類器，指定一些從手寫映像中擷取的手寫屬性值。在訓練資料中，從手寫字母映像中擷取 16 個特徵。26 個字母形成我們的 26 個類別。已設定實驗以訓練多類別分類模型進行字母辨識，並且預測測試資料集的相同特徵集，如「圖 6」所示。

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/5_1.png)
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/6.png)

圖 6 字母辨識多類別分類問題的實驗

從[評分模型][score-model]模組視覺化結果，方法是以滑鼠右鍵/左鍵按一下[評分模型][score-model]模組的輸出連接埠，然後按一下 [**視覺化**]，您會看見如「圖 7」所示的視窗。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/7.png)

圖 7 視覺化多類別分類中的評分模型結果

**結果解譯**

左側 16 個資料行代表測試集的特徵值。類別 "XX" 之名稱為「評分機率」的資料行，就像是雙類別案例的「評分機率」資料行。它們會顯示對應項目落在特定類別的機率。例如，對於第一個項目，有 0.003571 的機率是 “A”，有 0.000451 的機率是 “B”，依此類推。最後的「評分標籤」資料行與雙類別案例的「評分標籤」相同。它會選取具有最大評分機率的類別做為對應項目的預測類別。例如，對於第一個項目，評分標籤為 “F”，因為它的最大機率是 “F” (0.916995)。

**Web 服務發佈**

目前並非使用[專案資料行][project-columns]以選取一些資料行做為我們的 Web 服務的輸出，而是對每個項目取得評分標籤和評分標籤的機率。基本邏輯是尋找所有評分機率當中最大的機率。若要執行此操作，我們必須使用[執行 R 指令碼][execute-r-script]模組。R 程式碼如「圖 8」所示，實驗如「圖 9」所示。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/8.png)

圖 8 R 程式碼，用以擷取評分標籤和標籤的相關聯機率
  
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/9.png)

圖 9 字母辨識多類別分類問題的最終評分實驗

發佈及執行 Web 服務並且輸入一些輸入特徵值之後，傳回結果如「圖 10」。此手寫字母具有其擷取的 16 個特徵，預測為 “T”，其機率為 0.9715。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/9_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/10.png)

圖 10 鳶尾花雙類別分類的 Web 服務結果

##迴歸

迴歸問題與分類問題不同。在分類問題中，我們嘗試預測離散案例，例如鳶尾花屬於哪個類別。但是在迴歸問題中，我們嘗試預測連續變數，例如汽車的價格，如以下範例所示。

**範例實驗**

我們使用汽車價格預測做為我們的迴歸範例。我們嘗試根據汽車的特徵預測其價格，例如製造、燃料類型、車體類型、驅動輪等等。實驗如「圖 11」所示。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/11.png)

圖 11 汽車價格迴歸問題的實驗

視覺化[評分模型][score-model]模組，結果如「圖 12」所示。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/12.png)

圖 12 視覺化汽車價格預測問題的評分結果

**結果解譯**

在此評分結果中，「評分標籤」是結果資料行。數字是每部汽車的預測價格。

**Web 服務發佈**

我們可以將迴歸實驗發佈至 Web 服務，並且針對汽車價格預測呼叫，方式與雙類別分類使用案例中的方式相同。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/13.png)

圖 13 汽車價格迴歸問題的評分實驗

執行 Web 服務，傳回的結果如「圖 14」所示。此汽車的預測價格為 15085.52。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/13_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/14.png)

圖 14 汽車價格迴歸問題的 Web 服務結果

##叢集

**範例實驗**

讓我們再次使用鳶尾花資料集來建立叢集實驗。我們在這裡篩選資料集中的類別標籤，使其僅具有特徵，且可用於叢集。在此鳶尾花使用案例中，讓我們將訓練處理期間的叢集數指定為 2，表示我們想要將花卉叢集為兩個類別。實驗如「圖 15」所示。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/15.png)

圖 15 鳶尾花叢集問題的實驗

叢集與分類的不同之處在於訓練資料集本身沒有地面實況標籤。我們對於如何將訓練資料集執行個體群組至不同的叢集更感興趣。在訓練處理期間，模型會為項目加上標籤，方法是學習其特徵之間的差異。之後，可以進一步使用已訓練的模型以分類未來的項目。在叢集問題當中，我們感興趣的結果有兩個部分。第一個部分是如何為訓練資料集加上標籤，第二個部分是如何使用已訓練的模型分類新的資料集。

結果的第一個部分可以藉由按一下[訓練叢集模型][train-clustering-model]模組的左側輸出連接埠，然後按一下 [**視覺化**]，加以視覺化。視覺化視窗如「圖 16」所示。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/16.png)

圖 16 視覺化訓練資料集的叢集結果

結果的第二個部分，使用已訓練的叢集模型叢集新的項目，如「圖 17」所示。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/17.png)

圖 17 視覺化新資料集上的叢集結果

**結果解譯**

雖然兩個部分的結果出自於不同的實驗階段，但是其外觀完全相同，並且是以相同的方式進行解譯。前面四個資料行是特徵。最後的資料行 [指派] 是預測結果。已指派相同數字的項目預測為處於相同叢集中，也就是說，它們某種程度上有相似之處 (我們在此實驗中使用預設的歐幾里德距離度量)。請回想我們將叢集數指定為 2。所以在 [指派] 資料行中，項目標籤為 0 或 1。

**Web 服務發佈**

我們可以將叢集實驗發佈至 Web 服務，並且針對叢集預測呼叫，方式與雙類別分類使用案例中的方式相同。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/18.png)

圖 18 鳶尾花叢集問題的評分實驗

執行 Web 服務之後，傳回的結果如「圖 19」所示。此花卉預測為在叢集 0 中。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/18_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/19.png)

圖 19 鳶尾花雙類別分類的 Web 服務結果

##推薦系統
**範例實驗**

對於推薦系統，我們將會使用餐廳推薦問題做為範例：根據餐廳的評等歷史為客戶推薦餐廳。輸入資料是由三個部分組成：

* 來自客戶的餐廳評等 
* 客戶特色資料 
* 餐廳特色資料 

我們可以使用 Azure Machine Learning 的內建[訓練 Matchbox 推薦][train-matchbox-recommender]模組做許多事情：

- 為指定使用者和項目預測評等
- 對指定使用者推薦項目
- 尋找指定使用者相關的使用者
- 尋找指定項目相關的項目

我們可以選擇想要執行的動作，方法是從右窗格的 [**推薦預測類型**] 功能表上的四個選項中進行選取。我們會在這裡逐步完成全部四個案例。典型的推薦系統 Azure Machine Learning 實驗如「圖 20」所示。如需如何使用這些推薦系統模組的詳細資訊，請參閱[訓練 Matchbox 推薦][train-matchbox-recommender]和[評分 Matchbox 推薦][score-matchbox-recommender]的說明頁面。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/19_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/20.png)

圖 20 推薦系統實驗

**結果解譯**

*為指定使用者和項目預測評等*

藉由在 [**推薦預測類型**] 功能表中選取 [評等預測]，我們會要求推薦系統預測指定使用者和項目的評等。[評分 Matchbox 推薦][score-matchbox-recommender]輸出的視覺化如「圖 21」所示。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/21.png)

圖 21 視覺化推薦系統的評分結果 - 評等預測

有三個資料行。前兩個資料行是輸入資料提供的使用者-項目組。第三個資料行是對於特定項目之使用者的預測評等。例如，在第一個資料列中，預測客戶 U1048 將餐廳 135026 評等為 2。

*對指定使用者推薦項目*

藉由在 [**推薦預測類型**] 功能表中選取 [**項目推薦**]，我們會要求推薦系統對指定使用者推薦項目。此案例中還有一個需要選擇的參數，推薦項目選取。選項 [**從評等項目 (針對模型評估)**] 主要適用於訓練處理期間的模型評估。對於此預測階段，我們將會選擇 [**從所有項目**]。[評分 Matchbox 推薦][score-matchbox-recommender]輸出的視覺化如「圖 22」所示。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/22.png)

圖 22 視覺化推薦系統的評分結果 - 項目推薦

有六個資料行。第一個資料行代表用於推薦項目的指定使用者識別碼，由輸入資料提供。其餘五個資料行代表對使用者推薦的項目，以相關性的遞減順序排序。例如，在第一個資料列中，對客戶 U1048 最推薦的餐廳是 134986，接下來是 135018 134975 135021 及 132862。

*尋找指定使用者相關的使用者*

藉由在 [推薦預測類型] 功能表中選取 [相關使用者]，我們會要求推薦系統尋找指定使用者的相關使用者。相關使用者是具有類似偏好的使用者。此案例中還有一個需要選擇的參數，相關使用者選取。選項 [從評等項目的使用者 (針對模型評估)] 主要適用於訓練處理期間的模型評估。對於此預測階段，我們選擇 [從所有使用者]。[評分 Matchbox 推薦][score-matchbox-recommender]輸出的視覺化如「圖 23」所示。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/23.png)

圖 23 視覺化推薦系統的評分結果 - 相關使用者

有六個資料行。第一個資料行是用於尋找相關使用者的指定使用者識別碼，由輸入資料提供。其餘五個資料行會儲存使用者的預測相關使用者，以相關性的遞減順序排序。例如，在第一個資料列中，對於客戶 U1048 最相關的客戶是 U1051，接下來是 U1066 U1044 U1017 及 U1072。

**尋找指定項目相關的項目**

藉由在 [**推薦預測類型**] 功能表中選取 [**相關項目**]，我們會要求推薦系統尋找指定項目的相關項目。相關項目是相同使用者最有可能喜歡的項目。此案例中還有一個需要選擇的參數，相關項目選取。選項 [**從評等項目 (針對模型評估)**] 主要適用於訓練處理期間的模型評估。對於此預測階段，我們選擇 [**從所有項目**]。[評分 Matchbox 推薦][score-matchbox-recommender]輸出的視覺化如「圖 24」所示。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/24.png)

圖 24 視覺化推薦系統的評分結果 - 相關項目

有六個資料行。第一個資料行代表用於尋找相關項目的指定項目識別碼，由輸入資料提供。其他五個資料行會儲存項目的預測相關項目，以相關性的遞減順序排序。例如，在第一個資料列中，對項目 135026 最相關的項目是 135074，接下來是 135035 132875 135055 及 134992。Web 服務發佈

將這些實驗發佈為 Web 服務以取得預測的處理類似於四個案例。我們在這裡採用第二個案例，對指定的使用者推薦項目，做為範例。您可以遵循其他三個案例的相同程序。

將已訓練的推薦系統儲存為已訓練的模型、將輸入資料篩選為要求的單一使用者識別碼資料行，我們可以連結實驗，如「圖 25」所示，並且將其發佈為 Web 服務。

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/25.png)
 
圖 25 餐廳推薦問題的評分實驗

執行 Web 服務，傳回的結果如「圖 14」所示。對於使用者 U1048 的五個推薦餐廳是 134986、135018、134975、135021 及 132862。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/25_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/26.png)

圖 26 餐廳推薦問題的 Web 服務結果


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
 

<!---HONumber=July15_HO2-->