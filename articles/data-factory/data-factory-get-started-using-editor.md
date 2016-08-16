<properties 
    pageTitle="Zelfstudie: een pijplijn maken met de kopieeractiviteit middels de Data Factory-editor" 
    description="In deze zelfstudie maakt u een Azure Data Factory-pijplijn met een kopieeractiviteit. Hiervoor gebruikt u de Data Factory-editor in Azure Portal." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/16/2016" 
    ms.author="spelluru"/>

# Zelfstudie: een pijplijn maken met de kopieeractiviteit middels de Data Factory-editor
> [AZURE.SELECTOR]
- [Overzicht van de zelfstudie](data-factory-get-started.md)
- [De Data Factory-editor gebruiken](data-factory-get-started-using-editor.md)
- [Visual Studio gebruiken](data-factory-get-started-using-vs.md)
- [PowerShell gebruiken](data-factory-monitor-manage-using-powershell.md)
- [De wizard Kopiëren gebruiken](data-factory-copy-data-wizard-tutorial.md)


Deze zelfstudie bestaat uit de volgende stappen:

Stap | Beschrijving
-----| -----------
[Een Azure-gegevensfactory maken](#create-data-factory) | In deze stap maakt u een Azure-gegevensfactory met de naam **ADFTutorialDataFactory**.  
[Gekoppelde services maken](#create-linked-services) | In deze stap maakt u twee gekoppelde services: **AzureStorageLinkedService** en **AzureSqlLinkedService**. Met de AzureStorageLinkedService wordt een Azure-opslag gekoppeld en met AzureSqlLinkedService wordt de Azure SQL Database gekoppeld aan ADFTutorialDataFactory. De invoergegevens van de pijplijn staan in een blobcontainer in Azure Blob Storage en de uitvoergegevens worden opgeslagen in een tabel in de Azure SQL Database. Daarom voegt u deze twee gegevensarchieven als gekoppelde services toe aan de gegevensfactory.      
[Invoer- en uitvoergegevenssets maken](#create-datasets) | In de vorige stap hebt u gekoppelde services gemaakt die verwijzen naar gegevensarchieven die invoer- of uitvoergegevens bevatten. In deze stap definieert u twee gegevensfactorytabellen (**EmpTableFromBlob** en **EmpSQLTable**) die staan voor de invoer- en uitvoergegevens die zijn opgeslagen in de gegevensarchieven. In EmpTableFromBlob geeft u op welke blobcontainer een blob bevat met de brongegevens en voor EmpSQLTable geeft u op in welke SQL-tabel de uitvoergegevens worden opgeslagen. U geeft ook andere eigenschappen op, zoals de structuur van de gegevens, de beschikbaarheid van de gegevens, enzovoort... 
[Een pijplijn maken](#create-pipeline) | In deze stap maakt u een pijplijn met de naam **ADFTutorialPipeline** in ADFTutorialDataFactory. De pijplijn heeft een **kopieeractiviteit** waarmee invoergegevens van de Azure-blob naar de uitvoer-Azure SQL-tabel worden gekopieerd. Met de kopieerbewerking wordt de gegevensverplaatsing in Azure Data Factory uitgevoerd. De activiteit wordt mogelijk gemaakt door een wereldwijd beschikbare service waarmee gegevens veilig, betrouwbaar en schaalbaar kunnen worden gekopieerd tussen verschillende gegevensarchieven. Zie [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie over de kopieeractiviteit. 
[De pijplijn bewaken](#monitor-pipeline) | In deze stap bewaakt u segmenten van de invoer- en uitvoertabellen middels Azure Portal.

> [AZURE.IMPORTANT] Lees het [Overzicht van de zelfstudie](data-factory-get-started.md) en voltooi de vereiste stappen voordat u deze zelfstudie volgt.

## Een gegevensfactory maken
In deze stap gebruikt u Azure Portal om een Azure Data Factory met de naam **ADFTutorialDataFactory** te maken.

1.  Wanneer u zich hebt aangemeld bij [Azure Portal][azure-portal] klikt u op **NIEUW** in de linkeronderhoek. Selecteer vervolgens **Gegevensanalyse** in de blade **Maken** en klik op **Gegevensfactory** in de blade **Gegevensanalyse**. 

    ![Nieuw -> DataFactory][image-data-factory-new-datafactory-menu]    

6. In de blade **Nieuwe gegevensfactory**:
    1. Voer **ADFTutorialDataFactory** in als **naam**. 
    
        ![Blade voor een nieuwe gegevensfactory][image-data-factory-getstarted-new-data-factory-blade]
    2. Klik op de **RESOURCEGROEPNAAM** en doe het volgende:
        1. Klik op **Een nieuwe resourcegroep maken**.
        2. In de blade **Resourcegroep maken** voert u **ADFTutorialResourceGroup** in als **naam** van de resourcegroep en klikt u op **OK**. 

            ![Een resourcegroep maken][image-data-factory-create-resource-group]

        Voor sommige van de stappen in deze zelfstudie wordt ervan uitgegaan dat u voor de resourcegroep de naam **ADFTutorialResourceGroup** gebruikt. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../resource-group-overview.md) voor meer informatie.  
7. In de blade **Nieuwe gegevensfactory** controleert u of **Toevoegen aan Startboard** is geselecteerd.
8. Klik op **Maken** op de blade **Nieuwe gegevensfactory**.

    De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u de volgende fout ontvang: **De gegevensfactory ADFTutorialDataFactory is niet beschikbaar**, wijzigt u de naam van de gegevensfactory (naar bijvoorbeeld uwnaamADFTutorialDataFactory) en probeert u het opnieuw. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.  
     
    ![Naam van gegevensfactory niet beschikbaar][image-data-factory-name-not-available]
    
    > [AZURE.NOTE] De naam van de gegevensfactory wordt in de toekomst mogelijk geregistreerd als DNS-naam en wordt daarmee ook voor iedereen zichtbaar.  
    > 
    > Als u Data Factory-exemplaren wilt maken, moet u bijdrager/beheerder zijn van het Azure-abonnement

9. Klik op de hub **MELDINGEN** aan de linkerzijde en zoek meldingen over het aanmaakproces. Klik op **X** om de blade **MELDINGEN** te sluiten als deze is geopend. 
10. Wanneer het aanmaken is voltooid, ziet u de blade **GEGEVENSFACTORY** zoals hieronder wordt weergegeven.

    ![Startpagina van de gegevensfactory][image-data-factory-get-stated-factory-home-page]

## Gekoppelde services maken
Met gekoppelde services worden gegevensarchieven of compute-services gekoppeld aan een Azure Data Factory. Een gegevensarchief kan een Azure Storage-, Azure SQL Database of een on-premises SQL Server-database zijn.

In deze stap maakt u twee gekoppelde services: **AzureStorageLinkedService** en **AzureSqlLinkedService**. Met de gekoppelde AzureStorageLinkedService-service wordt een Azure-opslagaccount gekoppeld en met AzureSqlLinkedService wordt een Azure SQL Database gekoppeld aan **ADFTutorialDataFactory**. Later in deze zelfstudie maakt u een pijplijn waarmee gegevens worden gekopieerd van een blobcontainer in AzureStorageLinkedService naar een SQL-tabel in AzureSqlLinkedService.

### Een gekoppelde service maken voor het Azure-opslagaccount
1.  In de blade **GEGEVENSFACTORY** klikt u op de tegel **Ontwerpen en implementeren** om de **editor** van de gegevensfactory te openen.

    ![Tegel Ontwerpen en implementeren][image-author-deploy-tile] 

     
5. In de **editor** klikt u op **Nieuw gegevensarchief** op de werkbalk en selecteert u **Azure-opslag** uit de vervolgkeuzelijst. U ziet het JSON-sjabloon voor het maken van een gekoppelde Azure-service in het rechterdeelvenster. 

    ![Knop Nieuw gegevensarchief in de editor][image-editor-newdatastore-button]
    
6. Vervang **accountname** en **accountkey** door de naam van uw account en de accountsleutel van uw Azure-opslagaccount. 

    ![JSON voor Blob Storage in de editor](./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png)    
    
    Zie de [naslaginformatie voor JSON-scriptverwerking](http://go.microsoft.com/fwlink/?LinkId=516971) voor meer informatie over de JSON-eigenschappen.

6. Klik op **Implementeren** op de werkbalk om AzureStorageLinkedService te implementeren. Controleer of u **GEKOPPELDE SERVICE IS GEMAAKT** ziet op de titelbalk.

    ![Blob Storage implementeren in de editor][image-editor-blob-storage-deploy]

### Een gekoppelde service maken voor de Azure SQL Database
1. In de **Data Factory-editor** klikt u op **Nieuw gegevensarchief** op de werkbalk en selecteert u **Azure SQL Database** uit de vervolgkeuzelijst. U ziet het JSON-sjabloon voor het maken van een gekoppelde Azure SQL-service in het rechterdeelvenster.

    ![Azure SQL-instellingen in de editor][image-editor-azure-sql-settings]

2. Vervang **servername**, **databasename**, **username@servername** en **password** door de namen van uw Azure SQL-server, database en gebruikersaccount en voer uw wachtwoord in. 
3. Klik op **Implementeren** op de werkbalk om AzureSqlLinkedService te maken en te implementeren. 
   

## Gegevenssets maken
In de vorige stap hebt u de gekoppelde services **AzureStorageLinkedService** en **AzureSqlLinkedService** gemaakt om een Azure-opslagaccount en een Azure SQL Database te koppelen aan de gegevensfactory **ADFTutorialDataFactory**. In deze stap definieert u twee gegevensfactorytabellen (**EmpTableFromBlob** en **EmpSQLTable**) die staan voor de invoer- en uitvoergegevens die zijn opgeslagen in de gegevensarchieven waarnaar wordt verwezen door respectievelijk AzureStorageLinkedService en AzureSqlLinkedService. In EmpTableFromBlob geeft u op welke blobcontainer een blob bevat met de brongegevens en voor EmpSQLTable geeft u op in welke SQL-tabel de uitvoergegevens worden opgeslagen. 

### Invoergegevensset maken 
Een tabel is een rechthoekige gegevensset en bevat een schema. In deze stap maakt u een tabel met de naam **EmpBlobTable** die verwijst naar een blobcontainer in Azure Storage. Deze container wordt vertegenwoordigd door de gekoppelde **AzureStorageLinkedService**-service.

1. In de **editor** van de gegevensfactory klikt u op **Nieuwe gegevensset** op de werkbalk en klikt u op **Blobtabel** in de vervolgkeuzelijst. 
2. Vervang JSON in het rechterdeelvenster met het volgende JSON-fragment: 

        {
          "name": "EmpTableFromBlob",
          "properties": {
            "structure": [
              {
                "name": "FirstName",
                "type": "String"
              },
              {
                "name": "LastName",
                "type": "String"
              }
            ],
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
              "folderPath": "adftutorial/",
              "fileName": "emp.txt",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "external": true,
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }

        
     Houd rekening met het volgende: 
    
    - De gegevensset **type** wordt ingesteld op **AzureBlob**.
    - **linkedServiceName** wordt ingesteld op **AzureStorageLinkedService**. U hebt deze gekoppelde service gemaakt in stap 2.
    - **folderPath** wordt ingesteld op de container **adftutorial**. U kunt ook de naam van een blob opgeven in de map. Omdat u de naam van de blob niet opgeeft, worden de gegevens uit alle blobs in de container gezien als invoergegevens.  
    - De indeling **type** wordt ingesteld op **TextFormat**
    - Er zijn twee velden in het tekstbestand: **FirstName** en **LastName**, gescheiden door een kommateken (**columnDelimiter**) 
    - De **beschikbaarheid** wordt ingesteld op **elk uur** (de **frequentie** wordt ingesteld op **elk uur** en het **interval** wordt ingesteld op **1** ). De Data Factory-service zoekt dan elk uur naar gegevens in de hoofdmap van de blobcontainer (**adftutorial**) die u hebt opgegeven. 
    

    Als u geen **fileName** opgeeft voor een **invoer****tabel**, worden alle bestanden/blobs uit de invoermap (**folderPath**) beschouwd als invoer. Als u een fileName opgeeft in de JSON, wordt alleen het opgegeven bestand/de opgegeven blob gezien als invoer.
 
    Als u geen **fileName** opgeeft voor een **uitvoertabel**, krijgen de bestanden die worden gegenereerd in **folderPath** een naam op basis van de volgende indeling: Data.&lt;Guid\&gt;.txt (voorbeeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    Als u **folderPath** en **fileName** dynamisch wilt instellen op basis van de **SliceStart**-tijd, gebruikt u de eigenschap **partitionedBy**. In het volgende voorbeeld worden voor folderPath Year, Month en Day gebruikt van de SliceStart-waarde (tijd waarop is begonnen met het verwerken van het segment). Voor fileName wordt gebruikgemaakt van Hour van de SliceStart-waarde. Als er bijvoorbeeld een segment wordt geproduceerd voor 2014-10-20T08:00:00, wordt folderName ingesteld op wikidatagateway/wikisampledataout/2014/10/20 en wordt fileName ingesteld op 08.csv. 

        "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
            { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

    Zie de [naslaginformatie voor JSON-scriptverwerking](http://go.microsoft.com/fwlink/?LinkId=516971) voor meer informatie over de JSON-eigenschappen.

2. Klik op **Implementeren** op de werkbalk om te tabel **EmpTableFromBlob** te implementeren. Controleer of u **TABEL IS GEMAAKT** ziet op de titelbalk van de editor.

### Uitvoergegevensset maken
In dit gedeelte van de stap maakt u een uitvoertabel met de naam **EmpSQLTable** die verwijst naar een SQL-tabel in de Azure SQL Database die wordt vertegenwoordigd door de gekoppelde **AzureSqlLinkedService** service. 

1. In de **editor** van de gegevensfactory klikt u op **Nieuwe gegevensset** op de werkbalk en klikt u op **Azure SQL-tabel** in de vervolgkeuzelijst. 
2. Vervang JSON in het rechterdeelvenster met het volgende JSON-fragment:

        {
          "name": "EmpSQLTable",
          "properties": {
            "structure": [
              {
                "name": "FirstName",
                "type": "String"
              },
              {
                "name": "LastName",
                "type": "String"
              }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
              "tableName": "emp"
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }

        
     Houd rekening met het volgende: 
    
    * De gegevensset **type** wordt ingesteld op **AzureSQLTable**.
    * **linkedServiceName** wordt ingesteld op **AzureSqlLinkedService** (u hebt deze gekoppelde service gemaakt in stap 2).
    * **tablename** wordt ingesteld op **emp**.
    * Er zijn drie kolommen (**ID**, **FirstName** en **LastName**) in de emp-tabel in de database, maar ID is een identiteitskolom, dus u hoeft hier alleen **FirstName** en **LastName** in te voeren.
    * De **beschikbaarheid** wordt ingesteld op **elk uur** (de **frequentie** wordt ingesteld op **elk uur** en het **interval** wordt ingesteld op **1**).  De Data Factory-service maakt elk uur een uitvoergegevenssegment in de tabel **emp** in de Azure SQL-database.


3. Klik op **Implementeren** op de werkbalk om te tabel **EmpSQLTable** te implementeren.


## Pijplijn maken
In deze stap maakt u een pijplijn met een **kopieeractiviteit** die gebruikmaakt van **EmpTableFromBlob** als invoer en **EmpSQLTable** als uitvoer.

1. In de **editor** van de gegevensfactory klikt u op de werkbalk op **Nieuwe pijplijn**. Klik op **... (ellips)** op de werkbalk als u de knop niet ziet. U kunt ook met de rechtermuisknop op **Pijplijnen** klikken in de boomstructuur vervolgens klikken op **Nieuwe pijplijn**.

    ![Knop Nieuwe pijplijn van de editor][image-editor-newpipeline-button]
 
2. Vervang JSON in het rechterdeelvenster met het volgende JSON-fragment: 
        
        {
          "name": "ADFTutorialPipeline",
          "properties": {
            "description": "Copy data from a blob to Azure SQL table",
            "activities": [
              {
                "name": "CopyFromBlobToSQL",
                "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "EmpTableFromBlob"
                  }
                ],
                "outputs": [
                  {
                    "name": "EmpSQLTable"
                  }
                ],
                "typeProperties": {
                  "source": {
                    "type": "BlobSource"
                  },
                  "sink": {
                    "type": "SqlSink",
                    "writeBatchSize": 10000,
                    "writeBatchTimeout": "60:00:00"
                  }
                },
                "Policy": {
                  "concurrency": 1,
                  "executionPriorityOrder": "NewestFirst",
                  "retry": 0,
                  "timeout": "01:00:00"
                }
              }
            ],
            "start": "2015-07-12T00:00:00Z",
            "end": "2015-07-13T00:00:00Z"
          }
        } 

    Houd rekening met het volgende:

    - In het gedeelte Activiteiten is er slechts één activiteit waarvan **type** is ingesteld op **CopyActivity**.
    - De invoer voor de activiteit is ingesteld op **EmpTableFromBlob** en de uitvoer voor de activiteit is ingesteld op **EmpSQLTable**.
    - In het gedeelte **transformation** is **BlobSource** opgegeven als het brontype en **SqlSink** als het sink-type.

    Vervang de waarde van de eigenschap **start** door de huidige dag en de waarde **end** door de volgende dag. U hoeft alleen de datum in te vullen en kunt de tijd overslaan. Dit wordt dan bijvoorbeeld 2015-02-03, wat gelijk staat aan 2015-02-03T00:00:00Z
    
    Zowel de begin- als einddatum en -tijd moeten de [ISO-indeling](http://en.wikipedia.org/wiki/ISO_8601) hebben. Bijvoorbeeld: 2014-10-14T16:32:41Z. De tijd voor **end** is optioneel, maar wordt wel gebruikt voor deze zelfstudie. 
    
    Als u geen waarde opgeeft voor de eigenschap **end**, wordt automatisch **start + 48 uur** gebruikt. Als u de pijplijn voor onbepaalde tijd wilt uitvoeren, geeft u **9999-09-09** op als waarde voor de eigenschap **end**.
    
    Met het bovenstaande voorbeeld zijn er 24 gegevenssegmenten omdat er elk uur één gegevenssegment wordt gemaakt.
    
    Zie de [naslaginformatie voor JSON-scriptverwerking](http://go.microsoft.com/fwlink/?LinkId=516971) voor meer informatie over de JSON-eigenschappen.

4. Klik op **Implementeren** op de werkbalk om de tabel **ADFTutorialPipeline** te implementeren. Controleer of u het bericht **PIJPLIJN GEMAAKT** ziet.
5. Sluit nu de blade **Editor** door op **X** te klikken. Klik opnieuw op **X** om de blade ADFTutorialDataFactory met de werkbalk en boomstructuur te sluiten. Als u het bericht **uw niet-opgeslagen wijzigingen worden genegeerd** ziet, klikt u op **OK**.
6. U keert nu terug naar de blade **GEGEVENSFACTORY** van de **ADFTutorialDataFactory**.

**Gefeliciteerd.** U hebt een Azure-gegevensfactory, gekoppelde services, tabellen en een pijplijn gemaakt en u hebt een planning ingesteld voor de pijplijn.   
 
### De gegevensfactory weergeven in een diagramweergave 
1. In de blade **GEGEVENSFACTORY** klikt u op **Diagram**.

    ![Blade Gegevensfactory - tegel Diagram][image-datafactoryblade-diagramtile]

2. U ziet een diagram dat lijkt op het volgende: 

    ![Diagramweergave][image-data-factory-get-started-diagram-blade]

    U kunt inzoomen, uitzoomen, zoomen naar 100%, passend maken, pijplijnen en tabellen automatisch positioneren en de afkomst weergeven (upstream- en downstreamitems van geselecteerde items worden gemarkeerd).  Als u dubbelklikt op een object (invoer-/uitvoertabel of pijplijn) ziet u de bijbehorende eigenschappen. 
3. Klik met de rechtermuisknop op **ADFTutorialPipeline** in de diagramweergave en klik op **Pijplijn openen**. U ziet hier de activiteiten in de pijplijn in combinatie met de invoer- en uitvoergegevenssets van de activiteiten. In deze zelfstudie hebt u slechts één activiteit in de pijplijn (kopieeractiviteit), met EmpTableBlob als invoergegevensset en EmpSQLTable als uitvoergegevensset.   

    ![Pijplijn openen](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. Klik op **Gegevensfactory** in het koppelingenmenu linksboven om terug te keren naar de diagramweergave. In de diagramweergave worden alle pijplijnen weergegeven. In dit voorbeeld hebt u slechts één pijplijn gemaakt.   
 

## De pijplijn bewaken
In deze stap gebruikt u Azure Portal om te bewaken wat er gebeurt in een Azure Data Factory. U kunt ook PowerShell-cmdlets gebruiken om gegevenssets en pijplijnen te bewaken. Zie [Gegevensfactory bewaken en beheren met PowerShell-cmdlets][monitor-manage-using-powershell] voor meer informatie over het gebruik van cmdlets voor bewaking.

1. Navigeer naar [Azure Portal (Preview)][azure-portal] als u die nog niet hebt geopend. 
2. Als de blade van **ADFTutorialDataFactory** niet is geopend, opent u die door op **ADFTutorialDataFactory** te klikken op het **Startboard**. 
3. U ziet het aantal tabellen en de namen van de tabellen en pijplijn die u op deze blade hebt gemaakt.

    ![startpagina met namen][image-data-factory-get-started-home-page-pipeline-tables]

4. Klik nu op de tegel **Gegevenssets**.
5. In de blade **Gegevenssets** klikt u op **EmpTableFromBlob**. Dit is de invoertabel voor **ADFTutorialPipeline**.

    ![Gegevenssets waarvoor EmpTableFromBlob is geselecteerd][image-data-factory-get-started-datasets-emptable-selected]   
5. De gegevenssegmenten tot aan de huidige tijd zijn al gemaakt en hebben de status **Gereed** omdat het bestand **emp.txt** aanwezig is in de blobcontainer **adftutorial\input**. Controleer of er geen segmenten worden weergegeven in het gedeelte **Recent mislukte segmenten** onderaan.

    Zowel **Onlangs bijgewerkt segmenten** als **Onlangs mislukte segmenten** worden gesorteerd op basis van de **TIJD VAN DE LAATSTE UPDATE**. De bijwerktijd van een segment wordt in de volgende gevallen gewijzigd. 
    

    -  U kunt de status van het segment handmatig bijwerken door bijvoorbeeld **Set-AzureRmDataFactorySliceStatus** te gebruiken of door op **UITVOEREN** te klikken op de blade **SEGMENT** van het segment.
    -  De status van het segment wordt ook gewijzigd na een bepaalde uitvoering (een uitvoering is gestart, een uitvoering is beëindigd en mislukt, een uitvoering is beëindigd en voltooid, etc.).
 
    Klik op de naam van de lijsten of **... (ellipsen)** voor een langere lijst segmenten. Klik op **Filter** op de werkbalk om de segmenten te filteren.  
    
    Als u in plaats daarvan de gegevenssegmenten wilt sorteren op basis van de begin-/eindtijd, klikt u op de tegel **Gegevenssegmenten (op segmenttijd)**.   

    ![Gegevenssegmenten op basis van de segmenttijd][DataSlicesBySliceTime]   

6. Klik nu in de blade **Gegevenssets** op **EmpSQLTable**. Dit is de uitvoertabel voor **ADFTutorialPipeline**.

    ![blade gegevenssets][image-data-factory-get-started-datasets-blade]



     
6. De blade **EmpSQLTable** wordt nu als volgt weergegeven:

    ![blade tabel][image-data-factory-get-started-table-blade]
 
7. De gegevenssegmenten tot nu zijn al gemaakt en hebben de status **Gereed**. Er worden geen segmenten weergegeven in het gedeelte **Probleemsegmenten** onderaan.
8. Klik op **... (Ellips)** om alle segmenten te bekijken.

    ![blade gegevenssegmenten][image-data-factory-get-started-dataslices-blade]

9. Als u op een gegevenssegment uit de lijst klikt, ziet u de blade **GEGEVENSSEGMENT**.

    ![blade Gegevenssegment][image-data-factory-get-started-dataslice-blade]
  
    Als het segment niet de status **Gereed** heeft, kunt u de upstreamsegmenten bekijken die niet de status Gereed hebben en die voorkomen dat de huidige status wordt uitgevoerd. U ziet deze segmenten in de lijst **Upstreamsegmenten die niet gereed zijn**. 

11. In de blade **GEGEVENSSEGMENT** ziet u in de lijst onderaan alle activiteiten die worden uitgevoerd. Klik op een **activiteit die wordt uitgevoerd** om de blade **DETAILS UITVOERING VAN ACTIVITEIT**. 

    ![Details uitvoering van activiteit][image-data-factory-get-started-activity-run-details]

    
12. Klik op **X** om alle blades te sluiten tot u weer op de startblade bent voor **ADFTutorialDataFactory**.
14. (optioneel) Klik op **Pijplijnen** op de startpagina van **ADFTutorialDataFactory**, klik op **ADFTutorialPipeline** in de blade **Pijplijnen** en open een detailanalyse van invoertabellen (**Verbruikt**) of uitvoertabellen (**Geproduceerd**).
15. Open **SQL Server Management Studio**, maak verbinding maken met de Azure SQL Database en controleer of de rijen zijn ingevoegd in de tabel **emp** in de database.

    ![SQL-queryresultaten][image-data-factory-get-started-sql-query-results]


## Samenvatting 
In deze zelfstudie hebt u een Azure-gegevensfactory gemaakt om gegevens te kopiëren van een Azure-blob naar een Azure SQL-database. U hebt Azure Portal gebruikt om de gegevensfactory, gekoppelde services, gegevenssets en pijplijn te maken. In deze zelfstudie hebt u de volgende hoofdstappen uitgevoerd:  

1.  U hebt een Azure-**gegevensfactory** gemaakt.
2.  U hebt **gekoppelde services** gemaakt:
    1. Een gekoppelde **Azure Storage**-service om uw Azure-opslagaccount te koppelen dat invoergegevens bevat.    
    2. Een gekoppelde **Azure SQL**-service om uw Azure SQL Database te koppelen die uitvoergegevens bevat. 
3.  U hebt **gegevenssets** gemaakt waarin de invoer- en uitvoergegevens van pijplijnen worden beschreven.
4.  U hebt een **pijplijn** gemaakt met een **kopieeractiviteit** met **BlobSource** als bron en **SqlSink** als sink.  


## Zie ook
| Onderwerp | Beschrijving |
| :---- | :---- |
| [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) | Dit artikel biedt gedetailleerde informatie over de kopieeractiviteit die u tijdens deze zelfstudie hebt gemaakt. |
| [Plannen en uitvoeren](data-factory-scheduling-and-execution.md) | In dit artikel wordt uitleg gegeven over de plannings- en uitvoeringsaspecten van het Azure Data Factory-toepassingsmodel. |
| [Pijplijnen](data-factory-create-pipelines.md) | Met behulp van dit artikel krijgt u inzicht in de pijplijnen en activiteiten in Azure Data Factory en in de wijze waarop u deze kunt gebruiken om end-to-end gegevensgestuurde werkstromen te maken voor uw scenario of bedrijf. |
| [Gegevenssets](data-factory-create-datasets.md) | Op basis van dit artikel krijgt u inzicht in de gegevenssets in Azure Data Factory.
| [Pijplijnen bewaken en beheren met de app voor bewaking en beheer](data-factory-monitor-manage-app.md) | In dit artikel wordt beschreven hoe u pijplijnen bewaakt en beheert en hoe u fouten hierin oplost met de app voor bewaking en beheer. 

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account



[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started-using-editor/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started-using-editor/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started-using-editor/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started-using-editor/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started-using-editor/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started-using-editor/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started-using-editor/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started-using-editor/CreateNewResourceGroup.png


[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png


[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png
 


<!--HONumber=Jun16_HO2-->


