<properties 
	pageTitle="SharePoint 內部網路伺服器陣列工作負載第 1 階段：設定 Azure" 
	description="第一個階段是在 Azure 基礎結構服務中，部署包含 SQL Server AlwaysOn 可用性群組且僅限內部網路的 SharePoint 2013 伺服器陣列，而您將在這個階段中建立 Azure 虛擬機器和其他 Azure 基礎結構元素。" 
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

# SharePoint 內部網路伺服器陣列工作負載第 1 階段：設定 Azure

這個階段是在 Azure 基礎結構服務中，部署包含 SQL Server AlwaysOn 可用性群組且僅限內部網路的 SharePoint 2013 伺服器陣列，而您將在這個階段中建置 Azure 網路功能與儲存體基礎結構。您必須先完成這個階段才能前往[第 2 階段](virtual-machines-workload-intranet-sharepoint-phase2.md)。如需所有階段的詳細資訊，請參閱[在 Azure 中部署包含 SQL Server AlwaysOn 可用性群組的 SharePoint](virtual-machines-workload-intranet-sharepoint-overview.md)。

您必須使用下列基本網路元件來佈建 Azure：

- 含有一個子網路的跨單位虛擬網路
- 三個 Azure 雲端服務
- 一個 Azure 儲存體帳戶，可用來儲存 VHD 磁碟映像和額外的資料磁碟

## 開始之前

開始設定 Azure 元件之前，請先填寫下列表格：為了協助您進行設定 Azure 的程序，請列印本節並寫下必要資訊，或者將本節複製到文件並完成填寫。

針對虛擬網路 (VNet) 的設定，請填寫表格 V。

項目 | 設定元素 | 說明 | 值 
--- | --- | --- | --- 
1. | VNet 名稱 | 要指派給 Azure 虛擬網路的名稱 (例如 SPFarmNet)。 | __________________
2. | VNet 位置 | 將包含虛擬網路的 Azure 資料中心。 | __________________
3. | 區域網路名稱 | 要指派給組織網路的名稱。 | __________________
4. | VPN 裝置 IP 位址 | 您的 VPN 裝置介面在網際網路上的公用 IPv4 位址。與您的 IT 部門合作來決定這個位址。 | __________________
5. | VNet 位址空間 | 適用於虛擬網路的位址空間 (定義於單一私人位址首碼中)。與您的 IT 部門合作來決定這個位址空間。 | __________________
6. | 第一部最終的 DNS 伺服器 | 第四個適用於虛擬網路之子網路位址空間的可能 IP 位址 (請參閱表格 S)。與您的 IT 部門合作來決定這些位址。 | __________________
7. | 第二部最終的 DNS 伺服器 | 第五個適用於虛擬網路之子網路位址空間的可能 IP 位址 (請參閱表格 S)。與您的 IT 部門合作來決定這些位址。 | __________________

**表格 V：跨單位虛擬網路設定**

針對這個解決方案的子網路填寫表格 S。為子網路指定易記名稱，根據虛擬網路位址空間來指定單一 IP 位址空間，並提供描述性用途。位址空間格式應該是無類別網域間路由選擇 (CIDR) 格式，亦稱為網路首碼格式。例如，10.24.64.0/20。與您的 IT 部門合作，從虛擬網路位址空間來決定這個位址空間。

項目 | 子網路名稱 | 子網路位址空間 | 目的 
--- | --- | --- | --- 
1. | _______________ | _____________________________ | _________________________

**表格 S：虛擬網路中的子網路**

