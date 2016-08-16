<properties
   pageTitle="Een virtueel netwerk maken met een site-naar-site-VPN-verbinding met Azure Resource Manager en PowerShell | Microsoft Azure"
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

# Een virtueel netwerk maken met een site-naar-site-VPN-verbinding met PowerShell en Azure Resource Manager

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Klassieke Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

In dit artikel leert u stapsgewijs hoe u een virtueel netwerk en een site-naar-site-VPN-verbinding met uw on-premises netwerk maakt met behulp van het Azure Resource Manager-implementatiemodel. Site-naar-site-verbindingen kunnen worden gebruikt voor cross-premises en hybride configuraties. 


**Over Azure-implementatiemodellen**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Verbindingsdiagram 

![Site-naar-site-diagram](./media/vpn-gateway-create-site-to-site-rm-powershell/site2site.png "site-to-site")

**Implementatiemodellen en hulpmiddelen voor site-naar-site-verbindingen**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Als u VNets met elkaar wilt verbinden, maar geen verbinding maakt met een on-premises locatie, raadpleeg dan [Configure a VNet-to-VNet connection](vpn-gateway-vnet-vnet-rm-ps.md) (Een VNet-naar-VNet-verbinding configureren). Als u de configuratie van een ander type verbinding zoekt, raadpleeg dan het artikel [VPN Gateway connection topologies](vpn-gateway-topology.md) (Verbindingstopologieën voor VPN-gateways).


## Voordat u begint

Controleer op voorhand of u de volgende items hebt.

- Een compatibel VPN-apparaat en iemand die dit kan configureren. Zie [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Over VPN-apparaten). Als u niet vertrouwd bent met de configuratie van uw VPN-apparaat of niet bekend bent met de IP-adresbereiken in uw on-premises netwerkconfiguratie, moet u contact opnemen met iemand die u deze details kan verstrekken.

- Een extern gericht openbaar IP-adres voor het VPN-apparaat. Dit IP-adres kan zich niet achter een NAT bevinden.
    
- Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial/).
    
- U moet de meest recente versie van de Azure Resource Manager PowerShell-cmdlets installeren. Zie [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van de PowerShell-cmdlets.


## 1. Verbinding maken met uw abonnement 

Zorg ervoor dat u overschakelt naar de PowerShell-modus als u de Resource Manager-cmdlets wilt gebruiken. Zie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Windows PowerShell gebruiken met Resource Manager) voor meer informatie.

Open de PowerShell-console en maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

    Login-AzureRmAccount

Controleer de abonnementen voor het account.

    Get-AzureRmSubscription 

Geef het abonnement op dat u wilt gebruiken.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 2. Een virtueel netwerk en een gatewaysubnet maken

In onze voorbeelden hieronder ziet u een gatewaysubnet van /28. Hoewel het mogelijk is om een gatewaysubnet van slechts /29 te maken, wordt dit niet aanbevolen. We raden u aan om een gatewaysubnet van /27 of groter (/26/25 enzovoort) te maken, zodat u ruimte hebt om extra functievereisten toe te voegen. 

Als u al een virtueel netwerk hebt met een gatewaysubnet van /29 of groter, kunt u verdergaan met [De lokale netwerkgateway toevoegen](#localnet).


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]  

### Een virtueel netwerk en een gatewaysubnet maken

Gebruik onderstaand voorbeeld als hulp bij het maken van een virtueel netwerk en een gatewaysubnet. Vervang de waarden door uw eigen waarden. 

Eerst maakt u een resourcegroep:
    
    New-AzureRmResourceGroup -Name testrg -Location 'West US'

Vervolgens maakt u het virtuele netwerk. Controleer of de adresruimten die u opgeeft niet overlappen met adresruimten in uw on-premises netwerk.

In onderstaand voorbeeld wordt een virtueel netwerk gemaakt met de naam *testvnet*. Er worden ook twee subnetten gemaakt, *GatewaySubnet* en *Subnet1*. Het is belangrijk dat er één subnet met de exacte naam *GatewaySubnet* wordt gemaakt. Als u een andere naam kiest, mislukt de configuratie van de verbinding. 

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Een gatewaysubnet toevoegen aan een virtueel netwerk dat u al hebt gemaakt

Deze stap is alleen vereist als u een gatewaysubnet wilt toevoegen aan een VNet dat u eerder hebt gemaakt.

U kunt het gatewaysubnet maken met behulp van het voorbeeld hieronder. Zorg dat u het gatewaysubnet 'GatewaySubnet' noemt. Als u een andere naam kiest, maakt u wel een subnet, maar wordt dit door Azure niet als een gatewaysubnet behandeld.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Nu gaat u de configuratie instellen. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3. <a name="localnet"></a>De lokale netwerkgateway toevoegen

In een virtueel netwerk verwijst de lokale netwerkgateway doorgaans naar uw on-premises locatie. U geeft de site een naam waarmee Azure naar de site kan verwijzen, en u geeft ook het adresruimtevoorvoegsel voor de lokale netwerkgateway op. 

Azure gebruikt het IP-adresvoorvoegsel dat u opgeeft om te bepalen welk verkeer naar uw on-premises locatie moet worden verzonden. Dit betekent dat u elk adresvoorvoegsel moet opgeven dat u aan uw lokale netwerkgateway wilt koppelen. Als uw on-premises netwerk verandert, kunt u deze voorvoegsels eenvoudig bijwerken. 

Let op het volgende wanneer u de PowerShell-voorbeelden volgt:
    
