<properties 
   pageTitle="設定兩個虛擬網路之間的 VPN 連線 | Microsoft Azure" 
   description="了解如何設定兩個 Azure 虛擬網路之間的 VPN 連線和網域名稱解析，以及如何設定 HBase 異地複寫。" 
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

# 設定兩個 Azure 虛擬網路之間的 VPN 連線  

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 

Azure 虛擬網路的站對站連線會使用 VPN 閘道來提供採用 Ipsec/IKE 的安全通道。VNet 可位於不同的訂用帳戶和不同的區域。您甚至可以使用多網站組態來結合 VNet 對 VNet 通訊。VNet 對 VNet 連線的原因有幾種：

- 跨區域的異地備援和異地目前狀態 
- 具有嚴密隔離界限的區域性多層式應用程式 
- 在 Azure 中的跨訂用帳戶、組織間通訊

如需詳細資訊，請參閱[設定 VNet 對 VNet 連線](https://msdn.microsoft.com/library/azure/dn690122.aspx)。

若要觀看相關影片：

> [AZURE.VIDEO configure-the-vpn-connectivity-between-two-azure-virtual-networks]

本教學課程是建立 HBase 異地複寫[系列][hdinsight-hbase-replication]的一部分。

- 設定兩個虛擬網路之間的 VPN 連線 (本教學課程)
- [設定虛擬網路的 DNS][hdinsight-hbase-geo-replication-DNS]
- [設定 HBase 異地複寫][hdinsight-hbase-geo-replication]

下圖說明您將在本教學課程中建立的兩個虛擬網路：

![HDInsight HBase 複寫虛擬網路圖表][img-vnet-diagram]
 

##必要條件
開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **具有 Azure PowerShell 的工作站**。請參閱[安裝和使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。

	執行 PowerShell 指令碼之前，請確定您已使用下列 Cmdlet 連接到 Azure 訂用帳戶：

		Add-AzureAccount

	如果您有多個 Azure 訂用帳戶，請使用下列 Cmdlet 設定目前的訂用帳戶：

		Select-AzureSubscription <AzureSubscriptionName>


>[AZURE.NOTE]Azure 服務名稱和虛擬機器名稱必須是唯一的。本教學課程中所使用的名稱為 Contoso-[Azure Service/VM name]-[EU/US]。例如，Contoso-VNet-EU 是指位於北歐資料中心的 Azure 虛擬網路；Contoso-DNS-US 是指位於美國東部資料中心的 DNS 伺服器 VM。您必須提供自己的名稱。
 

##建立兩個 Azure Vnet



**建立北歐名為 Contoso-VNet-EU 的虛擬網路**

1.	登入 [Azure 入口網站][azure-portal]。
2.	依序按一下 [**新增**]、[**網路服務**]、[**虛擬網路**]、[**自訂建立**]。
3.	輸入：

	- **名稱**：Contoso-VNet-EU
	- **位置**：北歐

		本教學課程使用北歐和美國東部資料中心。您可以選擇自己的資料中心。
4.	輸入：

	- **DNS 伺服器**：(保留為空白) 
	
		您需要有自己的 DNS 伺服器才能在虛擬網路內進行名稱解析。如需有關何時使用 Azure 提供的名稱解析，以及何時使用自己的 DNS 伺服器的詳細資訊，請參閱[名稱解析 (DNS)](https://msdn.microsoft.com/library/azure/jj156088.aspx)。如需有關設定 Vnet 之間名稱解析的指示，請參閱[設定兩個 Azure 虛擬網路之間的 DNS][hdinsight-hbase-dns]。
  
	- **設定點對站 VPN**：(未核取)

		點對站不適用於這種情況。

 	- **設定站對站 VPN**：(未核取)
 	
		您將在美國東部資料中心設定 Azure 虛擬網路的站對站 VPN 連線。
5.	輸入：

	- 	**位址空間起始 IP**：10.1.0.0
	- 	**位址空間 CIDR**：/16
	- 	**Subnet-1 起始 IP**：10.1.0.0
	- 	**Subnet-1 CIDR**：/24

	位址空間不可以與美國虛擬網路重疊。

**建立西歐名為 Contoso-VNet-EU 的虛擬網路**

- 使用下列值重複上一個程序：

	- **名稱**：Contoso-VNet-US
	- **位置**：美國東部
	 
	- **DNS 伺服器**：(保留為空白)
	- **設定點對站 VPN**：(未核取)
	- **設定站對站 VPN**：(未核取)
	 
	- **位址空間起始 IP**：10.2.0.0
	- **位址空間 CIDR**：/16
	- **Subnet-1 起始 IP**：10.2.0.0
	- **Subnet-1 CIDR**：/24

















##設定兩個 VNet 之間的 VPN 連線

###建立區域網路

當您建立 VNet 對 VNet 組態時，您需要設定每個 VNet 以彼此視為區域網路網站。在本節中，您會將每個 VNet 設定為區域網路。區域網路會與對應的 VNet 共用相同的 IP 位址空間。

![設定 Azure VPN 站對站組態 - Azure 區域網路][img-vnet-lnet-diagram]


**建立符合 Contoso-VNet-EU 網路位址空間、名為 Contoso-LNet-EU 的區域網路**

1. 從 Azure 入口網站，依序按一下 [**新增**]、[**網路服務**]、[**虛擬網路**]、[**新增區域網路**]。
3. 輸入：

	- **名稱**：Contoso-LNet-EU
	- **VPN 裝置 IP 位址**：192.168.0.1 (此位址將於稍後更新)

		一般而言，您會將實際的外部 IP 位址用於 VPN 裝置。若是 VNet 對 VNet 組態，您將使用 VPN 閘道 IP 位址。假設您尚未建立這兩個 Vnet 的 VPN 閘道，請輸入任意 IP 位址，再回頭修正此問題。
4.	輸入：

	- **位址空間起始 IP**：10.1.0.0
	- **位址空間 CIDR**：/16
	
	這必須確實對應到您先前為 Contoso-VNet-EU 指定的範圍。

**建立符合 Contoso-VNet-US 網路位址空間、名為 Contoso-LNet-US 的區域網路**

- 使用下列參數重複上一個程序：

	- **名稱**：Contoso-LNet-US
	- **VPN 裝置 IP 位址**：192.168.0.1 (此位址將於稍後更新)
	 
	- **位址空間起始 IP**：10.2.0.0
	- **位址空間 CIDR**：/16


###建立 VPN 閘道

此組態分為兩個部分。首先，您可以將 VNet 站對站連線設定為區域網路，然後建立動態路由 VPN。VNet 對 VNet 需要具備動態路由 VPN 的 Azure VPN 閘道。不支援 Azure 靜態路由 VPN。

**將 Contoso-VNet-EU 站對站連線設定為 Contoso-LNet-US**

1.	從 Azure 入口網站，按一下左側窗格上的 [**網路**]，
2.	按一下 [**Contoso-VNet-EU**]。
3.	按一下 **[設定]** 索引標籤。
4.	核取 [**連線到區域網路**]。
5.	在 [**區域網路**] 中，選取 [**Contoso-LNet-US**]。
6.	在 [虛擬網路位址空間] 區段中，按一下 [**新增閘道子網路**]。
7.	按一下 [儲存]。
8.	按一下 [**確定**] 以確認。


**建立 Contoso-VNet-EU 的 VPN 閘道**

1.	從 Azure 入口網站，按一下 [**儀表板**] 索引標籤。
4.	按一下頁面底部的 [**建立閘道**]，然後按一下 [**動態路由**]。
5.	按一下 [**是**] 以確認。請注意，頁面上的閘道圖形會變更為黃色，並顯示 [正在建立閘道]。建立閘道通常需要大約 15 分鐘的時間。

	當閘道狀態變更為 [正在連線] 時，將會在 [儀表板] 中看到每個閘道的 IP 位址。記下對應到每個 VNet 的 IP 位址，並小心不要混淆。這些是您在 [區域網路] 中為 VPN 裝置編輯預留位置 IP 位址時所使用的 IP 位址。

6.	建立一份**閘道 IP 位址**的副本。您將在下一節中使用它來設定 Contoso-VNet-EU 的 VPN 閘道 IP 位址。

**建立 Contoso-VNet-EU 的 VPN 閘道**

- 重複最後兩個程序，將 Contoso-VNet-US 站對站連線設定為 Contoso-LNet-EU，並建立 Contoso-Vnet-US 的 VPN 閘道。完成後，您將會有 Contoso-VNet-US 的 VPN 閘道 IP 位址。


### 設定區域網路的 VPN 裝置 IP 位址
在最後一節中，您會為每個 VNet 建立 VPN 閘道。您已經有 VPN 閘道的 IP 位址。現在您可以回去設定區域網路 VPN 裝置 IP 位址。

**設定 Contoso-LNet-EU 的 VPN 裝置 IP 位址**

1.	從 Azure 入口網站，按一下左側窗格中的 [**網路**]。
2.	按一下上方的 [**區域網路**]。
3.	按一下 [**Contoso-LNet-EU**]，然後按一下底部的 [**編輯**]。
4.	更新 [**VPN 裝置 IP 位址**]。這是您從 Contoso-VNET-EU 的 [儀表板] 索引標籤中取得的位址。
5.	按一下向右按鈕。
6.	按一下核取按鈕。

**設定 Contoso-LNet-US 的 VPN 裝置 IP 位址**

- 重複上一個程序，設定 Contoso-LNet-US 的 VPN 裝置 IP 位址。

###設定 VNet 閘道金鑰

Vnet 閘道會使用共用金鑰來驗證虛擬網路之間的連線。此金鑰無法在 Azure 入口網站中設定。您必須使用 PowerShell 或 .NET SDK。

**設定金鑰**

1. 從您的工作站，開啟 **Windows PowerShell ISE** 或 Windows PowerShell 主控台。
2. 更新下列指令碼中的參數，並執行指令碼：

		Add-AuzreAccount
		Select-AzureSubscription -[AzureSubscriptionName]
		Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
		Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 


##檢查 VPN 連線 

在沒有任何 VM 部署到 VNet 的情況下，您可以使用 Azure 入口網站上的虛擬網路視覺化圖表 VNet 儀表板頁面，來檢查連接狀態：

![HDInsight HBase 複寫虛擬網路 VPN 連線狀態][img-vpn-status]
  



##後續步驟

在本教學課程中，您已經學會如何設定兩個 Azure 虛擬網路之間的 VPN 連線。本系列中的其他兩篇文章涵蓋下列內容：

- [設定兩個 Azure 虛擬網路之間的 DNS][hdinsight-hbase-geo-replication-dns]
- [設定 HBase 異地複寫][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: http://manage.windowsazure.com


[powershell-install]: ../install-configure-powershell



[hdinsight-hbase-replication]: ../hdinsight-hbase-geo-replication/
[hdinsight-hbase-dns]: ../hdinsight-hbase-geo-replication-configure-DNS/


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.LNet.diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.status.png

<!---HONumber=July15_HO2-->