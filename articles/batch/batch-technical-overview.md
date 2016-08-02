<properties
    pageTitle="Basisbeginselen van Azure Batch-service | Microsoft Azure"
    description="Meer informatie over het gebruik van de Azure Batch-service voor grootschalige parallelle en HPC-workloads"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/02/2016"
    ms.author="marsma"/>

# Basisbeginselen van Azure Batch

In Azure Batch kunt u grootschalige parallelle en High Performance Computing (HPC)-toepassingen efficiënt in de cloud uitvoeren. Dit is een platformservice die de uitvoering van rekenintensief werk op een beheerde verzameling van virtuele machines plant en automatisch het aantal rekenresources kan aanpassen aan de behoeften van uw jobs.

Met de Batch-service definieert u Azure-rekenresources om uw toepassingen parallel en op de juiste schaal uit te voeren. U kunt taken op aanvraag uitvoeren of geplande taken uitvoeren en u hoeft niet handmatig een HPC-cluster, individuele virtuele machines, virtuele netwerken of een complexe job- en taakplanningsinfrastructuur te maken, configureren en beheren.

## Gebruiksvoorbeelden voor Batch

Batch is een beheerde Azure-service die wordt gebruikt voor *batchverwerking* of *batchberekeningen*, waarbij een groot aantal vergelijkbare taken wordt uitgevoerd om een bepaald gewenst resultaat te verkrijgen. Batchverwerking wordt doorgaans gebruikt door organisaties die regelmatig grote hoeveelheden gegevens verwerken, transformeren en analyseren.

Batch is bijzonder geschikt voor intrinsiek parallelle (ook wel bekend als 'perfect parallelle') toepassingen en workloads. Intrinsiek parallelle workloads worden eenvoudig onderverdeeld in meerdere taken die op meerdere computers tegelijk werk verrichten.

![Parallelle taken][1]<br/>

Dit zijn enkele voorbeelden van workloads die vaak met deze techniek worden verwerkt:

* Modellering van financiële risico's
* Hydrologische en klimaatgegevensanalyse
* Rendering, analyse en verwerking van beelden
* Mediacodering en -transcodering
* Genetische sequentieanalyse
* Technische spanningsanalyse
* Softwaretests

Batch kan ook parallelle berekeningen met aan het einde een verkleiningsstap, evenals complexere HPC-workloads, uitvoeren zoals [Message Passing Interface (MPI)](batch-mpi.md)-toepassingen.

Zie [Batch and HPC solutions](batch-hpc-solutions.md) (Batch- en HPC-oplossingen) voor een vergelijking tussen Batch en andere opties voor HPC-oplossingen in Azure.

## Ontwikkelen met Batch

Wanneer u oplossingen bouwt die gebruikmaken van Azure Batch voor de verwerking van parallelle workloads, doet u dit programmatisch met behulp van de Batch-API's. Met de Batch-API's maakt en beheert u pools van rekenknooppunten (virtuele machines) en plant u jobs en taken voor uitvoering op die knooppunten. Een clienttoepassing of -service die u schrijft, gebruikt de Batch-API's om met de Batch-service te communiceren. U kunt voor uw organisatie efficiënt grootschalige workloads verwerken of uw klanten een front-endservice aanbieden, zodat zij (op aanvraag of gepland) jobs en taken kunnen uitvoeren op één knooppunt of honderden of duizenden knooppunten. U kunt Batch ook gebruiken als onderdeel van een grotere werkstroom, beheerd door hulpprogramma's zoals [Azure Data Factory][data_factory].

> [AZURE.TIP] Wanneer u gereed bent om met de Batch-API aan de slag te gaan en u te verdiepen in de mogelijkheden ervan, leest u het [Overzicht van de Azure Batch-functies](batch-api-basics.md).

### Azure-accounts die u nodig hebt

Wanneer u Batch-oplossingen ontwikkelt, gebruikt u de volgende accounts in Microsoft Azure.

- **Azure-account en -abonnement** - Als u nog geen Azure-abonnement hebt, kunt u uw [voordelen als MSDN-abonnee][msdn_benefits] activeren of u aanmelden voor een [gratis Azure-account][free_account]. Wanneer u een account maakt, wordt voor u een standaardabonnement gemaakt.

- **Batch-account** - Wanneer uw toepassingen met de Batch-service communiceren, worden de accountnaam, de URL van het account en een toegangssleutel als referenties gebruikt. Al uw Batch-resources zoals pools, rekenknooppunten, jobs en taken worden aan een Batch-account gekoppeld. U kunt [een Batch-account maken en beheren](batch-account-create-portal.md) in Azure Portal.

