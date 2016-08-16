<properties
   pageTitle="Vereisten voor ExpressRoute-acceptatie | Microsoft Azure"
   description="Deze pagina bevat een lijst met vereisten waaraan moet worden voldaan voordat u een Azure ExpressRoute-circuit kunt bestellen."
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
   ms.date="04/19/2016"
   ms.author="cherylmc"/>


# Vereisten en controlelijst voor ExpressRoute  

Als u ExpressRoute wilt gebruiken om verbinding te maken met Microsoft Cloud-services moet u controleren of aan de vereisten die in de onderstaande secties worden genoemd, is voldaan.

## Azure-account

- Een geldig en actief Microsoft Azure-account. Dit is vereist om het ExpressRoute-circuit in te stellen. ExpressRoute-circuits zijn resources in Azure-abonnementen. Een Azure-abonnement is vereist, zelfs als connectiviteit is beperkt tot niet-Azure Microsoft Cloud-services, zoals Office 365-services en CRM online.
- Een actief Office 365-abonnement (als u Office 365-services gebruikt). Zie de sectie [Specifieke vereisten voor Office 365](#office-365-specific-requirements) in dit artikel voor meer informatie.

## Connectiveitsprovider
- U kunt met een [ExpressRoute-connectiviteitspartner](expressroute-locations.md#partners) werken om verbinding te maken met de Microsoft Cloud. U kunt op [drie manieren](expressroute-introduction.md#howtoconnect) een verbinding instellen tussen uw on-premises netwerk en Microsoft. 
- Als uw provider geen ExpressRoute-connectiviteitspartner is, kunt u via een [cloudexchange-provider](expressroute-locations.md#nonpartners) verbinding maken met de Microsoft Cloud.

## Netwerkvereisten
- **Redundante connectiviteit**: er is geen redundantievereiste voor fysieke connectiviteit tussen u en uw provider. Microsoft vereist dat redundante BGP-sessies worden ingesteld tussen Microsoft-routers en de peeringrouters, zelfs als u maar [één fysieke verbinding met een cloudexchange](expressroute-faqs.md#onep2plink) hebt. 
- **Routering**: afhankelijk van hoe u verbinding maakt met de Microsoft Cloud, moet u of uw provider de BGP-sessies voor [routeringsdomeinen](expressroute-circuit-peerings.md) instellen en beheren. Sommige Ethernet-connectiviteitsproviders of cloudexchange-providers bieden BGP-beheer aan als extra service.
- **NAT**: Microsoft accepteert alleen openbare IP-adressen via Microsoft-peering. Als u privé IP-adressen in uw on-premises netwerk gebruikt, moet u of uw provider de privé IP-adressen [met behulp van de NAT](expressroute-nat.md) vertalen naar openbare IP-adressen.
- **QoS**: Skype voor Bedrijven heeft verschillende services (zoals spraak, video, tekst) die allemaal een andere QoS-behandeling vereisen. U en uw provider moeten de [QoS-vereisten](expressroute-qos.md) volgen.
- **Netwerkbeveiliging**: u moet [netwerkbeveiliging](../best-practices-network-security.md) overwegen wanneer u via ExpressRoute verbinding maakt met de Microsoft Cloud.
 
## Office 365

Als u Office 365 op ExpressRoute wilt gaan inschakelen, raadpleeg dan de volgende documenten voor meer informatie over de vereisten voor Office 365.


- [Overview of ExpressRoute for Office 365 (Overzicht van ExpressRoute voor Office 365)](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
- [Routing with ExpressRoute for Office 365 (Routering met ExpressRoute voor Office 365)](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
- [Office 365 URLs and IP address ranges (URL's en IP-adresbereiken voor Office 365)](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
- [Network planning and performance tuning for Office 365 (Netwerkplanning en prestatieafstemming voor Office 365)](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
- [Network bandwidth calculators and tools (Calculators en hulpmiddelen voor het berekenen van de netwerkbandbreedte)](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
- [Office 365 integration with on-premises environments (Office 365-integratie met on-premises omgevingen)](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## CRM Online 
Als u CRM Online op ExpressRoute wilt gaan inschakelen, raadpleeg dan de volgende documenten voor meer informatie over CRM Online.

- [CRM Online URLs](https://support.microsoft.com/kb/2655102) and [IP address ranges](https://support.microsoft.com/kb/2728473) (URL's en IP-adresbereiken voor CRM Online)

## Volgende stappen

- Voor meer informatie over ExpressRoute raadpleegt u de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md).
- Zoek een ExpressRoute-connectiviteitsprovider. Zie [ExpressRoute partners and peering locations](expressroute-locations.md) (ExpressRoute-partners en -peeringlocaties).
- Raadpleeg de vereisten voor [Routering](expressroute-routing.md), [NAT](expressroute-nat.md) en [QoS](expressroute-qos.md).
- Configureer uw ExpressRoute-verbinding.
    - [Een ExpressRoute-circuit maken](expressroute-howto-circuit-classic.md)
    - [Routering configureren](expressroute-howto-routing-classic.md)
    - [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-classic.md)




<!--HONumber=Jun16_HO2-->


