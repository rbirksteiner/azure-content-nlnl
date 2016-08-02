<properties
    pageTitle="Een geëxporteerde Resource Manager-sjabloon aanpassen | Microsoft Azure"
    description="Voeg parameters toe aan een geëxporteerde Azure Resource Manager-sjabloon en implementeer de sjabloon opnieuw via Azure PowerShell of Azure CLI."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/10/2016"
    ms.author="tomfitz"/>

# Een geëxporteerde Azure Resource Manager-sjabloon aanpassen

Dit artikel laat zien hoe u een geëxporteerde sjabloon wijzigt, zodat u extra waarden als parameters kunt doorgeven. Dit is gebaseerd op de stappen die worden uitgevoerd in het artikel [Resource Manager-sjabloon exporteren](resource-manager-export-template.md), maar het is niet noodzakelijk dat artikel eerst te voltooien. U vindt de vereiste sjabloon en scripts in dit artikel.

## Een geëxporteerde sjabloon bekijken

Als u klaar bent met [Resource Manager-sjabloon exporteren](resource-manager-export-template.md), opent u de sjabloon die u hebt gedownload. De sjabloon heet **template.json**. Als u Visual Studio of Visual Code hebt, kunt u een van beide gebruiken om de sjabloon te bewerken. Anders kunt u een JSON-editor of een teksteditor gebruiken.

