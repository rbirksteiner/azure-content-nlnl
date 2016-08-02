<properties
   pageTitle="Gegevens laden in SQL Data Warehouse | Microsoft Azure met behulp van BCP"
   description="Informatie over BCP en het gebruik van BCP voor datawarehousescenario's."
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
   ms.date="04/21/2016"
   ms.author="mausher;barbkess;sonyama"/>


# Gegevens laden met BCP

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)


**[BCP][]** is een opdrachtregelprogramma voor het bulksgewijs laden van gegevens. Hiermee kunt u gegevens kopiëren van en naar SQL Server, gegevensbestanden en SQL Data Warehouse. Gebruikt BCP om veel rijen in SQL Data Warehouse-tabellen te importeren of gegevens uit SQL Server-tabellen te exporteren naar gegevensbestanden. Voor het gebruik van BCP is geen kennis van Transact-SQL vereist, behalve wanneer u de optie queryout wilt gebruiken.

Met BCP kunt u snel en eenvoudig kleinere gegevenssets verplaatsen van en naar een SQL Data Warehouse-database. De exacte aanbevolen hoeveelheid gegevens die u kunt laden/extraheren met BCP is afhankelijk van uw netwerkverbinding met het Azure-datacentrum.  Over het algemeen kunt u dimensietabellen probleemloos laden en extraheren met BCP. Voor grote hoeveelheden gegevens wordt BCP echter niet aangeraden.  Als u veel gegevens wilt laden en extraheren, kunt u het beste Polybase gebruiken. Dit hulpprogramma is beter geschikt voor de uitgebreide parallelle verwerkingsarchitectuur van SQL Data Warehouse.

Met BCP kunt u:

- Gegevens in SQL Data Warehouse laden met behulp van een eenvoudig opdrachtregelprogramma.
- Gegevens uit SQL Data Warehouse extraheren met behulp van een eenvoudig opdrachtregelprogramma.

In deze zelfstudie leert u hoe u het volgende kunt doen:

- Gegevens importeren in een tabel met behulp van de BCP-opdracht in
- Gegevens exporteren uit een tabel met behulp van de BCP-opdracht out

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- Een SQL Data Warehouse-database
- Het opdrachtregelprogramma BCP (moet zijn geïnstalleerd)
- Het opdrachtregelprogramma SQLCMD (moet zijn geïnstalleerd)

>[AZURE.NOTE] U kunt de opdrachtregelprogramma's BCP en SQLCMD downloaden van het [Microsoft Downloadcentrum][].

## Gegevens importeren in SQL Data Warehouse

In deze zelfstudie maakt u een tabel in Azure SQL Data Warehouse en importeert u gegevens in de tabel.

### Stap 1: een tabel maken in Azure SQL Data Warehouse

Open een opdrachtprompt en voer de volgende query voor het maken van een tabel op uw exemplaar uit met SQLCMD:

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

>[AZURE.NOTE] Zie [Tabelontwerp][] of [Syntaxis voor CREATE TABLE][] voor meer informatie over het maken van een tabel in SQL Data Warehouse en de beschikbare opties in de WITH-clausule.

### Stap 2: een brongegevensbestand maken

Open Kladblok, kopieer de volgende regels met gegevens naar een nieuw tekstbestand en sla dit bestand op in de lokale tijdelijke map C:\Temp\DimDate2.txt.

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

> [AZURE.NOTE] Denk eraan dat de bestandscodering UTF-8 niet wordt ondersteund in bcp.exe. Gebruik ASCII-bestanden of bestanden met de bestandscodering UTF-16 als u bcp.exe gebruikt.

### Stap 3: verbinding maken en de gegevens importeren
Met BCP kunt u verbinding maken en de gegevens importeren met de volgende opdracht, waarbij u de waarden waar nodig vervangt:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

U kunt controleren of de gegevens zijn geladen door de volgende query uit te voeren met SQLCMD:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Hierdoor zouden de volgende resultaten moeten worden geretourneerd:

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

### Stap 4: statistieken maken voor uw zojuist geladen gegevens

Azure SQL Data Warehouse bevat nog geen functionaliteit voor het automatisch maken of bijwerken van statistieken. Voor optimale resultaten van uw query's is het belangrijk dat u statistieken maakt voor alle kolommen van alle tabellen nadat de gegevens voor het eerst zijn geladen of wanneer de gegevens substantieel zijn gewijzigd. Zie het onderwerp [Statistieken][] in de groep onderwerpen voor ontwikkelaars voor gedetailleerde uitleg van statistieken. Hieronder ziet u een kort voorbeeld van het maken van statistieken voor de tabellen die zijn geladen in dit voorbeeld

Voer de volgende CREATE STATISTICS-instructies uit vanaf een SQLCMD-opdrachtprompt:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## Gegevens uit SQL Data Warehouse exporteren
In deze zelfstudie maakt u een gegevensbestand op basis van een tabel in SQL Data Warehouse. U exporteert de eerder gemaakte gegevens naar een nieuw gegevensbestand met de naam DimDate2_export.txt.

### Stap 1: de gegevens exporteren

Met het hulpprogramma BCP kunt u verbinding maken en gegevens exporteren met de volgende opdracht, waarbij u de waarden waar nodig vervangt:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
U kunt controleren of de gegevens correct zijn geëxporteerd door het nieuwe bestand te openen. De gegevens in het bestand moeten overeenkomen met de onderstaande tekst:

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

>[AZURE.NOTE] Vanwege de aard van gedistribueerde systemen kan de gegevensvolgorde per SQL Data Warehouse-database verschillen. In plaats van de hele tabel te exporteren kunt u ook een query voor het extraheren van gegevens schrijven met de functie **queryout** van BCP.

## Volgende stappen
Zie [Gegevens laden in SQL Data Warehouse][] voor een overzicht van het laden.
Zie [Overzicht van SQL Data Warehouse voor ontwikkelaars][] voor meer tips voor ontwikkelaars.

<!--Image references-->

<!--Article references-->

[Gegevens laden in SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[Overzicht van SQL Data Warehouse voor ontwikkelaars]: sql-data-warehouse-overview-develop.md
[Tabelontwerp]: sql-data-warehouse-develop-table-design.md
[Statistieken]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[BCP]: https://msdn.microsoft.com/library/ms162802.aspx
[Syntaxis voor CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Downloadcentrum]: https://www.microsoft.com/download/details.aspx?id=36433



<!--HONumber=Jun16_HO2-->


