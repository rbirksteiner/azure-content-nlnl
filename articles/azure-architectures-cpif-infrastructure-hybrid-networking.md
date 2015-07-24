<properties 
   pageTitle="混合式網路功能 (Azure 架構模式)" 
   description="混合式網路功能模式是基礎結構區域的一部分，CPIF 架構文件中有相關的詳細說明。" 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# 混合式網路功能 (Azure 架構模式)

[雲端平台整合架構 (CPIF)](azure-architectures-cpif-overview.md) 提供工作負載整合指引，供您將應用程式上架到 Microsoft 雲端解決方案。

CPIF 描述組織、客戶及合作夥伴應如何使用 Azure、System Center 及 Windows Server 的混合式雲端平台和管理功能來設計和部署以雲端為目標的工作負載。

**混合式網路功能**模式是**基礎結構**區域的一部分，CPIF 架構文件中有相關的詳細說明。

##  混合式網路功能

混合式網路功能設計模式詳述供應網路功能所需的 Azure 功能和服務；網路功能可跨越地理界限提供可預期的效能和高可用性。Microsoft Azure 文件站台提供 Microsoft Azure 區域和各區域可用之服務的完整清單。本文件提供 Microsoft Azure 混合式環境之網路功能的概觀。Microsoft Azure 虛擬網路功能可讓您在 Azure 中建立邏輯隔離的網路，並透過網際網路或使用私用網路連線將其連接到內部部署資料中心。此外，您也可以使用 IPsec VPN 連線將個別的用戶端機器連接到隔離的 Azure 網路。

混合式網路功能架構模式包含下列焦點區域：

- 將內部部署網路連接到 Azure 
- 跨越區域延伸 Azure 虛擬網路 
- 在訂用帳戶之間延伸 Azure 虛擬網路 
- 為開發人員提供遠端網路存取 

## 架構模式概觀 

因可建立的可行案例數目不足，導致混合式網路功能架構模式過於複雜。本架構模式將著重在下列四個案例：

- 單一訂用帳戶和區域內，搭配多重躍點虛擬網路路由的站對站混合式網路功能 
- 跨越訂用帳戶和區域，搭配多重躍點虛擬網路路由的站對站混合式網路功能 
- 使用 MPLS 連線功能的 ExpressRoute 混合式網路功能 
- 使用 IXP 連線功能的 ExpressRoute 混合式網路功能 

##  其他資源
[混合式網路功能 (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

## 另請參閱
[CPIF 架構](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[全域負載平衡 Web 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[負載平衡資料層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Azure 搜尋服務層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[批次處理層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=July15_HO1-->