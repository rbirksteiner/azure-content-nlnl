<properties
	pageTitle="使用 Ambari API 監視 HDInsight 上的 Hadoop 叢集 | Microsoft Azure"
	description="使用 Apache Ambari API 來佈建、管理和監視 Hadoop 叢集。直覺式操作工具和 API 可消除 Hadoop 的複雜性。"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	editor="cgronlun"
	manager="paulettm"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/08/2015"
	ms.author="jgao"/>

# 使用 Ambari API 監視 HDInsight 上的 Hadoop 叢集

了解如何使用 Ambari API 監視 HDInsight 叢集 3.1 及 2.1 版。

> [AZURE.NOTE]本文中的資訊主要適用於 Windows 架構的 HDInsight 叢集，該叢集提供 Ambari REST API 的唯讀版本。對於 Linux 架構的叢集，請參閱[使用 Ambari 管理 Hadoop 叢集](hdinsight-hadoop-manage-ambari.md)。

## <a id="whatisambari"></a> 什麼是 Ambari？

[Apache Ambari][ambari-home] 可用來佈建、管理及監視 Apache Hadoop 叢集。其中包含一組直接易懂的操作員工具和健全的 API 集，可消除 Hadoop 的複雜性，並簡化叢集作業。如需關於 API 的詳細資訊，請參閱 [Ambari API 參考資料][ambari-api-reference]。


HDInsight 目前僅支援 Ambari 監視功能。HDInsight 3.0 及 2.1 版叢集可支援 Ambari API 1.0。本文涵蓋於 HDInsight 3.1 和 2.1 版叢集上存取 Ambari API。兩者的主要差別在於某些元件已隨著新功能引進而變更 (例如工作歷程伺服器)。


##<a id="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **具有 Azure PowerShell 的工作站**。請參閱[安裝和使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。


- (選擇性) [cURL][curl]。若要安裝此項目，請參閱 [cURL 版本和下載][curl-download]。

	>[AZURE.NOTE]在 Windows 上使用 cURL 命令時，請針對選項值使用雙引號，而不要使用單引號。

- **Azure HDInsight 叢集**。如需叢集佈建的指示，請參閱[開始使用 HDInsight][hdinsight-get-started] 或[佈建 HDInsight 叢集][hdinsight-provision]。進行教學課程時，您將需要下列資料：

	<table border="1">
<tr><th>叢集屬性</th><th>Azure PowerShell 變數名稱</th><th>值</th><th>說明</th></tr>
<tr><td>HDInsight 叢集名稱</td><td>$clusterName</td><td></td><td>您的 HDInsight 叢集名稱。</td></tr>
<tr><td>叢集使用者名稱</td><td>$clusterUsername</td><td></td><td>佈建時指定的叢集使用者名稱。</td></tr>
<tr><td>叢集密碼</td><td>$clusterPassword</td><td></td><td>叢集使用者密碼。</td></tr>
</table>> [AZURE.NOTE]將值填入資料表。這將有助於本教學課程的執行。



##<a id="jumpstart"></a>搶先使用

您可以透過數種方式使用 Ambari 監視 HDInsight 叢集。

**使用 Azure PowerShell**

以下是*在 HDInsight 3.1 叢集*取得 MapReduce 工作追蹤程式資訊的 Azure PowerShell 指令碼。 主要差別在於我們從 YARN 服務 (而非 MapReduce) 提取這些詳細資料。

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"

	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

	$response.metrics.'yarn.queueMetrics'

以下是*在 HDInsight 2.1 叢集*取得 MapReduce 工作追蹤程式資訊的 Azure PowerShell 指令碼：

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"

	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

	$response.metrics.'mapred.JobTracker'

輸出如下：

![Jobtracker Output][img-jobtracker-output]

**使用 cURL**

以下是使用 cURL 取得叢集資訊的範例：

	curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

輸出如下：

	{"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
	 "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
	 "services"[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
	 "hosts":[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**2014/10/8 版本的相關資訊**：

使用 Ambari 端點 "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}" 時，*host_name* 欄位會傳回節點的完整網域名稱 (FQDN)，而不是主機名稱。在 2014/10/8 版本之前，此範例只會傳回 "**headnode0**"。在 2014/10/8 版本之後，您會得到 FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**"，如先前範例所示。需要此變更，以便將多種叢集類型 (例如 HBase 和 Hadoop) 部屬至一個虛擬網路 (VNET) 中。例如，使用 HBase 做為 Hadoop 的後端平台時就是這種情形。

##<a id="monitor"></a>Ambari 監視 API

下表列出部分最常用的 Ambari 監視 API 呼叫。如需 API 的詳細資訊，請參閱 [Ambari API 參考資料][ambari-api-reference]。

<table border="1">
<tr><th>監視 API 呼叫</th><th>URI</th><th>說明</th></tr>
<tr><td>取得叢集</td><td><tt>/api/v1/clusters</tt></td><td></td></tr>
<tr><td>取得叢集資訊。</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net</tt></td><td>叢集、服務、主機</td></tr>
<tr><td>取得服務</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/services</tt></td><td>服務包括：hdfs、mapreduce</td></tr>
<tr><td>取得服務資訊</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/services/&lt;ServiceName></tt></td><td></td></tr>
<tr><td>取得服務元件</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/services/&lt;ServiceName>/components</tt></td><td>HDFS：namenode、datanode<br/>MapReduce：jobtracker；tasktracker</td></tr>
<tr><td>取得元件資訊</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/services/&lt;ServiceName>/components/&lt;ComponentName></tt></td><td>ServiceComponentInfo、主機元件、度量</td></tr>
<tr><td>取得主機</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts</tt></td><td>headnode0、workernode0</td></tr>
<tr><td>取得主機資訊</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts/&lt;HostName>
</td><td></td></tr>
<tr><td>取得主機元件</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts/&lt;HostName>/host_components </tt></td><td>namenode、resourcemanager</td></tr>
<tr><td>取得主機元件資訊</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts/&lt;HostName>/host_components/&lt;ComponentName> </tt></td><td>HostRoles、元件、主機、度量</td></tr>
<tr><td>取得組態</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/configurations </tt></td><td>組態類型：core-site、hdfs-site、mapred-site、hive-site</td></tr>
<tr><td>取得組態資訊</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/configurations?type=&lt;ConfigType>&amp;tag=&lt;VersionName> </tt></td><td>組態類型：core-site、hdfs-site、mapred-site、hive-site</td></tr>
</table>


##<a id="nextsteps"></a>後續步驟

現在，您已了解如何使用 Ambari 監視 API。若要深入了解，請參閱：

- [使用管理入口網站管理 HDInsight 叢集][hdinsight-admin-portal]
- [使用 Azure PowerShell 管理 HDInsight 叢集][hdinsight-admin-powershell]
- [使用命令列介面管理 HDInsight 叢集][hdinsight-admin-cli]
- [HDInsight 文件][hdinsight-documentation]
- [開始使用 HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
 

<!---HONumber=July15_HO2-->