<properties
   pageTitle="Gegevens uit SQL Server laden in Azure SQL Data Warehouse (PolyBase) | Microsoft Azure"
   description="Gebruikt bcp om gegevens uit SQL Server te exporteren naar platte bestanden, AZCopy om gegevens te importeren in een Azure Blob Storage en PolyBase om de gegevens op te nemen in Azure SQL Data Warehouse."
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
   ms.date="05/10/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# Gegevens in SQL Data Warehouse laden met PolyBase

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

In deze zelfstudie wordt getoond hoe u gegevens laadt in SQL Data Warehouse met behulp van AzCopy en PolyBase. Aan het einde kunt u:

- AzCopy gebruiken om gegevens te kopiëren naar Azure Blob Storage
- Databaseobjecten maken om de gegevens te definiëren
- Een T-SQL-query uitvoeren om de gegevens te laden

>[AZURE.VIDEO loading-data-with-polybase-in-azure-sql-data-warehouse]

## Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- Een SQL Data Warehouse-database.
- Een Azure-opslagaccount van het type standaard lokaal redundante opslag (Standard Locally Redundant Storage (Standard-LRS)), standaard geografisch redundante opslag (Standard Geo-Redundant Storage (Standard-GRS)) of standaard geografisch redundante opslag met leestoegang (Standard Read-Access Geo-Redundant Storage (Standard-RAGRS)).
- AzCopy-opdrachtregelprogramma. Download en installeer de [meest recente versie van AzCopy][] die is geïnstalleerd met de hulpprogramma's van Microsoft Azure Storage.

    ![Hulpprogramma's van Azure Storage](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)


## Stap 1: voorbeeldgegevens toevoegen aan Azure Blob Storage

Als u gegevens wilt laden, moet u voorbeeldgegevens in een Azure Blob Storage plaatsen. In deze stap vult u een Azure Storage Blob met voorbeeldgegevens. Later gaat u PolyBase gebruiken om deze voorbeeldgegevens in de SQL Data Warehouse-database te laden.

### A. Een voorbeeldtekstbestand voorbereiden

Bereid als volgt een voorbeeldtekstbestand voor:

1. Open Kladblok en kopieer de volgende regels met gegevens naar een nieuw bestand. Sla dit in uw lokale tijdelijke map op als %temp%\DimDate2.txt.

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

### B. Het eindpunt van de blob-service zoeken

Zoek als volgt het eindpunt van de blob-service:

1. Selecteer in Azure Portal **Bladeren** > **Opslagaccounts**.
2. Klik op het opslagaccount dat u wilt gebruiken.
3. Klik op de blade Opslagaccount op Blobs.

    ![Klik op Blobs.](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)

1. Sla de URL van het eindpunt van de blob-service op voor later gebruik.

    ![Eindpunt van blob-service](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### C. Uw Azure-opslagsleutel zoeken

Zoek als volgt uw Azure-opslagsleutel:

1. Selecteer in Azure Portal **Bladeren** > **Opslagaccounts**.
2. Klik op het opslagaccount dat u wilt gebruiken.
3. Selecteer **Alle instellingen** > **Toegangssleutels**.
4. Klik op Kopiëren om een van de toegangssleutels naar het Klembord te kopiëren.

    ![Azure-opslagsleutel kopiëren](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### D. Het voorbeeldbestand kopiëren naar Azure Blob Storage

Kopieer als volgt uw gegevens naar Azure Blob Storage:

1. Open een opdrachtprompt en wijzig de mappen in de installatiemap van AzCopy. Met deze opdracht schakelt u naar de standaardinstallatiemap op een 64-bits Windows-client.

    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```

1. Voer de volgende opdracht uit om het bestand te uploaden. Geef de URL van het eindpunt van de blob-service op voor <blob service endpoint URL> en de Azure-toegangssleutel voor <azure_storage_account_key>.

    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Zie ook [Aan de slag met het AzCopy-opdrachtregelprogramma][].

### E. De Blob Storage-container verkennen

Controleer als volgt het bestand dat naar Blob Storage is geüpload:

1. Ga terug naar de blade Blob-service.
2. Dubbelklik onder Containers op **datacontainer**.
3. Als u het pad naar uw gegevens wilt verkennen, klikt u op de map **datedimension**. Nu ziet u het geüploade bestand **DimDate2.txt**.
4. Klik op **DimDate2.txt** als u eigenschappen wilt weergeven.
5. Op de blade Blobeigenschappen kunt u het bestand downloaden of verwijderen.

    ![Azure Storage Blob weergeven](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)


## Stap 2: een externe tabel voor de voorbeeldgegevens maken

In deze sectie maakt u een externe tabel waarin de voorbeeldgegevens worden gedefinieerd.

PolyBase gebruikt externe tabellen voor de toegang tot gegevens in Azure Blob Storage. Omdat de gegevens niet zijn opgeslagen in SQL Data Warehouse, wordt verificatie van de externe gegevens door PolyBase uitgevoerd met behulp van een database-scoped referentie.

In het voorbeeld in deze stap worden de volgende Transact-SQL-instructies gebruikt om een externe tabel te maken.

- [Create Master Key (Transact-SQL)][] om het geheim van de database-scoped referentie te versleutelen.
- [Create Database Scoped Credential (Transact-SQL)][] om verificatiegegevens voor het Azure-opslagaccount op te geven.
- [Create External Data Source (Transact-SQL)][] om de locatie van de Azure Blob Storage op te geven.
- [Create External File Format (Transact-SQL)][] om de indeling van uw gegevens op te geven.
- [Create External Table (Transact-SQL)][] om de tabeldefinitie en locatie van de gegevens op te geven.

Voer deze query uit voor uw SQL Data Warehouse-database. Hiermee wordt een externe tabel DimDate2External gemaakt in het DBO-schema dat wijst naar de voorbeeldgegevens in DimDate2.txt in de Azure Blob Storage.


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


In SQL Server-objectverkenner in Visual Studio ziet u de externe bestandsindeling, externe gegevensbron en de tabel DimDate2External.

![Externe tabel weergeven](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## Stap 3: gegevens laden in SQL Data Warehouse

Nadat de externe tabel is gemaakt, kunt u de gegevens in een nieuwe tabel laden of in een bestaande tabel invoegen.

- Als u de gegevens in een nieuwe tabel wilt laden, voert u de instructie [CREATE TABLE AS SELECT (Transact-SQL)][] uit. De kolommen in de nieuwe tabel hebben in de query een naam gekregen. De gegevenstypen van de kolommen komen overeen met de gegevenstypen in de definitie van de externe tabel.
- Als u de gegevens in een bestaande tabel wilt laden, gebruikt u de instructie [INSERT...SELECT (Transact-SQL)][].

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## Stap 4: statistieken maken voor uw zojuist geladen gegevens

SQL Data Warehouse bevat geen functionaliteit voor het automatisch maken of bijwerken van statistieken. Voor hoge queryprestaties is het dan ook belangrijk dat u voor elke kolom in elke tabel statistieken maakt nadat de tabel de eerste keer is geladen. Het is ook belangrijk dat de statistieken worden bijgewerkt wanneer gegevens substantieel zijn gewijzigd.

In dit voorbeeld maakt u statistieken voor één kolom voor de nieuwe tabel DimDate2.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Zie [Statistieken][] voor meer informatie.  


## Volgende stappen
Raadpleeg de [PolyBase-handleiding][] voor meer informatie over het ontwikkelen van een oplossing die gebruikmaakt van PolyBase.

<!--Image references-->


<!--Article references-->
[Zelfstudie: PolyBase in SQL Data Warehouse]: sql-data-warehouse-get-started-load-with-polybase.md
[Gegevens laden met bcp]: sql-data-warehouse-load-with-bcp.md
[oplossingspartners]: sql-data-warehouse-solution-partners.md
[ontwikkelingsoverzicht]: sql-data-warehouse-overview-develop.md
[Statistieken]: sql-data-warehouse-develop-statistics.md
[PolyBase-handleiding]: sql-data-warehouse-load-polybase-guide.md
[Aan de slag met het AzCopy-opdrachtregelprogramma]: ../storage/storage-use-azcopy.md
[meest recente versie van AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[ondersteunde bron/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[kopieerbewerking]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server-doeladapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx



<!--HONumber=Jun16_HO2-->


