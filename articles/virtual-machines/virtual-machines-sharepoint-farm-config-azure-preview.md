<properties 
	pageTitle="SharePoint Server 伺服器陣列組態詳細資料" 
	description="說明 SharePoint 伺服器陣列的預設組態" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sharepoint" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="josephd"/>


# SharePoint Server 伺服器陣列組態詳細資料

SharePoint Server 伺服器陣列是 Microsoft Azure 預覽入口網站的一項功能，可自動為您建立預先設定的 SharePoint Server 2013 伺服器陣列。伺服器陣列組態有兩種：

- 基本
- 高可用性

下列區段提供每個伺服器陣列的組態詳細資料。

如需詳細資訊，請參閱 [SharePoint Server 伺服器陣列](virtual-machines-sharepoint-farm-azure-preview.md)。

## 基本 SharePoint 伺服器陣列

基本的 SharePoint 伺服器陣列是由這個組態中的三部虛擬機器所組成：

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png)

以下是組態詳細資料：

-	Azure 訂用帳戶：在初始組態期間指定的。
-	Azure 網域名稱 (亦稱為雲端服務)：系統會為每部虛擬機器自動建立個別的網域名稱。
-	儲存體帳戶：在初始組態期間指定的。
-	虛擬網路 	
	-   類型：純雲端	
    -	位址空間：10.0.0.0/26    

- 虛擬機器
	-	*HostNamePrefix*-DC (AD DS 網域控制站)
	-	*HostNamePrefix*-SQL (SQL Server 2014 伺服器)
	-	*HostNamePrefix*-SP (SharePoint 2013 伺服器)

- 網域控制站
	-	虛擬機器映像：Windows Server 2012 R2。
	-	主機名稱首碼：在初始組態期間指定的。
	-	大小：A1 (預設)
	-	網域名稱：contoso.com (預設)
	-	網域系統管理員帳戶名稱：在初始組態期間指定的。
	-	網域系統管理員帳戶密碼：在初始組態期間指定的。

- SQL Server
	-	虛擬機器映像：Windows Server 2012 R2 上的 SQL Server 2014 RTM Enterprise。
	-	主機名稱首碼：在初始組態期間指定的。
	-	大小：A5 (預設)
	-	資料庫存取帳戶名稱：在初始組態期間指定的。
	-	資料庫存取帳戶密碼：在初始組態期間指定的。
	-	SQL Server 服務帳戶名稱：sqlservice (預設)
	-	SQL Server 服務帳戶密碼：在初始組態期間指定的。

- SharePoint Server
	-	虛擬機器映像：SharePoint Server 2013 試用版。
	-	主機名稱首碼：在初始組態期間指定的。
	-	大小：A2 (預設)
	-	SharePoint 伺服器陣列帳戶名稱：sp_farm (預設)
	-	SharePoint 伺服器陣列帳戶密碼：在初始組態期間指定的。
	-	SharePoint 伺服器陣列複雜密碼：在初始組態期間指定的。


## 高可用性 SharePoint 伺服器陣列

高可用性 SharePoint 伺服器陣列由下列組態中的九個虛擬機器組成：

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)
 
以下是組態詳細資料：

-	Azure 訂用帳戶：在初始組態期間指定的。
-	Azure 網域名稱 (亦稱為雲端服務)：根據上圖所示，已建立個別的網域名稱。
-	儲存體帳戶：在初始組態期間指定的。
-	虛擬網路	
	-	類型：純雲端
	-	位址空間：10.0.0.0/26	

-	虛擬機器
	-	*HostNamePrefix*-DC1 (AD DS 網域控制站)
	-	*HostNamePrefix*-DC2 (AD DS 網域控制站)
	-	*HostNamePrefix*-SQL1 (SQL Server 2014 伺服器)
	-	*HostNamePrefix*-SQL2 (SQL Server 2014 伺服器)
	-	*HostNamePrefix*-SQL0 (Windows Server 2012 R2 伺服器)
	-	*HostNamePrefix*-WEB1 (SharePoint 2013 伺服器)
	-	*HostNamePrefix*-WEB2 (SharePoint 2013 伺服器)
	-	*HostNamePrefix*-APP1 (SharePoint 2013 伺服器)
	-	*HostNamePrefix*-APP2 (SharePoint 2013 伺服器)

-	網域控制站
	-	虛擬機器映像：Windows Server 2012 R2。
	-	主機名稱首碼：在初始組態期間指定的。
	-	大小：A1 (預設)
	-	網域名稱：contoso.com (預設)
	-	網域系統管理員帳戶名稱：在初始組態期間指定的。
	-	網域系統管理員帳戶密碼：在初始組態期間指定的。

-	SQL Server
	-	虛擬機器映像：Windows Server 2012 R2 上的 SQL Server 2014 RTM Enterprise。
	-	主機名稱首碼：在初始組態期間指定的。
	-	大小：針對 SQL 伺服器為 A5 (預設)，針對檔案共用見證為 A0 (預設)
	-	資料庫存取帳戶名稱：在初始組態期間指定的。
	-	資料庫存取帳戶密碼：在初始組態期間指定的。
	-	SQL Server 服務帳戶名稱：sqlservice (預設)
	-	SQL Server 服務帳戶密碼：在初始組態期間指定的。

-	SharePoint Server
	-	虛擬機器映像：SharePoint Server 2013 試用版。
	-	主機名稱首碼：在初始組態期間指定的。
	-	大小：A2 (預設)
	-	SharePoint 伺服器陣列帳戶名稱：sp_farm (預設)
	-	SharePoint 伺服器陣列帳戶密碼：在初始組態期間指定的。	
	-	SharePoint 伺服器陣列複雜密碼：在初始組態期間指定的。

> [AZURE.NOTE]從 SharePoint Server 2013 試用版映像建立 SharePoint Server。若要在試用版到期後繼續使用該虛擬機器，您需要將安裝轉換為使用適用於 SharePoint Server 2013 Standard 或 Enterprise 版本的零售或大量授權金鑰。

## Azure 資源管理員

Azure Preview 入口網站的 SharePoint Server 伺服器陣列功能會在服務管理中建立虛擬機器。若要在資源管理員中建立 SharePoint Server 2013 伺服器陣列，請參閱[使用 Azure 資源管理員範本部署 SharePoint 伺服器陣列](virtual-machines-workload-template-sharepoint.md)。

## 其他資源

[SharePoint Server 伺服器陣列](virtual-machines-sharepoint-farm-azure-preview.md)

[在 Azure 基礎結構服務上的 SharePoint](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[在混合式雲端中設定用於測試的 SharePoint 內部網路伺服器陣列](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)
 

<!---HONumber=July15_HO2-->