<properties
   pageTitle="Zelfstudie: PolyBase in SQL Data Warehouse | Microsoft Azure"
   description="Informatie over PolyBase en het gebruik van PolyBase voor datawarehousescenario's."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/18/2016"
   ms.author="sahajs;barbkess;jrj;sonyama"/>


# Gegevens laden met PolyBase in SQL Data Warehouse

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

In deze zelfstudie ziet u hoe u met AzCopy en PolyBase gegevens laadt in SQL Data Warehouse. Als u klaar bent, weet u hoe u:

- AzCopy gebruikt om gegevens te kopiëren naar Azure Blob-opslag;
- databaseobjecten maakt om de gegevens te definiëren;
- een TSQL-query uitvoert om de gegevens te laden.

>[AZURE.VIDEO loading-data-with-polybase-in-azure-sql-data-warehouse]

## Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- Een SQL Data Warehouse-database.
- Een Azure-opslagaccount van het type Standard-LRS (lokaal redundante opslag), Standard-GRS (geografisch redundante opslag) of Standard-RAGRS (alleen-lezen geografisch redundante opslag).
- AzCopy-opdrachtregelprogramma Download en installeer de [meest recente versie van AzCopy][] die is geïnstalleerd met de hulpprogramma's van Microsoft Azure Storage.

    ![Hulpprogramma's van Azure Storage](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)


## Stap 1: voorbeeldgegevens toevoegen aan Azure Blob-opslag

Om gegevens te kunnen laden, moet u voorbeeldgegevens in een Azure Blob-opslag plaatsen. In deze stap vult u een Azure Storage-blob met voorbeeldgegevens. Later gaat u PolyBase gebruiken om deze voorbeeldgegevens in de SQL Data Warehouse-database te laden.

### A. Een voorbeeldtekstbestand voorbereiden

Ga als volgt te werk om een voorbeeldtekstbestand voor te bereiden:

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

### B. Het eindpunt van blob-service zoeken

Ga als volgt te werk om het eindpunt van blob-service te zoeken:

1. Selecteer in de Azure Portal **Bladeren** > **Opslagaccounts**.
2. Klik op het opslagaccount dat u wilt gebruiken.
3. Klik in de blade Opslagaccount op Blobs

    ![Klik op Blobs](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)

1. Sla de URL van het eindpunt van blob-service op voor later.

    ![Eindpunt van blob-service](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### C. Uw Azure-opslagsleutel zoeken

Ga als volgt te werk om uw Azure-opslagsleutel te zoeken:

1. Selecteer in de Azure Portal **Bladeren** > **Opslagaccounts**.
2. Klik op het opslagaccount dat u wilt gebruiken.
3. Selecteer **Alle instellingen** > **Toegangssleutels**.
4. Klik op Kopiëren om de toegangssleutels naar het Klembord te kopiëren.

    ![Azure-opslagsleutel kopiëren](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### D. Het voorbeeldbestand kopiëren naar Azure Blob-opslag

Ga als volgt te werk om uw gegevens te kopiëren naar Azure Blob-opslag:

1. Open een opdrachtprompt en wijzig de mappen in de installatiemap van de AzCopy. Met deze opdracht schakelt u naar de standaardinstallatiemap op een 64-bits Windows-client.

    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```

1. Voer de volgende opdracht uit om het bestand te uploaden. Geef de URL van het eindpunt voor blob-service op voor <blob service endpoint URL> en de Azure-toegangssleutel voor <azure_storage_account_key>.

    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Zie ook [Aan de slag met het AzCopy-opdrachtregelprogramma][].

### E. De blob-opslagcontainer verkennen

Ga als volgt te werk om te zien dat het bestand is geüpload naar blob-opslag:

1. Ga terug naar de blade Blob-service.
2. Dubbelklik onder Containers op **DataContainer**.
3. Als u het pad naar uw gegevens wilt verkennen, klikt u op de map **datedimension**. Nu ziet u het geüploade bestand **DimDate2.txt**.
4. Klik op **DimDate2.txt** als u eigenschappen wilt weergeven.
5. In de blade met blob-eigenschappen kunt u het bestand ook downloaden of verwijderen.

    ![Azure Storage-blob weergeven](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)


## Stap 2: een externe tabel maken voor de voorbeeldgegevens

In deze sectie maakt u een externe tabel waarin de voorbeeldgegevens worden gedefinieerd.

PolyBase gebruikt externe tabellen voor de toegang tot gegevens in Azure Blob-opslag. Omdat de gegevens niet zijn opgeslagen in SQL Data Warehouse, wordt verificatie van de externe gegevens door PolyBase uitgevoerd met behulp van een database-scoped referentie.

In het voorbeeld in deze stap worden de volgende Transact-SQL-instructies gebruikt om een externe tabel te maken.

- [Create Master Key (Transact-SQL)][] om het geheim van de database-scoped referentie te versleutelen.
- [Create Database Scoped Credential (Transact-SQL)][] om verificatiegegevens voor het Azure-opslagaccount op te geven.
- [Create External Data Source (Transact-SQL)][] om de locatie van de Azure Blob-opslag op te geven.
- [Create External File Format (Transact-SQL)][] om de indeling van uw gegevens op te geven.
- [Create External Table (Transact-SQL)][] om de tabeldefinitie en locatie van de gegevens op te geven.

Voer deze query uit voor uw SQL Data Warehouse-database. Hiermee wordt een externe tabel DimDate2External gemaakt in het dbo-schema dat wijst naar de voorbeeldgegevens in DimDate2.txt in de Azure Blob-opslag.


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

## Stap 3: Gegevens laden in SQL Data Warehouse

Wanneer de externe tabel is gemaakt, kunt u de gegevens in een nieuwe tabel laden of in een bestaande tabel invoegen.

- Als u de gegevens wilt laden in een nieuwe tabel, voert u de instructie [CREATE TABLE AS SELECT (Transact-SQL)][] uit. De kolommen in de nieuwe tabel hebben in de query een naam gekregen. De gegevenstypen van de kolommen komen overeen met de gegevenstypen in de definitie van de externe tabel.
- Als u de gegevens wilt laden in een bestaande tabel, gebruikt u de instructie [INSERT…SELECT (Transact-SQL)][].

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

## Stap 4: Statistieken maken voor uw zojuist geladen gegevens

SQL Data Warehouse bevat geen functionaliteit voor het automatisch maken of bijwerken van statistieken. Voor hoge queryprestaties is het dan ook belangrijk dat u voor elke kolom in elke tabel statistieken maakt nadat de tabel de eerste keer is geladen. Het is ook belangrijk dat de statistieken worden bijgewerkt wanneer gegevens substantieel zijn gewijzigd.

In dit voorbeeld maakt u statistieken voor één kolom voor de nieuwe tabel DimDate2.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Zie [Statistieken][] voor meer informatie.  


## Volgende stappen
Raadpleeg de [PolyBase-handleiding][] voor alles wat u moet weten wanneer u een oplossing ontwikkelt die gebruikmaakt van PolyBase.

<!--Image references-->


<!--Article references-->
[Zelfstudie: PolyBase in SQL Data Warehouse]: sql-data-warehouse-get-started-load-with-polybase.md
[Gegevens laden met BCP]: sql-data-warehouse-load-with-bcp.md
[oplossingspartners]: sql-data-warehouse-solution-partners.md
[overzicht voor ontwikkelaars]: sql-data-warehouse-overview-develop.md
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
[INSERT…SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx



<!--HONumber=Jun16_HO2-->


