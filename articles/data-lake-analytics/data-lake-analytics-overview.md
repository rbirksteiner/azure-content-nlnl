<properties 
   pageTitle="Overzicht van Microsoft Azure Data Lake Analytics | Azure" 
   description="Data Lake Analytics is een Azure Big Data-rekenservice waarmee u de inzichten die u verkrijgt uit uw gegevens in de cloud, kunt gebruiken voor uw bedrijfsvoering - ongeacht de locatie en omvang van die gegevens. Data Lake Analytics biedt de eenvoudigste, voordeligste en meest schaalbare manier om dit te bereiken. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/26/2016"
   ms.author="edmaca"/>

# Overzicht van Microsoft Azure Data Lake Analytics

## Wat is Azure Data Lake Analytics?

Azure Data Lake Analytics is een nieuwe service die is bedoeld om big data-analyses gemakkelijk te maken. Met deze service kunt u zich richten op het schrijven, uitvoeren en beheren van taken, in plaats van op het beheren van een gedistribueerde infrastructuur. U hoeft geen hardware te implementeren, configureren en optimaliseren, maar kunt query’s schrijven om uw data te transformeren en waardevolle inzichten te verkrijgen. De analyseservice kan taken van elke schaal onmiddellijk verwerken, door eenvoudigweg de hoeveelheid rekenkracht die u nodig hebt aan te passen. U betaalt alleen voor de tijd die nodig is voor het uitvoeren van uw taak, wat het gebruik van deze service zeer rendabel maakt. De analyseservice biedt ondersteuning voor Azure Active Directory voor eenvoudig beheer van toegang en rollen, en integratie met uw on-premises identiteitsbeheersysteem. Het omvat ook U-SQL, een taal die de voordelen van SQL combineert met de expressieve voordelen van gebruikerscode. De schaalbare gedistribueerde runtime van U-SQL kunt u gebruiken om op een efficiënte manier gegevens te analyseren in de Store en op alle SQL-servers in Azure, Azure SQL Database en Azure SQL Data Warehouse.


## Belangrijkste mogelijkheden

- **Dynamische schaalbaarheid** 

    Data Lake Analytics is ontworpen voor schaalbaarheid en prestaties in de cloud.  De service voorziet op dynamische wijze in resources en kan worden gebruikt voor het analyseren van terabytes - of zelfs exabytes - aan gegevens. Wanneer de taak is voltooid, wordt het aantal resources automatisch teruggedraaid. U betaalt alleen voor de verwerkingskracht die u daadwerkelijk hebt gebruikt. Bij het vergroten of verkleinen van de hoeveelheid opgeslagen gegevens of gebruikte rekenkracht, hoeft u geen code te herschrijven. Hierdoor kunt u zich richten op uw bedrijfslogica in plaats van op het verwerken en opslaan van omvangrijke gegevenssets. 

- **Sneller ontwikkelen, slimmer fouten opsporen en gemakkelijker optimaliseren met vertrouwde hulpprogramma 's**

    Data Lake Analytics is in hoge mate geïntegreerd met Visual Studio, zodat u vertrouwde hulpprogramma’s kunt gebruiken voor het uitvoeren en optimaliseren van uw code en voor het opsporen van fouten daarin. Met visualisaties van uw U-SQL-taken ziet u hoe uw code wordt uitgevoerd op de gewenste schaal voor het eenvoudig identificeren van knelpunten en het besparen van kosten. 

- **U-SQL: eenvoudig en vertrouwd, krachtig en uitbreidbaar**

    Data Lake Analytics bevat U-SQL, een querytaal die de vertrouwde en eenvoudige declaratieve aard van SQL uitbreidt met de expressieve voordelen van C#. De U-SQL-taal is gebouwd op de dezelfde gedistribueerde runtime die de basis vormt van de big-datasystemen binnen Microsoft. Miljoenen SQL- en .NET-ontwikkelaars kunnen nu hun gegevens verwerken en analyseren met de vaardigheden die ze al hebben.

- **Moeiteloze integratie met uw IT-investeringen**

    Met Data Lake Analytics kunt u uw bestaande IT-investeringen gebruiken voor identiteitsservices, beheer, beveiliging en datawarehousing. Dit vereenvoudigt het gegevensbeheer en maakt het gemakkelijk om uw huidige gegevenstoepassingen uit te breiden. Data Lake Analytics is geïntegreerd met Active Directory voor het beheer van gebruikers en machtigingen en biedt ingebouwde functionaliteit voor bewaking en controle.

- **Betaalbaar en rendabel**

    Data Lake Analytics is een rendabele oplossing voor het uitvoeren van big data-workloads. U betaalt per taak wanneer de gegevens worden verwerkt. Hardware, licenties en servicespecifieke ondersteuningsovereenkomsten zijn niet nodig. Het systeem schaalt automatisch op en af bij het starten en eindigen van de taak, zodat u nooit betaalt voor meer dan u nodig hebt. 

- **Werkt met alle Azure-gegevens**

    Data Lake Analytics werkt met meerdere Azure-gegevensbronnen: Azure Blob-opslag, Azure SQL Database en natuurlijk Data Lake Analytics, dat speciaal is geoptimaliseerd voor samenwerking met Azure Data Lake Store. Dat garandeert maximale prestaties en optimale doorvoer en parallelle uitvoering van uw big-dataworkloads.

## Zie ook

- Aan de slag
    - [Aan de slag met Data Lake Analytics met Azure Portal](data-lake-analytics-get-started-portal.md)
    - [Aan de slag met Data Lake Analytics met Azure PowerShell](data-lake-analytics-get-started-powershell.md)
    - [Aan de slag met Data Lake Analytics met Azure .NET SDK](data-lake-analytics-get-started-net-sdk.md)
    - [U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Aan de slag met de Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL en ontwikkelen
    - [Aan de slag met de Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md)
    - [U-SQL-vensterfuncties gebruiken voor Azure Data Lake Analytics-taken](data-lake-analytics-use-window-functions.md)
    - [Door de gebruiker gedefinieerde U-SQL-operators ontwikkelen voor Data Lake Analytics-taken](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- Beheer
    - [Azure Data Lake Analytics beheren met Azure Portal](data-lake-analytics-manage-use-portal.md)
    - [Azure Data Lake Analytics beheren met Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
    - [Azure Data Lake Analytics-taken bewaken en problemen oplossen met Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- End-to-end zelfstudie
    - [Interactieve zelfstudies voor Azure Data Lake Analytics gebruiken](data-lake-analytics-use-interactive-tutorials.md)
    - [Websitelogboeken analyseren met Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)

- Laat ons weten wat u ervan vindt
    - [Geef feedback over onze documentatieachterstand](data-lake-analytics-documentation-backlog.md)
    - [Een functieaanvraag indienen](http://aka.ms/adlafeedback)
    - [Hulp krijgen in de forums](http://aka.ms/adlaforums)





<!--HONumber=Jun16_HO2-->


