<properties
    pageTitle="Azure Machine Learning Veelgestelde vragen | Microsoft Azure"
    description="Inleiding Azure Machine Learning: veelgestelde vragen over facturering en de mogelijkheden en beperkingen van een cloudservice voor gestroomlijnde voorspellende modellen."
    keywords="machine learning introduction,predictive modeling,what is machine learning"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/18/2016"
    ms.author="garye"/>

# Veelgestelde vragen over Azure Machine Learning: facturering, mogelijkheden, beperkingen en ondersteuning

Deze Veelgestelde vragen geven antwoord op vragen over Azure Machine Learning, een cloudservice voor het ontwikkelen van voorspellende modellen en operationele oplossingen via webservices. In deze Veelgestelde vragen worden vragen behandeld over het gebruik van de service, zoals het factureringsmodel, de mogelijkheden, beperkingen en ondersteuning.

## Algemene vragen

**Wat is Azure Machine Learning?**

Azure Machine Learning is een volledig beheerde service waarmee u voorspellende analytische oplossingen in de cloud kunt maken, testen, gebruiken en beheren. Via een browser kunt u zich eenvoudig aanmelden, gegevens uploaden en direct aan de slag gaan met Machine Learning. U kunt voorspellende modellen, een groot palet modules en een bibliotheek van sjablonen slepen en neerzetten, zodat u algemene taken snel en eenvoudig kunt uitvoeren.  Zie het [service-overzicht van Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) voor meer informatie. Zie [Inleiding op Azure Machine Learning](machine-learning-what-is-machine-learning.md) voor een introductie, waarin de belangrijkste termen en begrippen worden behandeld.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Wat is Machine Learning Studio?**

Machine Learning Studio is een workbenchomgeving in een webbrowser. Machine Learning Studio biedt een palet modules met een visuele compositie-interface waarmee u een end-to-end, wetenschappelijke werkstroom in de vorm van een experiment kunt maken.

Zie [Wat is Machine Learning Studio?](machine-learning-what-is-ml-studio.md) voor meer informatie over Machine Learning Studio.

**Wat is de Machine Learning API-service?**

Met de Machine Learning API-service kunt u voorspellende modellen implementeren als schaalbare en fouttolerante webservices, zoals de modellen die zijn gemaakt in Machine Learning Studio. De webservices van de Machine Learning API-service zijn REST-API's die een interface bieden voor de communicatie tussen externe toepassingen en uw predictive analytics-modellen.

Zie [Verbinding maken met een Machine Learning-webservice](machine-learning-connect-to-azure-machine-learning-web-service.md) voor meer informatie.


## Vragen over facturering

**Hoe werkt de facturering in Machine Learning?**

