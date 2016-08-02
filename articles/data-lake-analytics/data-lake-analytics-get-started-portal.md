<properties 
   pageTitle="Aan de slag met Azure Data Lake Analytics met Azure Portal | Azure" 
   description="Informatie over het gebruik van de Azure-portal voor het maken van een Data Lake Analytics-account, het maken van een Data Lake Analytics-taak met U-SQL, en het verzenden van de taak. " 
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

# Zelfstudie: Aan de slag met Azure Data Lake Analytics met Azure Portal

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Informatie over het gebruik van de Azure-portal voor het maken van Azure Data Lake Analytics-accounts, het definiëren van Data Lake Analytics-taken in [U-SQL](data-lake-analytics-u-sql-get-started.md), en het verzenden van taken naar Data Lake Analytics-accounts. Zie [Overzicht van Azure Data Lake Analytics](data-lake-analytics-overview.md) voor meer informatie over Data Lake Analytics.

In deze zelfstudie gaat u een taak ontwikkelen die een bestand met door tabs gescheiden waarden (TSV) leest en converteert naar een bestand met door komma's gescheiden waarden (CSV). Om de zelfstudie te volgen met andere ondersteunde hulpprogramma’s klikt u op de tabbladen boven aan deze sectie. Als uw eerste taak is voltooid, kunt u complexere gegevenstransformaties gaan schrijven met U-SQL.

[AZURE.INCLUDE [basic-process-include](../../includes/data-lake-analytics-basic-process.md)]

##Vereisten

Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

- **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

##Een Data Lake Analytics-account maken

U moet een Data Lake Analytics-account hebben voordat u taken kunt uitvoeren.

Elk Data Lake Analytics-account is afhankelijk van een [Azure Data Lake Store]()-account.  Dit account wordt het Data Lake Store-standaardaccount genoemd.  U kunt het Data Lake Store-account van tevoren maken, of wanneer u het Data Lake Analytics-account maakt. In deze zelfstudie gaat u het Data Lake-account maken met het Data Lake Analytics-account.

**Een Data Lake Analytics-account maken**

