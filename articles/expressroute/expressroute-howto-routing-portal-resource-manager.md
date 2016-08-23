<properties
   pageTitle="Routering configureren voor een ExpressRoute-circuit met de Azure Portal | Microsoft Azure"
   description="Dit artikel begeleidt u stapsgewijs door de procedure voor het maken en inrichten van de persoonlijke, openbare en Microsoft-peering van een ExpressRoute-circuit. In dit artikel leest u hoe u de status controleert en peerings voor uw circuit bijwerkt of verwijdert."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/08/2016"
   ms.author="cherylmc"/>

# Routering voor een ExpressRoute-circuit maken en wijzigen



> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-routing-arm.md)
[PowerShell - Klassiek](expressroute-howto-routing-classic.md)



In dit artikel worden de stappen besproken voor het maken en beheren van routeringsconfiguratie voor een ExpressRoute-circuit met de Azure Portal en het Resource Manager-implementatiemodel.

**Over Azure-implementatiemodellen**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## Configuratievereisten

- Zorg dat u de pagina met [vereisten](expressroute-prerequisites.md), de pagina over [routeringsvereisten](expressroute-routing.md) en de pagina over [werkstromen](expressroute-workflows.md) hebt gelezen voordat u begint met de configuratie.
- U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Het ExpressRoute-circuit moet zijn ingericht en zijn ingeschakeld om de hieronder beschreven cmdlets te kunnen uitvoeren.

Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services met Laag-2-connectiviteit aanbieden. Als u gebruikmaakt van een serviceprovider die beheerde Laag-3-services aanbiedt (meestal een IPVPN, zoals MPLS), zal de connectiviteitsprovider routering voor u configureren en beheren. 


>[AZURE.IMPORTANT] Op dit moment bieden we nog geen peerings aan die door serviceproviders worden geconfigureerd via de beheerportal van de service. Deze mogelijkheid zal binnenkort worden ingeschakeld. Neem contact op met uw serviceprovider voordat u BGP-peerings configureert.

U kunt een, twee of alle drie de peerings (Azure privé, Azure openbaar en Microsoft) voor een ExpressRoute-circuit configureren. U kunt peerings configureren in elke gewenste volgorde. U moet er echter wel voor zorgen dat u de configuratie van elke peering een voor een voltooit. 

## Persoonlijke Azure-peering

In deze sectie vindt u instructies voor het maken, verkrijgen, bijwerken en verwijderen van de configuratie voor een persoonlijke Azure-peering voor een ExpressRoute-circuit. 

### Persoonlijke Azure-peering maken

1. Configureer het ExpressRoute-circuit. Zorg dat het circuit volledig is ingericht door de connectiviteitsprovider voordat u verder gaat.

    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)


2. Configureer persoonlijke Azure-peering voor het circuit. Zorg ervoor dat u de volgende items hebt voordat u verdergaat met de volgende stappen:

    - Een /30-subnet voor de primaire koppeling. Dit mag geen deel uitmaken van een adresruimte die is gereserveerd voor virtuele netwerken.
    - Een /30-subnet voor de secundaire koppeling. Dit mag geen deel uitmaken van een adresruimte die is gereserveerd voor virtuele netwerken.
    - Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
    - AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken. U kunt een persoonlijk AS-nummer voor deze peering gebruiken. Zorg dat u niet 65515 gebruikt.
    - Een MD5-hash, als u er een wilt gebruiken. **Dit is optioneel**.


3. Selecteer de rij voor persoonlijke Azure-peering, zoals hieronder is weergegeven.
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
    

4. Configureer persoonlijke Azure-peering. In onderstaande afbeelding ziet u een configuratievoorbeeld.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

    
5. Sla de configuratie op wanneer u alle parameters hebt opgegeven. Wanneer de configuratie is geaccepteerd, wordt iets soortgelijks als in het voorbeeld hieronder weergegeven.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)
    

### De details van persoonlijke Azure-peering weergeven

U kunt de eigenschappen van persoonlijke Azure-peering weergeven door de peering te selecteren.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)


### De configuratie van persoonlijke Azure-peering bijwerken

U kunt de rij voor peering selecteren en de eigenschappen van de peering wijzigen. 

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### Persoonlijke Azure-peering verwijderen

U kunt de peeringconfiguratie verwijderen door het verwijderingspictogram te selecteren, zoals hieronder is weergegeven.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)


## Openbare Azure-peering

In deze sectie vindt u instructies voor het maken, verkrijgen, bijwerken en verwijderen van de configuratie voor een openbare Azure-peering voor een ExpressRoute-circuit. 

