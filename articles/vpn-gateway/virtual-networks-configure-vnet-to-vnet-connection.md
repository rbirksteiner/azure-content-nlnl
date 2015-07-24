<properties
   pageTitle="設定 VNet 對 VNet 連線"
   description="如何在相同或不同的訂用帳戶或區域中，將 Azure 虛擬網路連接在一起。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/28/2015"
   ms.author="cherylmc"/>


# 設定 VNet 對 VNet 連線

將 Azure 虛擬網路 (VNet) 連線到另一個 Azure 虛擬網路非常類似於將虛擬網路連線到內部部署網站位置。這兩種連線類型都使用虛擬網路閘道提供使用 IPsec/IKE 的安全通道。您所連線的 Vnet 可位於不同的訂用帳戶和不同的區域。您甚至可以使用多網站組態將結合 VNet 對 VNet 通訊。這可讓您建立使用內部虛擬網路連線結合跨單位連線的網路拓撲，如下圖所示：

![VNet 對 VNet 連線能力圖表](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)

## 為什麼要連接虛擬網路？

針對下列原因，您可能希望連接虛擬網路：

- **跨區域的異地備援和異地目前狀態**
	- 您可以使用安全連線設定自己的異地複寫或同步處理，而不用查看網際網路對向端點。
	- 您可以使用 Azure 負載平衡器和 Microsoft 或協力廠商叢集技術，利用異地備援跨多個 Azure 區域設定高度可用的工作負載。其中一個重要的範例就是使用分散在多個 Azure 區域的可用性群組設定 SQL Always On。

- **具有嚴密隔離界限的區域性多層式應用程式**
	- 在相同區域中，您可以使用嚴密的隔離和安全的層次間通訊，設定多層式應用程式與多個虛擬網路連線在一起。

- **在 Azure 中的跨訂用帳戶、組織間通訊**
	- 如果您有多個 Azure 訂用帳戶，可以將虛擬網路之間不同訂用帳戶的工作負載安全地連線在一起。
	- 對於企業或服務提供者，可以在 Azure 中使用安全的 VPN 技術啟用跨組織通訊。

## VNet 對 VNet 常見問題集

- 虛擬網路可位於相同或不同的訂用帳戶。

- 虛擬網路可位於相同或不同的 Azure 區域 (位置)。

- 即使雲端服務或負載平衡端點與虛擬網路連線在一起，也「無法」橫跨虛擬網路。

- 除非需要跨部署連線，否則將多個 Azure 虛擬網路連線在一起不需要使用任何內部部署 VPN 閘道。

- VNet 對 VNet 支援連線 Azure 虛擬網路。但是不支援連線「不」在虛擬網路中的虛擬機器或雲端服務。

- VNet 對 VNet 需要具備動態路由 VPN 的 Azure VPN 閘道。不支援 Azure 靜態路由 VPN閘道。

- 虛擬網路連線可以同時搭配多網站 VPN 使用，最多可以將虛擬網路 VPN 閘道的 10 個 VPN 通道連線到其他虛擬網路或內部部署網站。

- 虛擬網路與內部部署區域網路網站的位址空間不得重疊。位址空間重疊會導致建立虛擬網路或上傳 netcfg 組態檔失敗。

- 不支援成對虛擬網路之間的備援通道。

- 虛擬網路的所有 VPN 通道 (包括 P2S VPN) 在 Azure VPN 閘道與 Azure 中相同的 VPN 閘道運作時間 SLA 共用可用的頻寬。

## 設定 VNet 對 VNet 連線

在此程序中，我們將逐步引導您連線兩個虛擬網路：VNet1 和 VNet2。您必須熟悉網路連線，才能替換與您的網路設計需求相容的 IP 位址範圍。從 Azure 虛擬網路連線到另一個 Azure 虛擬網路與透過站對站 (S2S) VPN 連線到內部部署網路相同。

此程序主要使用管理入口網站，不過您必須使用 Microsoft Azure PowerShell Cmdlet 連線 VPN 閘道。

![連線 VNet 對 VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)

有 5 個區段需要規劃和設定。以下面所列的順序設定每個區段：

