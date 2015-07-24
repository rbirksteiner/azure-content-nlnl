<properties 
	pageTitle="在虛擬網路上佈建 HBase 叢集 | Microsoft Azure" 
	description="開始在 Azure HDInsight 中使用 HBase。了解如何在 Azure 虛擬網路上建立 HDInsight HBase 叢集。" 
	keywords=""	
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
   ms.date="04/21/2015"
   ms.author="jgao"/>

# 在 Azure 虛擬網路上佈建 HBase 叢集

了解如何在 [Azure 虛擬網路][1]上建立 Azure HDInsight HBase 叢集。

由於 HBase 叢集已與虛擬網路整合，因此能夠部署到與您應用程式相同的虛擬網路，讓應用程式得以和 HBase 直接通訊。其優點包括：

- Web 應用程式可以直接連接到 HBase 叢集節點，因而能夠使用 HBase Java 遠端程序呼叫 (RPC) API 來進行通訊。
- 透過使流量不須經過多個閘道器及負載平衡器，以提升其效能。
- 能夠以更安全的方式處理敏感資訊，而不會暴露公用端點。

##必要條件
開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **具有 Azure PowerShell 的工作站**。請參閱[安裝及使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。如需指示，請參閱[安裝並設定 Azure PowerShell](../install-configure-powershell.md)。若要執行 Azure PowerShell 指令碼，您必須以系統管理員的身分執行 Azure PowerShell，並將執行原則設為 *RemoteSigned*。請參閱[使用 Set-executionpolicy cmdlet][2]。

	執行 Azure PowerShell 指令碼之前，請確定您已使用下列 Cmdlet 連接到 Azure 訂用帳戶：

		Add-AzureAccount

	如果您有多個 Azure 訂用帳戶，請使用下列 Cmdlet 設定目前的訂用帳戶：

		Select-AzureSubscription <AzureSubscriptionName>


##將 HBase 叢集佈建到虛擬網路 

佈建 HBase 叢集之前，您需要有 Azure 虛擬網路。

**使用 Azure 入口網站建立虛擬網路**

1. 登入 [Azure 入口網站][azure-portal]。
2. 依序按一下左下角 [新增]、[網路服務]、[虛擬網路] 及 [快速建立]。
3. 輸入或選取下列值：

	- **名稱** - 虛擬網路的名稱。
	- **位址空間** - 為虛擬網路選擇足夠大小的位址空間，如此才能為叢集中的所有節點提供位址。否則，佈建將會失敗。要逐步解說此教學課程，您可以挑選三個選項的任一者。 
	- **最大 VM 計數** - 選擇最大虛擬機器 (VM) 計數之一。此值決定可在位址空間下建立的可能主機 (VM) 數目。對於此教學課程的逐步解說，**4096 [CIDR: /20]** 就已足夠。 
	- **位置** - 此位置必須與您將建立的 HBase 叢集相同。
	- **DNS 伺服器** - 此教學課程使用 Azure 所提供的內部網域名稱系統 (DNS) 伺服器，因此您可以選擇 [無]。同時也支援更多自訂 DNS 伺服器的進階網路功能設定。如需詳細指引，請參閱[名稱解析 (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx)。
4. 在右下角按一下 [建立虛擬網路]。新的虛擬網路名稱隨即會出現在清單中。稍待片刻，直到 [狀態] 欄顯示 [已建立]。
5. 在主要窗格上，按一下您剛建立的虛擬網路。
6. 按一下頁面頂端的 [儀表板]。
7. 將 [快速概覽] 下方的虛擬網路 ID 記下來。佈建 HBase 叢集時需要使用它。
8. 按一下頁面頂端的 [設定]。
9. 在頁面底部，可看到預設的子網路名稱為 [Subnet-1]。您可以選擇性地重新命名子網路，或針對 HBase 叢集加入新的子網路。記下子網路名稱，佈建叢集時將需要使用它。
10. 驗證 [CIDR (位址計數)]，其將用於叢集的子網路。位址計數必須大於背景工作節點數量加上七 (閘道器：2、前端節點：2、Zookeeper：3)。例如，如果您需要 10 個節點的 HBase 叢集，那麼子網路的位址計數必須大於 17 (10+7)。否則，部署將會失敗。
11. 如果您已更新子網路值，請按一下頁面底部的 [儲存]。


**將 DNS 伺服器虛擬機器加入虛擬網路**

DNS 伺服器是選擇性的，但在某些案例中為必要。程序已記錄在[設定兩個 Azure 虛擬網路之間的 DNS][hdinsight-hbase-replication-dns]。基本上，您需要執行下列步驟：

1. 將 Azure 虛擬機器加入虛擬網路
2. 設定虛擬機器的靜態 IP 位址
3. 將 DNS 伺服器角色加入虛擬機器
4. 將 DNS 伺服器指派給虛擬網路 


**建立叢集要使用的 Azure 儲存體帳戶和 Blob 儲存體容器**

> [AZURE.NOTE]HDInsight 叢集使用 Azure Blob 儲存體來儲存資料。如需詳細資訊，請參閱[在 HDInsight 中搭配使用 Azure Blob 儲存體和 Hadoop](../hdinsight-use-blob-storage.md) (英文)。您將會需要儲存體帳戶和 Blob 儲存體容器。儲存體帳戶的位置必須符合虛擬網路位置和叢集位置。

像其他 HDInsight 叢集一樣，HBase 叢集需要 Azure 儲存體帳戶和 Blob 儲存體容器做為預設檔案系統。儲存體帳戶的位置必須符合虛擬網路位置和叢集位置。如需詳細資訊，請參閱[在 HDInsight 中搭配使用 Azure Blob 儲存體和 Hadoop][hdinsight-storage] (英文)。當您佈建 HBase 叢集時，可以選擇建立新叢集或使用現有叢集。此程序顯示如何使用 Azure 入口網站建立儲存體帳戶和 Blob 儲存體容器。

1. 登入 [Azure 入口網站][azure-portal]。
2. 按一下左下角的 [新增]，並指向 [資料服務]，接著指向 [儲存體]，然後按一下 [快速建立]。

3. 輸入或選取下列值：

	- **URL** - 儲存體帳戶的名稱。
	- **位置** - 儲存體帳戶的位置。請確定此位置符合虛擬網路位置。不支援同質群組。
	- **複寫** - 針對測試目的，請使用 [本地備援] 以降低成本。

4. 按一下 [建立儲存體帳戶]。您將在儲存體清單中看到新的儲存體帳戶。
5. 等候新儲存體帳戶的 [狀態] 變更為 [線上]。
6. 按一下清單中的新儲存體帳戶加以選取。
7. 按一下頁面底部的 [MANAGE ACCESS KEYS]。
8. 請記下儲存體帳戶名稱及主要存取金鑰 (或次要存取金鑰，兩者皆可)。稍後在教學課程中需要這些資訊。
9. 按一下頁面頂端的 [容器]。
10. 按一下頁面底部的 [新增]。
11. 輸入容器名稱。此容器會用來作為 HBase 叢集的預設容器。依預設，預設的容器名稱會符合叢集名稱。保持 [存取] 欄位為 [私用]。  
12. 按一下核取記號以建立容器。

**使用 Azure 入口網站佈建 HBase 叢集**

> [AZURE.NOTE]如需使用 Azure PowerShell 佈建新 HBase 叢集的相關資訊，請參閱[使用 Azure PowerShell 佈建 HBase 叢集](#powershell)。

1. 登入 [Azure 入口網站][azure-portal]。

2. 按一下左下角的 [新增]，並指向 [資料服務]，接著指向 [HDInsight]，然後按一下 [自訂建立]。

3. 輸入叢集名稱、依序選取 HBase 做為叢集類型、Windows Server 2012 作業系統、HDInsight 版本，然後按一下向右按鈕。

	![提供 HBase 叢集的詳細資料][img-provision-cluster-page1]


	> [AZURE.NOTE]Windows Server 是HBase 叢集唯一可用的作業系統選項。

4. 在 [設定叢集] 頁面上，輸入或選取下列內容：

	![提供 HBase 叢集的詳細資料](./media/hdinsight-hbase-provision-vnet/hbasewizard2.png)

	<table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>資料節點</td><td>選取您要部署的資料節點數。請建立單一節點叢集，以供測試之用。<br />叢集大小限制會隨著 Azure 訂用帳戶而不同。若要提高限制，請與 Azure 帳務支援人員連絡。</td></tr>
	<tr><td>區域/虛擬網路</td><td><p>如果您已建立區域或 Azure 虛擬網路，請選取其一。針對本教學課程，請選取您先前建立的網路，接著選取對應的子網路。預設名稱為 <b>Subnet-1</b>。</p></td></tr>
	<tr><td>前端節點大小</td><td><p>選取前端節點的 VM 大小。</p></td></tr>
	<tr><td>資料節點大小</td><td><p>選取資料節點的 VM 大小。</p></td></tr>
	<tr><td>Zookeeper 大小</td><td><p>選取 Zookeeper 節點的 VM 大小。</p></td></tr>
</table>>[AZURE.NOTE]根據選擇的 VM ，您的成本可能會有所不同。HDInsight 針對叢集節點會使用所有標準層 VM。如需 VM 大小對您價格影響的相關資訊，請參閱 <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 定價</a>。

	按一下向右按鈕。

5. 輸入此叢集的 Hadoop 使用者名稱及密碼，然後按一下右鍵。

	![提供 Hadoop HDInsight 叢集的儲存體帳戶](./media/hdinsight-hbase-provision-vnet/hbasewizard3.png)

	<table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>HTTP 使用者名稱</td>
		<td>指定 HDInsight 叢集使用者名稱。</td></tr>
	<tr><td>HTTP 密碼/確認密碼</td>
		<td>指定 HDInsight 叢集使用者密碼。</td></tr>
	<tr><td>為叢集啟用遠端桌面</td>
		<td>選取此核取方塊，以指定遠端桌面使用者的使用者名稱、密碼和到期日期，使其在佈建之後可以遠端進入叢集節點。您也可以稍後在叢集佈建後啟用遠端桌面。如需指示，請參閱<a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">使用 RDP 連接至 HDInsight 叢集</a>。</td></tr>
</table>

6. 在 [儲存體帳戶] 頁面上，提供下列值：

    ![提供 Hadoop HDInsight 叢集的儲存體帳戶](./media/hdinsight-hbase-provision-vnet/hbasewizard4.png)

	<table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>儲存體帳戶</td>
		<td>指定將作為 HDInsight 叢集之預設檔案系統的 Azure 儲存帳號。您可以從三個選項中擇一使用：
		<ul>
			<li><strong>使用現有的儲存體</strong></li>
			<li><strong>建立新的儲存體</strong></li>
			<li><strong>使用其他訂用帳戶的儲存體</strong></li>
		</ul>
		</td></tr>
	<tr><td>帳戶名稱</td>
		<td><ul>
			<li>如果選擇使用現有的儲存體，則在 [帳戶名稱]<strong></strong> 中，請選取現有儲存體帳戶。下拉式清單列出的儲存體帳戶僅限位於與您選擇佈建叢集相同的資料中心。</li>
			<li>如果選擇 [建立新的儲存體]<strong></strong> 或 [從另一個訂用帳戶使用儲存體]<strong></strong> 選項，則您必須提供儲存體帳戶名稱。</li>
		</ul></td></tr>
	<tr><td>帳戶金鑰</td>
		<td>如果選擇 [從另一個訂用帳戶使用儲存體]<strong></strong> 選項，請指定該儲存體帳戶的帳戶金鑰。</td></tr>
	<tr><td>預設容器</td>
		<td><p>指定儲存體帳戶上的預設容器做為 HDInsight 叢集的預設檔案系統。如果您在 [儲存體帳戶] <strong></strong>欄位中選擇了 [使用現有的儲存體] <strong></strong>，且該帳戶中沒有現有的容器，根據預設，將使用叢集名稱建立名稱相同的容器。如果已有使用叢集名稱的容器存在，則會為容器名稱加上序號。例如 mycontainer1、mycontainer2，依此類推。然而，如果現有儲存體帳戶已有容器存在，且其名稱與您指定的叢集名稱不同，您仍可以使用該容器。</p>
        <p>如果選擇建立新儲存體或從另一個 Azure 訂用帳戶使用儲存體，則必須指定預設容器名稱。</p>
    </td></tr>
	<tr><td>其他儲存體帳戶</td>
		<td>視需要指定叢集的其他儲存體帳戶。HDInsight 支援多個儲存體帳戶。叢集可使用的其他儲存體帳戶沒有數量上的限制。但如果您使用 Azure 入口網站建立叢集，則會因為 UI 的限制而只能使用最多七個儲存體帳戶。您所指定的每個其他儲存體帳戶都會在精靈上另外加入一個 [儲存體帳戶]<strong></strong> 頁面，您可在此指定帳戶資訊。以上面的螢幕擷取畫面為例，其未選取其他儲存體帳戶，因此沒有在精靈上加入額外頁面。</td></tr>
</table>按一下向右箭頭。

7. 在 [指令碼動作] 頁面上，選取右下角的核取記號。請不要點選 [加入指令碼動作] 按鈕，因為本教學課程不需要自訂叢集設定。
	
	![設定指令碼動作以自訂 HDInsight HBase 叢集][img-provision-cluster-page5]

	> [AZURE.NOTE]設定期間可使用此頁面自訂叢集。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。
	
若要開始使用新的 HBase 叢集，您可以使用＜[開始在 HDInsight 中搭配使用 HBase 與 Hadoop](../hdinsight-hbase-get-started.md)＞中提供的程序。

##使用 HBase Java RPC API 連接到佈建在虛擬網路中的 HBase 叢集

1.	將基礎結構即服務 (IaaS) 虛擬機器佈建至相同的 Azure 虛擬網路和相同的子網路中。因此，虛擬機器和 HBase 叢集會使用相同的內部 DNS 伺服器來解析主機名稱。若要這麼做，您必須選擇 [從組件庫] 選項，並選取虛擬網路而不是資料中心。如需指示，請參閱[建立執行 Windows Server 的虛擬機器](../virtual-machines-windows-tutorial.md)。標準 Windows Server 2012 映像搭配小型 VM 即已足夠。
	
2.	使用 Java 應用程式從遠端連接到 HBase 時，您必須使用完整網域名稱 (FQDN)。若要決定此名稱，您必須取得 HBase 叢集的連線特定 DNS 尾碼。請使用 Curl 以查詢 Ambari，或使用遠端桌面連接到叢集。

	* **Curl** - 使用下列命令：

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		在傳回的 JavaScript 物件標記法 (JSON) 資料中，找出 "host_name" 項目。這會包含叢集中節點的 FQDN。例如：

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		以叢集名稱開頭的網域名稱部分就是 DNS 尾碼。例如，mycluster.b1.cloudapp.net。

	* **Azure PowerShell** - 使用下列 Azure PowerShell 指令碼以註冊 **Get-ClusterDetail** 函數，並可使用該函數傳回 DNS 尾碼：

			function Get-ClusterDetail(
			    [String]
			    [Parameter( Position=0, Mandatory=$true )]
			    $ClusterDnsName,
				[String]
			    [Parameter( Position=1, Mandatory=$true )]
			    $Username,
				[String]
			    [Parameter( Position=2, Mandatory=$true )]
			    $Password,
			    [String]
			    [Parameter( Position=3, Mandatory=$true )]
			    $PropertyName
				)
			{
			<#
			    .SYNOPSIS 
			     Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
				.Description
				 This command shows the following 4 properties of an HDInsight cluster:
				 1. ZookeeperQuorum (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (supports only HBase type cluster)
					Shows a list of host FQDNs that run the HBase REST server.
				 4. FQDNSuffix (supports all cluster types)
					Shows the FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of HBase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs that run the HBase REST server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows the FQDN suffix of hosts in the cluster.
			#>
			
				$DnsSuffix = ".azurehdinsight.net"
				
				$ClusterFQDN = $ClusterDnsName + $DnsSuffix
				$webclient = new-object System.Net.WebClient
				$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
			
				if($PropertyName -eq "ZookeeperQuorum")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
				}
				if($PropertyName -eq "ZookeeperClientPort")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
				}
				if($PropertyName -eq "HBaseRestServers")
				{
					$Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
					$Response1 = $webclient.DownloadString($Url1)
					$JsonObject1 = $Response1 | ConvertFrom-Json
					$PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'
					
					$Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
					$Response2 = $webclient.DownloadString($Url2)
					$JsonObject2 = $Response2 | ConvertFrom-Json
					foreach ($host_component in $JsonObject2.host_components)
					{
						$ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
						Write-host $ConnectionString
					}
				}
				if($PropertyName -eq "FQDNSuffix")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					$FQDN = $JsonObject.host_components[0].HostRoles.host_name
					$pos = $FQDN.IndexOf(".")
					$Suffix = $FQDN.Substring($pos + 1)
					Write-host $Suffix
				}
			}

		執行 Azure PowerShell 指令碼之後，透過下列命令使用 **Get-ClusterDetail** 函數傳回 DNS 尾碼。使用此命令時，請指定您的 HDInsight HBase 叢集名稱、管理員名稱和管理員密碼。

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		這會傳回 DNS 尾碼。例如，**yourclustername.b4.internal.cloudapp.net**。

	> [AZURE.NOTE]您也可以使用遠端桌面連接到 HBase 叢集 (將連接到前端節點)，再從命令提示字元執行 **ipconfig** 以取得 DNS 尾碼。如需啟用遠端桌面通訊協定 (RDP) 並使用 RDP 連接到叢集的指示，請參閱[使用 Azure 入口網站在 HDInsight 中管理 Hadoop 叢集][hdinsight-admin-portal]。
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!-- 
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster. 

	To make the configuration change:

	1. RDP into the virtual machine. 
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**. 
	- Set **Primary DNS Suffix** to the value obtained in step 2: 

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**. 
	5. Reboot the virtual machine.
-->

若要驗證虛擬機器能夠與 HBase 叢集通訊，請從虛擬機器使用命令 `ping headnode0.<dns suffix>`。例如，ping headnode0.mycluster.b1.cloudapp.net。

若要在 Java 應用程式中使用此資訊，您可以依照＜[使用 Maven 建置在 HDInsight (Hadoop) 上使用 HBase 的 Java 應用程式](hdinsight-hbase-build-java-maven.md)＞(英文) 中的步驟來建立應用程式。若要讓應用程式連接到遠端 HBase 伺服器，請修改此範例中的 **hbase-site.xml** 檔案，以使用 Zookeeper 的 FQDN。例如：

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE]如需 Azure 虛擬網路中的名稱解析的詳細資訊，包括如何使用您自己的 DNS 伺服器，請參閱[名稱解析 (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx)。

##使用 Azure PowerShell 佈建 HBase 叢集

**使用 Azure PowerShell 佈建 HBase 叢集**

1. 開啟 Azure PowerShell 整合式指令碼環境 (ISE)。
2. 複製下列內容，並將其貼到指令碼窗格中：

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"
		
		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 
		
		New-AzureHDInsightCluster -Name $hbaseClusterName `
		                          -ClusterType HBase `
		                          -Version 3.1 `
		                          -Location $location `
		                          -ClusterSizeInNodes $clusterSize `
		                          -Credential $creds `
		                          -VirtualNetworkId $vnetID `
		                          -SubnetName $subNetName `
		                          -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		                          -DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainerName $storageContainerName


3. 按一下 [執行指令碼]，或按 [F5]。
4. 若要驗證叢集，您可以從 Azure 入口網站檢查，或從底部窗格執行下列 Azure PowerShell Cmdlet：

	Get-AzureHDInsightCluster

##後續步驟

在本教學課程中，您已了解如何佈建 HBase 叢集。若要深入了解，請參閱：

- [開始使用 HDInsight](../hdinsight-get-started.md)
- [在 HDInsight 中設定 HBase 複寫](hdinsight-hbase-geo-replication.md) 
- [在 HDInsight 中佈建 Hadoop 叢集](hdinsight-provision-clusters.md) 
- [開始在 HDInsight 中搭配使用 HBase 與 Hadoop](../hdinsight-hbase-get-started.md)
- [使用 HDInsight 中的 HBase 分析 Twitter 情緒](../hdinsight-hbase-twitter-sentiment.md)
- [虛擬網路概觀][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started.md
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment.md
[vnet-overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[vm-create]: ../virtual-machines-windows-tutorial.md

[azure-portal]: https://manage.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "佈建新 HBase 叢集的詳細資料"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "使用指令碼動作以自訂 HBase 叢集"
 

<!---HONumber=July15_HO2-->