<properties
   pageTitle="Een toepassingsgateway configureren voor SSL-offload met Azure Resource Manager | Microsoft Azure"
   description="Op deze pagina vindt u instructies voor het maken van een toepassingsgateway met SSL-offload met behulp van Azure Resource Manager"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="joaoma"/>

# Een toepassingsgateway configureren voor SSL-offload met Azure Resource Manager

> [AZURE.SELECTOR]
-[Azure Classic PowerShell](application-gateway-ssl.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)

 Azure Application Gateway kan zodanig worden geconfigureerd dat de Secure Sockets Layer-sessie (SSL) wordt beëindigd bij de gateway om kostbare SSL-ontsleutelingstaken te voorkomen die worden uitgevoerd in de webfarm. Met SSL-offload worden ook het instellen van de front-endserver en het beheer van de webtoepassing eenvoudiger.


## Voordat u begint

1. Installeer de nieuwste versie van de Azure PowerShell-cmdlets via het webplatforminstallatieprogramma. U kunt de nieuwste versie downloaden en installeren via het gedeelte **Windows PowerShell** op de pagina [Downloads](https://azure.microsoft.com/downloads/).
2. U gaat een virtueel netwerk en een subnet voor de toepassingsgateway maken. Zorg ervoor dat er geen virtuele machines en cloudimplementaties zijn die gebruikmaken van het subnet. De toepassingsgateway moet afzonderlijk in een subnet van een virtueel netwerk staan.
3. De servers die u voor gebruik van de toepassingsgateway configureert, moeten al bestaan in het virtuele netwerk of hier hun eindpunten hebben. Een andere optie is om er een openbaar IP- of VIP-adres aan toe te wijzen.

## Wat is er vereist om een toepassingsgateway te maken?


- **Back-endserverpool:** de lijst met IP-adressen van de back-endservers. De IP-adressen moeten ofwel deel uitmaken van het subnet van het virtuele netwerk, ofwel openbare IP-/VIP-adressen zijn.
- **Back-endserverpoolinstellingen:** elke pool heeft instellingen, zoals voor de poort, het protocol en de op cookies gebaseerde affiniteit. Deze instellingen zijn gekoppeld aan een pool en worden toegepast op alle servers in de pool.
- **Front-endpoort:** dit is de openbare poort die in de toepassingsgateway wordt geopend. Het verkeer komt binnen via deze poort en wordt vervolgens omgeleid naar een van de back-endservers.
- **Listener:** de listener beschikt over een front-endpoort, een protocol (Http of Https; deze zijn hoofdlettergevoelig) en de SSL-certificaatnaam (als u SSL-offloading configureert).
- **Regel:** de regel verbindt de listener met de back-endserverpool en definieert naar welke back-endserverpool het verkeer moet worden omgeleid wanneer dit bij een bepaalde listener aankomt. Momenteel wordt alleen de regel *basic* ondersteund. De regel *basic* is een vorm van round-robinbelastingverdeling.

**Aanvullende configuratieopmerkingen**

Voor het configureren van SSL-certificaten moet het protocol in **HttpListener** worden gewijzigd in *Https* (hoofdlettergevoelig). Het element **SslCertificate** moet worden toegevoegd aan **HttpListener**, waarbij de waarde van de variabele moet worden geconfigureerd voor het SSL-certificaat. De front-endpoort moet worden bijgewerkt naar 443.

**Op cookies gebaseerde affiniteit inschakelen**: u kunt een toepassingsgateway zodanig configureren dat u zeker weet dat aanvragen vanuit clientsessies altijd worden omgeleid naar dezelfde virtuele machine in de webfarm. Dit wordt gedaan door het injecteren van een sessiecookie waarmee de gateway het verkeer correct kan omleiden. Als u op cookies gebaseerde affiniteit wilt inschakelen, stelt u **CookieBasedAffinity** in op *Enabled* in het element **BackendHttpSettings**.


## Een nieuwe toepassingsgateway maken

Het verschil tussen het gebruik van het klassieke Azure-implementatiemodel en Azure Resource Manager zit hem in de volgorde waarin u een Application Gateway maakt en in de items die u moet configureren.

Met Resource Manager worden alle items waaruit een toepassingsgateway bestaat, afzonderlijk geconfigureerd en vervolgens samengesteld om de toepassingsgatewayresource te maken.


Dit zijn de stappen voor het maken van een toepassingsgateway:

1. Een resourcegroep maken voor Resource Manager
2. Een virtueel netwerk, subnet en openbaar IP-adres maken voor de toepassingsgateway
3. Een configuratieobject voor de toepassingsgateway maken
4. Een toepassingsgatewayresource maken


## Een resourcegroep maken voor Resource Manager

Zorg ervoor dat u overschakelt naar de PowerShell-modus om de Azure Resource Manager-cmdlets te gebruiken. Zie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Windows PowerShell gebruiken met Resource Manager) voor meer informatie.