Als u de vorige procedure niet hebt voltooid, maakt u een bestand met de naam **template.json** en voegt u de volgende inhoud van de geëxporteerde sjabloon toe aan het bestand.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
        },
        "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworks_VNET_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "default",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24"
                        }
                    }
                ]
            },
            "dependsOn": []
        },
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccounts_storagetf05092016_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "tags": {},
            "properties": {
                "accountType": "Standard_RAGRS"
            },
            "dependsOn": []
        }
    ]
}
```

De sjabloon template.json werkt goed als u hetzelfde type opslagaccount in dezelfde regio wilt maken met een virtueel netwerk dat voor elke implementatie hetzelfde adresvoorvoegsel en hetzelfde subnetvoorvoegsel gebruikt. Resource Manager biedt echter opties die u in staat stellen sjablonen met veel meer flexibiliteit te implementeren. Tijdens de implementatie wilt u bijvoorbeeld mogelijk het type opslagaccount opgeven dat moet worden gemaakt of de waarden die moeten worden gebruikt voor het adresvoorvoegsel en het subnetvoorvoegsel van het virtuele netwerk.

## De sjabloon aanpassen

In deze sectie voegt u parameters toe aan de geëxporteerde sjabloon, zodat u de sjabloon opnieuw kunt gebruiken wanneer u deze resources in andere omgevingen implementeert. Ook voegt u sommige functies toe aan de sjabloon om de kans te verminderen dat er een fout optreedt wanneer u de sjabloon implementeert. U hoeft niet langer een unieke naam voor uw opslagaccount te bedenken. In plaats daarvan maakt de sjabloon een unieke naam. U beperkt de waarden die voor het type opslagaccount kunnen worden opgegeven, tot uitsluitend geldige opties.

1. Om de waarden te kunnen gebruiken die u tijdens de implementatie misschien wilt opgeven, vervangt u de sectie **parameters** door de volgende parameterdefinities. Let op de waarden van **allowedValues** voor **storageAccount_accountType**. Als u per ongeluk een ongeldige waarde opgeeft, wordt deze fout herkend voordat de implementatie start. Merk ook op dat u alleen een voorvoegsel voor de opslagaccountnaam opgeeft en het voorvoegsel tot 11 tekens is beperkt. Door het voorvoegsel tot 11 tekens te beperken, zorgt u ervoor dat de volledige naam niet langer is dan het maximumaantal tekens voor een opslagaccount. Met behulp van het voorvoegsel kunt u een naamgevingsconventie toepassen op uw opslagaccounts. In de volgende stap ziet u hoe u een unieke naam kunt maken.

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },

2. De sectie met **variabelen** van uw sjabloon is momenteel leeg. Vervang deze sectie door de volgende code. In de sectie met **variabelen** kunt u, als de auteur van de sjabloon, waarden maken die de syntaxis voor de rest van de sjabloon vereenvoudigen. De variabele **storageAccount_name** voegt het voorvoegsel van de parameter samen tot een unieke tekenreeks die wordt gegenereerd op basis van de id van de resourcegroep.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. Als u de parameters en de variabele in de resourcedefinities wilt gebruiken, vervangt u de sectie **resources** door de volgende definities. U ziet dat er, afgezien van de waarde die aan de resource-eigenschap is toegewezen, in feite weinig is gewijzigd in de resourcedefinities. De eigenschappen zijn exact hetzelfde als de eigenschappen van de geëxporteerde sjabloon. U wijst eenvoudigweg eigenschappen toe aan de parameterwaarden in plaats van aan de vastgelegde waarden. De locatie van de resources is ingesteld voor gebruik van dezelfde locatie als de resourcegroep via de expressie **resourceGroup () .location**. Naar de variabele die u voor de opslagaccountnaam hebt gemaakt, wordt verwezen via de expressie **variabelen**.

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

## Het parameterbestand corrigeren

Het gedownloade parameterbestand komt niet langer overeen met de parameters in de sjabloon. U hoeft geen parameterbestand te gebruiken, maar dit kan het proces vereenvoudigen bij het opnieuw implementeren van een omgeving. Voor veel van de parameters gebruikt u de standaardwaarden die in de sjabloon zijn gedefinieerd, zodat uw parameterbestand maar twee waarden nodig heeft.

Vervang de inhoud van het bestand parameters.json door:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

Het bijgewerkte parameterbestand biedt alleen waarden voor parameters die geen standaardwaarde hebben. Voor de andere parameters kunt u waarden opgeven als u een waarde wilt die verschilt van de standaardwaarde.

## De sjabloon implementeren

U kunt Azure PowerShell of de Azure CLI (opdrachtregelinterface) gebruiken voor het implementeren van de aangepaste sjabloon en de parameterbestanden. Installeer zo nodig [Azure PowerShell](powershell-install-configure.md) of [Azure CLI](xplat-cli-install.md). U kunt de scripts gebruiken die u tijdens het exporteren van de oorspronkelijke sjabloon samen met de sjabloon hebt gedownload, maar u kunt ook uw eigen script schrijven om de sjabloon te implementeren.
Beide opties worden in dit artikel getoond.

2. Gebruik een van de volgende opties om de implementatie uit te voeren met behulp van uw eigen script.

     Voer voor PowerShell het volgende uit:

        # login
        Add-AzureRmAccount

        # create a new resource group
        New-AzureRmResourceGroup -Name ExportGroup -Location "West Europe"

        # deploy the template to the resource group
        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExportGroup -TemplateFile {path-to-file}\template.json -TemplateParameterFile {path-to-file}\parameters.json

     Voer voor Azure CLI het volgende uit:

        azure login

        azure group create -n ExportGroup -l "West Europe"

        azure group deployment create -f {path-to-file}\azuredeploy.json -e {path-to-file}\parameters.json -g ExportGroup -n ExampleDeployment

3. Als u de geëxporteerde sjabloon en scripts hebt gedownload, zoek dan het bestand **deploy.ps1** (voor PowerShell) of **deploy.sh**-bestand (voor Azure CLI) op.

     Voer voor PowerShell het volgende uit:

        Get-Item deploy.ps1 | Unblock-File

        .\deploy.ps1

     Voer voor Azure CLI het volgende uit:

        .\deploy.sh

## Volgende stappen

- De [Procedure voor Resource Manager-sjablonen](resource-manager-template-walkthrough.md) bouwt voort op wat u in dit artikel hebt geleerd, door een sjabloon te maken voor een gecompliceerdere oplossing. Dit helpt u om een beter inzicht te krijgen in de resources die beschikbaar zijn en in de wijze waarop u bepaalt welke waarden u moet opgeven.
- Zie [Azure PowerShell gebruiken met Azure Resource Manager](powershell-azure-resource-manager.md) voor het exporteren van een sjabloon via PowerShell.
- Zie [De Azure CLI voor Mac, Linux en Windows gebruiken met Azure Resource Manager](xplat-cli-azure-resource-manager.md) voor het exporteren van een sjabloon via Azure CLI.
- Zie [Azure Resource Manager-sjablonen samenstellen](resource-group-authoring-templates.md) voor meer informatie over de structuur van sjablonen.



<!--HONumber=Jun16_HO2-->


