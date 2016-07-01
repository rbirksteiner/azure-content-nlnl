<properties
    pageTitle="Instructiehandleiding voor Node.js | Microsoft Azure"
    description="Informatie over het maken van een eenvoudige Node.js-webtoepassing en het implementeren van deze toepassing in een cloudservice van Azure."
    services="cloud-services"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.date="05/03/2016" 
    ms.author="robmcm"/>

# Een Node.js-toepassing maken en implementeren in een Azure Cloud Service

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

In deze zelfstudie kunt u zien hoe u een eenvoudige Node.js-toepassing kunt maken die wordt uitgevoerd in een Azure Cloud Service. Cloud Services vormen de bouwstenen van schaalbare cloudtoepassingen in Azure. Deze bieden de mogelijkheid om de front-end- en back-end-onderdelen van uw toepassing te scheiden en onafhankelijk van elkaar te beheren en uit te schalen.  Cloud Services bieden een robuuste toegewezen virtuele machine voor het op betrouwbare wijze hosten van elke rol.

Zie [Vergelijking van Azure Websites, Cloud Services en Virtual Machines] voor meer informatie over Cloud Services en hoe deze zich verhouden tot Azure Websites en Virtual Machines.

>[AZURE.TIP] Wilt u een eenvoudige website bouwen? Als uw scenario alleen een ongecompliceerde website-front-end omvat, kunt u overwegen [een eenvoudige web-app-functie te gebruiken]. U kunt vervolgens gemakkelijk upgraden naar een cloudservice naarmate uw web-app groeit en uw vereisten veranderen.

In deze zelfstudie maakt u een eenvoudige webtoepassing gehost binnen een webrol. U gebruikt de rekenemulator om uw toepassing lokaal te testen, en vervolgens implementeert u de toepassing met behulp van PowerShell- opdrachtregelprogramma's.

De toepassing is een eenvoudige 'Hallo wereld'-toepassing:

![Een webbrowser waarin de webpagina 'Hallo wereld' wordt weergegeven][A web browser displaying the Hello World web page]

## Vereisten

> [AZURE.NOTE] In deze zelfstudie wordt Azure PowerShell gebruikt waarvoor Windows is vereist.

- Installeer en configureer [Azure Powershell].
- Download en installeer [Azure SDK voor .NET 2.7]. Selecteer in de installatie-instellingen:
    - MicrosoftAzureAuthoringTools
    - MicrosoftAzureComputeEmulator


## Een Azure Cloud Service-project maken

Voer de volgende taken uit om een nieuw Azure Cloud Services-project te maken, samen met een Node.js-basisstructuur:

1. Voer **Windows PowerShell** als administrator uit. Zoek in het **Startmenu** of **Startscherm** naar **Windows PowerShell**.

2. [Koppel PowerShell] aan uw abonnement.

3. Voer de volgende PowerShell-cmdlet in om het project te maken:

        New-AzureServiceProject helloworld

    ![Het resultaat van de helloworld-opdracht van New-AzureService][The result of the New-AzureService helloworld command]

    De **New-AzureServiceProject**-cmdlet genereert een basisstructuur voor het publiceren van een Node.js-toepassing naar een cloudservice. Deze bevat configuratiebestanden die nodig zijn voor publicatie naar Azure. De cmdlet wijzigt ook uw werkmap naar de map voor de service.

    De cmdlet maakt de volgende bestanden:

    -   **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** en **ServiceDefinition.csdef**: Azure-specifieke bestanden die nodig zijn voor het publiceren van uw toepassing. Zie [Overzicht van het maken van een gehoste service voor Azure].

    -   **deploymentSettings.json**: lokale instellingen die worden gebruikt door de Azure PowerShell-cmdlets voor implementatie.

4.  Voer de volgende opdracht in om een nieuwe webrol toe te voegen:

        Add-AzureNodeWebRole

    ![De uitvoer van de opdracht Add-AzureNodeWebRole][The output of the Add-AzureNodeWebRole command]

    De **Add-AzureNodeWebRole**-cmdlet maakt een eenvoudige Node.js-toepassing. Ook worden de **.csfg**- en **.csdef**-bestanden aangepast met configuratie-items voor de nieuwe rol.

    > [AZURE.NOTE] Als u geen rolnaam opgeeft, wordt een standaardnaam gebruikt. U kunt een naam opgeven als de eerste parameter van de cmdlet: `Add-AzureNodeWebRole MyRole`

De Node.js-app is gedefinieerd in het bestand **server.js**, dat zich bevindt in de map voor de webrol (standaard **WebRole1**). Dit is de code:

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

Deze code is in wezen hetzelfde als het testitem 'Hallo wereld' op de [nodejs.org]-website, behalve dat het poortnummer wordt gebruikt dat is toegewezen door de cloudomgeving.

## De toepassing implementeren in Azure

    [AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

### De Azure-publicatie-instellingen downloaden

Voor het implementeren van uw toepassing naar Azure moet u eerst de publicatie-instellingen voor uw Azure-abonnement downloaden.

1.  Voer de volgende Azure PowerShell-cmdlet uit:

        Get-AzurePublishSettingsFile

    Hierbij wordt uw browser gebruikt om te navigeren naar de downloadpagina voor publicatie-instellingen. U wordt mogelijk gevraagd om aan te melden met een Microsoft-account. Als dit het geval is, gebruikt u het account dat is gekoppeld aan uw Azure-abonnement.

    Sla het gedownloade profiel op naar een bestandslocatie waar u gemakkelijk bij kunt.

2.  Voer de volgende cmdlet uit om het publicatieprofiel te importeren dat u hebt gedownload:

        Import-AzurePublishSettingsFile [path to file]


    > [AZURE.NOTE] Na het importeren van de publicatie-instellingen is het raadzaam het gedownloade .publishSettings-bestand te verwijderen, omdat dit informatie bevat die iemand toegang zou kunnen geven tot uw account.

### De toepassing publiceren

Voer de volgende opdrachten uit om de toepassing te publiceren:

    $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))   
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

