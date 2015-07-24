
虛擬機器一律放在雲端服務中，雲端服務做為容器，並提供唯一的公用 DNS 名稱、公用 IP 位址及一組端點，供透過網際網路來存取虛擬機器。雲端服務可以選擇性地位於虛擬網路。

如果雲端服務不在虛擬網路中，就稱為*獨立*雲端服務。該雲端服務中的虛擬機器只能與透過其他虛擬機器的公用 DNS 名稱之其他虛擬機器通訊，該流量會透過網際網路傳送。如果雲端服務是在虛擬網路中，該雲端服務中的虛擬機器可與虛擬網路中的所有其他虛擬機器通訊，而不需要透過網際網路傳送任何流量。

如果您將虛擬機器放在相同的獨立雲端服務，您仍然可以利用負載平衡和可用性集。如需詳細資訊，請參閱[負載平衡虛擬機器](../articles/load-balance-virtual-machines.md)和[管理虛擬機器的可用性](../articles/manage-availability-virtual-machines.md)。不過，您無法組織子網路上的虛擬機器，或將獨立雲端服務連線到內部部署網路。範例如下。

![獨立雲端服務中的虛擬機器](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)
 
如果您將虛擬機器放在虛擬網路，您可以決定要將多少雲端服務用於負載平衡和可用性集。此外，您可以利用內部部署網路的相同方式，在子網路上組織虛擬機器，並將虛擬網路連線到內部部署網路。範例如下。

![虛擬網路中的虛擬機器](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

若要在 Azure 中連線虛擬機器，建議使用虛擬網路。最佳作法是在個別的雲端服務中設定應用程式的每一層。這可透過角色型存取控制 (RBAC) 委派進階使用者權限。如需詳細資訊，請參閱 [Azure Preview 入口網站中的角色型存取控制](../articles/role-based-access-control-configure.md)。不過，您可能需要將不同應用程式層的部分虛擬機器結合至相同的雲端服務，以維持在每個訂用帳戶最多有 200 項雲端服務的限制內。

## 連接虛擬網路中的 VM

若要連線虛擬網路中的虛擬機器：

1.	在 Azure 管理入口網站中建立虛擬網路。如需詳細資訊，請參閱[虛擬網路組態工作](https://msdn.microsoft.com/library/azure/jj156206.aspx)。
2.	使用 [**新增 > 計算 > 雲端服務 > 自訂建立**]，在 Azure 管理入口網站中為您的部署建立雲端服務集，以反映您的可用性集和負載平衡的設計。
3.	當您建立每個新的虛擬機器時，請指定正確的雲端服務和虛擬網路。如果雲端服務已與虛擬網路相關聯，則已經會為您選取其名稱。

以下是使用 Azure 管理入口網站的範例。

![選取虛擬機器的雲端服務](./media/howto-connect-vm-cloud-service/VMConfig1.png)

## 連接獨立雲端服務中的 VM
 
若要在獨立雲端服務中連接虛擬機器：

1.	在 Azure 管理入口網站中為您的部署建立雲端服務。按一下 [**新增 > 計算 > 雲端服務 > 自訂建立**]。
2.	當您建立虛擬機器時，選擇上一個步驟中建立的雲端服務名稱。或者，當您建立第一部虛擬機器時，您可以為您的部署建立雲端服務。

下列範例為名為 EndpointTest 的現有雲端服務使用 Azure 管理入口網站。
 
![將虛擬機器加入至現有的雲端服務。](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##資源
[虛擬機器負載平衡](../articles/load-balance-virtual-machines.md)

[管理虛擬機器的可用性](../articles/manage-availability-virtual-machines.md)

[虛擬網路設定工作](https://msdn.microsoft.com/library/azure/jj156206.aspx)

建立虛擬機器後，最好要新增資料磁碟，您的服務和工作負載才有地方可存放資料。執行下列其中一項：

[如何將資料磁碟連接至 Linux 虛擬機器](../articles/virtual-machines/virtual-machines-linux-how-to-attach-disk.md)

[如何將資料磁碟連接至 Windows 虛擬機器](../articles/virtual-machines/storage-windows-attach-disk.md)

<!---HONumber=July15_HO2-->