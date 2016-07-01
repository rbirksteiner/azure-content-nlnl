<properties
    pageTitle="Aan de slag met Azure Blob Storage (objectopslag) met .NET | Microsoft Azure"
    description="Sla niet-gestructureerde gegevens op in de cloud met Azure Blob Storage (objectopslag)."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="04/25/2016"
    ms.author="tamram"/>


# Aan de slag met Azure Blob Storage met .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Overzicht

Azure Blob Storage is een dienst waarmee ongestructureerde gegevens in de cloud worden opgeslagen als objecten/blobs. In Blob Storage kan elk type tekst of binaire gegevens, zoals een document, mediabestand of toepassingsinstallatieprogramma, worden opgeslagen. U kunt Blob Storage zien als een vorm van objectopslag.

### Over deze zelfstudie

Deze zelfstudie laat zien hoe u .NET-code kunt schrijven voor een aantal algemene scenario's die gebruikmaken van Azure Blob Storage. Scenario's die aan bod komen, zijn onder meer het uploaden, in een lijst weergeven, downloaden en verwijderen van blobs.

**Geschatte duur:** 45 minuten

**Vereisten:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Azure Storage-clientbibliotheek voor .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Azure Configuration Manager voor .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Een [Azure Storage-account](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### Naamruimtedeclaraties toevoegen

Voeg boven aan het `program.cs`-bestand de volgende `using`-instructies toe:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### De verbindingsreeks parseren

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### De Blob-serviceclient maken

Met de **CloudBlobClient** kunt u containers en blobs ophalen die zijn opgeslagen in Blob Storage. Hier volgt één manier om de serviceclient te maken:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

U bent nu klaar om code te schrijven die gegevens leest uit en schrijft naar Blob Storage.

## Een container maken

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

In dit voorbeeld ziet u hoe u een container kunt maken als deze nog niet bestaat:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Standaard is de nieuwe container privé, wat betekent dat u uw toegangssleutel voor opslag moet opgeven om blobs uit deze container te downloaden. Als u de bestanden in de container beschikbaar wilt maken voor iedereen, kunt u de container met de volgende code instellen op openbaar:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

Iedereen op internet kan blobs in een openbare container zien, maar de blobs kunnen alleen worden gewijzigd of verwijderd door iemand die de juiste accounttoegangssleutel of een Shared Access Signature heeft.

## Een blob uploaden naar een container

Azure Blob Storage ondersteunt blok-blobs en pagina-blobs.  In de meeste gevallen is de blok-blob het aangewezen type om te gebruiken.

Om een bestand naar een blok-blob te uploaden, haalt u een containerverwijzing op en gebruikt u deze om een blok-blobverwijzing op te halen. Zodra u een blobverwijzing hebt, kunt u er elke gewenste gegevensstroom naar uploaden door de methode **UploadFromStream** aan te roepen. Met deze bewerking wordt de blob gemaakt (als deze nog niet bestaat) of overschreven (als deze wel al bestaat).

Het volgende voorbeeld laat zien hoe u een blob uploadt naar een container. Hierbij wordt ervan uitgegaan dat de container al is gemaakt.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## De blobs in een container in een lijst weergeven

Als u een lijst van de blobs in een container wilt weergeven, moet u eerst een containerverwijzing ophalen. Vervolgens kunt u de methode **ListBlobs** van de container gebruiken voor het ophalen van de blobs en/of de mappen hierin. Voor toegang tot de uitgebreide set eigenschappen en methoden voor een geretourneerde **IListBlobItem** moet u dit casten naar een object van het type **CloudBlockBlob**, **CloudPageBlob** of **CloudBlobDirectory**.  Als het type onbekend is, kunt u typecontrole gebruiken om te bepalen waarnaar het moet worden gecast.  De volgende code toont hoe de URI van elk item in de `photos`-container wordt opgehaald en uitgevoerd:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Zoals hierboven beschreven, kunt u informatie over het pad opnemen in de naam van een blob. Hiermee maakt u een virtuele mapstructuur die u kunt ordenen en kunt doorlopen als een traditioneel bestandssysteem. Houd er rekening mee dat de mapstructuur alleen virtueel is: de enige beschikbare resources in Blob Storage zijn containers en blobs. De opslagclientbibliotheek biedt echter een **CloudBlobDirectory**-object om te verwijzen naar een virtuele map en het gemakkelijker te maken om te werken met blobs die op deze manier zijn ingedeeld.

Bekijk bijvoorbeeld de volgende set blok-blobs in een container met de naam `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Wanneer u **ListBlobs** aanroept in de container 'photos' (zoals in het bovenstaande voorbeeld), wordt er een hiërarchische opsomming geretourneerd. Deze opsomming bevat zowel **CloudBlobDirectory**- als **CloudBlockBlob**-objecten. Deze vertegenwoordigen respectievelijk de mappen en blobs in de container. De resulterende uitvoer ziet er als volgt uit:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


U kunt desgewenst de parameter **UseFlatBlobListing** van de methode **ListBlobs** instellen op **waar**. In dit geval wordt elke blob in de container geretourneerd als een **CloudBlockBlob**-object. De aanroep van **ListBlobs** om een platte lijst te retourneren, ziet er als volgt uit:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

De resultaten zien er als volgt uit:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## Blobs downloaden

Om blobs te downloaden, moet u eerst een blobverwijzing ophalen en vervolgens de methode **DownloadToStream** aanroepen. In het volgende voorbeeld wordt de methode **DownloadToStream** gebruikt om de blobinhoud over te dragen naar een stroomobject dat u vervolgens kunt blijven behouden in een lokaal bestand.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

U kunt ook de methode **DownloadToStream** gebruiken om de inhoud van een blob te downloaden als een tekenreeks.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## Blobs verwijderen

Als u een blob wilt verwijderen, moet u eerst een blobverwijzing ophalen en vervolgens de methode **Delete** aanroepen.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## Blobs asynchroon op pagina's weergeven

Als u een groot aantal blobs in een lijst weergeeft of als u het aantal resultaten dat per lijst wordt geretourneerd, wilt bepalen, kunt u blobs weergeven op pagina's met resultaten. Dit voorbeeld laat zien hoe resultaten op pagina's asynchroon worden geretourneerd, zodat de uitvoering niet wordt geblokkeerd tijdens het wachten op het retourneren van een groot aantal resultaten.

In dit voorbeeld gebruiken we een platte bloblijst, maar u kunt ook een hiërarchische lijst uitvoeren door de parameter `useFlatBlobListing` van de methode **ListBlobsSegmentedAsync** in te stellen op `false`.

Omdat de voorbeeldmethode een asynchrone bewerking aanroept, moet deze worden voorafgegaan door het sleutelwoord `async` en een **Task**-object retourneren. Het sleutelwoord 'await' dat is opgegeven voor de methode **ListBlobsSegmentedAsync**, onderbreekt de uitvoering van de voorbeeldmethode totdat de taak in de lijst is voltooid.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## Schrijven naar een toevoeg-blob

Een toevoeg-blob is een nieuw type blob, dat is geïntroduceerd in versie 5.x van de Azure Storage-clientbibliotheek voor .NET. Een toevoeg-blob is geoptimaliseerd voor toevoegbewerkingen, zoals logboekregistratie. Net als een blok-blob bestaat een toevoeg-blob uit blokken, maar wanneer u een nieuw blok aan een toevoeg-blob toevoegt, wordt het altijd toegevoegd aan het einde van de blob. U kunt een bestaand blok in een toevoeg-blob niet bijwerken of verwijderen. De blok-id's voor een toevoeg-blob worden niet weergegeven zoals voor een blok-blob.

Alle blokken in een toevoeg-blob kunnen verschillend van grootte zijn. De maximale grootte is 4 MB. Een toevoeg-blob kan maximaal 50.000 blokken bevatten. De maximale grootte van een toevoeg-blob is daarom iets meer dan 195 GB (4 MB X 50.000 blokken).

In onderstaand voorbeeld wordt een nieuwe toevoeg-blob gemaakt en worden er enkele gegevens aan toegevoegd, waarmee een eenvoudige logboekregistratiebewerking wordt gesimuleerd.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

Zie [Blok-blobs, pagina-blobs en toevoeg-blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx) voor meer informatie over de verschillen tussen de drie typen blobs.

## Beveiliging beheren voor blobs

Standaard beveiligt Azure Storage uw gegevens door de toegang te beperken tot de accounteigenaar, die in bezit is van de toegangssleutels van het account. Als u blobgegevens in uw opslagaccount wilt delen, moet u voorkomen dat u de beveiliging van de toegangssleutels van uw account in gevaar brengt. U kunt de blobgegevens ook versleutelen om ervoor te zorgen dat deze worden beveiligd tijdens de overdracht en in Azure Storage.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### Toegang tot blobgegevens beheren

Standaard zijn de blobgegevens in uw opslagaccount alleen toegankelijk voor de eigenaar van het opslagaccount. Wanneer er een verificatieaanvraag wordt gedaan bij Blob Storage, is de toegangssleutel voor het account standaard vereist. Mogelijk wilt u echter bepaalde blobgegevens beschikbaar stellen aan andere gebruikers. U hebt hiervoor twee opties:

- **Anonieme toegang:** u kunt een container of de blobs erin openbaar beschikbaar maken voor anonieme toegang. Zie [Anonieme leestoegang tot containers en blobs beheren](storage-manage-access-to-resources.md) voor meer informatie.
- **Shared Access Signature:** u kunt clients voorzien van een Shared Access Signature (SAS), die gedelegeerde toegang tot een resource in uw opslagaccount biedt gedurende een door u opgegeven tijdsbestek en met door u opgegeven machtigingen. Zie [Shared Access Signatures: inzicht in het SAS-model](storage-dotnet-shared-access-signature-part-1.md) voor meer informatie.

### Blobgegevens versleutelen

Azure Storage ondersteunt de versleuteling van blobgegevens op de client en op de server:

- **Versleuteling aan clientzijde:** de Storage-clientbibliotheek voor .NET ondersteunt de versleuteling van gegevens binnen clienttoepassingen voordat naar Azure Storage wordt geüpload en de ontsleuteling van gegevens tijdens het downloaden naar de client. De bibliotheek ondersteunt ook integratie met Azure Key Vault voor het beheer van sleutels voor opslagaccounts. Zie [Versleuteling aan clientzijde met .NET voor Microsoft Azure Storage](storage-client-side-encryption.md) voor meer informatie. Zie ook [Zelfstudie: blobs versleutelen en ontsleutelen in Microsoft Azure Storage met Azure Key Vault](storage-encrypt-decrypt-blobs-key-vault.md).
- **Versleuteling aan serverzijde**: Azure Storage ondersteunt nu ook versleuteling aan serverzijde. Zie [Azure Storage-service: versleuteling van data-at-rest (preview)](storage-service-encryption.md). 

## Volgende stappen

Nu u de basisprincipes van Blob Storage hebt geleerd, volgt u deze koppelingen voor meer informatie.

### Naslaginformatie over Blob Storage

- [Naslaginformatie over de Storage-clientbibliotheek voor .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [Naslaginformatie over REST API](http://msdn.microsoft.com/library/azure/dd179355)

### Conceptuele handleidingen

- [Gegevensoverdracht met het AzCopy-opdrachtregelprogramma](storage-use-azcopy.md)
- [Aan de slag met File Storage voor .NET](storage-dotnet-how-to-use-files.md)
- [Het gebruik van Azure Blob Storage met de WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Blog van het Azure Storage-team]: http://blogs.msdn.com/b/windowsazurestorage/
  [Verbindingsreeksen configureren]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [Naslaginformatie over de .NET-clientbibliotheek]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Naslaginformatie over REST API]: http://msdn.microsoft.com/library/azure/dd179355



<!--HONumber=Jun16_HO2-->


