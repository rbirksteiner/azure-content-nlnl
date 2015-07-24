<properties 
	pageTitle="使用 Machine Learning Studio 中的範例資料集 | Microsoft Azure" 
	description="ML Studio 中包含的範例模型所使用之資料集的描述。您可以為您的實驗使用這些範例資料集。" 
	keywords="data sets,datasets,ml studio,sample data sets"
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


# 在 Azure Machine Learning Studio 中使用範例資料集

[top]: #machine-learning-sample-datasets

在 Azure Machine Learning 中建立新的工作區時，預設會包含一些範例資料集和實驗。其中的許多範例資料集會由 [Azure Machine Learning 模型資源庫](http://azure.microsoft.com/documentation/services/machine-learning/models/)中的範例模型所使用，其他則是包含做為機器學習中使用的各種類型資料的範例。

其中的部分資料集可在 Azure Blob 儲存體中使用。下表提供這些資料集的直接連結。您也可以在您的實驗中使用這些資料集，方法是使用[讀取器][reader]模組，若要查看如何存取資料集的範例，請在與資料集相關聯的範例實驗中檢視[讀取器][reader]模組的屬性。

在 ML Studio 中開啟或建立新實驗時，實驗畫布左側的模組調色盤中**儲存的資料集**底下會列出其餘的範例資料集。您可以將資料集拖曳到您的實驗畫布上，在自己的實驗中使用任一資料集。


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

## 與 Machine Learning 資源庫中的模組相關聯的範例資料集

下列資料集是由 [Azure Machine Learning 資源庫](http://azure.microsoft.com/documentation/services/machine-learning/models/)中的範例模型所使用。如需模型和其相關聯的實驗的詳細資訊，請遵循資料表中的連結以查看模型詳細資料。

<table>

<tr>
  <th align=left>範例模型</th>
  <th align=left>關聯的實驗</th>
  <th align=left>資料集名稱</th>
  <th align=left>資料集說明</th>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-color-quantization-using-k-means-clustering/">使用 K-Means 叢集的色彩量化</a></td>
  <td valign=top>範例實驗 - 使用 K-Means 叢集的色彩型影像壓縮 - 開發</td>
  <td valign=top>Bill Gates RGB 影像</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-credit-risk-prediction/">信用風險預測</a></td>
  <td valign=top>範例實驗 - 德國信用 - 開發</td>
  <td valign=top>德國信用卡 UCI 資料集</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td rowspan=4 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-crm-task/">CRM 工作</a></td>
  <td rowspan=4 valign=top>範例實驗 - CRM - 開發</td>
  <td valign=top>共用 CRM 資料集</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]
  </td>
</tr>
<tr ID=crm-appetency-labels-shared>
  <td valign=top>共用 CRM Appetency 標籤</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-churn-labels-shared>
  <td valign=top>共用 CRM 流失標籤</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-upselling-labels-shared>
  <td valign=top>共用 CRM 向上銷售標籤</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-finding-similar-companies/">尋找類似的公司</a></td>
  <td valign=top>範例實驗 - S &amp; P 500 公司叢集 - 開發</td>
  <td valign=top>Wikipedia SP 500 資料集</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
  </td>
</tr>

<tr ID=airport-codes-dataset>
  <td rowspan=3 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-flight-delay-prediction/">航班誤點預測</a></td>
  <td rowspan=3 valign=top>範例實驗 - 航班誤點預測 - 開發</td>
  <td valign=top>機場代碼資料集</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-airport-codes](../../includes/machine-learning-sample-dataset-airport-codes.md)]
  </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>航班誤點資料</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-delays-data](../../includes/machine-learning-sample-dataset-flight-delays-data.md)]
</td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>天氣資料集</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-weather-dataset](../../includes/machine-learning-sample-dataset-weather-dataset.md)]
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top> <a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-network-intrusion-detection/">網路入侵偵測</a></td>
  <td valign=top>範例實驗 - 網路入侵偵測 - 開發</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../../includes/machine-learning-sample-dataset-network-intrusion.md)]
  </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-number-of-bike-rentals/">預測單車出租數量</a></td>
  <td valign=top>範例實驗 - 單車的需求預測</td>
  <td valign=top>單車出租 UCI 資料集</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-student-performance/">預測學習成效</a></td>
  <td valign=top>範例實驗 - 學習成效 - 開發</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-student-performance](../../includes/machine-learning-sample-dataset-student-performance.md)]
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-sentiment-analysis/">情感分析</a></td>
  <td valign=top>範例實驗 - 情感分類 - 開發</td>
  <td valign=top>來自 Amazon 的書籍評論</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
  </td>
</tr>

</table>



## Machine Learning Studio 範例實驗中使用的資料集

