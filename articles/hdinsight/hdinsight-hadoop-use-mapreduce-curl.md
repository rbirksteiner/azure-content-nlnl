<properties
   pageTitle="搭配使用 MapReduce 和 Curl 與 HDInsight 中的 Hadoop | Microsoft Azure"
   description="了解如何使用 Curl 從遠端搭配執行 MapReduce 工作與 HDInsight 上的 Hadoop。"
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

#使用 Curl 搭配執行 MapReduce 工作與 HDInsight 上的 Hadoop

[AZURE.INCLUDE [MapReduce 選取器](../../includes/hdinsight-selector-use-mapreduce.md)]

在本文件中，您將學習如何使用 Curl 在 HDInsight 叢集的 Hadoop 上執行 MapReduce 工作。

Curl 用來示範如何使用原始 HTTP 要求與 HDInsight 互動，以執行 MapReduce 工作。要想執行這些作業，就要使用 HDInsight 叢集所提供的 WebHCat REST API (先前稱為 Templeton)。

> [AZURE.NOTE]如果您已熟悉使用以 Linux 為基礎的 Hadoop 伺服器，但剛接觸 HDInsight，請參閱[在以 Linux 為基礎的 HDInsight 上安裝 Hadoop 的須知事項](hdinsight-hadoop-linux-information.md)。

##<a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目：

* HDInsight 叢集上的 Hadoop (Linux 或 Windows 型)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>使用 Curl 執行 MapReduce 工作

> [AZURE.NOTE]在使用 Curl 或與 WebHCat 進行任何其他 REST 通訊時，您必須提供 HDInsight 叢集管理員使用者名稱和密碼來驗證要求。您也必須使用叢集名稱，做為用來將要求傳送至伺服器的 URI 一部分。
>
> 針對本節中的命令，將 **USERNAME** 取代為向叢集驗證的使用者，並將 **PASSWORD** 取代為使用者帳戶的密碼。將 **CLUSTERNAME** 取代為您叢集的名稱。
>
> 使用[基本存取驗證](http://en.wikipedia.org/wiki/Basic_access_authentication)來保護 REST API 的安全。您應該一律使用 HTTPS 提出要求，確保認證安全地傳送至伺服器。

1. 從命令列中，使用下列命令來確認您可以連線到 HDInsight 叢集：

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    您應該會收到如下所示的回應：

        {"status":"ok","version":"v1"}

    此命令中使用的參數如下：

    * **-u**：指出用來驗證要求的使用者名稱和密碼
    * **-G**：指出這是 GET 要求

    所有要求的 URI 開頭 (**https://CLUSTERNAME.azurehdinsight.net/templeton/v1**) 都相同。

2. 若要提交 MapReduce 工作，請使用下列命令：

		curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasb:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasb:///example/data/gutenberg/davinci.txt -d arg=wasb:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    URI 結尾 (/mapreduce/jar) 告訴 WebHCat，此要求會從 jar 檔案中的類別啟動 MapReduce 工作。此命令中使用的參數如下：

	* **-d**：未使用 `-G`，因此要求會依預設使用 POST 方法。`-d` 可指定與要求一起傳送的資料值。

        * **user.name**：執行命令的使用者
        * **jar**：包含要執行之類別的 jar 檔案位置
        * **class**：包含 MapReduce 邏輯的類別
        * **arg**：要傳送到 MapReduce 工作的引數；在此案例中，是用於輸出的輸入文字檔和目錄

    此命令應該會傳回可用來檢查工作狀態的工作識別碼：

        {"id":"job_1415651640909_0026"}

3. 若要檢查工作的狀態，請使用下列命令。將 **JOBID** 取代為上一個步驟中所傳回的值。例如，如果傳回值為 `{"id":"job_1415651640909_0026"}`，則 JOBID 會是 `job_1415651640909_0026`。

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	如果工作已完成，則狀態會是 [成功]。

    > [AZURE.NOTE]此 Curl 要求會傳回含有工作資訊的 JSON 文件；jq 用來僅擷取狀態值。

4. 工作狀態變更為 [**成功**] 之後，即可從 Azure Blob 儲存體擷取工作結果。隨查詢一起傳送的 `statusdir` 參數包含輸出檔案的位置；在此案例中為 **wasb:///example/curl**。此位址會將工作輸出儲存至 HDInsight 叢集所使用之預設儲存體容器的 **example/curl** 目錄中。

您可以使用[適用於 Mac、Linux 和 Windows 的 Azure CLI](../xplat-cli.md) 來列出並下載這些檔案。例如，若要列出 **example/curl** 中的檔案，請使用下列命令：

	azure storage blob list <container-name> example/curl

若要下載檔案，請使用下列命令：

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]您必須使用 `-a` 和 `-k` 參數指定包含 Blob 的儲存體帳戶名稱，或是設定 **AZURE_STORAGE_ACCOUNT** 和 **AZURE_STORAGE_ACCESS_KEY** 環境變數。如需詳細資訊，請參閱[如何將資料上傳至 HDInsight](hdinsight-upload-data.md)。

##<a id="summary"></a>摘要

如這份文件所示，您可以使用原始 HTTP 要求來執行、監視和檢視 HDInsight 叢集中的 Hive 工作結果。

如需本文中使用的 REST 介面的詳細資訊，請參閱 [WebHCat 參照](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)。

##<a id="nextsteps"></a>接續步驟

如需 HDInsight 中 MapReduce 工作的一般資訊：

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

<!---HONumber=July15_HO2-->