1. [規劃 IP 位址範圍](#plan-your-ip-address-ranges)
2. [建立虛擬網路](#create-your-virtual-networks)
3. [新增區域網路](#add-local-networks)
4. [為每個 VNet 建立動態路由閘道](#create-the-dynamic-routing-gateways-for-each-vnet)
5. [連線 VPN 閘道](#connect-the-vpn-gateways)


## 規劃 IP 位址範圍

請務必決定您要用來設定網路組態檔 (netcfg) 的範圍。從 VNet1 的角度來說，VNet2 只是 Azure 平台中定義的另一個 VPN 連線。而對 VNet2 來說，VNet1 只是另一個 VPN 連線。它們兩者都會將彼此視為區域網路網站。請記住，您必須先確定您的 VNet 範圍或區域網路範圍沒有以任何方式重疊。

表 1 顯示如何定義 VNet 的範例。僅使用以下的範圍做為指導方針。記下您將用於虛擬網路的範圍。您將需要這項資訊供之後的步驟使用。

**表 1**

|虛擬網路 |虛擬網路網站定義 |區域網路網站定義|
|:----------------|:-------------------------------|:----------------------------|
|VNet1 |VNet1 (10.1.0.0/16) |VNet2 (10.2.0.0/16) |
|VNet2 |VNet2 (10.2.0.0/16) |VNet1 (10.1.0.0/16) |

## 建立虛擬網路

基於本教學課程的目的，我們將建立兩個虛擬網路：VNet1 和 VNet2。建立您的 VNet 時，請替換您自己的值。基於本教學課程的目的，我們將為 VNet 使用以下的值：

VNet1：位址空間 = 10.1.0.0/16；區域 = 美國西部

VNet2： 位址空間 = 10.2.0.0/16；區域 = 日本東部

1. 登入**管理入口網站**。

2. 按一下螢幕左下角的 [新增]。在導覽窗格中依序按一下 [網路服務] 和 [虛擬網路]。按一下 [Custom Create] 開始組態精靈。

在 [虛擬網路詳細資料] 頁面上，輸入下列資訊。如需詳細資料頁面上設定的詳細資訊，請參閱[虛擬網路詳細資料頁面](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNetDetails)。

  ![虛擬網路詳細資料](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **名稱** - 為虛擬網路命名。例如：VNet1。
  - **位置** – 當您建立虛擬網路時，您會將該位置與 Azure 位置 (區域) 產生關聯。例如，如果您希望部署到虛擬網路的 VM 實際位於美國西部，請選取該位置。建立關聯之後，您就無法變更與您的虛擬網路相關聯的位置。



在 [DNS Servers and VPN Connectivity] 頁面上，輸入下列資訊，然後按一下右下角的下一步箭頭。如需此頁面上設定的詳細資訊，請參閱 [DNS 伺服器和 VPN 連線能力頁面](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETDNS)。

  ![DNS 伺服器和 VPN 連線能力](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)


- **DNS 伺服器**：輸入 DNS 伺服器名稱和 IP 位址，或從下拉式清單中選取先前註冊的 DNS 伺服器。此設定不會建立 DNS 伺服器，它可讓您指定您想要用於此虛擬網路的名稱解析的 DNS 伺服器。如果您想要在虛擬網路之間進行名稱解析，您必須設定自己的 DNS 伺服器，而不是使用 Azure 所提供的名稱解析。

  - 請不要選取任何核取方塊。只要按一下右下角的箭頭，就可以移到下一個畫面。

在 [虛擬網路位址空間] 頁面上，指定您想要用於虛擬網路的位址範圍。這些是將指派給 VM 的動態 IP 位址 (DIP)，以及指派給您部署至此虛擬網路之其他角色執行個體的動態 IP 位址 (DIP)。有不少關於虛擬網路位址空間的規則，因此您將可以查看 [虛擬網路位址空間][](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNET_ADDRESS) 頁面，以獲得詳細資訊。特別重要的是，選取的範圍不得與用於內部部署網路的任何範圍重疊。您將需要與網路管理員協調，他/她可能需要為您從內部部署網路位址空間規劃一個 IP 位址範圍，供您的虛擬網路使用。


  ![虛擬網路位址空間頁面](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  **輸入下列資訊**，然後按一下右下角的核取記號來設定您的網路。

  - **位址空間** - 包括起始 IP 和位址計數。請確認您指定的位址空間沒有與您在內部部署網路上所擁有的任何位址空間重疊。在此範例中，我們將為 VNet1 使用 10.1.0.0/16。
  - **新增子網路** - 包括起始 IP 和位址計數。不需要其他子網路，但是您可以為將擁有靜態 DIP 的 VM 建立個別的子網路。或者，您可以讓您的 VM 位於與其他角色執行個體不同的子網路中。

**按一下頁面右下角的核取記號**，即會開始建立您的虛擬網路。完成時，您將在管理入口網站的 [*網路*] 頁面上看到 [*狀態*] 下列出 [*已建立*]。

## 建立另一個虛擬網路

接下來，請重複上述步驟以建立另一個虛擬網路。在這個練習中，您稍後將會連接這兩種虛擬網路。請注意，位址空間不能夠重複或重疊非常重要。基於本教學課程的目的，使用下列值：

- **VNet2**：位址空間 = 10.2.0.0/16
- **區域** = 日本東部

## 新增區域網路

當您建立 VNet 對 VNet 組態時，您需要設定每個 VNet 將彼此視為區域網路網站。在此程序中，您會將每個 VNet 設定為區域網路。如果您先前已設定 VNet，這是在管理入口網站將它們新增為區域網路的方式。

1. 按一下螢幕左下角的 [新增]。在導覽窗格中依序按一下 [網路服務] 和 [虛擬網路]。按一下 [新增區域網路]

2. 在 [指定區域網路詳細資料] 頁面上，針對 [名稱]，輸入您想要在 VNet 對 VNet 組態中使用的虛擬網路的名稱。此範例中，我們將使用 VNet 1，因為我們會將 VNet2 指向此虛擬網路組態，以供我們的組態使用。

  對於 [VPN 裝置 IP 位址]，使用任何 IP 位址。一般而言，您會將實際的外部 IP 位址用於 VPN 裝置。若是 VNet 對 VNet 組態，您將使用閘道 IP 位址。但是，假設您尚未建立閘道，我們會使用您在這裡指定的 IP 位址做為預留位置。接著，您將在 Azure 產生閘到之後，回到這些設定，使用對應的閘道 IP 位址進行設定。

3. 在 [指定位址頁面] 上，您會為 VNet1 放入實際的 IP 位址範圍和位址計數。這必須確實對應到您先前為 VNet1 指定的範圍。

4. 將 VNet1 設定為區域網路之後，回來使用對應於該 VNet 的值設定 VNet2。

5. 現在您將每個 VNet 指向對方，以做為區域網路。在管理入口網站中，移至 VNet1 的 [設定] 頁面。在 [站台對站連線能力] 底下，選取 [連線到區域網路]，然後選取 **VNET2** 做為區域網路。

  ![連線到區域網路](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736058.jpg)

6. 在相同頁面的 [虛擬網路位址空間] 區段中，按一下 [新增閘道子網路]，然後按一下頁面底部的 [儲存] 圖示來儲存您的設定。

7. 為 VNet2 重複上述步驟，以便將 VNet1 指定為區域網路。

## 為每個 VNet 建立動態路由閘道

既然您已經設定每個 VNet，您將需要設定 VNet 閘道。

1. 在 [網路] 頁面上，確認您的虛擬網路的狀態欄為 [已建立]。

2. 在 [名稱] 欄中，按一下虛擬網路的名稱。

3. 在 [儀表板] 頁面上，注意此 VNet 尚未設定閘道。當您進行設定閘道器的步驟時，您會看到此狀態變更。

4. 按一下頁面底部的 [建立閘道]。

  您必須選取 [動態路由]。當系統提示您確認是否要建立閘道時，請按一下 [是]。

  ![閘道類型](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)

5. 閘道正在建立時，請注意頁面上的閘道圖形會變更為黃色，並顯示 [正在建立閘道]。建立閘道通常需要大約 15 分鐘。

6. 為另一個 VNet 重複相同的步驟，且務必選取 [動態閘道]。您不需要第一個 VNet 閘道完成，就可以開始為另一個 VNet 建立閘道。

7. 當閘道狀態變更為 [正在連線] 時，將會在 [儀表板] 中看到每個閘道的 IP 位址。記下對應到每個 VNet 的 IP 位址，並小心不要混淆。這些是您在 [區域網路] 中為 VPN 裝置編輯預留位置 IP 位址時所使用的 IP 位址 。

## 編輯區域網路

1. 在 [區域網路] 頁面上，按一下您想要編輯的 [區域網路名稱] 的名稱，然後按一下頁面底部的 [編輯]。針對 [VPN 裝置 IP 位址]，輸入對應於 VNet 之閘道的 IP 位址。例如，針對 VNet1，放入指派給 VNet1 的閘道 IP 位址。按一下頁面底部的箭頭。

2. 在 [指定位址空間] 頁面上，按一下右下角的核取記號，而不進行任何變更。

## 連線 VPN 閘道

所有先前的步驟都完成後，您要將 IPsec/IKE 預先共用的金鑰設為相同。您可以使用 REST API 或 PowerShell Cmdlet 完成。如果您使用 PowerShell，請確認您擁有最新版的 Microsoft Azure PowerShell Cmdlet。下列範例會使用 PowerShell Cmdlet，將金鑰值設為 A1b2C3D4。請注意，兩者都使用相同的金鑰值。編輯下列範例，以反映您自己的值。

對於 Vnet1

	PS C:> Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2 -SharedKey A1b2C3D4

對於 VNet2

	PS C:> Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1 -SharedKey A1b2C3D4

等待連線初始化。一旦閘道完成初始化之後，將會如下圖所示，而且您的虛擬網路已連線。

![閘道狀態 - 已連線](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)

## 後續步驟

您可以在此文章中進一步了解虛擬網路跨單位連線：[關於虛擬網路安全的跨單位連線](https://msdn.microsoft.com/library/azure/dn133798.aspx)。


如果您想要設定站對站 VPN 連線，請參閱[設定站對站 VPN 連線](vpn-gateway-site-to-site-create.md)

如果您想要將虛擬機器新增至虛擬網路，請參閱[如何建立自訂虛擬機器](../virtual-machines/virtual-machines-create-custom.md)。

如果您想要使用 RRAS 設定 VNet 連線，請參閱[使用 Windows Server 2012 路由及遠端存取服務 (RRAS) 設定站對站 VPN](https://msdn.microsoft.com/library/dn636917.aspx)。

如需有關組態結構描述的相關資訊，請參閱 [Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)。


[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 

<!---HONumber=July15_HO2-->