<properties
	pageTitle="SharePoint Server 伺服器陣列"
	description="您可以使用 Azure Preview 入口網站中的 SharePoint Server 伺服器陣列功能快速建立新的 SharePoint Server 2013 伺服器陣列。"
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2015"
	ms.author="josephd"/>

# SharePoint Server 伺服器陣列

使用 SharePoint Server 伺服器陣列時，Microsoft Azure 預覽入口網站會自動為您建立預先設定的 SharePoint Server 2013 伺服器陣列。當您在開發和測試環境中需要基本或高可用性 SharePoint 伺服器陣列時，或是您要評估將 SharePoint Server 2013 做為組織的共同作業方案時，這將可為您省下許多時間。

基本的 SharePoint 伺服器陣列由這個組態中的三部虛擬機器所組成。

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png)

此伺服器陣列組態可用於 SharePoint 應用程式開發的簡化設定，或用於您第一次的 SharePoint 2013 評估。

高可用性 SharePoint 伺服器陣列由下列組態中的九個虛擬機器組成。

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png)

此伺服器陣列組態可用來測試較高的用戶端負載、外部 SharePoint 網站的高可用性，以及 SharePoint 伺服器陣列的 SQL Server AlwaysOn。此組態也可用於高可用性環境中的 SharePoint 應用程式開發。

如需這兩種伺服器陣列的組態詳細資料，請參閱 [SharePoint Server 伺服器陣列組態詳細資料](virtual-machines-sharepoint-farm-config-azure-preview.md)。

## 逐步設定

若要使用 SharePoint 伺服器陣列範本建立您的 SharePoint 伺服器陣列，請執行下列動作：

