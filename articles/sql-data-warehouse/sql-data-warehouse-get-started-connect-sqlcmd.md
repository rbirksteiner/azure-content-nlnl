<properties
   pageTitle="Aan de slag: Verbinding maken met Azure SQL Data Warehouse | Microsoft Azure"
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
   ms.date="05/16/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Verbinding maken en query's uitvoeren met SQLCMD

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)


In deze procedure wordt beschreven hoe u met het hulpprogramma sqlcmd.exe in slechts enkele minuten verbinding maakt met een Azure SQL Data Warehouse-database en query’s uitvoert. In deze procedure gaat u het volgende doen:

+ Vereiste software installeren
+ Verbinding maken met een database die de voorbeelddatabase AdventureWorksDW bevat
+ Een query op de database uitvoeren  

## Vereisten

+ Raadpleeg [Microsoft Command Line Utilities 11 voor SQL Server][] voor het downloaden van [sqlcmd.exe][].

## De volledig gekwalificeerde naam van de Azure SQL-server ophalen

Om verbinding te maken met de database hebt u de volledige naam nodig van de server (***servernaam**.database.windows.net*) die de database bevat waarmee u verbinding wilt maken.

1. Ga naar de [Azure Portal][].
2. Blader naar de database waarmee u verbinding wil maken.
3. Zoek de volledige servernaam (die gaan we in de onderstaande stappen gebruiken):

![][1]


## Verbinding maken met SQL Data Warehouse met sqlcmd

Als u een specifiek exemplaar van SQL Data Warehouse wilt verbinden wanneer u sqlcmd gebruikt, moet u de opdrachtprompt openen en **sqlcmd** invoeren, gevolgd door de verbindingsreeks voor de SQL Data Warehouse-database. De verbindingsreeks heeft de volgende parameters nodig:

+ **Server (-S):** server in de notatie `<`servernaam`>`.database.windows.net
+ **Database (-d):** databasenaam.
+ **Gebruiker (-U):** servergebruiker in de notatie `<`gebruiker`>`
+ **Wachtwoord (-P):** wachtwoord dat is gekoppeld aan de gebruiker.
+ **ID's tussen aanhalingstekens inschakelen (-I):** id's tussen aanhalingstekens moeten zijn ingeschakeld om verbinding te kunnen maken met een exemplaar van SQL Data Warehouse.

Als u verbinding wilt maken met een exemplaar van SQL Data Warehouse, zou u dus het volgende invoeren:

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## Voorbeeldquery's uitvoeren

Wanneer verbinding is gemaakt, kunt u elke ondersteunde Transact-SQL-instructie voor het exemplaar uitvoeren.

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Raadpleeg de [sqlcmd-documentatie][sqlcmd.exe] voor meer informatie over sqlcmd.


## Volgende stappen

Nu u weet hoe u verbinding maakt en een query uitvoert, kunt u proberen [verbinding te maken met Power BI][].

Als u uw omgeving wilt configureren voor Windows-verificatie raadpleegt u [Verbinding maken met SQL Database of SQL Data Warehouse met behulp van Azure Active Directory-verificatie][].

<!--Articles-->
[Verbinding maken met SQL Database of SQL Data Warehouse met behulp van Azure Active Directory-verificatie]: ../sql-data-warehouse/sql-data-warehouse-get-started-connect-aad-authentication.md
[verbinding te maken met Power BI]: ./sql-data-warehouse-integrate-power-bi.md
[Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[SQLCMD]: ./sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/en-us/library/ms162773.aspx
[Microsoft Command Line Utilities 11 voor SQL Server]: http://go.microsoft.com/fwlink/?LinkId=321501
[Azure Portal]: https://portal.azure.com

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png



<!--HONumber=Jun16_HO2-->


