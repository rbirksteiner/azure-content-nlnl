<properties
    pageTitle="Uw eerste gegevensfactory bouwen (ARM-sjabloon) | Microsoft Azure"
    description="In deze zelfstudie maakt u een Azure Data Factory-voorbeeldpijplijn op basis van een Azure Resource Manager-sjabloon."
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

# Zelfstudie: bouw uw eerste Azure-gegevensfactory op basis van een Azure Resource Manager-sjabloon
> [AZURE.SELECTOR]
- [Overzicht van de zelfstudie](data-factory-build-your-first-pipeline.md)
- [De Data Factory-editor gebruiken](data-factory-build-your-first-pipeline-using-editor.md)
- [PowerShell gebruiken](data-factory-build-your-first-pipeline-using-powershell.md)
- [Visual Studio gebruiken](data-factory-build-your-first-pipeline-using-vs.md)
- [Een Resource Manager-sjabloon gebruiken](data-factory-build-your-first-pipeline-using-arm.md)


In dit artikel leert u hoe u een Azure Resource Manager-sjabloon (ARM) gebruikt voor het maken van uw eerste Azure-gegevensfactory. 


## Vereisten
Naast de vereisten die worden vermeld in het onderwerp Overzicht van de zelfstudie, moet u de volgende zaken installeren:

- U **moet** het artikel [Overzicht van de zelfstudie](data-factory-build-your-first-pipeline.md) lezen en de vereiste stappen volgen voordat u doorgaat. 
- **Installeer Azure PowerShell**. Volg de instructies in [Azure PowerShell installeren en configureren](../powershell-install-configure.md) om de meest recente versie van Azure PowerShell te installeren op uw computer.
- Dit artikel biedt geen conceptueel overzicht van de Azure Data Factory-service. Lees voor een gedetailleerd overzicht van de service het artikel [Inleiding tot Azure Data Factory](data-factory-introduction.md). 
- Zie [Azure Resource Manager-sjablonen samenstellen](../resource-group-authoring-templates.md) voor meer informatie over Azure Resource Manager-sjablonen (ARM). 

> [AZURE.IMPORTANT] U moet de vereiste stappen uit [Overzicht van de zelfstudie](data-factory-build-your-first-pipeline.md) voltooien om de procedures in dit artikel te kunnen volgen. 

## Een ARM-sjabloon maken

Maak een JSON-bestand met de naam **ADFTutorialARM.json** in de map **C:\ADFGetStarted**. Geef het bestand de volgende inhoud: 

Met de sjabloon kunt u de volgende Data Factory-entiteiten maken.

1. Een **gegevensfactory** met de naam **TutorialDataFactoryARM**. Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn kan één of meer activiteiten bevatten. Bijvoorbeeld een kopieeractiviteit om gegevens van een bron- naar een doelgegevensopslagplaats te kopiëren en een HDInsight Hive-activiteit om Hive-script uit te voeren voor het transformeren van invoergegevens naar productuitvoergegevens. 
2. Twee **gekoppelde services**: **StorageLinkedService** en **HDInsightOnDemandLinkedService**. Met deze gekoppelde services koppelt u uw Azure-opslagaccount en een Azure HDInsight-cluster op aanvraag aan uw gegevensfactory. Het Azure-opslagaccount bevat de in- en uitvoergegevens van de pijplijn in dit voorbeeld. De gekoppelde HDInsight-service wordt gebruikt om het Hive-script uit te voeren dat is opgegeven in de activiteit van de pijplijn in dit voorbeeld. U moet aangeven welk(e) gegevensarchief/rekenservices er in uw scenario worden gebruikt. Koppel die services aan de gegevensfactory door gekoppelde services te maken. 
3. Twee **gegevenssets (invoer/uitvoer)**: **AzureBlobInput** en **AzureBlobOutput**. Deze gegevenssets vertegenwoordigen de invoer- en uitvoergegevens voor Hive-verwerking. Deze gegevenssets verwijzen naar de **StorageLinkedService** die u eerder in deze zelfstudie hebt gemaakt. De gekoppelde service verwijst naar een Azure-opslagaccount en in de gegevenssets vindt u de container, map en bestandsnaam in de opslag van de invoer- en uitvoergegevens.   

Klik op het tabblad **De Data Factory-editor gebruiken** om over te schakelen naar het artikel met informatie over de JSON-eigenschappen die in deze sjabloon worden gebruikt.

