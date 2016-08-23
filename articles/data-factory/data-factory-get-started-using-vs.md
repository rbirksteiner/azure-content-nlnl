<properties 
    pageTitle="Zelfstudie: een pijplijn maken met de kopieeractiviteit in Visual Studio" 
    description="In deze zelfstudie maakt u een Azure Data Factory-pijplijn met een kopieeractiviteit. Hiervoor gebruikt u Visual Studio." 
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

# Zelfstudie: een pijplijn maken met de kopieeractiviteit in Visual Studio
> [AZURE.SELECTOR]
- [Overzicht van de zelfstudie](data-factory-get-started.md)
- [De Data Factory-editor gebruiken](data-factory-get-started-using-editor.md)
- [Visual Studio gebruiken](data-factory-get-started-using-vs.md)
- [PowerShell gebruiken](data-factory-monitor-manage-using-powershell.md)
- [De wizard Kopiëren gebruiken](data-factory-copy-data-wizard-tutorial.md)

In deze zelfstudie doet u het volgende met behulp van Visual Studio 2013:

1. Twee gekoppelde services maken: **AzureStorageLinkedService1** en **AzureSqlinkedService1**. Met de AzureStorageLinkedService1 wordt een Azure-opslag gekoppeld en met AzureSqlLinkedService1 wordt een Azure SQL Database gekoppeld aan de gegevensfactory **ADFTutorialDataFactoryVS**. De invoergegevens van de pijplijn staan in een blobcontainer in Azure Blob Storage en de uitvoergegevens worden opgeslagen in een tabel in de Azure SQL Database. Daarom voegt u deze twee gegevensarchieven als gekoppelde services toe aan de gegevensfactory.
2. Twee gegevensfactorytabellen maken (**EmpTableFromBlob** en **EmpSQLTable**) die staan voor de invoer- en uitvoergegevens die zijn opgeslagen in de gegevensarchieven. In EmpTableFromBlob geeft u op welke blobcontainer een blob bevat met de brongegevens en voor EmpSQLTable geeft u op in welke SQL-tabel de uitvoergegevens worden opgeslagen. U geeft ook andere eigenschappen op, zoals de structuur van de gegevens, de beschikbaarheid van de gegevens, enzovoort...
3. Maak een pijplijn met de naam **ADFTutorialPipeline** in ADFTutorialDataFactoryVS. De pijplijn heeft een **kopieeractiviteit** waarmee invoergegevens van de Azure-blob naar de uitvoer-Azure SQL-tabel worden gekopieerd. Met de kopieerbewerking wordt de gegevensverplaatsing in Azure Data Factory uitgevoerd. De activiteit wordt mogelijk gemaakt door een wereldwijd beschikbare service waarmee gegevens veilig, betrouwbaar en schaalbaar kunnen worden gekopieerd tussen verschillende gegevensarchieven. Zie [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie over de kopieeractiviteit. 
4. Maak een gegevensfactory en implementeer gekoppelde services, tabellen en de pijplijn.    

## Vereisten
Lees het [Overzicht van de zelfstudie](data-factory-get-started.md) en voltooi de vereiste stappen volgen voordat u deze zelfstudie volgt.

De volgende zaken moeten op uw computer zijn geïnstalleerd: 
- Visual Studio 2013
- Download de Azure SDK voor Visual Studio 2013. Ga naar de [Azure-downloadpagina](https://azure.microsoft.com/downloads/) en klik op **VS 2013 installeren** in het gedeelte **.NET**.

## Een Visual Studio-project maken 
1. Open **Visual Studio 2013**. Klik op **File**, houd de muisaanwijzer op **New** en klik op **Project**. Het dialoogvenster **New Project** wordt weergegeven.  
2. Selecteer in het dialoogvenster **New Project** de sjabloon **DataFactory** en klik op **Empty Data Factory Project**. Als u de sjabloon DataFactory niet ziet, sluit u Visual Studio, installeert u de Azure SDK voor Visual Studio 2013 en opent u Visual Studio opnieuw.  

    ![Het dialoogvenster New Project](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. Geef een **naam** op voor het project, geef een **locatie** op en geef een naam op voor de **oplossing**. Klik vervolgens op **OK**.

    ![Solution Explorer](./media/data-factory-get-started-using-vs/solution-explorer.png)   

## Gekoppelde services maken
Met gekoppelde services worden gegevensarchieven of compute-services gekoppeld aan een Azure Data Factory. Een gegevensarchief kan een Azure Storage-, Azure SQL Database of een on-premises SQL Server-database zijn.

In deze stap maakt u twee gekoppelde services: **AzureStorageLinkedService1** en **AzureSqlLinkedService1**. Met de gekoppelde AzureStorageLinkedService1-service wordt een Azure-opslagaccount gekoppeld en met AzureSqlLinkedService wordt een Azure SQL Database gekoppeld aan de gegevensfactory **ADFTutorialDataFactory**. 

### De gekoppelde Azure Storage-service maken

4. Klik in Solution Explorer met de rechtermuisknop op **Linked Services**. Houd de muisaanwijzer op **Add** en klik op **New Item**.      
5. Selecteer in het dialoogvenster **Add New Item** de optie **Azure Storage Linked Service** in de lijst en klik op **Add**. 

    ![Nieuwe gekoppelde service](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. Vervang **accountname** en **accountkey** door de naam van uw Azure-opslagaccount en de bijbehorende sleutel. 

    ![Een gekoppelde Azure Storage-service](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. Sla het bestand **AzureStorageLinkedService1.json** op.

### De gekoppelde Azure SQL-service maken

5. Klik met de rechtermuisknop opnieuw op het knooppunt **Linked Services** in **Solution Explorer**. Houd de muisaanwijzer op **Add** en klik op **New Item**. 
6. Selecteer deze keer **Azure SQL Linked Service** en klik op **Add**. 
7. In het bestand **AzureSqlLinkedService1.json** vervangt u **servername**, **databasename**, **username@servername** en **password** door de namen van uw Azure SQL-server, database en gebruikersaccount en voert u uw wachtwoord in.    
8.  Sla het bestand **AzureSqlLinkedService1.json** op. 


## Gegevenssets maken
In de vorige stap hebt u de gekoppelde services **AzureStorageLinkedService1** en **AzureSqlLinkedService1** gemaakt om een Azure-opslagaccount en een Azure SQL Database te koppelen aan de gegevensfactory **ADFTutorialDataFactory**. In deze stap definieert u twee gegevensfactorytabellen (**EmpTableFromBlob** en **EmpSQLTable**) die staan voor de invoer- en uitvoergegevens die zijn opgeslagen in de gegevensarchieven waarnaar wordt verwezen door respectievelijk AzureStorageLinkedService1 en AzureSqlLinkedService1. In EmpTableFromBlob geeft u op welke blobcontainer een blob bevat met de brongegevens en voor EmpSQLTable geeft u op in welke SQL-tabel de uitvoergegevens worden opgeslagen.

### Invoergegevensset maken

9. Klik in **Solution Explorer** met de rechtermuisknop op **Tables**. Houd de muisaanwijzer op **Add** en klik op **New Item**.
10. In het dialoogvenster **Add New Item** selecteert u **Azure Blob** en klikt u op **Add**.   
10. Vervang de JSON-tekst door de volgende tekst en sla het bestand **AzureBlobLocation1.json** op. 

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
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
              "folderPath": "adftutorial/",
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

### Uitvoergegevensset maken

11. Klik in **Solution Explorer** opnieuw met de rechtermuisknop op **Tables**. Houd de muisaanwijzer op **Add** en klik op **New Item**.
12. In het dialoogvenster **Add New Item** selecteert u **Azure SQL** en klikt u op **Add**. 
13. Vervang de JSON-tekst door de volgende JSON en sla het bestand **AzureSqlTableLocation1.json** op.

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
            "linkedServiceName": "AzureSqlLinkedService1",
            "typeProperties": {
              "tableName": "emp"
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }

## Pijplijn maken 
U hebt tot nu toe gekoppelde invoer- en uitvoerservices gemaakt. U maakt nu met een **kopieeractiviteit** een pijplijn om gegevens van de Azure-blob te kopiëren naar de Azure SQL Database. 


1. Klik in **Solution Explorer** met de rechtermuisknop op **Pipelines**. Houd de muisaanwijzer op **Add** en klik op **New Item**.  
15. Selecteer **Copy Data Pipeline** in het dialoogvenster **Add New Item** en klik op **Add**. 
16. Vervang de JSON door de volgende JSON en sla het bestand **CopyActivity1.json** op.
            
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
                  "style": "StartOfInterval",
                  "retry": 0,
                  "timeout": "01:00:00"
                }
              }
            ],
            "start": "2015-07-12T00:00:00Z",
            "end": "2015-07-13T00:00:00Z",
            "isPaused": false
          }
        }

