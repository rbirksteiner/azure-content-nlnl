<properties 
   pageTitle="Veelgestelde vragen over VPN-gateways van virtuele netwerken | Microsoft Azure"
   description="Veelgestelde vragen over VPN-gateways. Veelgestelde vragen over cross-premises verbinding, verbindingen voor hybride configuraties en VPN-gateways voor Microsoft Azure Virtual Network"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/10/2016"
   ms.author="yushwang" />

# Veelgestelde vragen VPN-gateways

## Verbinding maken met Virtual Network

### Kan ik virtuele netwerken in verschillende Azure-regio's verbinden?
Ja. Er is zelfs helemaal geen regiobeperking. Een virtueel netwerk kan verbinding maken met een ander virtueel netwerk in dezelfde regio of in een andere Azure-regio.

### Kan ik virtuele netwerken uit verschillende abonnementen verbinden?
Ja.

### Kan ik vanuit één virtueel netwerk verbinding maken met meerdere sites?

U kunt verbinding maken met meerdere sites met behulp van Windows PowerShell en de REST-API's van Azure. Raadpleeg de sectie Veelgestelde vragen bij [Multi-site- en VNet-naar-VNet-connectiviteit](#multi-site-and-vnet-to-vnet-connectivity).
## Wat zijn de opties voor cross-premises-verbinding?

De volgende cross-premises verbindingen worden ondersteund:

- [Site-naar-site](vpn-gateway-site-to-site-create.md): VPN-verbinding via IPsec (IKE v1 en IKE v2). Voor dit type verbinding is een VPN-apparaat of RRAS vereist.

- [Punt-naar-site](vpn-gateway-point-to-site-create.md): VPN-verbinding via SSTP (Secure Socket Tunneling Protocol). Voor deze verbinding is geen VPN-apparaat vereist.

- [VNet-naar-VNet](virtual-networks-configure-vnet-to-vnet-connection.md): dit type verbinding is hetzelfde als de site-naar-site-configuratie. VNet-naar-VNet is een VPN-verbinding via IPsec (IKE v1 en IKE v2). Hiervoor is geen VPN-apparaat vereist.

- [Multi-site](vpn-gateway-multi-site.md): dit is een variant op een site-naar-site-configuratie waarmee u meerdere on-premises sites kunt verbinden met een virtueel netwerk.

- [ExpressRoute](../expressroute/expressroute-introduction.md): ExpressRoute is een directe verbinding van uw WAN met Azure. Deze loopt niet via het openbare internet. Voor meer informatie raadpleegt u het [ExpressRoute Technical Overview](../expressroute/expressroute-introduction.md) (Technisch overzicht van ExpressRoute) en de [ExpressRoute FAQ](../expressroute/expressroute-faqs.md) (Veelgestelde vragen over ExpressRoute).

Voor meer informatie over cross-premises-verbindingen raadpleegt u [About secure cross-premises connectivity](vpn-gateway-cross-premises-options.md) (Over beveiligde cross-premises-connectiviteit).

### Wat is het verschil tussen een site-naar-site-verbinding en een punt-naar-site?

Met **site-naar-site**-verbindingen kunt u uw computers on-premises verbinden met elke virtuele machine of elke rolinstantie in uw virtuele netwerk, afhankelijk van hoe u routering wilt configureren. Het is een geweldige optie voor een cross-premises-verbinding die altijd beschikbaar is. Dit type verbinding is afhankelijk van een IPsec-VPN-apparaat (hardware of software) dat op de rand van uw netwerk moet worden geïmplementeerd. Voor dit type verbinding hebt u de vereiste VPN-hardware en een extern gericht IPv4-adres nodig.