> [AZURE.IMPORTANT] Wijzig de waarden van de variabelen **storageAccountName** en **storageAccountKey**. Wijzig ook de waarde van de variabele **dataFactoryName**, want de naam moet uniek zijn.


    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "TutorialDataFactoryARM",
            "storageAccountName":  "<AZURE STORAGE ACCOUNT NAME>" ,
            "storageAccountKey":  "<AZURE STORAGE ACCOUNT KEY>",
            "apiVersion": "2015-10-01",
            "storageLinkedServiceName": "AzureStorageLinkedService",
            "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
            "blobInputDataset": "AzureBlobInput",
            "blobOutputDataset": "AzureBlobOutput",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "linkedservices",
                        "name": "[variables('storageLinkedServiceName')]",
                        "apiVersion": "[variables('apiVersion')]",
                        "properties": {
                            "type": "AzureStorage",
                            "typeProperties": {
                                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
                            }
                        }
                    },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "linkedservices",
                        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
                        "apiVersion": "[variables('apiVersion')]",
                        "properties": {
                            "type": "HDInsightOnDemand",
                            "typeProperties": {
                                "clusterSize": 4,
                                "version":  "3.2",
                                "timeToLive": "00:05:00",
                                "osType": "windows",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                            }
                        }
                    },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "datasets",
                        "name": "[variables('blobInputDataset')]",
                        "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "type": "AzureBlob",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
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
                        },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "datasets",
                        "name": "[variables('blobOutputDataset')]",
                        "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "published": false,
                                "type": "AzureBlob",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
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
                        },
                        {
                            "dependsOn": [
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
                            ],
                            "type": "datapipelines",
                            "name": "[variables('dataFactoryName')]",
                            "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "description": "My first Azure Data Factory pipeline",
                                "activities": [
                                    {
                                        "type": "HDInsightHive",
                                        "typeProperties": {
                                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                                            "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                                            "defines": {
                                                "inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                                                "partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
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
                ]
            }
        ]
    }

Klik op het tabblad **De Data Factory-editor gebruiken** om over te schakelen naar het artikel met informatie over de JSON-eigenschappen die in deze sjabloon worden gebruikt.

Houd rekening met het volgende: 