- *GatewayIPAddress* is het IP-adres van uw on-premises VPN-apparaat. Het VPN-apparaat kan zich niet achter een NAT bevinden. 
- *AddressPrefix* is uw on-premises adresruimte.

Ga als volgt te werk als u een lokale netwerkgateway met één adresvoorvoegsel wilt toevoegen:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Ga als volgt te werk als u een lokale netwerkgateway met meerdere adresvoorvoegsels wilt toevoegen:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### IP-adresvoorvoegsels wijzigen voor uw lokale netwerkgateway

Soms veranderen de voorvoegsels voor uw lokale netwerkgateway. De stappen waarmee u de IP-adresvoorvoegsels moet wijzigen, zijn afhankelijk van of u al dan niet een VPN-gatewayverbinding hebt gemaakt. Raadpleeg de sectie [Adresvoorvoegsels voor een lokale netwerkgateway wijzigen](#modify) van dit artikel.


## 4. Een openbaar IP-adres voor de VPN-gateway aanvragen

Vervolgens vraagt u een openbaar IP-adres aan dat moet worden toegewezen aan de VPN-gateway van uw Azure VNet. Dit is niet hetzelfde IP-adres dat is toegewezen aan uw VPN-apparaat. Het wordt toegewezen aan de Azure VPN-gateway zelf. U kunt het IP-adres dat u wilt gebruiken, niet zelf opgeven. Het wordt dynamisch aan uw gateway toegewezen. U gebruikt dit IP-adres tijdens de configuratie van uw on-premises VPN-apparaat om verbinding te maken met de gateway.

Gebruik het onderstaande PowerShell-voorbeeld. De toewijzingsmethode voor dit adres moet Dynamisch zijn. 

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

>[AZURE.NOTE] De Azure VPN-gateway voor het Resource Manager-implementatiemodel ondersteunt momenteel alleen openbare IP-adressen met behulp van de dynamische toewijzingsmethode. Dit betekent echter niet dat het IP-adres verandert. Het IP-adres van de Azure VPN-gateway verandert alleen wanneer de gateway wordt verwijderd en opnieuw wordt gemaakt. Het openbare IP-adres van de gateway verandert niet wanneer uw Azure VPN-gateway van formaat verandert, opnieuw wordt ingesteld of wanneer andere onderhoudswerkzaamheden of upgrades worden uitgevoerd.

## 5. De IP-adresseringsconfiguratie voor de gateway maken

De gatewayconfiguratie bepaalt welk subnet en openbaar IP-adres moeten worden gebruikt. Gebruik het voorbeeld hieronder om de gatewayconfiguratie te maken. 

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6. De gateway van het virtuele netwerk maken

In deze stap maakt u de gateway van het virtuele netwerk. Het maken van een gateway kan vrij lang duren. Vaak 20 minuten of langer. 

Gebruik de volgende waarden:

- Het *-GatewayType* voor een site-naar-site-configuratie is *VPN*. Het gatewaytype is altijd specifiek voor de configuratie die u implementeert. Andere gatewayconfiguraties vereisen misschien -GatewayType ExpressRoute. 

- Het *-VpnType* kan *RouteBased* (in sommige documentatie een dynamische gateway genoemd) of *PolicyBased* (in sommige documentatie een statische gateway genoemd) zijn. Voor meer informatie over VPN-gatewaytypen raadpleegt u [Over VPN-gateways](vpn-gateway-about-vpngateways.md#vpntype).
- De *-GatewaySku* kan *Basic*, *Standard* of *HighPerformance* zijn.   

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

## 7. Uw VPN-apparaat configureren

Nu hebt u het openbare IP-adres van de gateway van het virtuele netwerk nodig om uw on-premises VPN-apparaat te configureren. Neem contact op met de fabrikant van uw apparaat voor specifieke configuratiegegevens. Raadpleeg daarnaast [VPN-apparaten](vpn-gateway-about-vpn-devices.md) voor meer informatie.

Gebruik het volgende voorbeeld om het openbare IP-adres van de gateway van uw virtuele netwerk te vinden:

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8. De VPN-verbinding maken

Vervolgens maakt u de site-naar-site-VPN-verbinding tussen de gateway van uw virtuele netwerk en het VPN-apparaat. Zorg dat u de waarden vervangt door die van uzelf. De gedeelde sleutel moet overeenkomen met de waarde die u hebt gebruikt voor de configuratie van uw VPN-apparaat. Het `-ConnectionType` voor Site-naar-Site is *IPsec*. 

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Na een korte tijd wordt de verbinding tot stand gebracht. 

## 9. Een VPN-verbinding controleren

Er zijn een aantal verschillende manieren om uw VPN-verbinding te controleren. Hierna wordt uitgelegd hoe u eenvoudige verificatie uitvoert met behulp van de Azure Portal en met PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>IP-adresvoorvoegsels wijzigen voor de gateway van een lokaal netwerk

Volg onderstaande instructies als u de voorvoegsels voor de gateway van een lokaal netwerk wilt wijzigen.  Er zijn twee sets met instructies. Welke instructies u kiest, is afhankelijk van of u uw VPN-gatewayverbinding al hebt gemaakt. 

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]


## Volgende stappen

- Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Een virtuele machine maken](../virtual-machines/virtual-machines-windows-hero-tutorial.md) voor de stappen.

- Voor meer informatie over BGP raadpleegt u [BGP Overview](vpn-gateway-bgp-overview.md) (BGP-overzicht) en [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md) (BGP configureren).




<!--HONumber=Jun16_HO2-->