- **-ServiceName** is de naam voor de implementatie. Dit moet een unieke naam zijn, anders mislukt het publicatieproces. De **Get-Date**-opdracht voegt een datum/tijd-tekenreeks toe die de naam uniek zou moeten maken.

- Met **-Location** geeft u het datacenter op waarin de toepassing wordt gehost. Gebruik de **Get-AzureLocation**- cmdlet als u een lijst van beschikbare datacenters wilt bekijken.

- Met **-Launch** opent u een browservenster en gaat u naar de gehoste service nadat de implementatie is voltooid.

Nadat de publicatie is uitgevoerd, ziet u een reactie vergelijkbaar met de volgende:

![De uitvoer van de opdracht Publish-AzureService][The output of the Publish-AzureService command]

> [AZURE.NOTE] Het kan enkele minuten duren voordat de toepassing is geïmplementeerd en beschikbaar is wanneer dit de eerste keer is dat de toepassing wordt gepubliceerd.

Zodra de implementatie is voltooid, wordt een browservenster geopend waarin naar de cloudservice wordt genavigeerd.

![Een browservenster met de Hallo wereld-pagina. De URL geeft aan dat de pagina wordt gehost in Azure.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

Uw toepassing wordt nu uitgevoerd in Azure.

De **Publish-AzureServiceProject**-cmdlet voert de volgende stappen uit:

1.  Er wordt een implementatiepakket gemaakt. Het pakket bevat alle bestanden in de toepassingsmap.

2.  Er wordt een nieuw **opslagaccount** gemaakt, als dit nog niet bestaat. Het Azure-opslagaccount wordt gebruikt voor het opslaan van het toepassingspakket tijdens de implementatie. U kunt het opslagaccount gewoon verwijderen nadat de implementatie is voltooid.

3.  Er wordt een nieuwe **cloudservice** gemaakt als deze nog niet bestaat. Een **cloudservice** is de container waarin uw toepassing wordt gehost wanneer deze naar Azure wordt geïmplementeerd. Zie [Overzicht van het maken van een gehoste service voor Azure].

4.  Het implementatiepakket wordt gepubliceerd naar Azure.


## De toepassing stoppen en verwijderen

Nadat u uw toepassing hebt geïmplementeerd, wilt u deze mogelijk uitschakelen om extra kosten te vermijden. Webrolexemplaren in Azure worden per uur van verbruikte servertijd in rekening gebracht. Er wordt servertijd verbruikt zodra de toepassing is geïmplementeerd, zelfs als de exemplaren niet worden uitgevoerd en de gestopte status hebben.

1.  In het Windows PowerShell-venster stopt u de service-implementatie die u in de vorige sectie hebt gemaakt, met de volgende cmdlet:

        Stop-AzureService

    Het kan enkele minuten duren voordat de service is gestopt. Als de service is gestopt, krijgt u een bericht waarin dit wordt aangegeven.

    ![De status van de opdracht Stop-AzureService][The status of the Stop-AzureService command]

2.  Als u de service wilt verwijderen, roept u de volgende cmdlet aan:

        Remove-AzureService

    Wanneer dit wordt gevraagd, typt u **Y** om de service te verwijderen.

    Het kan enkele minuten duren voordat de service is verwijderd. Als de service is verwijderd, krijgt u een bericht waarin dit wordt aangegeven.

    ![De status van de opdracht Remove-AzureService][The status of the Remove-AzureService command]

    > [AZURE.NOTE] Als u de service verwijdert, wordt niet het opslagaccount verwijderd dat is gemaakt toen de service voor de eerste keer werd gepubliceerd, en de kosten voor gebruikte opslag worden nog wel in rekening gebracht. Zie [Een opslagaccount verwijderen uit een Azure-abonnement] voor meer informatie over het verwijderen van een opslagaccount.

## Volgende stappen

Zie het [Node.js Developer Center] voor meer informatie.

<!-- URL List -->

[Vergelijking van Azure Websites, Cloud Services en Virtual Machines]: ../app-service-web/choose-web-site-cloud-service-vm.md
[een eenvoudige web-app-functie te gebruiken]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md">
[Azure Powershell]: ../powershell-install-configure.md
[Azure SDK voor .NET 2.7]: http://www.microsoft.com/en-us/download/details.aspx?id=48178
[Koppel PowerShell]: ../powershell-install-configure.md#how-to-connect-to-your-subscription
[nodejs.org]: http://nodejs.org/
[Een opslagaccount verwijderen uit een Azure-abonnement]: ../storage/how-to-manage-a-storage-account.md
[Overzicht van het maken van een gehoste service voor Azure]: https://azure.microsoft.com/documentation/services/cloud-services/
[Node.js Developer Center]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[Het resultaat van de helloworld-opdracht van New-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[De uitvoer van de opdracht Add-AzureNodeWebRole]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[Een webbrowser waarin de webpagina 'Hallo wereld' wordt weergegeven]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[De uitvoer van de opdracht Publish-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[De volledige statusuitvoer van de opdracht Publish-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Een browservenster met de Hallo wereld-pagina. De URL geeft aan dat de pagina wordt gehost in Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[De status van de opdracht Stop-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[De status van de opdracht Remove-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png



<!--HONumber=Jun16_HO2-->


