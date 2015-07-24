<properties 
	pageTitle="在 Machine Learning 中使用線性迴歸 | Microsoft Azure" 
	description="在 Excel 和 Azure Machine Learning Studio 中的線性迴歸模型的比較" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="kbaroni;garye" />

# 在 Azure Machine Learning 中使用線性迴歸

> *Kate Baroni* 和 *Ben Boatman* 是 Microsoft 的 Data Insights Center of Excellence 的企業方案架構設計人員。在本文章中，他們將說明使用 Azure Machine Learning 將現有的迴歸分析套件移轉至雲端式解決方案的經驗。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

## 目標

我們的專案開始時有兩個目標：

1. 使用預測分析來改善我們組織每月營收預測的準確度  
2. 使用 Azure ML 確認、最佳化、加快我們的成果的速度和規模。  

與許多企業一樣，我們的組織會每個月都會經歷營收預測程序。我們的商務分析師小組被賦與使用機器學習服務來支援程序並改善預測的準確度工作。小組花費數個月收集多個來源的資料，並透過可識別與服務銷售預測相關的索引鍵屬性的統計分析，來執行資料屬性。下一步是要開始在 Excel 中建立為資料建立統計迴歸模型的原型。在幾週內我們便有了 Excel 迴歸模型，其效果優於目前的欄位和財務預測程序。這也成為預測結果的比較基準。


然後我們進行下一步，將我們的預測分析移至 Azure ML 以了解 Azure ML 對預測的效能如何進行改善。


## 達成預測效能同位檢查

我們的第一優先是達到 Azure ML 和 Excel 迴歸模型之間的同位檢查。對我們想要在 Excel 和 Azure ML 之間達到預測效能同位檢查的訓練和測試資料，指定完全相同的資料，而且相同的分割。一開始我們失敗。Excel 模型的效能勝過 Azure ML 模型。失敗是因為對 Azure ML 的基礎工具設定缺乏了解。與 Azure ML 產品團隊同步討論之後，我們對於我們的資料集需要的基礎設定獲得進一步了解，並達成兩個模型之間的同位檢查。

### 在 Excel 中建立迴歸模型
我們的 Excel 迴歸使用可在 Excel 分析工具箱找到的標準的線性迴歸模型。

我們計算*平均絕對 %錯誤*，並用它做為模型的效能量值。大約花費 3 個月來達成使用 Excel 的工作模型。我們將許多學習經驗帶到 Azure ML 實驗中，最終有助於了解需求。

### 在 Azure Machine Learning 建立可比較的實驗  
我們遵循下列步驟在 Azure ML 中建立我們的實驗：

1.	將資料集以 csv 檔案 (非常小的檔案) 的形式上傳到 Azure ML
2.	建立新的實驗並使用[專案資料行][project-columns]模組來選取 Excel 中所使用的相同資料功能   
3.	使用[分割][split]模組 (與*相對運算式*模式)，將資料分成完全相同的訓練集，正如同在 Excel 中完成的動作  
4.	使用[線性迴歸][linear-regression]模組實驗 (只有使用預設選項)、記載，並將結果與我們 Excel 迴歸模型比較

### 檢閱初步結果
最初，Excel 模型效能明顯勝過 Azure ML 模型：

| |Excel|Azure ML|
|---|:---:|:---:|
|效能| | |
|<ul style="list-style-type: none;"><li>調整 R 平方</li></ul>| 0\.96 |N/A|
|<ul style="list-style-type: none;"><li>決定<br />係數</li></ul>|N/A|	0\.78<br />(低度精確度)|
|平均絕對誤差 |	$9.5M|	$ 19.4M|
|平均絕對誤差 (%)|	6\.03%|	12\.2%

當我們向 Azure ML 小組的開發人員和資料科學家執行我們的程序和結果時，他們快速提供一些實用的秘訣。

* 當您在 Azure ML 中使用[線性迴歸][linear-regression]模組時，會提供兩種方法：
	*  線上梯度下降：可能比較適合較大規模的問題
	*  一般最小平方：這是大多數人聽到線性迴歸時會想到的方法。對於小型資料集，一般最小平方是較佳的選擇。
*  考慮調整 L2 正規化加權參數，以改善效能。它預設設定為 0.001，而對我們的小型資料集，將它設定為 0.005 以改善效能。    

### 謎題解開了！
當我們套用建議時，我們在 Azure ML 中達成與 Excel 的相同基準效能：

|| Excel|Azure ML (初始)|Azure ML 無最小平方|
|---|:---:|:---:|:---:|
|標示的值 |實際值 (數值)|相同|相同|
|線性 |Excel -> 資料分析-> 迴歸|線性迴歸。|線性迴歸|
|線性選項|不適用|預設|一般最小平方<br />L2 = 0.005|
|資料集|26 個資料列，3 個功能，1 個標籤。全部數值。|相同|相同|
|分割：訓練|Excel 對前 18 個資料列進行訓練，對最後 8 個資料列進行測試。|相同|相同|
|分割：測試|Excel 迴歸公式套用至最後 8 個資料列|相同|相同|
|**效能**||||
|調整的 R 平方|0.96|不適用||
|決定係數|不適用|0.78|0.952049|
|平均絕對誤差 |$9.5M|$ 19.4M|$9.5M|
|平均絕對誤差 (%)|<span style="background-color: 00FF00;"> 6.03%</span>|12.2%|<span style="background-color: 00FF00;"> 6.03%</span>|

