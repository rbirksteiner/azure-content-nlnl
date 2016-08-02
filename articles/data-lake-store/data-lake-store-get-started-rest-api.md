<properties 
   pageTitle="Aan de slag met Data Lake Store met REST API| Microsoft Azure" 
   description="WebHDFS REST-API's gebruiken om bewerkingen uit te voeren in Data Lake Store" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/29/2016"
   ms.author="nitinme"/>

# Aan de slag met Azure Data Lake Store met REST-API's

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

In dit artikel leest u hoe u WebHDFS REST-API's en Data Lake Store REST-API's gebruikt voor accountbeheer en om bestandssysteembewerkingen uit te voeren in Azure Data Lake Store. Azure Data Lake Store beschikt over eigen REST-API's voor accountbeheerbewerkingen. Omdat Data Lake Store echter compatibel is met het HDFS- en Hadoop-ecosysteem, wordt ook het gebruik van WebHDFS REST-API's voor bestandssysteembewerkingen ondersteund.

>[AZURE.NOTE] Raadpleeg de [Naslaginformatie over REST API's van Azure Data Lake Store](https://msdn.microsoft.com/library/mt693424.aspx) voor gedetailleerde informatie over de REST-API-ondersteuning van Data Lake Store.

## Vereisten

- **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
- **Uw Azure-abonnement inschakelen** voor de openbare preview van Data Lake Store. Zie [Instructies](data-lake-store-get-started-portal.md#signup).
- **Een Azure Active Directory-toepassing maken**. Er zijn twee manieren waarop u kunt verifiëren met behulp van Azure Active Directory: **interactief** en **niet-interactief**. De vereisten zijn afhankelijk van de manier waarop u wilt verifiëren.
    * **Voor interactieve verificatie** (dat in dit artikel wordt gebruikt) moet u in Azure Active Directory een **Native Client-toepassing** maken. Wanneer u de toepassing hebt gemaakt, haalt u de volgende waarden op met betrekking tot de toepassing.
        - **Client-id** en **omleidings-URI** voor de toepassing ophalen
        - Gedelegeerde machtigingen instellen

    * **Voor niet-interactieve verificatie** moet u in Azure Active Directory een **webtoepassing** maken. Wanneer u de toepassing hebt gemaakt, haalt u de volgende waarden op met betrekking tot de toepassing.
        - **Client-id**, **clientgeheim** en **omleidings-URI** voor de toepassing ophalen
        - Gedelegeerde machtigingen instellen
        - Wijs de Azure Active Directory-toepassing toe aan een rol. De rol kan zich bevinden op het niveau van het bereik waarmee u toegang wilt geven aan de Azure Active Directory-toepassing. U kunt de toepassing bijvoorbeeld toewijzen op abonnementsniveau of op het niveau van een resourcegroep. Zie [Toepassing toewijzen aan een rol](../resource-group-create-service-principal-portal.md#assign-application-to-role) voor instructies. 

    Zie [Een Active Directory-toepassing en service-principal maken met portal](../resource-group-create-service-principal-portal.md) als u wilt weten hoe u deze waarden ophaalt, de machtigingen instelt en rollen toewijst.

- [cURL](http://curl.haxx.se/). In dit artikel wordt cURL gebruikt om te laten zien hoe u REST API-aanroepen maakt voor een Data Lake Store-account.

## Hoe verifieer ik met Azure Active Directory?

Er zijn twee benaderingen voor verificatie met Azure Active Directory.

### Interactief (gebruikersverificatie)

In dit scenario wordt de gebruiker via de toepassing gevraagd om zich te melden en worden alle bewerkingen uitgevoerd in de context van de gebruiker. Voer de volgende stappen uit voor interactieve verificatie.

1. Leid de gebruiker via de toepassing om naar de volgende URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<REDIRECT-URI> moet zijn gecodeerd om te worden gebruikt in een URL. Dus https://localhost, gebruik `https%3A%2F%2Flocalhost`)

    Voor deze zelfstudie kunt u de waarden van de tijdelijke aanduiding in bovenstaande URL vervangen en deze in de adresbalk van de webbrowser plakken. U wordt omgeleid om u te verifiëren met uw Azure-aanmelding. Wanneer u bent aangemeld, wordt het antwoord weergegeven in de adresbalk van de browser. Het antwoord heeft de volgende indeling:
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Leg de autorisatiecode uit het antwoord vast. Voor deze zelfstudie kunt u de autorisatiecode uit de adresbalk van de webbrowser kopiëren en deze in de POST-aanvraag doorgeven aan het eindpunt van het token, zoals hieronder wordt weergegeven:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] In dit geval hoeft de \<REDIRECT-URI> niet te worden gecodeerd.

3. Het antwoord is een JSON-object dat een toegangstoken (bijvoorbeeld `"access_token": "<ACCESS_TOKEN>"`) en een vernieuwingstoken (bijvoorbeeld `"refresh_token": "<REFRESH_TOKEN>"`) bevat. Uw toepassing gebruikt het toegangstoken om toegang te krijgen tot Azure Data Lake Store en het vernieuwingstoken om een nieuw toegangstoken op te halen wanneer het oude is verlopen.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Wanneer het toegangstoken is verlopen, kunt u als volgt met het vernieuwingstoken een nieuw toegangstoken aanvragen:

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Zie [De stroom voor autorisatiecodetoekenning](https://msdn.microsoft.com/library/azure/dn645542.aspx) voor meer informatie over interactieve gebruikersverificatie.

### Niet-interactief

In dit scenario verstrekt de toepassing zijn eigen referenties om bewerkingen uit te voeren. Daarvoor moet u een POST-aanvraag uitgeven, zoals in het voorbeeld hieronder. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

De uitvoer van deze aanvraag bevat een verificatietoken (in de onderstaande uitvoer aangegeven met `access-token`) die u vervolgens gaat doorgeven met de REST-API-aanroepen. Sla dit verificatietoken op in een tekstbestand. U hebt het verderop in dit artikel nodig.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

In dit artikel wordt de **niet-interactieve** benadering gebruikt. Zie [Service-naar-service-aanroepen met referenties](https://msdn.microsoft.com/library/azure/dn645543.aspx) voor meer informatie over niet-interactieve (service-naar-service) aanroepen.

## Een Data Lake Store-account maken

Deze bewerking is gebaseerd op de REST-API-aanroep die [hier](https://msdn.microsoft.com/library/mt694078.aspx) wordt gedefinieerd.

Gebruik de volgende cURL-opdracht: Vervang **\<yourstorename>** door de naam van uw Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Vervang \<`REDACTED`\> in de bovenstaande opdracht door het verificatietoken dat u eerder hebt opgehaald. De nettolading van de aanvraag voor deze opdracht zit in het bestand **input.json** dat is opgegeven voor de parameter `-d` hierboven. De inhoud van het bestand input.json ziet er ongeveer als volgt uit:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }   

## Mappen maken in een Data Lake Store-account

Deze bewerking is gebaseerd op de WebHDFS REST-API-aanroep die [hier](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory) wordt gedefinieerd.

Gebruik de volgende cURL-opdracht: Vervang **\<yourstorename>** door de naam van uw Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

Vervang \<`REDACTED`\> in de bovenstaande opdracht door het verificatietoken dat u eerder hebt opgehaald. Met deze opdracht maakt u een map **mytempdir** onder de hoofdmap van uw Data Lake Store-account.

Als de bewerking is geslaagd, wordt een antwoord van de volgende strekking weergegeven:

    {"boolean":true}

## Mappen weergeven in Data Lake Store-account

Deze bewerking is gebaseerd op de WebHDFS REST-API-aanroep die [hier](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory) wordt gedefinieerd.

Gebruik de volgende cURL-opdracht: Vervang **\<yourstorename>** door de naam van uw Data Lake Store.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

Vervang \<`REDACTED`\> in de bovenstaande opdracht door het verificatietoken dat u eerder hebt opgehaald.

Als de bewerking is geslaagd, wordt een antwoord van de volgende strekking weergegeven:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## Gegevens uploaden naar een Data Lake Store-account

Deze bewerking is gebaseerd op de WebHDFS REST-API-aanroep die [hier](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File) wordt gedefinieerd.

Gegevens uploaden met de WebHDFS REST-API is een proces dat uit twee stappen bestaat, zoals hieronder wordt uitgelegd.

1. Dien een HTTP PUT-aanvraag in zonder de bestandsgegevens te verzenden die u wilt uploaden. Vervang **\<yourstorename>** in de volgende opdracht door de naam van uw Data Lake Store.

        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

    De uitvoer van deze opdracht bevat een tijdelijke omleidings-URL, zoals hieronder wordt weergeven.

        HTTP/1.1 100 Continue

        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...

2. Nu moet u een andere HTTP PUT-aanvraag indienen voor de URL die wordt vermeld voor de eigenschap **Location** in het antwoord. Vervang **\<yourstorename>** door de naam van uw Data Lake Store.

        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

    De uitvoer ziet er ongeveer als volgt uit:

        HTTP/1.1 100 Continue

        HTTP/1.1 201 Created
        ...
        ...

## Gegevens lezen uit een Data Lake Store-account

Deze bewerking is gebaseerd op de WebHDFS REST-API-aanroep die [hier](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File) wordt gedefinieerd.

Het lezen van gegevens uit een Data Lake Store is een proces dat uit twee stappen bestaat.

* Eerst dient u een GET-aanvraag in voor het eindpunt `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Deze retourneert een locatie waarnaar u de volgende GET-aanvraag moet verzenden.
* Vervolgens dient u de GET-aanvraag in voor het eindpunt `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Hiermee wordt de inhoud van het bestand weergegeven.

Omdat de invoerparameters in de eerste en tweede stap echter gelijk zijn, kunt u de parameter `-L` gebruiken om de eerste aanvraag in te dienen. `-L` combineert in feite twee aanvragen in één en zorgt ervoor dat cURL de aanvraag opnieuw uitvoert op de nieuwe locatie. Ten slotte wordt de uitvoer van alle aanroepen van de aanvraag weergegeven, zoals u hieronder kunt zien. Vervang **\<yourstorename>** door de naam van uw Data Lake Store.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...
    
    HTTP/1.1 200 OK
    ...
    
    Hello, Data Lake Store user!

## Bestandsnamen wijzigen in een Data Lake Store-account

Deze bewerking is gebaseerd op de WebHDFS REST-API-aanroep die [hier](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory) wordt gedefinieerd.

Als u de naam van een bestand wil wijzigen, gebruikt u de volgende cURL-opdracht. Vervang **\<yourstorename>** door de naam van uw Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## Een bestand verwijderen uit een Data Lake Store-account

Deze bewerking is gebaseerd op de WebHDFS REST-API-aanroep die [hier](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory) wordt gedefinieerd.

Gebruik de volgende cURL-opdracht als u een bestand wilt verwijderen. Vervang **\<yourstorename>** door de naam van uw Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## Een Data Lake Store-account verwijderen

Deze bewerking is gebaseerd op de REST-API-aanroep die [hier](https://msdn.microsoft.com/library/mt694075.aspx) wordt gedefinieerd.

Gebruik de volgende cURL-opdracht als u een Data Lake Store-account wilt verwijderen. Vervang **\<yourstorename>** door de naam van uw Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

    HTTP/1.1 200 OK
    ...
    ...

## Zie ook

- [Open Source Big Data applications compatible with Azure Data Lake Store (Open Source Big Data-toepassingen die compatibel zijn met Azure Data Lake Store)](data-lake-store-compatible-oss-other-applications.md)
 



<!--HONumber=Jun16_HO2-->


