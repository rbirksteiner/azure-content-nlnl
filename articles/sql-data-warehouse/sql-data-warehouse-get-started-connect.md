<properties
   pageTitle="Verbinding maken met SQL Data Warehouse met Visual Studio | Microsoft Azure"
   description="Ga aan de slag door verbinding te maken met SQL Data Warehouse en enkele query's uit te voeren."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/13/2016"
   ms.author="sonyama;barbkess"/>

# Verbinding maken met SQL Data Warehouse met Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)

In deze procedure ziet u hoe u met behulp van de extensie SSDT (SQL Server Data Tools) in Visual Studio in slechts enkele minuten verbinding maakt met een Azure SQL Data Warehouse. Wanneer de verbinding is gemaakt, kunt u een eenvoudige query uitvoeren.

## Vereisten

+ Voorbeelddatabase AdventureWorksDW in SQL Data Warehouse. Zie [Een SQL Data Warehouse maken][] als u wilt weten hoe u dit doet.
+ SQL Server Data Tools voor Visual Studio. Voor installatie-instructies en -opties raadpleegt u [Visual Studio en SSDT installeren][].

## Stap 1: De volledig gekwalificeerde naam van de Azure SQL-server zoeken

De SQL Data Warehouse-database is gekoppeld aan een Azure SQL Server. Om verbinding te maken met uw database, hebt u de volledig gekwalificeerde naam van de server (**servernaam**.database.windows.net*) nodig.

Ga als volgt te werk om de volledig gekwalificeerde servernaam te vinden.

1. Ga naar de [Azure Portal][].
2. Klik op **SQL-databases** en vervolgens op de database waarmee u verbinding wilt maken. In dit voorbeeld wordt de voorbeelddatabase AdventureWorksDW gebruikt.
3. Zoek de volledige servernaam.

    ![Volledige servernaam][1]

## Stap 2: verbinding maken met uw SQL Data Warehouse

1. Open Visual Studio 2013 of 2015.
2. Open SQL Server-objectverkenner. Daartoe selecteert u **View** > **SQL Server Object Explorer**.

    ![SQL Server-objectverkenner][2]

3. Klik op het pictogram **SQL Server toevoegen**.

    ![SQL Server toevoegen][3]

4. Vul de velden in het venster Connect to Server (Verbinding maken met server) in.

    ![Verbinding maken met server][4]

    - **Server name** (Servernaam). Voer de eerder vastgestelde **servernaam** in.
    - **Authentication** (Verificatie). Selecteer **SQL Server Authentication** (SQL Server-verificatie) of **Active Directory Integrated Authentication** (Geïntegreerde Active Directory-verificatie).
    - **User Name** (Gebruikersnaam) en **Password** (Wachtwoord). Voer de gebruikersnaam en het wachtwoord in als u hierboven SQL Server-verificatie hebt geselecteerd.
    - Klik op **Connect** (Verbinden).

5. U kunt de Azure SQL-server uitvouwen als u deze wilt verkennen. U kunt de databases weergeven die aan de server zijn gekoppeld. Vouw AdventureWorksDW uit als u de tabellen in de voorbeelddatabase wilt zien.

    ![AdventureWorksDW verkennen][5]

## Stap 3: Een voorbeeldquery uitvoeren

Nu er een verbinding met uw database is ingesteld, gaat u een query schrijven.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.

2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.

    ![Nieuwe query][6]

3. Kopieer de volgende TSQL-query naar het queryvenster:

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Voer de query uit. Daartoe klikt u op de groene pijl of gebruikt u de volgende snelkoppeling: `CTRL`+`SHIFT`+`E`.

    ![Query uitvoeren][7]

5. Bekijk de resultaten van de query. In dit voorbeeld heeft de tabel FactInternetSales 60398 rijen.

    ![Queryresultaten][8]

## Volgende stappen

Nu u weet hoe u verbinding maakt en een query uitvoert, kunt u proberen [de gegevens te visualiseren met Power BI][].

Als u uw omgeving wilt configureren voor Windows-verificatie raadpleegt u [Verbinding maken met SQL Database of SQL Data Warehouse met behulp van Azure Active Directory-verificatie][].

<!--Arcticles-->
[Een SQL Data Warehouse maken]: sql-data-warehouse-get-started-provision.md
[Visual Studio en SSDT installeren]: sql-data-warehouse-install-visual-studio.md
[Verbinding maken met SQL Database of SQL Data Warehouse met behulp van Azure Active Directory-verificatie]: ../sql-data-warehouse/sql-data-warehouse-get-started-connect-aad-authentication.md
[de gegevens te visualiseren met Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure Portal]: https://portal.azure.com

<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png



<!--HONumber=Jun16_HO2-->