### Stap 1

        PS C:\> Login-AzureRmAccount



### Stap 2

Controleer de abonnementen voor het account.

        PS C:\> get-AzureRmSubscription

U wordt gevraagd om u te verifiëren met uw referenties.<BR>

### Stap 3

Kies welk Azure-abonnement u wilt gebruiken. <BR>


        PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Stap 4

Maak een nieuwe resourcegroep (u kunt deze stap overslaan als u een bestaande resourcegroep gebruikt).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure Resource Manager vereist dat er voor alle resourcegroepen een locatie wordt opgegeven. Deze locatie wordt gebruikt als de standaardlocatie voor resources in die resourcegroep. Zorg ervoor dat bij alle opdrachten voor het maken van een toepassingsgateway dezelfde resourcegroep wordt gebruikt.

In het bovenstaande voorbeeld is er een resourcegroep gemaakt met de naam appgw-RG en de locatie VS - west.

## Een virtueel netwerk en een subnet maken voor de toepassingsgateway

In het volgende voorbeeld ziet u hoe u een virtueel netwerk maakt met Resource Manager:

### Stap 1

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Hiermee wijst u het adresbereik 10.0.0.0/24 toe aan de subnetvariabele die u gaat gebruiken om een virtueel netwerk te maken.

### Stap 2
    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Hiermee maakt u voor de regio VS - west een virtueel netwerk met de naam appgwvnet in de resourcegroep appgw-rg, waarbij het voorvoegsel 10.0.0.0/16 met het subnet 10.0.0.0/24 wordt gebruikt.

### Stap 3

    $subnet=$vnet.Subnets[0]

Hiermee wijst u het subnetobject toe aan de variabele $subnet voor gebruik in de volgende stappen.

## Een openbaar IP-adres maken voor de front-endconfiguratie

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Hiermee maakt u voor de regio VS - west een openbare IP-resource publicIP01 in de resourcegroep appgw-rg.


## Een configuratieobject voor de toepassingsgateway maken

### Stap 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Hiermee maakt u voor de toepassingsgateway een IP-configuratie met de naam gatewayIP01. Wanneer de toepassingsgateway wordt geopend, wordt er een IP-adres opgehaald via het geconfigureerde subnet en wordt het netwerkverkeer omgeleid naar de IP-adressen in de back-end-IP-pool. Elk exemplaar gebruikt één IP-adres.

### Stap 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Hiermee configureert u de back-end-IP-adrespool met de naam pool01. Hiervoor worden de IP-adressen 134.170.185.46, 134.170.188.221 en 134.170.185.50 gebruikt. Dit zijn de IP-adressen waardoor het netwerkverkeer van het frond-end-IP-eindpunt binnenkomt. Vervang de IP-adressen uit het bovenstaande voorbeeld door de IP-adressen van de eindpunten van uw webtoepassing.

### Stap 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Hiermee configureert u de instelling poolsetting01 van de toepassingsgateway voor het netwerkverkeer met load balancing in de back-endpool.

### Stap 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Hiermee configureert u de front-end-IP-poort frontendport01 voor het openbare IP-eindpunt.

### Stap 5

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Hiermee configureert u het certificaat dat wordt gebruikt voor de SSL-verbinding. Het certificaat moet de indeling .pfx hebben en het wachtwoord moet uit 4-12 tekens bestaan.

### Stap 6

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Hiermee maakt u een front-end-IP-configuratie met de naam fipconfig01 en koppelt u het openbare IP-adres aan de front-end-IP-configuratie.

### Stap 7

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Hiermee maakt u de listener met de naam listener01 en koppelt u de front-endpoort aan de front-end-IP-configuratie en het certificaat.

### Stap 8

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Hiermee maakt u een load balancer-routeringsregel met de naam rule01 voor het configureren van het gedrag van de load balancer.

### Stap 9

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Hiermee configureert u de exemplaargrootte van de toepassingsgateway.

>[AZURE.NOTE]  De standaardwaarde voor *InstanceCount* is 2 en de maximale waarde is 10. De standaardwaarde voor *GatewaySize* is Medium. U kunt kiezen tussen Standard_Small, Standard_Medium en Standard_Large.

## Een toepassingsgateway maken met behulp van New-AzureApplicationGateway

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Hiermee maakt u een toepassingsgateway met alle configuratie-items uit de bovenstaande stappen. In dit voorbeeld heeft de toepassingsgateway de naam appgwtest.

## Volgende stappen

Als u een toepassingsgateway wilt configureren voor gebruik met een interne load balancer, raadpleegt u [Create an application gateway with an internal load balancer (ILB)](application-gateway-ilb.md) (Een toepassingsgateway met een interne load balancer (ILB) maken).

Als u meer informatie wilt over de algemene opties voor load balancing, raadpleegt u:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=Jun16_HO2-->


