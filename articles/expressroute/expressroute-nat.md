<properties
   pageTitle="NAT-vereisten voor ExpressRoute-circuits | Microsoft Azure"
   description="Deze pagina bevat gedetailleerde vereisten voor het configureren en beheren van de NAT voor ExpressRoute-circuits."
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
   ms.date="04/18/2016"
   ms.author="cherylmc"/>

# NAT-vereisten voor ExpressRoute

Als u ExpressRoute wilt gebruiken om verbinding te maken met Microsoft Cloud-services, moet u NAT's instellen en beheren. Sommige connecitiviteitsproviders bieden het instellen en beheren van NAT aan als een beheerde service. Neem contact op met uw connectiviteitsprovider om na te gaan of ze deze service leveren. Als dat niet het geval is, moet u voldoen aan de vereisten die hieronder worden beschreven. 

Bekijk de pagina [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuits en routeringsdomeinen) voor een overzicht van de verschillende routeringsdomeinen. Om te voldoen aan de vereisten voor openbare IP-adressen voor openbare Azure-peering en Microsoft-peering, wordt u aangeraden om NAT in te stellen tussen uw netwerk en Microsoft. In deze sectie vindt u een gedetailleerde beschrijving van de NAT-infrastructuur die u moet instellen.

## NAT-vereisten voor openbare Azure-peering

Met het pad voor openbare Azure-peering kunt u verbinding maken met alle services die via de openbare IP-adressen worden gehost in Azure. Deze lijst bevat alle services die worden vermeld in de [Veelgestelde vragen over ExpessRoute](expressroute-faqs.md) en alle services die door ISV's worden gehost op Microsoft Azure. Connectiviteit met Microsoft Azure-services via openbare peering wordt altijd gestart vanuit uw netwerk naar het Microsoft-netwerk. Verkeer dat is bestemd voor Microsoft Azure via openbare peering moet met SNAT worden omgezet naar geldige openbare IPv4-adressen voordat het het Microsoft-netwerk binnenkomt. In onderstaande afbeelding ziet u een algemeen beeld van hoe de NAT kan worden ingesteld om te voldoen aan bovenstaande vereiste.

![](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### NAT IP-adresgroep en route-advertenties

U moet ervoor zorgen dat verkeer het pad voor openbare Azure-peering binnenkomt met een geldig openbaar IPv4-adres. Microsoft moet het eigenaarschap van de IPv4 NAT-adresgroep kunnen controleren in het regionale Routing Internet Registry (RIR) of een Internet Routing Registry (IRR). Er wordt een controle uitgevoerd op basis van het AS-nummer waaraan het wordt gekoppeld en de IP-adressen die voor de NAT worden gebruikt. Raadpleeg de pagina [ExpressRoute routing requirements](expressroute-routing.md) (Routeringsvereisten voor ExpressRoute) voor meer informatie over routeringsregisters.
 
Er zijn geen lengtebeperkingen voor het NAT IP-voorvoegsel dat via deze peering wordt geadverteerd. U moet de NAT-adresgroep controleren en ervoor zorgen dat u geen NAT-sessies tekort komt.

>[AZURE.IMPORTANT] De NAT IP-adresgroep die wordt geadverteerd aan Microsoft mag niet worden geadverteerd op internet. Dit verbreekt de connectiviteit met andere Microsoft-services.

## NAT-vereisten voor Microsoft-peering

Met het pad voor Microsoft-peering kunt u verbinding maken met Microsoft Cloud-services die niet worden ondersteund via het pad voor openbare Azure-peering. De lijst met services bevat Office 365-services, zoals Exchange Online, SharePoint Online, Skype voor Bedrijven en CRM Online. Microsoft verwacht bidirectionele connectiviteit op de Microsoft-peering te gaan ondersteunen. Verkeer dat is bestemd voor Microsoft Cloud-services moet met SNAT worden omgezet naar geldige openbare IPv4-adressen voordat het het Microsoft-netwerk binnenkomt. Verkeer dat is bestemd voor uw netwerk en afkomstig is van Microsoft Cloud-services moet met SNAT worden omgezet voordat het uw netwerk binnenkomt. In onderstaande afbeelding ziet u een algemeen beeld van hoe de NAT moet worden ingesteld voor Microsoft-peering.
 
![](./media/expressroute-nat/expressroute-nat-microsoft.png) 


#### Verkeer dat afkomstig is van uw netwerk en is bestemd voor Microsoft

- U moet ervoor zorgen dat verkeer het pad voor Microsoft-peering binnenkomt met een geldig openbaar IPv4-adres. Microsoft moet de eigenaar van de IPv4 NAT-adresgroep kunnen controleren in het regionale Routing Internet Registry (RIR) of een Internet Routing Registry (IRR). Er wordt een controle uitgevoerd op basis van het AS-nummer waaraan het wordt gekoppeld en de IP-adressen die voor de NAT worden gebruikt. Raadpleeg de pagina [ExpressRoute routing requirements](expressroute-routing.md) (Routeringsvereisten voor ExpressRoute) voor meer informatie over routeringsregisters.

- IP-adressen die worden gebruikt voor de configuratie van openbare Azure-peering en andere ExpressRoute-circuits mogen niet aan Microsoft worden geadverteerd via de BGP-sessie. Er is geen lengtebeperking voor het NAT IP-voorvoegsel dat via deze peering wordt geadverteerd.

    >[AZURE.IMPORTANT] De NAT IP-adresgroep die wordt geadverteerd aan Microsoft mag niet worden geadverteerd op internet. Dit verbreekt de connectiviteit met andere Microsoft-services.

#### Verkeer dat afkomstig is van Microsoft en is bestemd voor Microsoft

- In bepaalde scenario's moet Microsoft connectiviteit starten met service-eindpunten die worden gehost in uw netwerk. Een typisch voorbeeld van het scenario is connectiviteit met ADFS-servers die vanuit Office 365 wordt gehost in uw netwerk. In dergelijke gevallen moet u vanuit uw netwerk geschikte voorvoegsels naar de Microsoft-peering laten lekken. 

- U moet verkeer dat is bestemd voor IP-adressen in uw netwerk vanuit Microsoft met SNAT omzetten. 

## Volgende stappen

- Raadpleeg de vereisten voor [Routering](expressroute-routing.md) en [QoS](expressroute-qos.md).
- Voor informatie over werkstromen raadpleegt u [ExpressRoute circuit provisioning workflows and circuit states](expressroute-workflows.md) (Werkstromen voor de inrichting van ExpressRoute-circuits en circuittoestanden).
- Configureer uw ExpressRoute-verbinding.

    - [Een ExpressRoute-circuit maken](expressroute-howto-circuit-classic.md)
    - [Routering configureren](expressroute-howto-routing-classic.md)
    - [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-classic.md)




<!--HONumber=Jun16_HO2-->


