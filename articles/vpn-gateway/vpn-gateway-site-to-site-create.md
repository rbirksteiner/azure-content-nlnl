<properties
   pageTitle="Een virtueel netwerk maken met een site-naar-site-VPN-gatewayverbinding met de klassieke Azure Portal | Microsoft Azure"
   description="Maak een VNet met een S2S-VPN-gatewayverbinding voor cross-premises en hybride configuraties met behulp van het klassieke implementatiemodel."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/13/2016"
   ms.author="cherylmc"/>

# Een virtueel netwerk maken met een site-naar-site-VPN-verbinding met de klassieke Azure Portal

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Klassieke Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


In dit artikel leert u stapsgewijs hoe u een virtueel netwerk en een site-naar-site-VPN-verbinding maakt met uw on-premises netwerk. Site-naar-site-verbindingen kunnen worden gebruikt voor cross-premises en hybride configuraties. Dit artikel is van toepassing op het klassieke implementatiemodel en maakt gebruik van de klassieke Azure Portal. 


**Over Azure-implementatiemodellen**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## Verbindingsdiagram
 
![Site-naar-site-diagram](./media/vpn-gateway-site-to-site-create/site2site.png "site-to-site")

**Implementatiemodellen en hulpmiddelen voor site-naar-site-verbindingen**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Als u VNets met elkaar wilt verbinden, maar geen verbinding maakt met een on-premises locatie, raadpleeg dan [Configure a VNet-to-VNet connection for the classic deployment model](virtual-networks-configure-vnet-to-vnet-connection.md) (Een VNet-naar-VNet verbinding voor het klassieke implementatiemodel configureren). Als u de configuratie van een ander type verbinding zoekt, raadpleeg dan het artikel [VPN Gateway connection topologies](vpn-gateway-topology.md) (Verbindingstopologieën voor VPN-gateways).

 
## Voordat u begint

Controleer op voorhand of u de volgende items hebt.

- Een compatibel VPN-apparaat en iemand die dit kan configureren. Zie [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Over VPN-apparaten). Als u niet vertrouwd bent met de configuratie van uw VPN-apparaat of niet bekend bent met de IP-adresbereiken in uw on-premises netwerkconfiguratie, moet u contact opnemen met iemand die u deze details kan verstrekken.

-  Een extern gericht openbaar IP-adres voor het VPN-apparaat. Dit IP-adres kan zich niet achter een NAT bevinden.

- Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial/).


## Het virtuele netwerk maken

