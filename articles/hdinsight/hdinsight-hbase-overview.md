<properties 
	pageTitle="什麼是 HDInsight 中的 HBase？ | Microsoft Azure" 
	description="HDInsight 中的 Apache HBase (建置在 Hadoop 的 NoSQL 資料庫) 簡介。了解使用案例，並將 HBase 與其他 Hadoop 叢集進行比較。" 
	keywords="bigtable,nosql,what is hbase"
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="jgao"/>



# 什麼是 HDInsight 中的 HBase：為 Hadoop 提供類似 BigTable 功能的 NoSQL 資料庫

Apache HBase 是開放原始碼的 NoSQL 資料庫，其建置於 Hadoop 上並模仿 Google BigTable。HBase 可針對依資料行系列組織的無結構描述資料庫中的大量非結構化及半結構化資料，提供隨機存取功能和強大一致性。

資料儲存在資料表的資料列中，而資料列中的資料會依據資料行系列進行分組。HBase 是無結構描述的資料庫，也就是說，在使用資料行之前，並不需要先定義資料行和其中儲存之資料的類型。開放原始碼的程式碼會以線性方式延展，以處理數千個節點上的 PB 資料。它可依賴散佈在 Hadoop 生態系統中的應用程式所提供的資料備援、批次處理及其他功能。

## Azure HDInsight 中的 HBase 是如何實作的？

HDInsight HBase 會以受管理叢集的形式提供，並整合到 Azure 環境中。叢集依設定會將資料直接儲存至 Azure Blob 儲存體中，使其在效能與成本的選擇中提供低延遲性與高度彈性。這可讓客戶建置使用大型資料集的互動式網站、建置可儲存數百萬端點上之感應器和遙測資料的服務，以及使用 Hadoop 工作分析此資料。HBase 和 Hadoop 是在 Azure 中處理巨量資料專案的好起點，尤其是它們讓即時應用程式能夠使用大型資料集。

HDInsight 實作運用 HBase 的向外延展架構，提供資料表自動分區功能、讀取和寫入的強大一致性，以及自動容錯移轉功能。透過在記憶體內部快取讀取和高輸送量的串流寫入，來提高效能。HDInsight HBase 也提供虛擬網路佈建功能。如需詳細資訊，請參閱[在 Azure 虛擬網路上佈建 HDInsight 叢集][hbase-provision-vnet] (英文)。


## 如何在 HDInsight HBase 中管理資料？

要管理 HBase 中的資料，可使用 HBase Shell 的 `create``get`, `put` 和 `scan` 命令。將資料寫入資料庫，需使用 `put`，讀取則使用 `get``scan` 命令可用來取得資料表中多個資料列裡的資料。您也可以使用 HBase C# API 管理資料，其在 HBase REST API 之上提供用戶端程式庫。HBase 資料庫也可使用 Hive 進行查詢。如需這些程式設計模型的簡介，請參閱[開始在 HDInsight 中搭配使用 HBase 與 Hadoop][hbase-get-started] (英文)。同時也提供共同處理器，其允許在主控資料庫的節點中進行資料處理。
 

## 情節：HBase 的使用案例
建立 BigTable (以及由此延伸出的 HBase) 的正式使用案例是 Web 搜尋。搜尋引擎會建置索引，以將字詞對應到包含這些字詞的網頁。除此之外，HBase 還有其他許多適用的使用案例，本節會列舉其中幾個。

- 索引鍵-值存放區

	HBase 可做為索引鍵-值存放區，也很適合用來管理訊息系統。Facebook 在訊息系統中使用 HBase，其用來儲存和管理網際網路通訊相當理想。WebTable 使用 HBase 來搜尋和管理從網頁擷取的資料表。

- 感應器資料

	HBase 適合用來擷取從多個來源收集累加的資料。這包括社交分析、時間序列、讓互動式儀表板保有最新的趨勢與計數器，以及管理稽核記錄系統。範例包括 Bloomberg 公司的股市資訊終端機以及 Open Time Series Database (OpenTSDB)，其可以儲存針對伺服器系統之健康情況所收集到的度量，並讓使用者加以存取。

- 即時查詢

	[Phoenix](http://phoenix.apache.org/) 是適用於 Apache HBase 的 SQL 查詢引擎。其以 JDBC 驅動程式的形式進行存取，並可使用 SQL 查詢和管理 HBase 資料表。

- HBase 即平台

	應用程式可以將 HBase 做為資料存放區，並在此基礎上執行。範例包括 Phoenix、OpenTSDB、Kiji 及 Titan。應用程式也可與 HBase 整合。範例包括 Hive、Pig、Solr、Storm、Flume、Impala、Spark、Ganglia 及 Drill。


##<a name="next-steps"></a>接續步驟

- [開始在 HDInsight 中搭配使用 HBase 與 Hadoop][hbase-get-started]
- [在 Azure 虛擬網路上佈建 HDInsight 叢集][hbase-provision-vnet]
- [在 HDInsight 中設定 HBase 複寫](hdinsight-hbase-geo-replication.md) 
- [使用 HDInsight 中的 HBase 分析 Twitter 情緒][hbase-twitter-sentiment]
- [使用 Maven 建置搭配使用 HBase 和 HDInsight (Hadoop) 的 Java 應用程式][hbase-build-java-maven]

##<a name="see-also"></a>另請參閱

- [Apache HBase](https://hbase.apache.org/)
- [Bigtable：結構化資料的分散式儲存體系統](http://research.google.com/archive/bigtable.html)




[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/










 

<!---HONumber=July15_HO2-->