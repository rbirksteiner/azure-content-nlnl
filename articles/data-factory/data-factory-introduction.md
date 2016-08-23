<properties 
    pageTitle="Inleiding tot Azure Data Factory" 
    description="Ontdek hoe u de Azure Data Factory-service kunt gebruiken voor het samenstellen van services voor gegevensverwerking, gegevensopslag en gegevensverplaatsing zodat u pijplijnen kunt maken voor het produceren van vertrouwde informatie." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="04/26/2016" 
    ms.author="spelluru"/>

# Inleiding tot de Azure Data Factory-service

## Overzicht
Een Data Factory is een cloudgebaseerde gegevensintegratieservice waarmee de verplaatsing en transformatie van gegevens wordt beheerd en geautomatiseerd. Net als bij een productiefabriek waarin apparatuur wordt gebruikt om grondstoffen te transformeren in eindproducten, beheert Data Factory bestaande services die onbewerkte gegevens verzamelen en transformeren in kant-en-klare informatie. 

Data Factory werkt met on-premises gegevensbronnen, gegevensbronnen in de cloud en SaaS om uw gegevens op te nemen, voor te bereiden, te transformeren, te analyseren en te publiceren.  Gebruik Data Factory om services samen te stellen voor uw beheerde gegevensstroompijplijnen om uw gegevens te transformeren. Dit doet u met services zoals [Azure HDInsight (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) en [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) voor uw grote gegevensberekeningsbehoeften en met services zoals [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) om uw analyseoplossingen in werking te stellen.  Profiteer van méér dan slechts een bewakingsweergave in tabelvorm en maak gebruik van de uitgebreide afbeeldingen van Data Factory om snel inzicht te krijgen in de afkomstinformatie en afhankelijkheden tussen uw gegevenspijplijnen. Bewaak al uw gegevensstroompijplijnen in één centrale weergave zodat u snel problemen kunt herkennen en bewakingswaarschuwingen kunt instellen.

![Overzicht](./media/data-factory-introduction/data-factory-overview.png)

**Afbeelding 1.** Verzamel gegevens van veel verschillende on-premises gegevensbronnen, neem ze op en bereid ze voor, orden en analyseer ze met verschillende transformaties en publiceer daarna kant-en-klare gegevens die geschikt zijn voor gebruik.

U kunt Data Factory te allen tijde gebruiken om verschillende soorten gegevens te verzamelen, te transformeren en te publiceren om uitgebreide statistieken te verkrijgen - en dit alles op basis van een betrouwbare planning. Data Factory wordt gebruikt om zeer beschikbare gegevensflowpijplijnen te maken om te voldoen aan de analysepijplijnbehoeften voor vele verschillende scenario’s in verschillende branches.  Onlinewinkels gebruiken gegevensfactory’s om persoonlijke [productaanbevelingen](data-factory-product-reco-usecase.md) te doen op basis van het bladergedrag van de klant. Gameontwikkelaars gebruiken gegevensfactory’s om inzicht te krijgen in de [effectiviteit van hun marketing](data-factory-customer-profiling-usecase.md)campagnes. Kom rechtstreeks van klanten te weten hoe en waarom ze Data Factory gebruiken. Lees daarvoor de [casestudy's van klanten](data-factory-customer-case-studies.md). 

> [AZURE.VIDEO azure-data-factory-overview]

## Belangrijkste concepten

Azure Data Factory heeft enkele belangrijke entiteiten die samenwerken om de in- en uitvoergegevens, verwerkingsgebeurtenissen en planning en resources te definiëren die nodig zijn om de gewenste gegevensstroom uit te voeren.

![Belangrijkste concepten](./media/data-factory-introduction/key-concepts.png)

**Afbeelding 2.** Relaties tussen de gegevensset, de activiteit, de pijplijn en de gekoppelde service


### Activiteiten
Met activiteiten definieert u welk acties moeten worden uitgevoerd voor uw gegevens. Voor elke activiteit zijn nul of meer [gegevenssets](data-factory-create-datasets.md) nodig als invoer en worden één of meer gegevenssets geproduceerd als uitvoer. Een activiteit is een eenheid voor beheer in Azure Data Factory. U kunt bijvoorbeeld een [kopieeractiviteit](data-factory-data-movement-activities.md) gebruiken om het kopiëren van gegevens van één gegevensset naar een andere te beheren. U kunt ook een [Hive-activiteit](data-factory-data-transformation-activities.md) gebruiken, waarmee een Hive-query wordt uitgevoerd voor een Azure HDInsight-cluster om uw gegevens te transformeren of analyseren. Azure Data Factory biedt een breed scala aan activiteiten voor gegevenstransformatie, analyse en gegevensverplaatsing. 

### Pijplijnen
[Pijplijnen](data-factory-create-pipelines.md) zijn logische groepen activiteiten. Ze worden gebruikt om activiteiten te groeperen in één eenheid waarmee vervolgens een taak kan worden uitgevoerd. Er is bijvoorbeeld mogelijk een reeks van verschillende transformatieactiviteiten nodig om logboekbestandgegevens op te schonen.  Deze reeks kan bestaan uit een complexe planning met afhankelijkheden die moeten worden beheerd en geautomatiseerd. Alle deze activiteiten kunnen worden gegroepeerd in één pijplijn met de naam CleanLogFiles.  CleanLogFiles kan vervolgens als één eenheid worden geïmplementeerd, gepland of verwijderd; u hoeft dan niet alle activiteiten afzonderlijk te beheren.

### Gegevenssets
[Gegevenssets](data-factory-create-datasets.md) hebben een naam en zijn verwijzingen naar de gegevens die u wilt gebruiken als invoer of uitvoer van een activiteit. Met gegevenssets worden gegevensstructuren binnen andere gegevensarchieven geïdentificeerd, waaronder tabellen, bestanden, mappen en documenten.

### Gekoppelde service
Met gekoppelde services wordt gedefinieerd welke informatie nodig is om een Data Factory verbinding te laten maken met externe resources.  Gekoppelde services worden voor twee doeleinden gebruikt in een Data Factory:

- Als vertegenwoordiging van een gegevensarchief, zoals een on-premises SQL Server, een Oracle DB, een bestandsshare of een Azure Blob Storage-account.   Zoals hierboven wordt beschreven, vertegenwoordigen gegevenssets de structuren in de gegevensarchieven die via een gekoppelde service zijn verbonden aan een gegevensfactory.
- Ter vertegenwoordiging van een berekeningsresource die de uitvoering van een activiteit kan hosten.  De activiteit HDInsightaHive wordt bijvoorbeeld uitgevoerd in een HDInsight Hadoop-cluster.

Met deze vier eenvoudige concepten (gegevenssets, activiteiten, pijplijnen en gekoppelde services) kunt u snel aan de slag.  U kunt [uw eerste pijplijn](data-factory-build-your-first-pipeline.md) volledig zelf bouwen of een voorbeeld implementeren door de instructies in het artikel [Data Factory-voorbeelden](data-factory-samples.md) te volgen. 

## Ondersteunde regio’s
U kunt op dit moment gegevensfactory’s maken in de regio’s **VS - west**, **VS - oost** en **Noord-Europa**. Een gegevensfactory heeft echter wel toegang tot gegevensarchieven en Compute Services in andere Azure-regio’s om gegevens te verplaatsen tussen gegevensarchieven of om gegevens te verwerken middels Compute Services. 

Azure Data Factory zelf slaat geen gegevens op. U kunt er gegevensgestuurde stromen mee maken om de verplaatsing van gegevens te beheren tussen [ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores) en om er gegevens mee te verwerken middels [Compute Services](data-factory-compute-linked-services.md) in andere regio's of in een on-premises omgeving. U kunt er ook [werkstromen mee bewaken en beheren](data-factory-monitor-manage-pipelines.md) met zowel programmatische als gebruikersinterfacemechanismen. 

Hoewel Azure Data Factory alleen beschikbaar is in **VS - west**, **VS - oost** en **Noord-Europa**, is de service die gegevensverplaatsing in Data Factory mogelijk maakt, [wereldwijd](data-factory-data-movement-activities.md#global) beschikbaar in meerdere regio’s. Als een gegevensarchief zich achter een firewall bevindt, worden de gegevens verplaatst middels een [gegevensbeheergateway](data-factory-move-data-between-onprem-and-cloud.md) die is geïnstalleerd in uw on-premises omgeving. 

Voorbeeld: uw berekeningsomgeving(en), zoals een Azure HDInsight-cluster en Azure Machine Learning, worden uitgevoerd in de regio West-Europa. U kunt een Azure Data Factory-exemplaar maken en gebruiken in Noord-Europa en dit gebruiken om taken te plannen in uw berekeningsomgevingen in West-Europa. Het duurt enkele milliseconden voordat de Data Factory-service de taak in uw berekeningsomgeving activeert, maar de uitvoertijd van de taak verandert niet in uw berekeningsomgeving.

Het plan bestaat om ervoor te zorgen dat in de toekomst Azure Data Factory in alle regio’s wordt ondersteund door Azure.
  




<!--HONumber=Jun16_HO2-->


