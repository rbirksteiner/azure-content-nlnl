<properties
   pageTitle="ExpressRoute 位置"
   description="本頁面提供提供服務所在位置以及如何連線到 Azure 區域的完整概觀。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/07/2015"
   ms.author="cherylmc" />

# ExpressRoute 合作夥伴和對等互連位置
此頁面上的資料表會提供有關 ExpressRoute 連線提供者 (EXP 和 NSP)、ExpressRoute 地理涵蓋範圍、透過 ExpressRoute 支援的 Microsoft 雲端服務，以及 ExpressRoute 系統整合者 (SI) 的資訊。

## ExpressRoute 連線提供者
所有的 Azure 區域和位置都支援 ExpressRoute。以下地圖提供了 Azure 區域和 ExpressRoute 位置的清單。ExpressRoute 位置是指 Microsoft 與數個服務提供者對等互連的位置。
 
![](./media/expressroute-locations/expressroute-locations-map.png)

如果您至少與地緣政治區域內的一個 ExpressRoute 位置連線，您將有權存取地緣政治區域內所有區域中的 Azure 服務。下表提供地緣政治區域內 ExpressRoute 位置的 Azure 區域對應。

|**地緣政治區域**|**Azure 區域**|**ExpressRoute 位置**|
|---|---|---|
|**美國**|所有美國區域 - 美國東部、美國西部、美國東部 2、美國中部、美國中南部、美國中北部|亞特蘭大、芝加哥、達拉斯、洛杉磯、紐約、西雅圖、矽谷、華盛頓特區|
|**南美洲**|巴西南部|聖保羅|
|**歐洲**|北歐、西歐|阿姆斯特丹、倫敦|
|**亞洲**|東亞、東南亞|香港特別行政區、新加坡|
|**日本**|日本西部、日本東部|東京|
|**澳大利亞**|澳洲東南部、澳洲東部|墨爾本、雪梨|
|**印度**|印度西部、印度中部、印度南部|辰內，孟買|

不支援跨地緣政治區域的連線。您可以與您的連線提供者合作，使用其網路將連線延伸到不同的地緣政治區域。


## Exchange 提供者 (EXP) 位置

| **服務提供者** |**Microsoft Azure** | **Office 365** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka 網路](http://www.aryaka.com/)** | 支援 | 不支援 | 矽谷、新加坡、華盛頓特區 |
| **[Colt 乙太網路](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | 支援 | 不支援 | 阿姆斯特丹、倫敦 |
| **Comcast** | 支援 | 不支援 | 矽谷、華盛頓特區 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 支援 | 敬請期待 | 阿姆斯特丹、亞特蘭大、芝加哥、達拉斯、香港、倫敦、洛杉磯、墨爾本、紐約、聖保羅、西雅圖、矽谷、新加坡、雪梨、東京、華盛頓特區 |
| **[InterCloud](https://www.intercloud.com/)** | 支援 | 不支援 | 阿姆斯特丹、倫敦、新加坡、華盛頓特區 |
| **Internet Solutions - Cloud Connect** | 支援 | 不支援 | 阿姆斯特丹、倫敦 |
| **Interxion** | 支援 | 不支援 | 阿姆斯特丹 |
| **[Level 3 Communications - Exchange](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 支援 | 不支援 | 芝加哥、達拉斯、倫敦、西雅圖、矽谷、華盛頓特區 |
| **NEXTDC** | 支援 | 不支援 | 墨爾本、雪梨+ |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | 支援 | 敬請期待 | 阿姆斯特丹、倫敦 |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | 支援 | 不支援 | 墨爾本+、雪梨 |
| **[Zayo Group](http://www.zayo.com/)** | 支援 | 不支援 | 華盛頓 |

 **+** 表示即將推出

如需設定連線的步驟，請參閱[設定 EXP 連線](expressroute-configuring-exps.md)。

## 網路服務提供者 (NSP) 位置


| **服務提供者** |**Microsoft Azure** | **Office 365** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | 支援 | 敬請期待 | 阿姆斯特丹+、倫敦+、達拉斯、矽谷、華盛頓特區 |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | 支援 | 敬請期待 | 阿姆斯特丹、倫敦、矽谷+、華盛頓特區 |
|**China Telecom Global** | 敬請期待 | 不支援 | 香港+ |
| **[Colt IPVPN](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | 支援 | 不支援 | 阿姆斯特丹、倫敦 |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2013/pdf/Azure_E.pdf)** | 支援 | 不支援 | 東京 |
| **[Level 3 Communications - IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 支援 | 不支援 | 芝加哥、達拉斯、倫敦、西雅圖、矽谷、華盛頓特區 |
| **NTT Communications** | 敬請期待 | 不支援 | 東京+ | 
| **[Orange](http://www.orange-business.com/)** | 支援 | 不支援 | 阿姆斯特丹、倫敦、矽谷、華盛頓特區 |
| **PCCW Global Limited** | 支援 | 不支援 | 香港 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | 支援 | 不支援 | 新加坡 |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | 支援 | 敬請期待 | 阿姆斯特丹、辰內+、香港、倫敦、孟買+、新加坡 |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | 支援 | 不支援 | 墨爾本+、雪梨 |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | 支援 | 不支援 | 倫敦、香港、矽谷、華盛頓特區 |

 **+** 表示即將推出

如需設定連線的步驟，請參閱[設定 NSP 連線](expressroute-configuring-nsps.md)。

## 透過未列出的服務提供者連線 

如果上一節中未列出您的連線提供者，您仍然可以建立連線。

- 請洽詢您的連線提供者，以了解他們是否連線到列出的 EXP 位置中的任何 Exchange 提供者。您可以檢查下方的連結，以收集 Exchange 提供者所提供的服務詳細資訊。已有數個連線提供者連線到 EXP 的乙太網路 Exchange。
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/) 
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- 讓您的連線提供者將您的網路延伸到選擇的 Exchange 位置。
	- 請確保您的連線提供者以高可用性的方式延伸您的連線，因此不會有單一失敗點。
	- 為確保高可用性，連線提供者 (尤其是乙太網路提供者) 可能會要求您採購一對乙太網路交換電路。 
- 向 Exchange 提供者訂購 ExpressRoute 電路以連線至 Azure。
	- 依照[設定 EXP 連線](expressroute-configuring-exps.md)中的步驟來設定連線。

|**連線提供者**|**Exchange 提供者**|**對等互連位置**|
|---|---|---|
|**[XO 通訊](http://www.xo.com/)**|Equinix|矽谷|

## ExpressRoute 系統整合者
根據您的網路規模，為符合您的需求而啟用私人連線可能有一定的難度。您可以使用下表所列出的任何系統整合來協助您開始使用 ExpressRoute。


|**系統整合**|**Continent**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|US||
|**[Dotnet 解決方案](http://www.dotnetsolutions.co.uk/)**|EMEA|

## 後續步驟
- 驗證您是否達到 [ExpressRoute 必要條件](expressroute-prerequisites.md)。
- 如需詳細資訊，請造訪[常見問題集](expressroute-faqs.md)。
- 如果您想要設定 ExpressRoute 連線，請參閱[設定 EXP 連線](expressroute-configuring-exps.md)或[設定 NSP 連線](expressroute-configuring-nsps.md)。
- 如果您想要為相同虛擬網路設定站對站 VPN 連線和 ExpressRoute，請參閱[設定並存的 ExpressRoute 和站對站 VPN 連線](expressroute-coexist.md)。
 

<!---HONumber=July15_HO2-->