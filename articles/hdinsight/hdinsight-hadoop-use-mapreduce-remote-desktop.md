<properties
   pageTitle="MapReduce 和遠端桌面與 HDInsight 中的 Hadoop | Microsoft Azure"
   description="了解如何使用遠端桌面連線到 HDInsight 上的 Hadoop，並執行 MapReduce 工作。"
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
   ms.date="07/06/2015"
   ms.author="larryfr"/>

# 利用遠端桌面在 HDInsight 上的 Hadoop 中使用 MapReduce

[AZURE.INCLUDE [MapReduce 選取器](../../includes/hdinsight-selector-use-mapreduce.md)]

在本文中，您將學習如何使用遠端桌面連線至 HDInsight 叢集上的 Hadoop，然後使用 Hadoop 命令執行 MapReduce 工作。

##<a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目：

* Windows 型 HDInsight (HDInsight 上的 Hadoop) 叢集

* 執行 Windows 10、Windows 8 或 Windows 7 的用戶端電腦

##<a id="connect"></a>使用遠端桌面連線

依照[使用 RDP 連線到 HDInsight 叢集](hdinsight-administer-use-management-portal.md#rdp)中的指示，為 HDInsight 叢集啟用遠端桌面，然後進行連線。

##<a id="hadoop"></a>使用 Hadoop 命令

當您連線到 HDInsight 叢集的桌面時，請使用下列步驟，利用 Hadoop 命令來執行 MapReduce 工作：

1. 從 HDInsight 桌面，啟動 **Hadoop 命令列**。這會在 **c:\\apps\\dist\\hadoop-&lt;version number>** 目錄中開啟新的命令提示字元。

	> [AZURE.NOTE]版本號碼會隨著 Hadoop 更新而變更。**HADOOP_HOME** 環境變數可用來尋找路徑。例如，`cd %HADOOP_HOME%` 會將目錄變更為 Hadoop 目錄，而您並不需要知道版本號碼。

2. 若要使用 **Hadoop** 命令執行範例 MapReduce 工作，請使用下列命令：

		hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	這樣會啟動 **wordcount** 類別 (內含於目前目錄的 **hadoop-mapreduce-examples.jar** 檔案中)。它使用 **wasb://example/data/gutenberg/davinci.txt** 文件作為輸入，輸出則儲存在：**wasb:///example/data/WordCountOutput**。

	> [AZURE.NOTE]如需有關此 MapReduce 工作和範例資料的詳細資訊，請參閱<a href="hdinsight-use-mapreduce.md">在 HDInsight Hadoop 上使用 MapReduce</a>。

2. 工作會在處理時發出詳細資料，並於工作完成時傳回與下列類似的資訊：

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. 工作完成時，使用下列命令列出儲存在 **wasb://example/data/WordCountOutput** 的輸出檔案：

		hadoop fs -ls wasb:///example/data/WordCountOutput

	這應該會顯示兩個檔案：**_SUCCESS** 和 **part-r-00000**。**part-r-00000** 檔案包含這項工作的輸出。

	> [AZURE.NOTE]某些 MapReduce 工作可能會將結果分成多個 **part-r-#####** 檔案。若是如此，請使用 ##### 尾碼指出檔案的順序。

4. 若要檢視輸出，請使用下列命令：

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	這會顯示 **wasb://example/data/gutenberg/davinci.txt** 檔案中所含的單字清單，以及每個單字的出現次數。以下是要包含在檔案中之資料的範例：

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>摘要

如您所見，Hadoop 命令提供簡單的方法，在 HDInsight 叢集上執行 MapReduce 工作，然後檢視工作輸出。

##<a id="nextsteps"></a>接續步驟

如需 HDInsight 中 MapReduce 工作的一般資訊：

* [在 HDInsight Hadoop 上使用 MapReduce](hdinsight-use-mapreduce.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

<!---HONumber=July15_HO2-->