1. Meld u aan bij de [klassieke Azure-portal](https://portal.azure.com).
2. Klik op **Nieuw**, klik op **Gegevens en analyse** en klik vervolgens op **Data Lake Analytics**.
6. Typ of selecteer het volgende:

    ![Azure Data Lake Analytics-portalblade](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

    - **Naam**: geef het Analytics-account een naam.
    - **Data Lake Store**: elke Data Lake Analytics-account is afhankelijk van een Data Lake Store-account. Het Data Lake Analytics-account en het afhankelijke Data Lake Store-account moeten zich in hetzelfde Azure-datacenter bevinden. Volg de instructies voor het maken van een nieuw Data Lake Store-account of selecteer een bestaand account.
    - **Abonnement**: kies het Azure-abonnement dat u gebruikt voor het Analytics-account.
    - **Resourcegroep**. Selecteer een bestaande Azure-resourcegroep of maak een nieuwe. Met Azure Resource Manager (ARM) kunt u de resources in uw toepassing als groep gebruiken. Zie [Overzicht van Azure Resource Manager](resource-group-overview.md) voor meer informatie. 
    - **Locatie**. Selecteer een Azure-datacenter voor het Data Lake Analytics-account. 
7. Selecteer **Vastmaken aan Startboard**. Dit is vereist voor het volgen van deze zelfstudie.
8. Klik op **Maken**. U gaat naar het Startboard van de portal. Er is een nieuwe tegel toegevoegd aan het Startboard met het label ‘Deploying Azure Data Lake Analytics’. Het duurt enkele minuten om een Data Lake Analytics-account te maken. Wanneer het account is gemaakt, wordt het in een nieuwe blade in de portal geopend.

    ![Azure Data Lake Analytics-portalblade](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)


Nadat een Data Lake Analytics-account is gemaakt, kunt u extra Data Lake Store-accounts en Azure Storage-accounts toevoegen. Zie [Gegevensbronnen voor Data Lake Analytics-account beheren](data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

##Brongegevens voorbereiden

In deze zelfstudie verwerkt u een aantal zoeklogboeken.  Het zoeklogboek kan worden opgeslagen in de Data Lake Store of Azure Blob-opslag. 

De Azure Portal biedt een gebruikersinterface waarmee u een aantal voorbeeldbestanden kunt kopiëren naar het Data Lake-standaardaccount, waaronder een zoeklogboekbestand.

**Bestanden met voorbeeldgegevens kopiëren**

1. Klik in de Azure Portal op **Microsoft Azure** in de linkerbovenhoek.
2. Klik op de tegel met de naam van uw Data Lake Analytics-account.  Deze is hier vastgemaakt bij het maken van het account.
Zie [Een Data Lake Analytics-account openen in de portal](data-lake-analytics-manage-use-portal.md#access-adla-account) voor informatie over hoe u het account kunt openen als het niet hier is vastgemaakt.
3. Vouw het deelvenster **Essentials** uit en klik vervolgens op **Explore sample jobs**. Er wordt een andere blade geopend, genaamd **Sample jobs**.
4. Klik op **Copy Sample Data** en vervolgens op **OK** om te bevestigen.
5. Klik op **Notification**, een pictogram in de vorm van een bel. Er wordt een logboek weergegeven met de tekst **Updating sample data completed**. Klik op een willekeurige plaats buiten het meldingsvenster om het te sluiten.
7. Klik in de blade Data Lake Analytics-account op **Data Explorer** bovenaan. 

    ![Azure Data Lake Analytics-knop data explorer](./media/data-lake-analytics-get-started-portal/data-lake-analytics-data-explorer-button.png)

    Er worden twee blades geopend. De ene is **Data Explorer** en de andere het Data Lake Store-standaardaccount.
8. Klik in de blade Data Lake Store-standaardaccount op **Samples** om de map uit te vouwen en klik op **Data** om de map uit te vouwen. De volgende bestanden en mappen worden weergegeven:

    - AmbulanceData/
    - AdsLog.tsv
    - SearchLog.tsv
    - version.txt
    - WebLog.log
    
    In deze zelfstudie gebruikt u SearchLog.tsv.

In de praktijk moet u uw toepassingen programmeren om gegevens te schrijven naar een gekoppeld opslagaccount, of gegevens uploaden. Zie [Gegevens uploaden naar Data Lake Store](data-lake-analytics-manage-use-portal.md#upload-data-to-adls) of [Gegevens uploaden naar Blob-opslag](data-lake-analytics-manage-use-portal.md#upload-data-to-wasb) voor informatie over het uploaden van bestanden.

##Een Data Lake Analytics-taak maken en verzenden

Nadat u de brongegevens hebt voorbereid, kunt u beginnen met het ontwikkelen van een U-SQL-script.  

**De taak verzenden**

1. Klik in de blade Data Lake Analytics-account in de portal op **New Job**. 

    ![Azure Data Lake Analytics-knop new job](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job-button.png)

    Zie [Een Data Lake Analytics-account openen in de portal](data-lake-analytics-manage-use-portal.md#access-adla-account) als de blade niet wordt weergegeven.
4. Vul **Job Name** in en plak het volgende U-SQL-script:

    ![Azure Data Lake Analytics U-SQL-taken maken](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job.png)

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
    
    Wijzig de twee paden niet, tenzij u het bronbestand naar een andere locatie kopieert.  Data Lake Analytics maakt de uitvoermap als deze nog niet bestaat.  In dit geval gebruiken we eenvoudige, relatieve paden.  
    
    Het is eenvoudiger om relatieve paden te  gebruiken voor bestanden die zijn opgeslagen in Data Lake-standaardaccounts. Maar u kunt ook absolute paden gebruiken.  Bijvoorbeeld 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
      

    Zie [Aan de slag met de Azure Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md) en [Naslaginformatie voor de U-SQL-taal](http://go.microsoft.com/fwlink/?LinkId=691348) voor meer informatie over U-SQL.
     
5. Klik op **Submit Job** bovenaan. Er wordt een nieuw deelvenster Job Details geopend. De taakstatus wordt vermeld op de titelbalk.   
6. Wacht tot de taakstatus verandert in **Succeeded**. Wanneer de taak is voltooid, worden de taakgegevens in een nieuwe blade in de portal geopend:

    ![Azure Data Lake Analytics-taakgegevens](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-completed.png)

    In de vorige schermafbeelding ziet u dat de taak ongeveer anderhalve minuut duurde, van indienen tot voltooiing.
    
    Zie [Data Lake Analytics-taken bewaken en fouten oplossen](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorials.md) als de taak is mislukt.

7. Klik onder aan de blade **Job Details** op de taaknaam in **SearchLog-from-Data-Lake.csv**. U kunt het uitvoerbestand bekijken, downloaden, een nieuwe naam geven en verwijderen.

    ![Eigenschappen Azure Data Lake Analytics-taakuitvoerbestand](./media/data-lake-analytics-get-started-portal/data-lake-analytics-output-file-properties.png)
8. Klik op **Preview** om het uitvoerbestand weer te geven.

    ![Voorbeeld Azure Data Lake Analytics-taakuitvoerbestand](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-output-preview.png)

##Zie ook

- Zie [Websitelogboeken analyseren met Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) voor een complexere query.
- Zie [U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) om aan de slag te gaan met het ontwikkelen van U-SQL-toepassingen.
- Zie [Aan de slag met de Azure Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md) om U-SQL te leren.
- Zie [Azure Data Lake Analytics beheren met Azure Portal](data-lake-analytics-manage-use-portal.md) voor informatie over beheertaken.
- Zie [Overzicht van Azure Data Lake Analytics](data-lake-analytics-overview.md) voor een overzicht van Data Lake Analytics.
- Als u dezelfde zelfstudie wilt bekijken met een ander hulpprogramma, klikt u op de tabselectors boven aan de pagina.



<!--HONumber=Jun16_HO2-->


