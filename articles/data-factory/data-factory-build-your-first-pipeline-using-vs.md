<properties
    pageTitle="Uw eerste gegevensfactory bouwen (Visual Studio) | Microsoft Azure"
    description="In deze zelfstudie maakt u een Azure Data Factory-voorbeeldpijplijn met behulp van Visual Studio."
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
    ms.topic="hero-article" 
    ms.date="05/16/2016"
    ms.author="spelluru"/>

# Uw eerste Azure-gegevensfactory bouwen met Microsoft Visual Studio
> [AZURE.SELECTOR]
- [Overzicht van de zelfstudie](data-factory-build-your-first-pipeline.md)
- [De Data Factory-editor gebruiken](data-factory-build-your-first-pipeline-using-editor.md)
- [PowerShell gebruiken](data-factory-build-your-first-pipeline-using-powershell.md)
- [Visual Studio gebruiken](data-factory-build-your-first-pipeline-using-vs.md)
- [Een Resource Manager-sjabloon gebruiken](data-factory-build-your-first-pipeline-using-arm.md)


In dit artikel leert u hoe u Microsoft Visual Studio gebruikt voor het maken van uw eerste Azure-gegevensfactory. 

## Vereisten

1. U **moet** het artikel [Overzicht van de zelfstudie](data-factory-build-your-first-pipeline.md) lezen en de vereiste stappen volgen voordat u doorgaat.
2. Dit artikel biedt geen conceptueel overzicht van de Azure Data Factory-service. Lees voor een gedetailleerd overzicht van de service het artikel [Inleiding tot Azure Data Factory](data-factory-introduction.md).  

## Data Factory-entiteiten maken en implementeren  

### Vereisten

De volgende zaken moeten op uw computer zijn geïnstalleerd: 

