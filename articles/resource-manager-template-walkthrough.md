<properties
   pageTitle="Walkthrough voor Resource Manager-sjablonen | Microsoft Azure"
   description="Een stapsgewijze walkthrough voor een Resource Manager-sjabloon waarmee een basis Azure IaaS-architectuur wordt ingericht."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/04/2016"
   ms.author="navale;tomfitz"/>
   
# Walkthrough voor het Resource Manager-sjabloon

Een van de eerste vragen bij het maken van een sjabloon is: hoe ga ik aan de slag? U kunt met een leeg sjabloon beginnen en dan de basisstructuur volgen die wordt beschreven in het artikel [Sjabloon aanmaken](resource-group-authoring-templates.md#template-format). Voeg vervolgens de resources en juiste parameters en variabelen toe. Een goed alternatief is om aan de slag te gaan via de [snelstartgalerie](https://github.com/Azure/azure-quickstart-templates) en te zoeken naar scenario’s die vergelijkbaar zijn met het scenario dat u wilt maken. U kunt verschillende sjablonen samenvoegen of een bestaande sjabloon aanpassen aan uw eigen scenario. 

Hier volgt een algemene infrastructuur als voorbeeld:

* Twee virtuele machines die gebruikmaken van hetzelfde opslagaccount maken onderdeel uit van dezelfde beschikbaarheidsset en bevinden zich in hetzelfde subnet van een virtueel netwerk.
* Er is één NIC en VM-IP-adres voor elke virtuele machine.
* Een load balancer met een taakverdelingsregel op poort 80

![architectuur](./media/resource-group-overview/arm_arch.png)

In dit onderwerp worden de stappen beschreven voor het maken van een Resource Manager-sjabloon voor die infrastructuur. Het laatste sjabloon dat u maakt, is gebaseerd op een snelstartsjabloon met de naam [2 virtuele machines in een Load Balancer en taakverdelingsregels](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

Dat is echter veel om in één keer op te zetten, dus u maakt en implementeert eerst een opslagaccount. Wanneer u het maken van opslagaccounts onder de knie hebt, kunt u de andere resources toevoegen en het sjabloon opnieuw implementeren om de infrastructuur te voltooien.

>[AZURE.NOTE] U kunt alle soorten editors gebruiken bij het maken van het sjabloon. Visual Studio biedt hulpprogramma's waardoor het maken van sjablonen eenvoudiger wordt, maar u hebt Visual Studio niet nodig voor deze zelfstudie. Voor een zelfstudie over het gebruiken van Visual Studio voor het maken van een webtoepassings- en SQL Database-implementatie, ziet u [Azure-resourcegroepen maken en implementeren met Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). 

## Het Resource Manager-sjabloon maken

Het sjabloon is een JSON-bestand waarin alle resources worden beschreven die u wilt implementeren. Hierin kunt u ook de parameters definiëren die worden opgegeven tijdens de implementatie, variabelen die zijn samengesteld op basis van andere waarden en expressies en uitvoer van de implementatie. 

U begint met het meest eenvoudige sjabloon:

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

Sla dit bestand op als **azuredeploy.json** (het sjabloon kan elke naam hebben die u wilt, maar het moet wel een JSON-bestand zijn).

## Een opslagaccount maken
In het gedeelte **Resources** voegt u een object toe waarmee het opslagaccount wordt gedefinieerd, zoals hieronder wordt weergegeven. 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

U vraagt zich misschien af waar deze eigenschappen en waarden van afkomstig zijn. De eigenschappen **type**, **name**, **apiVersion**, en **location** zijn standaardelementen die beschikbaar zijn voor alle resourcetypen. U vindt in [Resources](resource-group-authoring-templates.md#resources) meer informatie over de standaardelementen. **name** wordt ingesteld op een parameterwaarde die u opgeeft tijdens de implementatie en **location** op de locatie die door de resourcegroep wordt gebruikt. In de onderstaande gedeelten wordt uitgelegd hoe u de waarden voor **type** en **apiVersion** bepaalt.

Het gedeelte **Eigenschappen** bevat alle eigenschappen die uniek zijn voor een bepaald resourcetype. De waarden die u in dit gedeelte opgeeft, komen overeen met die uit de PUT-bewerking in de REST-API voor het maken van dat resourcetype. Bij het maken van een opslagaccount moet u het **accountType** opgeven. U ziet in de [REST-API voor het maken van een opslagaccount](https://msdn.microsoft.com/library/azure/mt163564.aspx) dat het gedeelte Eigenschappen van de REST-bewerking ook de eigenschap **accountType** bevat en dat de toegestane waarden worden beschreven. In dit voorbeeld is het accounttype ingesteld op **Standard_LRS**, maar u kunt een andere waarde opgeven of gebruikers toestaan om het accounttype op te geven als parameter.

Nu gaat u terug naar het gedeelte **Parameters** en bekijkt u hoe u de naam van het opslagaccount definieert. U vindt in [Parameters](resource-group-authoring-templates.md#parameters) meer informatie over het gebruik van parameters. 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
U hebt hier een parameter van het type Tekenreeks opgegeven die de naam van het opslagaccount bevat. De waarde voor deze parameter wordt tijdens de sjabloonimplementatie opgegeven.

## Het sjabloon implementeren
Er is een volledige sjabloon voor het maken van een nieuw opslagaccount. Het sjabloon is opgeslagen in het bestand **azuredeploy.json**:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

Er zijn een aantal manieren om een sjabloon te implementeren, zoals u kunt zien in het artikel [Resources implementeren](resource-group-template-deploy.md). Als u het sjabloon wilt implementeren met behulp van Azure PowerShell, gebruikt u het volgende:

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile azuredeploy.json
```

Als u het sjabloon wilt implementeren met behulp van Azure CLI, gebruikt u het volgende:

```
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

U hebt nu een opslagaccount.

Tijdens de volgende stappen worden alle resources toegevoegd die zijn vereist om de architectuur te implementeren die aan het begin van deze zelfstudie is beschreven. U voegt deze resources toe aan dezelfde sjabloon waar u al aan hebt gewerkt.

## Beschikbaarheidsset
Wanneer u klaar bent met definiëren voor het opslagaccount, voegt u een beschikbaarheidsset toe voor de virtuele machines. In dit geval zijn er geen extra eigenschappen vereist, waardoor het definiëren redelijk eenvoudig is. Zie de [REST-API voor het maken van een beschikbaarheidsset](https://msdn.microsoft.com/library/azure/mt163607.aspx) voor het gedeelte met volledige eigenschappen als u het aantal updatedomeinen en het aantal foutdomeinen wilt opgeven.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

**name** is ingesteld op de waarde van een variabele. Voor deze sjabloon is de naam van de beschikbaarheidsset vereist op een aantal verschillende plaatsen. U kunt het sjabloon eenvoudiger onderhouden door die waarde één keer te definiëren  en deze op meerdere plaatsen te gebruiken.

De waarde die u opgeeft voor **type** bevat de resourceprovider en het resourcetype. De resourceprovider voor beschikbaarheidssets is **Microsoft.Compute** en het resourcetype is **availabilitySets**. U kunt de lijst met beschikbare resourceproviders bekijken door de volgende PowerShell-opdracht uit te voeren:

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

Als u gebruikmaakt van Azure CLI kunt u ook de volgende opdracht uitvoeren:
```
    azure provider list
```
Gezien het feit dat u in dit onderwerp werkt met opslagaccounts, virtuele machines en virtuele netwerken, gaat u aan de slag met:

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

Als u wilt zien welke resourcetypen er zijn voor een bepaalde provider, voert de volgende PowerShell-opdracht uit:

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

In Azure CLI worden met de volgende opdracht de beschikbare typen in JSON-indeling geretourneerd en opgeslagen in een bestand.

```
    azure provider show Microsoft.Compute --json > c:\temp.json
```

U ziet **availabilitySets** als een van de typen binnen **Microsoft.Compute**. De volledige naam van het type is **Microsoft.Compute/availabilitySets**. U kunt de naam van het resourcetype voor alle resources in uw sjabloon bepalen.

## Openbare IP
Geef een openbaar IP-adres op. Bekijk opnieuw de [REST-API voor openbare IP-adressen](https://msdn.microsoft.com/library/azure/mt163590.aspx) om te zien welke eigenschappen u moet instellen.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

De toewijzingsmethode is ingesteld op **Dynamisch**, maar u kunt deze instellen op de gewenste waarde, of u kunt instellen dat er een parameterwaarde moet worden geaccepteerd. U heb ingeschakeld dat gebruikers van uw sjabloon een waarde kunnen opgeven voor het domeinnaamlabel.

Nu wordt beschreven hoe u de **apiVersion** bepaalt. De waarde die u opgeeft, komt overeen met de versie van de REST-API die u wilt gebruiken voor het maken van de resource. U kunt dus de documentatie van de REST-API doornemen voor dat resourcetype. U kunt ook de volgende PowerShell-opdracht uitvoeren voor een bepaald type.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
Hiermee worden de volgende waarden geretourneerd:

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Als u de API-versies met Azure CLI wilt bekijken, voert u de opdracht **azure provider show** uit die al eerder is weergegeven.

Wanneer u een nieuw sjabloon maakt, kiest u de meest recente API-versie.

## Virtueel netwerk en subnet
Maak een virtueel netwerk met één subnet. Bekijk de [REST-API voor virtuele netwerken](https://msdn.microsoft.com/library/azure/mt163661.aspx) voor alle eigenschappen die u moet instellen.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## Load balancer
U gaat nu een extern gerichte load balancer maken. Omdat deze load balancer gebruikmaakt van het openbare IP-adres, moet u een afhankelijkheid opgeven voor het openbare IP-adres. Dit doet u in het gedeelte **dependsOn**. Dit betekent dat de load balancer pas wordt geïmplementeerd wanneer het openbare IP-adres is geïmplementeerd. Als u deze afhankelijkheid niet definieert, treedt er een fout op, omdat Resource Manager probeert de resources parallel te implementeren en de load balancer probeert in te stellen op het openbare IP-adres dat nog niet bestaat. 

U maakt ook een back-endadresgroep, een aantal binnenkomende NAT-regels voor RDP in de virtuele machines en een load-balancingregel met een TCP-controle op poort 80 in deze resourcedefinitie. Bekijk de [REST-API voor load balancers](https://msdn.microsoft.com/library/azure/mt163574.aspx) voor alle eigenschappen.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## Netwerkinterface
U maakt twee netwerkinterfaces, één voor elke virtuele machine. In plaats van twee vermeldingen te gebruiken voor de netwerkinterfaces, kunt u de [functie copyIndex()](resource-group-create-multiple.md) gebruiken om de kopieerlus te herhalen (ook wel nicLoop genoemd) en het aantal netwerkinterfaces te maken dat is opgegeven in de `numberOfInstances`-variabelen. De netwerkinterface is afhankelijk van het maken van het virtuele netwerk en de load balancer. De interface maakt gebruik van het subnet dat is gedefinieerd tijdens het maken van het virtuele netwerk en van de id van de load balancer om de bijbehorende adresgroep en de binnenkomende NAT-regels te configureren.
Bekijk de [REST-API voor netwerkinterfaces](https://msdn.microsoft.com/library/azure/mt163668.aspx) voor alle eigenschappen die u moet instellen.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## Virtuele machine
U maakt twee virtuele machines met de functie copyIndex(), net als bij het maken van de [netwerkinterfaces](#network-interface).
Het aanmaakproces voor de virtuele machine is afhankelijk van het opslagaccount, de netwerkinterface en de beschikbaarheidsset. Deze virtuele machine wordt gemaakt vanuit een Marketplace-installatiekopie, zoals gedefinieerd in de `storageProfile`-eigenschap. `imageReference` wordt gebruikt om de installatiekopie-uitgever, aanbieding, SKU en versie te definiëren. Ten slotte wordt er een diagnostisch profiel geconfigureerd om diagnostiek in te schakelen voor de virtuele machine. 

Als u op zoek bent naar de relevante eigenschappen voor een Marketplace-installatiekopie, volgt u het artikel [Installatiekopieën van virtuele Linux-machines selecteren](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md) of [Installatiekopieën van virtuele Windows-machines selecteren](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md).

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

>[AZURE.NOTE] Als er sprake is van installatiekopieën die zijn gepubliceerd door **externe leveranciers**, moet u een andere eigenschap opgeven met de naam `plan`. Een voorbeeld kunt u vinden in [dit sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) uit de snelstartgalerie. 

U bent klaar met het definiëren van de resources voor uw sjabloon.

## Parameters

In het gedeelte Parameters geeft u de waarden op die kunnen worden opgegeven bij het implementeren van het sjabloon. Geef alleen parameters op voor waarden die volgens u moeten worden gewijzigd tijdens de implementatie. U kunt een standaardwaarde opgeven voor een parameter die wordt gebruikt indien deze niet is opgegeven tijdens de implementatie. Ook kunt u de toegestane waarden opgeven, zoals weergegeven voor de **imageSKU**-parameter.

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## Variabelen

In het gedeelte Variabelen kunt u waarden opgeven die op meer dan één plaats in uw sjabloon worden gebruikt, of waarden die zijn samengesteld uit andere expressies of variabelen. Variabelen worden vaak gebruikt om de syntaxis van sjablonen te vereenvoudigen.

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

Het sjabloon is nu klaar! U kunt uw sjabloon vergelijken met het volledige sjabloon in de [snelstartgalerie](https://github.com/Azure/azure-quickstart-templates). Zoek naar het sjabloon [2 virtuele machines in een load balancer en taakverdelingsregels](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules). Mogelijk is uw sjabloon iets anders wegens het gebruik van andere versienummers. 

U kunt het sjabloon opnieuw implementeren met behulp van dezelfde opdrachten die u hebt gebruikt bij het implementeren van het opslagaccount. U hoeft het opslagaccount niet te verwijderen vóór het opnieuw implementeren, omdat Resource Manager het opnieuw maken van resources die al bestaan en die niet zijn gewijzigd, overslaat.

## Volgende stappen

- De [Azure Resource Manager Template Visualizer (ARMViz)](http://armviz.io/#/) is een uitstekend hulpprogramma voor het visualiseren van ARM-sjablonen, omdat deze mogelijk te groot worden om te begrijpen door slechts het JSON-bestand te lezen.
- Zie [Azure Resource Manager-sjablonen samenstellen](resource-group-authoring-templates.md) voor meer informatie over de structuur van een sjabloon.
- Voor meer informatie over het implementeren van een sjabloon ziet u [Een resourcegroep implementeren met een Azure Resource Manager-sjabloon](resource-group-template-deploy.md)



<!--HONumber=Jun16_HO2-->


