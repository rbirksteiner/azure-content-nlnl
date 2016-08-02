<properties 
   pageTitle="Aan de slag met Azure Data Lake Analytics met Azure-opdrachtregelinterface | Microsoft Azure" 
   description="Informatie over het gebruik van de Azure-opdrachtregelinterface voor het maken van een Data Lake Store-account, het maken van een Data Lake Analytics-taak met U-SQL, en het verzenden van de taak. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/26/2016"
   ms.author="edmaca"/>

# Zelfstudie: Aan de slag met Azure Data Lake Analytics met Azure-opdrachtregelinterface

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Informatie over het gebruik van de Azure CLI voor het maken van Azure Data Lake Analytics-accounts, het definiëren van Data Lake Analytics-taken in [U-SQL](data-lake-analytics-u-sql-get-started.md) en het verzenden van taken naar Data Lake Analytics-accounts. Zie [Overzicht van Azure Data Lake Analytics](data-lake-analytics-overview.md) voor meer informatie over Data Lake Analytics.

In deze zelfstudie gaat u een taak ontwikkelen die een bestand met door tabs gescheiden waarden (TSV) leest en converteert naar een bestand met door komma's gescheiden waarden (CSV). Om de zelfstudie te volgen met andere ondersteunde hulpprogramma’s klikt u op de tabbladen boven aan deze sectie.

[AZURE.INCLUDE [basic-process-include](../../includes/data-lake-analytics-basic-process.md)]

##Vereisten

Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

- **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
- **Azure CLI**. Zie [Azure CLI installeren en configureren](../xplat-cli-install.md).
    - Download en installeer de **pre-release** [Azure CLI-hulpprogramma’s](https://github.com/MicrosoftBigData/AzureDataLake/releases) om deze demo te voltooien.
- **Verificatie**, met de volgende opdracht:

        azure login
    Zie [Verbinding maken met een Azure-abonnement met Azure CLI](../xplat-cli-connect.md) voor meer informatie over verificatie met een werk- of schoolaccount.
- **Overschakelen naar de modus Azure Resource Manager** met de volgende opdracht:

        azure config mode arm
        
## Een Data Lake Analytics-account maken

U moet een Data Lake Analytics-account hebben voordat u taken kunt uitvoeren. Om een Data Lake Analytics-account te maken, moet u het volgende opgeven:

- **Azure Resource Group**: een Data Lake Analytics-account moet worden gemaakt binnen een Azure-resourcegroep. Met [Azure Resource Manager](../resource-group-overview.md) kunt u met de resources in uw toepassing als groep gebruiken. U kunt alle resources voor uw toepassing implementeren, bijwerken of verwijderen in een enkele, gecoördineerde bewerking.  

    Het opsommen van de resourcegroepen in uw abonnement:
    
        azure group list 
    
    Een nieuwe resourcegroep maken:

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Data Lake Analytics-accountnaam**
- **Locatie**: een van de Azure-datacenters die ondersteuning biedt voor Data Lake Analytics.
- **Default Data Lake account**: elke Data Lake Analytics-account heeft een Data Lake-standaardaccount.

    Overzicht van de bestaande Data Lake-account weergeven:
    
        azure datalake store account list

    Een nieuwe Data Lake-account maken:

        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

    > [AZURE.NOTE] De naam van het Data Lake-account mag alleen kleine letters en cijfers bevatten.



**Een Data Lake Analytics-account maken**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"          

![Data Lake Analytics-account weergeven](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] De naam van de Data Lake Analytics-account mag alleen kleine letters en cijfers bevatten.


## Gegevens uploaden naar Data Lake Store

In deze zelfstudie verwerkt u een aantal zoeklogboeken.  Het zoeklogboek kan worden opgeslagen in de Data Lake Store of Azure Blob-opslag. 

De Azure Portal biedt een gebruikersinterface waarmee u een aantal voorbeeldbestanden kunt kopiëren naar het Data Lake-standaardaccount, waaronder een zoeklogboekbestand. Zie [Brongegevens voorbereiden](data-lake-analytics-get-started-portal.md#prepare-source-data) om de gegevens te uploaden naar het Data Lake Store-standaardaccount.

Om bestanden te uploaden met de opdrachtregelinterface gebruikt u de volgende opdracht:

    azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
    azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Data Lake Analytics heeft ook toegang tot Azure Blob-opslag.  Zie [De Azure CLI gebruiken met Azure Storage](../storage/storage-azure-cli.md) voor informatie over het uploaden van gegevens naar Azure Blob-opslag.

## Data Lake Analytics-taken verzenden

Data Lake Analytics-taken worden geschreven in de U-SQL-taal. Zie [Aan de slag met de U-SQL-taal](data-lake-analytics-u-sql-get-started.md) en [Naslaginformatie voor de U-SQL-taal](http://go.microsoft.com/fwlink/?LinkId=691348) voor meer informatie over U-SQL.

**Een Data Lake Analytics-taakscript maken**

- Maak een tekstbestand met het volgende U-SQL-script en bewaar het tekstbestand op uw werkstation:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Dit U-SQL-script leest het brongegevensbestand met **Extractors.Tsv()** en maakt vervolgens een CSV-bestand met **Outputters.Csv()**. 
    
    Wijzig de twee paden niet, tenzij u het bronbestand naar een andere locatie kopieert.  Data Lake Analytics maakt de uitvoermap als deze nog niet bestaat.
    
    Het is eenvoudiger om relatieve paden te gebruiken voor bestanden die zijn opgeslagen in Data Lake-standaardaccounts. Maar u kunt ook absolute paden gebruiken.  Bijvoorbeeld 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    U moet absolute paden gebruiken om toegang te krijgen tot bestanden in gekoppelde Storage-accounts.  De syntaxis voor bestanden die zijn opgeslagen in het gekoppelde Azure Storage-account is:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Azure Blob-containers met openbare blobs of machtigingen voor openbare containers worden momenteel niet ondersteund.      

    
**De taak verzenden**


    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
U kunt de volgende opdrachten gebruiken om taken weer te geven, informatie over taken weer te geven en taken te annuleren:

    azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
    azure datalake analytics job list "<Data Lake Analytics Account Name>"
    azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

Nadat de taak is voltooid, kunt u de volgende cmdlets gebruiken om het bestand weer te geven en te downloaden:
    
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## Zie ook

- Als u dezelfde zelfstudie wilt bekijken met een ander hulpprogramma, klikt u op de tabselectors boven aan de pagina.
- Zie [Websitelogboeken analyseren met Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) voor een complexere query.
- Zie [U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) om aan de slag te gaan met het ontwikkelen van U-SQL-toepassingen.
- Zie [Aan de slag met de Azure Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md) om U-SQL te leren.
- Zie [Azure Data Lake Analytics beheren met Azure Portal](data-lake-analytics-manage-use-portal.md) voor informatie over beheertaken.
- Zie [Overzicht van Azure Data Lake Analytics](data-lake-analytics-overview.md) voor een overzicht van Data Lake Analytics.




<!--HONumber=Jun16_HO2-->


