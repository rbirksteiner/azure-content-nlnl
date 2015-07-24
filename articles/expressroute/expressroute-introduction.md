<properties 
   pageTitle="ExpressRoute 簡介 | Microsoft Azure"
   description="此頁面提供 ExpressRoute 服務的概觀，包括 ExpressRoute 連線的運作方式；使用 Exchange 提供者和網路服務提供者；以及 ExpressRoute 公開、私人和 Microsoft 對等互連。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/15/2015"
   ms.author="cherylmc"/>

# ExpressRoute 技術概觀

Microsoft Azure ExpressRoute 可讓您在 Microsoft 資料中心和內部部署或共置環境中的基礎結構之間建立私人連線。透過 ExpressRoute，您可以在 ExpressRoute 合作夥伴共置設備建立連結至 Microsoft 雲端服務的連線 (例如 Microsoft Azure 和 Office 365)。或者，您可以使用網路服務提供者提供的 MPLS VPN 等，直接從現有的 WAN 網路連線。
 
相較於網際網路一般連線，ExpressRoute 連線提供更高安全性、可靠性、更快速度以及更低延遲。在某些情況下，使用 ExpressRoute 連線在內部部署網路和 Azure 之間傳輸資料，也可以產生重大的成本效益。如果您已從內部部署網路建立到 Azure 的跨單位連線，則您可以在保持虛擬網路不變的情況下移轉至 ExpressRoute 連線。

如需詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

## ExpressRoute 連線如何運作？

為了將您的 WAN 連線到 Microsoft 雲端服務，您必須向連線提供者訂購並啟用專用電路。可供選擇的連線提供者類型有兩種：透過 Exchange 提供者的直接式第 3 層 (EXP)，或透過網路服務提供者的第 3 層 (NSP)。您可以選擇啟用 WAN 和 Microsoft 雲端之間的任一或所有連線類型。

## Exchange 提供者和網路服務提供者
ExpressRoute 提供者可分類為網路服務提供者 (NSP) 和 Exchange 提供者 (EXP)。

![](./media/expressroute-introduction/expressroute-nsp-exp.png)


