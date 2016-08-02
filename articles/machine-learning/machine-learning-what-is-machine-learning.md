<properties
    pageTitle="Wat is Machine Learning in Azure? | Microsoft Azure"
    description="In dit artikel worden de basisconcepten uitgelegd van de volledig beheerde Azure Machine Learning-service, een cloudtechnologie waarmee u oplossingen kunt ontwikkelen, deze operationeel kunt maken en in omloop kunt brengen."
    keywords="what is machine learning,cloud technology,predictive,what is predictive analytics,operationalize"
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/28/2016"
    ms.author="cgronlun;tedway;olgali"/>


# Inleiding tot Machine Learning in Microsoft Azure

## Wat is machine learning?

Bij machine learning worden computers gebruikt om voorspellende modellen uit te voeren die kunnen leren van bestaande gegevens om zo toekomstige resultaten, gedrag en trends te voorspellen.

Deze prognoses of voorspellingen op basis van machine learning kunnen apps en apparaten slimmer maken. Wanneer u online winkelt, helpt machine learning op basis van wat u eerder hebt gekocht, bij het aanraden van andere producten waarvoor u mogelijk belangstelling hebt. Wanneer uw creditcard wordt gebruikt, vergelijkt machine learning de transactie met een database van transacties om de bank te helpen bij fraudedetectie.

## Wat is Machine Learning in Microsoft Azure?

Azure Machine Learning is een krachtige cloudservice voor predictive analytics waarmee u snel voorspellende modellen kunt maken en implementeren als analytics-oplossingen.

Azure Machine Learning bevat niet alleen hulpprogramma's om predictive analytics te ontwikkelen, maar ook een volledig beheerde service waarmee u de voorspellende modellen kunt implementeren als gebruiksklare webservices. Azure Machine Learning voorziet in hulpprogramma's voor het maken van volledige predictive analytics-oplossingen in de cloud. Hiermee kunt u snel voorspellende modellen opzetten, testen, operationeel maken en beheren. U hoeft geen hardware aan te schaffen en geen virtuele machines handmatig te beheren.

