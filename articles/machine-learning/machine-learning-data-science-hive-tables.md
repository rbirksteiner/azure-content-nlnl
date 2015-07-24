<properties 
	pageTitle="建立並將資料從 Azure Blob 儲存體載入 Hive 資料表 | Azure" 
	description="建立 Hive 資料表，並將 Blob 中的資料載入 Hive 資料表" 
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
	ms.date="03/16/2015" 
	ms.author="hangzh;bradsev" />

 
# 建立並將資料從 Azure Blob 儲存體載入 Hive 資料表
 
本文件會顯示泛型 Hive 查詢，這類查詢會建立 Hive 資料表，並從 Azure Blob 儲存體載入資料。在 GitHub 儲存機制中共用這些 Hive 查詢。[Github 儲存機制](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql)。 

如果您依照＜使用 IPython Notebook 伺服器設定 Azure 虛擬機器＞中的指示執行，就已經將這個指令碼檔案下載至虛擬機器上的 `C:\Users<user name>\Documents\Data Science Scripts` 目錄中。您需要在這些查詢的對應欄位中插入自己的資料結構描述和 Azure Blob 儲存體設定，而這些 Hive 查詢應已準備好可進行提交。 

我們假設 Hive 資料表的資料為**未壓縮**的表格格式，而且資料已上傳至 Hadoop 叢集所使用的儲存體帳戶的預設或其他容器。如果使用者想要練習_「NYC 計程車車程資料」_，就需要先[下載全部的 24 個檔案](http://www.andresmh.com/nyctaxitrips/) (12 個車程檔和 12 個費用檔)、將所有檔案**解壓縮**至 .csv 檔案，然後將它們上傳至在[自訂 Azure HDInsight Hadoop 叢集](machine-learning-data-science-customize-hadoop-cluster.html)時所使用的 Azure 儲存體帳戶的預設或其他容器。 

Hive 查詢可以在 Hadoop 叢集前端節點上的 Hadoop 命令列中提交。您必須：

1. [啟用 Hadoop 叢集前端節點的遠端存取並登入前端節點](machine-learning-data-science-customize-hadoop-cluster.md)。
2. [在前端節點的 Hadoop 命令列中提交 Hive 查詢](machine-learning-data-science-hive-queries.md)。

使用者也可以在 Web 瀏覽器中輸入 URL `https://<Hadoop 叢集名稱>.azurehdinsight.net/Home/HiveEditor 來使用[查詢主控台 (Hive 編輯器)] (系統將要求您輸入 Hadoop 叢集認證以進行登入)，或者可以[使用 PowerShell 提交 Hive 工作](hdinsight-submit-hadoop-jobs-programmatically.md)。 

- [步驟 1：建立 Hive 資料庫和資料表](#create-tables)
- [步驟 2：將資料載入 Hive 資料表](#load-data)
- [進階主題：資料分割資料表並以 ORC 格式儲存 Hive 資料 ](#partition-orc)

## <a name="create-tables"></a>建立 Hive 資料庫和資料表

    create database if not exists <database name>;
	CREATE EXTERNAL TABLE if not exists <database name>.<table name>
	(
		field1 string, 
		field2 int, 
		field3 float, 
		field4 double, 
		...,
		fieldN string
	) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
	STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

以下是使用者需要插入的欄位和其他設定的說明：

- `<database name>`：使用者要建立的資料庫名稱。如果使用者只想要使用預設資料庫，則可省略 `create database...` 這個查詢。 
- `<table name>`：使用者想要在指定資料庫內建立的資料表名稱。如果使用者想要使用預設資料庫，可透過 `<table name>` 直接參考資料表，而不需使用 `<database name>`
- `<field separator>`：要上傳至 Hive 資料表的資料檔中個別欄位的分隔符號。 
- `<line separator>`：用來分隔資料檔中的行的分隔符號。 
- `<storage location>`：用以儲存 Hive 資料表資料的 Azure 儲存體位置。如果使用者未指定 `LOCATION '<storage location>'`，則資料庫和資料表預設會儲存在 Hive 叢集預設容器的 `hive/warehouse/` 目錄中。如果使用者想要指定儲存體位置，  儲存體位置必須位於資料庫和資料表的預設容器內。這個位置必須使用 `'wasb:///<directory 1>/'` 或 `'wasb:///<directory 1>/<directory 2>/'` 依此類推的格式，以相對位置的形式來參考叢集的預設容器。執行查詢之後，將會在預設容器內建立相對目錄。 
- `TBLPROPERTIES("skip.header.line.count"="1")`：如果資料檔具有標頭行，使用者就必須在 `create table` 查詢的**結尾處**新增這個屬性。否則，載入的標頭行將做為資料表的記錄。如果資料檔不含標頭行，則可在查詢中省略此設定。 

## <a name="load-data"></a>將資料載入 Hive 資料表

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- `<path to blob data>`：如果要上傳至 Hive 資料表的 Blob 檔案是在 HDInsight Hadoop 叢集的預設容器中，`<path to blob data>` 格式應該是 `'wasb:///<directory in this container>/<blob file name>'`。Blob 檔案也可以位於 HDInsight Hadoop 叢集的其他容器中。在此情況下，`<path to blob data>` 格式應該是 `'wasb://<container name>@<storage account name>.blob.windows.core.net/<blob file name>'`。

>[AZURE.NOTE] 上傳至 Hive 資料表的 Blob 資料必須位於 Hadoop 叢集儲存體帳戶的預設或其他容器中。否則， `LOAD DATa` 查詢將會失敗並提報它無法存取資料。 


## <a name="partition-orc"></a>進階主題：資料分割資料表並以 ORC 格式儲存 Hive 資料 

如果資料量很大，對於只需掃描資料表中數個資料分割的查詢而言，分割資料表就很有助益。例如，  依日期分割網站的記錄資料就很合理。 

除了分割資料表之外，對於使用 ORC 格式儲存 Hive 資料也很有助益。ORC 代表「已最佳化的單欄式資料列」。  如需更多詳細資訊，請參閱_＜[在 Hive 讀取、寫入及處理資料時使用 ORC 檔案提升效能](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles)＞_。

### 資料分割資料表

建立資料分割資料表並將資料載入其中的查詢如下：

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

查詢資料分割資料表時，建議在 `where` 子句**開頭**新增資料分割條件，如此便能大幅提升搜尋效率。 

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>使用 ORC 格式儲存 Hive 資料：

使用者無法將 Blob 中的資料直接載入 ORC 儲存體格式的 Hive 資料表。以下是使用者為了將資料從 Azure Blob 載入以 ORC 格式儲存的 Hive 資料表所需採取的步驟。 

1. 建立外部資料表 **STORED AS TEXTFILE**，並將資料從 Blob 儲存體載入該資料表。

		CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
		(
			field1 string,
			field2 int,
			...
			fieldN date
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
			lines terminated by '<line separator>' STORED AS TEXTFILE 
			LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

		LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

2. 建立與步驟 1 中的外部資料表具有相同結構描述和相同欄位分隔符號的內部資料表，然後使用 ORC 格式儲存 Hive 資料。

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. 從步驟 1 中的外部資料表選取資料，並插入 ORC 資料表

		INSERT OVERWRITE TABLE <database name>.<ORC table name> SELECT * FROM <database name>.<external textfile table name>;

[AZURE.NOTE] 如果 TEXTFILE 資料表 `<database name>.<external textfile table name>` 具有資料分割，則在步驟 3 中， `SELECT * FROM <database name>.<external textfile table name>` 將會選取資料分割變數做為所傳回資料集中的欄位。將它插入 `<database name>.<ORC table name>` 將會失敗，因為 `<database name>.<ORC table name>` 沒有資料分割參數可做為資料表結構描述中的欄位。在此情況下，使用者需要明確選取要插入 `<database name>.<ORC table name>` 的欄位，如下所示：

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		      SELECT field1, field2, ..., fieldN
		      FROM <database name>.<external textfile table name> 
		      WHERE <partition variable>=<partition value>;

4. 在將所有資料插入 `<database name>.<ORC table name>` 之後，使用下列查詢時，即會安全卸除 `<external textfile table name>`：

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

現在會有一個含 ORC 格式之資料的資料表可供使用。 

<!--HONumber=49--> 