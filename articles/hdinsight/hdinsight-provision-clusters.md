<properties
   pageTitle="在 HDInsight 中自訂佈建 Hadoop 叢集 |Microsoft Azure"
   description="了解如何使用 Azure 入口網站、Azure PowerShell、命令列或 .NET SDK 自訂佈建 Azure HDInsight 的叢集。"
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/21/2015"
   ms.author="nitinme"/>

#使用自訂選項在 HDInsight 上佈建 Hadoop 叢集

了解在 Azure HDInsight 上自訂佈建 Hadoop 叢集的不同方式 - 使用 Azure 入口網站、Azure PowerShell、Azure CLI 或 HDInsight .NET SDK。如需佈建 HBase 叢集和 Storm 叢集的指示，請參閱[在 HDInsight 中佈建 HBase 叢集](../hdinsight-hbase-get-started.md)和[開始在 HDInsight 上使用 Storm](../hdinsight-storm-getting-started.md)。請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Hadoop 和 HBase 之間的差別？</a>，以了解如何在兩者之間做選擇。

## 什麼是 HDInsight 叢集？

您有沒有想過為什麼每次討論 Hadoop 或巨量資料時，都一定會提到叢集？ 那是因為 Hadoop 允許大型資料的分散式處理，散佈到叢集的不同節點中。叢集具有主/從架構，包含一個主要 (亦稱為前端節點或名稱節點) 和任意數目的從屬 (亦稱為資料節點)。如需詳細資訊，請參閱 [Apache Hadoop][apache-hadoop]。

![HDInsight Cluster][img-hdi-cluster]

HDInsight 叢集摘要了 Hadoop 實作詳細資料，因此您無需擔心如何與叢集的不同節點通訊。佈建 HDInsight 叢集時，您可以佈建包含 Hadoop 及相關應用程式的 Azure 運算資源。如需詳細資訊，請參閱 [HDInsight 中 Hadoop 的簡介](hdinsight-hadoop-introduction.md)。要變換的資料儲存於 Azure Blob 儲存體。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage.md)。

本文針對佈建叢集的不同方式提供指示。如果您在尋找佈建叢集的快速入門方法，請參閱[開始使用 Azure HDInsight](../hdinsight-get-started.md)。

**必要條件：**

開始執行本文中的指示之前，您必須擁有以下項目：

- Azure 訂閱。Azure 是訂用帳戶型平台。HDInsight Windows PowerShell Cmdlet 會使用您的訂用帳戶執行相關工作。如需取得訂用帳戶的詳細資訊，請參閱[購買選項][azure-purchase-options]、[成員優惠][azure-member-offers]或[免費試用][azure-free-trial]。

##<a id="configuration"></a>組態選項

###Linux 上的叢集

HDInsight 提供在 Azure 上設定 Linux 叢集的選項。如果您熟悉 Linux 或 Unix、要從現有的 Linux Hadoop 方案進行移轉，或想輕鬆整合針對 Linux 所建置的 Hadoop 生態系統元件，請設定 Linux 叢集。如需詳細資訊，請參閱[開始在 Linux 上的 HDInsight 中使用 Hadoop](../hdinsight-hadoop-linux-get-started.md)。

###其他儲存體

在設定期間，您必須指定 Azure Blob 儲存體帳戶和預設容器。叢集以此做為預設儲存位置。您可以選擇性地指定將與叢集相關聯的其他 Azure 儲存體帳戶。

>[AZURE.NOTE]請不要讓多個叢集共用一個 Blob 儲存體容器。不支援此做法。

如需使用次要 Blob 存放區的詳細資訊，請參閱[搭配使用 Azure Blob 儲存體與 HDInsight](../hdinsight-use-blob-storage.md)。

###Metastore

中繼存放區包含 Hive 和 Oozie 中繼資料，例如 Hive 資料表、資料分割、結構描述和資料行。使用中繼存放區能協助您保留 Hive 和 Oozie 中繼資料，因此在佈建新叢集時，您便無需重建 Hive 資料表或 Oozie 工作。依預設，Hive 會使用內嵌資料庫來儲存此資訊。刪除叢集時，嵌入的資料庫將無法保留中繼資料。