Zie [Machine Learning-prijzen](https://azure.microsoft.com/pricing/details/machine-learning/) voor informatie over de facturering en prijzen.

**Is er een gratis proefversie van Machine Learning?**

 Als u zich registreert voor een gratis proefversie van Azure, kunt u alle Azure-services een maand proberen. Ga naar [Veelgestelde vragen over de gratis proefversie van Azure](/pricing/free-trial-faq/) voor meer informatie over de gratis proefversie van Azure.

## Vragen over Machine Learning Studio

### Een experiment maken

**Is er versiebeheer of Git-integratie voor experimentele grafieken?**

Nee, maar Machine Learning Studio behoudt elke herhaling van een experiment die niet door andere gebruikers kunnen worden gewijzigd.
Zie [Experimentherhalingen in Machine Learning Studio beheren](machine-learning-manage-experiment-iterations.md) voor meer informatie.

### Gegevens importeren en exporteren voor Machine Learning

**Welke gegevensbronnen worden door Machine Learning ondersteund?**

Gegevens kunnen op drie manieren in een Machine Learning Studio-experiment worden geladen: door het uploaden van een lokaal bestand als een gegevensset, door via een module gegevens te importeren uit cloudgegevensservices of door het importeren van een gegevensset die is opgeslagen in een ander experiment. Zie [Trainingsgegevens importeren in Machine Learning Studio](machine-learning-data-science-import-data.md) voor meer informatie over ondersteunde bestandsindelingen.


#### <a id="ModuleLimit"></a>Hoe groot mag de gegevensset zijn voor mijn modules?

Modules in Machine Learning Studio ondersteunen gegevenssets tot 10 GB aan compacte numerieke gegevens voor algemeen gebruik. Als een module meer dan één invoer heeft, is 10 GB de totale invoergrootte. U kunt ook een steekproef nemen uit grotere gegevenssets via Hive of Azure SQL Database-query's, of gegevens van Learning by Counts vooraf verwerken voordat u deze opneemt.  

U kunt de volgende typen gegevens in grotere gegevenssets opnemen tijdens het normaliseren van kenmerken, tot maximaal 10 GB:

- Verspreide gegevens
- Categorische gegevens
- Tekenreeksen
- Binaire gegevens

De volgende modules zijn beperkt tot gegevenssets die kleiner zijn dan 10 GB:

- Aanbevelingsmodules
- SMOTE-modules
- Scriptmodules: R, Python, SQL
- Modules waarbij de grootte van de uitvoer groter is dan invoergegevens, zoals Join- of hash-functies.
- Kruisvalidatie, Tune Model Hyperparameters, ordinale regressie en One-vs-All-multiklasse, wanneer het aantal herhalingen groot is.

Voor gegevenssets die groter zijn dan een paar GB moet u de gegevens uploaden naar Azure Storage of Azure SQL Database, of gebruikmaken van HDInsight, in plaats van de gegevens direct vanuit het lokale bestand te uploaden.


####<a id="UploadLimit"></a>Wat zijn de limieten voor het uploaden van gegevens?
Voor gegevenssets die groter zijn dan een paar GB moet u de gegevens uploaden naar Azure Storage of Azure SQL Database, of gebruikmaken van HDInsight, in plaats van de gegevens direct vanuit het lokale bestand te uploaden.

**Kan ik gegevens vanaf Amazon S3 lezen?**

Als u een kleine hoeveelheid gegevens hebt en deze via een HTTP-URL beschikbaar wilt stellen, kunt u de module [Gegevens importeren][import-data] gebruiken. Als u een grotere hoeveelheid gegevens hebt, brengt u deze eerst over naar Azure Storage en gebruikt u vervolgens de module [Gegevens importeren][import-data] om deze in uw experiment te zetten.
<!--
<SEE CLOUD DS PROCESS>
-->

**Is er een ingebouwde beeldregistratiefunctie?**

U vindt meer informatie over de ingebouwde beeldregistratiefunctie in het gedeelte [Afbeeldingen importeren][image-reader].

### Modules

**Het algoritme, de gegevensbron, de gegevensindeling of de gegevenstransformatiebewerking die ik zoek, staat niet in Azure Machine Learning Studio. Wat kan ik doen?**

U kunt het [forum met feedback van gebruikers](http://go.microsoft.com/fwlink/?LinkId=404231) raadplegen om de functieaanvragen te zien die we volgen. Voeg uw stem toe aan een aanvraag als u een mogelijkheid zoekt die al eerder is aangevraagd. Als de mogelijkheid die u zoekt niet bestaat, maakt u een nieuwe aanvraag. U kunt de status van uw aanvraag in dit forum bekijken. We houden deze lijst nauwkeurig bij en werken de status van de beschikbaarheid van functies regelmatig bij. Bovendien kunt u met de ingebouwde ondersteuning voor R en Python aangepaste transformaties maken.


**Kan ik mijn bestaande code naar Machine Learning Studio overbrengen?**

Ja, u kunt uw bestaande R- of Python-code naar Machine Learning Studio overbrengen en uitvoeren in het hetzelfde experiment met andere Azure Machine Learning-gebruikers en de oplossing vervolgens als een webservice via Azure Machine Learning implementeren. Zie [Uw experiment uitbreiden met R](machine-learning-extend-your-experiment-with-r.md) en [Python machine learning-scripts in Azure Machine Learning Studio uitvoeren](machine-learning-execute-python-scripts.md) voor meer informatie.

**Kan ik zoiets als [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) gebruiken voor het definiëren van een model?**

Nee, dit wordt helaas niet ondersteund. U kunt aangepaste R- en Python-code echter wel gebruiken om een module te definiëren.

**Hoeveel modules kan ik parallel uitvoeren in mijn experiment?**  

U kunt maximaal vier modules parallel in een experiment uitvoeren.


### Gegevensverwerking

**Kan ik gegevens in het experiment interactief visualiseren (anders dan een R-visualisatie)?**

U kunt de gegevens visualiseren en statistieken opvragen door te klikken op de uitvoer van een module.

**Als ik de resultaten of gegevens in de browser weergeef, is het aantal rijen en kolommen beperkt. Waarom is dat?**

Aangezien de gegevens naar de browser worden verzonden en mogelijk omvangrijk zijn, is de gegevensgrootte beperkt om te voorkomen dat de Machine Learning Studio wordt vertraagd. Om alle gegevens of het resultaat te visualiseren, kunt u de gegevens beter downloaden en Excel of een ander hulpprogramma gebruiken.

### Algoritmen

**Welke bestaande algoritmen worden ondersteund in Machine Learning Studio?**

Machine Learning Studio biedt geavanceerde algoritmen, zoals schaalbare beslissingsstructuren, Bayesiaanse aanbevelingssystemen, Deep Neural Networks en Decision Jungles die zijn ontwikkeld door Microsoft Research. Ook bevat het schaalbare open-source machine learning-pakketten, zoals Vowpal Wabbit. Machine Learning Studio ondersteunt machine learning-algoritmen voor multiklassen en binaire classificatie, regressie en clusters. Bekijk de volledige lijst van [Machine Learning-modules][machine-learning-modules].

**Wordt automatisch het juiste Machine Learning-algoritme voor mijn gegevens voorgesteld?**

Nee, maar er zijn een aantal verschillende manieren in Machine Learning Studio om de resultaten van elk algoritme te vergelijken om te bepalen welke de juiste is voor u.

**Zijn er richtlijnen voor het kiezen van een bepaald algoritme uit de aangeboden algoritmen?**
Zie [Een algoritme kiezen](machine-learning-algorithm-choice.md).

**Zijn de geleverde algoritmen geschreven in R of Python?**

Nee, deze algoritmen zijn voornamelijk in gecompileerde talen geschreven voor optimale prestaties.

**Worden er details van de geleverde algoritmen gegeven?**

De documentatie biedt informatie over de algoritmen en er is een beschrijving van de geleverde parameters die u verder kunt afstemmen om het algoritme te optimaliseren.  

**Is er ondersteuning voor een onlinecursus?**

Nee, momenteel wordt alleen programmatisch opnieuw trainen ondersteund.

**Kan ik de lagen van een Neural Net-model met de ingebouwde module visualiseren?**

Nee.

**Kan ik mijn eigen modules in C# of een andere taal maken?**

Momenteel kunnen nieuwe aangepaste modules alleen worden gemaakt in R.

### R-module

**Welke R-pakketten zijn beschikbaar in Machine Learning Studio?**

Machine Learning Studio ondersteunt momenteel ruim 400 CRAN R-pakketten. Hier vindt u de [huidige lijst](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) van alle pakketten. Zie ook [Uw experiment uitbreiden met R](machine-learning-extend-your-experiment-with-r.md) voor meer informatie over hoe u deze lijst zelf kunt ophalen. Als het pakket dat u wilt niet in deze lijst staat, geeft u de naam op van het pakket op het [forum met feedback van gebruikers](http://go.microsoft.com/fwlink/?LinkId=404231).

**Kan ik een aangepaste R-module maken?**

Ja, zie [Aangepaste R-modules in Azure Machine Learning maken](machine-learning-custom-r-modules.md) voor meer informatie.

**Is er een REPL-omgeving voor R?**

Nee, er is geen REPL-omgeving voor R in de studio.

### Python-module

**Kan ik een aangepaste Python-module maken?**

Momenteel niet, maar u kunt een of meer [Python Script uitvoeren][python]-modules gebruiken voor hetzelfde resultaat.

**Is er een REPL-omgeving voor Python?**

U kunt de Jupyter Notebooks in Machine Learning Studio gebruiken. Zie [Introductie van Jupyter Notebooks in Azure Machine Learning Studio] (http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx) voor meer informatie.

## Webservice

###Programmatisch opnieuw trainen van modellen

**Hoe kan ik Retrain programmatisch uitvoeren voor Azure Machine Learning-modellen?**

Gebruik de Retraining API's. Zie [Machine Learning-modellen programmatisch opnieuw trainen](machine-learning-retrain-models-programmatically.md) voor meer informatie. Voorbeeldcode is ook beschikbaar in de [Microsoft Azure Maching Learning Retraining-demo](https://azuremlretrain.codeplex.com/).

### Maken

**Kan ik het model lokaal of in een toepassing implementeren zonder internetverbinding?**

Nee.


**Is er een standaardwachttijd voor alle webservices?**

Zie de [limieten voor een Azure-abonnement](../azure-subscription-service-limits.md).

### Gebruiken

**Wanneer moet ik mijn voorspellende model uitvoeren als een batchuitvoeringsservice en niet als een Request Response-service?**

De Request Response-service (RSS) is een schaalbare webservice met weinig latentie die wordt gebruikt om een interface te creëren voor staatloze modellen die worden gemaakt en geïmplementeerd vanuit de experimentele omgeving. De batchuitvoeringsservice (BES) is een service voor het asynchroon scoren van een batch gegevensrecords. De invoer voor BES is vergelijkbaar met de gegevensinvoer in RRS. Het belangrijkste verschil is dat BES een blok records uit diverse bronnen leest, zoals de blob-service en de tabelservice in Azure, Azure SQL Database, HDInsight (hive query) en HTTP-bronnen. Zie [Machine Learning-webservices gebruiken](machine-learning-consume-web-services.md) voor meer informatie.

**Hoe kan ik het model voor de geïmplementeerde webservice bijwerken?**

Het bijwerken van een voorspellend model voor een reeds geïmplementeerde service is heel eenvoudig. U wijzigt het experiment dat u hebt gemaakt en voert het opnieuw uit. Vervolgens slaat u het getrainde model op. Zodra de nieuwe versie van het getrainde model beschikbaar is, wordt via Machine Learning Studio gevraagd u of u de webservice wilt bijwerken. Zie [Een Machine Learning-webservice implementeren](machine-learning-publish-a-machine-learning-web-service.md) voor meer informatie over het bijwerken van een geïmplementeerde webservice.

U kunt ook gebruikmaken van de Retraining API's.
Zie [Machine Learning-modellen programmatisch opnieuw trainen](machine-learning-retrain-models-programmatically.md) voor meer informatie. Voorbeeldcode is ook beschikbaar in de [Microsoft Azure Maching Learning Retraining-demo](https://azuremlretrain.codeplex.com/).

**Hoe bewaak ik mijn geïmplementeerde webservice in productie?**

Zodra een voorspellend model is geïmplementeerd, kunt u dit bewaken in de klassieke Azure-portal. Elke geïmplementeerde service heeft een eigen dashboard met de controlegegevens voor de service.

**Kan ik de uitvoer van mijn RRS/BES ergens bekijken?**

Voor RRS is de reactie van de webservice de plek om het resultaat te bekijken. U kunt dit ook schrijven naar Azure Blob Storage. Voor BES wordt de uitvoer standaard naar een blob geschreven. U kunt de uitvoer ook schrijven naar een database of tabel met de module [Gegevens exporteren][export-data].

**Kan ik alleen webservices maken van modellen die in Machine Learning Studio zijn gemaakt?**

Nee, u kunt ook webservices rechtstreeks vanuit Jupyter Notebooks en RStudio maken.

**Waar vind ik informatie over foutcodes?**

Zie [Machine Learning Module-foutcodes](https://msdn.microsoft.com/library/azure/dn905910.aspx) voor een lijst met foutcodes en beschrijvingen.

## Schaalbaarheid

**Wat is de schaalbaarheid van de webservice?**

Het standaardeindpunt is momenteel voorzien van 20 gelijktijdige RRS-aanvragen per eindpunt. Dit kan worden geschaald tot 200 gelijktijdige aanvragen per eindpunt en elke webservice kan tot 10.000 eindpunten per webservice worden geschaald, zoals beschreven in [API-eindpunten schalen](machine-learning-scaling-endpoints.md). Voor BES kunnen op elk eindpunt 40 aanvragen tegelijk worden verwerkt; extra aanvragen worden in de wachtrij geplaatst. De aanvragen in de wachtrij wordt automatisch uitgevoerd terwijl de wachtrij afneemt.


**Worden R-taken verdeeld over knooppunten?**

Nee.  


**Hoeveel gegevens kan ik gebruiken voor training?**

Modules in Machine Learning Studio ondersteunen gegevenssets tot 10 GB aan compacte numerieke gegevens voor algemeen gebruik. Als een module meer dan één invoer heeft, is 10 GB de totale invoergrootte. U kunt ook een steekproef nemen uit grotere gegevenssets via Hive of Azure SQL Database-query's, of gegevens uit [Learning with Counts][counts]-modules vooraf verwerken voordat u deze opneemt.  

U kunt de volgende typen gegevens in grotere gegevenssets opnemen tijdens het normaliseren van kenmerken, tot maximaal 10 GB:

- verspreide gegevens
- categorische gegevens
- tekenreeksen
- binaire gegevens

De volgende modules zijn beperkt tot gegevenssets die kleiner zijn dan 10 GB:

- Aanbevelingsmodules
- SMOTE-modules
- Scriptmodules: R, Python, SQL
- Modules waarbij de grootte van de uitvoer groter is dan invoergegevens, zoals Join- of hash-functies.
- Kruisvalidatie, Tune Model Hyperparameters, ordinale regressie en One-vs-All-multiklasse, wanneer het aantal herhalingen groot is.

Voor gegevenssets die groter zijn dan een paar GB moet u de gegevens uploaden naar Azure Storage of Azure SQL Database, of gebruikmaken van HDInsight, in plaats van de gegevens direct vanuit het lokale bestand te uploaden.


**Zijn er beperkingen voor de vectorgrootte?**

Rijen en kolommen zijn beperkt tot de .NET-beperking van Max Int: 2.147.483.647.

**Kan de grootte van de virtuele machine die wordt gebruikt voor het uitvoeren van de webservice worden aangepast?**

Nee.  

## Beveiliging en beschikbaarheid

**Wie heeft standaard toegang tot het HTTP-eindpunt voor de webservice? Hoe beperk ik de toegang tot het eindpunt?**

Na de implementatie van een webservice wordt een standaardeindpunt voor de service gemaakt. Het standaardeindpunt kan worden aangeroepen met de API-sleutel. Extra eindpunten kunnen worden toegevoegd met hun eigen sleutels van de klassieke Azure-portal of programmatisch met de Web Service Management API's. Voor het aanroepen van de webservice is een toegangssleutel vereist. Zie [Verbinding maken met een Machine Learning-webservice](machine-learning-connect-to-azure-machine-learning-web-service.md) voor meer informatie.


**Wat gebeurt er als mijn Azure-opslagaccount niet kan worden gevonden?**

Voor Machine Learning Studio hebben gebruikers een Azure-opslagaccount nodig om gegevens tussentijds op te slaan bij het uitvoeren van de werkstroom. Dit opslagaccount wordt verstrekt aan Machine Learning Studio wanneer een werkruimte wordt gemaakt. Als het opslagaccount is verwijderd en niet meer kan worden gevonden nadat de werkruimte is gemaakt, functioneert de werkruimte niet meer en mislukken alle experimenten in deze werkruimte.

Als u per ongeluk het opslagaccount hebt verwijderd, kunt u dit alleen nog herstellen door het opslagaccount opnieuw te maken met dezelfde naam in dezelfde regio als het verwijderd opslagaccount. Vervolgens moet u de toegangssleutel opnieuw synchroniseren.


**Wat gebeurt er als mijn toegangssleutel voor het opslagaccount niet meer is gesynchroniseerd?**

Voor Machine Learning Studio hebben gebruikers een Azure-opslagaccount nodig om gegevens tussentijds op te slaan bij het uitvoeren van de werkstroom. Dit opslagaccount en de toegangssleutel voor de werkruimte worden verstrekt aan Machine Learning Studio wanneer een werkruimte wordt gemaakt. Als het opslagaccount is gemaakt en de toegangssleutel wordt gewijzigd, heeft de werkruimte geen toegang meer tot het opslagaccount. De werkruimte functioneert dan niet meer en alle experimenten in deze werkruimte mislukken.

Als u de toegangssleutel voor een opslagaccount hebt gewijzigd, moet u de toegangssleutel in de klassieke Azure-portal opnieuw synchroniseren in de werkruimte.  


## Azure Marketplace

Zie de [Veelgestelde vragen over het publiceren en gebruiken van apps in de Machine Learning Marketplace](machine-learning-marketplace-faq.md).

## Ondersteuning en training

**Waar kan ik training krijgen voor Azure Machine Learning?**

Het [Azure Machine Learning-documentatiecentrum](https://azure.microsoft.com/services/machine-learning/) bevat video’s voor zelfstudies en handleidingen. Deze stapsgewijze instructies zijn een inleiding op de services en helpen u op weg in het hele proces, van het importeren van gegevens, het opruimen van gegevens, het ontwikkelen van voorspellende modellen tot de implementatie in productie met Azure Machine Learning.

Er wordt regelmatig nieuw materiaal aan Machine Learning Center toegevoegd. U kunt aanvragen versturen voor extra materiaal in Machine Learning Center via het [forum met feedback van gebruikers](https://windowsazure.uservoice.com/forums/257792-machine-learning).

U vindt ook trainingsmateriaal op [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Waar kan ik ondersteuning krijgen voor Azure Machine Learning?**

Voor technische ondersteuning voor Azure Machine Learning gaat u naar [de ondersteuning van Azure](/support/options/) en selecteert u **Machine Learning**.

Azure Machine Learning heeft ook een communityforum op MSDN waar u vragen kunt stellen over Azure Machine Learning. Het forum wordt bewaakt door het Azure Machine Learning-team. Ga naar het [Azure-forum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx



<!--HONumber=Jun16_HO2-->


