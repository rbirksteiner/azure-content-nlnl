## Een VNet maken met de Azure CLI

U kunt Azure CLI gebruiken voor het beheer van uw Azure-resources via de opdrachtprompt op elke computer met Windows, Linux of OS X. Volg de onderstaande stappen om een VNet te maken met behulp van de Azure CLI.

1. Als u de Azure CLI nog nooit hebt gebruikt, raadpleegt u [De Azure CLI installeren en configureren](../articles/xplat-cli-install.md) en volgt u de instructies tot het punt waar u uw Azure-account en -abonnement moet selecteren.
2. Voer de opdracht **azure config mode** uit om over te schakelen naar de modus Resource Manager, zoals hieronder weergegeven.

        azure config mode arm

    Dit is de verwachte uitvoer voor de bovenstaande opdracht:

        info:    New mode is arm

3. Voer indien nodig de opdracht **azure group create** uit om een nieuwe resourcegroep te maken, zoals hieronder weergegeven. Hier ziet u de uitvoer van de opdracht. De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters. Zie voor meer informatie over resourcegroepen [Overzicht van Azure Resource Manager](../articles/virtual-network/resource-group-overview.md/#resource-groups).

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
    - **-l (of --locatie)**. De Azure-regio waar de nieuwe resourcegroep wordt gemaakt. In ons scenario *centralus*.

4. Voer de opdracht **azure network vnet create** uit om een VNet en een subnet te maken, zoals hieronder weergegeven. 

        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

    Dit is de verwachte uitvoer voor de bovenstaande opdracht:

        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK

    - **-g (of --resourcegroep)**. Naam van de resourcegroep waar de VNet wordt gemaakt. In ons scenario *TestRG*.
    - **-n (of --naam)**. Naam van de VNet die moet worden gemaakt. In ons scenario *TestVNet*
    - **-a (of--adresvoorvoegsels)**. Lijst met gebruikte CIDR-blokken voor de VNet-adresruimte. In ons scenario *192.168.0.0/16*
    - **-l (of --locatie)**. De Azure-regio waar de VNet wordt gemaakt. In ons scenario *centralus*.

5. Voer de opdracht **azure network vnet subnet create** om een subnet te maken, zoals hieronder weergegeven. Hier ziet u de uitvoer van de opdracht. De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters.

        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

    Dit is de verwachte uitvoer voor de bovenstaande opdracht:

        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK

    - **-e (of --vnet naam**. Naam van de VNet waar het subnet wordt gemaakt. In ons scenario *TestVNet*.
    - **-n (of --naam)**. Naam van het nieuwe subnet. In ons scenario *FrontEnd*.
    - **-a (of --adresvoorvoegsel)**. Subnet CIDR-blok. In ons scenario *192.168.1.0/24*.

6. Herhaal stap 5 hierboven als u meer subnetten wilt maken. In ons scenario voert u de onderstaande opdracht maken om het *BackEnd*-subnet te maken.

        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Voer de opdracht **azure network vnet show** uit om de eigenschappen van de nieuwe VNet weer te geven, zoals hieronder weergegeven.

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


