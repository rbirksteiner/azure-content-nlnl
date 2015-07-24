<properties 
   pageTitle="多站台資料層 (Azure 架構模式)" 
   description="多站台資料層模式是基礎區域的一部分，CPIF 架構文件中有相關的詳細說明" 
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

# 多站台資料層 (Azure 架構模式)

[雲端平台整合架構 (CPIF)](azure-architectures-cpif-overview.md) 提供工作負載整合指引，供您將應用程式上架到 Microsoft 雲端解決方案。

CPIF 描述組織、客戶及合作夥伴應如何使用 Azure、System Center 及 Windows Server 的混合式雲端平台和管理功能來設計和部署以雲端為目標的工作負載。

**多站台資料層**模式是**基礎**區域的一部分，CPIF 架構文件中有相關的詳細說明

## 多站台資料層

多站台資料層設計模式詳述供應資料層服務所需的 Azure 功能和服務；資料層服務能跨越地理界限提供可預期的效能和高可用性。有鑑於此設計模式的目的，資料層的定義為以隔離方式或隨附於多層式應用程式的方式，提供傳統資料平台服務的服務層。在此模式中，資料層的負載平衡可透過在本機區域內和跨區域等方式提供。

SQL Server 2012 引進的 AlwaysOn 可用性群組是 Azure 基礎結構服務完全支援的高可用性和嚴重損壞修復功能。如需 Windows Azure 基礎結構服務上之 AlwaysOn 可用性群組的詳細資訊和官方支援宣告，請參閱「AlwaysOn 可用性群組」一文。

本文件提供在 Azure 中使用 SQL AlwaysOn 可用性群組之多站台資料層的架構概觀。在額外 Azure 資料中心內架設選擇性唯讀次要節點，可取得額外的功能和嚴重損壞修復能力。在 Azure 中使用 SQL AlwaysOn 能提供 Web 或應用程式層均可取用的高可用性資料層。

本文件著重於架構模式和實務做法，儘管如此，您也可以在官方教學課程中找到完整的部署指引，其概述 Azure 中 AlwaysOn 可用性群組的組態和 AlwaysOn 可用性群組接聽程式的組態。

## 架構模式概觀 

本文件說明可透過多個地理位置提供 Microsoft SQL Server 內容之存取權限，進而滿足可用性和備援能力之需求的模式。下圖說明的關鍵服務並非著重在存取資料本身的應用程式或 Web 層。下圖簡單說明相關服務，以及如何將它們依附在本模式中使用。

圖表之後將有關於各項主要服務區域的詳盡概述。
 
![Tags part on resource and resource group blades](./media/azure-architectures-cpif-foundation-multi-site-data-tier/overview.png)

##  其他資源
[負載平衡資料層 (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

## 另請參閱
[CPIF 架構](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[全域負載平衡 Web 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[混合式網路功能](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure 搜尋服務層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[批次處理層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=July15_HO1-->