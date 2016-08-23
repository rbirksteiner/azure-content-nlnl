<properties
   pageTitle="Een toepassingsgateway met een interne load balancer (ILB) maken en configureren met Azure Resource Manager | Microsoft Azure"
   description="Op deze pagina vindt u instructies voor het maken, configureren, openen en verwijderen van een Azure-toepassingsgateway met een interne load balancer (ILB) voor Azure Resource Manager"
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
   ms.date="04/05/2016"
   ms.author="joaoma"/>


# Een toepassingsgateway met een interne load balancer (ILB) maken met behulp van Azure Resource Manager

> [AZURE.SELECTOR]
- [Stappen voor de klassieke Azure Portal](application-gateway-ilb.md)
- [Stappen voor Resource Manager PowerShell](application-gateway-ilb-arm.md)

Azure Application Gateway kan worden geconfigureerd met een internetgerichte VIP of met een intern eindpunt dat geen toegang heeft tot het internet. Dit heet ook wel een ILB-eindpunt (interne load balancer). Het is een goed idee om de gateway te configureren met een ILB als u interne line-of-business-toepassingen gebruikt die geen toegang hebben tot het internet. Ook is dit handig als u services en lagen gebruikt in een toepassing met meerdere lagen die zich binnen een beveiligingsgrens bevinden, en als deze toepassing geen toegang heeft tot het internet, maar er wel round-robinbelastingverdeling, sessiepersistentie of SSL-beëindiging (Secure Sockets Layer) vereist is.

Dit artikel begeleidt u door de stappen voor het configureren van een toepassingsgateway met een ILB.

## Voordat u begint

