<properties 
	pageTitle="進階分析程序和技術實務：在 1 TB Criteo 資料集上使用 Hadoop 叢集 | Microsoft Azure" 
	description="對採用 HDInsight Hadoop 叢集來建置和部署使用大型 (1 TB) 公開可用資料集模型的端對端案例使用進階分析程序和技術 (ADAPT) 使用" 
	metaKeywords="" 
	services="machine-learning,hdinsight" 
	solutions="" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="ginathan;mohabib;bradsev" />

# 進階分析程序和技術實務 - 在 1 TB 資料集上使用 Azure HDInsight Hadoop 叢集

在這個逐步解說中，我們會示範使用「進階分析程序和技術 (ADAPT)」端對端搭配 [Azure HDInsight Hadoop 叢集](http://azure.microsoft.com/services/hdinsight/)來儲存、探索、號召工程人員，並從其中一個公開可用的 [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) 資料集縮減取樣資料。我們使用 Azure Machine Learning 針對此資料建置二進位的分類和迴歸模型。我們也會顯示如何將其中一個模型發佈為 Web 服務。

此外，也可以使用 iPython Notebook 來完成此逐步解說中說明的工作。想要嘗試這種方法的使用者，應該查閱[使用 Hive ODBC 連線的 Criteo 逐步解說](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb)主題。


## <a name="dataset"></a>Criteo 資料集說明

Criteo 資料是點選預測的資料集，大約是 370 GB 的 gzip 壓縮 TSV 檔案 (~1.3 TB 未壓縮)，包含超過 43 億筆記錄。它取自 [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) 提供的 24 天點選資料。為了方便資料科學家，我們已將可供試驗的資料解壓縮。

在此資料集中的每一筆記錄包含 40 個資料行：

- 第一個資料行是標籤資料行，指出使用者是否按一下加入 (值 1) 或未按一下 (值 0) 
- 接下來 13 個資料行是數字，以及 
- 最後 26 個資料行是類別資料行 

資料行為匿名，並使用一系列的列舉名稱："Col1" (針對標籤資料行) 至 "Col40" (針對最後一個分類資料行)。

以下是來自這個資料集的兩個觀察 (資料列) 的前 20 個資料行的摘錄：

	Col1	Col2	Col3	Col4	Col5	Col6	Col7	Col8	Col9	Col10	Col11	Col12	Col13	Col14	Col15			Col16			Col17			Col18			Col19		Col20	

	0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
	0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

此資料集中的數值及分類資料行中有遺漏值。我們在以下說明簡單的方法，用來處理遺漏的值。資料的其他詳細資料會在以下將它們儲存成 Hive 資料表時加以探究。

**定義：***點選率 (CTR)：*這是資料中點選的百分比。在此 Criteo 資料集中，CTR 是大約 3.3%或 0.033。

## <a name="mltasks"></a>預測工作的範例
本逐步解說將討論兩個範例預測問題：

1. **二進位分類**：預測使用者是否按下加入：
	- 類別 0：未按一下
	- 類別 1：按一下

2. **迴歸**：預測使用者按一下廣告機率的功能。


## <a name="setup"></a>為資料科學設定 HDInsight Hadoop 叢集

**附註：**這通常是**管理**工作。

設定 Azure 資料科學環境，用於使用 HDInsight 叢集以三個步驟建置預測性的分析解決方案：

1. [建立儲存體帳戶](../storage-whatis-account.md)：此儲存體帳戶用來將資料儲存在 Azure Blob 儲存體中。HDInsight 叢集中使用的資料會儲存在這裡。

2. [自訂適用於資料科學的 Azure HDInsight Hadoop 叢集](machine-learning-data-science-customize-hadoop-cluster.md)：這個步驟將會建立已在所有節點上安裝 64 位元 Anaconda Python 2.7 的 Azure HDInsight Hadoop 叢集。自訂 HDInsight 叢集時有兩個需完成的重要步驟 (如本主題所述)。

	* 建立時您必須將步驟 1 中建立的儲存體帳戶與 HDInsight 叢集連結。此儲存體帳戶用於存取可以在叢集內處理的資料。

	* 建立後，您必須對叢集的前端節點啟用遠端存取。請記住您在此處指定的遠端存取認證 (與建立時指定叢集的不同)：您在以下會需要該資訊。

3. [建立 Azure ML 工作區](machine-learning-create-workspace.md)：此 Azure Machine Learning 工作區用來在初始資料瀏覽之後建置機器學習模型，並且在 HDInsight 叢集上下載取樣。

## <a name="getdata"></a>取得並從公用來源取用資料

[Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) 資料集可以透過按一下連結、接受使用條款並提供名稱來存取。其外觀的快照如下所示：

![接受 Criteo 條款](http://i.imgur.com/hLxfI2E.png)

按一下 [**繼續下載**] 來閱讀資料集的相關資訊和它的可用性。

資料位於公用 [Azure Blob 儲存體](../storage-dotnet-how-to-use-blobs.md)位置：wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/。"wasb" 指的是 Azure Blob 儲存體位置。

1. 這個公用 Blob 儲存體中的資料是由所解壓縮資料的三個子資料夾所組成。
		
	1. 子資料夾 *raw/count/* 包含前 21 天的資料 - 從 day_00 到 day_20
	2. 子資料夾 *raw/train/* 由單一天 day_21 的資料組成
	3. 子資料夾 *raw/test/* 由兩天 day_22 和 day_23 的資料組成

2. 對於想要從原始 gzip 資料開始的使用者，這些也可以在主要資料夾 *raw/* 取得，形式為 day_NN.gz，其中 NN 從 00 到 23。

本逐步解說中稍後會在我們建立 Hive 資料表時說明存取、瀏覽和模型化此資料而不需要任何本機下載的另一種方法。

## <a name="login"></a>登入到叢集前端節點

若要登入叢集的前端節點，請使用 [Azure 管理](manage.windowsazure.com)入口網站，來找出叢集。在左側按一下 HDInsight 大象圖示，然後按兩下叢集的名稱。瀏覽至 [**組態**] 索引標籤上，在頁面底部的 [連接] 圖示上連按兩下，然後在提示時輸入您的遠端存取認證。這會帶您前往叢集的前端節點。

以下是典型的第一次登入叢集前端節點時看起來的外觀：

![登入到叢集](http://i.imgur.com/Yys9Vvm.png)

在左邊，我們會看到「Hadoop 命令列」，這將是我們進行資料瀏覽的主要位置。我們也會看到兩個實用的 URL - "Hadoop Yarn Status" 和 "Hadoop Name Node"。Yarn 狀態 URL 會顯示工作進度，而名稱節點 URL 則會提供叢集組態的相關詳細資料。

我們現在已設定，並已準備好開始逐步解說的第一個部分：使用 Hive 的資料瀏覽和為 Azure Machine Learning 備妥資料。

## <a name="hive-db-tables"></a> 建立 Hive 資料庫和資料表

若要為我們的 Criteo 資料集建立 Hive 資料表，請在前端節點的桌面上開啟 ***Hadoop 命令列***，然後輸入以下命令來進入 Hive 目錄

    cd %hive_home%\bin

**重要事項**：**請從上述的 Hive bin/ 目錄提示執行此逐步解說中的所有 Hive 命令。如此可自動處理路徑相關問題。我們將使用交替使用詞彙「Hive 目錄提示」、「Hive bin/ 目錄提示」和「Hadoop 命令列」。**

**重要事項 2**：**若要執行任何 Hive 查詢，使用者一律可以執行下列 ** cd %hive_home%\bin hive

Hive REPL "hive >" 出現記號後，只需剪下並貼上查詢即可執行。

下列程式碼會建立資料庫 "criteo"，然後產生 4 個資料表：


* *計數資料表*，在 day_00 到 day_20 建置， 
* *訓練資料表*，在 day_21 建置，以及 
* 兩個*測試資料表*，分別在 day_22 和 day_23。 

因為其中一天是假日，而且我們想要判斷模型是否可以偵測到假日與非假日之間點選率的差異，我們將我們的測試資料集分割成兩個不同資料表。

下面顯示指令碼 [sample&\#95;hive&\#95;create&\#95;criteo&\#95;database&\#95;and&\#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)，以方便使用：

	CREATE DATABASE IF NOT EXISTS criteo;
	DROP TABLE IF EXISTS criteo.criteo_count;
	CREATE TABLE criteo.criteo_count (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

	DROP TABLE IF EXISTS criteo.criteo_train;
	CREATE TABLE criteo.criteo_train (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

	DROP TABLE IF EXISTS criteo.criteo_test_day_22;
	CREATE TABLE criteo.criteo_test_day_22 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

	DROP TABLE IF EXISTS criteo.criteo_test_day_23;
	CREATE TABLE criteo.criteo_test_day_23 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

請注意，這所有資料表都是外部的，因為我們只是指向 Azure Blob 儲存體 (wasb) 位置。

**有兩種方式可以執行我們現在提及的任何 Hive 查詢。**

1. **使用 Hive REPL 命令列**：第一個是發出 "hive" 命令，複製上述查詢並在 Hive REPL 命令列貼上。若要這樣做，請執行：

		cd %hive_home%\bin
		hive

 	現在，在 REPL 命令列，剪下和貼上查詢即可執行。

2. **將查詢儲存到檔案，並執行命令**：第二個是要將查詢儲存為 .hql 檔案 ([sample&\#95;hive&\#95;create&\#95;criteo&\#95;database&\#95;and&\#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) 然後發出下列命令以執行查詢：

		hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### 確認資料庫和資料表的建立

接下來，我們從 Hive bin/ 目錄提示發出以下命令，確認資料庫的建立：

		hive -e "show databases;"

這會提供：

		criteo
		default
		Time taken: 1.25 seconds, Fetched: 2 row(s)

這會確認建立新資料庫，也就是 "criteo"。

若要查看我們建立的資料表，我們只要從 Hive bin/ 目錄提示發出以下命令：

		hive -e "show tables in criteo;"

我們會看到下列輸出：

		criteo_count
		criteo_test_day_22
		criteo_test_day_23
		criteo_train
		Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a> 在 Hive 中的資料瀏覽

現在我們已準備好在 Hive 中執行一些基本的資料瀏覽。我們從計算訓練中的範例數目和測試資料的資料表開始。

### 訓練範例的數目

[sample&\#95;hive&\#95;count&\#95;train&\#95;table&\#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) 的內容如下所示：

		SELECT COUNT(*) FROM criteo.criteo_train;

這會產生：

		192215183
		Time taken: 264.154 seconds, Fetched: 1 row(s)

或者，使用者也可以從 Hive bin/ 目錄提示發出下列命令：

		hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### 兩個測試資料集中測試範例的數目

我們現在會計算兩個測試資料集中的範例數目。[sample&\#95;hive&\#95;count&\#95;criteo&\#95;test&\#95;day&\#95;22&\#95;table&\#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) 的內容如下所示：

		SELECT COUNT(*) FROM criteo.criteo_test_day_22;

這會產生：
	
		189747893
		Time taken: 267.968 seconds, Fetched: 1 row(s)

如往常，我們也可以透過發出下列命令，從 Hive bin/ 目錄提示呼叫指令碼：

		hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

最後，我們將檢查以 day_23 為基礎的測試資料集內的測試範例的數目。

要執行此動作的命令類似於上述程式碼 (請參閱 [sample&\#95;hive&\#95;count&\#95;criteo&\#95;test&\#95;day&\#95;23&\#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql))：

		SELECT COUNT(*) FROM criteo.criteo_test_day_23;

這會提供：
	
		178274637
		Time taken: 253.089 seconds, Fetched: 1 row(s)

### 訓練資料集中的標籤分佈

訓練資料集中的標籤分佈值得一提。為了看到此結果，我們顯示 [sample&\#95;hive&\#95;criteo&\#95;label&\#95;distribution&\#95;train&\#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql) 的內容：

		SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

這會產生標籤分佈：

		1       6292903
		0       185922280
		Time taken: 459.435 seconds, Fetched: 2 row(s)

請注意，正數標籤的百分比為大約 3.3% (與原始資料集一致)。
		
### 訓練資料集中一些數值變數的長條圖分佈

我們可以使用 Hive 的原生 "histogram_numeric" 函式，來了解數值變數分佈的外觀。[sample&\#95;hive&\#95;criteo&\#95;histogram&\#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql) 的內容如下所示：

		SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM 
			(SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
			) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

這會產生下列：

		26      155878415
		2606    92753
		6755    22086
		11202   6922
		14432   4163
		17815   2488
		21072   1901
		24113   1283
		27429   1225
		30818   906
		34512   723
		38026   387
		41007   290
		43417   312
		45797   571
		49819   428
		53505   328
		56853   527
		61004   160
		65510   3446
		Time taken: 317.851 seconds, Fetched: 20 row(s)

橫向檢視 - Hive 中的切割組合是用來產生類似 SQL 的輸出，而不是一般的清單。請注意，在上述資料表中，第一個資料行對應至 bin 中心，而第二個對應至 bin 頻率。

### 訓練資料集中一些數值變數的近似百分比

數值變數是近似百分比的計算也值得一提。Hive 的原生 "percentile_approx" 會為我們執行此動作。[sample&\#95;hive&\#95;criteo&\#95;approximate&\#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) 的內容為：

		SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

這會產生：

		1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
		Time taken: 564.953 seconds, Fetched: 1 row(s)

我們注意到百分比的分佈與通常任何數值變數的長條圖分佈相關。

### 尋找訓練資料集中的某些類別資料行的唯一值數目

繼續進行資料瀏覽，我們現在發現，對於某些類別資料行，他們所採取的唯一值數目。為了執行此動作，我們顯示 [sample&\#95;hive&\#95;criteo&\#95;unique&\#95;values&\#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql) 的內容：

		SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

這會產生：

		19011825
		Time taken: 448.116 seconds, Fetched: 1 row(s)

我們注意到 Col15 有 1900 萬個唯一值！ 使用貝氏方法，像是「一個有效編碼」來編碼這類高維度類別變數不可行。特別是，我們將說明並示範稱為[以計數學習](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx)功能強大又穩健的技術，以有效率地解決此問題。

我們在這個子節的最後來看一下一些其他類別資料行的唯一值數目。[sample&\#95;hive&\#95;criteo&\#95;unique&\#95;values&\#95;multiple&\#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) 的內容為：

		SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)), 
		COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
		FROM criteo.criteo_train;

這會產生：

		30935   15200   7349    20067   3
		Time taken: 1933.883 seconds, Fetched: 1 row(s)

我們看到，除了 Col20 以外，所有其他資料行都有許多唯一值。

### 訓練資料集中成對類別變數的共生計數

訓練資料集中成對類別變數的共生計數也很有趣。這可以使用 [sample&\#95;hive&\#95;criteo&\#95;paired&\#95;categorical&\#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql) 中的程式碼來判斷：

		SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

我們將依其發生計數反轉順序，並在此情況下查看前 15 個。這會提供：

		ad98e872        cea68cd3        8964458
		ad98e872        3dbb483e        8444762
		ad98e872        43ced263        3082503
		ad98e872        420acc05        2694489
		ad98e872        ac4c5591        2559535
		ad98e872        fb1e95da        2227216
		ad98e872        8af1edc8        1794955
		ad98e872        e56937ee        1643550
		ad98e872        d1fade1c        1348719
		ad98e872        977b4431        1115528
		e5f3fd8d        a15d1051        959252
		ad98e872        dd86c04a        872975
		349b3fec        a52ef97d        821062
		e5f3fd8d        a0aaffa6        792250
		265366bf        6f5c7c41        782142
		Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> 對 Azure Machine Learning 的資料集縮減取樣

探索資料集並示範了我們如何可對任何變數 (包括組合) 的這類探索進行的動作之後，我們現在要對資料集縮減取樣，讓我們可以在 Azure Machine Learning 中建置模型。回顧一下我們著重的問題在於：提供一組範例屬性 (從 Col2 到 Col40 的功能值)，我們預測 Col1 是否為 0 (未按一下) 或 1 (按一下)。

為了對我們的訓練和測試資料集縮減取樣至原始大小的 1%，我們使用 Hive 的原生 RAND() 函式。下一個指令碼，[sample&\#95;hive&\#95;criteo&\#95;downsample&\#95;train&\#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) 會為訓練資料集執行此動作：

		CREATE TABLE criteo.criteo_train_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		---Now downsample and store in this table

		INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

這會產生：

		Time taken: 12.22 seconds
		Time taken: 298.98 seconds

指令碼 [sample&\#95;hive&\#95;criteo&\#95;downsample&\#95;test&\#95;day&\#95;22&\#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) 會為測試資料 day_22 執行此動作：

		--- Now for test data (day_22)

		CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

這會產生：

		Time taken: 1.22 seconds
		Time taken: 317.66 seconds


最後，指令碼 [sample&\#95;hive&\#95;criteo&\#95;downsample&\#95;test&\#95;day&\#95;23&\#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) 會為測試資料 day_23 執行此動作：

		--- Finally test data day_23
		CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

這會產生：

		Time taken: 1.86 seconds
		Time taken: 300.02 seconds

以此方式，我們已準備好使用縮減取樣的訓練和測試資料集，在 Azure Machine Learning 模型中建置模型。

在我們繼續 Azure Machine Learning 之前，還有最後一個重要元件是考量計數資料表。在下一個子節中，我們將就此討論一些細節。

##<a name="count"></a> 計數資料表的簡短討論

如我們所見，數個類別變數有極高的維度性。在我們的逐步解說中，我們會提供稱為[以計數學習](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx)的強大技術，以便利用有效率且穩健的方式編碼這些變數。提供的連結中說明此技術的詳細資訊。

**附註：**在本逐步解說中，我們著重在使用計數資料表來產生高維度類別功能的精簡表示法。這當然不是編碼類別功能的唯一方式；如需有關其他技術的詳細資訊，有興趣的使用者可以查看 [one-hot-encoding](http://en.wikipedia.org/wiki/One-hot) 和[功能雜湊](http://en.wikipedia.org/wiki/Feature_hashing)。

為了對計數資料建置計數資料表，我們使用 raw/count 資料夾中的資料。在模型建構區段中，我們告訴使用者如何從頭建置類別功能的這些計數資料表，或是可以為其瀏覽使用預先建置的計數資料表。在下面，我們提到「預先建置的計數資料表」時，表示使用我們提供的計數資料表。有關如何存取這些資料表的方式在以下詳細的說明。

## <a name="aml"></a> 使用 Azure Machine Learning 建置模型

在 Azure Machine Learning 建置模型的程序將遵循下列步驟：

1. [從 Hive 資料表取得資料到 Azure Machine Learning](#step1)
2. [建立實驗：清除資料、選擇學習者並使用計數資料表特性化](#step2)
3. [訓練模型](#step3)
4. [對測試資料進行評分模型](#step4)
5. [評估模型](#step5)
6. [將模型發佈為 Web 服務以供使用](#step6)

現在我們已經準備好在 Azure Machine Learning Studio 中建置模型。我們縮減取樣的資料會在叢集中儲存為 Hive 資料表。我們將使用 Azure Machine Learning 讀取器模組讀取此資料。存取此叢集的儲存體帳戶認證如下。

### <a name="step1"></a> 步驟 1：使用讀取器模組從 Hive 資料表取得資料到 Azure Machine Learning，並選取它進行機器學習實驗

針對**讀取器**模組，圖形中設定的參數值只是範例。以下是為**讀取器**模組「填寫」參數集的一般指引。

1. 對資料來源選擇「Hive 查詢」
2. 在「Hive 查詢」中，簡單的 SELECT * FROM <your_database_name.your_table_name> 就已經足夠。
3. Hcatalog 伺服器 URI：如果您的叢集是 "abc"，那麼這就是：https://abc.azurehdinsight.net
4. Hadoop 使用者帳戶名稱：選擇委任叢集時的使用者名稱 (不是遠端存取使用者名稱)
5. Hadoop 使用者帳戶密碼：選擇委任叢集時上述使用者名稱的密碼 (不是遠端存取使用者密碼)
6. 輸出資料的位置：選擇 "Azure"
7. Azure 儲存體帳戶名稱：叢集相關聯的儲存體帳戶
8. Azure 儲存體帳戶金鑰：叢集相關聯的儲存體帳戶金鑰
9. Azure 容器名稱：如果叢集名稱是"abc"，則這通常就是 "abc" 

這是從 Hive 資料表取得資料時**讀取器**看起來的樣子：

![讀取器取得資料](http://i.imgur.com/i3zRaoj.png)

一旦**讀取器**完成取得的資料 (您將在模組上看到綠色勾號)，將此資料儲存為資料集 (以您選擇的名稱)。看起來像這樣：

![讀取器儲存資料](http://i.imgur.com/oxM73Np.png)


以滑鼠右鍵按一下**讀取器**模組的輸出連接埠。這會顯示**另存為資料集**選項和**視覺化**選項。**視覺化**選項，如果按下，會顯示資料 100 個資料列，以及在右側面板顯示某些實用的摘要統計資料。若要儲存資料，只要選取 [**另存為資料集**] 並遵循指示。

若要選取已儲存的資料集，以用於機器學習實驗，請使用 [**搜尋**] 找出資料集，如下所示。然後只要輸入資料集的部分名稱即可存取它，並拖曳至主面板中。將它拖放到主面板中，選取它來用於機器學習模型建構。

![找出資料集](http://i.imgur.com/rx4nnUH.png)

為訓練和測試資料集執行這項操作。

### <a name="step2"></a> 步驟 2：在 Azure Machine Learning 中建立簡單的實驗來預測按一下/未按一下

我們先示範簡單的實驗架構，然後向下鑽研到一些細節。一開始我們清除資料，然後選擇學習者，最後示範如何對預先建置或由使用者建構的計數資料表使用計數資料表特性化。

![實驗架構](http://i.imgur.com/R4iTLYi.png)

為了向下鑽研，我們從已儲存的資料集開始，如下所示。

**清除遺漏的資料**模組的功能如其名稱所言：以可以是使用者指定的方式清除遺漏的資料。查看此模組，我們看到這個：

![清除遺漏的資料](http://i.imgur.com/0ycXod6.png)

在這裡，我們選擇將所有的遺漏值取代為 0。還有其他選項，可以藉由查看模組中的下拉式清單看到。

接下來，我們需要選擇學習者。我們使用兩個類別推進式決策樹做為我們的學習者。特別是，我們將示範如何在高維度分類功能上使用計數功能，來建置我們的模型的精簡表示，同時也為了有效的訓練和測試。

要在此處繼續討論的計數資料表其實是：它們是類別條件式計數 (有時候，我們也會只將它們稱為「條件式計數」)。基本上，它們是為每個類別的功能計算值的一個方式，然後使用這些計數來計算對數機率。


#### 取得預先建置的計數資料表的存取權來建立模型

若要取得我們預先建置的計數資料表的存取，請按一下 [**資源庫**]，如下所示：

![資源庫](http://i.imgur.com/TsWkig3.png)

按一下 [**資源庫**] 會帶領使用者進入看起來像下面的頁面：

![資源庫主要頁面](http://i.imgur.com/dmXo0KR.png)

在這裡，搜尋片語「Criteo 計數」，並在結果清單中向下捲動。您應該會看到：

![Criteo 計數](http://i.imgur.com/JZ119Jf.png)

按一下這個實驗將前往類似以下的頁面：

![計數](http://i.imgur.com/dxdjMjh.png)

在這裡，按一下 [**在 Studio 中開啟**] 來將實驗複製到您的工作區。這也會自動將資料集複製過去 - 在此情況下，相關的兩個主要資料集為計數資料表和計數中繼資料，我們會更詳細說明。

#### 資料集中的計數功能

在我們的實驗的下一個模組牽涉到使用預先建置的計數資料表。若要使用這些預先建置的計數資料表，請在新實驗的 [搜尋] 索引標籤中搜尋 "cr_count_"，您應該會看到兩個資料集："cr_count_cleanednulls_metadata" 和 "cr_count_table_cleanednulls"。將這兩個拖放到右側的實驗窗格。如往常在輸出連接埠上按一下滑鼠右鍵，可讓我們以視覺化方式檢視這些的外觀。

計數資料表中繼資料視覺效果看起來像下面：

![計數資料表中繼資料](http://i.imgur.com/A39PIe7.png)

請注意，中繼資料具備我們用來建置條件式計數的資料行、我們是否使用字典來建置它 (替代方法是計數-最小縮略圖)、使用的雜湊種子、用於雜湊功能的雜湊位元數、類別數量等等的詳細資訊。

計數資料表的視覺效果看起來如下所示：

![計數資料表](http://i.imgur.com/NJn1EQO.png)

我們看到計數資料表具有類別條件式計數的詳細資訊。類別功能的值處於「雜湊值」，因此功能本身會是雜湊。

計數功能是如何建置到資料集中的？ 為此，我們使用計數 **Featurizer** 模組，如下所示：

![計數 Featurizer 模組](http://i.imgur.com/dnMdrR1.png)

在建置計數資料表之後 (請記得我們要在此產生類別新功能的類別條件計數)，我們使用如上所示的**計數 Featurizer** 模組，在我們的資料集中建置這些計數功能。如我們所見，**Featurizer** 模組可讓您選擇要計算的功能，是只需要對數機率或也需要計數，以及其他進階選項。

#### 從頭建置計數資料表

回顧我們在「計數資料表的簡短討論」中所提到，除了使用預先建置的計數資料表 (我們在前幾節中詳細討論)，使用者也可以從頭建置自己的計數資料表。

在本節中，我們會示範如何從頭建立計數資料表。為了執行此動作，我們使用如下所示的**建置計數資料表**模組與其設定：

![建置計數資料表模組](http://i.imgur.com/r7pP8Qq.png)

接著是此模組的設定的後半部：

![建置計數資料表模組設定](http://i.imgur.com/PvmSh3C.png)


**重要事項：**針對叢集和儲存體帳戶設定，請使用「您」的相關值！

按一下 [**執行**] 可讓我們在選擇的叢集上建置計數資料表。如之前所示，輸出為計數資料表和其相關聯的中繼資料。準備好這些資料表後，我們現在可以建置實驗。


### <a name="step3"></a> 步驟 3：訓練模型

若要選取此選項，只要在 [搜尋] 方塊中輸入「兩個類別推進式」，然後拖曳該模組。我們使用推進式決策樹學習者的預設值，如下所示：

![BDT 學習者](http://i.imgur.com/dDk0Jtv.png)

在執行 ML 實驗之前，我們需要三個最終元件。

第一個是訓練模型模組。雖然其第一個連接埠會取得學習者做為輸入，第二個連接埠將會取得訓練資料集用於學習。我們在以下顯示其外觀，並顯示模組中需要設定的參數。

![BDT 訓練模型模組連接](http://i.imgur.com/szS2xBb.png)

![BDT 訓練模型模組設定](http://i.imgur.com/nd7lHBL.png)

### <a name="step4"></a> 步驟 4：對測試資料集進行評分模型

第二個元件是對測試資料集進行評分的方法。使用**評分模型**模組即可方便地達成 - 它會從訓練資料取得學習到的模型做為輸入，以及要進行預測的測試資料集。我們在以下顯示其外觀。

![分數 BDT 模型連接](http://i.imgur.com/AwIH1rH.png)

### <a name="step5"></a> 步驟 5：評估模型

最後，我們想要查看模型的效能。通常，針對兩個類別 (二進位) 分類的問題，AUC 是良好的測量方式。為了將此情況視覺化，我們為此將「評分模型」模組連接至「評估模型」模組。在 [**評估模型**] 模組上按一下 [**視覺化**] 會產生類似如下的圖形：

![評估模組 BDT 模型](http://i.imgur.com/0Tl0cdg.png)

在二進位檔 (或兩個類別) 分類的問題中，AUC 是預測準確度良好的測量方式。我們在以下顯示在我們的測試資料集上使用此模型的結果。若要檢查結果，請以滑鼠右鍵按一下 [**評估模型**] 模組的輸出連接埠，然後選取 [**視覺化**]。

![視覺化評估模型模組](http://i.imgur.com/IRfc7fH.png)

### <a name="step6"></a> 步驟 6：將模型發佈為 Web 服務
最值得一提的部分在於能夠將機器學習模型發佈為 Web 服務。完成後，我們可以使用我們需要預測的資料對 Web 服務呼叫，而模型在理想的情況下會傳回特定種類的預測。

若要執行此動作，我們先將我們的訓練模型儲存為訓練的模型物件。做法是以滑鼠右鍵按一下 [**訓練模型**] 模組，並使用「儲存為訓練的模型」選項。

接下來，我們要為我們的 Web 服務建立輸入和輸出連接埠 -- 輸入連接埠會以與我們需要預測的資料相同的形式取得資料，而輸出連接埠會傳回「評分標籤」和相關聯的機率。

#### 為輸入連接埠選取幾個資料列的資料

我們現在顯示如何為輸入連接埠只選取幾個資料列的資料。

![輸入連接埠資料](http://i.imgur.com/XqVtSxu.png)

我們可以方便地使用套用 SQL 轉換，僅選取 10 個資料列做為輸入連接埠資料 。

#### Web 服務
現在，我們已經準備好執行可以用來發佈 Web 服務的小型試驗。

#### 產生 Web 服務的輸入資料

做為預備步驟，由於計數資料表很大，我們會取得幾行測試資料，並使用計數功能從它產生輸出資料。這會做為我們的 Web 服務的輸入資料格式。這如以下所示：

![建立 BDT 輸入資料](http://i.imgur.com/OEJMmst.png)

附註：針對輸入資料格式，我們現在要使用**計數 Featurizer** 模組的輸出。一旦此實驗執行完成，將輸出從**計數 Featurizer** 模組儲存為資料集。**重要事項：**此資料集將用於 Web 服務中的輸入資料。

#### 發佈 Web 服務的評分實驗

首先，我們顯示其外觀。基本結構是評分模型模組，其會接受我們的訓練模型物件和我們在先前步驟中使用**計數 Featurizer** 模組產生的一些輸入資料行。我們使用「專案資料行」來投射出評分標籤和評分的機率。

![專案資料行](http://i.imgur.com/kRHrIbe.png)

它對於查看如何將「專案資料行」模組用於「篩選」掉資料集中的資料很有助益。我們顯示以下的內容：

![使用專案資料行模組篩選](http://i.imgur.com/oVUJC9K.png)

若要取得藍色的輸入和輸出連接埠，我們只要按一下位於右下方的「準備 Web 服務」。執行這項實驗也可讓我們發佈 Web 服務，方法是按一下右下方的 [**發佈 Web 服務**] 圖示，如下所示。

![發佈 Web 服務](http://i.imgur.com/WO0nens.png)

發佈 Web 服務之後，我們會被重新導向至看起來如下的頁面：

![](http://i.imgur.com/YKzxAA5.png)

我們在左邊看到兩個 Web 服務的連結：

* **要求/回應**服務 (或 RRS) 適用於單一預測，我們會在這場研討會中使用。 
* **批次執行**服務 (BES) 正如其名，用於批次預測，而且要求要進行預測的資料位於 Azure Blob。

按一下連結**要求/回應**會帶我們前往提供我們以 C\#、python 和 R 預先定義程式碼的頁面。此程式碼可以方便地用於對 Web 服務進行呼叫。請注意，需要使用此頁面上的 API 金鑰來進行驗證。

將此 python 程式碼複製到 iPython notebook 中新的儲存格很方便。

下面我們顯示一段具有正確的 API 金鑰的 python 程式碼。

![Python 程式碼](http://i.imgur.com/f8N4L4g.png)

請注意，我們以我們的 Web 服務 API 金鑰取代預設的 API 金鑰。按一下 iPython notebook 中這個儲存格上的 [執行]，會產生下列回應：

![iPython 回應](http://i.imgur.com/KSxmia2.png)

我們會看到對我們要求的兩個測試範例 (在 python 指令碼的 JSON 架構中)，我們會以 "Scored Labels, Scored Probabilities" 形式獲得解答。請注意，在此情況下，我們選擇預先定義的程式碼提供的預設值 (所有數值資料行為 0，所有類別資料行為字串 "value")。

這便結束使用 Azure ML 處理大型資料集端對端的討論 - 我們是從 1 TB 的資料開始，一直到部署在雲端中 Web 服務的預測模型。

 

<!---HONumber=58_postMigration-->