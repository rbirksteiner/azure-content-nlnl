<properties 
   pageTitle="設定兩個 Azure 虛擬網路之間的 DNS | Microsoft Azure" 
   description="了解如何設定兩個虛擬網路之間的 VPN 連線和網域名稱解析，以及如何設定 HBase 異地複寫。" 
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

# 設定兩個 Azure 虛擬網路之間的 DNS

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 


了解如何新增與設定 Azure 虛擬網路的 DNS 伺服器，以處理虛擬網路內部與虛擬網路之間的名稱解析。

本教學課程是建立 HBase 異地複寫[系列][hdinsight-hbase-geo-replication]的第二部分：

- [設定兩個虛擬網路之間的 VPN 連線][hdinsight-hbase-geo-replication-vnet]
- 設定虛擬網路的 DNS (本教學課程)
- [設定 HBase 異地複寫][hdinsight-hbase-geo-replication]


下圖說明您在[設定兩個虛擬網路之間的 VPN 連線][hdinsight-hbase-geo-replication-vnet]中所建立的兩個虛擬網路：

![HDInsight HBase 複寫虛擬網路圖表][img-vnet-diagram]

##必要條件
開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **具有 Azure PowerShell 的工作站**。請參閱[安裝和使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。

	執行 PowerShell 指令碼之前，請確定您已使用下列 Cmdlet 連接到 Azure 訂用帳戶：

		Add-AzureAccount

	如果您有多個 Azure 訂用帳戶，請使用下列 Cmdlet 設定目前的訂用帳戶：

		Select-AzureSubscription <AzureSubscriptionName>

- **具備 VPN 連線的兩個 Azure 虛擬網路**。如需相關指示，請參閱[設定兩個 Azure 虛擬網路之間的 VPN 連線][hdinsight-hbase-geo-replication-vnet]。

>[AZURE.NOTE]Azure 服務名稱和虛擬機器名稱必須是唯一的。本教學課程中所使用的名稱為 Contoso-[Azure Service/VM name]-[EU/US]。例如，Contoso-VNet-EU 是指位於北歐資料中心的 Azure 虛擬網路；Contoso-DNS-US 是指位於美國東部資料中心的 DNS 伺服器 VM。您必須提供自己的名稱。
 
 
##建立要做為 DNS 伺服器使用的 Azure 虛擬機器

**在 Contoso-VNet-EU 內部建立名為 Contoso-DNS-EU 的虛擬機器**

1.	依序按一下 [**新增**]、[**計算**]、[**虛擬機器**]、[**從組件庫**]。
2.	選擇 [**Windows Server 2012 R2 Datacenter**]。
3.	輸入：
	- **虛擬機器名稱**：Contoso-DNS-EU
	- **新的使用者名稱**： 
	- **新密碼**： 
4.	輸入：
	- **雲端服務**：建立新的雲端服務
	- **區域/同質群組/虛擬網路**：(選取 [Contoso-VNet-EU])
	- **虛擬網路子網路**：Subnet-1
	- **儲存體帳戶**：使用自動產生的儲存體帳戶
	
		雲端服務名稱會與虛擬機器名稱相同。在此例中是 Contoso-DNS-EU。對於後續的虛擬機器，我可以選擇使用相同的雲端服務。相同雲端服務下的所有虛擬機器會共用相同的虛擬網路和網域尾碼。

		儲存體帳戶用來儲存虛擬機器映像檔。 
	- **端點**：(向下捲動並選取 [**DNS**]) 

建立虛擬機器之後，請找出內部 IP 和外部 IP。

1.	按一下虛擬機器名稱 (**Contoso-DNS-EU**)。
2.	按一下 [**儀表板**]。
3.	請記下：
	- 公用虛擬 IP 位址
	- 內部 IP 位址


**在 Contoso-VNet-US 內部建立名為 Contoso-DNS-US 的虛擬機器**

- 重複相同的程序來建立附帶下列值的虛擬機器：
	- 虛擬機器名稱：Contoso-DNS-US
	- 區域/同質群組/虛擬網路：選取 [Contoso-VNET-US]
	- 虛擬網路子網路：Subnet-1
	- 儲存體帳戶：使用自動產生的儲存體帳戶
	- 端點：(選取 DNS)

##設定兩個虛擬機器的靜態 IP 位址

DNS 伺服器需要靜態 IP 位址。此步驟無法在 Azure 入口網站中完成。您將使用 Azure PowerShell。

**設定兩個虛擬機器的靜態 IP 位址**

1. 開啟 Windows PowerShell ISE。
2. 執行下列 cmdlet。  

		Add-AzureAccount
		Select-AzureSubscription [YourAzureSubscriptionName]
		
		Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
		Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

	ServiceName 是指雲端服務名稱。因為 DNS 伺服器是雲端服務的第一個虛擬機器，所以雲端服務名稱會是虛擬機器名稱。

	為符合您所具備的名稱，您可能必須更新 ServiceName 和名稱。


##新增兩個虛擬機器的 DNS 伺服器角色

**新增 Contoso-DNS-EU 的 DNS 伺服器角色**

1.	從 Azure 入口網站，按一下左側的 [**虛擬機器**]。 
2.	按一下 [**Contoso-DNS-EU**]。
3.	按一下上方的 [**儀表板**]。
4.	按一下底部的 [**連接**]，並依照指示透過 RDP 連接到虛擬機器。
2.	在 RDP 工作階段中，按一下左下角的 [Windows] 按鈕以開啟 [開始] 畫面。
3.	按一下 [**伺服器管理員**] 磚。
4.	按一下 [**新增角色與功能**]。
5.	按 [**下一步**]
6.	選取 [角色型或功能型安裝]，然後按 [下一步]。
7.	選取您的 DNS 虛擬機器 (它應該已反白顯示)，然後按 [**下一步**]。
8.	核取 [**DNS 伺服器**]。
9.	按一下 [**新增功能**]，然後按一下 [**繼續**]。
10.	按三次 [**下一步**]，然後按一下 [**安裝**]。 

**新增 Contoso-DNS-US 的 DNS 伺服器角色**

- 重複步驟以將 DNS 角色新增至 **Contoso-DNS-US**。

##將 DNS 伺服器指派給虛擬網路

**註冊兩個 DNS 伺服器**

1.	從 Azure 入口網站，依序按一下 [**新增**]、[**網路服務**]、[**虛擬網路**]、[**註冊 DNS 伺服器**]。
2.	輸入：
	- **名稱**：Contoso-DNS-EU
	- **DNS 伺服器 IP 位址**：10.1.0.4 – 此 IP 位址必須符合 DNS 伺服器的虛擬機器 IP 位址。
	 
3.	重複此程序，使用下列設定註冊 Contoso-DNS-US：
	- **名稱**：Contoso-DNS-US
	- **DNS 伺服器 IP 位址**：10.2.0.4

**將兩個 DNS 伺服器指派給兩個虛擬網路**

1.	按一下管理入口網站左側窗格的 [**網路**]。
2.	按一下 [**Contoso-VNet-EU**]。
3.	按一下 [**設定**]。
4.	在 [**DNS 伺服器**] 區段中選取 [**Contoso-DNS-EU**]。
5.	按一下頁面底部的 [**儲存**]，然後按一下 [**是**] 加以確認。
6.	重複此程序來將 **Contoso-DNS-US** DNS 伺服器指派給 **Contoso-VNet-US** 虛擬網路。

已部署至虛擬網路的所有虛擬機器都必須重新開機，才能更新 DNS 伺服器設定。

**重新啟動虛擬機器**

1. 從 Azure 入口網站，按一下左側的 [**虛擬機器**]。
2. 按一下 [**Contoso-DNS-EU**]。
3. 按一下上方的 [**儀表板**]。
4. 按一下底部的 [**重新啟動**]。
5. 重複相同步驟，以重新啟動 **Contoso-DNS-US**。


##設定 DNS 條件轉寄站

每個虛擬網路上的 DNS 伺服器只可以解析該虛擬網路內部的 DNS 名稱。您必須設定指向對等 DNS 伺服器的條件轉寄站，以進行對等虛擬網路中的名稱解析。

若要設定條件轉寄站，您必須知道兩個 DNS 伺服器的網域尾碼。DNS 尾碼可以不同，視您建立虛擬機器時所使用的雲端服務組態而定。您必須為虛擬網路中使用的每個 DNS 尾碼新增條件轉寄站。

**尋找兩個 DNS 伺服器的網域尾碼**

1. RDP 到 **Contoso-DNS-EU**。
2. 開啟 Windows PowerShell 主控台或命令提示字元。
3. 執行 **ipconfig**，並記下**連線特定 DNS 尾碼**。
4. 請勿關閉 RDP 工作階段，您仍然需要它。 
5. 重複相同步驟來找出 **Contoso-DNS-US** 的**連線特定 DNS 尾碼**。


**設定 DNS 轉寄站**
 
1.	從 RDP 工作階段到 **Contoso-DNS-EU**，按一下左下方的 Windows 鍵。
2.	按一下 [**系統管理工具**]。
3.	按一下 [**DNS**]。
4.	在左側窗格中，依序展開 [**DSN**]、[**Contoso-DNS-EU**]。
5.	輸入以下資訊：
	- **DNS 網域**：輸入 Contoso-DNS-US 的 DNS 尾碼。例如：Contoso-DNS-US.b5.internal.cloudapp.net。
	- **主要伺服器的 IP 位址**：輸入 10.2.0.4，也就是 Contoso-DNS-US 的 IP 位址。
6.	按下 **ENTER** 鍵，然後按一下 [**確定**]。現在您可以從 Contoso-DNS-EU 解析 Contoso-DNS-US 的 IP 位址。
7.	重複這些步驟，以將 DNS 轉寄站新增至附帶下列值之 Contoso-DNS-US 虛擬機器上的 DNS 服務：
	- **DNS 網域**：輸入 Contoso-DNS-EU 的 DNS 尾碼。 
	- **主要伺服器的 IP 位址**：輸入 10.2.0.4，也就是 Contoso-DNS-EU 的 IP 位址。

##測試虛擬網路之間的名稱解析

現在您可以測試虛擬網路之間的主機名稱解析。依預設，Ping 會遭到防火牆封鎖。您可以使用 nslookup 來解析對等網路中的 DNS 伺服器虛擬機器 (您必須使用 FQDN)。


##後續步驟

在本教學課程中，您已經學會如何透過 VPN 連線來設定虛擬網路之間的名稱解析。本系列中的其他兩篇文章涵蓋下列內容：

- [設定兩個 Azure 虛擬網路之間的 VPN 連線][hdinsight-hbase-geo-replication-vnet]
- [設定 HBase 異地複寫][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[powershell-install]: ../install-configure-powershell.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png

<!---HONumber=July15_HO2-->