- **Storage-account** - Batch bevat ingebouwde ondersteuning voor het werken met bestanden in [Azure Storage][azure_storage]. Vrijwel elk Batch-scenario gebruikt Azure Storage; voor het faseren van de programma's die door de taken worden uitgevoerd en de gegevens die zij verwerken, en voor de opslag van uitvoergegevens die zij genereren. Zie [Over Azure-opslagaccounts](./../storage/storage-create-storage-account.md) voor het maken van een opslagaccount.

### Batch-ontwikkelings-API's

De toepassingen en services kunnen rechtstreekse REST API-aanroepen uitgeven, een of meer van de volgende clientbibliotheken gebruiken of een combinatie van beide voor het beheren van rekenresources en het uitvoeren van parallelle workloads op de geschikte schaal met behulp van de Batch-service.

| API    | API-verwijzing | Download | Codevoorbeelden |
| ----------------- | ------------- | -------- | ------------ |
| **Batch REST** | [MSDN][batch_rest] | N.v.t. | [MSDN][batch_rest] |
| **Batch .NET**    | [MSDN][api_net] | [NuGet ][api_net_nuget] | [GitHub][api_sample_net] |
| **Batch Python**  | [readthedocs.io][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Batch Node.js** | [github.io][api_nodejs] | [npm][api_nodejs_npm] | - |
| **Batch Java** (in voorbereiding) | [github.io][api_java] | [Opslagplaats met Maven-momentopnamen][api_java_jar] | - |

### Batch-resourcebeheer

Naast de client-API's kunt u ook het volgende gebruiken om resources in uw Batch-account te beheren.

- [PowerShell-cmdlets voor Batch][batch_ps]: met de Azure Batch-cmdlets in de [Azure PowerShell](../powershell-install-configure.md)-module kunt u Batch-resources beheren met PowerShell.

- [Azure CLI](../xplat-cli-install.md): de Azure Command-Line Interface (Azure CLI) is een platformoverschrijdende hulpmiddelenset die shellopdrachten biedt voor interactie met vele Azure-services, waaronder Batch.

- [Batch Management .NET](batch-management-dotnet.md)-clientbibliotheek: Ook beschikbaar via [NuGet][api_net_mgmt_nuget]. U kunt de Batch Management .NET-clientbibliotheek gebruiken om Batch-accounts, -quota en -toepassingspakketten programmatisch te beheren. Verwijzing voor de beheerbibliotheek bevindt zich op [MSDN][api_net_mgmt].

### Batch-hulpprogramma's

Hoewel deze hulpprogramma's niet zijn vereist voor het bouwen van oplossingen met behulp van Batch, kunnen ze als essentieel worden beschouwd bij de ontwikkeling en foutopsporing van uw Batch-toepassingen en -services.

- [Azure Batch Explorer][batch_explorer]: Batch Explorer is een van de Batch .NET-voorbeeldtoepassingen die beschikbaar zijn in [GitHub][github_samples]. Bouw deze Windows Presentation Foundation (WPF)-toepassing met Visual Studio 2013 of 2015 en gebruik deze om door de resources in uw Batch-account te bladeren en ze te beheren bij de ontwikkeling en foutopsporing van uw Batch-oplossingen. Bekijk job-, pool- en taakdetails, download bestanden van rekenknooppunten of maak zelfs op afstand verbinding met knooppunten met behulp van  Remote Desktop (RDP)-bestanden die u verkrijgt met enkele muisklikken in de Batch Explorer-interface.

- [Microsoft Azure Storage Explorer][storage_explorer]: hoewel dit strikt genomen geen Azure Batch-hulpprogramma is, is Storage Explorer een ander waardevol hulpmiddel voor gebruik bij de ontwikkeling en foutopsporing van uw Batch-oplossingen.

## Scenario: een parallelle workload uitschalen

Een gangbare oplossing die gebruikmaakt van de Batch-API's om met de Batch-service te communiceren, bestaat erin intrinsiek parallel werk (zoals de rendering van beelden voor 3D-scènes) uit te schalen op een pool van rekenknooppunten. Deze pool van rekenknooppunten kan uw 'render farm' zijn die bijvoorbeeld tientallen, honderden of zelfs duizenden kernen aan uw renderingjob beschikbaar stelt.

Het volgende diagram toont een algemene Batch-werkstroom, met een clienttoepassing of gehoste service die gebruikmaakt van Batch om een parallelle workload uit te voeren.

![Werkstroom van Batch-oplossing][2]

In dit veelvoorkomende scenario verwerkt uw toepassing of service een rekenworkload in Azure Batch door de volgende stappen uit te voeren:

1. Upload de **invoerbestanden** en de **toepassing** die deze bestanden zal verwerken naar uw Azure-opslagaccount. De invoerbestanden kunnen alle gegevens zijn die uw toepassing zal verwerken, zoals de modellering van financiële gegevens of te transcoderen videobestanden. De toepassingsbestanden kunnen elke toepassing zijn die wordt gebruikt voor het verwerken van de gegevens, zoals een 3D-renderingtoepassing of mediatranscoder.

2. Maak een Batch-**pool** van rekenknooppunten in uw Batch-account. Dit zijn de virtuele machines die uw taken zullen uitvoeren. U geeft eigenschappen op, zoals de [knooppuntgrootte](./../cloud-services/cloud-services-sizes-specs.md), het bijbehorende besturingssysteem en de locatie in Azure Storage van de toepassing die moet worden geïnstalleerd wanneer de knooppunten aan de pool worden toegevoegd (de toepassing die u in stap 1 hebt geüpload). U kunt ook de pool zodanig configureren, dat deze [automatisch wordt geschaald vergroot/verkleind](batch-automatic-scaling.md) (waarbij het aantal rekenknooppunten in de pool dynamisch wordt aangepast) als reactie op de workload die door uw taken wordt gegenereerd.

3. Maak een Batch-**job** om de workload uit te voeren op de pool van rekenknooppunten. Wanneer u een taak maakt, koppelt u deze aan een Batch-pool.

4. Voeg **taken** toe aan de job. Wanneer u taken aan een taak toevoegt, plant de Batch-service automatisch de taken voor uitvoering op de rekenknooppunten in de pool. Elke taak maakt gebruik van de toepassing die u hebt geüpload om de invoerbestanden te verwerken.

    - 4a. Voordat een taak wordt uitgevoerd, kan deze de gegevens (de invoerbestanden) die deze moet verwerken, downloaden naar het rekenknooppunt waaraan de taak is toegewezen. Als de toepassing nog niet op het knooppunt is geïnstalleerd (zie stap 2), kan deze in plaats daarvan hier worden gedownload. Nadat de downloads zijn voltooid, worden de taken uitgevoerd op hun toegewezen knooppunten.

5. Wanneer de taken worden uitgevoerd, kunt u in Batch een query uitvoeren om de voortgang van de job en de bijbehorende taken te controleren. Uw clienttoepassing of service communiceert met de Batch-service via HTTPS. Omdat u mogelijk duizenden taken controleert die op duizenden rekenknooppunten worden uitgevoerd, moet u [in de Batch-service een efficiënte query uitvoeren](batch-efficient-list-queries.md).

6. Nadat de taken zijn voltooid, kunnen ze hun resultaatgegevens uploaden naar Azure Storage. U kunt ook bestanden rechtstreeks uit rekenknooppunten ophalen.

7. Wanneer u bij uw controle detecteert dat de taken in uw job zijn voltooid, kan uw clienttoepassing of service de uitvoergegevens downloaden voor verdere verwerking of evaluatie.

Houd er rekening mee dat dit slechts één manier is om Batch te gebruiken en dat in dit scenario slechts enkele van de beschikbare functies worden beschreven. U kunt bijvoorbeeld [meerdere taken parallel](batch-parallel-node-tasks.md) uitvoeren op elk rekenknooppunt en u kunt [jobvoorbereidingstaken en jobvrijgevingstaken](batch-job-prep-release.md) gebruiken om de knooppunten voor te bereiden op uw taken, en ze achteraf op te schonen.

## Volgende stappen

Nu u een Batch-voorbeeldscenario hebt gezien, is het tijd om u te verdiepen in de service en deze te leren gebruiken voor het verwerken van uw rekenintensieve parallelle workloads.

- Lees [Aan de slag met de Azure Batch-bibliotheek voor .NET](batch-dotnet-get-started.md) voor informatie over het gebruik van C# en de Batch .NET-bibliotheek voor het uitvoeren van de technieken die hierboven worden beschreven. Als u de Batch-service wilt leren gebruiken, is dit een van de eerste artikelen die u zeker moet lezen.

- Bekijk [Overzicht van Azure Batch-functies](batch-api-basics.md) voor meer gedetailleerde informatie over de API-functies van Batch voor de verwerking van uw rekenintensieve workloads.

- Naast Batch Explorer tonen de andere [codevoorbeelden op GitHub][github_samples] u hoe u vele van de Batch-functies gebruikt met behulp van de Batch .NET-bibliotheek.

- Bekijk het [leertraject van Batch][learning_path] voor een beter beeld van de resources die beschikbaar zijn als u met Batch leert werken.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://adxsnapshots.azurewebsites.net/?dir=com%5cmicrosoft%5cazure%5cazure-batch
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[data_factory]: https://azure.microsoft.com/documentation/services/data-factory/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png



<!--HONumber=Jun16_HO2-->


