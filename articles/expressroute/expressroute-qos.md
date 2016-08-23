<properties
   pageTitle="QoS-vereisten voor ExpressRoute | Microsoft Azure"
   description="Deze pagina bevat gedetailleerde vereisten voor het configureren en beheren van de QoS voor ExpressRoute-circuits."
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

# QoS-vereisten voor ExpressRoute

Skype voor Bedrijven heeft verschillende workloads die allemaal een andere QoS-behandeling vereisen. Als u via ExpressRoute voice-services wilt gaan gebruiken, moet u voldoen aan de vereisten die hieronder worden beschreven.

![](./media/expressroute-qos/expressroute-qos.png)

>[AZURE.NOTE] QoS-vereisten zijn alleen van toepassing op de Microsoft-peering.

De volgende tabel bevat een lijst van DSCP-markeringen die worden gebruikt door Skype voor Bedrijven. Raadpleeg [Managing QoS for Skype for Business](https://technet.microsoft.com/library/gg405409.aspx) (QoS voor Skype voor Bedrijven beheren) voor meer informatie.

| **Verkeersklasse** | **Behandeling (DSCP-markering)** | **Workloads voor Skype voor Bedrijven** |
|---|---|---|
| **Spraak** | EF (46) | Skype / Lync voice |
| **Interactief** | AF41 (34) | Video |
|   | AF21 (18) | Apps delen | 
|   | CS3 (24) | SIP-signalering |
| **Standaard** | AF11 (10) | Bestandsoverdracht|
|   | CS0 (0) | Overige| 


- U moet de workloads classificeren en de juiste DSCP-waarden markeren. Volg [deze](https://technet.microsoft.com/library/gg405409.aspx) richtlijnen over het instellen van DSCP-markeringen in uw netwerk.

- U moet meerdere QoS-wachtrijen in uw netwerk configureren en ondersteunen. Voice moet een zelfstandige klasse zijn en de EF-behandeling ontvangen die is opgegeven in RFC 3246. 

- U kunt het wachtrijmechanisme, het congestiedetectiebeleid en de bandbreedtetoewijzing per verkeersklasse bepalen. De DSCP-markering voor workloads voor Skype voor Bedrijven moet echter behouden blijven. Als u DSCP-markeringen gebruikt die niet worden weergegeven, bijvoorbeeld AF31 (26), moet u deze DSCP-waarde wijzigen in 0 voordat u het pakket naar Microsoft verzendt. Microsoft verzendt alleen pakketten die zijn gemarkeerd met de DSCP-waarde die wordt weergegeven in de bovenstaande tabel. 

## Volgende stappen

- Raadpleeg de vereisten voor [Routering](expressroute-routing.md) en [NAT](expressroute-nat.md).
- Klik op de volgende koppelingen als u uw ExpressRoute-verbinding wilt configureren.

    - [Een ExpressRoute-circuit maken](expressroute-howto-circuit-classic.md)
    - [Routering configureren](expressroute-howto-routing-classic.md)
    - [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-classic.md)



<!--HONumber=Jun16_HO2-->


