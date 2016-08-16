<properties
   pageTitle="Routeringsvereisten voor ExpressRoute | Microsoft Azure"
   description="Deze pagina bevat gedetailleerde vereisten voor het configureren en beheren van routering voor ExpressRoute-circuits."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/01/2016"
   ms.author="cherylmc"/>


# Routeringsvereisten voor ExpressRoute  

Als u ExpressRoute wilt gebruiken om verbinding te maken met Microsoft Cloud-services, moet u routering instellen en beheren. Sommige connecitiviteitsproviders bieden het instellen en beheren van routering aan als een beheerde service. Neem contact op met uw connectiviteitsprovider om na te gaan of ze deze service leveren. Als dat niet het geval is, moet u voldoen aan de vereisten die hieronder worden beschreven. 

Raadpleeg het artikel [Circuits en Routeringsdomeinen](expressroute-circuit-peerings.md) (Circuits en routeringsdomeinen) voor een beschrijving van de routeringssessies die moeten worden ingesteld om connectiviteit te vergemakkelijken.

**Opmerking:** Microsoft biedt geen ondersteuning voor routerredundantieprotocollen (zoals HSRP, VRRP) voor de configuratie van hoge beschikbaarheid. We zijn afhankelijk van twee redundante BGP-sessies per peering voor maximale beschikbaarheid.

## IP-adressen voor peerings

U moet enkele blokken met IP-adressen reserveren om routering tussen uw netwerk en de MSEE-routers (Microsoft Enterprise Edge) te configureren. In deze sectie vindt u een lijst met vereisten en worden de regels beschreven met betrekking tot hoe u deze IP-adressen kunt verkrijgen en gebruiken.

### IP-adressen voor persoonlijke Azure-peering

