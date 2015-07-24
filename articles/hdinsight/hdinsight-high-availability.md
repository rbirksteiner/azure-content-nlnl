<properties 
	pageTitle="HDInsight 中的 Hadoop 叢集可用性 | Microsoft Azure" 
	description="HDInsight 使用額外的前端節點部署高可用性且可靠的叢集。" 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="bradsev" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/19/2014" 
	ms.author="bradsev"/>


#HDInsight 上 Hadoop 叢集的可用性和可靠性


已在 Azure HDInsight 所部署的 Hadoop 叢集中加入第二個前端節點，以提升管理工作負載所需服務的可用性和可靠性。Hadoop 叢集的標準實作通常包含單一前端節點。這些叢集是專為順利管理背景工作節點錯誤所設計的，但任何在前端節點上執行的主要服務中斷都有可能導致叢集停止工作。

![HDInsight Hadoop 實作中的高可靠性前端節點圖表。](http://i.imgur.com/jrUmrH4.png)

HDInsight 會透過新增次要前端節點 (Head Node1)，將此單一失敗點移除。已加入 [ZooKeeper](http://zookeeper.apache.org/) 節點 (ZK)，並使用於前端節點的領袖選擇，以確保背景工作節點和閘道 (GW) 知道當作用中前端節點 (Head Node0) 變成非作用中狀態時，容錯移轉至次要前端節點 (Head Node1) 的時機。


## 如何檢查作用中前端節點的服務狀態 ##
若要判斷出作用中的前端節點及其執行的服務狀態，您必須使用遠端桌面通訊協定 (RDP) 連接到 Hadoop 叢集。根據 Azure 中的預設，遠端進入叢集的功能已停用，因此必須先啟用該功能。如需如何在入口網站中執行此動作的指示，請參閱[使用 RDP 連接到 HDInsight 叢集](hdinsight-administer-use-management-portal.md#rdp)。一旦遠端進入叢集後，按兩下位於桌面上的 [Hadoop 服務可用狀態] 圖示，以取得執行 Namenode、Jobtracker、Templeton、Oozieservice、Metastore 和 Hiveserver2 服務的前端節點狀態，或在 HDI 3.0 中，取得執行 Namenode、Resource Manager、History Server、Templeton、Oozieservice、Metastore 和 Hiveserver2 服務的前端節點狀態。

![](http://i.imgur.com/MYTkCHW.png)


## 如何存取次要前端節點上的記錄檔 \\

如果要存取事件中次要前端節點 (已成為作用中前端節點) 上的工作記錄，仍然可以使用瀏覽 JobTracker UI (如其為主要作用中節點般)。若要存取 JobTracker，您必須使用 RDP 連接到 Hadoop 叢集 (如上一節所述)。一旦遠端進入叢集後，按兩下位於桌面上的 [Hadoop 名稱節點] 圖示，然後按一下 [名稱節點記錄] 以前往次要前端節點上的記錄目錄。

![](http://i.imgur.com/eL6jzgB.png)


## 如何設定前端節點的大小 ##
根據預設，前端節點會配置為大型虛擬機器 (VM)。這個大小適合管理大部分在叢集上執行的 Hadoop 工作。但有些情況可能會因前端節點而要求超大型 VM。例如，叢集必須管理大量的小型 Oozie 工作。

可以使用 Azure PowerShell Cmdlet 或 HDInsight SDK 設定超大型 VM。

使用 Azure PowerShell 建立與佈建叢集的說明已記錄在 [使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)。超大型前端節點的組態要求將 `-HeadNodeVMSize ExtraLarge` 參數加入此程式碼中所使用的 `New-AzureHDInsightcluster` Cmdlet。

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge head-node VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

SDK 的情況十分類似。使用 SDK 建立與佈建叢集的說明已記錄在 [使用 HDInsight .NET SDK](hdinsight-provision-clusters.md#sdk)。超大型前端節點的組態要求將 `HeadNodeSize = NodeVMSize.ExtraLarge` 參數加入此程式碼中所使用的 `ClusterCreateParameters()` 方法。

    # Create a new HDInsight cluster with the HDInsight SDK
	# Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
    Name = clustername,
    Location = location,
    HeadNodeSize = NodeVMSize.ExtraLarge,
    DefaultStorageAccountName = storageaccountname,
    DefaultStorageAccountKey = storageaccountkey,
    DefaultStorageContainer = containername,
    UserName = username,
    Password = password,
    ClusterSizeInNodes = clustersize
    };


**參考**

- [ZooKeeper](http://zookeeper.apache.org/)
- [使用 RDP 連接到 HDInsight 叢集](hdinsight-administer-use-management-portal.md#rdp)
- [使用 HDInsight .NET SDK](hdinsight-provision-clusters.md#sdk) 







 

<!---HONumber=July15_HO2-->