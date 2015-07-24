<properties 
   pageTitle="設定兩個資料中心之間的 HBase 複寫 | Microsoft Azure" 
   description="了解如何跨兩個資料中心設定 HBase 複寫，以及有關叢集複寫的使用案例。" 
   services="hdinsight,virtual-network" 
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
   ms.date="04/02/2015"
   ms.author="jgao"/>

# 在 HDInsight 中設定 HBase 地理複寫

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 
 
了解如何跨兩個資料中心設定 HBase 複寫。叢集複寫的某些使用案例包含：

- 備份和災害復原
- 資料彙總
- 地理資料分佈
- 線上資料擷取加上離線資料分析

叢集複寫會使用來源推入方法。HBase 叢集可以是來源、目的地，或可同時滿足兩個角色。複寫為非同步作業，而複寫的目標是最終的一致性。當來源接收到已啟用複寫的資料行系列編輯時，編輯會傳播到所有目的地叢集。當資料從一個叢集複寫到另一個時，會追蹤來源叢集和已取用資料的所有叢集以避免複寫迴圈。如需詳細資訊，在此教學課程中，您將設定來源目的地複寫。若需其他叢集拓撲，請參閱 [Apache HBase 參考指南](http://hbase.apache.org/book.html#_cluster_replication)。

這是本系列的第三個部分：

- [設定兩個虛擬網路之間的 VPN 連線][hdinsight-hbase-replication-vnet]
- [設定虛擬網路的 DNS][hdinsight-hbase-replication-dns]
- 設定 HBase 地理複寫 (本教學課程)

下圖說明兩個虛擬網路和您在[設定兩個虛擬網路之間的 VPN 連線][hdinsight-hbase-geo-replication-vnet]和[設定虛擬網路的 DNS][hdinsight-hbase-replication-dns] 中建立的網路連線：

![HDInsight HBase 複寫虛擬網路圖表][img-vnet-diagram]

## <a id="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **具有 Azure PowerShell 的工作站**。請參閱[安裝和使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。若要執行 PowerShell 指令碼，您必須以系統管理員的身分執行 Azure PowerShell，並將執行原則設為 *RemoteSigned*。請參閱＜使用 Set-ExecutionPolicy Cmdlet＞。

- **兩個具備 VPN 連線且已設定 DNS 的 Azure 虛擬網路**。如需相關指示，請參閱[設定兩個 Azure 虛擬網路之間的 VPN 連線][hdinsight-hbase-replication-vnet]和[在兩個 Azure 虛擬網路之間設定 DNS][hdinsight-hbase-replication-dns]。


	執行 PowerShell 指令碼之前，請確定您已使用下列 Cmdlet 連接到 Azure 訂用帳戶：

		Add-AzureAccount

	如果您有多個 Azure 訂用帳戶，請使用下列 Cmdlet 設定目前的訂用帳戶：

		Select-AzureSubscription <AzureSubscriptionName>



## 在 HDInsight 中佈建 HBase 叢集

在[設定兩個 Azure 虛擬網路之間的 VPN 連線][hdinsight-hbase-replication-vnet]中，您已在歐洲資料中心建立了一個虛擬網路，也在美國資料中心建立了一個虛擬網路。兩個虛擬網路透過 VPN 連線。在這個工作階段中，您將在每個虛擬網路中佈建 HBase 叢集。稍後在本教學課程中，您將會讓其中一個 HBase 叢集複寫其他 HBase 叢集。

Azure 入口網站不支援使用自訂組態選項佈建 HDInsight 叢集。例如，將 *hbase.replication* 設為 *true*。佈建叢集之後，如果您設定組態檔中的值，您將會在叢集重新安裝映像之後遺失設定。如需詳細資訊，請參閱[在 HDInsight 中佈建 Hadoop 叢集][hdinsight-provision]。利用自訂選項來佈建 HDInsight 叢集的其中一個選項是使用 Azure PowerShell。


**在 Contoso-VNet-EU 中佈建 HBase 叢集**

1. 從您的工作站，開啟 Windows PowerShell ISE。
2. 在指令碼的開頭處設定變數，然後執行指令碼。

		# create hbase cluster with replication enabled
		
		$azureSubscriptionName = "[AzureSubscriptionName]"
		
		$hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
		$hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
		$hadoopUserLogin = "[HadoopUserName]"
		$hadoopUserpw = "[HadoopUserPassword]"
		
		$vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
		$subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
		
		$storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
		$storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
		$blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
		
		#connect to your Azure subscription
		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName
		
		# Create a storage account used by the HBase cluster
		$location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
		
		# Create a blob container used by the HBase cluster
		$storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
		
		# Create provision configuration object
		$hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
		    
		$hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
		
		# retrieve vnet id based on vnetname
		$vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
		
		$config = New-AzureHDInsightClusterConfig `
		                -ClusterSizeInNodes $hbaseClusterSize `
		                -ClusterType HBase `
		                -VirtualNetworkId $vNetID `
		                -SubnetName $subNetName `
		            | Set-AzureHDInsightDefaultStorage `
		                -StorageAccountName $storageAccountName `
		                -StorageAccountKey $storageAccountKey `
		                -StorageContainerName $blobContainerName `
		            | Add-AzureHDInsightConfigValues `
		                -HBase $hbaseConfigValues
		
		# provision HDInsight cluster
		$hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
		$credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
		
		$config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**在 Contoso-VNet-US 中佈建 HBase 叢集**

- 使用具有下列值的相同指令碼：

		$hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
		$vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
		$storageAccountName = 'ContosoStoreUS'	

	因為您已連線到您的 Azure 帳戶，您不需要再執行下列 comlet：

		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName




## 設定 DNS 條件轉寄站

在[設定虛擬網路的 DNS][hdinsight-hbase-replication-dns] 中，您已設定兩個網路的 DNS 伺服器。HBase 叢集有不同的網域尾碼。因此，您必須設定其他的 DNS 條件轉寄站。

若要設定條件轉寄站，您必須知道兩個 HBase 叢集的網域尾碼。

**尋找兩個 HBase 叢集的網域尾碼**

1. RDP 到 **Contoso-HBase-EU**。如需相關指示，請參閱[使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集][hdinsight-manage-portal]。實際上它是叢集的 headnode0。
2. 開啟 Windows PowerShell 主控台或命令提示字元。
3. 執行 **ipconfig**，並記下**連線專用 DNS 尾碼**。
4. 請不要關閉 RDP 工作階段。您稍後需要它測試網域名稱解析。
5. 重複相同步驟來找出 **Contoso-HBase-US** 的**連線專用 DNS 尾碼**。


**設定 DNS 轉寄站**
 
1.	RDP 到 **Contoso-DNS-EU**。 
2.	按一下左下方的 Windows 鍵。
2.	按一下 [**系統管理工具**]。
3.	按一下 [**DNS**]。
4.	在左窗格中，展開 **DSN**，**Contoso-DNS-EU**。
5.	以滑鼠右鍵按一下 [**條件轉寄站**]，然後按一下 [**新的條件轉寄站**]。 
5.	輸入以下資訊：
	- **DNS 網域**： 輸入 Contoso-HBase-US 的 DNS 尾碼。例如：Contoso-HBase-US.f5.internal.cloudapp.net。
	- **主要伺服器的 IP 位址**：輸入 10.2.0.4，也就是 Contoso-DNS-US 的 IP 位址。請確認 IP。您的 DNS 伺服器可以有不同的 IP 位址。
6.	按下 **ENTER**，然後按一下 [**確定**]。現在您可以從 Contoso-DNS-EU 解析 Contoso-DNS-US 的 IP 位址。
7.	重複這些步驟，以將 DNS 條件轉寄站新增至附帶下列值之 Contoso-DNS-US 上的虛擬機器：
	- **DNS 網域**： 輸入 Contoso-HBase-EU 的 DNS 尾碼。 
	- **主要伺服器的 IP 位址**：輸入 10.2.0.4，也就是 Contoso-DNS-EU 的 IP 位址。

**測試網域名稱解析**

1. 切換至 [Contoso-HBase-EU RDP] 視窗。
2. 開啟命令提示字元。
3. 執行 ping 命令：

		ping headnode0.[DNS suffix of Contoso-HBase-US]

	HBase 叢集的背景工作節點已開啟 ICM 通訊協定

4. 請不要關閉 RDP 工作階段。稍後在教學課程中，您將會需要它。
5. 重複相同的步驟來從 Contoso-HBase-US ping Contoso-HBase-EU 的 headnode0。

>[AZURE.IMPORTANT]DNS 必須在您繼續下一個步驟之前運作。

## 啟用 HBase 資料表之間的複寫

現在，您可以建立範例 HBase 資料表、啟用複寫，然後利用一些資料進行測試。您將使用的範例資料表有兩個資料行系列：個人和辦公室。

在本教學課程中，您將讓歐洲 HBase 叢集做為來源叢集，讓美國 HBase 叢集做為目的地叢集。

在來源與目的地叢集上建立具有相同名稱和資料行系列的 HBase 資料表，如此一來目的地叢集就會知道要在何處儲存它將接收的資料。如需有關使用 HBase Shell 的詳細資訊，請參閱[開始使用 HDInsight 中的 Apache HBase][hdinsight-hbase-get-started]。

**在 Contoso-HBase-EU 上建立 HBase 資料表**

1. 切換至 [**Contoso-HBase-EU** RDP] 視窗。
2. 從桌面上，按一下 [Hadoop 命令列]。
2. 將資料夾切換至 HBase 主目錄：

		cd %HBASE_HOME%\bin
3. 開啟 HBase Shell：

		hbase shell
4. 建立 HBase 資料表：

		create 'Contacts', 'Personal', 'Office'
5. 不要關閉 RDP 工作階段也不要關閉 [Hadoop 命令列] 視窗。稍後在教學課程中，您將會需要它們。
	
**在 Contoso-HBase-US 上建立 HBase 資料表**

- 重複相同的步驟，以在 Contoso-HBase-US 上建立相同的資料表。


**新增 Contoso-HBase-US 做為複寫對等**

1. 切換至 [**Contoso-HBase_EU** RDP] 視窗。
2. 從 [HBase Shell] 視窗，新增目的地叢集 (Contoso-HBase-US) 做為對等，例如：

		add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

	在此範例中，網域尾碼是 *contoso-hbase-us.d4.internal.cloudapp.net*。您必須將其更新以符合您的美國 HBase 叢集網域尾碼。請確定主機名稱之間沒有任何空格。

**在來源叢集上設定複寫每個資料行系列**

1. 從 **Contso-HBase-EU** RDP 工作階段的 [HBase Shell] 視窗，設定複寫每個資料行系列：

		disable 'Contacts'
		alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
		alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
		enable 'Contacts'

**大量上傳資料至 HBase 資料表**

範例資料檔案已利用下列 URL 上傳到公用的 Azure Blob 容器：

		wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

檔案內容：

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

您可以將相同的資料檔案上傳至 HBase 叢集並從中匯入資料。

1. 切換至 [**Contoso-HBase-EU** RDP] 視窗。
2. 從桌面上，按一下 [Hadoop 命令列]。
3. 將資料夾切換至 HBase 主目錄：

		cd %HBASE_HOME%\bin

4. 上傳資料：

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## 確認資料複寫正在進行中

您可以利用下列 HBase Shell 命令掃描這兩個叢集的資料表，以確認該複寫正在進行中：

		Scan 'Contacts'


## 後續步驟

在本教學課程中，您已經學會如何跨兩個資料中心設定 HBase 複寫。若要深入了解 HDInsight 與 HBase ，請參閱：

- [HDInsight 服務頁面](http://azure.microsoft.com/services/hdinsight/)
- [HDInsight 文件](http://azure.microsoft.com/documentation/services/hdinsight/)
- [開始使用 HDInsight 中的 Apache HBase][hdinsight-hbase-get-started]
- [HDInsight HBase 概觀][hdinsight-hbase-overview]
- [在 Azure 虛擬網路上佈建 HBase 叢集][hdinsight-hbase-provision-vnet]
- [使用 HBase 分析即時 Twitter 情緒][hdinsight-hbase-twitter-sentiment]
- [在 HDInsight (Hadoop) 中使用 Storm 和 HBase 分析感應器資料][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: ../install-configure-powershell.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md

<!---HONumber=July15_HO2-->