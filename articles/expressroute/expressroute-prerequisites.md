<properties
   pageTitle="ExpressRoute 應用的必要條件"
   description="本頁面提供在訂購 ExpressRoute 電路之前必須符合的需求清單。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/06/2015"
   ms.author="cherylmc"/>


# ExpressRoute 必要條件  

若要使用 ExpressRoute 連線到 Microsoft 雲端服務，您必須驗證是否符合下列必要條件：

## 連線的必要條件

- 使用中的有效 Microsoft Azure 帳戶
- 必須透過連線促進與[支援清單](expressroute-locations.md)中網路服務提供者 (NSP) 或 Exchange 提供者 (EXP) 的關係。您必須具備與網路服務提供者或 Exchange 提供者的現有業務關係。您必須確定使用的服務與 ExpressRoute 相容。
- 如果您打算使用網路服務提供者，但上述清單中未列出您的網路服務提供者，您仍然可以建立與 Azure 的連線。
	- 請洽詢您的網路提供者，以確認他們是否出現在上方所列的任何 Exchange 位置中。
	- 讓您的網路提供者將您的網路延伸到選擇的 Exchange 位置。
	- 向 Exchange 提供者訂購 ExpressRoute 電路以連線至 Azure。
- 連線到服務提供者的基礎結構。您必須符合至少下列其中一個所列項目的條件：
	- 您是網路服務提供者的 VPN 客戶，並至少有一個內部部署網站已連線到服務提供者的 VPN 基礎結構。請洽詢您的網路服務提供者，以了解您的 VPN 服務是否符合 ExpressRoute 的需求。
	- 您的基礎結構會共置於 Exchange 提供者的資料中心。
	- 您已設定乙太網路連線到 Exchange 提供者的乙太網路 Exchange 基礎結構。
- 路由組態的 IP 位址和 AS 編號
	- 您可以使用私人 AS 編號來連線到 Azure 私人對等互連路由網域。如果您選擇執行此作業，此值必須大於 65000。如需有關 AS 編號的詳細資訊，請參閱[自發系統 (AS) 編號](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml)。
	- 可設定路由的 IP 位址。/28 子網路為必要項目。這不能與您的內部部署或 Azure 中使用的任何 IP 位址範圍重疊。
	- 您必須使用專屬的公用 AS 編號，才能設定 Azure 公用服務中的 BGP 工作階段。

## 後續步驟

- 如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
- 如需如何設定 ExpressRoute 連線的詳細資訊，請參閱
	- [透過網路服務提供者設定 ExpressRoute 連線](expressroute-configuring-nsps.md)
	- [透過 Exchange 提供者設定 ExpressRoute 連線](expressroute-configuring-exps.md)
 

<!---HONumber=July15_HO2-->