<properties
   pageTitle="Een virtueel netwerk maken met een site-naar-site-VPN-verbinding met Azure Resource Manager en Azure Portal | Microsoft Azure"
   description="In dit artikel leert u stapsgewijs hoe u een VNet maakt met het Resource Manager-model en hoe u dit met uw on-premises netwerk verbindt via een S2S VPN-gatewayverbinding."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/13/2016"
   ms.author="cherylmc"/>

# Een VNet maken met een site-naar-site-VPN-verbinding met de Azure Portal en Azure Resource Manager

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Klassieke Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


In dit artikel leert u stapsgewijs hoe u een virtueel netwerk en een site-naar-site-VPN-verbinding met uw on-premises netwerk maakt met behulp van het Azure Resource Manager-implementatiemodel en de Azure Portal. In de volgende stappen gaat u een VNet maken en een gatewaysubnet, een gateway, een lokale site en een verbinding toevoegen. Daarnaast moet u het VPN-apparaat configureren. 



**Over Azure-implementatiemodellen**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## Verbindingsdiagram

![Site-naar-site](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site2site.png)

**Implementatiemodellen en hulpmiddelen voor site-naar-site-verbindingen**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)] 

Als u VNets met elkaar wilt verbinden, maar geen verbinding maakt met een on-premises locatie, raadpleeg dan [Configure a VNet-to-VNet connection](vpn-gateway-vnet-vnet-rm-ps.md) (Een VNet-naar-VNet-verbinding configureren). Als u de configuratie van een ander type verbinding zoekt, raadpleeg dan het artikel [VPN Gateway connection topologies](vpn-gateway-topology.md) (Verbindingstopologieën voor VPN-gateways).

## Voordat u begint

Controleer voordat u met de configuratie begint of u de volgende items hebt.

- Een compatibel VPN-apparaat en iemand die dit kan configureren. Zie [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Over VPN-apparaten). Als u niet vertrouwd bent met de configuratie van uw VPN-apparaat of niet bekend bent met de IP-adresbereiken in uw on-premises netwerkconfiguratie, moet u contact opnemen met iemand die u deze details kan verstrekken.

- Een extern gericht openbaar IP-adres voor het VPN-apparaat. Dit IP-adres kan zich niet achter een NAT bevinden.
    
- Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Voorbeeld van configuratiewaarden voor deze oefening


Wanneer u deze stappen uitvoert als oefening kunt u de volgende voorbeeldconfiguratiewaarden gebruiken:

- VNet-naam: TestVNet1
- Adresruimte: 10.11.0.0/16 en 10.12.0.0/16
- Subnetten: 
    - FrontEnd: 10.11.0.0/24
    - BackEnd: 10.12.0.0/24
    - GatewaySubnet: 10.12.255.0/27
- Resourcegroep: TestRG1
- Locatie: VS - oost
- DNS-server: 8.8.8.8
- Gatewaynaam: VNet1GW
- Openbare IP: VNet1GWIP
- VPN-type: Op route gebaseerd
- Verbindingstype: site-naar-site (IPsec)
- Gatewaytype: VPN
- Naam van lokale netwerkgateway: Site2
- Verbindingsnaam: VNet1toSite2



## 1. Een virtueel netwerk maken 

Als u al een virtueel netwerk hebt gemaakt, controleert u of de instellingen compatibel zijn met het ontwerp van uw VPN-gateway. Let er met name op dat subnetten geen andere netwerken overlappen. Als u overlappende subnetten hebt, werkt de verbinding niet goed. Als u hebt geverifieerd of het VNet met de juiste instellingen is geconfigureerd, kunt u beginnen met de stappen in de sectie [Een DNS-server opgeven](#dns).

### Een virtueel netwerk maken

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## 2. Extra adresruimte en subnetten toevoegen

Wanneer het VNet is gemaakt, kunt u er extra adresruimte en subnetten aan toevoegen.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

## <a name="dns"></a>3. Een DNS-server opgeven

Als u deze configuratie bij wijze van oefening maakt, gebruikt u deze [waarden](#values) wanneer u de DNS-server opgeeft.

### Een DNS-server opgeven

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## 4. Een gatewaysubnet maken

Voordat u het virtuele netwerk verbindt met een gateway, moet u eerst het gatewaysubnet maken voor het virtuele netwerk waarmee u verbinding wilt maken. Het gatewaysubnet dat u maakt moet de naam *GatewaySubnet* hebben, anders werkt het niet goed. 

Het voorvoegsel van het gatewaysubnet vereist voor bepaalde configuraties een subnet van /28 of hoger om te kunnen voldoen aan het aantal IP-adressen dat nodig is in de pool. Dat betekent dat het voorvoegsel van het gatewaysubnet /28, /27, /26 enzovoort moet zijn. Misschien wilt u hier een groter subnet maken om te voorzien in mogelijke toekomstige configuratietoevoegingen.

Als u deze configuratie bij wijze van oefening maakt, gebruikt u deze [waarden](#values) wanneer u het gatewaysubnet maakt.

### Een gatewaysubnet maken

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## 5. De gateway van een virtueel netwerk maken

Als u deze configuratie bij wijze van oefening maakt, gebruikt u deze [waarden](#values) wanneer u de gateway maakt.

### De gateway van een virtueel netwerk maken

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## 6. Een lokale netwerkgateway maken

De *lokale netwerkgateway* verwijst naar uw on-premises locatie. U geeft de lokale netwerkgateway een naam waarmee Azure naar de gateway kan verwijzen. 

Als u deze configuratie bij wijze van oefening maakt, gebruikt u deze [waarden](#values) wanneer u deze toevoegt aan uw lokale site.

### Een lokale netwerkgateway maken

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## 7. Uw VPN-apparaat configureren

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## 8. Een site-naar-site-VPN-verbinding maken

Vervolgens maakt u de site-naar-site-VPN-verbinding tussen de gateway van uw virtuele netwerk en het VPN-apparaat. Zorg dat u de waarden vervangt door die van uzelf. De gedeelde sleutel moet overeenkomen met de waarde die u hebt gebruikt voor de configuratie van uw VPN-apparaat. 

Controleer voordat u met deze sectie begint of de gateway van het virtuele netwerk en de lokale netwerkgateways zijn voltooid. Als u deze configuratie bij wijze van oefening maakt, gebruikt u deze [waarden](#values) wanneer u de verbinding maakt.

### De VPN-verbinding maken


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## 9. De VPN-verbinding controleren

U kunt de VPN-verbinding controleren in de portal of met behulp van PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## Volgende stappen

- Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie het [leertraject](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) voor virtuele machines voor meer informatie.

- Voor meer informatie over BGP raadpleegt u [BGP Overview](vpn-gateway-bgp-overview.md) (BGP-overzicht) en [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md) (BGP configureren).



<!--HONumber=Jun16_HO2-->


