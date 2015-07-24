<properties
   pageTitle="調整 HDInsight 中的 Hadoop、HBase 或 Apache Storm 叢集 | Microsoft Azure"
   description="變更在 HDInsight 中執行的 Hadoop、Apache Storm 或 HBase 叢集所用的資料節點數目，而不需要刪除然後再重新建立叢集。"
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/30/2015"
   ms.author="jgao"/>

#要變更 HDInsight 中資料節點數目的 Scale Hadoop、HBase 或 Apache Storm 叢集 

叢集調整功能可讓您變更在 Azure HDInsight 中執行的叢集所用的資料節點數目，而不需要刪除然後再重新建立叢集。您可以透過 Azure PowerShell、HDInsight SDK 或從 Azure 入口網站執行此作業。

## 功能詳細資料
本節描述變更 HDInsight 支援的每一種叢集所用的資料節點數目會有何影響：

* Hadoop
* Apache Storm
* HBase 

## Hadoop 

### 加入資料節點
您可以順暢增加正在執行的 Hadoop 叢集中的資料節點數目，而不會影響任何擱置或執行中的工作。您也可以在作業進行當中提交新工作。系統會順暢處理失敗的調整作業，讓叢集永保正常運作狀態。

### 移除資料節點
減少資料節點數目以縮減 Hadoop 叢集時，系統會重新啟動叢集中的部分服務。這會導致所有執行中和擱置的工作在調整作業完成時失敗。但您可以在作業完成後重新提交這些工作。

## Storm
您可以順暢地在 Storm 叢集運作時對其新增或移除資料節點。但在調整作業順利完成後，您需要重新平衡拓撲。

您可以使用兩種方式來完成重新平衡作業：

* Storm Web UI
* 命令列介面 (CLI) 工具 

如需詳細資訊，請參閱 [Apache Storm 文件](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

HDInsight 叢集上有提供 Storm Web UI：

![image1](./media/hdinsight-hadoop-cluster-scaling/StormUI.png)

以下是如何使用 CLI 命令重新平衡 Storm 拓撲的範例：

	## Reconfigure the topology "mytopology" to use 5 worker processes,
	## the spout "blue-spout" to use 3 executors, and
	## the bolt "yellow-bolt" to use 10 executors

	$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

##HBase
您可以順暢地在 HBase 叢集運作時對其新增或移除資料節點。區域伺服器會在完成調整作業的數分鐘之內自動取得平衡。但是，您也可以手動平衡區域伺服器，方法是登入叢集的前端節點，然後從命令提示字元視窗執行下列命令：

	>pushd %HBASE_HOME%\bin
	>hbase shell
	>balancer

## 必要條件

* 只支援使用 HDInsight 3.1.3 版或更高版本的叢集。如果您不確定您的叢集是什麼版本，您可以從 Azure 入口網站按一下 HDInsight 叢集名稱或從 Azure PowerShell 執行 `Get-AzureHDInsightCluster –name <clustername>` 命令，來檢查叢集的版本。

* 必須是 Azure PowerShell 0.8.14 版或更高版本，才可從 Azure PowerShell 執行此作業。您可以在 [Azure 下載](http://azure.microsoft.com/downloads/)網站上，從**命令列工具**區段下載最新版的 Azure PowerShell。您可以從 Azure PowerShell 視窗使用下列命令，檢查所安裝的 Azure PowerShell 版本：`(get-module Azure).Version`

## 如何使用叢集調整

### Azure 入口網站
從 Azure HDInsight 叢集儀表板的 [**調整**] 索引標籤可以變更執行中叢集的大小。

![](http://i.imgur.com/u5Mewwx.png)

### Azure PowerShell
若要使用 Azure PowerShell 變更 Hadoop 叢集大小，請從用戶端電腦執行下列命令：

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>	

> [AZURE.NOTE]用戶端電腦必須安裝 Azure PowerShell 0.8.14 版或更高版本才能使用這個命令。

### SDK
若要使用 HDInsight SDK 變更 Hadoop 叢集大小，請使用下列兩種方法之一：

	ChangeClusterSize(string dnsName, string location, int newSize) 

或

	ChangeClusterSizeAsync(string dnsName, string location, int newSize) 


這個方法的同步和非同步版本都會傳回 [ClusterDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterdetails_properties.aspx) 物件。

以下是部分範例程式碼，示範如何使用這個方法的同步版本：

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

	namespace HDInsightClusterScaling
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Friendly name for the certificate your created earlier  
	            string certfriendlyname = "<CertificateFriendlyName>";     
	            string subscriptionid = "<SubscriptionID>";
	            string clustername = "<ClusterDNSName>";
	     		string location = "<ClusterLocation>”";
				int newSize = <NewClusterSize>;
	
	            // Get the certificate object from certificate store by using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);
	
	            // Create an HDInsightClient object
	            HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
	            var client = HDInsightClient.Connect(creds);
	
	            Console.WriteLine("Rescaling HDInsight cluster ...");
	
	            // Change the cluster size
	     		ClusterDetails cluster = client.ChangeClusterSize(clustername, location, newSize);
	            
	            Console.WriteLine("Cluster Rescaled: {0} \n New Cluster Size = {1}", cluster.ConnectionUrl, cluster.ClusterSizeInNodes);
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadKey();
	        }
	    }
	}


請參閱[使用自訂選項在 HDInsight 上佈建 Hadoop 叢集](hdinsight-provision-clusters.md)主題，以取得如何使用 HDInsight .NET SDK 的詳細資訊。
 

<!---HONumber=July15_HO2-->