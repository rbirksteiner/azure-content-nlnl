<properties
    pageTitle="Een eenvoudige experiment maken in Machine Learning Studio | Microsoft Azure"
    description="Een eerste machine learning-zelfstudie waarin u een eenvoudig experiment maakt om een lineair regressiemodel in Azure Machine Learning Studio te trainen en te testen."
    keywords="experiment,linear regression,machine learning algorithms,machine learning tutorial,predictive modeling techniques"
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
    ms.topic="hero-article"
    ms.date="03/09/2016"
    ms.author="garye"/>

# Zelfstudie over machine learning: uw eerste experiment maken in Azure Machine Learning Studio

In deze eerste machine learning-zelfstudie maakt u een lineair regressiemodel waarmee de prijs van een auto kan worden voorspeld op basis van verschillende variabelen, zoals het merk en de technische specificaties. Hiervoor maken we gebruik van Azure Machine Learning Studio om eenvoudige predictive analytics-experimenten te ontwikkelen en te herhalen.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Als u een Machine Learning Studio-experiment wilt maken, sleept u onderdelen naar een canvas. Door de onderdelen met elkaar te verbinden kunt u *een model maken*, *het model trainen* en *het model beoordelen en testen*. Voor het experiment wordt gebruikgemaakt van voorspellende modelleringstechnieken in de vorm van Machine Learning Studio-modules die gegevens opnemen, het model trainen aan de hand van die gegeven en het model toepassen op nieuwe gegevens. U kunt ook modules toevoegen om de gegevens voor te verwerken en kenmerken te selecteren, trainings- en testsets op te splitsen en om de kwaliteit van uw model te evalueren of gekruist te valideren.  

