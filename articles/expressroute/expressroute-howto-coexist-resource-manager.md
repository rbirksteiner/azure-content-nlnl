<properties
   pageTitle="ExpressRoute- en site-naar-site-VPN-verbindingen configureren die naast elkaar kunnen worden gebruikt in het Resource Manager-implementatiemodel | Microsoft Azure"
   description="Dit artikel begeleidt u bij het configureren van ExpressRoute- en site-naar-site-VPN-verbindingen die naast elkaar kunnen worden gebruikt in het Resource Manager-implementatiemodel."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/04/2016"
   ms.author="charleywen"/>

# ExpressRoute- en site-naar-site-verbindingen configureren die naast elkaar kunnen worden gebruikt in het Resource Manager-implementatiemodel

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
- [PowerShell - Klassiek](expressroute-howto-coexist-classic.md)

De mogelijkheid om site-naar-site-VPN en ExpressRoute te configureren heeft verschillende voordelen. U kunt site-naar-site-VPN configureren als een beveiligd failoverpad voor ExressRoute of site-naar-site-VPN’s gebruiken om verbinding te maken met sites die niet zijn verbonden via ExpressRoute. In dit artikel gaan we in op de stappen voor het configureren van beide scenario's. Dit artikel is van toepassing op het Resource Manager-implementatiemodel. Deze configuratie is niet beschikbaar in de Azure Portal.


**Over Azure-implementatiemodellen**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

>[AZURE.IMPORTANT] ExpressRoute-circuits moeten vooraf worden geconfigureerd voordat u de onderstaande instructies volgt. Zorg ervoor dat u de handleidingen hebt gevolgd [om een ExpressRoute-circuit te maken](expressroute-howto-circuit-arm.md) en [routering te configureren](expressroute-howto-routing-arm.md) voordat u de volgende stappen volgt.

## Limieten en beperkingen