### 叢集自訂

您可以在佈建期間使用指令碼來安裝其他元件或自訂組態。這類指令碼可透過**指令碼動作**叫用，指令碼動作是一個組態選項，其可從 Azure 入口網站、HDInsight Windows PowerShell Cmdlet 或 HDInsight .NET SDK 使用。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。


###虛擬網路

[Azure 虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/)可讓您建立安全、持續的網路，其包含解決方案所需的資源。虛擬網路可讓您：

* 在私人網路中將雲端資源連接在一起 (僅限雲端)。

	![diagram of cloud-only configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* 使用虛擬私人網路 (VPN) 將雲端資源連接到本機資料中心網路 (站對站，或點對站)。

	站對站組態可讓您使用硬體 VPN 或路由及遠端存取服務，從資料中心將多項資源連接至 Azure 虛擬網路。

	![diagram of site-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	點對站組態可讓您使用軟體 VPN，將特定資源連接到 Azure 虛擬網路。

	![diagram of point-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

如需虛擬網路特性、優點和功能的詳細資訊，請參閱＜[虛擬網路概觀](http://msdn.microsoft.com/library/azure/jj156007.aspx)＞。

> [AZURE.NOTE]您必須先建立 Azure 虛擬網路，才能佈建 HDInsight 叢集。如需詳細資訊，請參閱＜[虛擬網路組態工作](http://msdn.microsoft.com/library/azure/jj156206.aspx)＞。
>
> Azure HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。
>
> 強烈建議您一個叢集只指定一個子網路。

##<a id="portal"></a> 使用 Azure 入口網站

HDInsight 叢集會使用 Azure Blob 儲存體容器做為預設檔案系統。相同的資料中心上必須要有 Azure 儲存體帳戶，才能夠建立 HDInsight 叢集。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage.md)。如需建立 Azure 儲存體帳戶的詳細資訊，請參閱[如何建立儲存體帳戶](../storage-create-storage-account.md)。


> [AZURE.NOTE]目前只有「東亞」、「東南亞」、「北歐」、「西歐」、「美國東部」、「美國西部」、「美國中北部」和「美國中南部」等區域可以代管 HDInsight 叢集。

**使用自訂建立選項建立 HDInsight 叢集**

1. 登入 [Azure 入口網站][azure-management-portal]。
2. 按一下頁面底部的 [+新增]，然後依序按一下 [資料服務]、[HDInsight]、[自訂建立]。
3. 在 [叢集詳細資料] 頁面上，輸入或選擇下列值：

	![提供 Hadoop HDInsight 叢集詳細資料][image-customprovision-page1]

    <table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>叢集名稱</td>
		<td><p>為叢集命名。</p>
			<ul>
			<li>網域名稱系統 (DNS) 名稱的開頭與結尾都必須是英數字元，且可包含連字號。</li>
			<li>欄位必須是 3 到 63 個字元的字串。</li>
			</ul></td></tr>
	<tr><td>叢集類型</td>
		<td>在叢集類型中選取 [Hadoop]<strong></strong>。</td></tr>
	<tr><td>作業系統</td>
		<td>選取 [Windows Server 2012 R2 Data Center]<b></b> 來佈建 Windows 叢集。若要佈建 Linux 叢集，請參閱<a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">在 HDInsight 中佈建　Hadoop Linux 叢集</a>。</td></tr>
	<tr><td>HDInsight 版本</td>
		<td>選擇版本。若是 Hadoop，預設值為採用 Hadoop 2.4 的 HDInsight 3.1 版。</td></tr>
	</table>輸入或選取如資料表中所示的值，然後按一下向右箭頭。

4. 在 [設定叢集] 頁面上，輸入或選取下列值：

	![提供 Hadoop HDInsight 叢集詳細資料](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
<tr><th>名稱</th><th>值</th></tr>
<tr><td>資料節點</td><td>您要部署的資料節點數。請建立單一節點叢集，以供測試之用。<br />叢集大小限制會隨著 Azure 訂用帳戶而不同。若要提高限制，請與 Azure 帳務支援人員連絡。</td></tr>
<tr><td>區域/虛擬網路</td><td><p>請選擇與您在前一個程序中建立的儲存體帳戶相同的區域。使用 HDInsight 時，儲存體帳戶必須位於相同的區域中。後續進行設定時，您只能選擇此處指定之相同區域中的儲存體帳戶。</p><p>可用的區域有：「東亞」<strong></strong>、「東南亞」<strong></strong>、「北歐」<strong></strong>、「西歐」<strong></strong>、「美國東部」<strong></strong>、「美國西部」<strong></strong>、「美國中北部」<strong></strong>和「美國中南部」<strong></strong>。<br/>如果您已建立 Azure 虛擬網路，您可以選取將設定 HDInsight 叢集為使用的網路。</p><p>如需建立 Azure 虛擬網路的詳細資訊，請參閱<a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">虛擬網路組態工作</a>。</p></td></tr>
<tr><td>前端節點大小</td><td><p>選取前端節點的虛擬機器 (VM) 大小。</p></td></tr>
<tr><td>資料節點大小</td><td><p>選取資料節點的 VM 大小。</p></td></tr>
</table>>[AZURE.NOTE]根據選擇的 VM ，您的成本可能會有所不同。HDInsight 針對叢集節點會使用所有標準層 VM。如需 VM 大小對您價格影響的相關資訊，請參閱 <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 定價</a>。


5. 在 [Configure Cluster User] 頁面上，提供下列值：

    ![提供 Hadoop HDInsight 叢集使用者和中繼存放區詳細資料](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>HTTP 使用者名稱</td>
		<td>指定 HDInsight 叢集使用者名稱。</td></tr>
	<tr><td>HTTP 密碼/確認密碼</td>
		<td>指定 HDInsight 叢集使用者密碼。</td></tr>
	<tr><td>為叢集啟用遠端桌面</td>
		<td>選取此核取方塊，以指定遠端桌面使用者的使用者名稱、密碼和到期日期，使其在佈建之後可以遠端進入叢集節點。您也可以稍後在叢集佈建後啟用遠端桌面。如需指示，請參閱<a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">使用 RDP 連接至 HDInsight 叢集</a>。</td></tr>
	<tr><td>輸入 Hive/Oozie 中繼存放區</td>
		<td>勾選此核取方塊，在與叢集相同的資料中心上指定 SQL 資料庫，以做為 Hive/Oozie 中繼存放區使用。如果您選取此核取方塊，則必須在精靈的後續頁面指定 Azure SQL Database 的詳細資料。即使在將叢集刪除之後，如果您想要保留 Hive/Oozie 工作的相關中繼資料，此選項將會很有幫助。</td></tr>
	</td></tr>
</table>按一下向右箭頭。

6. 在 [設定 Hive/Oozie 中繼存放區] 頁面上，提供下列值：

    ![提供 Hadoop HDInsight 叢集使用者](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

	指定要做為 Hive/OOzie Metastore 的 Azure SQL Database。您可以為 Hive 和 Oozie Metastore 指定相同的資料庫。此 SQL 資料庫必須與 HDInsight 叢集位於相同的資料中心內。清單方塊只會列出與您在 [叢集詳細資料]<strong></strong> 頁面上指定之相同資料中心內的 SQL 資料庫。同時請指定使用者名稱及密碼以連線到所選取的 Azure SQL Database。

    >[AZURE.NOTE]用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [**設定**]，然後在 [**Azure 服務**] 按一下 [**是**]，再按 [**儲存**]。

    按一下向右箭頭。


7. 在 [**儲存體帳戶**] 頁面上，提供下列值：

    ![提供 Hadoop HDInsight 叢集的儲存體帳戶](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png)

	<table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>儲存體帳戶</td>
		<td>指定將作為 HDInsight 叢集之預設檔案系統的 Azure 儲存帳號。您可以從三個選項中擇一使用：
		<ul>
			<li><strong>使用現有的儲存體</strong></li>
			<li><strong>建立新的儲存體</strong></li>
			<li><strong>從另一個訂用帳戶使用儲存體</strong></li>
		</ul>
		</td></tr>
	<tr><td>帳戶名稱</td>
		<td><ul>
			<li>如果要使用現有儲存體，請在 [帳戶名稱]<strong></strong> 中選取現有儲存體帳戶。下拉式清單列出的儲存體帳戶僅限位於與您選擇佈建叢集相同的資料中心。</li>
			<li>如果選擇 [建立新的儲存體]<strong></strong> 或 [從另一個訂用帳戶使用儲存體]<strong></strong> 選項，則您必須提供儲存體帳戶名稱。</li>
		</ul></td></tr>
	<tr><td>帳戶金鑰</td>
		<td>如果選擇 [從另一個訂用帳戶使用儲存體]<strong></strong> 選項，請指定該儲存體帳戶的帳戶金鑰。</td></tr>
	<tr><td>預設容器</td>
		<td><p>指定儲存體帳戶上的預設容器做為 HDInsight 叢集的預設檔案系統。如果您在 [儲存體帳戶] <strong></strong>欄位中選擇了 [使用現有的儲存體] <strong></strong>，且該帳戶中沒有現有的容器，根據預設，將使用叢集名稱建立名稱相同的容器。如果已有使用叢集名稱的容器存在，則會為容器名稱加上序號。例如 mycontainer1、mycontainer2，依此類推。然而，如果現有儲存體帳戶已有容器存在，且其名稱與您指定的叢集名稱不同，您仍可以使用該容器。</p>
        <p>如果選擇建立新的儲存體或從另一個 Azure 訂用帳戶使用儲存體，則必須指定預設容器名稱。</p>
    </td></tr>
	<tr><td>其他儲存體帳戶</td>
		<td>HDInsight 支援多個儲存體帳戶。叢集可使用的其他儲存體帳戶沒有數量上的限制。但如果您使用 Azure 入口網站建立叢集，則會因為 UI 的限制而只能使用最多七個儲存體帳戶。您所指定的每個其他儲存體帳戶都會在精靈上另外加入一個 **儲存體帳戶** 頁面，您可在此指定帳戶資訊。以上方的螢幕擷取畫面為例，在選取一個其他儲存體帳戶後，對話方塊因此加入了頁面 5。</td></tr>
</table>按一下向右箭頭。

7. 如果您選擇為叢集設定其他儲存體，請在 [儲存體帳戶] 頁面上，輸入其他儲存體帳戶的帳戶資訊：

	![提供 HDInsight 叢集的其他儲存體詳細資料](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png)

    在這裡，您可以選擇現有儲存體、建立新儲存體或使用其他 Azure 訂用帳戶的儲存體。提供值的程序會與上個步驟類似。

    > [AZURE.NOTE]在您為 HDInsight 叢集選擇 Azure 儲存體帳戶後，您將無法刪除帳戶，也無法將帳戶變更為不同的帳戶。

8. 若要在建立叢集時自訂叢集，請在 [指令碼動作] 頁面上，按一下 [加入指令碼動作] 以提供有關您將執行的自訂指令碼詳細資料。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。

	![設定指令碼動作以自訂 HDInsight 叢集](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page7.png)

	<table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>名稱</td>
		<td>指定指令碼動作的名稱。</td></tr>
	<tr><td>指令碼 URI</td>
		<td>針對自訂叢集所叫用的指令碼，指定統一資源識別項 (URI)。</td></tr>
	<tr><td>節點類型</td>
		<td>指定執行自訂指令碼的節點。您可以選擇 [所有節點]<b></b>、[僅限前端節點]<b></b> 或 [僅限資料節點]<b></b>。
	<tr><td>參數</td>
		<td>如果指令碼要求，請指定參數。</td></tr>
</table>您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。加入指令碼之後，請按一下核取記號以開始佈建叢集。

##<a id="powershell"></a> 使用 Azure PowerShell
Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。本節提供如何使用 Azure PowerShell 佈建 HDInsight 叢集的指示。如需設定工作站以執行 HDInsight Windows Powershell Cmdlet 的相關資訊，請參閱[安裝並設定 Azure PowerShell](../install-configure-powershell.md)。如需搭配使用 Azure PowerShell 與 HDInsight 的詳細資訊，請參閱[使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)。如需 HDInsight Windows PowerShell Cmdlet 的清單，請參閱 [HDInsight Cmdlet 參考資料][hdinsight-powershell-reference]。

> [AZURE.NOTE]本節的指令碼可用來設定 Azure 虛擬網路上的 HDInsight 叢集，但不會建立 Azure 虛擬網路。如需建立 Azure 虛擬網路的相關資訊，請參閱[虛擬網路組態工作](http://msdn.microsoft.com/library/azure/jj156206.aspx)。

以下是使用 Azure PowerShell 佈建 HDInsight 叢集時所需執行的程序：

- 建立 Azure 儲存體帳戶
- 建立 Azure Blob 容器
- 建立 HDInsight 叢集

您可以使用 Windows PowerShell 主控台或 Windows PowerShell 整合式指令碼環境 (ISE) 來執行指令碼。

HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。必須要有 Azure 儲存體帳戶和儲存體容器，才能夠建立 HDInsight 叢集。儲存體帳戶必須與 HDInsight 叢集位於相同的資料中心。

**連接到您的 Azure 帳戶**

		Add-AzureAccount

系統會提示您輸入 Azure 帳號認證。

**建立 Azure 儲存體帳戶**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

如果您已有儲存體帳戶，但不知道帳戶名稱和帳戶金鑰，可以使用下列 Windows PowerShell 命令來擷取資訊：

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**建立 Azure Blob 儲存體容器**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

在儲存體帳戶和 Blob 容器準備就緒後，您即可建立叢集。

**佈建 HDInsight 叢集**

> [AZURE.NOTE]Azure PowerShell Cmdlet 是唯一建議用來在 HDInsight 叢集中變更組態變數的方法。透過遠端桌面連接至叢集時對 Hadoop 組態檔所做的變更，可能會在叢集修補時遭到覆寫。透過 Azure PowerShell 所設定的組態值在叢集修補時會保留下來。

- 從 Azure PowerShell 主控台視窗執行下列命令：

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
        $hadoopUserPassword = "<HadoopUserPassword>"

        $secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

	>[AZURE.NOTE]$hadoopUserName 和 $hadoopUserPassword 命令用來建立叢集的 Hadoop 使用者帳戶。您將使用此帳戶來連接到叢集並執行工作。如果從 Azure 入口網站使用快速建立選項佈建叢集，則預設 Hadoop 使用者名稱是 "admin"。請不要將此帳戶與遠端桌面通訊協定 (RDP) 使用者帳戶相混淆。RDP 使用者帳戶必須與 Hadoop 使用者帳戶不同。如需詳細資訊，請參閱[使用 Azure 管理入口網站在 HDInsight 上管理 Hadoop 叢集][hdinsight-admin-portal]。

	叢集佈建可能需要幾分鐘的時間才會完成。

	![HDI.CLI.Provision][image-hdi-ps-provision]



**使用自訂組態選項佈建 HDInsight 叢集**

佈建叢集時，您可以使用其他組態選項，例如連接至多個 Azure Blob 儲存體容器、使用虛擬網路，或使用 Azure SQL Database 來處理 Hive 和 Oozie 中繼存放區。這可讓您區隔資料和中繼資料的存留期與叢集的存留期。

> [AZURE.NOTE]Windows PowerShell Cmdlet 是唯一建議用來在 HDInsight 叢集中變更組態變數的方法。透過遠端桌面連接至叢集時對 Hadoop 組態檔所做的變更，可能會在叢集修補時遭到覆寫。透過 Azure PowerShell 所設定的組態值在叢集修補時會保留下來。

- 從 Windows PowerShell 視窗執行下列命令：

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<ClusterName>"
		$location = "<MicrosoftDataCenter>"
		$clusterNodes = <ClusterSizeInNodes>

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = "<DefaultFileSystemContainerName>"

		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		# Create a Blob storage container
		$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[AZURE.NOTE]用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [設定]，然後在 [Azure 服務] 按一下 [是]，再按 [儲存]。

**列出 HDInsight 叢集**

- 從 Azure PowerShell 主控台視窗執行下列命令，以列出 HDInsight 叢集並確認是否已成功佈建叢集：

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a> 使用 Azure CLI

> [AZURE.NOTE]從 2014/8/29 開始，無法使用 Azure CLI 建立叢集與 Azure 虛擬網路的關聯。

另一個佈建 HDInsight 叢集的選項是 Azure CLI。Azure CLI 會在 Node.js 中實作。此工具可在任何支援 Node.js 的平台上使用，包括 Windows、Mac 和 Linux。您可以從下列位置安裝 CLI：

- **Node.js SDK** - <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **Azure CLI** - <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

如需如何使用 Azure CLI 的一般指南，請參閱[適用於 Mac、Linux 和 Linux 的 Azure CLI](../xplat-cli.md)。

上述指示將引導您如何在 Linux 和 Windows 上安裝跨平台命令列，以及接著如何使用命令列來佈建叢集。

- [設定適用於 Linux 的 Azure CLI](#clilin)
- [設定適用於 Windows 的 Azure CLI](#cliwin)
- [使用 Azure CLI 佈建 HDInsight 叢集](#cliprovision)

#### <a id="clilin"></a>設定適用於 Linux 的 Azure CLI

執行下列程序來設定您的 Linux 電腦使用 Azure 命令列工具 (Azure CLI)：

- 使用 Node.js 套件管理員 (NPM) 安裝 Azure CLI
- 連線到您的 Azure 訂用帳戶

**使用 NPM 安裝 Azure CLI**

1.	在 Linux 電腦上開啟終端機視窗，然後執行下列命令：

		sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	執行下列命令以驗證安裝：

		azure hdinsight -h

	您可以在不同層級使用 *-h* 參數，以顯示說明資訊。例如：

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**連線到您的 Azure 訂用帳戶**

使用 Azure CLI 之前，您必須先設定工作站與 Azure 之間的連線。Azure CLI 會使用您的 Azure 訂閱資訊來連線至您的帳戶。這項資訊可從 Azure 的發佈設定檔案取得。接著可以匯入發佈設定檔成為 Azure CLI 後續作業所用的永久本機組態設定。您只需匯入一次發佈設定。

> [AZURE.NOTE]發佈設定檔案包含敏感資訊。Microsoft 建議您刪除此檔案，或另採相關步驟加密包含此檔案的使用者資料夾。在 Windows 中，修改資料夾屬性或使用 BitLocker Drive Encryption。


1.	開啟終端機視窗。
2.	執行下列命令，以登入您的 Azure 訂用帳戶：

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	此命令會開啟可下載發行設定檔案的網頁。如果網頁未開啟，請按一下終端機視窗中的連結以開啟瀏覽器頁面並登入入口網站。

3.	將發行設定檔案下載至電腦。
5.	在命令提示字元視窗中執行下列命令，以匯入發佈設定檔案：

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>設定適用於 Windows 的 Azure CLI

執行下列程序來設定您的 Windows 電腦使用 Azure CLI：

- 安裝 Azure CLI (使用 NPM 或 Windows Installer)
- 下載及匯入 Azure 帳號發佈設定


Azure CLI 可使用 NPM 或 Windows Installer 進行安裝。Microsoft 建議您僅使用下列兩個選項之一來進行安裝。

**使用 NPM 安裝 Azure CLI**

1.	瀏覽至 **www.nodejs.org**。
2.	按一下 [安裝]，並依照指示使用預設設定。
3.	從您的工作站開啟 [命令提示字元] (或是 *Azure 命令提示字元*或 *VS2012 開發人員命令提示字元*)。
4.	在命令提示字元視窗中執行下列命令：

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]如果您收到錯誤，指出找不到 NPM 命令，請驗證下列路徑是否在 PATH 環境變數中：<i>C:\Program Files (x86)\nodejs;C:\Users[username]\AppData\Roaming\npm</i> 或 <i>C:\Program Files\nodejs;C:\Users[username]\AppData\Roaming\npm</i>

5.	執行下列命令以驗證安裝：

		azure hdinsight -h

	您可以在不同層級使用 *-h* 參數，以顯示說明資訊。例如：

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**使用 Windows Installer 安裝 Azure CLI**

1.	瀏覽至 **http://azure.microsoft.com/downloads/**2.	向下捲動至 [**命令列工具**] 區段，然後按一下 [**Azure 命令列介面**]，並依照 Web Platform Installer 精靈操作。

**下載及匯入發佈設定**

使用 Azure CLI 之前，您必須先設定工作站與 Azure 之間的連線。Azure CLI 會使用您的 Azure 訂閱資訊來連線至您的帳戶。這項資訊可從 Azure 的發佈設定檔案取得。接著可以匯入發佈設定檔成為 Azure CLI 後續作業所用的永久本機組態設定。您只需匯入一次發佈設定。

> [AZURE.NOTE]發佈設定檔案包含敏感資訊。Microsoft 建議您刪除此檔案，或另採相關步驟加密包含此檔案的使用者資料夾。在 Windows 中，修改資料夾屬性或使用 BitLocker。


1.	開啟 [命令提示字元]。
2.	執行下列命令來下載發佈設定檔案：

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	此命令會開啟可下載發行設定檔案的網頁。

3.	出現儲存檔案的提示時，請按一下 [儲存] 並提供檔案必須儲存的位置。
5.	在命令提示字元視窗中執行下列命令，以匯入發佈設定檔案：

		azure account import <path/to/the/file>

#### <a id="cliprovision"></a>使用 Azure CLI 佈建 HDInsight 叢集

以下是使用 Azure CLI 佈建 HDInsight 叢集時所需執行的程序：

- 建立 Azure 儲存體帳戶
- 佈建叢集

**建立 Azure 儲存體帳戶**

HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。必須要有 Azure 儲存體帳戶，才能夠建立 HDInsight 叢集。儲存體帳戶必須位於相同的資料中心內。

- 在命令提示字元視窗中執行下列命令，以建立 Azure 儲存體帳戶：

		azure storage account create [options] <StorageAccountName>

	出現位置提示時，請選取可佈建 HDInsight 叢集的位置。儲存體必須與 HDInsight 叢集位於相同的位置。目前只有「東亞」、「東南亞」、「北歐」、「西歐」、「美國東部」、「美國西部」、「美國中北部」和「美國中南部」等區域可以代管 HDInsight 叢集。

如需使用 Azure 入口網站建立 Azure 儲存體帳戶的相關資訊，請參閱[建立、管理或刪除儲存體帳戶](../storage-create-storage-account.md)。

如果您已經有儲存體帳戶，但不知道帳戶名稱和帳戶金鑰，則可使用下列命令來擷取資訊：

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

如需使用 Azure 入口網站取得資訊的詳細資訊，請參閱[建立、管理或刪除儲存體帳戶](../storage-create-storage-account.md)的*如何：檢視、複製及重新產生儲存體存取金鑰*一節。

HDInsight 叢集同時要求儲存體帳戶內含有容器。如果您所提供的儲存體帳戶沒有容器，則 *azure hdinsight cluster create* 命令會提示您輸入容器名稱並建立容器。不過，如果您想要預先建立容器，您可以使用下列命令：

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

在儲存體帳戶和 Blob 容器準備就緒後，您即可建立叢集。

**佈建 HDInsight 叢集**

- 在命令提示字元視窗中執行下列命令：

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**使用組態檔佈建 HDInsight 叢集**

一般而言，您會佈建 HDInsight 叢集、執行工作，然後將此叢集刪除，以降低成本。Azure CLI 可讓您選擇將組態儲存至檔案，以便在每次佈建叢集時重複使用。

- 在命令提示字元視窗中執行下列命令：


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>

	>[AZURE.NOTE]用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [**設定**]，然後在 [**Azure 服務**] 按一下 [**是**]，再按 [**儲存**]。


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**列出並顯示叢集詳細資料**

- 使用下列命令，以列出並顯示叢集詳細資料：

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**刪除叢集**

- 使用下列命令刪除叢集：

		azure hdinsight cluster delete <ClusterName>



##<a id="sdk"></a> 使用 HDInsight .NET SDK
HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您能夠輕鬆地從 .NET Framework 應用程式使用 HDInsight。

以下是使用 SDK 佈建 HDInsight 叢集時必須執行的程序：

- 安裝 HDInsight .NET SDK
- 建立自我簽署憑證
- 建立主控台應用程式
- 執行應用程式


**安裝 HDInsight .NET SDK**

您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 安裝最新發佈的 SDK 組建。下一個程序會顯示相關指示。

**建立自我簽署憑證**

建立自我簽署憑證，將它安裝到工作站，然後上傳至 Azure 訂用帳戶。如需指示，請參閱[建立自我簽署憑證](http://go.microsoft.com/fwlink/?LinkId=511138)。


**建立 Visual Studio 主控台應用程式**

1. 開啟 Visual Studio 2013。

2. 從 [檔案] 功能表中，按一下 [新增]，再按 [專案]。

3. 在 [**新增專案**] 中，輸入或選取下列值：

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">屬性</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">值</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">類別</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">範本/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">範本</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">主控台應用程式</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名稱</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. 按一下 [確定] 以建立專案。

5. 在 [工具] 功能表中按一下 [Nuget 套件管理員]，然後按一下 [Package Manager Console]。

6. 在主控台中執行下列命令，以安裝套件：

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	此命令會將 .NET 程式庫及其參考加入至目前的 Visual Studio 專案。

7. 從 [方案總管] 中，按兩下 [Program.cs] 將它開啟。

8. 在檔案頂端新增下列 using 陳述式：

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. 在 Main() 函數中，複製並貼上下列程式碼：

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate you created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. 取代 Main() 函數開頭處的變數。

**執行應用程式**

在 Visual Studio 中開啟應用程式後，按 **F5** 以執行應用程式。主控台視窗會開啟並顯示應用程式的狀態。建立 HDInsight 叢集可能需要幾分鐘的時間。



##<a id="nextsteps"></a> 後續步驟
在本文中，您學到幾種佈建 HDInsight 叢集的方法。若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight](../hdinsight-get-started.md) - 了解如何開始使用 HDInsight 叢集
* [使用 Sqoop 搭配 HDInsight](hdinsight-use-sqoop.md) - 了解如何在 HDInsight 與 SQL Database 或 SQL Server 之間複製資料
* [使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md) - 了解如何使用 PowerShell 處理 HDInsight
* [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md) - 了解如何以程式設計方式提交工作至 HDInsight
* [Azure HDInsight SDK 文件][hdinsight-sdk-documentation] - 探索 HDInsight SDK


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started.md

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli.md

[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png "提供 Hadoop HDInsight 叢集詳細資料"
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png "提供 Hadoop HDInsight 叢集使用者"
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png "提供 Hadoop HDInsight 叢集的儲存體帳戶詳細資料"
[image-customprovision-page5]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png "提供 Hadoop HDInsight 叢集的其他儲存體帳戶詳細資料"
[image-customprovision-page6]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png "設定指令碼動作以自訂 HDInsight 叢集"


[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "列出和顯示叢集"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: hdinsight-use-sqoop.md "搭配 HDInsight 使用 Sqoop"
 

<!---HONumber=62-->