<properties 
	pageTitle="執行 Python 機器學習服務指令碼 | Microsoft Azure" 
	description="概述 Azure Machine Learning 中對於 Python 指令碼目前支援基礎之下的設計原則，以及基本使用案例、功能及限制。" 
	keywords="python machine learning,pandas,python pandas,python scripts"
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


# 在 Azure Machine Learning Studio 中執行 Python 機器學習服務指令碼

本主題說明 Azure Machine Learning 中對於 Python 指令碼目前支援基礎之下的設計原則。也會概述主要功能，包括支援匯入現有程式碼、匯出視覺效果，最後會討論一些限制和進行中的工作。

[Python](https://www.python.org/) 是許多資料科學家工具櫃中不可或缺的工具。它有簡潔的語法、跨平台支持、大量的強力程式庫及成熟的的開發工具。Python 可用於機器學習模型中常用之工作流程的所有階段，從資料內嵌和處理開始，到模型訓練、驗證及部署的功能建構止。

Azure Machine Learning Studio 支援將 Python 指令碼內嵌至機器學習實驗的各個部分，並且順暢地將其發佈為 Microsoft Azure 上的可擴充、可運作 Web 服務。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]


## 在 Machine Learning 中設計 Python 指令碼的原則
Azure Machine Learning Studio 中 Python 的主要介面是透過「圖 1」中顯示的[執行 Python 指令碼][execute-python-script]模組。

![image1](./media/machine-learning-execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/machine-learning-execute-python-scripts/embedded-machine-learning-python-script.png)

圖 1.**執行 Python 指令碼**模組。

[執行 Python 指令碼][execute-python-script]模組接受最多三個輸入，並且產生最多兩個輸出 (在以下討論)，就像其 R 類比，[執行 R 指令碼][execute-r-script]模組。要執行的 Python 程式碼會輸入至稱為 `azureml_main` 之特殊命名進入點函數的參數方塊。以下是用來實作此模組的關鍵設計原則：