## Data Factory-entiteiten publiceren/implementeren
  
18. Klik met de rechtermuisknop op het project in Solution Explorer. Klik vervolgens op **Publish**. 
19. Als u het dialoogvenster **Sign in to your Microsoft account** ziet, voert u uw referenties in voor het account met het Azure-abonnement en klikt u op **Sign in**.
20. Het volgende dialoogvenster wordt weergegeven:

    ![Het dialoogvenster Publish](./media/data-factory-get-started-using-vs/publish.png)

21. Op de pagina Configure data factory doet u het volgende: 
    1. Selecteer **Create New Data Factory**.
    2. Voer **VSTutorialFactory** in als **naam**.  
    
        > [AZURE.NOTE]  
        > De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u tijdens het publiceren een foutmelding ontvangt over de naam van de gegevensfactory, wijzigt u de naam ervan (naar bijvoorbeeld uwnaamVSTutorialFactory) en publiceert u opnieuw. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
        
    3. Selecteer het juiste abonnement voor het veld **Subscription**. 
    4. Selecteer de **resourcegroep** voor de gegevensfactory die u wilt maken. 
    5. Selecteer de **regio** voor de gegevensfactory. 
    6. Klik op **Next** om over te schakelen naar de pagina **Publish Items**. 
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

## Samenvatting
In deze zelfstudie hebt u een Azure-gegevensfactory gemaakt om gegevens te kopiëren van een Azure-blob naar een Azure SQL-database. U hebt Visual Studio gebruikt om de gegevensfactory, gekoppelde services, gegevenssets en pijplijn te maken. In deze zelfstudie hebt u de volgende hoofdstappen uitgevoerd:  

