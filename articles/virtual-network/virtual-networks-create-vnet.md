<properties 
   pageTitle="如何建立虛擬網路 (VNet)"
   description="了解如何建立虛擬網路 (VNet)"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# 如何建立虛擬網路 (VNet)

當您建立 VNet 時，您在 VNet 中的服務和 VM 可以安全地與彼此通訊，而不必經過網際網路。如果不想要 VNet 連接至其他 Vnet 或您的內部部署網路，則建立 Azure VNet 的程序相對較為快速且輕鬆，因為您將不需要取得和設定 VPN 裝置，或與其他 Vnet 或區域網路協調您選擇的 IP 位址。

>[AZURE.WARNING]請勿使用此程序來建立稍後將連接其他 VNet 或內部部署網路的 VNet。如果想要建立安全的跨單位或混合式連接，請參閱＜[關於虛擬網路安全的跨單位連接](https://msdn.microsoft.com/library/azure/dn133798.aspx)＞。如果您想要建立連接至另一個 VNet 的 VNet，請參閱＜[設定 VNet 對 VNet 連接](https://msdn.microsoft.com/library/azure/dn690122.aspx)＞。

## 設定您的 VNet

1. 登入 **Azure 管理入口網站**。

1. 按一下螢幕左下角的 [**新增**]。在導覽窗格中依序按一下 [網路服務] 和 [虛擬網路]。按一下 [**自訂建立**] 開始組態精靈。

1. 在 [**虛擬網路詳細資料**] 頁面上，輸入下列資訊：

	![虛擬網路詳細資料](./media/virtual-networks-create-vnet/IC736054.png)

	- **名稱 -** 命名您的 VNet。例如，我們建立一個 *EastUSVNet* 的名稱。您可以建立任何想要的名稱。當您部署 VM 和服務時，將會使用此 VNet 名稱，因此最好不要使用過於複雜的名稱。

	- **位置 -** 從下拉式清單選取位置 (區域)。當您將其部署至此 VNet 時，位置會與您要存放資源 (VM) 的實體位置直接相關。例如，如果您要讓 VM 實際位於*美國東部*，請選取該位置區域。建立關聯之後，您就無法變更與 VNet 相關聯的區域。

1. 請勿在 [**DNS 伺服器和 VPN 連線能力**] 頁面上進行任何變更。只需按一下箭號，即可向前移到下一個頁面。預設為 Azure 會提供您 VNet 的基本名稱解析。您的名稱解析需求，可能比基本 Azure 名稱解析可處理的情況更複雜。在此案例中，您稍後可能想要將執行 DNS 的虛擬機器新增至 VNet。如需 Azure 名稱解析和 DNS 的詳細資訊，請參閱＜[名稱解析 (DNS)](https://msdn.microsoft.com/library/azure/jj156088.aspx)＞。

1. 在 [**虛擬網路位址空間**] 頁面中，輸入您想要針對此 VNet 使用的位址空間。除非您的 VM 需要特定的內部 IP 位址範圍，或者您想要為將接收靜態 DIP 的 VM 建立特定子網路，否則不需在此頁面上進行任何變更。如果您想建立多個子網路，可以按一下此頁面上的 [**新增子網路**] 來執行這個動作。

	其他資訊：

	- 此頁面上的範圍包含動態內部 IP 位址 (DIP)，當您將其部署到此 VNet 時，您的 VM 會收到該 IP 位址。這些 IP 位址僅適用於 VNet 的內部通訊，並不適用於網際網路端點的 IP 位址。

	- 由於您不會利用跨單位 VPN 組態將此私人 VNet 連接到內部部署網路，因此不需要在這些設定與您現有的內部部署網路 IP 位址範圍之間進行協調。如果您認為稍後可能會想要建立跨單位組態，現在就必須在位址空間與已經存在於本機網站上的範圍進行協調，以避免發生路由問題。稍後變更範圍可能有點複雜，尤其是如果您有重疊的位址範圍。

	![位址空間](./media/virtual-networks-create-vnet/IC716778.png)

1. 按一下 [**虛擬網路位址空間**] 頁面右下角的核取記號，即會開始建立您的 VNet。建立 VNet 後，您將在管理入口網站的 [**網路**] 頁面上看見 [**狀態**] 下列出 [**已建立**]。

1. 一旦建立 VNet，您可以建立 VNet 中的 VM 和 PaaS 執行個體。建立新的 VM 時，請務必選取 [**從資源庫**]，以便選取您的 VNet 選項。請注意，如果您已經部署了現有的 VM 和 PaaS 執行個體，則無法將它們完全移至新的 VNet。這是因為在部署期間已新增其所必要的網路組態設定。

## 將虛擬機器新增至 VNet

建立 VNet 之後，您可以將新的 VM 新增至 VNet。請務必先建立 VNet，然後再部署 VM。部署 VM 之後，您必須重新部署才能將其移動至 VNet。如果您使用管理入口網站建立 VM，僅在您選取 [**新增/計算/虛擬機器/從資源庫**] 時，才能使用部署 VM 至 VNet 的介面。當您使用精靈來逐步建立您的 VM 時，您會在 [**虛擬機器組態**] 頁面上看到 [**區域/同質群組/虛擬網路**]。在下拉式清單中選取已建立的的 VNet。如需建立虛擬機器的詳細資訊，請參閱＜[Azure 虛擬機器](../virtual-machines)＞。

## 後續步驟

[如何管理虛擬網路 (VNet) 屬性](../virtual-networks-settings)

[如何管理虛擬網路 (VNet) 所使用的 DNS 伺服器](../virtual-networks-manage-dns-in-vnet)

[如何在虛擬網路中使用公用 IP 位址](../virtual-networks-public-ip-within-vnet)

[如何刪除虛擬網路 (VNet)](../virtual-networks-delete-vnet)
 

<!---HONumber=July15_HO2-->