下列資料集是由 [Machine Learning Studio](https://studio.azureml.net/Home) 中包含的範例實驗所使用。

在 ML Studio 中開啟或建立新實驗時，您可以在實驗畫布左側的模組調色盤中**儲存的資料集**下找到這些資料集。您可以將資料集拖曳到您的實驗畫布上，在自己的實驗中使用任一資料集。

您可以在 ML Studio 的 [**實驗**] 頁面的 [**範例**] 索引標籤下中找到此處所列的範例實驗。若要製作一份您可以編輯的範例實驗，請在 ML Studio 中開啟實驗並按一下 [**另存新檔**]。


<table>

<tr>
  <th align=left>資料集名稱</th>
  <th align=left>範例實驗</th>
  <th align=left>資料集說明</th>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>乳癌特徵
  <td valign=top>範例實驗 - 乳癌 - 開發</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-features](../../includes/machine-learning-sample-dataset-breast-cancer-features.md)]
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>乳癌資訊</td>
  <td valign=top><i>相同</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-info](../../includes/machine-learning-sample-dataset-breast-cancer-info.md)]
  </td>
</tr>

<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>範例實驗 - 直接行銷 - 開發 - 訓練</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-direct-marketing](../../includes/machine-learning-sample-dataset-direct-marketing.md)]
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>IMDB 影片標題</td>
  <td valign=top>範例實驗 - 影片建議者 - 開發</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-imdb-movie-titles](../../includes/machine-learning-sample-dataset-imdb-movie-titles.md)]
  </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>影片推文</td>
  <td valign=top>範例實驗 - 影片分級的推文，根據其未來的熱門程度 - 開發</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-movie-tweets](../../includes/machine-learning-sample-dataset-movie-tweets.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>範例實驗 - 新聞分類 - 開發</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-test](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-test.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top><i>相同</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-train](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-train.md)]
  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td valign=top><i>相同</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-rcv1-v2-topics](../../includes/machine-learning-sample-dataset-rcv1-v2-topics.md)]
  </td>
</tr>

</table>



## Machine Learning Studio 中的其他範例資料集

[Machine Learning Studio](https://studio.azureml.net/Home) 中包含下列資料集，可提供機器學習中所使用之各種資料類型的範例。在 ML Studio 中開啟或建立新實驗時，您可以在實驗畫布左側的模組調色盤中**儲存的資料集**下找到它們。您可以將資料集拖曳到您的實驗畫布上，在自己的實驗中使用任一資料集。

<table>
<tr>
  <th align=left>資料集名稱</th>
  <th align=left>說明</th>
</tr>

<tr>
  <td valign=top>成人收入普查二進位分類資料集</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-adult-census-income](../../includes/machine-learning-sample-dataset-adult-census-income.md)]
  </td>
</tr>

<tr>
  <td valign=top>汽車價格資料 (原始)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-price](../../includes/machine-learning-sample-dataset-automobile-price.md)]
  </td>
</tr>

<tr>
  <td valign=top>捐血資料</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-blood-donation](../../includes/machine-learning-sample-dataset-blood-donation.md)]
  </td>
</tr>

<tr>
  <td valign=top>乳癌資料</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-data](../../includes/machine-learning-sample-dataset-breast-cancer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>能量效益迴歸資料</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-energy-efficiency-regression](../../includes/machine-learning-sample-dataset-energy-efficiency-regression.md)]
  </td>
</tr>

<tr>
  <td valign=top>航班準點率 (原始)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-on-time-performance](../../includes/machine-learning-sample-dataset-flight-on-time-performance.md)]
  </td>
</tr>

<tr>
  <td valign=top>森林火災資料</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-forest-fires](../../includes/machine-learning-sample-dataset-forest-fires.md)]
  </td>
</tr>

<tr>
  <td valign=top>Iris 雙類別資料</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-iris-two-class](../../includes/machine-learning-sample-dataset-iris-two-class.md)]
  </td>
</tr>

<tr>
  <td valign=top>不同汽車的油耗資料</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-mpg](../../includes/machine-learning-sample-dataset-automobile-mpg.md)]
  </td>
</tr>

<tr>
  <td valign=top>皮馬族印第安人糖尿病二進位分類資料集</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-pima-indians-diabetes](../../includes/machine-learning-sample-dataset-pima-indians-diabetes.md)]
  </td>
</tr>

<tr>
  <td valign=top>餐廳顧客資料</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-customer-data](../../includes/machine-learning-sample-dataset-restaurant-customer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>餐廳特色資料</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-feature-data](../../includes/machine-learning-sample-dataset-restaurant-feature-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>餐廳評等</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-ratings](../../includes/machine-learning-sample-dataset-restaurant-ratings.md)]
  </td>
</tr>

<tr>
  <td valign=top>煉鋼多類別資料集</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-steel-annealing](../../includes/machine-learning-sample-dataset-steel-annealing.md)]
  </td>
</tr>

<tr>
  <td valign=top>望遠鏡資料</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-telescope-data](../../includes/machine-learning-sample-dataset-telescope-data.md)]
  </td>
</tr>


</table>


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=July15_HO1-->