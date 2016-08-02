<properties
   pageTitle="U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio | Azure"
   description="Informatie over het installeren van Data Lake Tools voor Visual Studio en het ontwikkelen en testen van U-SQL-scripts. "
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/26/2016"
   ms.author="edmaca"/>

# Zelfstudie: U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Informatie over het installeren van Data Lake Tools en het gebruiken van Data Lake Tools voor Visual Studio om U-SQL-scripts te schrijven en testen.

U-SQL is een uitermate schaalbare en uitbreidbare taal voor het voorbereiden, transformeren en analyseren van alle gegevens in de data lake en daarbuiten. Zie voor meer informatie [Naslaginformatie U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).


###Vereisten

- **Visual Studio 2015, Visual Studio 2013 update 4 of Visual Studio 2012. Enterprise- (Ultimate/Premium), Professional- en Community-edities worden ondersteund; Express-editie wordt niet ondersteund. Visual Studio ‘15’ wordt momenteel niet ondersteund en daaraan wordt gewerkt.**
- **Microsoft Azure SDK voor .NET versie 2.7.1 of hoger**.  U kunt dit installeren met het [Webplatforminstallatieprogramma](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Data Lake Tools voor Visual Studio](http://aka.ms/adltoolsvs)**.

    Wanneer Data Lake Tools voor Visual Studio is geïnstalleerd, ziet u een knooppunt ‘Data Lake Analytics’ in Server Explorer, onder het 'Azure'-knooppunt (u opent Server Explorer met Ctrl + Alt + S).

- **Lees de volgende twee secties in [Aan de slag met Azure Data Lake Analytics met Azure Portal](data-lake-analytics-get-started-portal.md)**.

    - [Een Azure Data Lake Analytics-account maken](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).
    - [SearchLog.tsv uploaden naar het Data Lake Storage-standaardaccount](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

    U vindt een PowerShell-voorbeeldscript voor het maken van een Data Lake Analytics-service en uploaden van een brongegevensbestand in [Appx-A PowerShell-voorbeeld ter voorbereiding op de zelfstudie](data-lake-analytics-data-lake-tools-get-started.md#appx-a-powershell-sample-for-preparing-the-tutorial).

    Data Lake Tools biedt geen ondersteuning voor het maken van Data Lake Analytics-accounts. Daarom moet u deze maken met behulp van de Azure Portal, Azure PowerShell, .NET SDK of Azure CLI. Om een Data Lake Analytics-taak uit te voeren, hebt u enkele gegevens nodig. Hoewel Data Lake Tools ondersteuning biedt voor het uploaden van gegevens, gebruikt u de portal om de voorbeeldgegevens te uploaden. Zo is deze zelfstudie gemakkelijker te volgen.

## Verbinding maken met Azure

**Verbinding maken met Data Lake Analytics**

1. Open Visual Studio.
2. Klik in het menu **View** op **Server Explorer** om Server Explorer te openen. U kunt ook op **[Ctrl] + [Alt] + S** drukken.
3. Klik met de rechtermuisknop op **Azure**, klik op ‘Connect to Microsoft Azure Subscription’ en volg de instructies.
4. Vouw in **Server Explorer** achtereenvolgens **Azure** en **Data Lake Analytics** uit. U ziet een lijst met uw Data Lake Analytics-accounts, als u die hebt. U kunt vanuit Visual Studio geen Data Lake Analytics-accounts maken. Zie [Aan de slag met Azure Data Lake Analytics met Azure Portal](data-lake-analytics-get-started-portal.md) of [Aan de slag met Azure Data Lake Analytics met Azure PowerShell](data-lake-analytics-get-started-powershell.md) voor meer informatie over het maken van een account.

## Uploaden van brongegevensbestanden

Eerder in de zelfstudie, in de sectie **Vereisten**, hebt u enkele gegevens geüpload.  

Als u uw eigen gegevens wilt gebruiken, volgt u de onderstaande procedure voor het uploaden van gegevens van Data Lake Tools.

**Bestanden uploaden naar het afhankelijke Azure Data Lake-account**

1. Ga naar **Server Explorer**, vouw **Azure** uit, vouw **Data Lake Analytics** uit, vouw uw Data Lake Analytics-account uit en vouw **Storage Accounts** uit. U ziet de Data Lake Storage-standaardaccounts, de gekoppelde Data Lake Storage-accounts en de gekoppelde Azure Storage-accounts. Het Data Lake-standaardaccount heeft het label ‘Default Storage Account’.
2. Klik met de rechtermuisknop op het Data Lake Storage-standaardaccount en klik vervolgens op **Explorer**.  Hiermee opent u het deelvenster Data Lake Tools voor Visual Studio Explorer.  Aan de linkerkant wordt een structuurweergave en aan de rechterkant de inhoudsweergave weergegeven.
3. Blader naar de map waarnaar u bestanden wilt uploaden,
4. klik met de rechtermuisknop op een lege ruimte en klik vervolgens op **Upload**.

    ![U-SQL Visual Studio-project U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**Bestanden uploaden naar een gekoppelde Azure Blob-opslagaccount**

1. Ga naar **Server Explorer**, vouw **Azure** uit, vouw **Data Lake Analytics** uit, vouw uw Data Lake Analytics-account uit en vouw **Storage Accounts** uit. U ziet de Data Lake Storage-standaardaccounts, de gekoppelde Data Lake Storage-accounts en de gekoppelde Azure Storage-accounts.
2. Vouw de Azure Storage-account uit.
3. Klik met de rechtermuisknop op de container waarnaar u de bestanden wilt uploaden en klik vervolgens op **Explorer**. Als u geen container hebt, moet u er eerst een maken via de Azure-portal, met Azure PowerShell of met een ander hulpprogramma.
4. Blader naar de map waarnaar u bestanden wilt uploaden,
5. klik met de rechtermuisknop op een lege ruimte en klik vervolgens op **Upload**.

## U-SQL-scripts ontwikkelen

De Data Lake Analytics-taken worden geschreven in de U-SQL-taal. Zie [Aan de slag met de U-SQL-taal](data-lake-analytics-u-sql-get-started.md) en [Naslaginformatie voor de U-SQL-taal](http://go.microsoft.com/fwlink/?LinkId=691348) voor meer informatie over U-SQL.

**Een Data Lake Analytics-taak maken en verzenden**

1. Klik in het menu **File** op **New** en klik vervolgens op **Project**.
2. Selecteer het type **U-SQL Project**.

    ![nieuw U-SQL Visual Studio-project](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Klik op **OK**. Visual maakt een oplossing met een **Script.usql**-bestand.
4. Geef het volgende script op in **Script.usql**:

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

        @res =
            SELECT *
            FROM @searchlog;        

        OUTPUT @res   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Dit U-SQL-script leest het brongegevensbestand met **Extractors.Tsv()** en maakt vervolgens een CSV-bestand met **Outputters.Csv()**.

    Wijzig de twee paden niet, tenzij u het bronbestand naar een andere locatie hebt gekopieerd.  Data Lake Analytics maakt de uitvoermap als deze nog niet bestaat.

    Het is eenvoudiger om relatieve paden te gebruiken voor bestanden die zijn opgeslagen in Data Lake-standaardaccounts. Maar u kunt ook absolute paden gebruiken.  Bijvoorbeeld

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    U dient absolute paden te gebruiken om toegang te krijgen tot bestanden in gekoppelde Storage-accounts.  De syntaxis voor bestanden die zijn opgeslagen in het gekoppelde Azure Storage-account is:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Azure Blob-containers met openbare blobs of machtigingen voor openbare containers worden momenteel niet ondersteund.  

    U ziet de volgende functies:

    - **IntelliSense**

        Namen worden automatisch aangevuld en de leden worden weergegeven voor rijenset, klassen, databases, schema’s en door de gebruiker gedefinieerde objecten (User Defined Objects, UDO’s).

        IntelliSense voor catalogusentiteiten (databases, schema's, tabellen, UDO’s enz.) is gerelateerd aan uw Compute-account. U kunt het huidige actieve Compute-account, de database en het schema controleren in de bovenste werkbalk en ze overschakelen via de vervolgkeuzelijsten.

    - **Expand * columns**

        Klik rechts van de * om een blauwe onderstreping weer te geven onder de *. Houd de muisaanwijzer op de blauwe onderstreping en klik vervolgens op de pijl-omlaag.
        ![Data Lake Tools voor Visual Studio * uitvouwen](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-expand-asterisk.png)

        Klik op **Expand Columns** en het hulpprogramma vervangt de * door de kolomnamen.

    - **Auto Format**

        U kunt de inspringing van het Scope-script wijzigen op basis van de codestructuur onder Edit->Advanced:

        - Format Document (Ctrl + E, D): het hele document wordt opgemaakt.   
        - Format Selection (Ctrl + K, Ctrl + F): de selectie wordt opgemaakt. Als u geen selectie hebt gemaakt, wordt met deze sneltoets de regel opgemaakt waarin de cursor staat.  

        Alle opmaakregels kunnen worden geconfigureerd onder Tools->Options->Text Editor->SIP->Formatting.  
    - **Smart Indent**

        Data Lake Tools voor Visual Studio kan expressies automatisch laten inspringen tijdens het schrijven van scripts. Deze functie is standaard uitgeschakeld. Gebruikers kunnen deze inschakelen via U-SQL->Options and Settings ->Switches->Enable Smart Indent.

    - **Go To Definition en Find All References**

        Klik met de rechtermuisknop op de naam van een rijenset/parameter/kolom/UDO enzovoort, en klik vervolgens op Go To Definition (F12) om naar de definitie te gaan. Klik op Find All References (Shift+F12) om alle verwijzingen weer te geven.

    - **Insert Azure Path**

        In plaats van dat u moet onthouden wat het Azure-bestandspad is en het handmatig moet typen bij het schrijven van scripts, biedt Data Lake Tools voor Visual Studio een eenvoudigere manier: klik met de rechtermuisknop in de editor en klik op Insert Azure Path. Ga naar het bestand in het dialoogvenster Azure Blob Browser. Klik op **OK**. het bestandspad wordt ingevoegd in de code.

5. Geef het Data Lake Analytics-account, de database en het schema op. U kunt **(local)** selecteren om het script lokaal uit te voeren voor testdoeleinden. Zie [U-SQL lokaal uitvoeren](#run-u-sql-locally) voor meer informatie.

    ![U-SQL Visual Studio-project verzenden](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    Zie [U-SQL-catalogus gebruiken](data-lake-analytics-use-u-sql-catalog.md) voor meer informatie.

5. Ga naar **Solution Explorer**, klik met de rechtermuisknop op **Script.usql** en klik vervolgens op **Build Script**. Controleer het resultaat in het deelvenster Output.
6. Ga naar **Solution Explorer**, klik met de rechtermuisknop op **Script.usql** en klik vervolgens op **Submit Script**. U kunt ook klikken op **Submit** in het deelvenster Script.usql.  Zie de vorige schermafbeelding.  Klik op de pijl-omlaag naast de knop Submit om te verzenden met de geavanceerde opties:
7. Geef de **Job name** op, controleer het **Analytics Account** en klik vervolgens op **Submit**. Het resultaat van het verzenden en de koppeling naar de taak zijn beschikbaar in het resultaatvenster van Data Lake Tools voor Visual Studio wanneer het verzenden is voltooid.

    ![U-SQL Visual Studio-project verzenden](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)

8. U moet op de knop Refresh klikken om de status van de meest recente taak weer te geven en het scherm te vernieuwen. Wanneer de taak is voltooid, worden de **Job Graph**, **Meta Data Operations**, **State History** en **Diagnostics** weergegeven:

    ![Prestatiegrafiek U-SQL Visual Studio Data Lake Analytics-taak](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

    * Job Summary. De samenvattende informatie over de huidige taak wordt weergeven: State, Progress, Execution Time, Runtime Name, Submitter, enzovoort.   
    * Job Details. Gedetailleerde informatie over deze taak wordt weergegeven, waaronder Script, Resource, Vertex Execution View.
    * Job Graph. Er worden vier grafieken weergegeven die de informatie over de taak visualiseren: Progress, Data Read, Data Written, Excution Time, Average Execution Time Per Node, Input Throughput, Output Throughput.
    * MetaData Operations. Alle bewerkingen voor metagegevens worden weergegeven.
    * State History.
    * Diagnostics. Data Lake Tools voor Visual Studio onderzoekt automatisch de taakuitvoering. U ontvangt waarschuwingen wanneer er fouten of prestatieproblemen in taken optreden. Zie het gedeelte Job Diagnostics (koppeling nog te bepalen) voor meer informatie.

**De taakstatus controleren**

1. Ga naar Server Explorer, vouw **Azure** uit, vouw **Data Lake Analytics** uit en vouw uw Data Lake Analytics-account uit.
2. Dubbelklik op **Jobs** om de taken weer te geven.
2. Klik op een taak om de status weer te geven.

**De taakuitvoer weergeven**

1. Ga naar **Server Explorer**, vouw **Azure** uit, vouw **Data Lake Analytics** uit, vouw uw Data Lake Analytics-account uit, vouw **Storage Accounts** uit, klik met de rechtermuisknop op het Data Lake Storage-standaardaccount en klik vervolgens op **Explorer**.
2.  Dubbelklik op **output** om de map te openen.
3.  Dubbelklik op **SearchLog-From-adltools.csv**.


###Taak afspelen

Met Taak afspelen kunt u de uitvoering van de taak bekijken en afwijkingen en knelpunten visueel vaststellen. Deze functie kunt u gebruiken voordat de taakuitvoering is voltooid (dus tijdens de periode waarin de taak actief wordt uitgevoerd) en nadat de taakuitvoering is voltooid. Als u de taak afspeelt tijdens het uitvoeren ervan, wordt de voortgang afgespeeld tot het huidige tijdstip.

**Voortgang van de taak weergeven**  

1. Klik op **Load Profile** rechtsboven. Zie de vorige schermafbeelding.
2. Klik op de knop Play linksonder om de voortgang van de taak te controleren.
3. Klik tijdens het afspelen op **Pause** om het afspelen te stoppen of de voortgangsbalk direct naar een specifieke positie te slepen.


###Heat Map

Data Lake Tools voor Visual Studio biedt instelbare kleurenoverlays voor de taakweergave, voor het aanduiden van de voortgang, gegevens-I/O, uitvoeringstijd en I/O-doorvoer in elke fase. Hiermee kunt u potentiële problemen identificeren en taakeigenschappen direct en op intuïtieve wijze distribueren. Kies de gegevensbron die u wilt weergeven in de vervolgkeuzelijst.  

## U-SQL lokaal uitvoeren

Als u U-SQL lokaal uitvoert in Visual Studio, kunt u:

- U-SQL-scripts lokaal uitvoeren samen met C#-assembly's.
- Lokaal fouten opsporen in C#-assembly's.
- Lokale databases, assembly's, schema's en tabellen maken/verwijderen/weergeven in Server Explorer, net zoals u dat kunt doen voor Azure Data Lake Analytics-service.

U ziet een account *Local* in Visual Studio en het installatieprogramma maakt een map *DataRoot* in *C:\LocalRunRoot*. De map DataRoot wordt gebruikt voor:

- Het opslaan van metagegevens zoals tabellen, databases, TVF’s, enzovoort.
- Bepaalde scripts: als naar een relatief pad wordt verwezen in invoer-/uitvoerpaden, zoeken we de DataRoot op (en pad van het script als het de invoer is).
- Er wordt NIET verwezen naar de map DataRoot wanneer u een assembly probeert te registreren en een relatief pad gebruikt (Zie het gedeelte ‘Assembly’s gebruiken bij lokale uitvoering’ voor meer informatie).

In de volgende video wordt de functie voor het lokaal uitvoeren van U-SQL getoond:

>[AZURE.VIDEO usql-localrun]

### Bekende problemen en beperkingen

- Lokaal uitvoeren met U-SQL biedt geen ondersteuning voor het lokaal uitvoeren van query’s op bestandssets. Zie [U-SQL-bestandssets](https://msdn.microsoft.com/library/azure/mt621294.aspx). Dit wordt in de toekomst opgelost.
- Langzame prestaties vanwege onvoldoende parallelle uitvoering, omdat taakschema’s in een enkel proces opeenvolgend worden uitgevoerd.
- Bij lokaal uitvoeren kunnen taakgrafieken niet worden weergeven in Visual Studio. Dit wordt in de toekomst opgelost.
- Tabellen, databases, enzovoort kunnen niet worden gemaakt in Server Explorer voor het lokale account.
- Wanneer naar een relatief pad wordt verwezen:

    - In de scriptinvoer (EXTRACT * FROM “/path/abc”) wordt zowel in het pad DataRoot als het scriptpad gezocht.
    - In de scriptuitvoer (OUTPUT TO “path/abc”) wordt het pad DataRoot als uitvoermap gebruikt.
    - In de assembly-registratie (CRREATE ASSEMBLY xyz FROM “/path/abc”) wordt gezocht in het scriptpad, maar niet in de DataRoot.
    - In de geregistreerde TVF/View of andere metagegevensentiteiten wordt in het pad DataRoot gezocht, maar niet in het scriptpad.

    Voor scripts die worden uitgevoerd op de Data Lake-service wordt het standaardopslagaccount gebruikt als hoofdmap en doorzocht.

### U-SQL-scripts lokaal testen
Zie [U-SQL-scripts ontwikkelen](#develop-and-test-u-sql-scripts) voor instructies voor het ontwikkelen van U-SQL-scripts. Als u U-SQL-scripts lokaal wilt bouwen en uitvoeren, selecteert u **(Local)** in de vervolgkeuzelijst cluster en klikt u op **Submit**. Zorg ervoor dat u naar de juiste gegevens verwijst; u moet naar het absolute pad verwijzen of de gegevens in de map DataRoot plaatsen.

![U-SQL Visual Studio-project lokaal verzenden](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-local-run.png)

Om lokaal uitvoeren te activeren kunt u ook met de rechtermuisknop op een script klikken en vervolgens in het contextmenu op **Run Local Plan** klikken, of op **Ctrl + F5** drukken.

### Assembly's gebruiken bij lokaal uitvoeren

Er zijn twee manieren om de aangepaste C#-bestanden uit te voeren:

- Schrijf assembly's in het onderliggende-codebestand en ze worden automatisch geregistreerd en verwijderd nadat het script is uitgevoerd.
- Maak een assembly-project in C# en registreer de uitvoer-DLL bij het lokale account via een script zoals het onderstaande. Houd er rekening mee dat het pad relatief is ten opzichte van het script en niet ten opzichte van de map DataRoot.

![Assembly's gebruiken bij lokaal uitvoeren van U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-local-run-assembly.png)

### Lokaal fouten opsporen in scripts en C#-assembly's

U kunt fouten opsporen C#-assembly's zonder die te verzenden en registreren in de Azure Data Lake Analytics-service. U kunt onderbrekingspunten instellen in zowel het onderliggende-codebestand als een C#-project waarnaar wordt verwezen.

**Foutopsporing in lokale code in onderliggende-codebestand**
1.  Onderbrekingspunten instellen in het onderliggende-codebestand.
2.  Druk op **F5** om lokaal fouten op te sporen in het script.

De volgende procedure werkt alleen in Visual Studio 2015. In oudere Visual Studio-versies moet u mogelijk de PDB-bestanden handmatig toevoegen.

**Fouten opsporen in lokale code in een C#-project waarnaar wordt verwezen**
1.  Maak een C#-assemblyproject en bouw het zo dat het de DLL-uitvoer genereert.
2.  Registreer het DLL-bestand met een U-SQL-instructie:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
3.  Stel onderbrekingspunten in in de C#-code.
4.  Druk op **F5** om lokaal fouten op te sporen in het script dat verwijst naar de C#-DLL.  

##Zie ook

Om aan de slag te gaan met Data Lake Analytics met verschillende hulpprogramma's, zie:

- [Aan de slag met Data Lake Analytics met Azure Portal](data-lake-analytics-get-started-portal.md)
- [Aan de slag met Data Lake Analytics met Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Aan de slag met Data Lake Analytics met .NET SDK](data-lake-analytics-get-started-net-sdk.md)

Overige onderwerpen over ontwikkelen:

- [Weblogboeken analyseren met Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)
- [U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Aan de slag met de Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md)
- [Door de gebruiker gedefinieerde U-SQL-operators ontwikkelen voor Data Lake Analytics-taken](data-lake-analytics-u-sql-develop-user-defined-operators.md)

##Appx-A PowerShell-voorbeeld ter voorbereiding op de zelfstudie

Het volgende PowerShell-script bereidt een Azure Data Lake Analytics-account en de brongegevens voor, zodat u verder kunt gaan met [U-SQL-scripts ontwikkelen](data-lake-analytics-data-lake-tools-get-started.md#develop-u-sql-scripts).

    #region - used for creating Azure service names
    $nameToken = "<Enter an alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - service names
    $resourceGroupName = $namePrefix + "rg"
    $dataLakeStoreName = $namePrefix + "adas"
    $dataLakeAnalyticsName = $namePrefix + "adla"
    $location = "East US 2"
    #endregion


    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an Azure Data Lake Analytics service account
    Write-Host "Create a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
    New-AzureRmDataLakeStoreAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeStoreName `
        -Location $location

    Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
    New-AzureRmDataLakeAnalyticsAccount `
        -Name $dataLakeAnalyticsName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName

    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsName  
    #endregion

    #region - prepare the source data
    Write-Host "Import the source data ..."  -ForegroundColor Green
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file.
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

    Write-Host "List the source data ..."  -ForegroundColor Green
    Get-AzureRmDataLakeStoreChildItem -Account $dataLakeStoreName -Path  "/Samples/Data/"
    #endregion



<!--HONumber=Jun16_HO2-->


