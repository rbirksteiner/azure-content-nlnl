<properties
   pageTitle="在 HDInsight 中搭配使用 Hadoop Pig 與 Curl | Microsoft Azure"
   description="了解如何使用 Curl 從遠端提交 Pig 工作到 HDInsight。"
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

#使用 Curl 在 HDInsight 中以 Hadoop 執行 Hive 查詢

[AZURE.INCLUDE [Hive 選取器](../../includes/hdinsight-selector-use-hive.md)]

在本文件中，您將學習如何使用 Curl 在 Azure HDInsight 叢集的 Hadoop 上執行 Hive 查詢。

本文件使用 Curl 示範如何使用未經處理的 HTTP 要求來與 HDInsight 互動，以便執行、監視和擷取 Hive 查詢的結果。要想執行這些作業，就要使用 HDInsight 叢集所提供的 WebHCat REST API (先前稱為 Templeton)。

> [AZURE.NOTE]如果您已熟悉使用以 Linux 為基礎的 Hadoop 伺服器，但剛接觸 HDInsight，請參閱[在以 Linux 為基礎的 HDInsight 上安裝 Hadoop 的須知事項](hdinsight-hadoop-linux-information.md)。

##<a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目：

* HDInsight 叢集上的 Hadoop (Linux 或 Windows 型)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>使用 Curl 執行 Hive 查詢

