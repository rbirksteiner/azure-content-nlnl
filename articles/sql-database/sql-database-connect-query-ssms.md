<properties
    pageTitle="Verbinding maken met SQL Database: SQL Server Management Studio | Microsoft Azure"
    description="Ontdek hoe u verbinding maakt met SQL Database in Azure met behulp van SQL Server Management Studio (SSMS). Voer een voorbeeldquery uit met Transact-SQL (T-SQL)."
    metaCanonical=""
    keywords="connect to sql database,sql server management studio"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="" />

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sstein;carlrab" />

# Verbinding maken met SQL Database met SQL Server Management Studio en een voorbeeld T-SQL-query uitvoeren

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Dit artikel laat zien hoe u verbinding maakt met een Azure SQL Database met behulp van de meest recente versie van SQL Server Management Studio (SSMS) en hoe u een eenvoudige query uitvoert met (T-SQL) Transact-SQL-instructies.

[AZURE.INCLUDE [Sign in](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

[AZURE.INCLUDE [SSMS Connect](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

Zie voor meer informatie over firewallregels [de uitleg bij Firewall-instellingen configureren (Azure SQL Database)](sql-database-configure-firewall-settings.md).

## Voorbeeldquery‘s uitvoeren

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

    ![Geslaagd. Verbinding maken met de SQL Database-server: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Volgende stappen

U kunt de T-SQL-instructies gebruiken om databases te maken en te beheren in Azure op ongeveer dezelfde manier als dat kan bij SQL Server. Als u vertrouwd bent met T-SQL met SQL Server, raadpleegt u [Azure SQL Database Transact-SQL-informatie](sql-database-transact-sql-information.md) voor een overzicht van verschillen.

Als u geen ervaring hebt met T-SQL, raadpleegt u de [Zelfstudie: Transact-SQL-instructies schrijven](https://msdn.microsoft.com/library/ms365303.aspx) en het [Transact-SQL-naslagmateriaal (Database-engine)](https://msdn.microsoft.com/library/bb510741.aspx).

Om aan de slag te gaan met het maken van databasegebruikers en databasegebruikersbeheerders raadpleegt u [Aan de slag met Azure SQL Database-beveiliging](sql-database-get-started-security.md)



<!--HONumber=Jun16_HO2-->


