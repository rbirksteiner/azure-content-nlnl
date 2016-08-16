<properties 
   pageTitle="Over beveiligde cross-premises connectiviteit voor virtuele netwerken | Microsoft Azure"
   description="Meer informatie over de typen beveiligde cross-premises verbindingen voor virtuele netwerken, waaronder site-naar-site-, punt-naar-site- en ExpressRoute-verbindingen."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# Over beveiligde cross-premises connectiviteit voor virtuele netwerken

In dit artikel worden de verschillende manieren besproken waarop u uw on-premises site kunt aansluiten op een virtueel Azure-netwerk. Dit artikel is van toepassing op zowel het Resource Manager- als het klassieke implementatiemodel. Voor VPN-gatewayverbindingsdiagrammen raadpleegt u [Azure VPN Gateway connection topologies](vpn-gateway-topology.md) (Verbindingstopologieën voor VPN-gateways).

Er zijn drie verbindingsopties beschikbaar: site-naar-site, punt-naar-site en ExpressRoute. Welke optie u kiest, kan afhankelijk zijn van tal van overwegingen, zoals:


- Wat voor doorvoer heeft uw oplossing nodig?
- Wilt u communiceren via het openbare internet met een beveiligd VPN, of via een particuliere verbinding?
- Hebt u de beschikking over een openbaar IP-adres?
- Bent u van plan om een VPN-apparaat te gebruiken? Zo ja, is het compatibel?
- Verbindt u slechts enkele computers met elkaar, of u wilt een persistente verbinding voor uw site?
- Welk type VPN-gateway is vereist voor de oplossing die u wilt maken?

Onderstaande tabel kan u helpen bij het kiezen van de beste connectiviteitsoptie voor uw oplossing.

[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]                                                                    

## Site-naar-site-verbindingen

Met een site-naar-site-VPN kunt u een beveiligde verbinding maken tussen uw on-premises site en het virtuele netwerk. Voor een site-naar-site-verbinding wordt een VPN-apparaat in uw on-premises netwerk geconfigureerd voor het maken van een beveiligde verbinding met de Azure VPN-gateway. Wanneer de verbinding is gemaakt, kunnen resources in het lokale netwerk en resources in het virtuele netwerk rechtstreeks en veilig communiceren. Voor site-naar-site-verbindingen hoeft u geen afzonderlijke verbinding voor elke clientcomputer in het lokale netwerk tot stand te brengen voor toegang tot resources in het virtuele netwerk.

**In de volgende gevallen gebruikt u een site-naar-site-verbinding:**

- U wilt een hybride oplossing maken.
- U wilt een verbinding maken tussen uw on-premises locatie en het virtuele netwerk, zonder configuraties aan de zijde van de client.
- U wilt een persistente verbinding. 

**Vereisten**

- Het on-premises VPN-apparaat moet een internetgericht IPv4-adres hebben. Dit mag zich niet achter een NAT bevinden.
- U hebt een compatibel VPN-apparaat nodig. Zie [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Over VPN-apparaten). 
- Het VPN-apparaat dat u gebruikt moet compatibel zijn met het gatewaytype dat voor uw oplossing is vereist. Zie [About VPN Gateways](vpn-gateway-about-vpngateways.md) (Over VPN-gateways).
- De gateway-SKU heeft ook invloed op geaggregeerde doorvoer. Zie [Gateway-SKU's](vpn-gateway-about-vpngateways.md#gwsku) voor meer informatie. 

**Beschikbare implementatiemodellen en -methoden voor S2S**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 


## Punt-naar-site-verbindingen

Met een punt-naar-site-VPN kunt u een beveiligde verbinding maken met het virtuele netwerk. In een point-naar-site configuratie is de verbinding afzonderlijk geconfigureerd op elke clientcomputer die u met het virtuele netwerk wilt verbinden. Punt-naar-site-verbindingen hebben geen VPN-apparaat nodig. Dit type verbinding gebruikt een VPN-client die u op elke clientcomputer installeert. De VPN-verbinding wordt handmatig tot stand gebracht door de verbinding te starten vanaf de on-premises clientcomputer.

Punt-naar-site- en site-naar-site-verbindingen kunnen gelijktijdig bestaan, maar in tegenstelling tot site-naar-site-verbindingen kunnen punt-naar-site-verbindingen niet worden geconfigureerd als een ExpressRoute-verbinding met hetzelfde virtuele netwerk.

**In de volgende gevallen gebruikt u een punt-naar-site-verbinding:**

- U wilt alleen enkele clients configureren om verbinding te maken met een virtueel netwerk.

- U wilt vanaf een externe locatie verbinding maken met het virtuele netwerk. U wilt bijvoorbeeld verbinding maken vanuit een restaurant of een vergaderruimte.

- U hebt een site-naar-site-verbinding, maar u hebt ook clients die verbinding moeten maken vanaf een externe locatie.

- U hebt geen toegang tot een VPN-apparaat dat voldoet aan de minimumvereisten voor een site-naar-site-verbinding.

- U hebt geen internetgericht IPv4-IP-adres voor uw VPN-apparaat.

**Beschikbare implementatiemodellen en -methoden voor P2S**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## ExpressRoute-verbindingen

Met Azure ExpressRoute kunt u particuliere verbindingen maken tussen Azure-datacenters en infrastructuur on-premises of in een co-locatie-omgeving. ExpressRoute-verbindingen gaan niet via het openbare internet en zijn daardoor betrouwbaarder en sneller en ze hebben lagere latenties en betere beveiliging dan gewone verbindingen via internet.

In sommige gevallen levert het gebruik van ExpressRoute-verbindingen voor het overbrengen van gegevens tussen on-premises en Azure ook aanzienlijke kostenvoordelen op. Met ExpressRoute kunt u verbindingen instellen met Azure op een ExpressRoute-locatie (faciliteit van Exchange-provider) of u kunt rechtstreeks verbinding maken met Azure vanaf uw eigen WAN-netwerk (zoals een MPLS VPN), geleverd door een netwerkserviceprovider.

Voor meer informatie over ExpressRoute raadpleegt u het [Technical Overview](../expressroute/expressroute-introduction.md) (Technisch overzicht).


## Volgende stappen

- Voor meer informatie over VPN-gateway raadpleegt u de artikelen [About VPN Gateway](vpn-gateway-about-vpngateways.md) (Over VPN-gateway), [FAQ](vpn-gateway-vpn-faq.md) (Veelgestelde vragen over de VPN-gateway) en [Planning and Design](vpn-gateway-plan-design.md) (Planning en ontwerp).

- Voor meer informatie over ExpressRoute raadpleegt u het [Technical Overview](../expressroute/expressroute-introduction.md) (Technisch overzicht), de [FAQ](../expressroute/expressroute-faqs.md) (Veelgestelde vragen) en [Workflows](../expressroute/expressroute-workflows.md) (Werkstromen).







<!--HONumber=Jun16_HO2-->