此外，Excel 係數相較與 Azure 訓練模型中的功能加權不相上下：

||Excel 係數|Azure 功能加權|
|---|:---:|:---:|
|截距/乖離率|19470209.88|19328500|
|功能 A|0.832653063|0.834156|
|功能 B|11071967.08|11007300|
|功能 C|25383318.09|25140800|

## 後續步驟

我們想要在 Excel 內使用 Azure ML Web 服務。我們的商務分析師依賴 Excel，我們需要方法來呼叫 Azure ML Web 服務與一列 Excel 資料列，並讓它將預測值傳回 Excel。

我們也想要使用 Azure ML 中可用的選項和演算法來最佳化我們的模型。

### 與 Excel 整合
我們的解決方案要使我們的 Azure ML 迴歸模型作業化，方法是透過從訓練的模型建立 Web 服務。在幾分鐘內，Web 服務即已建立，我們可以直接從 Excel 呼叫它，以傳回預測的收入值。

*Web 服務儀表板*一節包含可下載的 Excel 活頁簿。活頁簿已使用 Web 服務 API 預先格式化並內嵌結構描述資訊。按一下 [*下載 Excel 活頁簿*] 時，它會開啟，您可以將它儲存到本機電腦。

![][1]
 
在活頁簿開啟時，請將預先定義的參數複製到藍色的 Parameter 區段，如下所示。一旦輸入參數，Excel 即會對外呼叫 Azure ML Web 服務，而會在綠色的預測值區段中顯示預測的計分標籤。活頁簿將會針對在 Parameters 下輸入參數下的所有資料列項目，繼續根據您的訓練模型建立預測。如需有關如何使用這項功能的詳細資訊，請參閱 [從 Excel 使用 Azure Machine Learning Web 服務](machine-learning-consuming-from-excel.md)。

![][2]
 
### 最佳化及進一步實驗
現在我們已具備 Excel 模型的基準，我們可以進行最佳化 Azure ML 線性迴歸模型。我們使用模組[以篩選為基礎的功能選取][filter-based-feature-selection] 來改善我們的初始資料元素的選取，並且它幫助我們達到平均絕對誤差 4.6% 的效能提升。針對未來的專案，我們將使用這項功能，它可以為逐一查看資料屬性，以找出正確的功能，用於模型化組合上，可為我們節省數週的時間。

接下來我們計劃要在我們的實驗中納入其他演算法 (例如 [Bayesian][bayesian-linear-regression]或[推進式決策樹][boosted-decision-tree-regression]) 來比較效能。

如果您想要實驗迴歸，「能量效益迴歸」範例資料集即是可用來嘗試的良好的資料集，其中包含多個數值屬性。資料集是在 ML Studio 中的範例資料集的一部分提供。您可以使用各種不同的學習模組，來預測加熱負載或冷卻負載。下列圖表是針對目標變數冷卻負載預測的能源效率資料集所學習不同的迴歸的效能比較：

|模型|平均絕對誤差|均方根誤差|相對絕對誤差|相對平方誤差|決定係數
|---|---|---|---|---|---
|推進式決策樹|0\.930113|1\.4239|0\.106647|0\.021662|0\.978338
|線性迴歸 (梯度下降)|2\.035693|2\.98006|0\.233414|0\.094881|0\.905119
|類神經網路迴歸|1\.548195|2\.114617|0\.177517|0\.047774|0\.952226
|線性迴歸 (一般最小平方)|1\.428273|1\.984461|0\.163767|0\.042074|0\.957926  

## 重要心得 

我們從並行執行 Excel 迴歸和 Azure Machine Learning 實驗中學到很多。在 Excel 中建立基準模型並與使用 Azure ML [線性迴歸][linear-regression]的模型比較，幫助我們了解 Azure ML，並且我們探索了改善資料的選項和模型效能的機會。

我們也發現，最好使用[以篩選為基礎的功能選取][filter-based-feature-selection]來加速未來的預測專案。藉由將功能選取套用到您的資料，您可以在 Azure ML 中建立改良的模型，以獲得更好的整體效能。

能夠從 Azure ML 傳送預測性的分析預測至 Excel 可大幅增加成功將結果提供給廣泛的商業使用者對象的能力。


## 資源
以下列出一些可幫助您處理迴歸的資源：

* Excel 中的迴歸。如果您未曾使用 Excel 中的迴歸，本教學課程可讓它變得容易：[http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* 迴歸與預測。Tyler Chessman 撰寫部落格文章，說明如何在 Excel 中執行時間序列預測，其中包含初學者適用的良好線性迴歸描述：[http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts)  
* 	一般最小平方線性迴歸：缺點、問題和陷阱。迴歸的簡介和討論：[http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 

<!---HONumber=58_postMigration-->