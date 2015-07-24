<properties
   pageTitle="在 HDInsight 中搭配使用 Hadoop Pig 與 Curl | Microsoft Azure"
   description="學習如何使用 Curl 在 Azure HDInsight 中的 Hadoop 叢集上執行 Pig Latin 工作。"
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

#使用 Curl 搭配執行 Pig 工作與 HDInsight 上的 Hadoop

[AZURE.INCLUDE [Pig 選取器](../../includes/hdinsight-selector-use-pig.md)]

在本文件中，您將學習如何使用 Curl 在 Azure HDInsight 叢集上執行 Pig Latin 工作。Pig Latin 程式設計語言可讓您描述套用至輸入資料來產生想要輸出的轉換。

本文件使用 Curl 示範如何使用未經處理的 HTTP 要求來與 HDInsight 互動，以便執行、監視和擷取 Pig 工作的結果。要想執行這些作業，就要使用 HDInsight 叢集所提供的 WebHCat REST API (先前稱為 Templeton)。

> [AZURE.NOTE]如果您已熟悉使用以 Linux 為基礎的 Hadoop 伺服器，但剛接觸 HDInsight，請參閱[以 Linux 為基礎的 HDInsight 秘訣](hdinsight-hadoop-linux-information.md)。

##<a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目：

* Azure HDInsight (HDInsight 上的 Hadoop) 叢集 (Linux 型或 Windows 型)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>使用 Curl 執行 Pig 工作

> [AZURE.NOTE]在使用 Curl 或與 WebHCat 進行任何其他 REST 通訊時，您必須提供 HDInsight 叢集的系統管理員使用者名稱和密碼來驗證要求。您也必須在用來將要求傳送至伺服器的統一資源識別項 (URI) 中使用叢集名稱。
>
> 在本節的所有命令中，將 **USERNAME** 取代為用來驗證叢集的使用者，並將 **PASSWORD** 取代為使用者帳戶的密碼。將 **CLUSTERNAME** 取代為您叢集的名稱。
>
> 透過[基本存取驗證](http://en.wikipedia.org/wiki/Basic_access_authentication)來保護 REST API 的安全。您應該一律使用安全 HTTP (HTTPS) 提出要求，確保認證安全地傳送至伺服器。

1. 從命令列中，使用下列命令來確認您可以連線到 HDInsight 叢集：

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    您應該會收到如下所示的回應：

        {"status":"ok","version":"v1"}

    此命令中使用的參數如下：

    * **-u**：用來驗證要求的使用者名稱和密碼
    * **-G**：指出這是 GET 要求

    所有要求的 URL 開頭 (**https://CLUSTERNAME.azurehdinsight.net/templeton/v1**) 都會相同。路徑 **/status** 指出要求是要傳回伺服器之 WebHCat (也稱為 Templeton) 的狀態。

2. 使用下列程式碼，以將 Pig Latin 工作提交至叢集：

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasb:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasb:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    此命令中使用的參數如下：

    * **-d**：因為未使用 `-G`，要求會依預設使用 POST 方法。`-d` 可指定與要求一起傳送的資料值。

        * **user.name**：執行命令的使用者
        * **execute**：要執行的 Pig Latin 陳述式
        * **statusdir**：要寫入此工作狀態的目錄

    > [AZURE.NOTE]請注意，與 Curl 搭配使用時，會將 Pig Latin 陳述式中的空格取代為 `+` 字元。

    此命令應該會傳回可用來檢查工作狀態的工作識別碼，例如：

        {"id":"job_1415651640909_0026"}

3. 若要檢查工作的狀態，請使用下列命令。將 **JOBID** 取代為上一個步驟中所傳回的值。例如，如果傳回值為 `{"id":"job_1415651640909_0026"}`，則 **JOBID** 會是 `job_1415651640909_0026`。

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	如果工作已完成，則狀態會是 [**成功**]。

    > [AZURE.NOTE]此 Curl 要求會傳回含有工作資訊的 JavaScript Object Notation (JSON) 文件，而 jq 則用來僅擷取狀態值。

##<a id="results"></a>檢視結果

工作狀態變更為 [**成功**] 之後，即可從 Azure Blob 儲存體擷取工作結果。隨查詢一起傳送的 `statusdir` 參數包含輸出檔案的位置；在此案例中為 **wasb:///example/pigcurl**。此位址會將工作輸出儲存至 HDInsight 叢集所使用之預設儲存體容器的 **example/pigcurl** 目錄中。

您可以使用[適用於 Mac、Linux 和 Windows 的 Azure CLI](../xplat-cli.md) 來列出並下載這些檔案。例如，若要列出 **example/pigcurl** 中的檔案，請使用下列命令：

	azure storage blob list <container-name> example/pigcurl

若要下載檔案，請使用下列命令：

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]您必須使用 `-a` 和 `-k` 參數指定包含 Blob 的儲存體帳戶名稱，或是設定 **AZURE_STORAGE_ACCOUNT** 和 **AZURE_STORAGE_ACCESS_KEY** 環境變數。

##<a id="summary"></a>摘要

如這份文件所示，您可以使用原始 HTTP 要求來執行、監視和檢視 HDInsight 叢集上的 Pig 工作結果。

如需本文中使用的 REST 介面的詳細資訊，請參閱 [WebHCat 參照](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)。

##<a id="nextsteps"></a>接續步驟

如需 HDInsight 上 Pig 的一般資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

<!---HONumber=July15_HO2-->