- **Transitroutering wordt niet ondersteund:** u kunt niet (via Azure) routeren tussen uw lokale netwerk dat is verbonden via site-naar-site-VPN en uw lokale netwerk dat is verbonden via ExpressRoute.
- **Geforceerde tunneling kan niet worden ingeschakeld op de site-naar-site-VPN-gateway:** u kunt alle internetverkeer alleen terugsturen naar uw on-premises netwerk via ExpressRoute. 
- **Alleen gateways met standaardprestaties of hoge prestaties:** u moet een gateway met standaardprestaties of hoge prestaties gebruiken voor de ExpressRoute-gateway en de site-naar-site-VPN-gateway. Zie [Gateway-SKU's](../vpn-gateway/vpn-gateway-about-vpngateways.md) voor informatie over gateway-SKU's.
- **Alleen op route gebaseerde VPN-gateway:** u moet een op route gebaseerde VPN-gateway gebruiken. Zie [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) voor informatie over de op route gebaseerde VPN-gateway.
- **Statische route vereist:** als uw lokale netwerk is verbonden met ExpressRoute en een site-naar-site-VPN, moet u in uw lokale netwerk een statische route hebben geconfigureerd voor het routeren van de site-naar-site-VPN-verbinding met het openbare internet.
- **De ExpressRoute-gateway moet als eerste worden geconfigureerd:** u moet de ExpressRoute-gateway maken voordat u de site-naar-site-VPN-gateway toevoegt.


## Configuratie-ontwerpen

### Een site-naar-site-VPN configureren als een failoverpad voor ExpressRoute

U kunt een site-naar-site-VPN-verbinding configureren als een back-up voor ExpressRoute. Dit geldt alleen voor virtuele netwerken die zijn gekoppeld aan het pad voor persoonlijke Azure-peering. Er is geen op VPN gebaseerde failoveroplossing voor services die toegankelijk zijn via openbare Azure- en Microsoft-peerings. Het ExpressRoute-circuit is altijd de primaire koppeling. Gegevens worden alleen via het site-naar-site-VPN-pad geleid als het ExpressRoute-circuit niet beschikbaar is. 

![Naast elkaar gebruiken](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### Een site-naar-site-VPN configureren om verbinding te maken met sites die niet zijn verbonden via ExpressRoute

U kunt uw netwerk zodanig configureren dat sommige sites rechtstreeks verbinding maken met Azure via site-naar-site-VPN en sommige sites verbinding maken via ExpressRoute. 

![Naast elkaar gebruiken](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

>[AZURE.NOTE] U kunt een virtueel netwerk niet configureren als transitrouter.

## De stappen selecteren die u gaat gebruiken

Er zijn twee sets met procedures waaruit u kunt kiezen om verbindingen te configureren die naast elkaar kunnen worden gebruikt. Welke configuratieprocedure u selecteert, is afhankelijk van het gegeven of u een bestaand virtueel netwerk hebt waarmee u verbinding wilt maken, of of u een nieuw virtueel netwerk wilt maken.


- Ik heb geen VNet en moet er een maken.
    
    Als u nog geen virtueel netwerk hebt, begeleidt deze procedure u bij het maken van een nieuw virtueel netwerk met behulp van de Resource Manager-implementatie, en bij het maken van nieuwe ExpressRoute- en site-naar-site-VPN-verbindingen. Volg voor de configuratie de stappen in het gedeelte [Een nieuw virtueel netwerk en naast elkaar bestaande verbindingen maken](#new) in dit artikel.

- Ik heb al een VNet van het Resource Manager-implementatiemodel.

    Mogelijk hebt u al een virtueel netwerk met een bestaande site-naar-site-VPN-verbinding of ExpressRoute-verbinding. Het gedeelte [Naast elkaar bestaande verbindingen configureren voor een bestaand VNet](#add) begeleidt u bij het verwijderen van de gateway en vervolgens bij het maken van nieuwe ExpressRoute- en site-naar-site-VPN-verbindingen. Houd er rekening mee dat de stappen voor het maken van de nieuwe verbindingen moeten worden uitgevoerd in een specifieke volgorde. Gebruik geen instructies uit andere artikelen om uw gateways en verbindingen te maken.

    In deze procedure moet u uw gateway verwijderen en vervolgens nieuwe gateways configureren om verbindingen te maken die naast elkaar kunnen worden gebruikt. Dit betekent dat u tijdens het verwijderen en opnieuw maken van uw gateway en verbindingen rekening moet houden met uitvaltijd voor uw cross-premises verbindingen. U hoeft uw virtuele machines of services echter niet te migreren naar een nieuw virtueel netwerk. Terwijl u uw gateway configureert, kunnen uw virtuele machines en services nog steeds communiceren via de load balancer, mits ze hiervoor zijn geconfigureerd.


## <a name="new"></a>Een nieuw virtueel netwerk en naast elkaar bestaande verbindingen maken

Deze procedure helpt u bij het maken van een VNet en site-naar-site- en ExpressRoute-verbindingen die naast elkaar kunnen worden gebruikt.
    
1. U moet de meest recente versie van de Azure PowerShell-cmdlets installeren. Zie [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van de PowerShell-cmdlets. Houd er rekening mee dat de cmdlets die u voor deze configuratie gebruikt, mogelijk enigszins afwijken van de cmdlets waarmee u bekend bent. Zorg ervoor dat u de cmdlets gebruikt die in deze instructies worden vermeld.

2. Meld u aan bij uw account en stel de omgeving in.
    
        login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
        $location = "Central US"
        $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location

3. Maak een virtueel netwerk met een gatewaysubnet. Zie [Azure Virtual Network-configuratie](../virtual-network/virtual-networks-create-vnet-arm-ps.md) voor meer informatie over de configuratie van het virtuele netwerk.

    >[AZURE.IMPORTANT] Het gatewaysubnet moet /27 of een korter voorvoegsel (zoals /26 of /25) zijn.
    
    Maak een nieuw VNet.

        $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 

    Voeg subnetten toe.

        Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    Sla de VNet-configuratie op.

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

4. <a name="gw"></a>Maak een ExpressRoute-gateway. Zie [ExpressRoute-gatewayconfiguratie](expressroute-howto-add-gateway-resource-manager.md) voor meer informatie over de configuratie van de ExpressRoute-gateway. De GatewaySKU moet *Standard* of *HighPerformance* zijn.

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 

5. Koppel de ExpressRoute-gateway aan het ExpressRoute-circuit. Nadat deze stap is voltooid, wordt de verbinding tussen uw on-premises netwerk en Azure tot stand gebracht via ExpressRoute. Zie [VNets koppelen aan ExpressRoute](expressroute-howto-linkvnet-arm.md) voor meer informatie over de koppelingsbewerking.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
        New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute

6. <a name="vpngw"></a>Maak vervolgens uw site-naar-site-VPN-gateway. Zie [Een VNet-naar-VNet-verbinding configureren](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) voor meer informatie over de configuratie van de VPN-gateway. De GatewaySKU moet *Standard* of *HighPerformance* zijn. Het VpnType moet *RouteBased* zijn.

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"

7. Maak een lokaal exemplaar van de VPN-gateway van uw site. Deze opdracht wordt niet gebruikt om uw on-premises VPN-gateway te configureren. Met deze opdracht kunt u de instellingen voor de lokale gateway opgeven, zoals het openbare IP-adres en de on-premises-adresruimte, zodat de Azure VPN-gateway er verbinding mee kan maken. 
    >[AZURE.NOTE] Als uw lokale netwerk meerdere routes heeft, kunt u ze doorgeven als een matrix.  $MyLocalNetworkAddress = @('10.100.0.0/16', '10.101.0.0/16', '10.102.0.0/16')  

        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix '10.100.0.0/16'

8. Configureer het lokale VPN-apparaat om verbinding te maken met de nieuwe Azure VPN-gateway. Zie [VPN-apparaatconfiguratie](../vpn-gateway/vpn-gateway-about-vpn-devices.md) voor meer informatie over het configureren van een VPN-apparaat. 

9. Koppel de site-naar-site-VPN-gateway in Azure aan de lokale gateway.

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>


## <a name="add"></a>Naast elkaar bestaande verbindingen configureren voor een bestaand VNet

Als u een bestaand virtueel netwerk hebt, controleert u de grootte van het gatewaysubnet. Als het gatewaysubnet /28 of /29 is, moet u eerst de gateway van het virtuele netwerk verwijderen en het gatewaysubnet vergroten. In de stappen in dit gedeelte wordt beschreven hoe u dat doet.

Als het gatewaysubnet /27 of groter is en het virtuele netwerk is verbonden via ExpressRoute, kunt u onderstaande stappen overslaan en doorgaan met [Stap 6 - Een site-naar-site-VPN-gateway maken](#vpngw) in het vorige gedeelte. 

>[AZURE.NOTE] Als u de bestaande gateway verwijdert terwijl u bezig bent met deze configuratie, wordt de verbinding tussen uw lokale site en uw virtuele netwerk verbroken. 

1. U moet de meest recente versie van de Azure PowerShell-cmdlets installeren. Zie [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van de PowerShell-cmdlets. Houd er rekening mee dat de cmdlets die u voor deze configuratie gebruikt, mogelijk enigszins afwijken van de cmdlets waarmee u bekend bent. Zorg ervoor dat u de cmdlets gebruikt die in deze instructies worden vermeld. 

2. Verwijder de bestaande ExpressRoute- of site-naar-site-VPN-gateway. 

        Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>

3. Verwijder het gatewaysubnet.
        
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
        Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet

4. Voeg een gatewaysubnet toe van /27 of hoger.
    >[AZURE.NOTE] Als u in uw virtuele netwerk niet voldoende IP-adressen over hebt om het gatewaysubnet te vergroten, moet u meer IP-adresruimte toevoegen.

        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    Sla de VNet-configuratie op.

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

5. U hebt op dit moment een VNet zonder gateways. Als u nieuwe gateways wilt maken en uw verbindingen wilt voltooien, kunt u doorgaan met [Stap 4 - Een ExpressRoute-gateway maken](#gw). Deze stap vindt u in de voorgaande reeks stappen.

## Punt-naar-site-configuratie toevoegen aan de VPN-gateway
Volg onderstaande stappen om punt-naar-site-configuratie toe te voegen aan uw VPN-gateway in een installatie waarbij ze naast elkaar kunnen worden gebruikt.

1. Voeg een VPN-clientadresgroep toe. 

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"

2. Upload het VPN-basiscertificaat voor uw VPN-gateway naar Azure. In dit voorbeeld wordt ervan uitgegaan dat het basiscertificaat is opgeslagen op de lokale computer waarop de volgende PowerShell-cmdlets worden uitgevoerd. 

        $p2sCertFullName = "RootErVpnCoexP2S.cer"
        $p2sCertMatchName = "RootErVpnCoexP2S"
        $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
        if ($p2sCertToUpload.count -eq 1){
            write-host "cert found"
        } else {
            write-host "cert not found"
            exit
        } 
        $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

Zie [Een punt-naar-site-verbinding configureren](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) voor meer informatie over punt-naar-site-VPN.

## Volgende stappen

Voor meer informatie over ExpressRoute raadpleegt u de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md).



<!--HONumber=Jun16_HO2-->