Met **punt-naar-site**-verbindingen kunt u vanaf één computer waar dan ook verbinding maken met alles binnen het virtuele netwerk. Hiervoor wordt de met Windows meegeleverde VPN-client gebruikt. Als onderdeel van de punt-naar-site-configuratie installeert u een certificaat en een VPN-clientconfiguratiepakket. Dit pakket bevat de instellingen waarmee de computer verbinding kan maken met elke virtuele machine of rolinstantie in het virtuele netwerk. Het is ideaal wanneer u verbinding wilt maken met een virtueel netwerk maar u zich niet on-premises bevindt. Het is ook een goede optie wanneer u geen toegang hebt tot VPN-hardware of een extern gericht IPv4-adres, twee zaken die vereist zijn voor een site-naar-site-verbinding. 

U kunt uw virtuele netwerk configureren om tegelijkertijd gebruik te maken van site-naar-site en punt-naar-site, mits u de site-naar-site-verbinding maakt met een op route gebaseerd VPN-type voor uw gateway. Op route gebaseerde VPN-typen worden in het klassieke implementatiemodel dynamische gateways genoemd.

Voor meer informatie raadpleegt u [About secure cross-premises connectivity for virtual networks](vpn-gateway-cross-premises-options.md) (Over beveiligde cross-premises-connectiviteit voor virtuele netwerken).

### Wat is ExpressRoute?

Met ExpressRoute kunt u particuliere verbindingen maken tussen Microsoft-datacenters en infrastructuur on-premises of in een co-locatie-omgeving. Met ExpressRoute kunt u verbindingen instellen met Microsoft-cloudservices, zoals Microsoft Azure en Office 365 op een co-locatiefaciliteit van een ExpressRoute-partner, of u kunt vanaf uw eigen WAN-netwerk (zoals een MPLS VPN), geleverd door een netwerkserviceprovider, rechtstreeks verbinding maken met Azure.

ExpressRoute-verbindingen zijn beter beveiligd, betrouwbaarder en bieden hogere bandbreedte en lagere latenties dan gewone verbindingen via internet. In sommige gevallen levert het gebruik van ExpressRoute-verbindingen voor het overbrengen van gegevens tussen uw on-premises netwerk en Azure ook aanzienlijke kostenvoordelen op. Als u vanaf uw on-premises netwerk al een cross-premises-verbinding hebt gemaakt met Azure, kunt u migreren naar een ExpressRoute-verbinding terwijl het virtuele netwerk intact blijft.

Zie de [Veelgestelde vragen over ExpressRoute](../expressroute/expressroute-faqs.md) voor meer informatie.

## Site-naar-site-verbindingen en VPN-apparaten

### Waaraan moet ik denken bij het selecteren van een VPN-apparaat?

We hebben samen met apparaatleveranciers een reeks standaard site-naar-site-VPN-apparaten gevalideerd. [Hier](vpn-gateway-about-vpn-devices.md) vindt u een lijst met bekende compatibele VPN-apparaten, hun bijbehorende configuratie-instructies of voorbeelden en apparaatspecificaties. Alle apparaten in de vermelde apparaatfamilies die als compatibel worden weergegeven, zouden met Virtual Network moeten werken. Als hulp bij de configuratie van uw VPN-apparaat kunt u het voorbeeld van de apparaatconfiguratie raadplegen of de koppeling bij de betreffende apparaatfamilie.

### Wat moet ik doen als ik een VPN-apparaat heb dat niet voorkomt in de lijst met bekende compatibele apparaten?

Als uw apparaat niet wordt vermeld als een bekend compatibel VPN-apparaat en u dit toch wilt gebruiken voor uw VPN-verbinding, moet u controleren of het voldoet aan de [hier](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list) vermelde ondersteunde opties en parameters voor IPsec/IKE-configuratie. Apparaten die voldoen aan de minimumvereisten zouden met VPN-gateways moeten werken. Neem contact op met de fabrikant van uw apparaat voor aanvullende ondersteuning en configuratie-instructies.

### Waarom wordt mijn op beleid gebaseerde VPN-tunnel inactief als er geen verkeer is?

