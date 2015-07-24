<properties
   pageTitle="在 Linux 架構的 HDInsight 上使用 Hadoop 的秘訣 |Microsoft Azure"
   description="取得在 Azure 雲端中執行的熟悉 Linux 環境上使用 Linux 架構的 HDInsight (Hadoop) 叢集的實作秘訣。"
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
   ms.date="05/27/2015"
   ms.author="larryfr"/>

# 在 Linux 上使用 HDInsight 的相關資訊 (預覽)

以 Linux 為基礎的 Azure HDInsight 叢集可在您熟悉的 Linux 環境中提供於 Azure 雲端中執行的 Hadoop。其操作大多與 Linux 安裝上的任何其他 Hadoop 相同。本文件會指出其中應注意的特殊不同之處。

## 網域名稱

連接到叢集時所要使用的完整網域名稱 (FQDN) 是 **&lt;clustername>.azurehdinsight.net** 或 (僅適用於 SSH) **&lt;clustername-ssh>.azurehdinsight.net**。

## 遠端存取服務

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

	> [AZURE.NOTE]使用叢集系統管理員使用者和密碼進行驗證，然後登入 Ambari 。這也會用到叢集系統管理員使用者和密碼。
	>
	> 驗證是純文字的 - 請一律使用 HTTPS 來協助確保連線的安全性。

	雖然可以直接透過網際網路存取叢集的 Ambari，但要使用某些功能則要靠存取叢集所使用之內部網域名稱的節點來達成。由於這是內部網域名稱且並未公開，因此在嘗試透過網際網路存取某些功能時會收到「找不到伺服器」的錯誤。

	若要解決這個問題，請使用 SSH 通道將 Web 流量以 Proxy 處理傳輸到叢集前端節點。使用下列文章的 **SSH 通道**章節從本機電腦上的連接埠建立連往叢集的 SSH 通道：

	* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)：使用 `ssh` 命令建立 SSH 通道的步驟。

	* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows)：使用 Putty 建立 SSH 通道的步驟。

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE]使用叢集系統管理員使用者和密碼進行驗證。
	>
	> 驗證是純文字的 - 請一律使用 HTTPS 來協助確保連線的安全性。

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE]使用叢集系統管理員使用者和密碼進行驗證。
	>
	> 驗證是純文字的 - 請一律使用 HTTPS 來協助確保連線的安全性。

* **SSH** - &lt;clustername>-ssh.azurehdinsight.net on port 22

	> [AZURE.NOTE]您只能從用戶端電腦透過 SSH 存取叢集前端節點。然後在連線後，再從前端節點使用 SSH 存取背景工作節點。

## 檔案位置

Hadoop 相關檔案可以在叢集節點的 `/usr/hdp/current` 上找到。

範例資料和 JAR 檔案可於 Hadoop 分散式檔案系統 (HDFS) 或 Azure Blob 儲存體的 '/example' 或 'wasb:///example' 取得。

## HDFS、Azure Blob 儲存體及儲存最佳作法

在大部分的 Hadoop 散發套件中，是以叢集機器上的本機儲存體支援 HDFS 的運作。這樣的方式雖有效率，但在用於雲端解決方案時則需高昂成本，因為運算資源是以每小時為單位來計費。

HDInsight 則是使用 Azure Blob 儲存體當做預設存放區，這麼做有下列好處：

* 長期儲存成本低廉

* 可從各種外部服務進行存取，例如網站、檔案上傳/下載公用程式、各種語言的 SDK 和網頁瀏覽器

由於 HDInsight 預設使用這種存放區，因此您通常不需要進行任何設定就能使用。例如，下列命令會列出 **/example/data** 資料夾中的檔案，這些檔案便是儲存在 Azure Blob 儲存體中：

	hadoop fs -ls /example/data

有些命令可能需要您指定您使用的是 Blob 儲存體。對於這些命令，您可以為其加上前置詞 **WASB://**。

HDInsight 也可讓您將多個 Blob 儲存體帳戶與叢集相關聯。若要存取非預設 Blob 儲存體帳戶上的資料，您可以使用下列格式：**WASB://&lt;container-name>@&lt;account-name>.blob.core.windows.net/**。例如，以下命令會列出指定容器和 Blob 儲存體帳戶之 **/example/data** 目錄的內容：

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### 叢集所使用的 Blob 儲存體為何？

叢集建立期間，您會選取使用現有 Azure 儲存體帳戶和容器，或是建立新的。之後您可能就忘得一乾二淨。如果要尋找儲存體帳戶和容器，您可以使用下列方法。

**Ambari API**

1. 請使用以下命令來擷取 HDFS 組態資訊：

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

2. 在傳回的 JSON 資料中，找到 `fs.defaultFS` 項目。這樣會以如下的格式包含預設容器和儲存體帳戶名稱：

        wasb://CONTAINTERNAME@STORAGEACCOUNTNAME.blob.core.windows.net

	> [AZURE.TIP]如果您已安裝 [jq](http://stedolan.github.io/jq/)，您可以使用下列程式碼以只傳回 `fs.defaultFS` 項目：
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'`
	
3. 若要尋找用來驗證儲存體帳戶的金鑰，或尋找與叢集相關聯的任何次要儲存體帳戶，使用下列方法：

		curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"
		
4. 在傳回的 JSON 資料中，找到以 `fs.azure.account.key` 開頭的項目。項目名稱的其餘部分是儲存體帳戶名稱。例如，`fs.azure.account.key.mystorage.blob.core.windows.net`。此項目中儲存的值是用來驗證儲存體帳戶的金鑰。

	> [AZURE.TIP]如果您已安裝 [jq](http://stedolan.github.io/jq/)，可以使用下列程式碼來傳回金鑰和值清單：
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties as $in | $in | keys[] | select(. | contains("fs.azure.account.key.")) as $item | $item | ltrimstr("fs.azure.account.key.") | { storage_account: ., storage_account_key: $in[$item] }'`


**Azure 入口網站**

1. 在 [Azure 入口網站](https://manage.windowsazure.com/)中，選取您的 HDInsight 叢集。

2. 選取頁面頂端的 [**儀表板**]。

3. 頁面的 [**連結的資源**] 區段就會列出儲存體帳戶和容器。

	![連結的資源](./media/hdinsight-hadoop-linux-information/storageportal.png)

### 如何存取 Blob 儲存體？

除了透過叢集的 Hadoop 命令，還有各種不同方式可用來存取 Blob：

* [適用於 Mac、Linux 及 Windows 的 Azure CLI](../xplat-cli.md)：用於 Azure 的命令列介面命令。安裝好後，請使用 `azure storage` 命令以協助使用儲存體，或是針對 Blob 特有命令使用 `azure blob`。

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage)：python 指令碼，用於 Azure 儲存體中的 Blob。

* 各種 SDK：

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.js](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [儲存體 REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

## 後續步驟

* [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [搭配 HDInsight 使用 MapReduce 工作](hdinsight-use-mapreduce.md)
 

<!---HONumber=July15_HO2-->