| |**Exchange 提供者**|**網路服務提供者**|
|---|---|---|
|**典型連線模式**| 點對點乙太網路連結或雲端交換中的連線 | 透過電信公司 VPN 的跨越任何平台連線 |
|**支援的頻寬**|200 Mbps、500 Mbps、1 Gbps 和 10 Gbps|10 Mbps、50 Mbps、100 Mbps、500 Mbps、1 Gbps|
|**連線提供者**|[Exchange 提供者](expressroute-locations.md)|[網路服務提供者](expressroute-locations.md)|
|**路由**|直接使用客戶邊緣路由器的 BGP 工作階段| 使用電信公司的 BGP 工作階段|
|**定價**|[EXP 定價](http://azure.microsoft.com/pricing/details/expressroute/)|[NSP 定價](http://azure.microsoft.com/pricing/details/expressroute/)|

### Exchange 提供者 (EXP)
我們與雲端 Exchange 服務提供者 (例如 Equinix 和 TeleCity Group) 以及點對點連線服務提供者 (例如 Cole 和 Level 3) 進行合作，為 Azure 與客戶內部部署之間提供連線。我們提供從 200 Mbps 到 10 Gbps (200 Mbps、500 Mbps、1 Gbps 和 10 Gbps) 的電路頻寬。

如果需要透過 Exchange 提供者的直接式第 3 層連線，您可以使用下列 3 種的其中一種方式：

- 您可以與雲端 Exchange (例如 Equinix 的 Cloud Exchange 或 TeleCity 的 Cloud IX) 共置於提供服務的位置。在這種情況下，您需要訂購雲端 Exchange 的備援連線。 
- 您可以使用提供者 (例如 Level 3) 來設定資料中心與 Microsoft 之間的乙太網路電路。 
- 您可以與您的本機連線提供者合作，以取得與最接近 Exchange 提供者設備的備援連線，並連線到雲端 Exchange。

我們需要您具備符合 SLA 需求的備援連線。我們不支援與 Microsoft Edge 的直接連線。您可透過乙太網路提供者或本機雲端 Exchange 隨時啟用專用電路。雖然這將會設定 Microsoft 與您的網路之間的第 2 層連線，但我們不支援擴充第 2 層網域。您必須設定邊緣路由器與 Microsoft Edge 路由器之間的備援路由工作階段，才能使用第 3 層連線。

如需關於組態的詳細資訊，以及若要查看實際範例，您可以依照本逐步解說指南進行作業：[透過 Exchange 提供者設定 ExpressRoute 連線](expressroute-configuring-exps.md)。


### 網路服務提供者 (NSP)

我們與電信公司 (例如 AT&T 和 British Telecom) 進行合作，為 Azure 和您的 WAN 之間提供連線。我們提供從 10 Mbps 到 1 Gbps (10 Mbps、50 Mbps、100 Mbps、500 Mbps 和 1 Gbps) 的電路頻寬。

如果您打算使用與我們合作的任何網路服務提供者的 VPN 服務，他們可以將網路擴充至 Azure，而無需部署任何新的硬體或無需進行現有網路的主要組態變更。

如需關於組態的詳細資訊，以及若要查看實際範例，您可以依照本逐步解說指南進行作業：[透過網路服務提供者設定 ExpressRoute 連線](expressroute-configuring-nsps.md)。

## ExpressRoute 對等互連
下圖提供您的 WAN 與 Microsoft 之間連線的邏輯表示法。您必須訂購「專用電路」，才能透過連線提供者 (NSP/EXP) 將您的 WAN 連線到 Microsoft。專用電路表示您的 WAN 與 Microsoft 之間是透過連線提供者的邏輯連線。您可以訂購許多專用電路，每個電路可以位於相同或不同區域，且可透過不同的服務提供者連線到您的 WAN。

![](./media/expressroute-introduction/expressroute-basic.png)

專用電路會有多個相關聯的路由網域 – 公用、私人和 Microsoft。每個路由網域在路由器 (使用主動-主動或載入共用組態) 配對上的設定將會完全相同，以提供高可用性。

![](./media/expressroute-introduction/expressroute-peerings.png)


### 私人對等互連
部署於虛擬網路內的 Azure 計算服務 (也就是虛擬機器 (IaaS) 和雲端服務 (PaaS)) 可透過私人對等互連網域進行連線。私人對等互連網域會被視為核心網路至 Microsoft Azure 的受信任延伸。您可以設定核心網路與 Azure 虛擬網路 (VNet) 之間的雙向連線。這將可讓您直接在私人 IP 位址上連線到虛擬機器和雲端服務。

您可以將多個虛擬網路連線到私人對等互連網域。如需限制的相關資訊，請檢閱[常見問題集頁面](expressroute-faqs.md)。
  

### 公用對等互連
公用 IP 位址上提供如 Azure 儲存體、SQL Database 和網站等服務。您可以透過公用對等互連路由網域，私人連線到在公用 IP 位址上託管的服務 (包括雲端服務的 VIP)。您可以將公用對等互連網域，從您的 WAN 連線到外部網路及連線到其公用 IP 位址上的所有 Azure 服務，而無需透過網際網路進行連線。一律會從您的 WAN 啟動連線到 Microsoft Azure 服務。Microsoft Azure 服務將無法透過這個路由網域啟動連線到您的網路。一旦啟用公用對等互連，您便能夠連線到所有 Azure 服務。我們不允許您選擇性地選取已通告路由的服務。您可以在 [Microsoft Azure 資料中心 IP 範圍](http://www.microsoft.com/download/details.aspx?id=41653)頁面中，檢閱我們透過這個對等互連向您通告的字首清單。您可以在您的網路內定義自訂路由篩選條件，以便僅取用所需的路由。

如需透過公用對等互連路由網域支援的服務詳細資料，請檢閱[常見問題集頁面](expressroute-faqs.md)。
 
### Microsoft 對等互連
與所有其他 Microsoft 線上服務 (例如 Office 365 服務) 的連線將會透過 Microsoft 對等互連進行。我們會透過 Microsoft 對等互連路由網域啟用 WAN 與 Microsoft 雲端服務之間的雙向連線。您必須只透過您或連線提供者所擁有的公用 IP 位址連線到 Microsoft 雲端服務，且必須遵守我們所定義的所有規則。如需詳細資訊，請檢閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。

如需支援的服務、費用和組態詳細資料，請檢閱[常見問題集頁面](expressroute-faqs.md)。如需提供 Microsoft 對等互連支援的連線提供者清單，請檢閱 [ExpressRoute 位置](expressroute-locations.md)頁面。


下表是這三種路由網域的比較。

||**私人對等互連**|**公用對等互連**|**Microsoft 對等互連**| |---|---|---|---| |**每種對等互連支援的字首數目上限**|預設值為 4,000，在 ExpressRoute Premium 中為 10,000 |預設值為 4,000，在 ExpressRoute Premium 中為 10,000 |200| |**支援的 IP 位址範圍**|您 WAN 內任何有效的 IPv4 位址 |您或您的連線提供者所擁有的公用 IPv4 位址 |您或您的連線提供者所擁有的公用 IPv4 位址 |**AS 編號需求**|私人和公用 AS 編號。客戶必須擁有公用 AS 編號。| 私人和公用 AS 編號。客戶必須擁有公用 AS 編號。| 僅限公用 AS 編號。必須驗證 AS 編號的路由註冊以驗證擁有權。| |**路由介面 IP 位址**|RFC1918 和公用 IP 位址|註冊給客戶的公用 IP 位址 / 路由註冊中的 NSP。| 註冊給客戶的公用 IP 位址 / 路由註冊中的 NSP。| |**MD5 雜湊支援**| 是|是|是|

您可以選擇啟用一或多個路由網域作為其專用電路的一部分。如果想要將所有路由網域內嵌到單一路由網域中，您可以選擇將所有路由網域置於相同的 VPN (適用於 NSP 情況)。您也可以將他們置於與上圖類似的不同路由網域。我們的建議設定是，私人對等互連直接連線到核心網路，而公用和 Microsoft 對等互連連結則連線到您的外部網路。
 
如果您選擇使用全部三種對等互連工作階段，就必須擁有三個 BGP 工作階段配對 (每個對等互連類型一組)。BGP 工作階段配對提供高可用性連結。如果透過 EXP 進行連線，您將負責設定和管理路由 (除非 EXP 可為您管理路由)。如果選擇透過 NSP 進行連線，您可以仰賴 NSP 為您管理路由。如需深入瞭解更多資訊，請檢閱設定 ExpressRoute 的工作流程


## 後續步驟

- 尋找服務提供者。請參閱 [ExpressRoute 服務提供者和位置](expressroute-locations.md)。
- 設定 ExpressRoute 連線。如需相關指示，請參閱[透過網路服務提供者設定 ExpressRoute 連線](expressroute-configuring-nsps.md)或[透過 Exchange 提供者設定 ExpressRoute 連線](expressroute-configuring-exps.md)。 

<!---HONumber=July15_HO2-->