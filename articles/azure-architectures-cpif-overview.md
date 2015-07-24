<properties 
   pageTitle="雲端平台整合架構 - -Azure 架構模式" 
   description="雲端平台整合架構提供工作負載整合指引，供您將應用程式上架到由 Microsoft Azure 架構模式所組成的 Microsoft 雲端解決方案" 
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


# 雲端平台整合架構 (Azure 架構模式)

雲端平台整合架構 (CPIF) 提供工作負載整合指引，供您將應用程式上架到 Microsoft 雲端解決方案。

CPIF 描述組織、客戶及合作夥伴應如何使用 Azure、System Center 及 Windows Server 的混合式雲端平台和管理功能來設計和部署以雲端為目標的工作負載。CPIF 網域可分解成下列功能：

![Tags part on resource and resource group blades](./media/azure-architecture-cpif-overview/overview.png)

##  CPIF 架構

公用和私用雲端環境均提供支援複雜工作負載執行的通用元素。雖然這些架構在傳統內部部署和虛擬化環境中較廣為人知，不過 Microsoft Azure 中的建構需要經過額外規劃才能將公用雲端環境中的基礎結構和平台功能合理化。為了支援裝載於 Azure 中的應用程式或服務，我們需要一系列模式來概述撰寫指定工作負載解決方案所需的各種元件。

這些架構模式可分為以下類別：

- **基礎結構** – Microsoft Azure 是基礎結構和平台即服務解決方案，它包含幾個基礎服務和功能。這些服務主要可分成計算、儲存體和網路服務，不過也有幾項無法以這些定義歸納的功能。基礎結構模式詳述 Microsoft Azure 的特定功能區域，其為將特定服務提供給一或多個裝載於特定 Azure 訂用帳戶之解決方案時的必要項目。 
- **基礎** – 在 Azure 中撰寫多層式應用程式或服務時，您必須搭配使用幾個元件才能提供合適的裝載環境。基礎模式能從 Microsoft Azure 構成一或多個服務，藉此支援應用程式內特定層級的功能。這可能需要使用上述基礎結構模式描述的一或多個元件。例如，多層式應用程式的展示層需要 Azure 中的計算、網路和儲存體功能才能正常運作。基礎模式的構成僅限搭配特定解決方案隨附的其他模式。
- **解決方案** – 解決方案模式由基礎結構和/或基礎模式所組成，它能呈現開發中的終端應用程式或服務。我們可以想像，複雜解決方案的開發將無法獨立於其他模式之外單獨進行。解決方案應使用前述各模式類別所定義的元件和介面。    

## Azure 架構模式概念

為了支援 Azure 中的解決方案方案架構開發，我們提供一系列適用於常見案例的模式指南。這些案例指南的建構需要一段時間，不過我們已設想到下列模式：

- 全域負載平衡 Web 層 
- 負載平衡資料層
- 批次處理層
- 混合式網路功能
- Azure 搜尋服務 

##  其他資源
[CPIF 架構 (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

## 另請參閱
[全域負載平衡 Web 層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[負載平衡資料層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[批次處理層](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

[Azure 網路功能](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Azure 搜尋服務](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=July15_HO1-->