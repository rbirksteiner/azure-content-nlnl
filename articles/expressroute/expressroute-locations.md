<properties
   pageTitle="ExpressRoute-locaties | Microsoft Azure"
   description="Dit artikel bevat een gedetailleerd overzicht van de locaties waar services worden aangeboden en hoe u verbinding maakt met Azure-regio's."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/05/2016"
   ms.author="cherylmc" />

# Partners en peeringlocaties voor ExpressRoute

In de tabellen in dit artikel vindt u informatie over ExpressRoute-connectiviteitsproviders, geografische dekking van ExpressRoute, Microsoft Cloud-services die via ExpressRoute worden ondersteund en Expressroute SI's (System Integrator).

## <a name="partners"></a>ExpressRoute-connectiviteitsproviders

ExpressRoute wordt ondersteund in alle Azure-regio's en -locaties. De volgende kaart bevat een lijst van Azure-regio's en ExpressRoute-locaties. ExpressRoute-locaties zijn locaties waarop Microsoft samenwerkt met verschillende serviceproviders.

![Locatiekaart][0]

U hebt toegang tot Azure-services in alle regio's binnen een geopolitieke regio als u bent verbonden met ten minste één ExpressRoute-locatie in die geopolitieke regio. In de volgende tabel vindt u een toewijzing van Azure-regio's aan ExpressRoute-locaties binnen een geopolitieke regio.

|**Geopolitieke regio**|**Azure-regio's**|**ExpressRoute-locaties**|
|---|---|---|
|**Noord-Amerika**|VS - oost, VS - west, VS - oost 2, VS - midden, Zuid-centraal VS, Noord-centraal VS, Canada Centraal, Canada Oost|Atlanta, Chicago, Dallas, Las Vegas+, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, Montreal+, Quebec City+, Toronto|
|**Zuid-Amerika**|Brazilië - zuid|Sao Paulo|
|**Europa**|Noord-Europa, West-Europa|Amsterdam, Dublin, Londen, Newport(Wales)+, Parijs+|
|**Azië**|Oost-Azië, Zuidoost-Azië|Hongkong, Singapore|
|**Japan**|Japan - west, Japan - oost|Osaka, Tokio|
|**Australië**|Australië - zuidoost, Australië - oost|Melbourne, Sydney|
|**India**|India - west, India - midden, India - zuid|Chennai, Mumbai|



De volgende tabel bevat informatie over regio's en geopolitieke grenzen voor nationale clouds.

|**Geopolitieke regio**|**Azure-regio's**|**ExpressRoute-locaties**|
|---|---|---|---|
|**Cloud van de Amerikaanse overheid**|VS (overheid) - Iowa, VS (overheid) - Virginia|Chicago, Dallas+, New York, Washington DC|
|**China**|China Noord, China Oost|Beijing, Shanghai|
|**Duitsland**|Duitsland Centraal, Duitsland Oost|Berlijn, Frankfurt|


Connectiviteit tussen de geopolitieke regio's wordt niet ondersteund op de standaard ExpressRoute-SKU. U moet de invoegtoepassing ExpressRoute Premium inschakelen voor ondersteuning van globale connectiviteit. Connectiviteit met nationale cloudomgevingen wordt niet ondersteund. U kunt met uw connectiviteitsprovider samenwerken als de noodzaak daartoe zich voordoet.


## Locaties van connectiviteitsproviders

### Productie-Azure