> [AZURE.NOTE]為求簡化，這個預先定義的架構會使用單一子網路。如果您想要涵蓋一組流量篩選器來模擬子網路隔離，可以使用 Azure [網路安全性群組](https://msdn.microsoft.com/library/azure/dn848316.aspx)。

如果這兩部內部部署的 DNS 伺服器是您最初在虛擬網路上設定網域控制站時想要使用的伺服器，請填寫表格 D。請為每部 DNS 伺服器指定易記名稱和單一 IP 位址。這個易記名稱不需要與 DNS 伺服器的主機名稱或電腦名稱相符。請注意，其中列出兩個空白項目，但您可以增加更多項目。與您的 IT 部門合作來決定這份清單。

項目 | DNS 伺服器易記名稱 | DNS 伺服器 IP 位址 
--- | --- | ---
1. | ___________________________ | ___________________________
2. | ___________________________ | ___________________________ 

**表格 D：內部部署 DNS 伺服器**

若要透過網站間 VPN 連線，將封包從跨單位網路路由傳送到您的組織網路，您必須設定含有區域網路的虛擬網路，其中包含適用於組織內部部署網路上所有可連線位置的位址空間清單 (CIDR 標記法)。定義區域網路的位址空間清單不得包含已用於其他虛擬網路或其他區域網路的位址空間或與之重疊。換句話說，適用於已設定虛擬網路和區域網路的位址空間必須是唯一的。

針對本機網路位址空間組合，請填寫表格 L。請注意，其中列出三個空白項目，但您通常需要更多項目。與您的 IT 部門合作來決定這個位址空間清單。

項目 | 區域網路位址空間 
--- | ---
1. | ___________________________________
2. | ___________________________________
3. | ___________________________________

**表格 L：適用於區域網路的位址首碼**

若要使用來自表格 V、S、D 及 L 的設定建立虛擬網路，請利用[使用設定表格建立跨單位虛擬網路](virtual-machines-workload-deploy-vnet-config-tables.md)中的指示。

> [AZURE.NOTE]此程序將逐步引導您使用網站間 VPN 連線來建立虛擬網路。如需針對網站間連線使用 ExpressRoute 的相關資訊，請參閱 [ExpressRoute 技術概觀](http://msdn.microsoft.com/library/dn606309.aspx)。

建立 Azure 虛擬網路之後，Azure 管理入口網站將會決定下列資訊：

- 適用於您虛擬網路之 Azure VPN 閘道的公用 IPv4 位址。
- 適用於網站間 VPN 連線的網際網路通訊協定安全性 (IPsec) 預先共用金鑰

若要在建立虛擬網路之後，於 Azure 管理入口網站中查看這些設定，請依序按一下 [網路]、虛擬網路的名稱，以及 [儀表板] 功能表選項。

其次，您要設定虛擬網路閘道來建立安全的網站間 VPN 連線。如需相關指示，請參閱[在管理入口網站中設定虛擬網路閘道](http://msdn.microsoft.com/library/jj156210.aspx)。

接下來，在新的虛擬網路與內部部署 VPN 裝置之間建立網站間 VPN 連線。如需詳細資訊，請參閱[在管理入口網站中設定虛擬網路閘道](http://msdn.microsoft.com/library/jj156210.aspx)來取得相關指示。

接著，確定可從您的內部部署網路連線到虛擬網路的位址空間。這通常是藉由將對應到虛擬網路位址空間的路由新增到您的 VPN 裝置，然後將該路由公告至組織網路中路由基礎結構的剩餘部分。與您的 IT 部門合作來決定如何執行這個動作。

接下來，按照[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md) 中的操作方法，在本機電腦安裝 Azure PowerShell。開啟 Azure PowerShell 命令提示字元。

首先，利用以下命令選取正確的 Azure 訂用帳戶。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

您可以從 **Get-AzureSubscription** 命令輸出的 **SubscriptionName** 屬性取得訂用帳戶名稱。

然後，建立這個 SharePoint 伺服器陣列所需的三個雲端服務。填寫表格 C。

項目 | 目的 | 雲端服務名稱 
--- | --- | ---
1. | 網域控制站 | ___________________________
2. | SQL Server | ___________________________
3. | SharePoint Server | ___________________________

**表格 C：雲端服務名稱**

您必須為每一個雲端服務選擇唯一的名稱。*雲端服務名稱可以包含字母、數字和連字號。欄位中的第一個和最後一個字元，必須是字母或數字。*

例如，您可以將第一個雲端服務命名為 DCs-*UniqueSequence*，其中的 *UniqueSequence* 是組織的縮寫。例如，如果組織名稱為 Tailspin Toys，您可以將雲端服務命名為 DCs-Tailspin。

您可以在本機電腦使用下列 Azure PowerShell 命令，測試名稱是否不重複。

	Test-AzureName -Service <Proposed cloud service name>

如果這個命令傳回「False」，表示您設定的名稱不重複。然後，使用以下命令來建立雲端服務。

	New-AzureService -Service <Unique cloud service name> -Location "<Table V – Item 2 – Value column>"

在表格 C 中記錄每一個新建立之雲端服務的實際名稱。

接下來，為 SharePoint 伺服器陣列建立儲存體帳戶。*您選取的名稱不可以和其他名稱重複而且只能使用小寫字母和數字。* 您可以使用下列 Azure PowerShell 命令，來測試儲存體帳戶名稱是否重複。

	Test-AzureName -Storage <Proposed storage account name>

如果這個命令傳回「False」，表示您設定的名稱不重複。然後，使用以下命令來建立儲存體帳戶，以及設定訂閱來使用儲存體帳戶。

	$staccount="<Unique storage account name>"
	New-AzureStorageAccount -StorageAccountName $staccount -Location "<Table V – Item 2 – Value column>"
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

接著，定義四個可用性設定組的名稱。填寫表格 A。

項目 | 目的 | 可用性設定組名稱 
--- | --- | --- 
1. | 網域控制站 | ___________________________
2. | SQL Server | ___________________________
3. | SharePoint 應用程式伺服器 | ___________________________
4. | SharePoint 前端 Web 伺服器 | ___________________________

**表格 A：可用性設定組名稱**

當您在第 2、3 及 4 階段中建立虛擬機器時，需要使用這些名稱。

此處顯示已成功完成此階段的設定。

![](./media/virtual-machines-workload-intranet-sharepoint-phase1/workload-spsqlao_01.png)

## 下一步

若要繼續設定這個工作負載，請前往[第 2 階段：設定網域控制站](virtual-machines-workload-intranet-sharepoint-phase2.md)。

## 其他資源

[在 Azure 中部署含有 SQL Server AlwaysOn 可用性群組的 SharePoint](virtual-machines-workload-intranet-sharepoint-overview.md)

[裝載於 Azure 基礎結構服務中的 SharePoint 伺服器陣列](virtual-machines-sharepoint-infrastructure-services.md)

[包含 SQL Server AlwaysOn 的 SharePoint 資訊圖](http://go.microsoft.com/fwlink/?LinkId=394788)

[適用於 SharePoint 2013 的 Microsoft Azure 架構](https://technet.microsoft.com/library/dn635309.aspx)

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=July15_HO2-->