1. Meld u aan bij de [klassieke Azure Portal](https://manage.windowsazure.com/).

2. Klik linksonder in het scherm op **Nieuw**. Klik in het navigatievenster op **Netwerkservices** en vervolgens op **Virtueel netwerk**. Klik op **Aangepast maken** om de configuratiewizard te starten.

3. Vul de informatie op de volgende pagina's in om het VNet te maken.

## Pagina met details voor virtueel netwerk

Voer de volgende informatie in.

- **Naam**: geef het virtuele netwerk een naam. Bijvoorbeeld *EastUSVNet*. U gebruikt deze naam voor het virtuele netwerk wanneer u de virtuele machines en PaaS-instanties implementeert. Maak de naam dus niet te ingewikkeld.
- **Locatie**: de locatie is direct gerelateerd aan de fysieke locatie (regio) waar u wilt dat de resources (VM's) zich bevinden. Selecteer bijvoorbeeld VS - oost als u wilt dat de virtuele machines die u in dit virtuele netwerk implementeert zich fysiek bevinden in *VS - oost*. U kunt de regio die aan het virtuele netwerk is gekoppeld niet meer wijzigen wanneer het netwerk is gemaakt.

## Pagina DNS-servers en VPN-verbinding 

Voer de volgende informatie in en klik rechtsonder op de pijl Volgende.

- **DNS-servers**: geef de naam en het IP-adres van de DNS-server op of selecteer een eerder geregistreerde DNS-server in het snelmenu. Met deze instelling wordt geen DNS-server gemaakt. U kunt geeft hiermee alleen de DNS-servers op die u wilt gebruiken voor de naamomzetting voor dit virtuele netwerk.
- **Site-naar-site-VPN configureren**: schakel het selectievakje **Site-naar-site-VPN configureren** in.
- **Lokaal netwerk**: een lokaal netwerk vertegenwoordigt uw fysieke on-premises locatie. U kunt een lokaal netwerk selecteren dat u eerder hebt gemaakt of u kunt een nieuw lokaal netwerk maken. Als u echter kiest voor het gebruik van een lokaal netwerk dat u eerder hebt gemaakt, wilt u misschien op de configuratiepagina **Lokale netwerken** controleren of het IP-adres van het VPN-apparaat (openbaar IPv4-adres) voor het VPN-apparaat dat u voor deze verbinding gebruikt juist is.

## Pagina Site-naar-site-connectiviteit

Als u een nieuw lokaal netwerk maakt, wordt de pagina **Site-naar-site-connectiviteit** geopend. Als u een lokaal netwerk wilt gebruiken dat u eerder hebt gemaakt, wordt deze pagina niet in de wizard weergegeven en kunt u naar de volgende sectie gaan.

Voer de volgende informatie in en klik op de pijl Volgende.

-   **Naam**: de naam voor de lokale (on-premises) netwerksite.
-   **IP-adres van VPN-apparaat**: dit is het openbare IPv4-adres van het on-premises VPN-apparaat dat u gebruikt om verbinding te maken met Azure. Het VPN-apparaat kan zich niet achter een NAT bevinden.
-   **Adresruimte**: neem het IP-beginadres en de CIDR (aantal adressen) op. Hier geeft u de adresbereiken op die u via de gateway van het virtuele netwerk naar uw lokale on-premises locatie wilt laten verzenden. Als het IP-adres van een bestemming binnen de bereiken valt die u hier opgeeft, wordt het via de gateway van het virtuele netwerk gerouteerd.
-   **Adresruimte toevoegen**: als u meerdere-adresbereiken via de gateway van het virtuele netwerk wilt laten verzenden, kunt u hier aanvullende adresbereiken opgeven. U kunt later op de pagina **Lokaal netwerk** bereiken toevoegen of verwijderen.

## Pagina Adresruimten voor virtueel netwerk

Geef het adresbereik op dat u voor het virtuele netwerk wilt gebruiken. Dit zijn de dynamische IP-adressen (DIPS) die worden toegewezen aan de virtuele machines en andere rolinstanties die u in dit virtuele netwerk implementeert.

Het is vooral van belang dat u een bereik selecteert dat niet overlapt met een van de bereiken die voor uw on-premises netwerk worden gebruikt. Neem hiervoor contact op met uw netwerkbeheerder. De netwerkbeheerder moet mogelijk een bereik van IP-adressen in de adresruimte van uw on-premises netwerk reserveren voor het virtuele netwerk.

Voer de volgende informatie in en klik linksonder op het vinkje om het netwerk te configureren.

- **Adresruimte**: neem het IP-beginadres en het Aantal adressen op. Controleer of de adresruimten die u opgeeft niet overlappen met adresruimten in uw on-premises netwerk.
- **Subnet toevoegen**: neem het IP-beginadres en het Aantal adressen op. U hebt geen aanvullende subnetten nodig, maar misschien wilt u een apart subnet maken voor virtuele machines die statische DIPS krijgen. Of misschien wilt u uw virtuele machines in een subnet plaatsen dat is gescheiden van uw andere rolinstanties.
- **Gatewaysubnet toevoegen**: klik om het gatewaysubnet toe te voegen. Het gatewaysubnet wordt alleen gebruikt voor de gateway van het virtuele netwerk. Het is vereist voor deze configuratie.

Wanneer u onder aan de pagina op het vinkje klikt, wordt het virtuele netwerk gemaakt. Als dit is voltooid, wordt op de pagina **Netwerken** in de klassieke Azure Portal **Gemaakt** vermeld onder **Status**. Wanneer het VNet is gemaakt, kunt u de gateway van het virtuele netwerk configureren.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 

## De gateway van het virtuele netwerk configureren

Vervolgens configureert u de gateway van het virtuele netwerk om een beveiligde site-naar-site-verbinding te maken. Zie [Configure a virtual network gateway in the Azure classic portal](vpn-gateway-configure-vpn-gateway-mp.md) (De gateway van een virtueel netwerk configureren in de klassieke Azure Portal).

## Volgende stappen

Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Raadpleeg de documentatie over [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) voor meer informatie.



<!--HONumber=Jun16_HO2-->


