<properties
   pageTitle="Gegevens uit SQL Server laden in Azure SQL Data Warehouse (BCP) | Microsoft Azure"
   description="Voor kleine gegevenssets gebruikt u BCP om gegevens uit SQL Server te exporteren naar platte bestanden en de gegevens rechtstreeks in Azure SQL Data Warehouse te importeren."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# Gegevens uit SQL Server laden in Azure SQL Data Warehouse (platte bestanden)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [BCP](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Voor kleine gegevenssets kunt u het opdrachtregelprogramma BCP gebruiken om gegevens uit SQL Server te exporteren en deze vervolgens rechtstreeks in Azure SQL Data Warehouse te laden.

In deze zelfstudie gebruikt u BCP voor het volgende:

- Een tabel uit SQL Server exporteren met behulp van de opdracht out in BCP (of een eenvoudig voorbeeldbestand maken)
- De tabel uit een plat bestand importeren in SQL Data Warehouse.
- Statistieken maken voor de geladen gegevens.

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## Voordat u begint

### Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- Een SQL Data Warehouse-database
- Het opdrachtregelprogramma BCP (moet zijn geïnstalleerd)
- Het opdrachtregelprogramma SQLCMD (moet zijn geïnstalleerd)

U kunt de opdrachtregelprogramma's BCP en SQLCMD downloaden van het [Microsoft Downloadcentrum][].

### Gegevens in ASCII- of UTF-16-indeling

Als u deze zelfstudie wilt uitvoeren met uw eigen gegevens, moeten deze zijn gecodeerd in de ASCII- of UTF-16-indeling, omdat de indeling UTF-8 niet wordt ondersteund in BCP. 

UTF-8 wordt wel ondersteund in PolyBase, maar UTF-16 nog niet. Als u BCP wilt gebruiken in combinatie met PolyBase, moet u de gegevens na het exporteren uit SQL Server transformeren naar UTF-8. 


## 1. Een doeltabel maken

Definieer een (doel)tabel in SQL Data Warehouse waarin u de gegevens wilt laden. De kolommen in de tabel moeten overeenkomen met de gegevens in elke rij van het gegevensbestand.

Open een opdrachtprompt en voer de volgende opdracht uit met sqlcmd.exe om een tabel te maken:


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```


## 2. Een brongegevensbestand maken

Open Kladblok, kopieer de volgende regels met gegevens naar een nieuw tekstbestand en sla dit bestand op in de lokale tijdelijke map C:\Temp\DimDate2.txt. Dit zijn gegevens in ASCII-indeling.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Optioneel) Open een opdrachtprompt en voer de volgende opdracht uit om uw eigen gegevens uit een SQL Server-database te exporteren. Vervang TableName, ServerName, DatabaseName, Username en Password door uw eigen waarden.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## 3. De gegevens laden
Open een opdrachtprompt en voer de volgende opdracht uit om de gegevens te laden. Vervang de waarden voor ServerName, DatabaseName, Username en Password door uw eigen waarden.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Gebruik deze opdracht om te controleren of de gegevens correct zijn geladen

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

De resultaten horen er als volgt uit te zien:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

## 4. Statistieken maken

SQL Data Warehouse bevat nog geen functionaliteit voor het automatisch maken of bijwerken van statistieken. Voor optimale resultaten van uw query's is het belangrijk dat u statistieken maakt voor alle kolommen van alle tabellen nadat de gegevens voor het eerst zijn geladen of wanneer de gegevens substantieel zijn gewijzigd. Zie [statistieken][] voor gedetailleerde uitleg van statistieken. 

Voer de volgende opdracht uit om statistieken te maken voor uw zojuist geladen tabel.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## 5. Gegevens uit SQL Data Warehouse exporteren
Desgewenst kunt u de gegevens die u zojuist hebt geladen, weer uit SQL Data Warehouse exporteren.  De opdracht voor het exporteren is exact hetzelfde als die voor het exporteren uit SQL Server.

De resultaten zijn echter verschillend. Omdat de gegevens zijn opgeslagen op gedistribueerde locaties in SQL Data Warehouse, worden de gegevens van elk rekenknooppunt bij het exporteren weggeschreven naar het uitvoerbestand. De volgorde van de gegevens in het uitvoerbestand is waarschijnlijk anders dan de volgorde van de gegevens in het invoerbestand.

### Een tabel exporteren en exportresultaten vergelijken

Om de geëxporteerde gegevens te bekijken, opent u een opdrachtprompt en voert u deze opdracht uit met uw eigen parameters. ServerName is de naam van uw logische Azure SQL-server.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
U kunt controleren of de gegevens correct zijn geëxporteerd door het nieuwe bestand te openen. De gegevens in het bestand moeten overeenkomen met de onderstaande tekst, maar staan waarschijnlijk in een andere volgorde:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### De resultaten van een query exporteren

U kunt de functie **queryout** van BCP gebruiken om de resultaten van een query te exporteren in plaats van de hele tabel te exporteren. 

## Volgende stappen
Zie [Gegevens laden in SQL Data Warehouse][] voor een overzicht van het laden.
Zie [Overzicht van SQL Data Warehouse voor ontwikkelaars][] voor meer tips voor ontwikkelaars.
Zie [Tabelontwerp][] of [Syntaxis voor CREATE TABLE][] voor meer informatie over het maken van een tabel in SQL Data Warehouse.

<!--Image references-->

<!--Article references-->

[Gegevens laden in SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[Overzicht van SQL Data Warehouse voor ontwikkelaars]: sql-data-warehouse-overview-develop.md
[Tabelontwerp]: sql-data-warehouse-develop-table-design.md
[statistieken]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[BCP]: https://msdn.microsoft.com/library/ms162802.aspx
[Syntaxis voor CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Downloadcentrum]: https://www.microsoft.com/download/details.aspx?id=36433



<!--HONumber=Jun16_HO2-->


