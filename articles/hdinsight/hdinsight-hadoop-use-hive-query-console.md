<properties
   pageTitle="在 HDInsight 中的查詢主控台上使用 Hadoop Hive | Microsoft Azure"
   description="學習如何使用 Web 型查詢主控台，透過瀏覽器在 HDInsight Hadoop 叢集上執行 Hive 查詢。"
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

# 使用查詢主控台執行 Hive 查詢

[AZURE.INCLUDE [Hive 選取器](../../includes/hdinsight-selector-use-hive.md)]

在本文中，您將學習如何使用 HDInsight 查詢主控台，透過瀏覽器在 HDInsight Hadoop 叢集上執行 Hive 查詢。

> [AZURE.NOTE]只有在 Windows 型 HDInsight 叢集上才能使用查詢主控台。

##<a id="prereq"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目。

* Windows 型 HDInsight Hadoop 叢集

* 現代網頁瀏覽器

##<a id="run"></a>使用查詢主控台執行 Hive 查詢

1. 開啟 [Azure 入口網站](https://manage.windowsazure.com)，然後選取您的 HDInsight 叢集。選取頁面底部的 [**查詢主控台**]。出現提示時，輸入您建立叢集時所輸入的使用者名稱和密碼。

    > [AZURE.NOTE]在瀏覽器中輸入 **https://CLUSTERNAME.azurehdinsight.net**，也可以到達查詢主控台。

2. 在頁面頂端的連結中，選取 [**Hive 編輯器**]。這會顯示一個表單，而此表單可用來輸入您要在 HDInsight 叢集中執行的 HiveQL 陳述式。

	![Hive 編輯器](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)

	將文字 `Select * from hivesampletable` 取代為下列 HiveQL 陳述式：

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    這些陳述式將執行下列動作：

    * **DROP TABLE**：刪除資料表和資料檔 (如果資料表已存在)。
    * **CREATE EXTERNAL TABLE**：在 Hive 中建立新的「外部」資料表。外部資料表只會在 Hive 中儲存資料表定義；資料會保留在原始位置。

    > [AZURE.NOTE]當您預期以外部來源更新基礎資料 (例如自動化資料上傳程序)，或以其他 MapReduce 作業更新基礎資料，但希望 Hive 查詢一律使用最新資料時，必須使用外部資料表。
    >
    > 捨棄外部資料表並**不會**刪除資料，只會刪除資料表定義。

    * **ROW FORMAT**：告訴 Hive 如何格式化資料。在此情況下，每個記錄中的欄位會以空格隔開。
    * **STORED AS TEXTFILE LOCATION**：將資料的儲存位置告訴 Hive (example/data 目錄)，且資料儲存為文字
    * **SELECT**：選擇其資料欄 **t4** 包含值 **[ERROR]** 的所有資料列計數。這應該會傳回值 **3**，因為有三個資料列包含此值。

2. 按一下 [提交]。頁面底部的 [**作業工作階段**] 應該會顯示工作的詳細資料。

3. 當 [**狀態**] 欄位變更為 [**已完成**] 時，請選取工作的 [**檢視詳細資料**]。在詳細資料頁面上，[**工作輸出**] 包含 `[ERROR]	3`。您可以使用此欄位下方的 [**下載**] 按鈕，下載含有工作輸出的檔案。


##<a id="summary"></a>摘要

如您所見，[查詢主控台] 提供簡單的方法，在 HDInsight 叢集中執行 Hive 查詢、監視工作狀態，以及擷取輸出。

若要深入了解如何使用 Hive 查詢主控台執行 Hive 工作，請選取 [查詢主控台] 頂端的 [**入門**]，然後使用提供的範例。每個範例都會逐步進行使用 Hive 分析資料的程序 (包括範例中所用之 HiveQL 陳述式的說明)。

##<a id="nextsteps"></a>接續步驟

如需 HDInsight 中 Hive 的一般資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

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


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: install-configure-powershell.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!---HONumber=July15_HO2-->