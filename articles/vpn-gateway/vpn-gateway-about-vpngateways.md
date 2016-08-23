<properties 
   pageTitle="Informatie over VPN-gateway| Microsoft Azure"
   description="Meer informatie over VPN-gateway voor Azure Virtual Network."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# Informatie over VPN-gateway

VPN-gateway wordt gebruikt voor het verzenden van netwerkverkeer tussen virtuele netwerken en on-premises locaties. Het wordt ook gebruikt om verkeer tussen meerdere virtuele netwerken in Azure (VNet naar VNet) te verzenden. In de volgende secties worden de items besproken die zijn gerelateerd aan VPN-gateway.

De instructies die u gebruikt voor het maken van uw VPN-gateway zijn afhankelijk van het implementatiemodel waarmee u uw virtuele netwerk hebt gemaakt. Als u bijvoorbeeld uw VNet hebt gemaakt met het klassieke implementatiemodel, gebruikt u de richtlijnen en instructies voor het klassieke implementatiemodel om uw VPN-gateway te maken en te configureren. Het is niet mogelijk om een Resource Manager-VPN-gateway te maken voor een virtueel netwerk van een klassiek implementatiemodel. 

Zie [Understanding Resource Manager and classic deployment models](../resource-manager-deployment-model.md) (Het Resource Manager-implementatie en klassieke implementatie begrijpen) voor meer informatie over de implementatiemodellen.


## <a name="gwsub"></a>Gatewaysubnet

Als u een VPN-gateway wilt configureren, moet u eerst een gatewaysubnet voor het VNet maken. Voor een goede werking moet het gatewaysubnet de naam *GatewaySubnet* krijgen. Aan de hand van deze naam weet Azure dat dit subnet voor de gateway moet worden gebruikt.<BR>Als u de klassieke portal gebruikt, krijgt het gatewaysubnet in de interface van de portal automatisch de naam *Gateway*. Dit is alleen het geval wanneer het gatewaysubnet in de klassieke portal wordt weergegeven. In dit geval wordt het subnet in feite in Azure gemaakt als *GatewaySubnet* en kan het op deze manier worden weergegeven in de Azure Portal en in PowerShell.

De minimale grootte van het gatewaysubnet is volledig afhankelijk van de configuratie die u wilt maken. Hoewel u voor bepaalde configuraties een gatewaysubnet kunt maken dat zo klein is als /29, raden wij aan om een gatewaysubnet van /28 of groter (/28, /27, /26 enzovoort) te maken. 

Door grotere gateways te maken, voorkomt u dat u later tegen gatewaybeperkingen aanloopt. Als u bijvoorbeeld een gateway maakt met een gatewaysubnetgrootte van /29 en u een configuratie wilt maken waarbij Site-naar-Site en ExpressRoute naast elkaar worden gebruikt, moet u de gateway verwijderen, het gatewaysubnet verwijderen, het gatewaysubnet maken als een /28 of groter en vervolgens de gateway opnieuw maken. 

Door direct al een groter gatewaysubnet te maken, bespaart u later tijd wanneer u nieuwe configuratiefuncties aan uw netwerkomgeving toevoegt. 

In het volgende voorbeeld ziet u een gatewaysubnet met de naam GatewaySubnet. U ziet dat de CIDR-notatie een /27 opgeeft. Dit biedt voldoende IP-adressen voor de meeste configuraties die er op dit moment zijn.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Zorg dat er op het GatewaySubnet geen netwerkbeveiligingsgroep (NSG) is toegepast, aangezien dit tot verbindingsproblemen kan leiden.

## <a name="gwtype"></a>Gatewaytypen

Het Gatewaytype bepaalt hoe de gateway zelf is verbonden. Het is een vereiste configuratie-instelling voor het Resource Manager-implementatiemodel. Verwar het gatewaytype niet met het VPN-type. Hiermee wordt het type routering voor uw VPN opgegeven. De beschikbare waarden voor `-GatewayType` zijn: 

- VPN
- ExpressRoute


In dit voorbeeld voor het Resource Manager-implementatiemodel is het GatewayType *VPN* opgegeven. Wanneer u een gateway maakt, moet u het juiste gatewaytype voor uw configuratie kiezen. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="gwsku"></a>Gateway-SKU's

Wanneer u een VPN-gateway maakt, moet u de gewenste gateway-SKU opgeven. Er zijn drie VPN-gateway-SKU's:

- Basic
- Standard
- HighPerformance

In onderstaand voorbeeld is de `-GatewaySku` *Standard* opgegeven.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

###  <a name="aggthroughput"></a>Geschatte geaggregeerde doorvoer per SKU en gatewaytype


In de volgende tabel ziet u de gatewaytypen en de geschatte geaggregeerde doorvoer. De prijzen zijn afhankelijk van de gateway-SKU's. Zie [Prijzen voor VPN-gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/) voor meer informatie over prijzen. Deze tabel is van toepassing op de Resource Manager en de klassieke implementatiemodellen.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

## <a name="vpntype"></a>VPN-typen

Elke configuratie vereist een specifiek VPN-type om te kunnen werken. Als u twee configuraties combineert - u maakt bijvoorbeeld een site-naar-site-verbinding en een punt-naar-site-verbinding met hetzelfde VNet - dan moet u een VPN-type gebruiken dat voldoet aan de vereisten van beide verbindingen. 

In het geval van een punt-naar-site-verbinding naast een site-naar-site-verbinding, moet u een op een VPN-route gebaseerd VPN-type gebruiken als u werkt met het Azure Resource Manager-implementatiemodel, of een dynamische gateway als u werkt met de klassieke implementatiemodus.

Wanneer u de configuratie maakt, selecteert u het VPN-type dat is vereist voor de verbinding. 

Er zijn twee VPN-typen:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

In dit voorbeeld voor het Resource Manager-implementatiemodel is het `-VpnType` *RouteBased* opgegeven. Wanneer u een gateway maakt, moet u het juiste VPN-type voor uw configuratie kiezen. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="connectiontype"></a>Verbindingstypen

Elke configuratie vereist een specifiek verbindingstype. De beschikbare Resource Manager PowerShell-waarden voor `-ConnectionType` zijn:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

In het volgende voorbeeld wordt een site-naar-site-verbinding gemaakt die het verbindingstype IPsec vereist.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="lng"></a>Lokale netwerkgateways

De lokale netwerkgateway verwijst doorgaans naar uw on-premises locatie. In het klassieke implementatiemodel werd de lokale gateway een lokale site genoemd. U gaat de lokale netwerkgateway een naam geven, het openbare IP-adres van het on-premises VPN-apparaat, en de adresvoorvoegsels opgeven die zich op de on-premises-locatie bevinden. Azure kijkt naar de voorvoegsels voor de bestemmingsadressen voor netwerkverkeer, raadpleegt de configuratie die u voor uw lokale netwerkgateway hebt opgegeven en routeert pakketten dienovereenkomstig. U kunt deze adresvoorvoegsels zo nodig wijzigen.



### Adresvoorvoegsels wijzigen - Resource Manager

De procedure voor het wijzigen van adresvoorvoegsels is afhankelijk van of u al uw VPN-gateway hebt gemaakt. Zie het artikel [Modify address prefixes for a local network gateway](vpn-gateway-create-site-to-site-rm-powershell.md#modify) (Adresvoorvoegsels voor een lokale netwerkgateway wijzigen).

In het volgende voorbeeld ziet u een lokale netwerkgateway met de naam MyOnPremiseWest die twee IP-adresvoorvoegsels bevat.

    New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24') 

### Adresvoorvoegsels wijzigen - klassieke implementatie

Als u uw lokale sites moet wijzigen wanneer u het klassieke implementatiemodel gebruikt, kunt u de configuratiepagina Lokale netwerken in de klassieke portal gebruiken of het netwerkconfiguratiebestand NETCFG.XML rechtstreeks wijzigen.


##  <a name="devices"></a> VPN-apparaten

Zorg ervoor dat het VPN-apparaat dat u wilt gaan gebruiken ondersteuning biedt voor het VPN-type dat voor uw configuratie is vereist. Zie [Over VPN-apparaten](vpn-gateway-about-vpn-devices.md) voor meer informatie over compatibele VPN-apparaten.

##  <a name="requirements"></a>Gatewayvereisten


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 


## Volgende stappen

Zie het artikel [VPN Gateway FAQ](vpn-gateway-vpn-faq.md) (Veelgestelde vragen over de VPN-gateway) voor meer informatie, voordat u verder gaat met het plannen en ontwerpen van uw configuratie.





 



<!--HONumber=Jun16_HO2-->


