<properties
   pageTitle="Een Azure Container Service-cluster implementeren | Microsoft Azure"
   description="Een Azure Container Service-cluster implementeren met behulp van de Azure-portal, de Azure CLI of PowerShell."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Containers, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>

# Een Azure Container Service-cluster implementeren

Azure Container Service biedt een snelle implementatie van populaire open-sourceoplossingen voor containerclustering en -orchestration. Met behulp van Azure Container Service kunt u DC/OS- en Docker Swarm-clusters implementeren met Azure Resource Manager-sjablonen of Azure Portal. U implementeert deze clusters met behulp van Azure Virtual Machine-schaalsets, en de clusters profiteren van het Azure-aanbod voor netwerken en opslag. Voor toegang tot Azure Container Service hebt u een Azure-abonnement nodig. Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefabonnement](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

In dit document vindt u instructies voor het implementeren van een Azure Container Service-cluster met behulp van [Azure Portal](#creating-a-service-using-the-azure-portal), de [Azure-opdrachtregelinterface (CLI)](#creating-a-service-using-the-azure-cli) en de [Azure PowerShell-module](#creating-a-service-using-powershell).  

## Een service maken met behulp van Azure Portal

Meld u aan bij Azure Portal, selecteer Nieuw en zoek op de Azure-marktplaats naar **Azure Container Service**.

![Implementatie maken](media/acs-portal1.png)  <br /> 

Selecteer **Azure Container Service** en klik op **Maken**.

![Implementatie maken](media/acs-portal2.png)  <br /> 

Voer de volgende informatie in:

- Gebruikersnaam: dit is de naam van de gebruiker die wordt gebruikt voor een account op elk van de virtuele machines en virtuele-machineschaalsets in het Azure Container Service-cluster.
- Abonnement: selecteer een Azure-abonnement.
- Resourcegroep: selecteer een bestaande resourcegroep of creëer een nieuwe.
- Locatie: selecteer een Azure-regio voor de Azure Container Service-implementatie.
- Openbare SSH-sleutel: voeg de openbare sleutel toe die wordt gebruikt voor verificatie op basis van de virtuele machines voor Azure Container Service. Het is heel belangrijk dat deze sleutel geen regeleinden bevat, maar wel de prefix 'ssh-rsa' en de postfix 'gebruikersnaam@domein'. De sleutel moet er ongeveer als volgt uitzien: **ssh-rsa AAAAB3Nz...SNIPPEDCONTENT...UcyupgH azureuser@linuxvm**’. Zie de artikelen voor [Linux]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) en [Windows]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/) voor informatie over het maken van SSH-sleutels.

Klik op **OK** wanneer u klaar bent om door te gaan.

![Implementatie maken](media/acs-portal3.png)  <br /> 

Selecteer een Orchestration-type. De opties zijn:

- DC/OS: implementeert een DC/OS-cluster.
- Swarm: implementeert een Docker Swarm-cluster.

Klik op **OK** wanneer u klaar bent om door te gaan.

![Implementatie maken](media/acs-portal4.png)  <br /> 

Voer de volgende informatie in:

- Aantal masters: het aantal masters in het cluster.
- Aantal agents: Voor Docker Swarm dit is het oorspronkelijke aantal agents in de agentschaalset. Voor DC/OS is dit het oorspronkelijke aantal agents in een persoonlijke schaalset. Bovendien wordt een openbare schaalset gemaakt met een vooraf bepaald aantal agents. Het aantal agents in deze openbare schaalsets is afhankelijk van hoeveel masters in het cluster zijn gemaakt: 1 openbare agent voor 1 master en 2 openbare agents voor 3 of 5 masters.
- Grootte van virtuele machines van agent: de grootte van de virtuele machines van de agent.
- DNS-voorvoegsel: een wereldwijd unieke naam die wordt gebruikt voor het toevoegen van belangrijke delen van de volledig gekwalificeerde domeinnamen voor de service. 

Klik op **OK** wanneer u klaar bent om door te gaan.

![Implementatie maken](media/acs-portal5.png)  <br /> 

Klik op **OK** wanneer servicevalidatie is voltooid.

![Implementatie maken](media/acs-portal6.png)  <br /> 

Klik op **Maken** om het implementatieproces te starten.

![Implementatie maken](media/acs-portal7.png)  <br /> 

Als u ervoor gekozen hebt om de implementatie aan de Azure Portal vast te maken, is de status van de implementatie zichtbaar.

![Implementatie maken](media/acs-portal8.png)  <br /> 

Wanneer de implementatie is voltooid, is het Azure Container Service-cluster klaar voor gebruik.

## Een service maken met behulp van de Azure CLI

Wanneer u een Azure Container Service-exemplaar wilt maken met behulp van de opdrachtregel, hebt u een Azure-abonnement nodig. Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefabonnement](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). Ook moet de Azure CLI geconfigureerd en geïnstalleerd zijn.

