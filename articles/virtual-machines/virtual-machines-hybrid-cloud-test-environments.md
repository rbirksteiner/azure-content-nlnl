<properties 
	pageTitle="Azure 混合式雲端測試環境" 
	description="瀏覽重要主題並了解如何建立測試環境，讓您您可以用於 Azure 混合式雲端的開發/測試概念證明。" 
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
	ms.date="05/29/2015" 
	ms.author="josephd"/>

# Azure 混合式雲端測試環境

開發/測試或概念證明，混合式雲端測試環境會使用本機的網際網路連線，以及其中一個公用 IP 位址，並引導您逐步設定正常運作，跨單位 Azure 虛擬網路 (VNet)。完成後，您可以進行應用程式的開發和測試、試驗簡化的 IT 工作負載，以及根據您在網際網路上的位置，評估相對的站對站虛擬私人網路 (VPN) 連線效率。

## 混合式雲端基本設定

[混合式雲端基本設定](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md)包括：

- 簡化的內部部署網路與 4 個虛擬機器 (網域控制站、應用程式伺服器、用戶端電腦以及執行 Windows Server 和「路由及遠端存取」的 VPN 裝置)
- Azure 虛擬網路與複本網域控制站
- 站對站 VPN 連線

## 混合式雲端中的 SharePoint 內部網路伺服器陣列

[混合式雲端測試環境中的 SharePoint 內部網路伺服器陣列](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)會將 SQL Server 2014 伺服器和 SharePoint Server 2013 伺服器，加入至混合式雲端基本設定。這會建立兩層式 SharePoint 伺服器陣列，您可以從簡化的內部部署網路上的用戶端電腦存取。

## 混合式雲端中的網頁型 LOB 應用程式

[混合式雲端測試環境中的網頁型 LOB 應用程式](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)會將 SQL Server 2014 伺服器和網際網路資訊服務 (IIS) 伺服器加入至混合式雲端基本設定。這樣會在裡面建立基礎結構，讓您可以部署和測試分層式網頁型 LOB 應用程式。

## 混合式雲端中的 Office 365 目錄同步作業 (DirSync) 伺服器

[混合式雲端測試環境中的 Office 365 DirSync 伺服器](../virtual-network/virtual-networks-setup-dirsync-hybrid-cloud-testing.md)會將 DirSync 伺服器加入至混合式雲端基本設定，並且示範 Office 365 DirSync 如何與 Office 365 訂用帳戶進行密碼同步處理。

## 模擬混合式雲端測試環境

組織和個人的直接網際網路連線和公用 IP 位址尚未就緒時，[模擬的混合式雲端測試環境](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)就會在不同的 Azure 虛擬網路中建立簡化的內部部署網路，然後利用 VNet 對 VNet VPN 連線來連接兩個虛擬網路。


## 其他資源

[在 Azure 基礎結構服務中架設的 SharePoint 伺服器陣列](virtual-machines-sharepoint-infrastructure-services.md)

[立體 LOB 應用程式架構藍圖的 PDF 檔](http://download.microsoft.com/download/2/C/8/2C8EB75F-AC45-4A79-8A63-C1800C098792/MS_Arch_LOB_App_3D_pdf.pdf)

[在 Microsoft Azure 中部署 Office 365 目錄同步作業 (DirSync)](https://technet.microsoft.com/library/dn635310.aspx)

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)


 

<!---HONumber=July15_HO2-->