Dit is normaal voor op beleid gebaseerde VPN-gateways (ook wel bekend als statische routering genoemd). Wanneer er langer dan vijf minuten geen verkeer is via de tunnel, wordt de tunnel verwijderd. Zodra er echter weer verkeer is in een van de richtingen, wordt de tunnel onmiddellijk opnieuw ingesteld . Dit gebeurt niet als u een op route gebaseerde VPN-gateway hebt (ook wel dynamisch genoemd).

### Kan ik software-VPN's gebruiken om verbinding te maken met Azure?

Windows Server 2012 RRAS-servers (Routering en RAS) worden ondersteund voor site-naar-site-cross-premises-configuratie.

Andere VPN-softwareoplossingen zouden in principe met onze gateway moeten werken zolang ze voldoen aan de standaard-IPSec-implementaties. Neem contact op met de leverancier van de software voor configuratie- en ondersteuningsinstructies.

## Punt-naar-site-verbindingen

### Welke besturingssystemen kan ik met punt-naar-site gebruiken?

De volgende besturingssystemen worden ondersteund:

- Windows 7 (alleen 64-bits-versie)

- Windows Server 2008 R2

- Windows 8 (alleen 64-bits-versie)

- Windows Server 2012

- Windows 10

### Kan ik voor punt-naar-site elke VPN-softwareclient gebruiken die SSTP ondersteunt?

Nee. Ondersteuning is beperkt tot de hierboven vermelde versies van Windows-besturingssystemen.

### Hoeveel VPN-clienteindpunten kan mijn punt-naar-site-configuratie hebben?

Er kunnen maximaal 128 VPN-clients tegelijk met een virtueel netwerk worden verbonden.

### Kan ik mijn eigen interne PKI basis-CA voor een punt-naar-site-verbinding gebruiken?

Ja. Voorheen konen alleen zelfondertekende basiscertificaten worden gebruikt. U kunt nog steeds 20 basiscertificaten uploaden.

### Kan ik met punt-naar-site-functionaliteit proxy's en firewalls passeren?

Ja. SSTP (Secure Socket Tunneling Protocol) wordt gebruikt om firewalls te passeren. Deze tunnel wordt weergegeven als een HTTPs-verbinding.

### Als ik een clientcomputer die is geconfigureerd voor punt-naar-site opnieuw start, wordt de VPN-verbinding dan automatisch opnieuw tot stand gebracht?

Standaard wordt de VPN-verbinding niet automatisch op de clientcomputer hersteld.

### Biedt punt-naar-site ondersteuning voor automatisch opnieuw verbinding maken en DDNS op de VPN-clients?

Automatisch opnieuw verbinding maken en DDNS worden momenteel niet ondersteund in VPN’s met punt-naar-site-verbinding.

### Kunnen site-naar-site- en punt-naar-site-configuraties naast elkaar worden gebruikt in hetzelfde virtuele netwerk?

Ja. Beide oplossingen werken als de gateway een op route gebaseerd VPN-type heeft. Voor het klassieke implementatiemodel hebt u een dynamische gateway nodig. Punt-naar-site wordt niet ondersteund voor VPN-gateways met statische routering of gateways met een op beleid gebaseerd VPN-type.

### Kan ik een punt-naar-site-client configureren om verbinding te maken met meerdere virtuele netwerken tegelijk?

Ja, dat is mogelijk. De virtuele netwerken kunnen echter geen overlappende IP-voorvoegsels hebben en de punt-naar-site-adresruimten tussen de virtuele netwerken mogen elkaar niet overlappen.

### Hoeveel doorvoer kan ik verwachten via site-naar-site- of punt-naar-site-verbindingen?

Het is moeilijk om de exacte doorvoer van de VPN-tunnels te onderhouden. IPSec en SSTP zijn cryptografisch zware VPN-protocollen. Doorvoer wordt ook beperkt door de latentie en bandbreedte tussen uw locatie en het internet.

## Gateways

### Wat is een op beleid gebaseerde gateway (statische routering)?

