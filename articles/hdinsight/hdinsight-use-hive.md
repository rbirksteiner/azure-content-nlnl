<properties
	pageTitle="了解什麼是 Hive 以及如何使用 HiveQL |Microsoft Azure"
	description="了解 Apache Hive 以及如何搭配 HDInsight 中的 Hadoop 使用它。選擇想要如何執行 Hive 工作，並使用 HiveQL 分析範例 Apache Log4j 檔案。" 
	keywords="hiveql,what is hive"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="04/23/2015"
	ms.author="larryfr"/>

# 搭配 HDInsight 中的 Hadoop 使用 Hive 和 HiveQL 來分析範例 Apache Log4j 檔案

[AZURE.INCLUDE [Hive 選取器](../../includes/hdinsight-selector-use-hive.md)]


在本教學課程中，您將了解如何在 HDInsight 中使用 Hadoop 內的 Apache Hive，以及選擇要如何執行 Hive 工作。您也將了解 HiveQL 以及如何分析範例 Apache Log4j 檔案。

##<a id="why"></a>什麼是 Hive，為什麼要使用它？ 
[Apache Hive](http://hive.apache.org/) 是適用於 Hadoop 的資料倉儲系統，可透過 HiveQL (與 SQL 類似的查詢語言) 執行資料摘要、查詢以及資料分析。Hive 可以互動方式用於探索資料，或者建立可重複使用的批次處理工作。

Hive 可讓您將結構投影在大量非結構化資料上。定義結構後，您不須具備 Jave 或 MapReduce 相關知識，即可使用 Hive 查詢該資料。**HiveQL** (Hive 查詢語言) 可讓您使用類似於 T-SQL 的陳述式撰寫查詢。

Hive 了解如何處理結構化和半結構化資料 (例如，以特定字元分隔欄位的文字檔)。Hive 也支援自訂複雜或不規則結構化資料的**序列化/反序列化程式 (SerDe)**。如需詳細資訊，請參閱[如何搭配 HDInsight 使用自訂 JSON SerDe](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx) (英文)。

Hive 也可透過**使用者定義函數 (UDF)** 延伸。UDF 可讓您在 HiveQL 中實作功能或不易模型化的邏輯。如需搭配 Hive 使用 UDF 的範例，請參閱以下內容：

* [在 HDInsight 中搭配 Hive 與 Pig 使用 Python](hdinsight-python.md)

* [在 HDInsight 中搭配 Hive 與 Pig 使用 C#](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [如何將自訂 Hive UDF 新增至 HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx) (英文)

##<a id="data"></a>關於範例資料 Apache Log4j 檔案

此範例使用 *log4j* 範例檔案，其儲存在 Blob 儲存容器中的 **/example/data/sample.log**。檔案中的每一筆記錄均由一列欄位組成，包括以 `[LOG LEVEL]` 欄位來顯示類型和嚴重性，例如：

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

在上一個範例中，記錄層級是「錯誤」。

> [AZURE.NOTE]您也可以使用 [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) 記錄工具產生 log4j 檔案，然後將該檔案上傳至 Blob 容器。如需指示，請參閱[將資料上傳至 HDInsight](hdinsight-upload-data.md)。如需關於如何搭配 HDInsight 使用 Azure Blob 儲存體的詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage.md)。

範例資料儲存在 Azure Blob 儲存體中，供 HDInsight 做為預設檔案系統使用。HDInsight 可使用 **wasb** 字首存取儲存在 Blob 中的檔案。例如，若要存取 sample.log 檔案，您應使用下列語法：

	wasb:///example/data/sample.log

因為 Azure Blob 儲存體是 HDInsight 的預設儲存體，所以您也可以從 HiveQL 中的 **/example/data/sample.log** 存取檔案。

> [AZURE.NOTE]語法 **wasb:///** 是用來存取 HDInsight 叢集的預設儲存容器所儲存的檔案。如果您在佈建叢集時指定其他儲存體帳戶，並想要存取儲存在這些帳戶上的檔案，您可以指定容器名稱和儲存體帳戶位址來存取資料，例如：**wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**。

##<a id="job"></a>範例工作：將資料行投影至帶分隔符號的資料上

下列 HiveQL 陳述式會將資料行投影在帶分隔符號的資料上，而資料儲存在 **wasb:///example/data** 目錄中：

	DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

在上一個範例中，HiveQL 陳述式會執行下列動作：

* **DROP TABLE**：刪除資料表和資料檔 (如果資料表已存在)。
* **CREATE EXTERNAL TABLE**：在 Hive 中建立新的**外部**資料表。外部資料表只會在 Hive 中儲存資料表定義；資料會以原始格式保留在原始位置。
* **ROW FORMAT**：告訴 Hive 如何格式化資料。在此情況下，每個記錄中的欄位會以空格隔開。
* **STORED AS TEXTFILE LOCATION**：告訴 Hive 資料的儲存位置 (example/data 目錄) 且資料儲存為文字。資料可以在目錄的一個檔案中，也可以分散在多個檔案中。
* **SELECT**：選擇其資料行 **t4** 包含值 **[ERROR]** 的所有資料列計數。這應該會傳回值 **3**，因為有三個資料列包含此值。

> [AZURE.NOTE]當您預期以外部來源更新基礎資料 (例如自動化資料上傳程序)，或以其他 MapReduce 作業更新基礎資料，並希望 Hive 查詢一律使用最新資料時，必須使用外部資料表。
>
> 捨棄外部資料表並「不會」****刪除資料，只會刪除資料表定義。

建立外部資料表後，下列的陳述式可用於建立**内部**資料表。

	CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
	STORED AS ORC;
	INSERT OVERWRITE TABLE errorLogs
	SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

這些陳述式會執行下列動作：

* **CREATE TABLE IF NOT EXISTS**：建立資料表 (如果資料表不存在)。因為未使用 **EXTERNAL** 關鍵字，所以這是內部資料表，而內部資料表儲存在 Hive 資料倉儲中，並完全透過 Hive 來管理。
* **STORED AS ORC**：以最佳化資料列單欄式 (ORC) 格式儲存資料。這是高度最佳化且有效率的 Hive 資料儲存格式。
* **INSERT OVERWRITE ...SELECT**：從包含 **[ERROR]** 的 **log4jLogs** 資料表選取資料列，然後將資料插入 **errorLogs** 資料表。

> [AZURE.NOTE]與外部資料表不同之處在於，捨棄內部資料表也會刪除基礎資料。

##<a id="usetez"></a>使用 Apache Tez 以提升效能

[Apache Tez](http://tez.apache.org) 是可讓資料高用量應用程式 (例如 Hive)，以大規模而更有效率方式執行作業的架構。在最新版的 HDInsight 中，Hive 已支援在 Tez 上執行。此功能目前預設為關閉，而必須啟用。若要充分發揮 Tez 的效益，您必須設定 Hive 查詢的下列值：

	set hive.execution.engine=tez;

此值可就個別查詢逐一提交，只要將值放在查詢開頭處即可。您也可以在建立叢集時設定組態值，在叢集上將此值預設為開啟。您可以在[佈建 HDInsight 叢集](hdinsight-provision-clusters.md)中找到詳細資訊。

[Tez 上的 Hive 設計文件](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez)包含實作選擇和調整組態的一些詳細資料。


##<a id="run"></a>選擇如何執行 HiveQL 工作

HDInsight 可以使用各種方法執行 Hive QL 工作。請使用下表決定適合您的方法，然後跟著連結逐項閱讀介紹。

| 如果您想要...，請**使用此方法** | ...一個**互動式**殼層 | ...**批次**處理 | ...搭配此**叢集作業系統** | ...從此**用戶端作業系統** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-hive-ssh.md) | ✔ | ✔ | Linux | Linux、Unix、Mac OS X 或 Windows |
| [Curl](hdinsight-hadoop-use-hive-curl.md) | &nbsp; | ✔ | Linux 或 Windows | Linux、Unix、Mac OS X 或 Windows |
| [查詢主控台](hdinsight-hadoop-use-hive-query-console.md) | &nbsp; | ✔ | Windows | 瀏覽器型 |
| [HDInsight Tools for Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) | &nbsp; | ✔ | Linux 或 Windows | Windows |
| [.NET SDK for Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) | &nbsp; | ✔ | Linux 或 Windows | Windows (目前) |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) | &nbsp; | ✔ | Linux 或 Windows | Windows |
| [遠端桌面](hdinsight-hadoop-use-hive-remote-desktop.md) | ✔ | ✔ | Windows | Windows |

##<a id="nextsteps"></a>接續步驟

現在您已了解什麼是 Hive 以及如何搭配 HDInsight 中的 Hadoop 使用它，接著請使用下列連結探索 Azure HDInsight 的其他使用方式。

* [將資料上傳至 HDInsight][hdinsight-upload-data]
* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]
* [搭配 HDInsight 使用 MapReduce 工作][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: ../hdinsight-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[Powershell-install-configure]: ../install-configure-powershell.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=54--> 