<properties
    pageTitle="Verbinding maken met SQL Database met een C#-query | Microsoft Azure"
    description="Schrijf een programma in C# om een SQL Database op te vragen en hiermee verbinding te maken. Informatie over IP-adressen, verbindingsreeksen, beveiligde aanmelding en gratis Visual Studio."
    services="sql-database"
    keywords="c# database query, c# query, connect to database, SQL C#"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="04/25/2016"
    ms.author="annemill"/>


# Verbinding maken met een SQL Database met behulp van Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Ontdek hoe u verbinding kunt maken met een Azure SQL Database in Visual Studio. 

## Vereisten


Voor verbinding met SQL Database met Visual Studio, heeft u het volgende nodig: 


- Een Azure-account en -abonnement. U  kunt zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/). 


- Een **AdventureWorksLT**-demonstratiedatabase op de Azure SQL Database-service.
 - [Maak een demonstratiedatabase](sql-database-get-started.md) in slechts enkele minuten.


- Visual Studio 2013 update 4 (of hoger). Microsoft biedt de Visual Studio Community nu *gratis* aan.
 - [Visual Studio Community downloaden](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Meer opties voor gratis Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - Verderop in dit onderwerp wordt ook [uitgelegd](#InstallVSForFree) hoe het [Azure Portal](https://portal.azure.com/) u helpt bij het installeren van Visual Studio.


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## Stap 1: Installeer gratis Visual Studio Community 


Als u Visual Studio wilt installeren, kunt u:

- Visual Studio Community gratis installeren door in uw browser te zoeken naar webpagina’s met gratis downloads en andere opties voor Visual Studio-producten; of
- via de [Azure Portal](https://portal.azure.com/) navigeren naar de webpagina met de download, zoals hieronder staat uitgelegd.


### Visual Studio via de Azure Portal


1. Meld u aan bij de [Azure Portal](https://portal.azure.com/), http://portal.azure.com/.

2. Klik op **BLADEREN* ALLE** > **SQL databases**. Er wordt een blade geopend waarin wordt gezocht naar databases.

3. Typ de naam van uw **AdventureWorksLT** database in het tekstvak bovenin om de zoekopdracht te filteren.

4. Wanneer u de rij van uw database op uw server ziet, klikt u op die rij. Er wordt een blade geopend voor uw database.

5. U kunt voor het gemak alle vorige blades minimaliseren.

6. Klik op de knop **Openen in Visual Studio** bovenaan uw databaseblade. Een nieuwe blade over Visual Studio wordt geopend met links naar installatielocaties voor Visual Studio.

    ![De knop Openen in Visual Studio][20-OpenInVisualStudioButton]

7. Klik op de link **Community (gratis)**, of een dergelijke link. Er wordt een nieuwe webpagina toegevoegd.

8. Gebruik de links op de nieuwe webpagina om Visual Studio te installeren.

9. Nadat Visual Studio is geïnstalleerd, klikt u op de knop  **Openen in Visual Studio** in de blade **Openen in Visual Studio** Visual Studio wordt geopend.

10. Omwille van het venster **SQL Server Object Explorer**, vraagt Visual Studio u om de velden voor de verbindingsreeks in te vullen in een dialoogvenster.
 - Kies voor **SQL Server-verificatie**, niet voor **Windows-verificatie**.
 - Geef uw **AdventureWorksLT** database op (**Opties** > **Verbindingseigenschappen** in het dialoogvenster).

11. Vouw de knooppunten voor uw database in de **SQL Server Object Explorer** uit.


## Stap 2: voer een voorbeeldquery uit

Nadat u verbinding hebt gemaakt met uw logische server, kunt u verbinding maken met een database en een voorbeeldquery uitvoeren. 

1. Navigeer in **Object Explorer** naar een database op de server waarvoor u gemachtigd bent, zoals de **AdventureWorks**-voorbeelddatabase.
2. Klik met de rechtermuisknop op de database en selecteer **Nieuwe query**.

    ![Nieuwe query. Verbinding maken met de SQL Database-server: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. Kopieer en plak de volgende code in het queryvenster.

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. Klik op de knop **Uitvoeren**.  In het volgende screenshot staat een voorbeeld van een geslaagde query.

    ![Geslaagd. Verbinding maken met de SQL Database-server: SVisual Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Volgende stappen

[Verbinding maken met SQL Database met behulp van .NET (C#)](sql-database-develop-dotnet-simple.md) 


<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png




<!--HONumber=Jun16_HO2-->