1.  U hebt een Azure-**gegevensfactory** gemaakt.
2.  U hebt **gekoppelde services** gemaakt:
    1. Een gekoppelde **Azure Storage**-service om uw Azure-opslagaccount te koppelen dat invoergegevens bevat.    
    2. Een gekoppelde **Azure SQL**-service om uw Azure SQL Database te koppelen die uitvoergegevens bevat. 
3.  U hebt **gegevenssets** gemaakt waarin de invoer- en uitvoergegevens van pijplijnen worden beschreven.
4.  U hebt een **pijplijn** gemaakt met een **kopieeractiviteit** met **BlobSource** als bron en **SqlSink** als sink. 


## Server Explorer gebruiken om gegevensfactory’s weer te geven

1. Klik in het menu van **Visual Studio** op **View** en vervolgens op **Server Explorer**.
2. Vouw in het Server Explorer-venster **Azure** en **Data Factory** uit. Wanneer u **Sign in to Visual Studio** ziet, voert u het **account** in dat aan uw Azure-abonnement is gekoppeld, en klikt u op **Continue**. Voer het **wachtwoord** in en klik op **Sign in**. Visual Studio haalt informatie op uit alle Azure Data Factory’s in uw abonnement. U ziet de status van deze bewerking in het venster **Data Factory Task List**.
    ![Server Explorer](./media/data-factory-get-started-using-vs/server-explorer.png)
3. Klik met de rechtermuisknop op een gegevensfactory en selecteer Export Data Factory to New Project om een Visual Studio-project te maken op basis van een bestaande gegevensfactory.
    ![Een gegevensfactory exporteren naar een VS-project](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Data Factory-hulpprogramma's voor Visual Studio bijwerken
Doe het volgende om Azure Data Factory-hulpprogramma's voor Visual Studio bij te werken:

1. Klik in het menu op **Extra** en selecteer **Extensies en updates**. 
2. Selecteer **Updates** in het linkerdeelvenster en selecteer vervolgens **Visual Studio-galerie**.
4. Selecteer **Azure Data Factory-hulpprogramma's voor Visual Studio** en klik op **Bijwerken**. Als u deze vermelding niet ziet, beschikt u al over de nieuwste versie van de hulpprogramma's. 

Zie [Gegevenssets en pijplijn bewaken](data-factory-get-started-using-editor.md#monitor-pipeline) voor instructies over het gebruik van de Azure Portal om de pijplijn en gegevenssets te bewaken die u tijdens deze zelfstudie hebt gemaakt.

## Zie ook
| Onderwerp | Beschrijving |
| :---- | :---- |
| [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) | Dit artikel biedt gedetailleerde informatie over de kopieeractiviteit die u tijdens deze zelfstudie hebt gemaakt. |
| [Plannen en uitvoeren](data-factory-scheduling-and-execution.md) | In dit artikel wordt uitleg gegeven over de plannings- en uitvoeringsaspecten van het Azure Data Factory-toepassingsmodel. |
| [Pijplijnen](data-factory-create-pipelines.md) | Met behulp van dit artikel krijgt u inzicht in de pijplijnen en activiteiten in Azure Data Factory en in de wijze waarop u deze kunt gebruiken om end-to-end gegevensgestuurde werkstromen te maken voor uw scenario of bedrijf. |
| [Gegevenssets](data-factory-create-datasets.md) | Op basis van dit artikel krijgt u inzicht in de gegevenssets in Azure Data Factory.
| [Pijplijnen bewaken en beheren met de app voor bewaking en beheer](data-factory-monitor-manage-app.md) | In dit artikel wordt beschreven hoe u pijplijnen bewaakt en beheert en hoe u fouten hierin oplost met de app voor bewaking en beheer. 



<!--HONumber=Jun16_HO2-->