1. 在 [Microsoft Azure Preview 入口網站](https://portal.azure.com/)中，按一下 [新增] > [計算] > [SharePoint Server 伺服器陣列]。如果沒有顯示 [SharePoint Server 伺服器陣列]，請依序按一下 [新增] > [計算] > [Marketplace]，在 [搜尋計算] 中輸入 **SharePoint**，然後按一下 [SharePoint Server 伺服器陣列]。在 [SharePoint Server 伺服器陣列] 窗格中，按一下 [建立]。
2. 在 [建立 SharePoint 伺服器陣列] 窗格中，輸入資源群組的名稱。
3. 為您伺服器陣列中每個虛擬機器的本機系統管理員帳戶，輸入使用者名稱和密碼。選擇難以猜測的名稱和密碼，並且在記錄後儲存於安全之處。
4. 如果您需要高可用性伺服器陣列，請按一下 [啟用高可用性]。
5. 若要設定網域控制站，請按一下箭頭。您可以指定主機名稱首碼 (預設值為資源群組名稱)、樹系根網域名稱 (預設值為 contoso.com)，和網域控制站的大小 (預設值為 A1)。
6. 若要設定 SQL Server，請按一下箭頭。您可以指定主機名稱首碼 (預設值為資源群組名稱)、SQL Server 的大小 (預設值為 A5)、資料庫存取帳戶名稱和密碼 (預設值是使用系統管理員帳戶)，以及 SQL Server 服務帳戶名稱 (預設值為 sqlservice) 和密碼 (預設值是使用與系統管理員帳戶相同的密碼)。
7. 若要設定 SharePoint 伺服器，請按一下箭頭。您可以指定主機名稱首碼 (預設值為資源群組名稱)、SharePoint 伺服器的大小 (預設值為 A2)、SharePoint 使用者帳戶 (預設值為 sp_setup) 和密碼、SharePoint 伺服器陣列帳戶名稱 (預設值為 sp_farm) 和密碼，以及 SharePoint 伺服器陣列複雜密碼。預設值是將系統管理員密碼用於 SharePoint 使用者帳戶、伺服器陣列帳戶和複雜密碼。
8. 若要設定適用於虛擬網路、儲存體帳戶或診斷的選用組態設定，請按一下對應的箭頭。
9. 若要指定訂閱，請按一下箭頭。
10. 完成之後，請按一下 [建立]。

> [AZURE.NOTE]網域控制站預設未安裝 Active Directory 管理工具。若要安裝它們，請從網域控制站虛擬機器上系統管理員層級的 Windows PowerShell 命令提示字元執行 **Install-WindowsFeature AD-Domain-Services -IncludeManagementTools** 命令。

## 存取和管理 SharePoint 伺服器陣列

SharePoint 伺服器陣列具有預先設定的端點，可讓對於連接網際網路的用戶端電腦未驗證的 Web 流量 (TCP 連接埠 80) 輸送到 SharePoint Web 伺服器。此端點連接到預先設定的團隊網站。若要存取此團隊網站：

1.	在 Azure 預覽入口網站中，按一下 [瀏覽]，然後按一下 [資源群組]。
2.	在資源群組清單中，按一下您 SharePoint 伺服器陣列資源群組的名稱。
3.	在 SharePoint 伺服器陣列資源群組的窗格中，按一下 [Deployment history]。
4.	在 [Deployment history] 窗格中，按一下 [Microsoft.SharePointFarm]。
5.	在 [Microsoft.SharePointFarm] 窗格中，選取 [SHAREPOINTSITEURL] 欄位中的 URL 並將它複製。
6.	在您的網際網路瀏覽器中，將此 URL 貼入地址欄位。
7.	出現提示時，輸入您在建立伺服器陣列時所指定的使用者帳戶認證。

在「管理中心 SharePoint」網站中，您可以設定「我的網站」、SharePoint 應用程式和其他功能。如需詳細資訊，請參閱[設定 SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx)。若要存取「管理中心 SharePoint」網站：

1.	在 Azure 預覽入口網站中，按一下 [瀏覽]，然後按一下 [資源群組]。
2.	在資源群組清單中，按一下您 SharePoint 伺服器陣列資源群組的名稱。
3.	在 SharePoint 伺服器陣列資源群組的窗格中，按一下 [Deployment history]。
4.	在 [Deployment history] 窗格中，按一下 [Microsoft.SharePointFarm]。
5.	在 [Microsoft.SharePointFarm] 窗格中，選取 [SHAREPOINTCENTRALADMINURL] 欄位中的 URL 並將它複製。
6.	在您的網際網路瀏覽器中，將此 URL 貼入地址欄位。
7.	出現提示時，輸入您在建立伺服器陣列時所指定的使用者帳戶認證。


注意：

- Azure Preview 入口網站會在指定的訂閱內建立這些虛擬機器。
- Azure 預覽入口網站會在僅限雲端、具有網際網路對向網站空間的虛擬網路中，同時建立這兩種伺服器陣列。沒有任何站對站 VPN 或 ExpressRoute 連線會連回您的組織網路。
- 您可以透過遠端桌面連線來管理這些伺服器。如需詳細資訊，請參閱[如何登入執行 Windows Server 的虛擬機器](virtual-machines-log-on-windows-server.md)。

## Azure 資源管理員

Azure Preview 入口網站的 SharePoint Server 伺服器陣列功能會在服務管理中建立虛擬機器。若要在資源管理員中建立 SharePoint Server 2013 伺服器陣列，請參閱[使用 Azure 資源管理員範本部署 SharePoint 伺服器陣列](virtual-machines-workload-template-sharepoint.md)。

## 其他資源

[SharePoint Server 伺服器陣列組態詳細資料](virtual-machines-sharepoint-farm-config-azure-preview.md)

[在 Azure 基礎結構服務上的 SharePoint](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[在混合式雲端中設定用於測試的 SharePoint 內部網路伺服器陣列](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[裝載於 Azure 基礎結構服務中的 SharePoint 伺服器陣列](virtual-machines-sharepoint-infrastructure-services.md)

<!---HONumber=July15_HO2-->