1. Installeer de nieuwste versie van de Azure PowerShell-cmdlets via het webplatforminstallatieprogramma. U kunt de nieuwste versie downloaden en installeren via het gedeelte **Windows PowerShell** op de pagina [Downloads](https://azure.microsoft.com/downloads/).
2. U maakt een virtueel netwerk en een subnet voor de toepassingsgateway. Zorg ervoor dat er geen virtuele machines en cloudimplementaties zijn die gebruikmaken van het subnet. De toepassingsgateway moet afzonderlijk in een subnet van een virtueel netwerk staan.
3. De servers die u voor gebruik van de toepassingsgateway configureert, moeten al bestaan in het virtuele netwerk of hier hun eindpunten hebben. Een andere optie is om er een openbaar IP- of VIP-adres aan toe te wijzen.

## Wat is er vereist om een toepassingsgateway te maken?


- **Back-endserverpool:** de lijst met IP-adressen van de back-endservers. De IP-adressen moeten ofwel deel uitmaken van het virtueel netwerk, maar zich bevinden in een ander subnet voor de toepassingsgateway, ofwel openbare IP-/VIP-adressen zijn.
- **Back-endserverpoolinstellingen:** elke pool heeft instellingen, zoals voor de poort, het protocol en de op cookies gebaseerde affiniteit. Deze instellingen zijn gekoppeld aan een pool en worden toegepast op alle servers in de pool.
- **Front-endpoort:** dit is de openbare poort die in de toepassingsgateway wordt geopend. Het verkeer komt binnen via deze poort en wordt vervolgens omgeleid naar een van de back-endservers.
- **Listener:** de listener beschikt over een front-endpoort, een protocol (Http of Https; deze zijn hoofdlettergevoelig) en de SSL-certificaatnaam (als u SSL-offloading configureert).
- **Regel:** de regel verbindt de listener met de back-endserverpool en definieert naar welke back-endserverpool het verkeer moet worden omgeleid wanneer dit bij een bepaalde listener aankomt. Momenteel wordt alleen de regel *basic* ondersteund. De regel *basic* is een vorm van round-robinbelastingverdeling.



## Een nieuwe toepassingsgateway maken

Het verschil tussen het gebruik van Azure Classic en Azure Resource Manager zit hem in de volgorde waarin u de toepassingsgateway maakt en in de items die u moet configureren.
Met Resource Manager worden alle items waaruit een toepassingsgateway bestaat, afzonderlijk geconfigureerd en vervolgens samengesteld om de toepassingsgatewayresource te maken.


Dit zijn de stappen voor het maken van een toepassingsgateway:

1. Een resourcegroep maken voor Resource Manager
2. Een virtueel netwerk en een subnet maken voor de toepassingsgateway
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

In het bovenstaande voorbeeld is er een resourcegroep gemaakt met de naam appgw-rg en de locatie VS - west.

## Een virtueel netwerk en een subnet maken voor de toepassingsgateway

In het volgende voorbeeld ziet u hoe u een virtueel netwerk maakt met Resource Manager:

### Stap 1

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Hiermee wijst u het adresbereik 10.0.0.0/24 toe aan de subnetvariabele die u gaat gebruiken om een virtueel netwerk te maken.

### Stap 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Hiermee maakt u voor de regio VS - west een virtueel netwerk met de naam appgwvnet in de resourcegroep appgw-rg, waarbij het voorvoegsel 10.0.0.0/16 met het subnet 10.0.0.0/24 wordt gebruikt.

### Stap 3

    $subnet=$vnet.subnets[0]

Hiermee wijst u het subnetobject toe aan de variabele $subnet voor gebruik in de volgende stappen.

## Een configuratieobject voor de toepassingsgateway maken

### Stap 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Hiermee maakt u voor de toepassingsgateway een IP-configuratie met de naam gatewayIP01. Wanneer de toepassingsgateway wordt geopend, wordt er een IP-adres opgehaald via het geconfigureerde subnet en wordt het netwerkverkeer omgeleid naar de IP-adressen in de back-end-IP-pool. Elk exemplaar gebruikt één IP-adres.


### Stap 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Hiermee configureert u de back-end-IP-adrespool met de naam pool01. Gebruik hiervoor de IP-adressen 134.170.185.46, 134.170.188.221 en 134.170.185.50. Dit zijn de IP-adressen waardoor het netwerkverkeer van het frond-end-IP-eindpunt binnenkomt. U gaat de bovenstaande IP-adressen vervangen om uw eigen toepassings-IP-adreseindpunten toe te voegen.

### Stap 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Hiermee configureert u voor de toepassingsgateway de instelling poolsetting01 voor het netwerkverkeer met load balancing in de back-endpool.

### Stap 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Hiermee configureert u de front-end-IP-poort frontendport01 voor de ILB.

### Stap 5

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Hiermee maakt u de front-end-IP-configuratie met de naam fipconfig01 en koppelt u deze aan een eigen IP-adres van het huidige subnet van een virtueel netwerk.

### Stap 6

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Hiermee maakt u een listener met de naam listener01 en koppelt u de front-endpoort aan de front-end-IP-configuratie.

### Stap 7

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Hiermee maakt u een load balancer-routeringsregel met de naam rule01 voor het configureren van het gedrag van de load balancer.

### Stap 8

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Hiermee configureert u de exemplaargrootte van de toepassingsgateway.

>[AZURE.NOTE]  De standaardwaarde voor *InstanceCount* is 2 en de maximale waarde is 10. De standaardwaarde voor *GatewaySize* is Medium. U kunt kiezen tussen Standard_Small, Standard_Medium en Standard_Large.

## Een toepassingsgateway maken met behulp van New-AzureApplicationGateway

Hiermee maakt u een toepassingsgateway met alle configuratie-items uit de bovenstaande stappen. In dit voorbeeld heeft de toepassingsgateway de naam appgwtest.


    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Hiermee maakt u een toepassingsgateway met alle configuratie-items uit de bovenstaande stappen. In dit voorbeeld heeft de toepassingsgateway de naam appgwtest.


## Een toepassingsgateway verwijderen

Als u een toepassingsgateway wilt verwijderen, moet u de volgende stappen in deze volgorde doorlopen:

1. Gebruik de cmdlet **Stop-AzureRmApplicationGateway** om de gateway te stoppen.
2. Gebruik de cmdlet **Remove-AzureRmApplicationGateway** om de gateway te verwijderen.
3. Gebruik de cmdlet **Get-AzureApplicationGateway** om te controleren of de gateway is verwijderd.


### Stap 1

Haal het toepassingsgatewayobject op en koppel dit aan de variabele $getgw.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Stap 2

Gebruik **Stop-AzureRmApplicationGateway** om de toepassingsgateway te stoppen. In dit voorbeeld wordt de cmdlet **Stop-AzureRmApplicationGateway** op de eerste regel weergegeven, gevolgd door de uitvoer.

    PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Nadat de toepassingsgateway is gestopt, gebruikt u de cmdlet **Remove-AzureRmApplicationGateway** om de service te verwijderen.


    PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] U kunt de switch **-force** gebruiken om het bevestigingsbericht voor de verwijdering niet te laten weergeven.


Als u wilt controleren of de service is verwijderd, gebruikt u de cmdlet **Get-AzureRmApplicationGateway**. Deze stap is niet vereist.


    PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## Volgende stappen

Als u SSL-offload wilt configureren, raadpleegt u [Configure an application gateway for SSL offload](application-gateway-ssl.md) (Een toepassingsgateway voor SSL-offload configureren).

Als u een Application Gateway wilt configureren voor gebruik met een ILB, raadpleegt u [Een Application Gateway met een interne Load Balancer (ILB) maken](application-gateway-ilb.md).

Als u meer informatie wilt over de algemene opties voor load balancing, raadpleegt u:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=Jun16_HO2-->


