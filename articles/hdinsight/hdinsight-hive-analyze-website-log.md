<properties 
	pageTitle="使用 Hive 和 Hadoop 進行網站記錄分析 | Microsoft Azure" 
	description="了解如何使用 HDInsight 上的 Hive 來分析網站記錄。您將使用記錄檔做為 HDInsight 資料表的輸入，然後使用 HiveQL 來查詢資料。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="nitinme"/>

# 使用 HDInsight 上的 Hive 分析網站的記錄

了解如何使用 HDInsight 上的 HiveQL 來分析網站的記錄。網站記錄分析可用於根據類似活動來區隔對象、依人口統計將網站造訪者分類，以及找出他們檢視的內容、他們來自的網站等。

在此範例中，您將使用 HDInsight 叢集來分析網站記錄檔，以深入了解一天內從外部網站的網站造訪頻率。也將產生使用者遇到的網站錯誤摘要。您將了解如何：

- 連接至包含網站記錄檔的 Azure Blob 儲存體。
- 建立 Hive 資料表以查詢這些記錄。
- 建立 Hive 查詢以分析資料。
- 使用 Microsoft Excel 連接到 HDInsight (使用開放式資料庫連接，ODBC) 擷取已分析的資料。

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##必要條件

- 您必須已佈建 **HDInsight 叢集**。如需指示，請參閱＜[佈建 HDInsight 叢集][hdinsight-provision]＞。 
- 您必須已安裝 Microsoft Excel 2013 或 Excel 2010。
- 您必須有 [Microsoft Hive ODBC 驅動程式](http://www.microsoft.com/download/details.aspx?id=40886)，才能從 Hive 將資料匯入 Excel 中。


##執行範例

1. 從 Azure 入口網站，按一下您要執行範例的叢集，然後按一下底部的 [查詢主控台]。或者，您也可以使用下列 URL 直接開啟查詢主控台：

	 	https://<clustername>.azurehdinsight.net
	
	出現提示時，使用您佈建叢集時所用的系統管理員使用者名稱和密碼來進行驗證。
  
2. 從開啟的網頁中，按一下 [Getting Started Gallery] 索引標籤，然後在 [範例] 類別下，按一下 [Website Log Analysis] 範例。

3. 依照網頁上提供的指示完成範例。

##後續步驟
嘗試下列範例：[使用 Hive 和 HDInsight 分析感應器資料](hdinsight-hive-analyze-sensor-data.md)。


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 

<!---HONumber=July15_HO2-->