Op beleid gebaseerde gateways implementeren op beleid gebaseerde VPN's. Op beleid gebaseerde VPN-verbindingen versleutelen pakketten en versturen deze op basis van de combinaties van adresvoorvoegsels tussen uw on-premises netwerk en het Azure-VNET. Het beleid (of de verkeersselector) wordt gewoonlijk gedefinieerd als een toegangslijst in de VPN-configuratie.

### Wat is een op route gebaseerde gateway (dynamische routering)?

Op route gebaseerde gateways implementeren op route gebaseerde VPN's. Op route gebaseerde VPN's gebruiken 'routes' in de IP-doorstuurtabel of routeringstabel om pakketten naar de bijbehorende tunnelinterfaces te sturen. De tunnelinterfaces versleutelen of ontsleutelen de pakketten vervolgens naar en vanuit de tunnels. Het beleid of de verkeersselector voor op route gebaseerde VPN's is geconfigureerd als alles-naar-alles (of jokertekens).

### Kan ik het IP-adres van de VPN-gateway verkrijgen voordat ik de gateway maak?

Nee. U moet een gateway maken voordat u het IP-adres kunt verkrijgen. Het IP-adres verandert als u een VPN-gateway verwijdert en opnieuw maakt.

### Hoe wordt mijn VPN-tunnel geverifieerd?

Azure VPN maakt gebruik van PSK-verificatie (verificatie op basis van een vooraf gedeelde sleutel). Er wordt een PSK (vooraf gedeelde sleutel) gegenereerd wanneer de VPN-tunnel wordt gemaakt. Met de PowerShell-cmdlet of REST-API Set Pre-Shared Key kunt u de automatisch gegenereerde PSK wijzigen in een eigen sleutel.

### Kan ik de API Set Pre-Shared Key gebruiken om een op beleid gebaseerde VPN-gateway (statische routering) te configureren?

Ja, u kunt de API en PowerShell-cmdlet Set Pre-Shared Key gebruiken om zowel op beleid gebaseerde (statische) VPN's als op route gebaseerde VPN's (dynamische routering) van Azure te configureren.

### Kan ik andere verificatieopties gebruiken?

U kunt alleen PSK-verificatie (vooraf gedeelde sleutels) gebruiken.

### Wat is het 'gatewaysubnet' en waarom is het nodig?

Er is een gatewayservice die wordt uitgevoerd om cross-premises-connectiviteit mogelijk te maken. 

Als u een VPN-gateway wilt configureren, moet u eerst een gatewaysubnet voor het VNet maken. Alle gatewaysubnetten moeten de naam GatewaySubnet krijgen. Geef het gatewaysubnet geen andere naam. Implementeer ook geen VM's of iets anders in het gatewaysubnet.

De minimale grootte van het gatewaysubnet is volledig afhankelijk van de configuratie die u wilt maken. Hoewel u voor bepaalde configuraties een gatewaysubnet kunt maken dat zo klein is als /29, raden wij aan om een gatewaysubnet van /28 of groter (/28, /27, /26 enzovoort) te maken. 

## Kan ik Virtual Machines of rolinstanties implementeren in het gatewaysubnet?

Nee.

### Hoe geef ik op welk verkeer via de VPN-gateway loopt?

Als u de klassieke Azure-beheerportal gebruikt, voegt u elk gewenst bereik dat u via de gateway voor het virtuele netwerk wilt verzenden toe op de pagina Netwerken onder Lokale netwerken.

### Kan ik geforceerde tunneling configureren?

Ja. Zie [Configure forced tunneling](vpn-gateway-about-forced-tunneling.md) (Geforceerde tunneling configureren).

### Kan ik mijn eigen VPN-server in Azure instellen en deze gebruiken om verbinding te maken met mijn on-premises netwerk?

Ja, kunt u uw eigen VPN-gateways of -servers in Azure implementeren vanuit de Azure Marketplace of door uw eigen VPN-routers te implementeren. U moet in het virtuele netwerk zelfgedefinieerde routes configureren om ervoor te zorgen dat verkeer juist wordt gerouteerd tussen uw on-premises netwerken en de subnetten van het virtuele netwerk.