- Data Factory maakt voor u een HDInsight-cluster **op basis van Windows** met de bovenstaande JSON. U zou Data Factory ook een HDInsight-cluster **op basis van Linux** kunnen laten maken. Zie [Gekoppelde on-demand HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie. 
- U kunt **uw eigen HDInsight-cluster** gebruiken in plaats van een on-demand HDInsight-cluster. Zie [Gekoppelde HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) voor meer informatie.
- Het HDInsight-cluster maakt een **standaardcontainer** in de blobopslag die u hebt opgegeven in de JSON (**linkedServiceName**). HDInsight verwijdert deze container niet wanneer het cluster wordt verwijderd. Dit is standaard. Met een gekoppelde on-demand HDInsight-service wordt er steeds een HDInsight-cluster gemaakt wanneer er een segment moet worden verwerkt, tenzij er een bestaand livecluster is (**timeToLive**). Het cluster wordt verwijderd wanneer het verwerken is voltooid.

    Naarmate er meer segmenten worden verwerkt, verschijnen er meer containers in uw Azure-blobopslag. Als u deze niet nodig hebt voor het oplossen van problemen met taken, kunt u ze verwijderen om de opslagkosten te verlagen. De namen van deze containers worden als volgt opgebouwd: adf**naamvanuwgegevensfactory**-**naamvangekoppeldeservice**- datum-/tijdstempel. Gebruik hulpprogramma's zoals [Microsoft Opslagverkenner](http://storageexplorer.com/) om containers in uw Azure-blobopslag te verwijderen.

Zie [Gekoppelde on-demand HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie.

> [AZURE.NOTE] Een ander voorbeeld van een ARM-sjabloon voor het maken van een Azure-gegevensfactory vindt u op [Github](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json).  

## Een gegevensfactory maken

1. Open **Azure PowerShell** en voer de volgende opdracht uit. 
    - Voer **Login-AzureRmAccount** uit en geef de gebruikersnaam en het wachtwoord op die u gebruikt om u aan te melden bij de Azure Portal.  
    - Voer de volgende opdracht uit om het abonnement te selecteren waarin u de gegevensfactory wilt maken.
            Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
1. Voer de volgende opdracht uit om Data Factory-entiteiten te implementeren met behulp van het ARM-sjabloon dat u in stap 1 hebt gemaakt. 

        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## De pijplijn bewaken
 
1.  Wanneer u zich hebt aangemeld bij de [Azure Portal](https://portal.azure.com/), klikt u op **Bladeren** en selecteert u **Gegevensfactory’s**.
        ![Bladeren -> Gegevensfactory’s](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.  Klik op de blade **Gegevensfactory’s** op de gegevensfactory (**TutorialFactoryARM**) die u hebt gemaakt.   
2.  Klik op de blade **Gegevensfactory** van uw gegevensfactory op **Diagram**.
        ![Tegel Diagram](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.  In de **diagramweergave** ziet u een overzicht van de pijplijnen en gegevenssets die voor deze zelfstudie worden gebruikt.
    
    ![Diagramweergave](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. Dubbelklik in de diagramweergave op de gegevensset **AzureBlobOutput**. U ziet het segment dat momenteel wordt verwerkt.

    ![Gegevensset](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Als het verwerken is voltooid, ziet u dat het segment de status **Gereed** heeft. Het maken van een on-demand HDInsight-cluster duurt normaal gesproken enige tijd (ongeveer 20 minuten). 

    ![Gegevensset](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png) 
10. Wanneer het segment de status **Gereed** heeft, controleert u de map **partitioneddata** in de container **adfgetstarted** in uw blobopslag voor de uitvoergegevens.  

Zie [Gegevenssets en pijplijn bewaken](data-factory-monitor-manage-pipelines.md) voor instructies over het gebruik van Azure Portal-blades om de pijplijn en gegevenssets te bewaken die u tijdens deze zelfstudie hebt gemaakt.

U kunt ook de app voor bewaking en beheer gebruiken om uw gegevenspijplijnen te bewaken. Zie [Azure Data Factory-pijplijnen bewaken en beheren met de app voor bewaking en beheer](data-factory-monitor-manage-app.md) voor meer informatie over het gebruik van de toepassing. 

> [AZURE.IMPORTANT] Het invoerbestand wordt verwijderd zodra het segment is verwerkt. Als u het segment dus opnieuw wilt uitvoeren of als u de zelfstudie opnieuw wilt doorlopen, uploadt u het invoerbestand (input.log) naar de map met invoergegevens van de container adfgetstarted.

## ARM-sjabloon voor het maken van een gateway
Hier volgt een ARM-voorbeeldsjabloon voor het op de achtergrond maken van een logische gateway. U moet een gateway installeren op uw on-premises computer of virtuele Azure IaaS-machine en de gateway met een sleutel registreren bij de Data Factory-service. Zie [Gegevens verplaatsen tussen on-premises en de cloud](data-factory-move-data-between-onprem-and-cloud.md) voor meer informatie.

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "GatewayUsingArmDF",
            "apiVersion": "2015-10-01",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "eastus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "gateways",
                        "apiVersion": "[variables('apiVersion')]",
                        "name": "GatewayUsingARM",
                        "properties": {
                            "description": "my gateway"
                        }
                    }            
                ]
            }
        ]
    }

Met deze sjabloon maakt u een gegevensfactory met de naam GatewayUsingArmDF, met een gateway met de naam GatewayUsingARM. 

## Zie ook
| Onderwerp | Beschrijving |
| :---- | :---- |
| [Activiteiten voor gegevenstransformatie](data-factory-data-transformation-activities.md) | Dit artikel bevat een lijst van activiteiten voor gegevenstransformatie (zoals de HDInsight Hive-transformatie die u in deze zelfstudie hebt gebruikt) die door Azure Data Factory worden ondersteund. |
| [Plannen en uitvoeren](data-factory-scheduling-and-execution.md) | In dit artikel wordt uitleg gegeven over de plannings- en uitvoeringsaspecten van het Azure Data Factory-toepassingsmodel. |
| [Pijplijnen](data-factory-create-pipelines.md) | Met behulp van dit artikel krijgt u inzicht in de pijplijnen en activiteiten in Azure Data Factory en in de wijze waarop u deze kunt gebruiken om end-to-end gegevensgestuurde werkstromen te maken voor uw scenario of bedrijf. |
| [Gegevenssets](data-factory-create-datasets.md) | Op basis van dit artikel krijgt u inzicht in de gegevenssets in Azure Data Factory.
| [Pijplijnen bewaken en beheren met de app voor bewaking en beheer](data-factory-monitor-manage-app.md) | In dit artikel wordt beschreven hoe u pijplijnen bewaakt en beheert en hoe u fouten hierin oplost met de app voor bewaking en beheer. 

  




<!--HONumber=Jun16_HO2-->


