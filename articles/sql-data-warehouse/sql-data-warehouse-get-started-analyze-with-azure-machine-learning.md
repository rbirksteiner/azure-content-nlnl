<properties
   pageTitle="Gegevens analyseren met Azure Machine Learning | Microsoft Azure"
   description="Zelfstudie voor het gebruik van Azure Machine Learning met Azure SQL Data Warehouse om oplossingen te ontwikkelen."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="shivaniguptamsft"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/18/2016"
   ms.author="shigu;barbkess;sonyama"/>

# Gegevens analyseren met Azure Machine Learning

> [AZURE.SELECTOR]
- [Power BI][]
- [Azure Machine Learning][]

In deze zelfstudie leert u hoe u met Azure Machine Learning een voorspellend Machine Learning-model maakt op basis van uw Azure SQL Data Warehouse-gegevens. In deze zelfstudie maakt u een gerichte marketingcampagne voor Adventure Works, de fietsenwinkel, door te voorspellen of een klant een fiets waarschijnlijk wel of niet zal kopen.

> [AZURE.VIDEO integrating-azure-machine-learning-with-azure-sql-data-warehouse]

## Vereisten
Voor deze zelfstudie hebt u het volgende nodig:

- SQL Data Warehouse met voorbeelddatabase AdventureWorksDW.

In [Een SQL Data Warehouse maken][] ziet u hoe u een database inricht met voorbeeldgegevens. Als u wel een SQL Data Warehouse-database hebt maar nog geen voorbeeldgegevens, dan kunt u [handmatig voorbeeldgegevens laden][].


## Stap 1: Gegevens ophalen
U leest de gegevens uit de weergave dbo.vTargetMail in de database AdventureWorksDW.

1. Meld u aan bij [Azure Machine Learning Studio][] en klik op My experiments.
2. Klik op **+NEW** (Nieuw) en selecteer **Blank Experiment** (Leeg experiment).
3. Voer een naam in voor uw experiment: Targeted Marketing.
4. Sleep de module **Reader** van het deelvenster met modules naar het canvas.
5. Geef de details van uw SQL Data Warehouse-database op in het deelvenster Properties.
6. Geef de database**query** op om de gewenste gegevens te lezen.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Voer het experiment uit door onder experimentencanvas op **RUN** (UITVOEREN) te klikken.
![Het experiment uitvoeren][1]


Wanneer het experiment is uitgevoerd, klikt u op de uitvoerpoort onder in de module Reader en selecteert u **Visualize** (Visualiseren) om de geïmporteerde gegevens te zien.
![Geïmporteerde gegevens weergeven][3]


## Stap 2: Gegevens wissen
Nu gaat u kolommen verwijderen die niet relevant zijn voor het model.

1. Sleep de module **Project Columns** (Projectkolommen) naar het canvas.
2. Klik in het deelvenster Properties (Eigenschappen) op **Launch column selector** (Kolomselectie starten) om de kolommen op te geven die u wilt verwijderen.
![Projectkolommen][4]

3. Sluit twee kolommen uit: CustomerAlternateKey en GeographyKey.
![Overbodige kolommen verwijderen][5]


## Stap 3: model maken
U gaat de gegevens 80-20 splitsen: 80% om een Machine Learning-model te trainen en 20% om het model te testen. Voor dit binair klassificatieprobleem gaat u de algoritme Two-Class gebruiken.

1. Sleep de module **Split** (Splitsen) naar het canvas.
2. Typ 0,8 in Fraction of rows in the first output dataset (Fractie van rijen in de eerste gegevensset) in het deelvenster Properties (Eigenschappen).
![Gegevens splitsen in trainings- en testset][6]
3. Sleep de module **Two-Class Boosted Decision Tree** (Beslissingsstructuur op basis van twee klassen) naar het canvas.
4. Sleep de module **Train Model** (Model trainen) naar het canvas en geef de invoer op. Klik vervolgens in het deelvenster Properties (Eigenschappen) op **Launch column selector** (Kolomselectie starten).
      - Eerste invoer: ML-algoritme.
      - Tweede invoer: de gegevens waarmee u het algoritme wilt trainen.
![Verbinding maken met de module Train Model (Model trainen)][7]
5. Selecteer de kolom **BikeBuyer** als de kolom die u wilt voorspellen.
![Te voorspellen kolom selecteren][8]


## Stap 4: model beoordelen
Nu gaat u testen hoe het model functioneert met testgegevens. U gaat het gekozen algoritme vergelijken met een ander algoritme om te zien welk algoritme de beste prestaties levert.

1. Sleep de module **Score Model** (Model beoordelen) naar het canvas.
    Eerste invoer: getraind model Tweede invoer: testgegevens ![Het model beoordelen][9]
2. Sleep de **Two-Class Bayes Point Machine** naar het experimentencanvas. U gaat dit algoritme vergelijken met de Two-Class Boosted Decision Tree (Beslissingsstructuur met twee klassen).
3. Kopieer en plak de modules Train Model en Score Model naar het canvas.
4. Sleep het model **Evaluate Model** (Model evalueren) naar het canvas om de twee algoritmen te vergelijken.
5. **Voer het experiment uit**.
![Het experiment uitvoeren][10]
6. Klik op de uitvoerpoort onder in de module Evaluate Model (Model evalueren) en klik op Visualize (Visualiseren).
![Evaluatieresultaten visualiseren][11]

De verstrekte metrische gegevens zijn de ROC-curve, het precisie-/oproepdiagram en de liftcurve. Als u deze metrische gegevens bekijkt, ziet u dat het eerste model beter presteert dan het tweede. Als u de voorspellingen van het eerste model wilt zien, klikt u op de uitvoerpoort van de module Score Model en op Visualize (Visualiseren).
![Scoreresultaten visualiseren][12]

U ziet dat er twee of meer kolommen aan de testgegevensset zijn toegevoegd.

- Scored Probabilities (Berekende kansen): de waarschijnlijkheid dat een klant een fiets koopt.
- Scored Labels (Berekende Labels): de classificatie die door het model is uitgevoerd: fietskoper (1) of niet (0). Deze drempelwaarde voor waarschijnlijkheid voor labeling is ingesteld op 50% en kan worden aangepast.

Door de kolom BikeBuyer (werkelijk) te vergelijken met de kolom Scored Labels (voorspelling), ziet u hoe goed het model heeft gepresteerd. In de volgende stappen kunt u dit model gebruiken om voorspellingen te doen voor nieuwe klanten, en dit model publiceren als een webservice of resultaten opslaan in SQL Data Warehouse.

## Volgende stappen

Raadpleeg [Inleiding tot Machine Learning in Azure][] voor meer informatie over het bouwen van voorspellende Machine Learning-modellen.

<!--Image references-->
[1]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure Machine Learning Studio]:https://studio.azureml.net/
[Inleiding tot Machine Learning in Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[handmatig voorbeeldgegevens laden]: sql-data-warehouse-get-started-manually-load-samples.md
[Een SQL Data Warehouse maken]: sql-data-warehouse-get-started-provision.md
[Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Azure Machine Learning]: ./sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md



<!--HONumber=Jun16_HO2-->