### Openbare Azure-peering maken

1. Configureer het ExpressRoute-circuit. Zorg dat het circuit volledig is ingericht door de connectiviteitsprovider voordat u verder gaat.

    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)


2. Configureer openbare Azure-peering voor het circuit. Zorg ervoor dat u de volgende items hebt voordat u verdergaat met de volgende stappen:

    - Een /30-subnet voor de primaire koppeling. 
    - Een /30-subnet voor de secundaire koppeling. 
    - Alle IP-adressen die voor het instellen van deze peering worden gebruikt, moeten geldige openbare IPv4-adressen zijn.
    - Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
    - AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
    - Een MD5-hash, als u er een wilt gebruiken. **Dit is optioneel**.

3. Selecteer de rij voor openbare Azure-peering, zoals hieronder is weergegeven.
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
    

4. Configureer openbare Azure-peering. In onderstaande afbeelding ziet u een configuratievoorbeeld.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

    
5. Sla de configuratie op wanneer u alle parameters hebt opgegeven. Wanneer de configuratie is geaccepteerd, wordt iets soortgelijks als in het voorbeeld hieronder weergegeven.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)
    

### De details van openbare Azure-peering weergeven

U kunt de eigenschappen van openbare Azure-peering weergeven door de peering te selecteren.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)


### De configuratie van openbare Azure-peering bijwerken

U kunt de rij voor peering selecteren en de eigenschappen van de peering wijzigen. 

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### Openbare Azure-peering verwijderen

U kunt de peeringconfiguratie verwijderen door het verwijderingspictogram te selecteren, zoals hieronder is weergegeven.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)


## Microsoft-peering

In deze sectie vindt u instructies voor het maken, verkrijgen, bijwerken en verwijderen van de configuratie voor een Microsoft-peering voor een ExpressRoute-circuit. 

### Microsoft-peering maken

1. Configureer het ExpressRoute-circuit. Zorg dat het circuit volledig is ingericht door de connectiviteitsprovider voordat u verder gaat.

    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)


2. Configureer Microsoft-peering voor het circuit. Zorg ervoor dat u over de volgende informatie beschikt voordat u verder gaat.

    - Een /30-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR.
    - Een /30-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR.
    - Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
    - AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
    - **Geadverteerde voorvoegsels**: u moet een lijst verstrekken van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. U kunt een met komma's gescheiden lijst verzenden als u een reeks voorvoegsels wilt verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR.
    - **Klant-ASN**: als u voorvoegsels adverteert die niet zijn geregistreerd op het AS-nummer van de peering, kunt u het AS-nummer opgeven waarop ze zijn geregistreerd. **Dit is optioneel**.
    - **Naam van routeringsregister**: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd. **Dit is optioneel.**
    - Een MD5-hash, als u er een wilt gebruiken. **Dit is optioneel.**
    
3. U kunt de peering selecteren die u wilt configureren, zoals hieronder is weergegeven. Selecteer de rij voor Microsoft-peering.
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
    

4.  Configureer Microsoft-peering. In onderstaande afbeelding ziet u een configuratievoorbeeld.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)

    
5. Sla de configuratie op wanneer u alle parameters hebt opgegeven. 

    Als het circuit moet worden gevalideerd (zoals hieronder is weergegeven), moet u een ondersteuningsticket openen om aan ons ondersteuningsteam aan te tonen dat u eigenaar bent van de voorvoegsels.  
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)


    U kunt een ondersteuningsticket rechtstreeks vanuit de portal openen, zoals hieronder wordt weergegeven   
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


6. Wanneer de configuratie is geaccepteerd, wordt iets soortgelijks als in het voorbeeld hieronder weergegeven.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)
    

### De details van Microsoft-peering weergeven

U kunt de eigenschappen van openbare Azure-peering weergeven door de peering te selecteren.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)


### Configuratie van Microsoft-peering bijwerken

U kunt de rij voor peering selecteren en de eigenschappen van de peering wijzigen. 


![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)


### Microsoft-peering verwijderen

U kunt de peeringconfiguratie verwijderen door het verwijderingspictogram te selecteren, zoals hieronder is weergegeven.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)



## Volgende stappen

Volgende stap, [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md).

-  Voor meer informatie over ExpressRoute-werkstromen raadpleegt u [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-werkstromen).

-  Voor meer informatie over circuitpeering raadpleegt u [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuits en -routeringsdomeinen).

-  Bekijk het [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtual Network-overzicht) voor meer informatie over het gebruik van virtuele netwerken.




<!--HONumber=Jun16_HO2-->


