<properties 
	pageTitle="在雲端資料科學程序中將 Hive 查詢提交至 HDInsight Hadoop 叢集 | Azure" 
	description="使用 Hive 查詢，處理來自 Hive 資料表的資料。" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="hangzh;bradsev" /> 

# <a name="heading"></a> 在雲端資料科學程序中將 Hive 查詢提交至 HDInsight Hadoop 叢集

本文件說明在 Azure 中，將 Hive 查詢提交至 HDInsight 服務所管理的 Hadoop 叢集的各種方式。我們將討論數個資料有爭議的工作：資料探索和功能產生。泛型 Hive 查詢，示範如何探索資料，或是在 Azure HDInsight Hadoop 叢集中使用 Hive 來產生功能。這些 Hive 查詢會使用所提供的內嵌 Hive 使用者定義函式 (UDF)。 

[Github 儲存機制](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)中也會提供 [NYC 計程車車程資料](http://chriswhong.com/open-data/foil_nyc_taxi/)案例特定的查詢範例。這些查詢已經具備指定的資料結構描述，且準備好進行提交來執行。 

最後一節將討論使用者可以微調的參數，如此即可改善 Hive 查詢的效能。

## 必要條件
本文假設您已經：
 
* 建立 Azure 儲存體帳戶。如需指示，請參閱[建立 Azure 儲存體帳戶](../hdinsight-get-started.md#storage) 
* 佈建含有 HDInsight 服務的自訂 Hadoop 叢集。如需指示，請參閱[自訂適用於資料科學的 Azure HDInsight Hadoop 叢集](machine-learning-data-science-customize-hadoop-cluster.md)。
* 已將資料上傳至 Azure HDInsight Hadoop 叢集中的 Hive 資料表。如果沒有，請依照[建立資料並載入 Hive 資料表](machine-learning-data-science-move-hive-tables.md)執行，先將資料上傳至 Hive 資料表。
* 啟用叢集的遠端存取。如需指示，請參閱[存取 Hadoop 叢集的前端節點](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess)。 


## <a name="submit"></a>如何提交 Hive 查詢
您可以使用下列方法來提交 Hive 查詢： 

* 叢集前端節點上的 Hadoop 命令列
* IPython Notebook 
* Hive 編輯器
* Azure PowerShell 指令碼 

提交 Hive 查詢時，您也可以控制 Hive 查詢輸出的目的地，它是否會出現在螢幕上，或是輸出到前端節點上的本機檔案或 Azure Blob。

### 透過 Hadoop 叢集前端節點中的 Hadoop 命令列主控台

如果查詢相當複雜，從 Hadoop 叢集的前端節點中直接提交 Hive 查詢，通常會導致整備速度比使用 Hive 編輯器或使用 Azure PowerShell 指令碼來提交還快。 

登入 Hadoop 叢集的前端節點、在前端節點的桌面上開啟 Hadoop 命令列，然後輸入命令

    cd %hive_home%\bin

使用者在 Hadoop 命令列主控台中提交 Hive 查詢的方式有三種：

* 直接從 Hadoop 命令列
* 使用 .hql 檔案
* 從 Hive 命令主控台

#### 從 Hadoop 命令列直接提交 Hive 查詢 

使用者可以執行類似 

	hive -e "<your hive query>; 

的命令，在 Hadoop 命令列中直接提交簡單的 Hive 查詢。在下列範例中，紅色方塊框起來的是提交 Hive 查詢的命令，而綠色方塊框起來的則是 Hive 查詢的輸出。

![Create workspace][10]

#### 提交 .hql 檔案中的 Hive 查詢。

當 Hive 查詢更為複雜且有多行時，在 Hadoop 命令列或 Hive 命令主控台中編輯查詢並不實際。替代方法是在 Hadoop 叢集的前端節點中使用文字編輯器，並將 Hive 查詢儲存在前端節點本機目錄中的 .hql 檔案。然後可在  `hive` 命令中使用 `-f` 引數來提交 .hql 檔案中的 Hive 查詢，如下所示：
	
	`hive -f "<path to the .hql file>"`

![Create workspace][15]


#### 隱藏 Hive 查詢的進度狀態畫面顯示

根據預設，在 Hadoop 命令列主控台中提交 Hive 查詢之後，Map/Reduce 工作的進度將顯示於螢幕上。若要隱藏 Map/Reduce 工作進度的畫面顯示，您可以在命令列中使用引數 `-S` ("S" 必須大寫)，如下所示：

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### 在 Hive 命令主控台中提交 Hive 查詢。

使用者可以也從 Hadoop 命令列執行 `hive` 命令，先進入 Hive 命令主控台，然後從 Hive 命令主控台提交 Hive 查詢。範例如下。  

![Create workspace][11]

在此範例中，這兩個紅色方塊反白顯示的命令分別是用來進入 Hive 命令主控台，以及在 Hive 命令主控台中提交 Hive 查詢。綠色方塊反白顯示的是 Hive 查詢的輸出。

上述範例會在螢幕上直接輸出 Hive 查詢結果。使用者也可以將輸出寫入前端節點上的本機檔案，或寫入 Azure Blob。接著，使用者可以使用其他工具，進一步分析 Hive 查詢的輸出。

#### 將 Hive 查詢結果輸出到本機檔案。 

若要將 Hive 查詢結果輸出到前端節點上的本機目錄，使用者必須在 Hadoop 命令列中提交 Hive 查詢，如下所示：

	`hive -e "<hive query>" > <local path in the head node>`

在下列範例中，Hive 查詢的輸出會寫入 `C:\apps\temp` 目錄中的 `hivequeryoutput.txt` 檔案。

![Create workspace][12]

#### 將 Hive 查詢結果輸出到 Azure Blob

使用者也可以將 Hive 查詢結果輸出到 Hadoop 叢集預設容器內的 Azure Blob。執行此動作的 Hive 查詢看起來如下：

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

在下列範例中，Hive 查詢的輸出會寫入 Hadoop 叢集預設容器內的 Blob 目錄 `queryoutputdir`。在此處，您應該只提供目錄名稱，而不需提供 Blob 名稱。如果您同時提供目錄和 Blob 名稱，即會擲回錯誤，例如 `wasb:///queryoutputdir/queryoutput.txt`。 

![Create workspace][13]

Hive 查詢的輸出會顯示於 Blob 儲存體中，方法是使用 Azure 儲存體總管 (或對等項目) 工具來開啟 Hadoop 叢集的預設容器。如果只要擷取名稱中具有指定字母的 Blob，您可以套用篩選條件 (以紅色方塊反白顯示)。

![Create workspace][14]

### 透過 Hive 編輯器或 Azure PowerShell 命令

使用者也可以透過下列方式來使用查詢主控台 (Hive 編輯器)：在 Web 瀏覽器中輸入
 
https://<Hadoop 叢集名稱>.azurehdinsight.net/Home/HiveEditor 格式的 URL  

。請注意，系統將要求您輸入 Hadoop 叢集認證以進行登入。或者，您可以[使用 PowerShell 提交 Hive 工作](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell)。 


## <a name="hive-dataexploration"></a>資料探索
以下提供數個 Hive 指令碼範例，可用來探索 Hive 資料表中的資料。

1. 取得每個資料分割的觀察計數
	`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. 取得每天的觀察計數
	`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);` 

3. 取得類別資料行中的層級  
	`SELECT  distinct <column_name> from <databasename>.<tablename>`

4. 取得兩個類別資料行組合中的層級數目 
	`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. 取得數值資料行的分佈  
	`SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. 聯結兩個資料表來擷取記錄 

	    SELECT 
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>, 
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>, 
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="hive-featureengineering"></a>功能產生

本節將說明使用 Hive 查詢產生功能的方式。 

> [AZURE.NOTE] 本節中的 Hive 查詢範例假設資料已上傳至 Azure HDInsight Hadoop 叢集中的 Hive 資料表。如果沒有，請依照[建立資料並載入 Hive 資料表](machine-learning-data-science-hive-tables.md)執行，先將資料上傳至 Hive 資料表。

一旦產生額外功能之後，就可以將它們當成資料行新增至現有的資料表，或是建立具有其他功能和主索引鍵的新資料表 (然後與原始資料表聯結)。  

1. [以頻率為基礎的功能產生](#hive-frequencyfeature)
2. [二進位分類中類別變數的風險](#hive-riskfeature)
3. [從日期時間欄位擷取功能](#hive-datefeatures)
4. [從文字欄位擷取功能](#hive-textfeatures)
5. [計算 GPS 座標間的距離](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>以頻率為基礎的功能產生

計算類別變數層級的頻率，或是來自多個類別變數之特定層級組合的頻率，通常很實用。使用者可以使用下列指令碼來計算這些頻率：

		select 
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select 
				<column_name1>,<column_name2>, count(*) as sub_count 
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;
	

### <a name="hive-riskfeature"></a>二進位分類中類別變數的風險

在二進位分類中，若使用的模型只會採用數值功能，我們就需要將非數值類別變數轉換成數值功能。您可以使用數值風險來取代每個非數值層級，藉以完成這個動作。在本節中，我們將說明一些計算類別變數風險值 (記錄機率) 的泛型 Hive 查詢。 


	    set smooth_param1=1;
	    set smooth_param2=20;
	    select 
	    	<column_name1>,<column_name2>, 
			ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
	    from
	    	(
	    	select 
	    		<column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
	    	from
	    		(
	    		select 
	    			<column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
	    		from <databasename>.<tablename> 
	    		)a
	    	group by <column_name1>, <column_name2>
	    	)b 

此範例會設定變數 `smooth_param1` 和 `smooth_param2`，以減緩從資料計算得來的風險值。風險範圍介於-Inf 和 Inf 之間。風險 > 0 表示目標等於 1 的機率大於 0.5。 

計算出風險資料表之後，使用者就可以藉由將資料表聯結至風險資料表，來將風險值指派給該資料表。Hive 聯結查詢已在上一節中提供。

### <a name="hive-datefeature"></a>從日期時間欄位擷取功能

Hive 會和一組 UDF 一起出現，用來處理日期時間欄位。在 Hive 中，預設的日期時間格式是 'yyyy-MM-dd 00:00:00' (例如 '1970-01-01 12:21:32')。本節會顯示擷取月份日期和來自日期時間欄位的月份範例，以及其他可將預設格式以外格式的日期時間字串轉換為預設格式的日期時間字串範例。 

    	select day(<datetime field>), month(<datetime field>) 
		from <databasename>.<tablename>;

這個 Hive 查詢假設 `<日期時間欄位>` 是預設的日期時間格式。

如果日期時間欄位不是預設格式，您需要先將日期時間欄位轉換為 Unix 時間戳記，然後將 Unix 時間戳記轉換為預設格式的日期時間字串。將日期時間為預設格式之後，使用者就可以套用內嵌的日期時間 UDF 來擷取功能。

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

在此查詢中，如果 `<datetime field>` 具有 `03/26/2015 12:04:39` 之類的模式，則 `'<pattern of the datetime field>'` 應該為 `'MM/dd/yyyy HH:mm:ss'`。若要對其進行測試，使用者可以執行

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

佈建叢集時，此查詢中的 `hivesampletable` 預設會預先安裝於所有 Azure HDInsight Hadoop 叢集中。 


### <a name="hive-textfeature"></a>從文字欄位擷取功能

當 Hive 資料表具有一個文字欄位且其中包含以空格分隔的文字字串時，下列查詢便會擷取字串長度，以及字串中的字數。

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num 
		from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>計算 GPS 座標組之間的距離

本節中提供的查詢可直接套用至「NYC 計程車車程資料」。此查詢的目的是示範如何在 Hive 中套用內嵌的數學函式來產生功能。 

此查詢中所使用的欄位是上車與下車位置的 GPS 座標，其名稱為 pickup_longitude、pickup_latitude、dropoff_longitude 及 dropoff_latitude。計算上車與下車座標間直線距離的查詢如下：

		set R=3959;
		set pi=radians(180);
		select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, 
			${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
			*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
			*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
			/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
			+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
			pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
		from nyctaxi.trip 
		where pickup_longitude between -90 and 0
		and pickup_latitude between 30 and 90
		and dropoff_longitude between -90 and 0
		and dropoff_latitude between 30 and 90
		limit 10; 

您可以在[可移動的類型指令碼](http://www.movable-type.co.uk/scripts/latlong.html)網站 (作者為 Peter Lapisu) 上找到計算兩個 GPS 座標間之距離的數學方程式。在他的 Javascript 中，toRad() 函式只是 `lat_or_lon*pi/180`, which converts degrees to radians. Here, `lat_or_lon` 則是緯度或經度。由於 Hive 不提供函式 `atan2`, but provides the function `atan`，因此， `atan2` function is implemented by `atan` 函式 (位於上述 Hive 查詢)，方法是使用 [Wikipedia](http://en.wikipedia.org/wiki/Atan2) 中所提供的定義。 

![Create workspace][1]

您可以在 [Apache Hive wiki](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions) 上的 **內建函式**一節中找到 Hive 內嵌 UDF 的完整清單。 

## <a name="tuning"></a> 進階主題：微調 Hive 參數以提升查詢速度

Hive 叢集的預設參數設定可能不適合 Hive 查詢以及查詢正在處理的資料。本節將討論一些使用者可以微調的參數，來提升 Hive 查詢的效能。使用者需要在處理資料的查詢之前新增參數微調查詢。 

1. **Java 堆積空間**：對於涉及聯結大型資料集或處理長記錄的查詢，常見的錯誤是**堆積空間不足**。這可以藉由將參數 `mapreduce.map.java.opts` 和 `mapreduce.task.io.sort.mb` 設定為所需的值來微調。範例如下：

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;
	

	這個參數會配置 4 GB 記憶體給 Java 堆積空間，也會藉由配置更多記憶體來使排序更有效率。如果發生任何與堆積空間相關的工作失敗錯誤，那麼使用這些配置會是個好主意。

2. **DFS 區塊大小**：這個參數會設定檔案系統所儲存的最小資料單位。例如，如果 DFS 區塊大小為 128 MB，則任何大小小於等於 128 MB 的資料都會儲存於單一區塊中，而大於 128 MB 的資料則會配置額外的區塊。選擇非常小的區塊大小會在 Hadoop 中造成極大的負荷，因為名稱節點必須處理更多要求，以尋找與檔案有關的相關區塊。在處理 GB (或更大型) 資料時，建議的設定如下：

		set dfs.block.size=128m;

3. **將 Hive 中的聯結作業最佳化**：儘管 Map/Reduce 架構中的聯結作業通常是在縮減階段執行，但有時可藉由在對應階段中排程聯結 (亦稱為 "mapjoins") 來得到巨大的收穫。若要在適當時機引導 Hive 執行這個動作，我們可以設定：

		set hive.auto.convert.join=true;

4. **指定 Hive 的對應程式數目**：儘管 Hadoop 允許使用者設定縮減程式的數目，但使用者通常不會設定對應程式的數目。允許對此數目進行某種程度控制的一個技巧選擇 hadoop 變數 ( *mapred.min.split.size* 和 *mapred.max.split.size*)，因為每個對應工作的大小取決於：

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	一般而言， *mapred.min.split.size* 的預設值為 0， *mapred.max.split.size* 的預設值是 **Long.MAX**，而 *dfs.block.size* 的預設值則是 64 MB。誠如所見，若指定了資料大小，則藉由「設定」這些參數來微調它們，讓我們能夠微調所使用的對應程式數目。 

5. 以下將提及最佳化 Hive 效能的其他數個更**進階選項**。這些選項讓您能夠設定配置的記憶體來對應和縮減工作，而且在調整效能時非常實用。請記住， `mapreduce.reduce.memory.mb` 不能大於 Hadoop 叢集中每個背景工作節點的實際記憶體大小。

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;

[1]: ./media/machine-learning-data-science-process-hive-tables/atan2new.png
[10]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png
[11]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png
[12]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-1.png
[13]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png
[14]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png
[15]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-3.png



<!--HONumber=49--> 