Ga naar Machine Learning Studio: [https://studio.azureml.net](https://studio.azureml.net) en klik op de knop **Get started** (Aan de slag). U kunt zich aanmelden als gast of met uw Microsoft-account.

Zie [Wat is Machine Learning Studio?](machine-learning-what-is-ml-studio.md) voor meer algemene informatie over Machine Learning Studio.

>[AZURE.TIP] Zie [Overzichtsdiagram van de mogelijkheden van Azure Machine Learning Studio](machine-learning-studio-overview-diagram.md) als u een diagram wilt downloaden en afdrukken met een overzicht van de mogelijkheden van Machine Learning Studio.


## In vijf stappen een experiment maken

In deze machine learning-zelfstudie volgt u vijf eenvoudige stappen in Machine Learning Studio om een model te maken, te trainen en te beoordelen:

- Een model maken
    - [Stap 1: gegevens ophalen]
    - [Stap 2: gegevens voorverwerken]
    - [Stap 3: functies definiëren]
- Het model trainen
    - [Stap 4: een leeralgoritme kiezen en toepassen]
- Het model beoordelen en testen
    - [Stap 5: prijzen van nieuwe auto's voorspellen]

[Stap 1: gegevens ophalen]: #step-1-get-data
[Stap 2: gegevens voorverwerken]: #step-2-preprocess-data
[Stap 3: functies definiëren]: #step-3-define-features
[Stap 4: een leeralgoritme kiezen en toepassen]: #step-4-choose-and-apply-a-learning-algorithm
[Stap 5: prijzen van nieuwe auto's voorspellen]: #step-5-predict-new-automobile-prices


## Stap 1: gegevens ophalen

Machine Learning Studio bevat een aantal voorbeeldgegevenssets waaruit u kunt kiezen. Daarnaast kun u uit tal van bronnen gegevens importeren. Voor dit voorbeeld gebruiken we de bijgeleverde voorbeeldgegevensset **Automobile price data (Raw)**.
Deze gegevensset bevat vermeldingen voor een aantal afzonderlijke auto’s, inclusief informatie over het merk, het model, de technische specificaties en de prijs.

1. Start een nieuw experiment door onder aan het Machine Learning Studio-venster op **+NEW** (+NIEUW) te klikken en achtereenvolgens **EXPERIMENT** en **Blank Experiment** (Leeg experiment) te selecteren. Selecteer boven aan het canvas de standaardnaam voor een experiment en wijzig deze in een beschrijvende naam **Prijzen auto's voorspellen**.

2. Aan de linkerkant van het experimentcanvas bevindt zich een palet met gegevenssets en modules. Typ in het zoekvak boven aan dit palet **automobile** om de gegevensset met het label **Automobile price data (Raw)** te zoeken.

    ![Palet zoeken][screen1a]

3. Sleep de gegevensset naar het experimentcanvas.

    ![Gegevensset][screen1]

Als u wilt zien hoe deze gegeven eruitzien, klikt u op de uitvoerpoort onder aan de gegevensset automobile en selecteert u vervolgens **Visualize** (Visualiseren). De variabelen in de gegevensset worden weergegeven als kolommen en elk exemplaar van een auto wordt weergegeven als een rij. De meest rechtse kolom (kolom 26, genaamd 'price') is de doelvariabele die we proberen te voorspellen.

![Visualisatie van de gegevensset][screen1b]

Sluit het visualisatievenster door op de **x** in de rechterbovenhoek te klikken.

## Stap 2: gegevens voorverwerken

Normaal gesproken moet een gegevensset worden voorverwerkt voordat deze kan worden geanalyseerd. Het is u mogelijk opgevallen dat er in verschillende rijen ontbreken in de kolommen van verschillende rijen. Deze ontbrekende waarden moeten worden opgeschoond, zodat de gegevens correct kunnen worden geanalyseerd. In ons geval verwijderen we de rijen met ontbrekende waarden. Ook in de kolom **normalized-losses** ontbreekt een groot deel van de waarden. Deze kolom zal daarom helemaal worden uitgesloten van het model.

> [AZURE.TIP] Voor de meeste modules geldt dat voor het gebruik van de module de ontbrekende invoergegevens moeten worden opgeschoond.

Eerst verwijderen we de kolom **normalized-losses**. Vervolgens worden de rijen met ontbrekende gegevens verwijderd.

1. Typ in het zoekvak boven aan het modulepalet **select columns** om de module [Select Columns in Dataset][select-columns] (Kolommen in gegevensset selecteren), sleep de module naar het experimentcanvas en koppel de module aan de uitvoerpoort van de gegevensset **Automobile price data (Raw)**. Met deze module kunt u selecteren welke kolommen met gegevens u wilt opnemen in of uitsluiten voor het model.

2. Selecteer de module [Select Columns in Dataset][select-columns] (Kolommen in gegevensset selecteren) en klik in het deelvenster **Properties** (Eigenschappen) op **Launch column selector** (Kolomselector starten).

    - Zorg ervoor dat in de vervolgkeuzelijst **Begin With** (Beginnen met) het filter **All columns** (Alle kolommen) is geselecteerd. Zodoende zorgt u ervoor dat [Select Columns in Dataset][select-columns] (Kolommen in gegevensset selecteren) alle kolommen doorgeeft (met uitzondering van de kolommen die we dadelijk zullen uitsluiten).
    - Selecteer in de volgende rij **Exclude** (Uitsluiten) en **column names** (kolomnamen) en klik in het tekstvak. Er wordt een lijst met kolommen weergegeven. Selecteer **normalized-losses** om aan het tekstvak toe te voegen.
    - Klik op de knop met het vinkje (OK) om de kolomkiezer te sluiten.

    ![Kolommen selecteren][screen3]

    Het deelvenster met eigenschappen van de module **Select Columns in Dataset** (Kolommen in gegevensset selecteren) geeft aan dat alle kolommen uit de gegevensset worden doorgegeven, met uitzondering van **normalized-losses**.

    ![Kolommen in gegevensseteigenschappen selecteren][screen4]

    > [AZURE.TIP] U kunt een opmerking aan een module toevoegen door te dubbelklikken op de module en tekst in te voeren. Zodoende kunt u in één oogopslag zien wat de module in uw experiment doet. Dubbelklik in dit geval op de module [Select Columns in Dataset][select-columns] (Kolommen in gegevensset selecteren) en typ de opmerking 'normalized-losses uitsluiten'.

3. Sleep de module [Clean Missing Data][clean-missing-data] (Ontbrekende gegevens opschonen) naar het canvas en verbindt deze met de module [Select Columns in Dataset][select-columns] (Kolommen in gegevensset selecteren). Selecteer in het deelvenster **Properties** (Eigenschappen) de optie **Remove entire row** (Hele rij verwijderen) onder **Cleaning mode** (Opschoonmodus) om de gegevens op te schonen door rijen met ontbrekende waarden te verwijderen. Dubbelklik op de module en typ de opmerking 'Rijen met ontbrekende waarde verwijderen'.

    ![Ontbrekende gegevenseigenschappen opschonen][screen4a]

4. Voer het experiment uit door op **RUN** (UITVOEREN) onder het experimentcanvas te klikken.

Wanneer het experiment is voltooid, wordt er bij alle modules een groen vinkje weergegeven om aan te geven dat deze zijn voltooid. In de rechterbovenhoek wordt de status **Finished running** (Uitvoeren voltooid) weergegeven.

![Eerste experiment uitvoeren][screen5]

Tot nu toe hebben we in het experiment alleen de gegevens opgeschoond. Als u de opgeschoonde gegevensset wilt weergeven, klikt u op de uitvoerpoort links van de module [Clean Missing Data][clean-missing-data] (Ontbrekende gegevens opschonen) ('Opgeschoonde gegevensset') en selecteer **Visualize** (Visualiseren). Zoals u kunt zien, is de kolom **normalized-losses** verwijderd en ontbreken er geen waarden meer.

Nu de gegevens zijn opgeschoond, kunt u opgeven welke functies u wilt gebruiken in het voorspellende model.

## Stap 3: functies definiëren

In machine learning zijn *functies* afzonderlijke meetbare eigenschappen van iets waarin u geïnteresseerd bent. In onze gegevensset staat elke rij voor één auto en elke kolom bevat een kenmerk van die auto. Voor een goede set kenmerken voor het maken van een voorspellend model, moet u experimenteren en beschikken over kennis van het probleem dat u wilt oplossen. Bepaalde kenmerken zijn beter voor het voorspellen van het doel dan andere. Bovendien bestaat er tussen sommige kenmerken een nauwe correlatie (bijvoorbeeld tussen city-mpg en highway-mpg), waardoor ze weinig nieuwe informatie aan het model toevoegen en ze kunnen worden verwijderd.

Laten we een model bouwen dat gebruikmaakt van een subset kenmerken onze gegevensset. U kunt terugkeren en andere kenmerken selecteren om het experiment vervolgens opnieuw uit te voeren en te zien of u betere resultaten krijgt. In eerste instantie selecteren we de volgende kenmerken (kolommen) met de module [Select Columns in Dataset][select-columns] (Kolommen in gegevensset selecteren). Om het model te trainen, moeten we de waarde *price* opnemen die we willen voorspellen.

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Sleep nog een module [Select Columns in Dataset][select-columns] (Kolommen in gegevensset selecteren) naar het experimentcanvas en koppel de module aan de linkeruitvoerpoort van de module [Clean Missing Data][clean-missing-data] (Ontbrekende gegevens opschonen). Dubbelklik op de module en typ 'Select features for prediction' (Kenmerken voor de voorspelling selecteren).

2. Klik in het deelvenster **Properties** (Eigenschappen) op **Launch column selector** (Kolomselector starten).

3. Selecteer in de kolomselector de optie **No columns** (Geen kolommen) voor **Begin With** (Beginnen met) en selecteer in de filterrij vervolgens **Include** (Opnemen) en **column names** (kolomnamen). Geef onze lijst met kolomnamen op. Dit zorgt ervoor dat de module alleen die kolommen doorgeeft die wij opgeven.

    > [AZURE.TIP] Omdat we het experiment hebben uitgevoerd, zijn de kolomdefinities voor onze gegevens vanuit de oorspronkelijke gegevensset doorgegeven aan de module [Clean Missing Data][clean-missing-data] (Ontbrekende gegevens opschonen). Wanneer u de module [Select Columns in Dataset][select-columns] (Kolommen in gegevensset selecteren) koppelt aan de module [Clean Missing Data][clean-missing-data] (Ontbrekende gegevens opschonen), wordt de module [Select Columns in Dataset][select-columns] zich bewust van de kolomdefinities in onze gegevens. Wanneer u op het vak **column names** (kolomnamen) klikt, wordt er een lijst met kolommen weergegeven en kunt u de kolommen selecteren die u wilt toevoegen aan de lijst.

4. Klik op de knop met het vinkje (OK).

![Kolommen selecteren][screen6]

Hiermee produceert u de gegevensset die wordt gebruikt in het leeralgoritme in de volgende stappen. U kunt later terugkeren en het opnieuw proberen met een andere selectie kenmerken.

## Stap 4: een leeralgoritme kiezen en toepassen

Nu de gegevens klaar zijn, kunt u een voorspellend model bouwen door het model te trainen en te testen. We gebruiken onze gegevens om het model te trainen. Vervolgens testen we het model om te controleren in hoeverre de prijzen met dit model kunnen worden voorspeld.

*Classificatie* en *regressie* zijn twee soorten beheerde machine learning-technieken. Classificatie wordt gebruikt om een voorspelling te maken voor een gedefinieerde set waarden, zoals een kleur (rood, blauw of groen). Regressie wordt gebruikt om een voorspelling te maken voor een continue reeks waarden, zoals de leeftijd van een persoon.

Aangezien we prijs van een auto willen voorspellen en deze elke waarde kan hebben, gebruiken we een regressiemodel. Voor dit voorbeeld trainen we een eenvoudig *lineair regressiemodel* dat we in de volgende stap zullen testen.

1. We kunnen onze gegevens zowel voor trainings- als testdoeleinden gebruiken door ze op te splitsen in twee afzonderlijke trainings- en testsets. Selecteer de module [Split Data][split] (Gegevens splitsen), sleep deze naar het experimentcanvas en koppel de module aan de uitvoer van de laatste module [Select Columns in Dataset][select-columns] (Kolommen in gegevensset selecteren). Stel **Fraction of rows in the first output dataset** (Fractie van rijen in de eerste uitvoergegevensset) in op 0,75. Zodoende gebruiken we 75 procent van de gegevens om het model te trainen en gebruiken we 25 procent van de gegevens om het model te testen.

    > [AZURE.TIP] Door de parameter **Random seed** (Willekeurige seed) te wijzigen, kunt u verschillende willekeurig samples voor trainings- en testdoeleinden gebruiken. Deze parameter bepaalt de seeding van de pseudo-willekeurige nummergenerator.

2. Voer het experiment uit. Zodoende kunnen de modules [Select Columns in Dataset][select-columns] (Kolommen in gegevensset selecteren) en [Split Data][split] (Gegevens splitsen) kolomdefinities doorgeven aan de modules die we hierna zullen toevoegen.  

3. Als u een leeralgoritme wilt selecteren, moet u de categorie **Machine Learning** in het modulepalet links van het canvas uitvouwen en vouwt u vervolgens **Initialize Model** (Model initialiseren) uit. Er worden verschillende categorieën weergegeven die kunnen worden gebruikt om de machine learning-algoritmen te initialiseren.

    Selecteer voor dit experiment de module [Linear Regression][linear-regression] (Lineaire regressie) in de categorie **Regression** (Regressie) (u kunt de module ook zoeken door 'linear regression' in het zoekvak van het palet te typen) en sleep de module naar het experimentcanvas.

4. Zoek de module [Train Model][train-model] (Model trainen) en sleep deze naar het experimentcanvas. Koppel de linkerinvoerpoort aan de uitvoer van de module [Linear Regression][linear-regression] (Lineaire regressie). Koppel de rechteruitvoerpoort aan de trainingsgegevensuitvoer (linkerpoort) van de module [Split Data][split] (Gegevens splitsen).

5. Selecteer de module [Train Model][train-model] (Model trainen), klik in het deelvenster **Properties** (Eigenschappen) op **Launch column selector** (Kolomselector starten) en selecteer vervolgens de kolom **price** (prijs). Dit is de waarde die door het model wordt voorspeld.

    ![De kolom 'price' (prijs) selecteren][screen7]

6. Voer het experiment uit.

Het resultaat is een getraind regressiemodel dat kan worden gebruikt om nieuwe samples te beoordelen voor het maken van voorspellingen.

![Het machine learning-algoritme toepassen][screen8]

## Stap 5: prijzen van nieuwe auto's voorspellen

Nu we het model met 75 procent van de gegevens hebben getraind, kunnen we het model gebruiken om de overige 25 procent van onze gegevens te beoordelen om te zien hoe goed het model werkt.

1. Zoek de module [Score Model][score-model] (Scoremodel), sleep deze naar het experimentcanvas en koppel de module aan de linkerinvoerpoort van de module [Train Model][train-model] (Model trainen). Koppel de rechterinvoerpoort om de gegevensuitvoer (rechterpoort) van de module [Split Data][split] (Gegevens splitsen) te testen.  

    ![De module Score Model (Scoremodel)][screen8a]

2. Als u het experiment wilt uitvoeren en de uitvoer van de module [Score Model][score-model] (Scoremodel) wilt weergeven, klikt u op de uitvoerpoort en selecteert u **Visualize** (Visualiseren). De uitvoer bevat de voorspelde waarden voor de prijs en de bekende waarden uit de testgegevens.  

3. Als u tot slot de kwaliteit van de resultaten wilt testen, selecteert u de module [Evaluate Model][evaluate-model] (Model evalueren), sleept u deze naar het experimentcanvas en koppelt u de module aan de linkerinvoerpoort van de module [Score Model][score-model] (Scoremodel). (Er zijn twee invoerpoorten omdat de module [Evaluate Model][evaluate-model] (Model evalueren) kan worden gebruikt om twee modellen met elkaar te vergelijken.)

4. Voer het experiment uit.

Als u de uitvoer van de module [Evaluate Model][evaluate-model] (Model evalueren) wilt weergeven, klikt u op de uitvoerpoort en selecteert u **Visualize** (Visualiseren). De volgende statistieken worden weergegeven voor het model:

- **Mean Absolute Error** (MAE): het gemiddelde aan absolute fouten (een *fout* is het verschil tussen de voorspelde waarde en de werkelijke waarde).
- **Root Mean Squared Error** (RMSE): de wortel uit het gemiddelde aan gekwadrateerde fouten voor de voorspellingen op basis van de testgegevensset.
- **Relative Absolute Error**: het gemiddelde aan absolute fouten ten opzichte van het absolute verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
- **Relative Squared Error**: het gemiddelde aan gekwadrateerde fouten ten opzichte van het gekwadrateerde verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
- **Coefficient of Determination**: de determinatiecoëfficiënt, ook wel **R²-waarde** genoemd, is een statistische meetwaarde die aangeeft hoe goed het model is in het voorspellen van de gegevens.

Voor elk van de foutstatistieken geldt: hoe kleiner hoe beter. Een kleine waarde geeft aan dat de voorspelling dichter bij de werkelijke waarde ligt. Hoe dichter de **determinatiecoëfficiënt** bij één (1.0) ligt, hoe nauwkeuriger de voorspellingen.

![Evaluatieresultaten][screen9]

Het laatste experiment ziet er als volgt uit:

![Machine learning-zelfstudie: volledig lineair regressie-experiment waarbij gebruik wordt gemaakt van voorspellende modelleringstechnieken.][screen10]

## Volgende stappen

Nu u de aanvankelijke machine learning-zelfstudie hebt voltooid en u een experiment hebt gemaakt, kunt u het experiment herhalen om het model te verbeteren. U kunt bijvoorbeeld de kenmerken wijzigen die u voor uw voorspelling gebruikt. Of u kunt de eigenschappen wijzigen van het algoritme [Linear Regression][linear-regression] (Lineaire regressie) of een compleet ander algoritme proberen. U kunt zelfs meerdere machine learning-algoritmen tegelijk aan uw experiment toevoegen en de module [Evaluate Model][evaluate-model] (Model evalueren) gebruiken om ze te vergelijken.

> [AZURE.TIP] Gebruik de knop **SAVE AS** (OPSLAAN ALS) onder het experimentcanvas om herhalingen van uw experiment op te slaan. U kunt alle herhalingen van uw experiment weergeven door onder het canvas te klikken op **VIEW RUN HISTORY** (UITVOERINGSGESCHIEDENIS WEERGEVEN). Zie [Experimentherhalingen in Azure Machine Learning Studio beheren][runhistory] voor meer informatie.

[runhistory]: machine-learning-manage-experiment-iterations.md

Als u tevreden bent over het model, kunt u dit implementeren als een webservice om de prijzen van auto's te voorspellen op basis van nieuwe gegevens. Zie [Een Azure Machine Learning-webservice implementeren][publish] voor meer informatie.

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Zie [Een voorspellende oplossing met Azure Machine Learning ontwikkelen][walkthrough] voor een uitgebreid en gedetailleerd overzicht van de voorspellende modelleringstechnieken die kunnen worden gebruikt om een model te maken, te trainen, te beoordelen en te implementeren.

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[scherm1]:./media/machine-learning-create-experiment/screen1.png
[scherm2]:./media/machine-learning-create-experiment/screen1a.png
[scherm1b]:./media/machine-learning-create-experiment/screen1b.png
[scherm2]:./media/machine-learning-create-experiment/screen2.png
[scherm3]:./media/machine-learning-create-experiment/screen3.png
[scherm4]:./media/machine-learning-create-experiment/screen4.png
[scherm4a]:./media/machine-learning-create-experiment/screen4a.png
[scherm5]:./media/machine-learning-create-experiment/screen5.png
[scherm6]:./media/machine-learning-create-experiment/screen6.png
[scherm7]:./media/machine-learning-create-experiment/screen7.png
[scherm8]:./media/machine-learning-create-experiment/screen8.png
[scherm8a]:./media/machine-learning-create-experiment/screen8a.png
[scherm9]:./media/machine-learning-create-experiment/screen9.png
[scherm10]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/



<!--HONumber=Jun16_HO2-->


