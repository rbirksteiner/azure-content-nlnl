<properties 
	pageTitle="在 Azure 中部署包含 SQL Server AlwaysOn 可用性群組的 SharePoint" 
	description="您可以透過五個步驟，在 Azure 中部署包含 SQL Server AlwaysOn 可用性群組的 SharePoint。" 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# 在 Azure 中部署包含 SQL Server AlwaysOn 可用性群組的 SharePoint

本主題包含在 Azure 基礎結構服務中，部署包含 SQL Server AlwaysOn 可用性群組且僅限內部網路的 SharePoint 2013 伺服器陣列的逐步指示連結。此伺服器陣列包含下列電腦：

- 兩部 SharePoint Web 伺服器
- 兩部 SharePoint 應用程式伺服器
- 兩部資料庫伺服器
- 一部叢集多數節點伺服器
- 兩個網域控制站

以下呈現的就是這個設定，其中會為每部伺服器提供預留位置名稱。

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)
 
兩部適用於每個角色的機器可確保高可用性。所有的虛擬機器都位於單一區域中。適用於特定角色的每個虛擬機器群組都位於它們自己的可用性設定組中。

您可以在下列階段中部署這個設定：

- [第 1 階段：設定 Azure](virtual-machines-workload-intranet-sharepoint-phase1.md)。建立儲存體帳戶、雲端服務及跨單位虛擬網路。
- [第 2 階段：設定網域控制站](virtual-machines-workload-intranet-sharepoint-phase2.md)。建立和設定複本 Active Directory 網域服務 (AD DS) 網域控制站。
- [第 3 階段：建立 SQL Server 基礎結構](virtual-machines-workload-intranet-sharepoint-phase3.md)。建立和設定 SQL Server 虛擬機器、準備將它們與 SharePoint 搭配使用，然後建立叢集。
- [第 4 階段：設定 SharePoint 伺服器](virtual-machines-workload-intranet-sharepoint-phase4.md)。建立和設定四部 SharePoint 虛擬機器。
- [第 5 階段：建立可用性群組並新增 SharePoint 資料庫](virtual-machines-workload-intranet-sharepoint-phase5.md)。準備資料庫並建立 SQL Server AlwaysOn 可用性群組。

這個包含 SQL Server AlwaysOn 的 SharePoint 部署是設計來搭配[包含 SQL Server AlwaysOn 的 SharePoint 資訊圖](http://go.microsoft.com/fwlink/?LinkId=394788)使用，其中並包含最新建議。

這個設定是針對每個階段進行引導的指南，適用於在 Azure 基礎結構服務中建立功能完整且高可用性之內部網路 SharePoint 伺服器陣列的預先定義架構。如需在 Azure 中實作 SharePoint 2013 的其他架構指導方針，請參閱[適用於 SharePoint 2013 的 Microsoft Azure 架構](https://technet.microsoft.com/library/dn635309.aspx)。

請記住下列要點：

- 如果您是經驗豐富的 SharePoint 實作者，請自行決定是否要調整第 3 到 5 階段中的指示，以建置最適合您需求的伺服器陣列。 
- 如果您已經具備現有的 Azure 混合式雲端實作，可自行決定是否要調整或略過第 1 和 2 步驟中的指示，在適當的子網路上裝載新的 SharePoint 伺服器陣列。
- 所有伺服器都位於 Azure 虛擬網路中的單一子網路上。如果您想要提供其他相當於隔離子網路的安全性，可以使用 [網路安全性群組][](https://msdn.microsoft.com/library/azure/dn848316.aspx)。

若要建置開發/測試環境或此設定的概念證明，請參閱[在混合式雲端中設定用於測試的 SharePoint 內部網路伺服器陣列](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)。

如需包含 SQL Server AlwaysOn 可用性群組的 SharePoint 的其他資訊，請參閱[為 SharePoint 2013 設定 SQL Server 2012 AlwaysOn 可用性群組 ](https://technet.microsoft.com/library/jj715261.aspx)。

## 下一步

若要開始設定這個工作負載，請前往[第 1 階段：設定 Azure](virtual-machines-workload-intranet-sharepoint-phase1.md)。


## 其他資源

[包含 SQL Server AlwaysOn 的 SharePoint 資訊圖](http://go.microsoft.com/fwlink/?LinkId=394788)

[適用於 SharePoint 2013 的 Microsoft Azure 架構](https://technet.microsoft.com/library/dn635309.aspx)

[裝載於 Azure 基礎結構服務中的 SharePoint 伺服器陣列](virtual-machines-sharepoint-infrastructure-services.md)

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=July15_HO2-->