| **Serviceprovider**  |**Microsoft Azure** | **Office 365 en CRM Online** | **Locaties** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks]( http://www.aryaka.com/)** | Ondersteund | Ondersteund | Amsterdam, Silicon Valley, Singapore, Tokio, Washington DC |
| **[AT&T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Ondersteund | Ondersteund | Amsterdam, Chicago, Dallas, Londen, Silicon Valley, Singapore, Sydney, Washington DC |
| **[British Telecom]( http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Ondersteund | Ondersteund | Amsterdam, Hongkong, Londen, Silicon Valley, Singapore, Sydney, Tokio, Washington DC |
|**CenturyLink** | Binnenkort beschikbaar | Binnenkort beschikbaar| Silicon Valley |
|**China Telecom Global** | Ondersteund | Niet ondersteund | Hongkong |
|**Cologix** | Ondersteund | Binnenkort beschikbaar | Montreal+, Toronto |
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)**  |  Ondersteund | Ondersteund | Amsterdam, Dublin, Londen, Tokio |
| **Comcast** | Ondersteund | Ondersteund | Chicago, Silicon Valley, Washington DC |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | Ondersteund | Ondersteund | Los Angeles | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Ondersteund | Ondersteund | Amsterdam, Atlanta, Chicago, Dallas, Hongkong, Londen, Los Angeles, Melbourne, New York, Osaka, Sao Paulo, Seattle, Silicon Valley, Singapore, Sydney, Tokio, Toronto, Washington DC |
| **euNetworks** |  Ondersteund | Ondersteund | Amsterdam |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |  Ondersteund | Ondersteund | Osaka, Tokio |
| **[InterCloud]( https://www.intercloud.com/)** | Ondersteund | Ondersteund | Amsterdam, Londen, Singapore, Washington DC |
| **Internet Solutions - Cloud Connect** | Ondersteund | Ondersteund | Amsterdam, Londen |
| **Interxion** | Ondersteund | Ondersteund | Amsterdam, Londen |
| **[Level 3 Communications]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Ondersteund | Ondersteund | Amsterdam, Chicago, Dallas, Las Vegas+, Londen, Seattle, Silicon Valley, Washington DC |
| **Megaport** | Ondersteund | Ondersteund | Dallas Las Vegas+, Los Angeles, Melbourne, New York, Seattle, Singapore, Sydney, Washington DC |
| **MTN** | Ondersteund | Ondersteund | Londen |
| **NEXTDC** | Ondersteund | Ondersteund | Melbourne, Sydney |
| **NTT Communications** | Ondersteund | Ondersteund | Londen, Osaka, Tokio |
| **[Orange]( http://www.orange-business.com/en/products/business-vpn-galerie)** | Ondersteund | Ondersteund | Amsterdam, Hongkong, Londen, Silicon Valley, Singapore, Washington DC |
| **PCCW Global Limited** | Ondersteund | Ondersteund | Hongkong |
| **[SingTel]( http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |  Ondersteund | Ondersteund | Singapore |
| **Softbank** | Ondersteund | Ondersteund | Osaka, Tokio | 
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Ondersteund | Ondersteund | Amsterdam, Chennai, Hongkong, Londen, Mumbai, Silicon Valley, Singapore, Washington DC |
| **[TeleCity Group]( http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Ondersteund | Ondersteund | Amsterdam, Londen |
| **Telefonica** | Binnenkort beschikbaar | Binnenkort beschikbaar | Sao Paulo+ |
| **Telenor** | Ondersteund | Ondersteund | Amsterdam, Londen |
| **[Telstra Corporation]( http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Ondersteund | Niet ondersteund | Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Ondersteund | Ondersteund | Amsterdam, Hongkong, Londen, Silicon Valley, Singapore, Sydney, Tokio, Washington DC |
| **Vodafone** | Ondersteund | Niet ondersteund | Londen | 
| **[Zayo Group]( http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Ondersteund | Ondersteund | Chicago, Los Angeles, New York, Silicon Valley Toronto, Washington DC |

 **+** betekent binnenkort beschikbaar

### Nationale cloudomgevingen

#### Cloud van de Amerikaanse overheid

| **Serviceprovider**  |**Microsoft Azure** | **Office 365** | **Locaties** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Ondersteund | Ondersteund | Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Ondersteund | Ondersteund | Chicago, New York, Washington DC |
| **[Level 3 Communications - IPVPN]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Ondersteund | Binnenkort beschikbaar | Chicago, New York+, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Ondersteund | Ondersteund | Chicago, New York, Washington DC |

#### China

| **Serviceprovider**  |**Microsoft Azure** | **Office 365** | **Locaties** |
|-----------------------|--------------------|----------------|---------------|
| **China Telecom** | Ondersteund | Niet ondersteund | Beijing, Shanghai|
Zie [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/) voor meer informatie.

#### Duitsland

| **Serviceprovider**  |**Microsoft Azure** | **Office 365** | **Locaties** |
|-----------------------|--------------------|----------------|---------------|
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Ondersteund | Niet ondersteund | Berlijn+, Frankfurt|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Binnenkort beschikbaar | Niet ondersteund | Frankfurt+|
| **e-shelter** | Binnenkort beschikbaar | Niet ondersteund | Berlijn+|
| **Interxion** | Ondersteund | Niet ondersteund | Frankfurt|

## <a name="nonpartners"></a>Connectiviteit via niet vermelde serviceproviders

Als uw connectiviteitsprovider niet wordt vermeld in de vorige secties, kunt u alsnog verbinding maken.

- Neem contact op met uw connectiviteitsprovider om na te gaan of ze zijn verbonden met een van de exchange-punten in de bovenstaande tabel. Via de volgende koppelingen vindt u meer informatie over services die door de exchange-providers worden verstrekt. Verschillende connectiviteitsproviders zijn al verbonden met Ethernet-exchange-punten.

    - [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [InterXion](http://www.interxion.com/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
- Vraag uw connectiviteitsprovider om uw netwerk uit te breiden tot de gewenste peeringlocatie.
    - Vergewis u ervan dat de connectiviteitsprovider uw connectiviteit uitbreidt op een maximaal beschikbare manier, zodat er geen storingspunten zijn.
- Vraag een ExpressRoute-circuit aan met het exchange-punt wanneer uw connectiviteitsprovider verbinding maakt met Microsoft.
    - Volg de stappen in [Create an ExpressRoute circuit](expressroute-howto-circuit-classic.md) (Een ExpressRoute-circuit maken) om connectiviteit in te stellen.

|**Connectiveitsprovider**|**Exchange**|**Locaties**|
|---|---|---|
|**Alaska Communications**|Equinix|Seattle|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|

## ExpressRoute-SI's

Het inschakelen van particuliere connectiviteit conform uw specifieke behoeften kan lastig zijn, al naargelang de schaal van uw netwerk. U kunt alle SI's uit de volgende tabel gebruiken om u te helpen met de voorbereidingen voor ExpressRoute.

|**System integrator**|**Continent**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| Azië, Europa, VS |
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**| Europa |
|**[Nimbo](http://www.nimbo.com/)**|VS||
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Azië |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | VS |
|**[Project Leadership](http://www.projectleadership.net/azure)** | VS |

## Volgende stappen

- Voor meer informatie over ExpressRoute raadpleegt u de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md).
- Controleer of aan alle vereisten is voldaan. Zie [ExpressRoute prerequisites](expressroute-prerequisites.md) (Vereisten voor ExpressRoute).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Locatiekaart"



<!--HONumber=Jun16_HO2-->