1.	*必須是 Python 使用者慣用的。* 大部分 Python 使用者會將其程式碼歸因為模組內部的函數，所以在最上層模組中放置許多可執行陳述式的情形相對少見。因此，指令碼方塊也會採用特殊命名的 Python 函數，而不是採用一系列的陳述式。在函數中公開的物件是標準 Python 程式庫類型，例如 [Pandas](http://pandas.pydata.org/) 陣列。資料框架和 [NumPy](http://www.numpy.org/) 陣列。
2.	*必須在本機和雲端執行之間具有高畫質。* 用來執行 Python 程式碼的後端是根據 [Anaconda](https://store.continuum.io/cshop/anaconda/) 2.1，在跨平台科學 Python 發佈中廣泛使用。它隨附將近 200 個最常見的 Python 封裝。因此，資料科學家可以在其本機 Azure Machine Learning 相容 Anaconda 環境中對程式碼進行偵錯或驗證是否合格，方法是使用如 [IPython](http://ipython.org/) Notebook 或 [Python Tools for Visual Studio](http://pytools.codeplex.com/) 的開發環境，並且以高度信賴程度做為 Azure Machine Learning 實驗的一部分執行。此外，`azureml_main` 進入點是 Vanilla Python 函數，不需要 Azure Machine Learning 特定程式碼或安裝 SDK 即可編寫。
3.	*必須可以順暢地與其他 Azure Machine Learning 模組組合。* [執行 Python 指令碼][execute-python-script]模組接受標準 Azure Machine Learning 資料集做為輸入和輸出。基礎框架可以透明且有效地橋接 Azure Machine Learning 和 Python 執行階段 (支援如遺漏值的功能)。因此，Python 可以用於與現有 Azure Machine Learning 工作流程接合，包括呼叫至 R 和 SQLite 的那些工作流程。所以可以將工作流程設想為：
  * 使用 Python 和 Pandas 進行前處理和清除、 
  * 將資料饋送至 SQL 轉換、連結多個資料集以形成功能、 
  * 在 Azure Machine Learning 中使用廣泛集合的演算法訓練模型，以及 
  * 使用 R 評估和後處理結果。


## 機器學習服務中 Python 指令碼的基本使用案例
在此章節中，我們會調查[執行 Python 指令碼][execute-python-script]模組的一些基本用法。如稍早所述，對 Python 模組的任何輸入都會公開為 Pandas 資料框架。如需 Python Pandas 及如何使用其更有效率地處理資料的詳細資訊，請參閱 W. McKinney 所撰寫的 *Python for Data Analysis* (Sebastopol, CA.: O'Reilly, 2012)。函數必須傳回在 Python [序列](https://docs.python.org/2/c-api/sequence.html) (例如 tuple、清單或 NumPy 陣列) 內封裝的單一 Pandas 資料框架。然後會在模組的第一個輸出連接埠中傳回此序列的第一個元素。此配置顯示在「圖 2」中。

![image3](./media/machine-learning-execute-python-scripts/map-of-python-script-inputs-outputs.png)

圖 2.將輸入連接埠對應至參數，並且將值傳回至輸出連接埠。

輸入連接埠如何對應至 `azureml_main` 函數的參數的更詳細語意如「表 1」所示：

![image1T](./media/machine-learning-execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

表 1.將輸入連接埠對應至函數參數。

請注意，輸入連接埠和函數參數之間的對應是有位置關係的，例如，第一個連接的輸入連接埠是對應至函數的第一個參數，而第二個輸入 (如果連接) 則是對應至函數的第二個參數。

## 輸入和輸出類型的轉譯
如前面所述，Azure Machine Learning 中的輸入資料集會轉換為 Pandas 中的資料框架，而輸出資料框架會轉換回 Azure Machine Learning 資料集。會執行下列轉換：

1.	字串和數值資料行會如現狀轉換，資料集中的遺漏值則會在 Pandas 中轉換為 ‘NA’ 值。會在反向發生相同轉換 (Pandas 中的 NA 值轉換為 Azure Machine Learning 中的遺漏值)。
2.	Azure Machine Learning 中不支援 Pandas 的索引向量，Python 函數中的所有輸入資料框架一律具有 64 位元數值索引，從 0 到資料列數減 1。 
3.	Azure Machine Learning 資料集無法具有重複的資料行名稱或非字串的資料行名稱。如果輸出資料框架包含非數值資料行，則架構會在資料行名稱上呼叫 `str`。同樣地，任何重複的資料行名稱會自動錯位，以確保名稱是唯一的。後置詞 (2) 會新增至第一個重複項目，後置詞 (3) 新增至第二個重複項目等等。

## 運作 Python 指令碼
當發佈為 Web 服務時，會呼叫計分實驗中的任何[執行 Python 指令碼][execute-python-script]模組。例如，「圖 3」顯示計分實驗，其中包含用以評估單一 Python 運算式的程式碼。

![image4](./media/machine-learning-execute-python-scripts/figure3a.png)

![image5](./media/machine-learning-execute-python-scripts/python-script-with-python-pandas.png)

圖 3.用以評估 Python 運算式的 Web 服務。

根據此實驗建立的 Web 服務會採用 Python 運算式的輸入 (字串)，將其傳送至 Python 解譯器，並且傳回包含運算式和評估結果的資料表。

## 匯入現有的 Python 指令碼模組
對於許多資料科學家，常見的使用案例是將現有 Python 指令碼併入 Azure Machine Learning 實驗。而不是將所有程式碼串連和貼上至單一指令碼方塊，[執行 Python 指令碼][execute-python-script]模組接受第三個輸入連接埠，可以連接包含 Python 模組的 zip 檔案。然後，該檔案由執行架構在執行階段解壓縮，將內容新增至 Python 解譯器的程式庫路徑。然後 `azureml_main` 進入點函數可以直接匯入這些模組。

做為範例，請考量包含簡單 “Hello, World” 函數的 Hello.py 檔案。

![image6](./media/machine-learning-execute-python-scripts/figure4.png)

圖 4.使用者定義函數。

接下來，我們可以建立包含 Hello.py 的 Hello.zip 檔案：

![image7](./media/machine-learning-execute-python-scripts/figure5.png)

圖 5.包含使用者定義 Python 程式碼的 Zip 檔案。

然後，將此檔案上傳至 Azure Machine Learning Studio 做為資料集。如果我們稍後可以建立及執行簡單實驗以使用模組：

![image8](./media/machine-learning-execute-python-scripts/figure6a.png)

![image9](./media/machine-learning-execute-python-scripts/figure6b.png)

圖 6.範例實驗，具有上傳為 zip 檔案的使用者定義 Python 程式碼。

模組輸出顯示 zip 檔案解除封裝，且函數 `print_hello` 已確實執行。![image10](./media/machine-learning-execute-python-scripts/figure7.png)
 
圖 7.使用者定義函數在[執行 Python 指令碼][execute-python-script]模組內部使用。

## 使用視覺效果
使用可以在瀏覽器上看到的 MatplotLib 建立的繪圖，可以由[執行 Python 指令碼][execute-python-script]傳回。但是繪圖不會在使用 R 時自動重新導向至映像。因此使用者必須明確地將任何繪圖儲存為 PNG 檔案，才能傳回至 Azure Machine Learning。

若要從 MatplotLib 產生映像，您必須計算下程序：

* 將後端從預設 Qt 型轉譯器切換為 “AGG” 
* 建立新的圖表物件 
* 取得軸並且對其產生所有繪圖 
* 將圖表儲存為 PNG 檔案 

此程序在以下的「圖 8」中說明，會使用 Pandas 中的 scatter_matrix 函數建立散佈圖矩陣。
 
![image1v](./media/machine-learning-execute-python-scripts/figure-v1-8.png)

圖 8.將 MatplotLib 圖表儲存為映像。



圖 9 顯示實驗，該實驗使用上方顯示的指令碼透過第二個輸出連接埠傳回繪圖。

![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9a.png)
	 
![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9b.png)

圖 9.視覺化從 Python 程式碼產生的繪圖。

請注意，可以藉由將圖表儲存為不同的映像以傳回多個圖表，Azure Machine Learning 執行階段會挑選所有映像並且加以串連以視覺化。


## 進階範例
Azure Machine Learning 中安裝的 Anaconda 環境包含常用封裝 (例如 NumPy、SciPy 及 Scikits-Learn)，以及可有效用於典型機器學習服務管線中各種資料處理工作的封裝。做為範例，下列實驗和指令碼說明在 Scikits-Learn 中使用集成學習器以計算資料集的功能重要性分數。然後可以使用該分數先執行受監督的功能選取，再將功能饋送至其他機器學習模型。

Python 函數，用於根據以下顯示的功能計算重要性分數及排序功能：

![image11](./media/machine-learning-execute-python-scripts/figure8.png)

圖 10.函數，依據分數排名功能。然後下列實驗會在 Azure Machine Learning 中的 “Pima Indian Diabetes” 資料集計算及傳回功能的重要性分數：

![image12](./media/machine-learning-execute-python-scripts/figure9a.png) ![image13](./media/machine-learning-execute-python-scripts/figure9b.png)
	
圖 11.實驗，在 Pima Indian Diabetes 資料集中排名功能。

## 限制 
[執行 Python 指令碼][execute-python-script]目前具有下列限制：

1.	*沙箱化執行。* Python 執行階段目前已沙箱化，因此，不允許以永續方式存取網路或本機檔案系統。所有本機儲存的文件都會被隔離，並且在模組結束時加以刪除。Python 程式碼無法在其執行的機器上存取大部分的目錄，例外是目前目錄及其子目錄。
2.	*缺少精細的開發和偵錯支援。* Python 模組目前不支援 IDE 功能，例如 intellisense 和偵錯。此外，如果模組在執行階段時失敗，則可以使用完整的 Python 堆疊追蹤，但是必須在模組的輸出記錄中檢視。目前我們建議使用者在如 IPython 的環境中開發及偵錯其 Python 指令碼，然後將程式碼匯入模組。
3.	*單一資料框架輸出。* Python 進入點是唯一獲得允許的位置，可以將單一資料框架傳回為輸出。目前無法直接將任意 Python 物件 (例如訓練模型) 傳回 Azure Machine Learning 執行階段。像是[執行 R 指令碼][execute-r-script]，其具有相同的限制，但是在許多案例中可以將物件揀選至位元組陣列，然後將其傳回資料框架內。
4.	*無法自訂 Python 安裝*。目前，新增自訂 Python 模組的唯一方法是透過稍早所述的 zip 檔案機制。對於小模組可行，但是對於大模組 (特別是具有原生 DLL 的模組) 和大量模組而言則顯得繁瑣。 


##結論
[執行 Python 指令碼][execute-python-script]模組可以讓資料科學家將現有 Python 程式碼併入 Azure Machine Learning 中雲端託管的機器學習工作流程，並且順暢地將其運作為 Web 服務的一部分。Python 指令碼模組會自然地與 Azure Machine Learning 中的其他模組相互配合，並且可以用於一定範圍的工作，從資料探索到前處理、功能擷取、及評估和後處理結果。用於執行的後端執行階段是根據 Anaconda，這是一個經過良好測試及廣泛使用的 Python 散佈。如此可讓使用者方便將現有程式碼資產加入雲端。

在未來幾個月，我們期望為[執行 Python 指令碼][execute-python-script]模組提供其他功能，例如在 Python 中訓練和運作模型的功能，以及為在 Azure Machine Learning Studio 中的開發和偵錯程式碼新增更好的支援。


<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=July15_HO2-->