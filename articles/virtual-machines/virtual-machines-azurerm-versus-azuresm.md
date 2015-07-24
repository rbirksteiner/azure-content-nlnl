<properties
   pageTitle="Azure Resource Manager 提供的 Azure 運算、網路和儲存提供者"
   description="運算、網路和儲存體資源提供者 (CRP、NRP 和 SRP) 的概念簡介"
   services="virtual-machines"
   documentationCenter="dev-center-name"
   authors="mahthi"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="mahthi"/>

# Azure Resource Manager 提供的 Azure 運算、網路和儲存提供者

Azure Resource Manager 包含的運算、網路與存放功能，可以大幅簡化各種 IaaS 複雜應用程式的部署和管理。很多應用程式需要各種的資源組合，其中包括虛擬網路、儲存體帳戶、虛擬機器以及網路介面。Azure Resource Manager 可以建構 JSON 範本，然後做為單一應用程式部署所有這些資源，並集中管理。

## Azure Resource Manager 提供整合運算、網路和存放功能等優點

Azure Resource Manager 可讓您輕鬆運用預先建立的應用程式範本，或建構應用程式範本來部署以及管理 Azure 上的運算、網路和儲存體資源。在本節中，我們將詳細說明利用 Azure Resource Manager 部署資源有哪些優點。

-	化繁為簡 -- 利用一個可共用的範本檔案來建立、整合及協調各種應用程式，它們可能包含整個範圍的 Azure 資源，例如網站、SQL 資料庫、虛擬機器或虛擬網路。
-	當您使用相同的範本檔案時，可以彈性重複部署開發、devOps 和系統管理員
-	利用 Azure Resource Manager 將 VM 延伸模組 (自訂指令碼、DSC、Chef、Puppet 等) 與範本檔案進行深度整合，輕鬆協調 VM 內部的各項安裝設定
-	為運算、網路和儲存體資源的標記，定義標記和帳單傳送
-	使用 Azure 角色型存取控制 (RBAC)，輕鬆準確管理組織資源的取用
-	修改原始範本，然後重新部署，讓整個升級/更新過程更加簡化


## Azure Resource Manager 提供各種先進的運算、網路和儲存體 API

除了上述優點之外，發行的 API 在效能方面還有一些重要的改善。

