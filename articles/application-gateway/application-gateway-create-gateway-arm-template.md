
<properties
   pageTitle="Een toepassingsgateway maken met Azure Resource Manager-sjablonen | Microsoft Azure"
   description="Op deze pagina staan instructies voor het maken van een toepassingsgateway met de Azure Resource Manager-sjabloon"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="joaoma"/>


# Een toepassingsgateway maken met de Azure Resource Manager-sjabloon

Azure Application Gateway is een load balancer in laag 7. De gateway biedt opties voor failovers en het routeren van HTTP-aanvragen tussen servers (on-premises en in de cloud). Application Gateway biedt de volgende functies voor de levering van toepassingen: HTTP load balancing, op cookies gebaseerde sessieaffiniteit en offloading van Secure Sockets Layer (SSL).

> [AZURE.SELECTOR]
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager-sjabloon](application-gateway-create-gateway-arm-template.md)

<BR>

U ziet hoe u een bestaande Azure Resource Manager-sjabloon kunt downloaden en wijzigen vanuit GitHub, en de sjabloon vervolgens kunt implementeren met GitHub, PowerShell en Azure CLI.

Als u de Azure Resource Manager-sjabloon rechtstreeks vanuit GitHub wilt implementeren zonder deze te wijzigen, gaat u naar Een sjabloon implementeren vanuit GitHub.


## Scenario

In dit scenario:

- Maakt u een toepassingsgateway met twee exemplaren.
- Maakt u een virtueel netwerk met de naam VirtualNetwork1 met een gereserveerd CIDR-blok van 10.0.0.0/16.
- Maakt u een subnet met de naam Appgatewaysubnet dat gebruikmaakt van 10.0.0.0/28 als CIDR-blok.
- Stelt u twee eerder geconfigureerde back-end-IP-adressen in voor de webservers die load balancing moeten verzorgen voor het verkeer. In deze voorbeeldsjabloon worden de back-end-IP-adressen 10.0.1.10 en 10.0.1.11 gebruikt.

>[AZURE.NOTE] Dit zijn de parameters voor deze sjabloon. U kunt regels, de listener en de SSL waarmee de azuredeploy.json wordt geopend, wijzigen om de sjabloon aan te passen.



![Scenario](./media/application-gateway-create-gateway-arm-template/scenario-arm.png)



## De Azure Resource Manager-sjabloon downloaden en begrijpen

U kunt de bestaande Azure Resource Manager-sjabloon downloaden om een virtueel netwerk en twee subnets vanuit GitHub te maken. Vervolgens kunt u wijzigingen aanbrengen en de sjabloon opnieuw gebruiken. Volg de onderstaande stappen om dit te doen:

1. Navigeer naar [Create Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create).
2. Klik op **azuredeploy.json** en vervolgens op **RAW**.
3. Sla het bestand op in een lokale map op uw computer.
4. Als u bekend bent met Azure Resource Manager-sjablonen, kunt u doorgaan naar stap 7.
5. Open het bestand dat u zojuist hebt opgeslagen en bekijk de inhoud onder **parameters** in regel 5. Azure Resource Manager-sjabloonparameters bieden een tijdelijke aanduiding voor waarden die kunnen worden ingevuld tijdens de implementatie.

  	| Parameter | Beschrijving |
  	|---|---|
  	| **location** | Azure-regio waarin u de toepassingsgateway maakt |
  	| **VirtualNetwork1** | Naam voor het nieuwe virtuele netwerk |
  	| **addressPrefix** | Adresruimte voor het virtuele netwerk, in CIDR-indeling |
  	| **ApplicationGatewaysubnet** | Naam voor het subnet van de toepassingsgateway |
  	| **subnetPrefix** | CIDR-blok voor het subnet van de toepassingsgateway |
  	| **skuname** | Grootte van het SKU-exemplaar |
  	| **capacity** | Aantal exemplaren |
  	| **backendaddress1** | IP-adres van de eerste webserver |
  	| **backendaddress2** | IP-adres van de tweede webserver |