U kunt privé IP-adressen of openbare IP-adressen gebruiken om de peerings te configureren. Het adresbereik dat wordt gebruikt voor het configureren van routes mag geen adresbereiken overlappen die worden gebruikt voor het maken van virtuele netwerken in Azure. 

 - U moet een /29-subnet of twee /30-subnetten voor routeringsinterfaces reserveren.
 - De subnetten voor routering kunnen privé of openbare IP-adressen zijn.
 - De subnetten mogen geen conflicten opleveren met het bereik dat door de klant is gereserveerd voor gebruik in de Microsoft Cloud.
 - Als een /29-subnet wordt gebruikt, wordt dit verdeeld in twee /30-subnetten. 
     - Het eerste /30-subnet wordt gebruikt voor de primaire koppeling en het tweede/30-subnet voor de secundaire koppeling.
     - Voor beide /30-subnetten moet u het eerste IP-adres van het /30-subnet op de router gebruiken. Microsoft gebruikt het tweede IP-adres van het/30-subnet voor het instellen van een BGP-sessie.
     - Onze [beschikbaarheids-SLA](https://azure.microsoft.com/support/legal/sla/) is alleen geldig als beide BGP-sessies zijn ingesteld.  

#### Voorbeeld voor persoonlijke peering

Als u a.b.c.d/29 gebruikt om de peering in te stellen, wordt dit gesplitst in twee /30-subnetten. In onderstaand voorbeeld kijken we hoe het subnet a.b.c.d/29 wordt gebruikt. 

a.b.c.d/29 wordt gesplitst in a.b.c.d/30 en a.b.c.d+4/30 en via de inrichting-API's doorgegeven aan Microsoft. U gaat a.b.c.d+1 gebruiken als de VRF-IP voor de primaire PE en Microsoft gaat a.b.c.d+2 gebruiken als de VRF-IP voor de primaire MSEE. U gaat a.b.c.d+5 gebruiken als de VRF-IP voor de secundaire PE en Microsoft gaat a.b.c.d+6 gebruiken als de VRF-IP voor de secundaire MSEE.

Stelt u zich een situatie voor waarin u 192.168.100.128/29 selecteert om persoonlijke peering in te stellen. 192.168.100.128/29 bevat adressen van 192.168.100.128 tot 192.168.100.135, waarbij:

- 192.168.100.128/30 wordt toegewezen aan link1, waarbij de provider 192.168.100.129 gebruikt en Microsoft 192.168.100.130 gebruikt.
- 192.168.100.132/30 wordt toegewezen aan link2, waarbij provider 192.168.100.133 gebruikt en Microsoft 192.168.100.134 gebruikt.

### IP-adressen voor openbare Azure-peering en Microsoft-peering

U moet voor het instellen van de BGP-sessies openbare IP-adressen gebruiken waarvan u eigenaar bent. Microsoft moet het eigenaarschap van de IP-adressen kunnen verifiëren via Routing Internet Registries en Internet Routing Registries. 

- U moet een uniek /29-subnet of twee /30-subnetten gebruiken om de BGP-peering voor elke peering per ExpressRoute-circuit (als u er meer dan één hebt) in te stellen. 
- Als een /29-subnet wordt gebruikt, wordt dit verdeeld in twee /30-subnetten. 
    - Het eerste /30-subnet wordt gebruikt voor de primaire koppeling en het tweede/30-subnet voor de secundaire koppeling.
    - Voor beide /30-subnetten moet u het eerste IP-adres van het /30-subnet op de router gebruiken. Microsoft gebruikt het tweede IP-adres van het/30-subnet voor het instellen van een BGP-sessie.
    - Onze [beschikbaarheids-SLA](https://azure.microsoft.com/support/legal/sla/) is alleen geldig als beide BGP-sessies zijn ingesteld.

Controleer of uw IP-adres en AS-nummer in een van de hieronder vermelde registers op uw naam zijn geregistreerd.

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPENCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)


## Dynamische route-uitwisseling

Routeringsuitwisseling vindt plaats via het eBGP-protocol. EBGP-sessies worden tot stand gebracht tussen de MSEE's en uw routers. Verificatie van BGP-sessies is niet vereist. Indien nodig kan een MD5-hash worden geconfigureerd. Zie [Configure routing](expressroute-howto-routing-classic.md) (Routering configureren) en [Circuit provisioning workflows and circuit states](expressroute-workflows.md) (Werkstromen voor de inrichting van ExpressRoute-circuits en circuittoestanden) voor informatie over het configureren van BGP-sessies.

## Autonome systeemnummers

Microsoft gebruikt AS 12076 voor persoonlijke Azure-peering, openbare Azure-peering en Microsoft-peering. We hebben ASN's van 65515 tot 65520 gereserveerd voor intern gebruik. Zowel 16- als 32-bits AS-getallen worden ondersteund.

Er zijn geen vereisten met betrekking tot gegevensoverdrachtsymmetrie. De inkomende en uitgaande paden lopen mogelijk langs verschillende routerparen. Identieke routes moeten worden geadverteerd van beide zijden van meerdere circuitparen waarvan u eigenaar bent. Route metrics hoeven niet identiek te zijn.

## Limieten voor route-aggregatie en voorvoegsel

Wij ondersteunen maximaal 4000 voorvoegsels die aan ons zijn geadverteerd door middel van de persoonlijke Azure-peering. Dit aantal kan worden verhoogd tot 10.000 voorvoegsels als de Premium-invoegtoepassing voor ExpressRoute wordt ingeschakeld. We accepteren maximaal 200 voorvoegsels per BGP-sessie voor openbare Azure-peering en Microsoft-peering. 

De BGP-sessie wordt verwijderd als het aantal voorvoegsels de limiet overschrijdt. Standaardroutes worden alleen geaccepteerd op de persoonlijke peeringkoppeling. Provider moet standaardroute- en privé IP-adressen (RFC 1918) uit de paden voor openbare Azure- en Microsoft-peering filteren. 

## Transitroutering en regio-overschrijdende routering

ExpressRoute kan niet worden geconfigureerd als transitrouter. Voor transitrouteringsservices bent u aangewezen op uw connectiviteitsprovider.

## Standaardroutes adverteren

Standaardroutes zijn alleen toegestaan voor persoonlijke Azure-peeringsessies. In dat geval wordt al het verkeer van de gekoppelde virtuele netwerken omgeleid naar uw netwerk. Wanneer standaardroutes worden geadverteerd naar persoonlijke peering, wordt het internetpad vanuit Azure geblokkeerd. Als u verkeer van en naar internet wilt routeren voor services die worden gehost in Azure, zult u gebruik moeten maken van uw bedrijfsfunctionaliteit. 

 Als u connectiviteit met andere Azure-services en infrastructuurservices wilt inschakelen, moet u een van de volgende voorzieningen treffen:

 - Openbare Azure-peering is ingeschakeld om verkeer om te leiden naar openbare eindpunten
 - U gebruikt door de gebruiker gedefinieerde routering om internetconnectiviteit toe te staan voor elk subnet dat internetconnectiviteit vereist.

**Opmerking:** wanneer standaardroutes worden geadverteerd, wordt de activering van Windows- en andere VM-licenties verbroken. Volg [deze](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) instructies als u dit wilt omzeilen.

## Ondersteuning voor BGP-community's (Preview)


Deze sectie bevat een overzicht van hoe BGP-community's worden gebruikt met ExpressRoute. Microsoft adverteert routes in de paden voor openbare en Microsoft-peering waarbij de routes zijn gemarkeerd met de juiste communitywaarden. De reden hiervoor en meer informatie over communitywaarden worden hieronder beschreven. Microsoft erkent echter geen communitywaarden die zijn toegevoegd aan routes die worden geadverteerd aan Microsoft.

Als u op willekeurig welke locatie in een geopolitieke regio via ExpressRoute verbinding maakt met Microsoft, hebt u toegang tot alle Microsoft-cloudservices in alle regio's binnen de geopolitieke grenzen. 

Als u bijvoorbeeld via ExpressRoute bent verbonden met Microsoft in Amsterdam, hebt u toegang tot alle Microsoft Cloud-services die worden gehost in Noord-Europa en West-Europa. 

Raadpleeg de pagina [ExpressRoute partners and peering locations](expressroute-locations.md) (Overzicht van ExpressRoute-partners en -peeringlocaties) voor een gedetailleerde lijst van de geopolitieke regio's, bijbehorende Azure-regio's en bijbehorende ExpressRoute-peeringlocaties.

U kunt meer dan één ExpressRoute-circuit per geopolitieke regio aanschaffen. Het hebben van meer verbindingen biedt aanzienlijke voordelen wat betreft hoge beschikbaarheid vanwege geografische redundantie. Als u meerdere ExpressRoute-circuits hebt, ontvangt u dezelfde set voorvoegsels die worden geadverteerd vanuit Microsoft op de paden voor openbare peering en Microsoft-peering. Dat betekent dat er vanuit uw netwerk meerdere paden zijn naar Microsoft. Dit kan tot gevolg hebben dat er in uw netwerk suboptimale routeringsbeslissingen worden genomen. Dit kan leiden tot suboptimale connectiviteitservaringen met andere services. 

Daarom markeert Microsoft voorvoegsels die worden geadverteerd via openbare peering en Microsoft-peering met de juiste BGP-communitywaarden. Deze waarden geven de regio aan waarin de voorvoegsels worden gehost. Op basis van de communitywaarden worden de juiste routeringsbeslissingen genomen voor [optimale routering naar klanten](expressroute-optimize-routing.md).

| **Geopolitieke regio** | **Microsoft Azure-regio** | **BGP-communitywaarde** |
|---|---|---|
| **Noord-Amerika** |    |  |
|    | VS - oost | 12076:51004 |
|    | VS - oost 2 | 12076:51005 |
|    | VS - west | 12076:51006 |
|    | Noord-centraal VS | 12076:51007 |
|    | Zuid-centraal VS | 12076:51008 |
|    | VS - midden | 12076:51009 |
|    | Canada - midden | 12076:51020 |
|    | Canada - oost | 12076:51021 |
| **Zuid-Amerika** |  |  |
|    | Brazilië - zuid | 12076:51014 |
| **Europa** |    |  |
|    | Noord-Europa | 12076:51003 |
|    | West-Europa | 12076:51002 |
| **Azië en Stille Oceaan** |    |   |
|    | Oost-Azië | 12076:51010 |
|    | Zuidoost-Azië | 12076:51011 |
| **Japan** |     |   |
|    | Japan - oost | 12076:51012 |
|    | Japan - west | 12076:51013 |
| **Australië** |    |   | 
|    | Australië - oost | 12076:51015 |
|    | Australië - zuidoost | 12076:51016 |
| **India** |    |   |
|    | India - zuid | 12076:51019 |
|    | India - west | 12076:51018 |
|    | India - midden | 12076:51017 |

Alle routes die worden geadverteerd vanuit Microsoft, worden gemarkeerd met de juiste community-waarde. 

>[AZURE.IMPORTANT] Globale voorvoegsels worden gemarkeerd met een juiste communitywaarde en worden alleen geadverteerd wanneer de Premium-invoegtoepassing voor ExpressRoute is ingeschakeld.


Daarnaast worden voorvoegsels door Microsoft gemarkeerd op basis van de service waartoe ze behoren. Dit geldt alleen voor de Microsoft-peering. De volgende tabel bevat een toewijzing van services aan BGP-communitywaarden.

| **Service** | **BGP-communitywaarde** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype voor Bedrijven** | 12076:5030 |
| **CRM Online** | 12076:5040 |
| **Andere Office 365-services** | 12076:5100 |

>[AZURE.NOTE] BGP-communitywaarden die u instelt op de routes die worden geadverteerd naar Microsoft, worden niet door Microsoft erkend.

## Volgende stappen

- Configureer uw ExpressRoute-verbinding.

    - [Create an ExpressRoute circuit for the classic deployment model](expressroute-howto-circuit-classic.md) (Een ExpressRoute-circuit maken voor het klassieke implementatiemodel) of [Create and modify an ExpressRoute circuit using Azure Resource Manager](expressroute-howto-circuit-arm.md) (Een ExpressRoute-circuit maken en wijzigen met Azure Resource Manager)
    - [Configure routing for the classic deployment model](expressroute-howto-routing-classic.md) (Routering configureren voor het klassieke implementatiemodel) of [Configure routing for the Resource Manager deployment model](expressroute-howto-routing-arm.md) (Routering configureren voor het Resource Manager-implementatiemodel)
    - [Link a classic VNet to an ExpressRoute circuit](expressroute-howto-linkvnet-classic.md) (Een klassiek VNet koppelen aan een ExpressRoute-circuit) of [Link a Resource Manager VNet to an ExpressRoute circuit](expressroute-howto-linkvnet-arm.md) (Een Resource Manager-VNet koppelen aan een ExpressRoute-circuit)





<!--HONumber=Jun16_HO2-->


