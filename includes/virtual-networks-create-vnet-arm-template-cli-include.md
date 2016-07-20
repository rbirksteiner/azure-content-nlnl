## De ARM-sjabloon implementeren met Azure CLI

Volg de onderstaande stappen voor het implementeren van de ARM-sjabloon die u hebt gedownload met Azure CLI.

1. Als u Azure CLI nog nooit hebt gebruikt, raadpleegt u [De Azure CLI installeren en configureren](../articles/xplat-cli-install.md) en volgt u de instructies tot het punt waar u uw Azure-account en -abonnement moet selecteren.
2. Voer de opdracht **`azure config mode`** uit om over te schakelen naar de modus Resource Manager, zoals hieronder weergegeven.

        azure config mode arm

    Dit is de verwachte uitvoer voor de bovenstaande opdracht:

        info:    New mode is arm

3. Voer indien nodig de opdracht **`azure group create`** uit om een nieuwe resourcegroep te maken, zoals hieronder weergegeven. Hier ziet u de uitvoer van de opdracht. De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters. Zie voor meer informatie over resourcegroepen [Overzicht van Azure Resource Manager](../articles/resource-group-overview.md).

        azure group create -n TestRG -l centralus

    Dit is de verwachte uitvoer voor de bovenstaande opdracht:

        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

    - **-n (of --naam)**. Naam voor de nieuwe resourcegroep. In ons scenario *TestRG*.
    - **-l (of --locatie)**. Azure-regio waar de nieuwe resourcegroep wordt gemaakt. In ons scenario *centralus*.

4. Voer de **`azure group deployment create`** cmdlet uit om de nieuwe VNet te implementeren met behulp van de sjabloon en de parameterbestanden die u hebt gedownload en hierboven zijn gewijzigd. De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters.

        azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

    Dit is de verwachte uitvoer voor de bovenstaande opdracht:

        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestVNetDeployment"
        + Registering providers
        info:    Registering provider microsoft.network
        + Waiting for deployment to complete
        data:    DeploymentName     : TestVNetDeployment
        data:    ResourceGroupName  : TestRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-08-14T21:56:11.152759Z
        data:    Mode               : Incremental
        data:    Name           Type    Value
        data:    -------------  ------  --------------
        data:    location       String  Central US
        data:    vnetName       String  TestVNet
        data:    addressPrefix  String  192.168.0.0/16
        data:    subnet1Prefix  String  192.168.1.0/24
        data:    subnet1Name    String  FrontEnd
        data:    subnet2Prefix  String  192.168.2.0/24
        data:    subnet2Name    String  BackEnd
        info:    group deployment create command OK

    - **-g (of --resourcegroep)**. Naam van de resourcegroep waar de nieuwe VNet in wordt gemaakt.
    - **-f (of--sjabloonbestand)**. Pad naar het sjabloonbestand ARM.
    - **-e (of--parametersbestand)**. Pad naar uw parametersbestand ARM.

5. Voer de opdracht **`azure network vnet show`** uit om de eigenschappen van de nieuwe vnet te bekijken, zoals hieronder weergegeven.

        azure network vnet show -g TestRG -n TestVNet

    Dit is de verwachte uitvoer voor de bovenstaande opdracht:

        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK



<!--HONumber=Jun16_HO2-->


