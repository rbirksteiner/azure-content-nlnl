<properties
    pageTitle="Uw eerste gegevensfactory bouwen (PowerShell) | Microsoft Azure"
    description="In deze zelfstudie maakt u een Azure Data Factory-voorbeeldpijplijn met behulp van Azure PowerShell."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"
/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/16/2016"
    ms.author="spelluru"/>

# Uw eerste Azure-gegevensfactory bouwen met Azure PowerShell
> [AZURE.SELECTOR]
- [Overzicht van de zelfstudie](data-factory-build-your-first-pipeline.md)
- [De Data Factory-editor gebruiken](data-factory-build-your-first-pipeline-using-editor.md)
- [PowerShell gebruiken](data-factory-build-your-first-pipeline-using-powershell.md)
- [Visual Studio gebruiken](data-factory-build-your-first-pipeline-using-vs.md)
- [Een Resource Manager-sjabloon gebruiken](data-factory-build-your-first-pipeline-using-arm.md)


In dit artikel leert u hoe u Azure PowerShell gebruikt voor het maken van uw eerste Azure-gegevensfactory. 


## Vereisten
Naast de vereisten die worden vermeld in het onderwerp Overzicht van de zelfstudie, moet u de volgende zaken installeren:

- U **moet** het artikel [Overzicht van de zelfstudie](data-factory-build-your-first-pipeline.md) lezen en de vereiste stappen volgen voordat u doorgaat.
- **Azure PowerShell**. Volg de instructies in [Azure PowerShell installeren en configureren](../powershell-install-configure.md) om de meest recente versie van Azure PowerShell te installeren op uw computer.
- (optioneel) Dit artikel omvat niet alle Data Factory-cmdlets. Zie [Naslaginformatie voor Data Factory-cmdlets](https://msdn.microsoft.com/library/dn820234.aspx) (Data Factory Cmdlet Reference) voor uitgebreide documentatie over Data Factory-cmdlets. 

Als u **versie <1.0** van Azure PowerShell gebruikt, moet u de cmdlets gebruiken die [hier](https://msdn.microsoft.com/library/azure/dn820234.aspx) worden beschreven. U moet ook de volgende opdrachten uitvoeren voordat u de Data Factory-cmdlets gebruikt: 
 
1. Open Azure PowerShell en voer de volgende opdrachten uit. Houd Azure PowerShell open tot het einde van deze zelfstudie. Als u het programma sluit en opnieuw opent, moet u deze opdrachten opnieuw uitvoeren.
    1. Voer **Add-AzureAccount** uit en voer de gebruikersnaam en het wachtwoord in die u gebruikt om u aan te melden bij de Azure Portal.
    2. Voer **Get-AzureSubscription** uit om alle abonnementen voor dit account weer te geven.
    3. Voer **Select-AzureSubscription** uit om het abonnement te selecteren waar u mee wilt werken. Dit moet hetzelfde abonnement zijn als het abonnement dat u in de Azure Portal gebruikt.
4. Schakel over naar de AzureResourceManager-modus (de Azure Data Factory-cmdlets zijn in deze modus beschikbaar): **Switch-AzureMode AzureResourceManager**.

Zie [Afschaffing van Switch AzureMode in Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell) voor meer informatie. 


## Een gegevensfactory maken

In deze stap gebruikt u Azure PowerShell om een Azure-gegevensfactory met de naam **FirstDataFactoryPSH** te maken. Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn kan één of meer activiteiten bevatten. Bijvoorbeeld een kopieeractiviteit om gegevens van een bron- naar een doelgegevensopslagplaats te kopiëren en een HDInsight Hive-activiteit om Hive-script uit te voeren voor het transformeren van invoergegevens naar productuitvoergegevens. U begint in deze stap met het maken van de gegevensfactory. 

1. Open Azure PowerShell en voer de volgende opdracht uit. Houd Azure PowerShell open tot het einde van deze zelfstudie. Als u het programma sluit en opnieuw opent, moet u deze opdrachten opnieuw uitvoeren.
    - Voer **Login-AzureRmAccount** uit en geef de gebruikersnaam en het wachtwoord op die u gebruikt om u aan te melden bij de Azure Portal.  
    - Voer **Get-AzureRmSubscription** uit om alle abonnementen voor dit account weer te geven.
    - Voer **Select-AzureRmSubscription<Name of the subscription>** uit om het abonnement te selecteren waar u mee wilt werken. Dit moet hetzelfde abonnement zijn als het abonnement dat u in de Azure Portal gebruikt.
3. Maak een Azure-resourcegroep met de naam **ADFTutorialResourceGroup** door de volgende opdracht uit te voeren.

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Voor sommige van de stappen in deze zelfstudie wordt ervan uitgegaan dat u de resourcegroep met de naam ADFTutorialResourceGroup gebruikt. Als u een andere resourcegroep gebruikt, moet u in plaats van ADFTutorialResourceGroup die groep gebruiken voor deze zelfstudie.
4. Voer de cmdlet **New-AzureRmDataFactory** uit om een gegevensfactory met de naam **FirstDataFactoryPSH** te maken.  

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"


Houd rekening met het volgende:
 
- De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u de foutmelding **De gegevensfactorynaam FirstDataFactoryPSH is niet beschikbaar** ziet, wijzigt u de naam (bijvoorbeeld in yournameFirstDataFactoryPSH). Gebruik deze naam in plaats van ADFTutorialFactoryPSH tijdens het uitvoeren van de stappen in de zelfstudie. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
- Als u Data Factory-exemplaren wilt maken, moet u bijdrager/beheerder zijn van het Azure-abonnement
- De naam van de gegevensfactory wordt in de toekomst mogelijk geregistreerd als DNS-naam en wordt daarmee ook voor iedereen zichtbaar.
- Als u de foutmelding **This subscription is not registered to use namespace Microsoft.DataFactory** ontvangt, voert u een van de volgende stappen uit en probeert u opnieuw te publiceren: 

    - Voer in Azure PowerShell de volgende opdracht uit om de Data Factory-provider te registreren. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        U kunt de volgende opdracht uitvoeren om te bevestigen dat de Data Factory-provider is geregistreerd. 
    
            Get-AzureRmResourceProvider
    - Meld u bij de [Azure Portal](https://portal.azure.com) aan met behulp van het Azure-abonnement en navigeer naar een Data Factory-blade of maak een gegevensfactory in de Azure Portal. De provider wordt dan automatisch voor u geregistreerd.

Voordat u een pijplijn maakt, moet u eerst enkele Data Factory-entiteiten maken. U maakt eerst gekoppelde services om gegevensopslag/berekeningen te koppelen aan uw gegevensopslag, vervolgens definieert u welke invoer- en uitvoergegevenssets de gegevens in de gekoppelde gegevensopslag vertegenwoordigen en daarna maakt u de pijplijn met een activiteit waarvoor gebruik wordt gemaakt van die gegevenssets. 

## Gekoppelde services maken 
In deze stap koppelt u uw Azure-opslagaccount en een on-demand Azure HDInsight-cluster aan uw gegevensfactory. Het Azure-opslagaccount bevat de in- en uitvoergegevens van de pijplijn in dit voorbeeld. De gekoppelde HDInsight-service wordt gebruikt om het Hive-script uit te voeren dat is opgegeven in de activiteit van de pijplijn in dit voorbeeld. U moet aangeven welk(e) gegevensarchief/rekenservices er in uw scenario worden gebruikt. Koppel die services aan de gegevensfactory door gekoppelde services te maken.

### Een gekoppelde Azure Storage-service maken
In deze stap koppelt u uw Azure-opslagaccount aan uw gegevensfactory. Voor deze zelfstudie gebruikt u hetzelfde Azure-opslagaccount om invoer- en uitvoergegevens en het HQL-scriptbestand op te slaan.

1. Maak een JSON-bestand met de naam StorageLinkedService.json in de map C:\ADFGetStarted. Geef dit bestand de volgende inhoud. Maak de map ADFGetStarted als deze nog niet bestaat.

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

    Vervang de **accountnaam** door de naam van uw Azure-opslagaccount en vervang de **accountsleutel** door de toegangssleutel van het Azure-opslagaccount. Zie [Toegangssleutels voor opslag weergeven, kopiëren en opnieuw genereren](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys) voor meer informatie over het verkrijgen van uw toegangssleutel voor opslag.

2. Schakel in Azure PowerShell over naar de map ADFGetStarted.
3. U kunt de cmdlet **New-AzureRmDataFactoryLinkedService** gebruiken om een gekoppelde service te maken. Voor deze cmdlet en andere Data Factory-cmdlets die u in deze zelfstudie gebruikt, moet u waarden doorgeven voor de parameters *ResourceGroupName* en *DataFactoryName*. U kunt ook **Get-AzureRmDataFactory** gebruiken om een **DataFactory**-object te verkrijgen. U geeft daarmee het object door zonder dat u de *ResourceGroupName* en de *DataFactoryName* hoeft op te geven telkens wanneer u een cmdlet uitvoert. Voer de volgende opdracht om de uitvoer van de cmdlet **Get-AzureRmDataFactory** toe te wijzen aan een **$df**-variabele.

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

4. Voer nu de cmdlet **New-AzureRmDataFactoryLinkedService** uit om de gekoppelde **StorageLinkedService**-service te maken.

        New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

    Als u de cmdlet **Get-AzureRmDataFactory** niet had uitgevoerd en u de uitvoer niet had toegewezen aan de **$df**-variabele, zou u als volgt waarden moeten opgeven voor de parameters *ResourceGroupName* en *DataFactoryName*.

        New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json

    Als u Azure PowerShell gedurende deze zelfstudie sluit, moet u de volgende keer dat u Azure PowerShell opent, de cmdlet **Get-AzureRmDataFactory** uitvoeren om de zelfstudie te voltooien.

### Een gekoppelde HDInsight-service maken
In deze stap koppelt u een on-demand HDInsight-cluster aan uw gegevensfactory. Het HDInsight-cluster wordt automatisch gemaakt tijdens runtime en wordt verwijderd wanneer het verwerken is voltooid en het cluster gedurende een opgegeven tijdsperiode niet actief is geweest. U kunt uw eigen HDInsight-cluster gebruiken in plaats van een on-demand HDInsight-cluster. Zie [Gekoppelde services berekenen](data-factory-compute-linked-services.md) voor meer informatie.  

1. Maak een JSON-bestand met de naam **HDInsightOnDemandLinkedService**.json in de map **C:\ADFGetStarted**. Geef dit bestand de volgende inhoud.

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "StorageLinkedService"
            }
          }
        }

    De volgende tabel bevat beschrijvingen van de JSON-eigenschappen die in het codefragment worden gebruikt:

  	| Eigenschap | Beschrijving |
  	| :------- | :---------- |
  	| Version | Hiermee wordt aangegeven dat de versie van het gemaakte HDInsight-cluster 3.2 is. | 
  	| ClusterSize | Hiermee maakt u een HDInsight-cluster met één knooppunt. | 
  	| TimeToLive | Hiermee wordt aangegeven hoelang het HDInsight-cluster niet actief moet zijn voordat het wordt verwijderd. |
  	| linkedServiceName | Hiermee geeft u het opslagaccount op dat wordt gebruikt voor het opslaan van de logboeken die door HDInsight worden gegenereerd. |

    Houd rekening met het volgende: 
    
    - Data Factory maakt voor u een HDInsight-cluster **op basis van Windows** met de bovenstaande JSON. U zou Data Factory ook een HDInsight-cluster **op basis van Linux** kunnen laten maken. Zie [Gekoppelde on-demand HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie. 
    - U kunt **uw eigen HDInsight-cluster** gebruiken in plaats van een on-demand HDInsight-cluster. Zie [Gekoppelde HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) voor meer informatie.
    - Het HDInsight-cluster maakt een **standaardcontainer** in de blobopslag die u hebt opgegeven in de JSON (**linkedServiceName**). HDInsight verwijdert deze container niet wanneer het cluster wordt verwijderd. Dit is standaard. Met een gekoppelde on-demand HDInsight-service wordt er steeds een HDInsight-cluster gemaakt wanneer er een segment moet worden verwerkt, tenzij er een bestaand livecluster is (**timeToLive**). Het cluster wordt verwijderd wanneer het verwerken is voltooid.
    
        Naarmate er meer segmenten worden verwerkt, verschijnen er meer containers in uw Azure-blobopslag. Als u deze niet nodig hebt voor het oplossen van problemen met taken, kunt u ze verwijderen om de opslagkosten te verlagen. De namen van deze containers worden als volgt opgebouwd: adf**naamvanuwgegevensfactory**-**naamvangekoppeldeservice**- datum-/tijdstempel. Gebruik hulpprogramma's zoals [Microsoft Opslagverkenner](http://storageexplorer.com/) om containers in uw Azure-blobopslag te verwijderen.

    Zie [Gekoppelde on-demand HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie. 
2. Voer de cmdlet **New-AzureRmDataFactoryLinkedService** uit om de gekoppelde service met de naam HDInsightOnDemandLinkedService te maken.

        New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


## Gegevenssets maken
In deze stap maakt u gegevenssets die de invoer- en uitvoergegevens voor Hive-verwerking vertegenwoordigen. Deze gegevenssets verwijzen naar de **StorageLinkedService** die u eerder in deze zelfstudie hebt gemaakt. De gekoppelde service verwijst naar een Azure-opslagaccount en in de gegevenssets vindt u de container, map en bestandsnaam in de opslag van de invoer- en uitvoergegevens.   

### Invoergegevensset maken
1. Maak een JSON-bestand met de naam **InputTable.json** in de map **C:\ADFGetStarted**. Geef dit bestand de volgende inhoud:

        {
            "name": "AzureBlobInput",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "StorageLinkedService",
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

    Met de bovenstaande JSON wordt een gegevensset gedefinieerd met de naam **AzureBlobInput**. Deze bevat de invoergegevens voor een activiteit in de pijplijn. Bovendien wordt hiermee aangegeven dat de invoergegevens zich bevinden in de blobcontainer met de naam **adfgetstarted** en in de map met de naam **inputdata**

    De volgende tabel bevat beschrijvingen van de JSON-eigenschappen die in het codefragment worden gebruikt:

  	| Eigenschap | Beschrijving |
  	| :------- | :---------- |
  	| type | De eigenschap type wordt ingesteld op AzureBlob, omdat de gegevens zich in de Azure-blobopslag bevinden. |  
  	| linkedServiceName | Deze eigenschap verwijst naar de StorageLinkedService die u eerder hebt gemaakt. |
  	| fileName | Deze eigenschap is optioneel. Als u deze eigenschap niet opgeeft, worden alle bestanden uit folderPath gekozen. In dit geval wordt alleen input.log verwerkt. |
  	| type | Omdat de logboekbestanden tekstbestanden zijn, gebruiken we TextFormat. | 
  	| columnDelimiter | De kolommen in de logboekbestanden worden gescheiden door middel van komma’s |
  	| frequency/interval | Als frequency wordt ingesteld op Month en de interval 1 is, betekent dat dat de invoersegmenten één keer per maand beschikbaar worden gemaakt. | 
  	| external | Deze eigenschap wordt ingesteld op true als de invoergegevens niet worden gegenereerd door de Data Factory-service. | 

2. Voer in Azure PowerShell de volgende opdracht uit om de Data Factory-gegevensset te maken.

        New-AzureRmDataFactoryDataset $df -File .\InputTable.json

### Uitvoergegevensset maken
U maakt nu de uitvoergegevensset die staat voor de uitvoergegevens die worden opgeslagen in de Azure-blobopslag.

1. Maak een JSON-bestand met de naam **OutputTable.json** in de map **C:\ADFGetStarted**. Geef dit bestand de volgende inhoud:

        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
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

    Met de bovenstaande JSON wordt een gegevensset gedefinieerd met de naam **AzureBlobOutput**. Deze bevat de uitvoergegevens voor een activiteit in de pijplijn. Bovendien wordt hiermee aangegeven dat de resultaten worden opgeslagen in de blobcontainer met de naam **adfgetstarted** en in de map met de naam **partitioneddata**. In het gedeelte **availability** wordt opgegeven dat de uitvoergegevensset op maandelijkse basis wordt geproduceerd.

2. Voer in Azure PowerShell de volgende opdracht uit om de Data Factory-gegevensset te maken.

        New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## Pijplijn maken
In deze stap maakt u uw eerste pijplijn met een **HDInsightHive**-activiteit. Het invoersegment wordt elke maand beschikbaar gesteld (frequency: Month, interval: 1), evenals het uitvoersegment. Ook de plannereigenschap van de activiteit wordt op maandelijks ingesteld (zie hieronder). De instellingen voor de uitvoergegevensset en de activiteitenplanner moeten overeenkomen. Op dit moment wordt de planning gebaseerd op de uitvoergegevensset. Daarom moet u ook een uitvoergegevensset maken als er tijdens de activiteit geen uitvoer wordt geproduceerd. Als er voor de activiteit geen invoer nodig is, kunt u het maken van de invoergegevensset overslaan. De eigenschappen die in de volgende JSON worden gebruikt, worden aan het einde van dit gedeelte beschreven. 


1. Maak een JSON-bestand met de naam MyFirstPipelinePSH.json in de map C:\ADFGetStarted. Geef dit bestand de volgende inhoud:

    > [AZURE.IMPORTANT] Vervang **storageaccountname** door de naam van uw opslagaccount in de JSON.
        
        {
            "name": "MyFirstPipeline",
            "properties": {
                "description": "My first Azure Data Factory pipeline",
                "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                            "scriptLinkedService": "StorageLinkedService",
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
    
    Het Hive-scriptbestand **partitionweblogs.hql** wordt opgeslagen in het Azure-opslagaccount (opgegeven door de scriptLinkedService met de naam **StorageLinkedService**) en in de map **script** van de container **adfgetstarted**.

    Het gedeelte **defines** wordt gebruikt om de runtime-instellingen die worden doorgegeven aan het Hive-script, op te geven als Hive-configuratiewaarden (bijvoorbeeld ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    Met de eigenschappen **start** en **end** van de pijplijn wordt opgegeven in welke periode de pijplijn actief is.

    In de activiteits-JSON geeft u op dat het Hive-script wordt uitgevoerd in de berekening die is opgegeven door de **linkedServiceName** – **HDInsightOnDemandLinkedService**.

    > [ACOM.NOTE] Zie [Anatomie van een pijplijn](data-factory-create-pipelines.md#anatomy-of-a-pipeline) voor meer informatie over de JSON-eigenschappen die in het bovenstaande voorbeeld worden gebruikt. 
2.  Controleer of u het bestand **input.log** ziet in de map **adfgetstarted/inputdata** in de Azure-blobopslag en voer de volgende opdracht uit om de pijplijn te implementeren. Aangezien de tijden voor **start** en **end** in het verleden vallen en **isPaused** is ingesteld op false, wordt de pijplijn (activiteit in de pijplijn) direct na het implementeren uitgevoerd. 

        New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. U hebt uw eerste pijplijn gemaakt met Azure PowerShell!

## De pijplijn bewaken
In deze stap gebruikt u Azure PowerShell om te bewaken wat er gebeurt in een Azure-gegevensfactory.

1. Voer **Get-AzureRmDataFactory** uit en wijs de uitvoer toe aan een **$df**-variabele.

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

2. Voer **Get-AzureRmDataFactorySlice** uit voor meer informatie over alle segmenten van **EmpSQLTable**, de uitvoertabel van de pijplijn.  

        Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

    De StartDateTime die u hier opgeeft, is dezelfde begintijd die u hebt opgegeven in de JSON van de pijplijn. De uitvoer ziet er als volgt uit.

        ResourceGroupName : ADFTutorialResourceGroup
        DataFactoryName   : FirstDataFactoryPSH
        DatasetName       : AzureBlobOutput
        Start             : 2/1/2014 12:00:00 AM
        End               : 3/1/2014 12:00:00 AM
        RetryCount        : 0
        State             : InProgress
        SubState          :
        LatencyStatus     :
        LongRetryCount    : 0


3. Voer **Get-AzureRmDataFactoryRun** uit om voor een bepaald segment gegevens over het uitvoeren van de activiteit op te halen.

        Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

    De uitvoer ziet er als volgt uit.
        
        Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
        ResourceGroupName   : ADFTutorialResourceGroup
        DataFactoryName     : FirstDataFactoryPSH
        DatasetName         : AzureBlobOutput
        ProcessingStartTime : 12/18/2015 4:50:33 AM
        ProcessingEndTime   : 12/31/9999 11:59:59 PM
        PercentComplete     : 0
        DataSliceStart      : 2/1/2014 12:00:00 AM
        DataSliceEnd        : 3/1/2014 12:00:00 AM
        Status              : AllocatingResources
        Timestamp           : 12/18/2015 4:50:33 AM
        RetryAttempt        : 0
        Properties          : {}
        ErrorMessage        :
        ActivityName        : RunSampleHiveActivity
        PipelineName        : MyFirstPipeline
        Type                : Script

    U kunt deze cmdlet blijven uitvoeren tot u ziet dat het segment de status **Gereed** of **Mislukt** heeft gekregen. Wanneer het segment de status Gereed heeft, controleert u de map **partitioneddata** in de container **adfgetstarted** in uw blobopslag voor de uitvoergegevens.  Het maken van een on-demand HDInsight-cluster duurt normaal gesproken enige tijd.

    ![Uitvoergegevens](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)


> [AZURE.IMPORTANT] Het invoerbestand wordt verwijderd zodra het segment is verwerkt. Als u het segment dus opnieuw wilt uitvoeren of als u de zelfstudie opnieuw wilt doorlopen, uploadt u het invoerbestand (input.log) naar de map met invoergegevens van de container adfgetstarted.

## Samenvatting 
In deze zelfstudie hebt u een Azure-gegevensfactory gemaakt voor het verwerken van gegevens. Dit hebt u gedaan door Hive-script uit te voeren op een HDInsight Hadoop-cluster. U hebt in de Azure Portal de Data Factory-editor gebruikt om de volgende stappen uit te voeren:  

1.  U hebt een Azure-**gegevensfactory** gemaakt.
2.  U hebt twee **gekoppelde services** gemaakt:
    1.  Een gekoppelde **Azure Storage**-service om te koppelen aan de Azure-blobopslag die de invoer-/uitvoerbestanden van de gegevensfactory bevat.
    2.  Een gekoppelde on-demand **Azure HDInsight**-service om te koppelen aan een on-demand HDInsight Hadoop-cluster van de gegevensfactory. Azure Data Factory maakt op tijd een HDInsight Hadoop-cluster om invoergegevens te verwerken en uitvoergegevens te produceren. 
3.  U hebt twee **gegevenssets** gemaakt waarin de invoer- en uitvoergegevens van de HDInsight Hive-activiteit in de pijplijn worden beschreven. 
4.  U hebt een **pijplijn** gemaakt met een **HDInsight Hive**-activiteit. 

## Volgende stappen
In dit artikel hebt u een pijplijn gemaakt met een transformatieactiviteit (HDInsight-activiteit) waarvoor een Hive-script wordt uitgevoerd op een on-demand Azure HDInsight-cluster. Zie [Zelfstudie: gegevens van een Azure-blob kopiëren naar Azure SQL](./data-factory-get-started.md) voor meer informatie over het gebruiken van een kopieeractiviteit om gegevens van een Azure-blob te kopiëren naar Azure SQL.

## Zie ook
| Onderwerp | Beschrijving |
| :---- | :---- |
| [Data Factory-cmdlet-verwijzing](https://msdn.microsoft.com/library/azure/dn820234.aspx) |  Zie de uitgebreide documentatie over Data Factory-cmdlets |
| [Activiteiten voor gegevenstransformatie](data-factory-data-transformation-activities.md) | Dit artikel bevat een lijst van activiteiten voor gegevenstransformatie (zoals de HDInsight Hive-transformatie die u in deze zelfstudie hebt gebruikt) die door Azure Data Factory worden ondersteund. |
| [Plannen en uitvoeren](data-factory-scheduling-and-execution.md) | In dit artikel wordt uitleg gegeven over de plannings- en uitvoeringsaspecten van het Azure Data Factory-toepassingsmodel. |
| [Pijplijnen](data-factory-create-pipelines.md) | Met behulp van dit artikel krijgt u inzicht in de pijplijnen en activiteiten in Azure Data Factory en in de wijze waarop u deze kunt gebruiken om end-to-end gegevensgestuurde werkstromen te maken voor uw scenario of bedrijf. |
| [Gegevenssets](data-factory-create-datasets.md) | Op basis van dit artikel krijgt u inzicht in de gegevenssets in Azure Data Factory.
| [Pijplijnen bewaken en beheren met de Azure Portal-blades](data-factory-monitor-manage-pipelines.md) | In dit artikel wordt beschreven hoe u pijplijnen bewaakt en beheert en hoe u fouten hierin oplost met de Azure Portal-blades. |
| [Pijplijnen bewaken en beheren met de app voor bewaking en beheer](data-factory-monitor-manage-app.md) | In dit artikel wordt beschreven hoe u pijplijnen bewaakt en beheert en hoe u fouten hierin oplost met de app voor bewaking en beheer. 




<!--HONumber=Jun16_HO2-->