### Waarom zijn bepaalde poorten geopend op mijn VPN-gateway?

Deze zijn nodig voor communicatie met de Azure-infrastructuur. Ze zijn beveiligd (vergrendeld) met Azure-certificaten. Zonder de juiste certificaten kunnen externe entiteiten, waaronder de klanten van deze gateways, niets uitvoeren op de eindpunten.

Een VPN-gateway is in feite een multihomed apparaat met één NIC die toegang heeft tot het privénetwerk van de klant en één NIC die is gericht op het openbare netwerk. Infrastructuurentiteiten van Azure hebben om wettelijke redenen geen toegang tot particuliere netwerken van klanten. Daarom moeten ze voor infrastructuurcommunicatie gebruikmaken van openbare eindpunten. De openbare eindpunten worden periodiek gescand door de beveiligingscontrole van Azure.


### Meer informatie over gatewaytypen, vereisten en doorvoer

Zie [About VPN Gateways](vpn-gateway-about-vpngateways.md) (Over VPN-gateways) voor meer informatie.

## Multi-site- en VNet-naar-VNet-connectiviteit

### Welk type gateways ondersteunt multi-site- en VNet-naar-VNet-connectiviteit?

Alleen op route gebaseerde VPN's (dynamische routering).

### Kan ik een VNet met een op route gebaseerd VPN-type verbinden met een op beleid gebaseerd VPN-type?

Nee, beide virtuele netwerken moeten gebruikmaken van op route gebaseerde VPN's (dynamische routering).

### Is het VNet-naar-VNet-verkeer beveiligd?

Ja, het is beveiligd met IPsec/IKE-versleuteling.

### Verplaatst VNet-naar-VNet-verkeer zich via de Azure-backbone?

Ja.

### Met hoeveel on-premises sites en virtuele netwerken kan één virtueel netwerk verbinding maken?

Met max. 10 gecombineerde sites/netwerken voor de gateways met Basic en Standard dynamische routering, en met max. 30 voor VPN-gateways met hoge prestaties.

### Kan ik punt-naar-site-VPN-verbindingen met het virtuele netwerk gebruiken met meerdere VPN-tunnels?

Ja, P2S-VPN-verbindingen (punt-naar-site) kunnen worden gebruikt met de VPN-gateways die verbinding maken met meerdere on-premises sites en andere virtuele netwerken.

### Kan ik meerdere tunnels tussen mijn virtuele netwerk en mijn on-premises site configureren met multi-site-VPN?

Nee, redundante tunnels tussen een virtueel netwerk van Azure en een on-premises site wordt niet ondersteund.

### Mogen er overlappende adresruimten zijn tussen de verbonden virtuele netwerken en on-premises lokale sites?

Nee. Als er overlappende adresruimten zijn, zal het uploaden van het netcfg-bestand of het maken van een virtueel netwerk niet lukken.

### Krijg ik meer bandbreedte met meerdere site-naar-site-VPN-verbindingen dan met één virtueel netwerk?

Nee, alle VPN-tunnels, inclusief punt-naar-site-VPN-verbindingen, delen dezelfde Azure VPN-gateway en beschikbare bandbreedte.

### Kan ik Azure VPN-gateway gebruiken om verkeer tussen mijn on-premises sites of naar een ander virtueel netwerk over te brengen?

Transitverkeer via Azure VPN-gateway is mogelijk, maar is afhankelijk van statisch gedefinieerde adresruimten in het netcfg-configuratiebestand. BGP wordt nog niet ondersteund met Azure Virtual Networks en VPN-gateways. Zonder BGP is het handmatig definiëren van adresruimten voor doorvoer zeer foutgevoelig en het wordt daarom niet aanbevolen.

### Genereert Azure dezelfde vooraf gedeelde IPsec/IKE-sleutel voor al mijn VPN-verbindingen voor hetzelfde virtuele netwerk?

