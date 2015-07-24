<properties
   pageTitle="使用適用於 Visual Studio 的 Hadoop 工具執行 HIVE 查詢 | Microsoft Azure"
   description="了解如何利用 Visual Studio Hadoop 工具搭配使用 Hive 與 HDInsight 中的 Hadoop。"
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

#使用 HDInsight Tools for Visual Studio 執行 Hive 查詢

[AZURE.INCLUDE [Hive 選取器](../../includes/hdinsight-selector-use-hive.md)]

在本文中，您將學習如何使用 HDInsight Tools for Visual Studio，將 Hive 查詢提交至 HDInsight 叢集。

> [AZURE.NOTE]本文件不提供範例中使用的 HiveQL 陳述式所執行的工作詳細的描述。如需此範例中使用的 HiveQL 的相關資訊，請參閱[在 HDInsight 上搭配 Hadoop 使用 Hive](hdinsight-use-hive.md)。

##<a id="prereq"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目。

* Azure HDInsight (HDInsight 上的 Hadoop) 叢集 (Linux 或 Windows 型)

* Visual Studio 2012 [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)、Visual Studio 2013 [Update 3](http://go.microsoft.com/fwlink/?LinkId=390465) 或 [Visual Studio Express 2013](http://www.microsoft.com/download/details.aspx?id=40769)

##<a id="run"></a>使用 HDInsight Tools for Visual Studio 執行 Hive 查詢

1. 開啟 **Visual Studio**，然後選取 [**新增**] > [**專案**] > [**HDInsight**] > [**Hive 應用程式**]。提供此專案的名稱。

2. 開啟使用此專案所建立的 **Script.hql** 檔案，並貼入下列 HiveQL 陳述式中：

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    這些陳述式會執行下列動作：

    * **DROP TABLE**：刪除資料表和資料檔 (如果資料表已存在)。
    * **CREATE EXTERNAL TABLE**：在 Hive 中建立新的「外部」資料表。外部資料表只會在 Hive 中儲存資料表定義 (資料會保留在原始位置)。

        > [AZURE.NOTE]當您預期以外部來源更新基礎資料 (例如自動化資料上傳程序)，或以其他 MapReduce 作業更新基礎資料，但希望 Hive 查詢一律使用最新資料時，必須使用外部資料表。
        >
        > 捨棄外部資料表並**不會**刪除資料，只會刪除資料表定義。

    * **ROW FORMAT**：告訴 Hive 如何格式化資料。在此情況下，每個記錄中的欄位會以空格隔開。
    * **STORED AS TEXTFILE LOCATION**：告訴 Hive 資料的儲存位置 (example/data 目錄) 且資料儲存為文字。
    * **SELECT**：選擇其資料欄 **t4** 包含值 **[ERROR]** 的所有資料列計數。這應該會傳回值 **3**，因為有三個資料列包含此值。

3. 從工具列中，選取您要用於此查詢的 [**HDInsight 叢集**]，然後選取 [**提交**] 以 Hive 工作形式執行陳述式。[**Hive 工作摘要**] 將會出現並顯示執行中工作的相關資訊。使用 [**￼重新整理**] 連結來重新整理工作資訊，直到 [**工作狀態**] 變更為 [**已完成**] 為止。

4. 使用 [**工作輸出**] 連結檢視此工作的輸出。它應該會顯示 `[ERROR] 3`，這是 SELECT 陳述式所傳回的值。

5. 您也可以執行 Hive 查詢，而不需建立專案。使用 [**伺服器總管**]，展開 [**Azure**] > [**HDInsight**]，在 HDInsight 伺服器上按一下滑鼠右鍵，然後選取 [**撰寫 Hive 查詢**]。

6. 在出現的 **temp.hql** 文件，新增下列 HiveQL 陳述式：

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    這些陳述式會執行下列動作：

    * **CREATE TABLE IF NOT EXISTS**：建立資料表 (如果不存在)。因為未使用 **EXTERNAL** 關鍵字，所以這是內部資料表，而內部資料表儲存在 Hive 資料倉儲中，並完全透過 Hive 來管理。

        > [AZURE.NOTE]與 **EXTERNAL** 資料表不同之處在於，捨棄內部資料表也會刪除基礎資料。

    * **STORED AS ORC**：以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。這是高度最佳化且有效率的 Hive 資料儲存格式。
    * **INSERT OVERWRITE ...SELECT**：從包含 **[ERROR]** 的 **log4jLogs** 資料表選取資料列，然後將資料插入 **errorLogs** 資料表

7. 從工具列中，選取 [**提交**] 來執行工作。使用 [**工作狀態**] 來判斷工作是否已順利完成。

8. 若要確認工作已完成並已建立新的資料表，請使用 [**伺服器總管**] 並展開 [**Azure**] > [**HDInsight**] > 您的 HDInsight 叢集 > [**Hive 資料庫**] > [**預設**]。您應該會看到 **errorLogs** 資料表和 **log4jLogs** 資料表。

##<a id="summary"></a>摘要

如您所見，HDInsight Tools for Visual Studio 提供簡單的方法，在 HDInsight 叢集上執行 Hive 查詢、監視工作狀態，以及擷取輸出。

##<a id="nextsteps"></a>接續步驟

如需 HDInsight 中 Hive 的一般資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

如需 HDInsight Tools for Visual Studio 的詳細資訊：

* [開始使用 HDInsight Tools for Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)


[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!---HONumber=July15_HO2-->