> [AZURE.NOTE]在使用 Curl 或與 WebHCat 進行任何其他 REST 通訊時，您必須提供 HDInsight 叢集系統管理員的使用者名稱和密碼來驗證要求。您也必須在用來將要求傳送至伺服器的統一資源識別項 (URI) 中使用叢集名稱。
>
> 在本節的所有命令中，將 **USERNAME** 取代為用來驗證叢集的使用者，並將 **PASSWORD** 取代為使用者帳戶的密碼。將 **CLUSTERNAME** 取代為您叢集的名稱。
>
> 透過[基本驗證](http://en.wikipedia.org/wiki/Basic_access_authentication)來保護 REST API 的安全。您應該一律使用安全 HTTP (HTTPS) 提出要求，確保認證安全地傳送至伺服器。

1. 從命令列中，使用下列命令來確認您可以連線到 HDInsight 叢集：

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    您應該會收到如下所示的回應：

        {"status":"ok","version":"v1"}

    此命令中使用的參數如下：

    * **-u** - 用來驗證要求的使用者名稱和密碼。
    * **-G** - 指出這是 GET 要求。

    所有要求的 URL 開頭 (**https://CLUSTERNAME.azurehdinsight.net/templeton/v1**) 都會相同。路徑 **/status** 指出要求是要傳回伺服器之 WebHCat (也稱為 Templeton) 的狀態。您也可以使用下列命令要求 Hive 的版本：

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    這應該會傳回類似下列的回應：

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. 使用下列命令建立新資料表 **log4jLogs**：

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasb:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+GROUP+BY+t4;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    此命令中使用的參數如下：

    * **-d** - 因為未使用 `-G`，要求會依預設使用 POST 方法。`-d` 可指定與要求一起傳送的資料值。

        * **user.name** - 執行命令的使用者。

        * **execute** - 要執行的 HiveQL 陳述式。

        * **statusdir** - 要寫入此工作狀態的目錄。

    這些陳述式將執行下列動作：

    * **DROP TABLE** - 刪除資料表和資料檔 (如果資料表已存在)。

    * **CREATE EXTERNAL TABLE** - 在 Hive 中建立新的「外部」資料表。外部資料表只會將資料表定義儲存在 Hive 中。資料會留在原來的位置。

		> [AZURE.NOTE]當您預期以外部來源更新基礎資料 (例如自動化資料上傳程序)，或以其他 MapReduce 作業更新基礎資料，但希望 Hive 查詢一律使用最新資料時，必須使用外部資料表。
		>
		> 捨棄外部資料表並**不會**刪除資料，只會刪除資料表定義。

    * **ROW FORMAT** - 告訴 Hive 如何格式化資料。在此情況下，每個記錄中的欄位會以空格隔開。

    * **STORED AS TEXTFILE LOCATION** - 將資料的儲存位置告訴 Hive (example/data 目錄)，且資料儲存為文字。

    * **SELECT** - 選取其資料行 **t4** 包含 **[ERROR]** 值的所有資料列計數。這應該會傳回值 **3**，因為有 3 個資料列包含此值。

    > [AZURE.NOTE]請注意，在搭配 Curl 使用時，會以 `+` 字元取代 HiveQL 陳述式之間的空格。加上引號的值若包含空格，例如分隔符號，則不應以 `+` 取代。

    此命令應該會傳回可用來檢查工作狀態的工作識別碼。

        {"id":"job_1415651640909_0026"}

3. 若要檢查工作的狀態，請使用下列命令。將 **JOBID** 取代為上一個步驟中所傳回的值。例如，如果傳回值為 `{"id":"job_1415651640909_0026"}`，則 **JOBID** 會是 `job_1415651640909_0026`。

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	如果工作已完成，則狀態會是 [**成功**]。

    > [AZURE.NOTE]此 Curl 要求會傳回含有工作資訊的 JavaScript Object Notation (JSON) 文件；jq 可用來僅擷取狀態值。

4. 工作狀態變更為 [**成功**] 之後，即可從 Azure Blob 儲存體擷取工作結果。隨查詢一起傳送的 `statusdir` 參數包含輸出檔案的位置；在此案例中為 **wasb:///example/curl**。此位址會將工作輸出儲存至 HDInsight 叢集所使用之預設儲存體容器的 **example/curl** 目錄中。

    您可以使用[適用於 Mac、Linux 和 Windows 的 Azure CLI](xplat-cli.md) 來列出並下載這些檔案。例如，若要列出 **example/curl** 中的檔案，請使用下列命令：

		azure storage blob list <container-name> example/curl

	若要下載檔案，請使用下列命令：

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE]您必須使用 `-a` 和 `-k` 參數指定包含 Blob 的儲存體帳戶名稱，或是設定 **AZURE_STORAGE_ACCOUNT** 和 **AZURE_STORAGE_ACCESS_KEY** 環境變數。如需詳細資訊，請參閱 <a href="hdinsight-upload-data.md" target="_blank"。

6. 使用下列陳述式來建立名為 **errorLogs** 的新「內部」資料表：

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]';SELECT+*+from+errorLogs;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    這些陳述式會執行下列動作：

    * **CREATE TABLE IF NOT EXISTS** - 建立資料表 (如果不存在)。因為未使用 **EXTERNAL** 關鍵字，所以這是內部資料表，而內部資料表儲存在 Hive 資料倉儲中，並完全透過 Hive 所管理。

		> [AZURE.NOTE]與外部資料表不同，捨棄內部資料表也會同時刪除基礎資料。

    * **STORED AS ORC** - 以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。這是高度最佳化且有效率的 Hive 資料儲存格式。
    * **INSERT OVERWRITE ...SELECT**：從包含 **[ERROR]** 的 **log4jLogs** 資料表選取資料列，然後將資料插入 **errorLogs** 資料表。
    * **SELECT** - 從新的 **errorLogs** 資料表選取所有資料列。

7. 使用傳回的工作識別碼檢查工作的狀態。成功後，如先前所述使用適用於 Mac、Linux 和 Windows 的 Azure CLI 下載並檢視結果。輸出應包含三行，其中都包含 **[ERROR]**。


##<a id="summary"></a>摘要

如這份文件所示，您可以使用原始 HTTP 要求來執行、監視和檢視 HDInsight 叢集上的 Hive 工作結果。

如需本文中使用的 REST 介面的詳細資訊，請參閱 <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">WebHCat 參照</a>。

##<a id="nextsteps"></a>接續步驟

Hive 與 HDInsight 搭配使用的一般資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)


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

<!---HONumber=July15_HO2-->