- Visual Studio 2013 of Visual Studio 2015
- Download de Azure SDK voor Visual Studio 2013 of Visual Studio 2015. Ga naar de [Azure-downloadpagina](https://azure.microsoft.com/downloads/) en klik in het gedeelte **.NET** op **VS 2013** of **VS 2015**.
- Download de nieuwste Azure Data Factory-invoegtoepassing voor Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) of [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Als u gebruikmaakt van Visual Studio 2013, kunt u de invoegtoepassing ook bijwerken met behulp van de volgende handelingen: klik in het menu op **Extra** -> **Extensies en updates** -> **Online** -> **Visual Studio-galerie** -> **Microsoft Azure Data Factory-hulpprogramma's voor Visual Studio** -> **Bijwerken**. 
    
    

## Een Visual Studio-project maken 
1. Open **Visual Studio 2013** of **Visual Studio 2015**. Klik op **File**, houd de muisaanwijzer op **New** en klik op **Project**. Het dialoogvenster **New Project** wordt weergegeven.  
2. Selecteer in het dialoogvenster **New Project** de sjabloon **DataFactory** en klik op **Empty Data Factory Project**.   

    ![Het dialoogvenster New Project](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Geef een **naam** op voor het project, geef een **locatie** op en geef een naam op voor de **oplossing**. Klik vervolgens op **OK**.

    ![Solution Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

## Gekoppelde services maken
Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn kan één of meer activiteiten bevatten. Bijvoorbeeld een kopieeractiviteit om gegevens van een bron- naar een doelgegevensopslagplaats te kopiëren en een HDInsight Hive-activiteit om Hive-script uit te voeren voor het transformeren van invoergegevens naar productuitvoergegevens. U geeft de naam en de instellingen voor de gegevensfactory later op, wanneer u uw Data Factory-oplossing publiceert.

In deze stap koppelt u uw Azure-opslagaccount en een on-demand Azure HDInsight-cluster aan uw gegevensfactory. Het Azure-opslagaccount bevat de in- en uitvoergegevens van de pijplijn in dit voorbeeld. De gekoppelde HDInsight-service wordt gebruikt om het Hive-script uit te voeren dat is opgegeven in de activiteit van de pijplijn in dit voorbeeld. U moet aangeven welk(e) gegevensarchief/rekenservices er in uw scenario worden gebruikt. Koppel die services aan de gegevensfactory door gekoppelde services te maken.  

#### Een gekoppelde Azure Storage-service maken
In deze stap koppelt u uw Azure-opslagaccount aan uw gegevensfactory. Voor deze zelfstudie gebruikt u hetzelfde Azure-opslagaccount om invoer- en uitvoergegevens en het HQL-scriptbestand op te slaan. 

4. Klik in Solution Explorer met de rechtermuisknop op **Linked Services**. Houd de muisaanwijzer op **Add** en klik op **New Item**.      
5. Selecteer in het dialoogvenster **Add New Item** de optie **Azure Storage Linked Service** in de lijst en klik op **Add**. 
3. Vervang **accountname** en **accountkey** door de naam van uw Azure-opslagaccount en de bijbehorende sleutel. Zie [Toegangssleutels voor opslag weergeven, kopiëren en opnieuw genereren](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys) voor meer informatie over het verkrijgen van uw toegangssleutel voor opslag.

    ![Een gekoppelde Azure Storage-service](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Sla het bestand **AzureStorageLinkedService1.json** op.

#### Een gekoppelde HDInsight-service maken
In deze stap koppelt u een on-demand HDInsight-cluster aan uw gegevensfactory. Het HDInsight-cluster wordt automatisch gemaakt tijdens runtime en wordt verwijderd wanneer het verwerken is voltooid en het cluster gedurende een opgegeven tijdsperiode niet actief is geweest. U kunt uw eigen HDInsight-cluster gebruiken in plaats van een on-demand HDInsight-cluster. Zie [Gekoppelde services berekenen](data-factory-compute-linked-services.md) voor meer informatie. . 

1. Klik in **Solution Explorer** met de rechtermuisknop op **Linked Services**. Houd de muisaanwijzer op **Add** en klik op **New Item**.
2. Selecteer **HDInsight On Demand Linked Service** en klik op **Add**. 
3. Vervang de **JSON** door het volgende:

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "AzureStorageLinkedService1"
            }
          }
        }
    
    De volgende tabel bevat beschrijvingen van de JSON-eigenschappen die in het codefragment worden gebruikt:
    
    Eigenschap | Beschrijving
    -------- | -----------
    Version | Hiermee wordt aangegeven dat de versie van het gemaakte HDInsight-cluster 3.2 is. 
    ClusterSize | Hiermee maakt u een HDInsight-cluster met één knooppunt. 
    TimeToLive | Hiermee wordt aangegeven hoelang het HDInsight-cluster niet actief moet zijn voordat het wordt verwijderd.
    linkedServiceName | Hiermee geeft u het opslagaccount op dat wordt gebruikt voor het opslaan van de logboeken die door HDInsight worden gegenereerd.

    Houd rekening met het volgende: 
    
    - Data Factory maakt voor u een HDInsight-cluster **op basis van Windows** met de bovenstaande JSON. U zou Data Factory ook een HDInsight-cluster **op basis van Linux** kunnen laten maken. Zie [Gekoppelde on-demand HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie. 
    - U kunt **uw eigen HDInsight-cluster** gebruiken in plaats van een on-demand HDInsight-cluster. Zie [Gekoppelde HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) voor meer informatie.
    - Het HDInsight-cluster maakt een **standaardcontainer** in de blobopslag die u hebt opgegeven in de JSON (**linkedServiceName**). HDInsight verwijdert deze container niet wanneer het cluster wordt verwijderd. Dit is standaard. Met een gekoppelde on-demand HDInsight-service wordt er steeds een HDInsight-cluster gemaakt wanneer er een segment moet worden verwerkt, tenzij er een bestaand livecluster is (**timeToLive**). Het cluster wordt verwijderd wanneer het verwerken is voltooid.
    
        Naarmate er meer segmenten worden verwerkt, verschijnen er meer containers in uw Azure-blobopslag. Als u deze niet nodig hebt voor het oplossen van problemen met taken, kunt u ze verwijderen om de opslagkosten te verlagen. De namen van deze containers worden als volgt opgebouwd: adf**naamvanuwgegevensfactory**-**naamvangekoppeldeservice**- datum-/tijdstempel. Gebruik hulpprogramma's zoals [Microsoft Opslagverkenner](http://storageexplorer.com/) om containers in uw Azure-blobopslag te verwijderen.

    Zie [Gekoppelde on-demand HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie. 
4. Sla het bestand **HDInsightOnDemandLinkedService1.json** op.

## Gegevenssets maken
In deze stap maakt u gegevenssets die de invoer- en uitvoergegevens voor Hive-verwerking vertegenwoordigen. Deze gegevenssets verwijzen naar de **AzureStorageLinkedService1** die u eerder in deze zelfstudie hebt gemaakt. De gekoppelde service verwijst naar een Azure-opslagaccount en in de gegevenssets vindt u de container, map en bestandsnaam in de opslag van de invoer- en uitvoergegevens.   

#### Invoergegevensset maken

1. Klik in **Solution Explorer** met de rechtermuisknop op **Tables**. Houd de muisaanwijzer op **Add** en klik op **New Item**. 
2. Selecteer **Azure Blob** in de lijst, wijzig de naam van het bestand in **InputDataSet.json** en klik op **Add**.
3. Vervang de **JSON** in de editor door het volgende: 

    U maakt in het JSON-fragment een gegevensset met de naam **AzureBlobInput** die staat voor invoergegevens van een activiteit in de pijplijn. Daarbij geeft u op dat de invoergegevens zich bevinden in de blobcontainer met de naam **adfgetstarted** en in de map met de naam **inputdata**
        
        {
            "name": "AzureBlobInput",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "AzureStorageLinkedService1",
                "typeProperties": {
                    "fileName": "input.log",
                    "folderPath": "adfgetstarted/inputdata",
                    "format": {
                        "type": "TextFormat",
                        "columnDelimiter": ","
                    }
                },
                "availability": {
                    "frequency": "Month",
                    "interval": 1
                },
                "external": true,
                "policy": {}
            }
        } 

    De volgende tabel bevat beschrijvingen van de JSON-eigenschappen die in het codefragment worden gebruikt:

  	| Eigenschap | Beschrijving |
  	| :------- | :---------- |
  	| type | De eigenschap type wordt ingesteld op AzureBlob, omdat de gegevens zich in de Azure-blobopslag bevinden. |  
  	| linkedServiceName | Deze eigenschap verwijst naar de AzureStorageLinkedService1 die u eerder hebt gemaakt. |
  	| fileName | Deze eigenschap is optioneel. Als u deze eigenschap niet opgeeft, worden alle bestanden uit folderPath gekozen. In dit geval wordt alleen input.log verwerkt. |
  	| type | Omdat de logboekbestanden tekstbestanden zijn, gebruiken we TextFormat. | 
  	| columnDelimiter | De kolommen in de logboekbestanden worden gescheiden door middel van komma’s |
  	| frequency/interval | Als frequency wordt ingesteld op Month en de interval 1 is, betekent dat dat de invoersegmenten één keer per maand beschikbaar worden gemaakt. | 
  	| external | Deze eigenschap wordt ingesteld op true als de invoergegevens niet worden gegenereerd door de Data Factory-service. | 
      
    
3. Sla het bestand **InputDataset.json** op. 

 
#### Uitvoergegevensset maken
U maakt nu de uitvoergegevensset die staat voor de uitvoergegevens die worden opgeslagen in de Azure-blobopslag. 

1. Klik in **Solution Explorer** met de rechtermuisknop op **Tables**. Houd de muisaanwijzer op **Add** en klik op **New Item**. 
2. Selecteer **Azure Blob** in de lijst, wijzig de naam van het bestand in **OutputDataset.json** en klik op **Add**. 
3. Vervang de **JSON** in de editor door het volgende: 

    In het JSON-codefragment maakt u een gegevensset met de naam **AzureBlobOutput** en geeft u op welke gegevensstructuur er door het Hive-script wordt geproduceerd. Bovendien geeft u op dat de resultaten worden opgeslagen in de blobcontainer met de naam **adfgetstarted** en in de map met de naam **partitioneddata**. In het gedeelte **availability** wordt opgegeven dat de uitvoergegevensset op maandelijkse basis wordt geproduceerd.
    
        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
              "folderPath": "adfgetstarted/partitioneddata",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Month",
              "interval": 1
            }
          }
        }

    Raadpleeg het gedeelte **Invoergegevensset maken** voor een beschrijving van deze eigenschappen. U stelt de externe eigenschap niet in op een uitvoergegevensset, omdat de gegevensset wordt geproduceerd door de Data Factory-service.