Selecteer een van de volgende sjablonen van GitHub wanneer u een DC/OS- of Docker Swarm-cluster wilt implementeren. Houd er rekening mee deze sjablonen hetzelfde zijn, met uitzondering van de standaardselectie voor orchestrator.

* [DC/OS-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Controleer vervolgens of de Azure CLI is verbonden met een Azure-abonnement. U kunt dit doen met behulp van de volgende opdracht:

```bash
azure account show
```
Als er geen Azure-account wordt geretourneerd, gebruikt u de volgende opdracht om de CLI aan te melden bij Azure.

```bash
azure login -u user@domain.com
```

Vervolgens configureert u de Azure CLI-hulpprogramma's voor het gebruik van Azure Resource Manager.

```bash
azure config mode arm
```

Maak een Azure Resource Group- en Container Service-cluster met de volgende opdracht, waarbij:

- **RESOURCE_GROUP** de naam is van de resourcegroep die u wilt gebruiken voor deze service.
- **LOCATION** de Azure-regio is waar de resourcegroep en Azure Container Service-implementatie wordt gemaakt.
- **TEMPLATE_URI** de locatie is van het implementatiebestand. **Opmerking** Dit moet het RAW-bestand zijn, niet een verwijzing naar de GitHub-gebruikersinterface. U vindt deze URL door het bestand azuredeploy.json te selecteren in GitHub en te klikken op de knop RAW:

> Opmerking: wanneer u deze opdracht uitvoert, vraagt de shell u om implementatieparameterwaarden.
 
```bash
# sample deployment

azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### Sjabloonparameters opgeven

Voor deze versie van de opdracht moet u parameters interactief definiëren. Als u parameters wilt opgeven, zoals een tekenreeks in JSON-indeling, kunt u dit doen met behulp van de `-p`-switch. Bijvoorbeeld:

 ```bash
 # sample deployment

azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

U kunt ook parameters in JSON-indeling opgeven met behulp van de `-e`-switch:

 ```bash
 # sample deployment

azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
 ```

Wanneer u een voorbeeld wilt zien van een parameterbestand met de naam `azuredeploy.parameters.json`, zoekt u deze op met de Azure Container Service-sjablonen in GitHub.

## Een service maken met behulp van de PowerShell

U kunt ook een Azure Container Service-cluster implementeren met behulp van PowerShell. Dit document is gebaseerd op versie 1.0 van de [Azure PowerShell-module](https://azure.microsoft.com/blog/azps-1-0/).

Selecteer een van de volgende sjablonen wanneer u een DC/OS- of Docker Swarm-cluster wilt implementeren. Houd er rekening mee deze sjablonen hetzelfde zijn, met uitzondering van de standaardselectie voor orchestrator.

* [DC/OS-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Controleer voordat u een cluster maakt in uw Azure-abonnement, of uw PowerShell-sessie is aangemeld bij Azure. U kunt dit doen met de opdracht `Get-AzureRMSubscription`:

```powershell
Get-AzureRmSubscription
```

Als u zich bij Azure wilt aanmelden, gebruikt u de opdracht `Login-AzureRMAccount`:

```powershell
Login-AzureRmAccount
```

Als u naar een nieuwe resourcegroep implementeert, moet u eerst de resourcegroep maken. Voor het maken van een nieuwe resourcegroep gebruikt u de opdracht `New-AzureRmResourceGroup`, waarbij u een resourcegroep en een bestemmingsregio opgeeft:

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Nadat u een resourcegroep hebt gemaakt, kunt u uw cluster maken met de volgende opdracht. De URI van de gewenste sjabloon worden opgegeven voor de `-TemplateUri`-parameter. Wanneer u deze opdracht uitvoert, vraagt PowerShell u de implementatieparameterwaarden op ge geven.

```powershell
# sample deployment

New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```

### Sjabloonparameters opgeven

Als u bekend bent met PowerShell, weet u dat u door de beschikbare parameters voor een cmdlet kunt lopen door een minteken (-) te typen en dan op de tabtoets te drukken. Dezelfde functionaliteit werkt ook met parameters die u in de sjabloon definieert. Zodra u de sjabloonnaam gaat typen, haalt de cmdlet de sjabloon op, parseert de parameters en voegt de sjabloonparameters dynamisch aan de opdracht toe. Dit maakt het heel eenvoudig om sjabloonparameterwaarden op te geven. En als u de waarde van een vereiste parameter bent vergeten, vraagt PowerShell u om de waarde.

Hieronder vindt u de volledige opdracht, inclusief parameters. U kunt uw eigen waarden opgeven voor de namen van de resources.

```powershell
# sample deployment

New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## Volgende stappen
 
Nu u een werkend cluster hebt, gaat u naar deze documenten voor meer informatie over verbinding en beheer.
 
[Verbinding maken met een Azure Container Service-cluster](container-service-connect.md)
[Werken met Azure Container Service en DC/OS](container-service-mesos-marathon-rest.md)
[Werken met Azure Container Service en Docker Swarm](container-service-docker-swarm.md) 



<!--HONumber=Jun16_HO2-->