>[AZURE.IMPORTANT] Azure Resource Manager-sjablonen die in GitHub worden bewaard, kunnen in de loop van de tijd veranderen. Zorg ervoor dat u de sjabloon controleert voordat u deze gebruikt.

6. Controleer de inhoud onder **Resources** en let op het volgende:

    - **type**. Het type resource dat door de sjabloon wordt gemaakt. In dit geval is het type **Microsoft.Network/applicationGateways**. Dit is een toepassingsgateway.
    - **name**. Naam voor de resource. Let op het gebruik van **[parameters('applicationGatewayName')]**. Dit geeft aan of de naam wordt geleverd als invoer door de gebruiker of door een parameterbestand tijdens de implementatie.
    - **properties**. Lijst met eigenschappen voor de resource. Deze sjabloon maakt tijdens het maken van de toepassingsgateway gebruik van het virtuele netwerk en het openbare IP-adres.

7. Navigeer naar https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/.
8. Klik op **azuredeploy-parameters.json** en vervolgens op **RAW**.
9. Sla het bestand op in een lokale map op uw computer.
10. Open het bestand dat u zojuist hebt opgeslagen en bewerk de waarden voor de parameters. Gebruik de onderstaande waarden voor het implementeren van de toepassingsgateway die in ons scenario wordt beschreven.

        {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        {
        "location" : {
        "value" : "West US"
        },
        "addressPrefix": {
        "value": "10.0.0.0/16"
        },
        "subnetPrefix": {
        "value": "10.0.0.0/24"
        },
        "skuName": {
        "value": "Standard_Small"
        },
        "capacity": {
        "value": 2
        },
        "backendIpAddress1": {
        "value": "10.0.1.10"
        },
        "backendIpAddress2": {
        "value": "10.0.1.11"
        }
        }

11. Sla het bestand op. U kunt de JSON-sjabloon en parametersjabloon testen met online JSON-validatiehulpprogramma’s zoals [JSlint.com](http://www.jslint.com/).

## De Azure Resource Manager-sjabloon implementeren met PowerShell

Als u Azure PowerShell nog niet eerder hebt gebruikt, raadpleegt u [Azure PowerShell installeren en configureren](../powershell-install-configure.md) en volgt u de instructies helemaal tot aan het einde om u aan te melden bij Azure en uw abonnement te selecteren.

### Stap 1

        Login-AzureRmAccount



### Stap 2

Controleer de abonnementen voor het account.

        get-AzureRmSubscription

U wordt gevraagd om u te verifiëren met uw referenties.<BR>

### Stap 3

Kies welk Azure-abonnement u wilt gebruiken. <BR>


        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Stap 4


Indien nodig kunt u een nieuwe resourcegroep maken met de cmdlet **New-AzureResourceGroup**. In het volgende voorbeeld maakt u een nieuwe resourcegroep met de naam AppgatewayRG op de locatie VS - oost.

     New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"
        VERBOSE: 5:38:49 PM - Created resource group 'AppgatewayRG' in location 'eastus'


        ResourceGroupName : AppgatewayRG
        Location          : eastus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                     Actions  NotActions
                     =======  ==========
                      *

        ResourceId        : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/AppgatewayRG

Voer de cmdlet **New-AzureRmResourceGroupDeployment** uit om het nieuwe virtuele netwerk te implementeren met de sjabloon en de parameterbestanden die u hebt gedownload en hierboven hebt gewijzigd.

        New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
           -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

De volgende uitvoer wordt gegenereerd door de opdrachtregel:

        DeploymentName    : testappgatewaydeployment
        ResourceGroupName : appgatewayRG
        ProvisioningState : Succeeded
        Timestamp         : 9/19/2015 1:49:41 AM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                   Name             Type                       Value
                   ===============  =========================  ==========
                   location         String                     East US
                   addressPrefix    String                     10.0.0.0/16
                   subnetPrefix     String                     10.0.0.0/24
                   skuName          String                     Standard_Small
                   capacity         Int                        2
                   backendIpAddress1  String                     10.0.1.10
                   backendIpAddress2  String                     10.0.1.11

        Outputs           :


## De Azure Resource Manager-sjabloon implementeren met de Azure CLI

Volg de onderstaande stappen voor het implementeren van de Azure Resource Manager-sjabloon die u hebt gedownload met Azure CLI.

1. Als u Azure CLI nog nooit hebt gebruikt, raadpleegt u [De Azure CLI installeren en configureren](../xplat-cli-install.md) en volgt u de instructies tot het punt waar u uw Azure-account en -abonnement moet selecteren.
2. Voer de opdracht **azure config mode** uit om over te schakelen naar de modus Resource Manager, zoals hieronder weergegeven.

        azure config mode arm

Dit is de verwachte uitvoer voor de bovenstaande opdracht:

        info:   New mode is arm

3. Voer indien nodig de opdracht **azure group create** uit om een nieuwe resourcegroep te maken, zoals hieronder weergegeven. Hier ziet u de uitvoer van de opdracht. De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters. Zie voor meer informatie over resourcegroepen [Overzicht van Azure Resource Manager](../resource-group-overview.md).

        azure group create -n appgatewayRG -l eastus

**-n (of --name)**. Naam voor de nieuwe resourcegroep. In ons scenario is dit *appgatewayRG*.

**-l (of --location)**. De Azure-regio waar de nieuwe resourcegroep wordt gemaakt. In ons scenario is dit *eastus*.

4. Voer de cmdlet **azure group deployment create** uit om het nieuwe virtuele netwerk te implementeren met behulp van de sjabloon en de parameterbestanden die u hebt gedownload en hierboven hebt gewijzigd. De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters.

        azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

Dit is de verwachte uitvoer voor de bovenstaande opdracht:

        azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestAppgatewayDeployment"
        + Waiting for deployment to complete
        data:    DeploymentName     : TestAppgatewayDeployment
        data:    ResourceGroupName  : appgatewayRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-09-21T20:50:27.5129912Z
        data:    Mode               : Incremental
        data:    Name               Type    Value
        data:    -----------------  ------  --------------
        data:    location           String  East US
        data:    addressPrefix      String  10.0.0.0/16
        data:    subnetPrefix       String  10.0.0.0/24
        data:    skuName            String  Standard_Small
        data:    capacity           Int     2
        data:    backendIpAddress1  String  10.0.1.10
        data:    backendIpAddress2  String  10.0.1.11
        info:    group deployment create command OK

**-g (of --resource-group)**. Naam van de resourcegroep waar het nieuwe virtuele netwerk in wordt gemaakt.

**-f (of --template-file)**. Pad naar uw Azure Resource Manager-sjabloonbestand

**-e (of --parameters-file)**. Pad naar uw Azure Resource Manager-parameterbestand

## De Azure Resource Manager-sjabloon implementeren met click-to-deploy

U kunt de Azure Resource Manager-sjablonen ook gebruiken via click-to-deploy. Dit is een eenvoudige manier om sjablonen te gebruiken in de Azure Portal.


### Stap 1
Ga naar [Een Application Gateway met een openbaar IP-adres maken](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/).


### Stap 2

Klik op **Implementeren in Azure**.

![Implementeren in Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### Stap 3

Vul de parameters voor de implementatiesjabloon in in de portal en klik op **OK**.

![Parameters](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### Stap 4

Selecteer **Juridische voorwaarden** en klik op **Kopen**.

### Stap 5

Klik op de blade voor aangepaste implementatie en klik op **Maken**.



## Volgende stappen

Als u SSL-offload wilt configureren, raadpleegt u [Configure an application gateway for SSL offload](application-gateway-ssl.md) (Een toepassingsgateway voor SSL-offload configureren).

Als u een toepassingsgateway wilt configureren voor gebruik met een interne load balancer, raadpleegt u [Create an application gateway with an internal load balancer (ILB)](application-gateway-ilb.md) (Een toepassingsgateway met een interne load balancer (ILB) maken).

Als u meer informatie wilt over de algemene opties voor load balancing, raadpleegt u:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=Jun16_HO2-->


