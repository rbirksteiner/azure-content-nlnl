<properties 
	pageTitle="在 Azure 基礎結構服務中架設的 SharePoint 伺服器陣列" 
	description="尋找重要文章說明如何在 Microsoft Azure 基礎結構服務中，設定開發/測試或實際執行中的 SharePoint 2013 伺服器陣列。" 
	documentationCenter="" 
	services="virtual-machines"
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sharepoint" 
	ms.devlang="na" 
	ms.topic="index-page" 
	ms.date="07/07/2015" 
	ms.author="josephd"/>

# 在 Azure 基礎結構服務中架設的 SharePoint 伺服器陣列

在 Microsoft Azure 基礎結構服務中，設定第一個或下一個開發/測試或實際執行 SharePoint 伺服器陣列，這樣您就可以利用簡化設定的優點，還能夠快速擴大伺服器陣列，使其增加新的容量或最佳化關鍵功能。

## 基本的 SharePoint 開發/測試伺服器陣列 

針對在服務管理中建立的虛擬機器，請使用 Azure Preview 入口網站中的 [SharePoint Server 伺服器陣列](virtual-machines-sharepoint-farm-azure-preview.md)功能，建立網際網路對向 SharePoint 網站的基本開發/測試伺服器陣列。

自動建立的環境包含 3 個伺服器，用於純雲端 Azure 虛擬網路的網域控制站、SQL Server 和 SharePoint 伺服器。

若要建立與資源管理員中建立之虛擬機器相同的組態，請使用範本。請參閱[部署一個包含三部伺服器的 SharePoint 陣列](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm)。

## 高可用性 SharePoint 開發/測試伺服器陣列

針對在服務管理中建立的虛擬機器，請使用 Azure Preview 入口網站中的 [SharePoint Server 伺服器陣列](virtual-machines-sharepoint-farm-azure-preview.md)功能，建立網際網路對向 SharePoint 網站的高可用性 SharePoint 開發/測試伺服器陣列。

自動建立的環境包含純雲端 Azure 虛擬網路中的 9 個伺服器：2 個用於網域控制站、3 個用於 SQL Server 叢集、2 個應用程式層 SharePoint 伺服器以及 2 個 Web 層 SharePoint 伺服器。

若要建立與資源管理員中建立之虛擬機器相同的組態，請使用範本。請參閱[部署一個包含九部伺服器的 SharePoint 伺服器陣列](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm)。

## 混合式雲端開發/測試伺服器陣列

利用 [混合式雲端開發/測試環境中的 SharePoint 內部網路伺服器陣列](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), ，您可以建立模擬的混合式雲端設定 (就可以架設簡易雙層式 SharePoint 伺服器陣列)，用來從您在網際網路上的位置，測試 Azure 中架設的內部網路 SharePoint 伺服陣列。

這個組態使用服務管理中建立的虛擬機器。

## 高可用性、內部網路 SharePoint 實際執行伺服器陣列

利用[在 Azure 中利用 SQL Server AlwaysOn 可用性群組部署 SharePoint 2013](virtual-machines-workload-intranet-sharepoint-overview.md)，您可以在 Azure 中建立可立即實際執行、高可用性的內部網路 SharePoint Server 2013 伺服器陣列。

這個組態使用服務管理中建立的虛擬機器。

## 其他資源

[Azure 基礎結構服務上的 SharePoint 伺服器](https://msdn.microsoft.com/library/dn275955.aspx)

[計劃在 Azure 基礎結構服務上部署 SharePoint 2013](https://msdn.microsoft.com/library/dn275958.aspx)

[適用於 SharePoint 2013 的 Microsoft Azure 架構](https://technet.microsoft.com/library/dn635309.aspx)
 

<!---HONumber=July15_HO2-->