![Wat is machine learning? De basiswerkstroom om predictive analytics operationeel te maken in Azure Machine Learning.](./media/machine-learning-what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Wat is predictive analytics?

Predictive analytics maakt gebruik van verschillende statistische technieken (in dit geval machine learning) om verzamelde of actuele gegevens te analyseren op patronen of trends, en op basis daarvan toekomstige gebeurtenissen te voorspellen.

Azure Machine Learning is een bijzonder krachtige manier om predictive analytics uit te voeren. U kunt met een kant-en-klare bibliotheek van algoritmen werken, modellen maken op een pc met internetverbinding zonder dat u extra apparatuur of infrastructuur hoeft aan te schaffen, en de voorspellende oplossing snel implementeren. Kant-en-klare voorbeelden en oplossingen vindt u in de [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning) en de [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/).

## Complete machine learning-oplossingen bouwen in de cloud

Azure Machine Learning heeft alles wat u nodig hebt om predictive analytics-oplossingen te maken in de cloud: van een grote bibliotheek met algoritmen tot een studio voor het ontwikkelen van modellen, en een eenvoudige manier om uw modellen vervolgens als webservices te implementeren.

### Machine Learning Studio: voorspellende modellen maken

Maak voorspellende modellen in [Machine Learning Studio](machine-learning-what-is-ml-studio.md), een op een browser gebaseerd hulpprogramma, door modules te slepen, te verwijderen en te verbinden.

![Wat is predictive analytics? Voorbeeld van een predictive analytics-experiment in Azure Machine Learning Studio](./media/machine-learning-what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)

* Gebruik een grote bibliotheek met [Machine Learning-algoritmen en -modules](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) in Machine Learning Studio om snel aan de slag te gaan met voorspellende modellen. Kies uit een bibliotheek met voorbeeldexperimenten, R- en Python-pakketten en de allerbeste algoritmen van Microsoft-bedrijven als Xbox en Bing. Breid Studio-modules uit met uw eigen aangepaste [R](machine-learning-r-quickstart.md)- en [Python](machine-learning-execute-python-scripts.md)-scripts.
* In de [Cortana Intelligence Gallery](machine-learning-gallery-how-to-use-contribute-publish.md) kunt u analytics-oplossingen van anderen uitproberen of uw eigen oplossingen bijdragen met Azure-services als Machine Learning, HDInsight (Hadoop), Stream Analytics en Data Lake Analytics, evenals Azure-archieven voor big data en gegevensbeheerservices.  U kunt vragen en opmerkingen over experimenten plaatsen in de community en koppelingen naar experimenten delen via sociale netwerken, zoals LinkedIn en Twitter.  

    ![Probeer voorspellende experimenten of deel uw eigen experimenten in de Azure Cortana Intelligence Gallery](./media/machine-learning-what-is-machine-learning/machine-learning-cortana-intelligence-gallery.png)

### Predictive analytics-oplossingen operationeel maken: webservices kopen of uw eigen websites publiceren

* Koop gebruiksklare webservices op [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning), zoals Recommendations, Text Analytics en Anomaly Detection.

* Predictive analytics-modellen operationeel maken:
    * [Webservices implementeren](machine-learning-publish-a-machine-learning-web-service.md)
    * [Modellen trainen en opnieuw trainen via API's](machine-learning-retrain-models-programmatically.md)
    * [Webservice-eindpunten beheren](machine-learning-create-endpoint.md)
    * [Webservices schalen](machine-learning-scaling-endpoints.md)
    * [Webservices gebruiken](machine-learning-consume-web-services.md)

## Belangrijkste termen en begrippen op het gebied van machine learning
### Gegevensverkenning, beschrijvende analyse en predictive analytics

**Gegevensverkenning** is het verzamelen van gegevens over een grote en vaak ongestructureerde gegevensset om kenmerken te identificeren voor gerichte analyse. **Gegevensanalyse** verwijst naar geautomatiseerde gegevensverkenning.

**Beschrijvende analyse** is het analyseren van een gegevensset om een overzicht te maken van wat er is gebeurd. De meeste bedrijfsanalysen, zoals verkoopoverzichten, webmetrieken en sociale-netwerkanalysen, zijn beschrijvend.

**Predictive analytics** is het bouwen van modellen van historische of actuele gegevens om toekomstige resultaten te voorspellen.


### Leren met of zonder supervisie
 Algoritmen voor **leren met supervisie** zijn getraind met gelabelde gegevens. Dat wil zeggen: met gegevens die bestaan uit voorbeelden van de gewenste antwoorden. Een model voor het detecteren van frauduleus creditcardgebruik wordt bijvoorbeeld getraind met een gegevensset waarin gegevenspunten zijn gelabeld die bekende frauduleuze of juist geldige afschrijvingen aangeven. De meeste machine learning vindt plaats met supervisie.

 **Leren zonder supervisie** wordt gebruikt voor gegevens zonder labels, met de bedoeling om relaties tussen gegevens te zoeken. U kunt bijvoorbeeld aan de hand van uw demografische klantgegevens groepen klanten met vergelijkbaar koopgedrag identificeren.

### Modeltraining en -evaluatie
Een machine learning-model is een abstractie van de vraag die u probeert te beantwoorden of van het resultaat dat u wilt voorspellen. Modellen worden getraind en geëvalueerd op basis van bestaande gegevens.

#### Training met gegevens
In Azure Machine Learning wordt een model ontwikkeld op basis van een algoritmemodule die trainingsgegevens en functionele modules verwerkt, bijvoorbeeld een scoremodule.

Als u bij leren met supervisie een model voor fraudedetectie traint, gebruikt u een reeks transacties die zijn gelabeld als frauduleus of geldig. U splitst de gegevensset willekeurig op. Vervolgens gebruikt u het ene deel om het model te trainen en het andere deel om het model te testen of te evalueren.

#### Evaluatiegegevens
Nadat uw model is getraind, kunt u het met de resterende testgegevens evalueren. U gebruikt gegevens waarvan u de resultaten al kent. U kunt dus controleren of de voorspellingen van het model nauwkeurig zijn.

### Andere veelvoorkomende machine learning-termen

* **Algoritme**: een onafhankelijke set regels voor het oplossen van problemen met behulp van gegevensverwerking, berekening of geautomatiseerde redenering.
* **Categorische gegevens**: gegevens die zijn ingedeeld in categorieën die in groepen kunnen worden verdeeld. Een categorische gegevensset voor auto's kan bijvoorbeeld informatie bevatten over bouwjaar, merk, model en prijs.
* **Classificatie**: een model voor het ordenen van gegevenspunten in categorieën op basis van een gegevensset waarvan de categoriegroeperingen al bekend zijn.
* **Functie-engineering**: het uitpakken of selecteren van functies voor een gegevensset om de gegevensset en de resultaten ervan te verbeteren. Zo kunnen vliegticketgegevens bijvoorbeeld worden uitgebreid met informatie over de dag van de week en het onderscheid werkdag versus feestdag. Zie [Feature selection and engineering in Azure Machine Learning](machine-learning-feature-selection-and-engineering.md) (Functieselectie en -engineering in Azure Machine Learning).
* **Module**: een functioneel element in een Machine Learning Studio-model, zoals het model Enter Data, voor het invoeren en bewerken van kleine gegevenssets. Een algoritme is ook een type module in Machine Learning Studio.
* **Model**: voor leren met supervisie is een model het product van een machine learning-experiment, bestaande uit een set trainingsgegevens, een algoritmemodule en functionele modules, zoals de module Score Model.
* **Numerieke gegevens**: gegevens die een betekenis hebben als meetwaarden (doorlopende gegevens) of aantallen (discrete gegevens). Dit worden ook wel *kwantitatieve gegevens* genoemd.
* **Partitie**: de methode waarmee u gegevens in steekproeven verdeelt. Zie [Partition and Sample](https://msdn.microsoft.com/library/azure/dn905960.aspx) (Partitie en steekproef) voor meer informatie.
* **Voorspelling**: een voorspelling is een prognose van een of meer waarden op basis van een machine learning-model. Soms ziet u ook de term 'voorspelde score'. Voorspelde scores zijn echter niet de uiteindelijke uitvoer van een model. Een evaluatie van het model volgt de score.
* **Regressie**: een model voor het voorspellen van een doorlopende waarde op basis van onafhankelijke variabelen, zoals het voorspellen van de prijs van een auto op basis van het bouwjaar en het model.
* **Score**: een voorspelde waarde, op basis van een getraind classificatie- of regressiemodel gegenereerd met behulp van de [module Score Model](https://msdn.microsoft.com/library/azure/dn905995.aspx) in Machine Learning Studio. Classificatiemodellen kunnen ook een score retourneren voor de waarschijnlijkheid van de voorspelde waarde. Wanneer u scores op basis van een model hebt gegenereerd, kunt u de nauwkeurigheid van het model evalueren met de [module Evaluate Model](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **Steekproef**: een representatief gedeelte van een gegevensset. Steekproeven kunnen willekeurig worden geselecteerd, maar ook op basis van specifieke functies van de gegevensset.



## Volgende stappen
U kunt zich de basiskennis van predictive analytics en machine learning eigen maken aan de hand van een [Stapsgewijze zelfstudie](machine-learning-create-experiment.md) en [door voorbeelden verder uit te werken](machine-learning-sample-experiments.md).  


<!-- Module References -->
[leren-met-aantallen]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/



<!--HONumber=Jun16_HO2-->