Nee, Azure genereert standaard verschillende vooraf gedeelde sleutels voor verschillende VPN-verbindingen. U kunt echter de REST-API of PowerShell-cmdlet Set VPN Gateway gebruiken om de gewenste sleutelwaarde in te stellen. De sleutel moet een alfanumerieke tekenreeks van 1 tot 128 tekens zijn.

### Worden er door Azure kosten in rekening gebracht voor verkeer tussen virtuele netwerken?

Bij verkeer tussen verschillende virtuele netwerken van Azure worden alleen kosten in rekening gebracht voor verkeer van de ene Azure-regio naar een andere. Een overzicht van de kosten vindt u op de Azure-pagina [Prijzen voor VPN-gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/).


### Kan ik een virtueel netwerk met IPsec-VPN's verbinden met mijn ExpressRoute-circuit?

Ja, dit wordt ondersteund. Voor meer informatie raadpleegt u [Expressroute en site-naar-site-VPN-verbindingen die naast elkaar kunnen worden gebruikt configureren](../expressroute/expressroute-howto-coexist-classic.md)

## BGP

[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 



## Cross-premises-connectiviteit en virtuele machines

### Als de virtuele machine zich in een virtueel netwerk bevindt en ik een cross-premises-verbinding heb, hoe moet ik dan verbinding maken met de virtuele machine?

U hebt een aantal opties. Als u RDP hebt ingeschakeld en een eindpunt hebt gemaakt, kunt u het VIP gebruiken om verbinding te maken met uw virtuele machine. In dat geval geeft u het VIP op en de poort waarmee u verbinding wilt maken. U moet de poort op de virtuele machine configureren voor het verkeer. Gewoonlijk gaat u naar de klassieke Azure Portal en slaat u de instellingen voor de RDP-verbinding op uw computer op. De instellingen bevatten de noodzakelijke verbindingsgegevens.

Als u een virtueel netwerk met cross-premises-connectiviteit hebt geconfigureerd, kunt u het DIP of particuliere IP-adres gebruiken om verbinding te maken met uw virtuele machine. U kunt uw virtuele machine ook verbinden met behulp van het interne DIP van een andere virtuele machine die zich in hetzelfde virtuele netwerk bevindt. Het is niet mogelijk om met het DIP een RDP-verbinding met de virtuele machine te maken als u verbinding maakt vanaf een locatie buiten het virtuele netwerk. Als u bijvoorbeeld een virtueel punt-naar-site-netwerk hebt geconfigureerd en u geen verbinding maakt vanaf uw computer, dan kunt u DIP niet gebruiken om verbinding te maken met de virtuele machine.

### Als mijn virtuele machine zich in een virtueel netwerk met cross-premises-connectiviteit bevindt, gaat al het verkeer vanuit mijn VM dan langs die verbinding?

Nee. Alleen het verkeer met een doel-IP dat zich bevindt in de door u opgegeven IP-adresbereiken van het lokale netwerk van het virtuele netwerk, loopt via de gateway van het virtuele netwerk. Verkeer met een doel-IP binnen het virtuele netwerk blijft in het virtuele netwerk. Ander verkeer wordt via de load balancer verzonden naar de openbare netwerken, tenzij geforceerde tunneling wordt gebruikt. In dat geval wordt het verzonden via de Azure VPN-gateway. Als u problemen moet oplossen, is het belangrijk dat u beschikt over alle bereiken in het lokale netwerk die u via de gateway wilt verzenden. Zorg dat de adresbereiken van het lokale netwerk niet overlappen met adresbereiken in het virtuele netwerk. Bovendien moet u controleren of de DNS-server die u gebruikt de naam omzet in het juiste IP-adres.


## Veelgestelde vragen over Virtual Network

Aanvullende informatie over virtuele netwerken vindt u in de [Veelgestelde vragen over Virtual Network](../virtual-network/virtual-networks-faq.md).

## Volgende stappen

Meer informatie over VPN-gateways vindt u op de [pagina met VPN-gatewaydocumentatie](https://azure.microsoft.com/documentation/services/vpn-gateway/).

 



<!--HONumber=Jun16_HO2-->