4. Sla het bestand **OutputDataset.json** op.


### Pijplijn maken
In deze stap maakt u uw eerste pijplijn met een **HDInsightHive**-activiteit. Het invoersegment wordt elke maand beschikbaar gesteld (frequency: Month, interval: 1), evenals het uitvoersegment. Ook de plannereigenschap van de activiteit wordt op maandelijks ingesteld (zie hieronder). De instellingen voor de uitvoergegevensset en de activiteitenplanner moeten overeenkomen. Op dit moment wordt de planning gebaseerd op de uitvoergegevensset. Daarom moet u ook een uitvoergegevensset maken als er tijdens de activiteit geen uitvoer wordt geproduceerd. Als er voor de activiteit geen invoer nodig is, kunt u het maken van de invoergegevensset overslaan. De eigenschappen die in de volgende JSON worden gebruikt, worden aan het einde van dit gedeelte beschreven.

1. Klik in **Solution Explorer** met de rechtermuisknop op **Pipelines**. Houd de muisaanwijzer op **Add** en klik op **New Item**. 
2. Selecteer **Hive Transformation Pipeline** in de lijst en klik op **Add**. 
3. Vervang de **JSON** door het volgende codefragment.

    > [AZURE.IMPORTANT] Vervang **storageaccountname** door de naam van uw opslagaccount.

        {
            "name": "MyFirstPipeline",
            "properties": {
                "description": "My first Azure Data Factory pipeline",
                "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                            "scriptLinkedService": "AzureStorageLinkedService1",
                            "defines": {
                                "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                                "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                            }
                        },
                        "inputs": [
                            {
                                "name": "AzureBlobInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "AzureBlobOutput"
                            }
                        ],
                        "policy": {
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Month",
                            "interval": 1
                        },
                        "name": "RunSampleHiveActivity",
                        "linkedServiceName": "HDInsightOnDemandLinkedService"
                    }
                ],
                "start": "2016-04-01T00:00:00Z",
                "end": "2016-04-02T00:00:00Z",
                "isPaused": false
            }
        }

    In het JSON-codefragment maakt u een pijplijn die bestaat uit een enkele activiteit waarvoor gebruik wordt gemaakt van Hive om gegevens in een HDInsight-cluster te verwerken.
    
    In het JSON-codefragment maakt u een pijplijn die bestaat uit een enkele activiteit waarvoor gebruik wordt gemaakt van Hive om gegevens in een HDInsight-cluster te verwerken.
    
    Het Hive-scriptbestand **partitionweblogs.hql** wordt opgeslagen in het Azure-opslagaccount (opgegeven door de scriptLinkedService met de naam **AzureStorageLinkedService1**) en in de map **script** van de container **adfgetstarted**.

    Het gedeelte **defines** wordt gebruikt om de runtime-instellingen die worden doorgegeven aan het Hive-script, op te geven als Hive-configuratiewaarden (bijvoorbeeld ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    Met de eigenschappen **start** en **end** van de pijplijn wordt opgegeven in welke periode de pijplijn actief is.

    In de activiteits-JSON geeft u op dat het Hive-script wordt uitgevoerd in de berekening die is opgegeven door de **linkedServiceName** – **HDInsightOnDemandLinkedService**.

    > [AZURE.NOTE] Zie [Anatomie van een pijplijn](data-factory-create-pipelines.md#anatomy-of-a-pipeline) voor meer informatie over de JSON-eigenschappen die in het bovenstaande voorbeeld worden gebruikt. 
3. Sla het bestand **HiveActivity1.json** op.

### partitionweblogs.hql en input.log toevoegen als afhankelijkheid 

1. Klik in het **Solution Explorer**-venster met de rechtermuisknop op **Dependencies**. Houd de muisaanwijzer op **Add** en klik op **Existing Item**.  
2. Navigeer naar **C:\ADFGettingStarted** en selecteer de bestanden **partitionweblogs.hql** en **input.log**. Klik vervolgens op **Add**. U hebt deze twee bestanden gemaakt omdat dit vereist werd in het [Overzicht van de zelfstudie](data-factory-build-your-first-pipeline.md).

Wanneer u de oplossing in de volgende stap publiceert, wordt het bestand **partitionweblogs.hql** geüpload naar de scriptmap in de blobcontainer **adfgetstarted**.   

### Data Factory-entiteiten publiceren/implementeren

18. Klik met de rechtermuisknop op het project in Solution Explorer. Klik vervolgens op **Publish**. 
19. Als u het dialoogvenster **Sign in to your Microsoft account** ziet, voert u uw referenties in voor het account met het Azure-abonnement en klikt u op **Sign in**.
20. Het volgende dialoogvenster wordt weergegeven:

    ![Het dialoogvenster Publish](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Op de pagina Configure data factory doet u het volgende: 
    1. Selecteer **Create New Data Factory**.
    2. Voer **FirstDataFactoryUsingVS** in bij **Name**. 
    
        > [AZURE.IMPORTANT] De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u tijdens het publiceren de foutmelding **Data factory name “FirstDataFactoryUsingVS” is not available** ziet, wijzigt u de naam (bijvoorbeeld in yournameFirstDataFactoryUsingVS). Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
3. Selecteer het juiste abonnement voor het veld **Subscription**. 4. Selecteer de **resourcegroep** voor de gegevensfactory die u wilt maken. 5. Selecteer de **regio** voor de gegevensfactory. 6. Klik op **Next** om over te schakelen naar de pagina **Publish Items**. (Druk op **TAB** als u het veld Naam wilt verlaten, maar de knop **Next** is uitgeschakeld.) 
23. Op de pagina **Publish Items** controleert u of alle Data Factory-entiteiten zijn geselecteerd en klikt u op **Next** om over te schakelen naar de pagina **Summary**.     
24. Controleer de samenvatting en klik op **Next** om te beginnen met het implementatieproces en om de **implementatiestatus** te bekijken.
25. Op de pagina **Deployment Status** ziet u de status van het implementatieproces. Klik op Finish wanneer de implementatie is uitgevoerd. 

Houd rekening met het volgende: 

- Als u de foutmelding **This subscription is not registered to use namespace Microsoft.DataFactory** ontvangt, voert u een van de volgende stappen uit en probeert u opnieuw te publiceren: 

    - Voer in Azure PowerShell de volgende opdracht uit om de Data Factory-provider te registreren. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        U kunt de volgende opdracht uitvoeren om te bevestigen dat de Data Factory-provider is geregistreerd. 
    
            Get-AzureRmResourceProvider
    - Meld u bij de [Azure Portal](https://portal.azure.com) aan met behulp van het Azure-abonnement en navigeer naar een Data Factory-blade of maak een gegevensfactory in de Azure Portal. De provider wordt dan automatisch voor u geregistreerd.
-   De naam van de gegevensfactory wordt in de toekomst mogelijk geregistreerd als DNS-naam en wordt daarmee ook voor iedereen zichtbaar.
-   Als u Data Factory-exemplaren wilt maken, moet u bijdrager/beheerder zijn van het Azure-abonnement

 
## De pijplijn bewaken

6. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en doe het volgende:
    1. Klik op **Bladeren** en selecteer **Gegevensfactory’s**.
        ![Door gegevensfactory’s bladeren](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png) 
    2. Selecteer **FirstDataFactoryUsingVS** in de lijst met gegevensfactory’s. 
7. Klik op de startpagina van uw gegevensfactory op **Diagram**.
  
    ![Tegel Diagram](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
7. In de diagramweergave ziet u een overzicht van de pijplijnen en gegevenssets die voor deze zelfstudie worden gebruikt.
    
    ![Diagramweergave](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png) 
8. Als u alle activiteiten in de pijplijn wilt weergeven, klikt u met de rechtermuisknop op de pijplijn in het diagram. Klik vervolgens op Pijplijn openen. 

    ![Menu Pijplijn openen](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
9. Bevestig dat u de HDInsightHive-activiteit in de pijplijn ziet. 
  
    ![Pijplijnweergave openen](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Als u terug wilt keren naar de vorige weergave, klikt u op **Gegevensfactory** in het koppelingenmenu bovenaan. 
10. Dubbelklik in de **diagramweergave** op de gegevensset **AzureBlobInput**. Controleer of het segment de status **Gereed** heeft. Het kan enkele minuten duren voordat het segment de status Gereed heeft. Als dit niet is gebeurd nadat u enige tijd hebt gewacht, controleert u of het invoerbestand (input.log) in de juiste container (adfgetstarted) en in de juiste map (inputdata) staat.

    ![Invoersegment met de status Gereed](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
11. Klik op **X** om de blade **AzureBlobInput** te sluiten. 
12. Dubbelklik in de **diagramweergave** op de gegevensset **AzureBlobOutput**. U ziet het segment dat momenteel wordt verwerkt.

    ![Gegevensset](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Als het verwerken is voltooid, ziet u dat het segment de status **Gereed** heeft.
    >[AZURE.IMPORTANT] Het maken van een on-demand HDInsight-cluster duurt normaal gesproken enige tijd (ongeveer 20 minuten).  

    ![Gegevensset](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png) 
    
10. Wanneer het segment de status **Gereed** heeft, controleert u de map **partitioneddata** in de container **adfgetstarted** in uw blobopslag voor de uitvoergegevens.  
 
    ![Uitvoergegevens](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)

Zie [Gegevenssets en pijplijn bewaken](data-factory-monitor-manage-pipelines.md) voor instructies over het gebruik van de Azure Portal om de pijplijn en gegevenssets te bewaken die u tijdens deze zelfstudie hebt gemaakt.

U kunt ook de Monitor and Manage App gebruiken om uw gegevenspijplijnen te bewaken. Zie [Azure Data Factory-pijplijnen bewaken en beheren met de app voor bewaking en beheer](data-factory-monitor-manage-app.md) voor meer informatie over het gebruik van de toepassing. 

> [AZURE.IMPORTANT] Het invoerbestand wordt verwijderd zodra het segment is verwerkt. Als u het segment dus opnieuw wilt uitvoeren of als u de zelfstudie opnieuw wilt doorlopen, uploadt u het invoerbestand (input.log) naar de map met invoergegevens van de container adfgetstarted.
 

## Server Explorer gebruiken om gegevensfactory’s weer te geven

1. Klik in het menu van **Visual Studio** op **View** en vervolgens op **Server Explorer**.
2. Vouw in het Server Explorer-venster **Azure** en **Data Factory** uit. Wanneer u **Sign in to Visual Studio** ziet, voert u het **account** in dat aan uw Azure-abonnement is gekoppeld, en klikt u op **Continue**. Voer het **wachtwoord** in en klik op **Sign in**. Visual Studio haalt informatie op uit alle Azure Data Factory’s in uw abonnement. U ziet de status van deze bewerking in het venster **Data Factory Task List**.

    ![Server Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Klik met de rechtermuisknop op een gegevensfactory en selecteer **Export Data Factory to New Project** om een Visual Studio-project te maken op basis van een bestaande gegevensfactory.

    ![Een gegevensfactory exporteren](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Data Factory-hulpprogramma's voor Visual Studio bijwerken

Doe het volgende om Azure Data Factory-hulpprogramma's voor Visual Studio bij te werken:

1. Klik in het menu op **Extra** en selecteer **Extensies en updates**.
2. Selecteer **Updates** in het linkerdeelvenster en selecteer vervolgens **Visual Studio-galerie**.
3. Selecteer **Azure Data Factory-hulpprogramma's voor Visual Studio** en klik op **Bijwerken**. Als u deze vermelding niet ziet, beschikt u al over de nieuwste versie van de hulpprogramma's. 

## Configuratiebestanden gebruiken
U kunt in Visual Studio configuratiebestanden gebruiken om de eigenschappen voor gekoppelde services/tabellen/pijplijnen anders te configureren voor elke omgeving. 

Overweeg de volgende JSON-definitie te gebruiken voor een gekoppelde Azure Storage-service. Geef **connectionString** op met verschillende waarden voor accountname en accountkey op basis van de omgeving (ontwikkeling/tests/productie) waarin u Data Factory-entiteiten implementeert. U kunt dit doen door een afzonderlijk configuratiebestand te gebruiken voor elke omgeving. 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "description": "",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    } 

### Een configuratiebestand toevoegen
Voeg een configuratiebestand voor elke omgeving toe door de volgende stappen uit te voeren:   

1. Klik met de rechtermuisknop op het Data Factory-project in uw Visual Studio-oplossing, houd de muisaanwijzer op **Add** en klik op **New item**.
2. Selecteer in de lijst met geïnstalleerde sjablonen aan de linkerkant de optie **Config**, selecteer **Configuration File**, voer een **naam** in voor het configuratiebestand en klik op **Add**.

    ![Een configuratiebestand toevoegen](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Voeg configuratieparameters en de bijbehorende waarden toe in de onderstaande indeling:

        {
            "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
            "AzureStorageLinkedService1": [
                {
                    "name": "$.properties.typeProperties.connectionString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            ],
            "AzureSqlLinkedService1": [
                {
                    "name": "$.properties.typeProperties.connectionString",
                    "value":  "Server=tcp:spsqlserver.database.windows.net,1433;Database=spsqldb;User ID=spelluru;Password=Sowmya123;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            ]
        }

    In dit voorbeeld configureert u de eigenschap connectionString van een gekoppelde Azure Storage-service en een gekoppelde Azure SQL-service. De syntaxis voor het opgeven van de naam is [JsonPath](http://goessner.net/articles/JsonPath/).   

    Als JSON een eigenschap heeft met een matrix met waarden zoals hieronder wordt weergegeven:  

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
    
    Moet u als volgt een configuratie doorvoeren in het configuratiebestand (gebruik op nul gebaseerde indexering): 
        
        {
            "name": "$.properties.structure[0].name",
            "value": "FirstName"
        }
        {
            "name": "$.properties.structure[0].type",
            "value": "String"
        }
        {
            "name": "$.properties.structure[1].name",
            "value": "LastName"
        }
        {
            "name": "$.properties.structure[1].type",
            "value": "String"
        }

### Eigenschapnamen met spaties
Als de naam van een eigenschap spaties bevat, gebruikt u vierkante haken, zoals in het volgende voorbeeld wordt weergegeven (databaseservernaam): 

     {
         "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
         "value": "MyAsqlServer.database.windows.net"
     }


### Een oplossing implementeren met behulp van een configuratie
Wanneer u Azure Data Factory-entiteiten publiceert in de VS, kunt u opgeven welke configuratie u voor die publicatiebewerking wilt gebruiken. 

Entiteiten publiceren in een Azure Data Factory-project via een configuratiebestand:   

1. Klik met de rechtermuisknop op het Data Factory-project en klik op **Publish** om het dialoogvenster **Publish Items** weer te geven. 
2. Selecteer een bestaande gegevensfactory of geef op de pagina **Configure data factory** waarden op voor het maken van een nieuwe gegevensfactory. Klik vervolgens op **Next**.   
3. Op de pagina **Publish Items**: u ziet een vervolgkeuzelijst met beschikbare configuraties voor het veld **Select Deployment Config**.

    ![Een configuratiebestand selecteren](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)

4. Selecteer het **configuratiebestand** dat u wilt gebruiken en klik op **Next**. 
5. Controleer of u de naam van het JSON-bestand ziet op de pagina **Summary** en klik op **Next**. 
6. Klik op **Finish** nadat de implementatiebewerking is voltooid. 

Wanneer u implementeert, worden de waarden van het configuratiebestand gebruikt voor de eigenschappen in de JSON-bestanden voor Data Factory-entiteiten (gekoppelde services, tabellen of pijplijnen) voordat de entiteiten worden geïmplementeerd in de Azure Data Factory-service.   

## Samenvatting 
In deze zelfstudie hebt u een Azure-gegevensfactory gemaakt voor het verwerken van gegevens. Dit hebt u gedaan door Hive-script uit te voeren op een HDInsight Hadoop-cluster. U hebt in de Azure Portal de Data Factory-editor gebruikt om de volgende stappen uit te voeren:  

1.  U hebt een Azure-**gegevensfactory** gemaakt.
2.  U hebt twee **gekoppelde services** gemaakt:
    1.  Een gekoppelde **Azure Storage**-service om te koppelen aan de Azure-blobopslag die de invoer-/uitvoerbestanden van de gegevensfactory bevat.
    2.  Een gekoppelde on-demand **Azure HDInsight**-service om te koppelen aan een on-demand HDInsight Hadoop-cluster van de gegevensfactory. Azure Data Factory maakt op tijd een HDInsight Hadoop-cluster om invoergegevens te verwerken en uitvoergegevens te produceren. 
3.  U hebt twee **gegevenssets** gemaakt waarin de invoer- en uitvoergegevens van de HDInsight Hive-activiteit in de pijplijn worden beschreven. 
4.  U hebt een **pijplijn** gemaakt met een **HDInsight Hive**-activiteit.  


## Volgende stappen
In dit artikel hebt u een pijplijn gemaakt met een transformatieactiviteit (HDInsight-activiteit) waarvoor een Hive-script wordt uitgevoerd op een on-demand HDInsight-cluster. Zie [Zelfstudie: gegevens van een Azure-blob kopiëren naar Azure SQL](data-factory-get-started.md) voor meer informatie over het gebruiken van een kopieeractiviteit om gegevens van een Azure-blob te kopiëren naar Azure SQL.
  
## Zie ook
| Onderwerp | Beschrijving |
| :---- | :---- |
| [Activiteiten voor gegevenstransformatie](data-factory-data-transformation-activities.md) | Dit artikel bevat een lijst van activiteiten voor gegevenstransformatie (zoals de HDInsight Hive-transformatie die u in deze zelfstudie hebt gebruikt) die door Azure Data Factory worden ondersteund. | 
| [Plannen en uitvoeren](data-factory-scheduling-and-execution.md) | In dit artikel wordt uitleg gegeven over de plannings- en uitvoeringsaspecten van het Azure Data Factory-toepassingsmodel. |
| [Pijplijnen](data-factory-create-pipelines.md) | Met behulp van dit artikel krijgt u inzicht in de pijplijnen en activiteiten in Azure Data Factory en in de wijze waarop u deze kunt gebruiken om end-to-end gegevensgestuurde werkstromen te maken voor uw scenario of bedrijf. |
| [Gegevenssets](data-factory-create-datasets.md) | Op basis van dit artikel krijgt u inzicht in de gegevenssets in Azure Data Factory.
| [Pijplijnen bewaken en beheren met de app voor bewaking en beheer](data-factory-monitor-manage-app.md) | In dit artikel wordt beschreven hoe u pijplijnen bewaakt en beheert en hoe u fouten hierin oplost met de app voor bewaking en beheer. 


<!--HONumber=Jun16_HO2-->