-	大規模平行部署虛擬機器
-	可用性設定組 (Availability Set) 支援 3 個容錯網域
-	改良的「自訂指令碼」延伸模組，允許從任何可公開存取的自訂 URL 指定指令碼
- 虛擬機器結合 Azure Key Vault，可以實現儲存體的高度安全而且還可以從 [FIPS 驗證的](http://wikipedia.org/wiki/FIPS_140-2)[硬體安全模組](http://wikipedia.org/wiki/Hardware_security_module)部署私人密碼
-	透過 API 提供基本的網路建置區塊，讓客戶建構複雜的應用程式，包括網路介面、負載平衡器，以及虛擬網路
-	「網路介面」做為新物件可以持續設定複雜的網路組態，並重複應用到虛擬機器
-	「負載平衡器」做為第一級資源可以指派 IP 位址
-	「細微虛擬網路 API」可讓您簡化個別虛擬網路的管理

## 新 API 帶來的觀念差異

在本節中，我們會詳細解說時下流行的 XML 型 API 和透過適用於運算、網路和儲存體的 Azure Resource Manager 所提供的  JSON 型 API，二者之間存在的重大觀念差異。

 項目 | Azure 服務管理 (XML 型) | 運算、網路和儲存提供者 (JSON 型)
 ---|---|---
| 虛擬機器的雲端服務 |	雲端服務是一種容器，專門保管那些要求平台和負載平衡可用性的虛擬機器。 | 使用新模型建立虛擬機器時，雲端服務已經不是必要的物件了。 |
| 可用性設定組 (Availability Sets) | 在虛擬機器上設定相同的 "AvailabilitySetName" 之後，即表示平台的可用性。容錯網域的最大個數為 2。 | 「可用性設定組」是 Microsoft.Compute 提供者公開的資源。需要高可用性的虛擬機器必須包含在「可用性設定組」中。容錯網域的最大個數現在是 3。 |
| [同質群組] |	建立虛擬網路時需要同質群組。不過，隨著區域虛擬網路引進，就再也不需要了。 |簡而言之，透過 Azure Resource Manager 而公開的 API，其實不存在同質群組這種概念。 |
| 負載平衡 | 雲端服務的建立，為部署的虛擬機器提供隱含的負載平衡器。 | 負載平衡器是 Microsoft.Network 提供者所公開的資源。虛擬機器如果需要平衡負載，其主要網路介面應該參考負載平衡器。負載平衡器可以放在內部或外部。[閱讀更多。](resource-groups-networking.md) |
|虛擬 IP 位址 | 將 VM 新增到雲端服務後，雲端服務會得到預設的 VIP (虛擬 IP 位址)。虛擬 IP 位址是隱含性負載平衡器的相關位址。 | 公用 IP 位址是 Microsoft.Network 提供者所公開的資源。公用 IP 位址可以是靜態 (保留) 或動態。動態公用 IP 可以指派至負載平衡器。使用安全性群組可以保護公用 IP。 |
|保留 IP 位址|	您可以將 IP 位址保留在 Azure 中，然後與雲端服務建立關聯，確保 IP 位址不會變動。 | 您可以在「靜態」模式中建立公用 IP 位址，然後它就具備「保留的 IP 位址」一樣的功能。靜態公用 IP 現在只能指派至負載平衡器。 |
|每一個 VM 的公用 IP 位址 (PIP) | 公用 IP 位址也可以直接與 VM 建立關聯。 | 公用 IP 位址是 Microsoft.Network 提供者所公開的資源。公用 IP 位址可以是靜態 (保留) 或動態。不過，只有動態公用 IP 才可以指派至網路介面，以立即取得每一個 VM 的公用 IP。 |
|端點| 輸入端點需要在開放特定連接埠連線的虛擬機器上設定。設定輸入端點之後，就能完成幾個常見的虛擬機器連線模式之一。| 您可以在負載平衡器上設定「傳入 NAT 規則」，以達到啟用特定連接埠上的端點以連線至 VM 的相同功能。 |
|DNS 名稱| 雲端服務會取得隱含的全域唯一 DNS 名稱。例如：`mycoffeeshop.cloudapp.net`。 | DNS 名稱是可以在公用 IP 位址資源上指定的選用參數。FQDN 的格式如下 - `<domainlabel>.<region>.cloudapp.azure.com`。 |
|網路介面 | 主要和次要網路介面與其屬性會定義為虛擬機器的網路組態。 | 網路介面是 Microsoft.Network 提供者所公開的資源。網路介面的生命週期與虛擬機器無關。 |

## 開始使用適用於虛擬機器的 Azure 範本

您可以利用我們專為開發和部署到平台而設計的工具，開始使用 Azure 範本。

### Azure 入口網站

Azure 入口網站會繼續允許使用者同時部署虛擬機器和虛擬機器 (預覽)。Azure 入口網站也允許部署自訂的範本。

### Azure PowerShell

Azure PowerShell 有兩種部署模式 - **AzureServiceManagement** 模式和 **AzureResourceManager** 模式。Azure ResourceManager 模式現在也包含 Cmdlet 來管理虛擬機器、虛擬網路和儲存體帳戶。您可以在[此處](../powershell-azure-resource-manager.md)閱讀相關資訊。

### Azure CLI

Azure 命令列介面 (Azure CLI) 有兩種部署模式 - **AzureServiceManagement** 模式和 **AzureResourceManager** 模式。AzureResourceManager 模式現在也包含管理虛擬機器、虛擬網路和儲存體帳戶的命令。您可以在[此處](xplat-cli-azure-resource-manager.md)閱讀相關資訊。

### Visual Studio

利用適用於 Visual Studio 的 Azure SDK 最新版本，您可以直接從 Visual Studio 設計以及部署虛擬機器和複雜的應用程式。利用 Visual Studio，您可以從預先建立的範本清單或從空白範本開始進行部署。

### REST API

您可以到[這裡](https://msdn.microsoft.com/library/azure/dn790568.aspx)找到與運算、網路和儲存體資源提供者有關的 REST API 詳細說明文件。

## 常見問題集

**我可以使用新的 Azure Resource Manager 建立虛擬機器，來部署使用 Azure Service Management API 所建立的虛擬網路或儲存體帳戶嗎？**

目前尚未支援。使用新的 Azure Resource Manager API 並無法將虛擬機器部署到使用 Service Management API 建立的虛擬網路中。

**我可以從使用 Azure Resource Manager API 建立的使用者映像建立使用新的 Azure Resource Manager API 的虛擬機器嗎？**

目前尚未支援。不過，您可以複製使用 Service Management API 所建立之儲存體帳戶中的 VHD 檔案，並將 VHD 檔案複製到使用新的 Azure Resource Manager API 建立的新帳戶。

**對訂閱的配額有何影響？**

虛擬機器、虛擬網路和透過新 Azure Resource Manager API 建立的儲存體帳戶，它們之間的配額與您現有的配額是分開的。每個訂閱會得到新的配額，然後就可以使用新的 API 建立資源。如需其他配額的詳細資訊，請參閱[這裡](../azure-subscription-service-limits.md)。

**我可以透過新的 Azure Resource Manager API，繼續使用自動指令碼佈建虛擬機器、虛擬網路、儲存體帳戶嗎？**

所有您建立的自動化和指令碼，仍然適用於 Azure 服務管理模式下建立的現有虛擬機器和虛擬網路。不過，您必須更新指令碼，才能使用新的結構描述並透過 Azure Resource Manager 模式建立相同的資源。
進一步了解如何修改 [Azure CLI 指令碼](xplat-cli-azure-manage-vm-asm-arm.md)。

**使用新的 Azure Resource Manager API 建立的虛擬網路，可以連線到我的 Express Route Circuit 嗎？**

目前尚未支援。使用新的 Azure Resource Manager API 建立的虛擬網路並無法連線到 Express Route Circuit。未來將會支援。

<!--HONumber=52-->
 