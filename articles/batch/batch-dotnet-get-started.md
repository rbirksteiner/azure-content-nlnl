<properties
    pageTitle="Zelfstudie - Aan de slag met de Azure Batch-bibliotheek voor .NET | Microsoft Azure"
    description="Leer de basisconcepten van Azure Batch en hoe u voor de Batch-service ontwikkelt met een eenvoudig scenario"
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="05/27/2016"
    ms.author="marsma"/>

# Aan de slag met de Azure Batch-bibliotheek voor .NET

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Ontdek in dit artikel de basisbeginselen van [Azure Batch][azure_batch] en de [Batch .NET][net_api]-bibliotheek aan de hand van een stapsgewijze C#-voorbeeldtoepassing. U leert hier hoe deze voorbeeldtoepassing gebruikmaakt van de Batch-service voor het verwerken van een parallelle workload in de cloud, en hoe deze samenwerkt met [Azure Storage](../storage/storage-introduction.md) voor het faseren en ophalen van bestanden. U leert algemene werkstroomtechnieken voor Batch-toepassingen. U krijgt hier ook een elementair inzicht in de belangrijkste onderdelen van Batch, zoals jobs, taken, pools en rekenknooppunten.

![Werkstroom van de Batch-oplossing (basis)][11]<br/>

## Vereisten

In dit artikel wordt ervan uitgegaan dat u praktische kennis hebt van C# en Visual Studio. Er wordt ook van uitgegaan dat u voldoet aan de vereisten voor het maken van een account die hieronder zijn opgegeven voor Azure en de Batch- en Storage-services.

### Accounts

- **Azure-account**: als u nog geen Azure-abonnement hebt, [maakt u een gratis Azure-account][azure_free_account].
- **Batch-account**: als u al een Azure-abonnement hebt, [maakt u een Azure Batch-account](batch-account-create-portal.md).
- **Opslagaccount**: zie [Een opslagaccount maken](../storage/storage-create-storage-account.md#create-a-storage-account) in [Over Azure-opslagaccounts](../storage/storage-create-storage-account.md).

> [AZURE.IMPORTANT] Batch ondersteunt momenteel *alleen* het opslagaccounttype **Algemeen**, zoals beschreven in stap 5 [Een opslagaccount maken](../storage/storage-create-storage-account.md#create-a-storage-account) in [Over Azure-opslagaccounts](../storage/storage-create-storage-account.md).

### Visual Studio

U moet beschikken over **Visual Studio 2013** of **Visual Studio 2015** om het voorbeeldproject te kunnen maken. U vindt gratis en evaluatieversies van Visual Studio in het [overzicht van Visual Studio 2015-producten][visual_studio].

### *DotNetTutorial*-codevoorbeeld

Het [DotNetTutorial][github_dotnettutorial]-voorbeeld is een van de vele codevoorbeelden die u vindt in de opslagplaats [azure-batch-samples][github_samples] in GitHub. U kunt het voorbeeld downloaden door te klikken op de knop **ZIP downloaden** op de introductiepagina van de opslagplaats of door te klikken op de koppeling van de directe download [azure-batch-samples-master.zip][github_samples_zip]. Nadat u de inhoud van het ZIP-bestand hebt uitgepakt, vindt u de oplossing in deze map:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### Azure Batch Explorer (optioneel)

[Azure Batch Explorer][github_batchexplorer] is een gratis hulpprogramma dat is opgenomen in de opslagplaats [azure-batch-samples][github_samples] in GitHub. Hoewel het niet is vereist om deze zelfstudie te voltooien, kan het nuttig zijn bij de ontwikkeling en foutopsporing van uw Batch-oplossingen.

## Overzicht DotNetTutorial-voorbeeldproject

Het *DotNetTutorial*-codevoorbeeld is een Visual Studio 2013-oplossing die uit twee projecten bestaat: **DotNetTutorial** en **TaskApplication**.

- **DotNetTutorial** is de clienttoepassing die met de Batch- en Storage-services communiceert voor het uitvoeren van een parallelle workload op rekenknooppunten (virtuele machines). DotNetTutorial wordt uitgevoerd op uw lokale werkstation.

- **TaskApplication** is het programma dat op rekenknooppunten in Azure wordt uitgevoerd om het echte werk te verrichten. In het voorbeeld parseert `TaskApplication.exe` de tekst in een bestand dat van Azure Storage is gedownload (het invoerbestand). Daarna creëert het een tekstbestand (het uitvoerbestand) met daarin een lijst van de eerste drie woorden die het invoerbestand bevat. Nadat TaskApplication het uitvoerbestand heeft gemaakt, wordt het bestand geüpload naar Azure Storage. Hierdoor is het voor de clienttoepassing beschikbaar als download. TaskApplication wordt parallel uitgevoerd op meerdere rekenknooppunten in de Batch-service.

Het volgende diagram illustreert de primaire bewerkingen die worden uitgevoerd door de clienttoepassing *DotNetTutorial*, en de toepassing die wordt uitgevoerd door de taken, *TaskApplication*. Deze basiswerkstroom is typerend voor vele rekenoplossingen die met Batch worden gemaakt. Hoewel hierin niet elke beschikbare functie in de Batch-service wordt getoond, zal vrijwel elk Batch-scenario soortgelijke processen bevatten.

![Batch-voorbeeldwerkstroom][8]<br/>

[**Stap 1.**](#step-1-create-storage-containers) Maak **containers** in Azure Blob Storage.<br/>
[**Stap 2.**](#step-2-upload-task-application-and-data-files) Upload taaktoepassings- en invoerbestanden naar containers.<br/>
[**Stap 3.**](#step-3-create-batch-pool) Maak een Batch-**pool**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** De pool **Begintaak** downloadt de binaire bestanden van de taak (TaskApplication) naar knooppunten wanneer deze aan de pool worden toegevoegd.<br/>
[**Stap 4.**](#step-4-create-batch-job) Maak een Batch-**job**.<br/>
[**Stap 5.**](#step-5-add-tasks-to-job) Voeg **taken** toe aan de job.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** De taken worden gepland om op knooppunten te worden uitgevoerd.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Elke taak downloadt de bijbehorende invoergegevens uit Azure Storage en wordt daarna uitgevoerd.<br/>
[**Stap 6.**](#step-6-monitor-tasks) Controleer taken.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Nadat taken zijn voltooid, uploaden zij hun uitvoergegevens naar Azure Storage.<br/>
[**Stap 7.**](#step-7-download-task-output) Download taakuitvoer uit Storage.

Zoals al is vermeld, zal niet elke Batch-oplossing exact deze stappen uitvoeren, en kan een oplossing ook veel meer stappen bevatten. De voorbeeldtoepassing *DotNetTutorial* toont echter de algemene processen die u in een Batch-oplossing terugvindt.

## Het *DotNetTutorial*-voorbeeldproject maken

Voordat u het voorbeeld met succes kunt uitvoeren, moet u in het *DotNetTutorial*-project in het bestand `Program.cs` Batch- en Storage-accountreferenties opgeven. Als u dit nog niet hebt gedaan, opent u de oplossing in Visual Studio door te dubbelklikken op het oplossingsbestand `DotNetTutorial.sln`. U kunt de oplossing ook vanuit Visual Studio openen met het menu **Bestand > Openen > Project/Oplossing**.

Open `Program.cs` in het project *DotNetTutorial*. Voeg daarna uw referenties toe zoals deze boven in het bestand zijn vermeld:

```
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [AZURE.IMPORTANT] Zoals eerder vermeld, moet u in Azure Storage momenteel de referenties voor een **algemeen** opslagaccount opgeven. Uw Batch-toepassingen gebruiken Blob Storage in een opslagaccount van het type **Algemeen**. Geef niet de referenties op voor een opslagaccount die is gemaakt door het accounttype *Blob Storage* te selecteren.

U vindt uw Batch- en Storage-accountreferenties op de accountblade van elke service in [Azure Portal][azure_portal]:

![Batch-referenties in de portal][9]
![Storage-referenties in de portal][10]<br/>

Nu dat u het project met uw referenties hebt bijgewerkt, klikt u met de rechtermuisknop op de oplossing in Solution Explorer en klikt u op **Build Solution** (Oplossing bouwen). Bevestig het herstel van alle NuGet-pakketten als dit wordt gevraagd.

> [AZURE.TIP] Als de NuGet-pakketten niet automatisch worden hersteld of als in foutberichten wordt gemeld dat de pakketten niet kunnen worden hersteld, controleert u of [NuGet Package Manager][nuget_packagemgr] is geïnstalleerd. Schakel vervolgens het downloaden van ontbrekende pakketten in. Zie [Enabling Package Restore During Build][nuget_restore] (Pakket herstellen inschakelen tijdens bouwen) om het downloaden van pakketten in te schakelen.

In de volgende secties wordt de voorbeeldtoepassing uitgesplitst in de stappen die met de toepassing worden uitgevoerd om een workload in de Batch-service te verwerken en worden die stappen in detail besproken. Bij het lezen van de rest van dit artikel kunt u het best de geopende oplossing in Visual Studio raadplegen omdat niet elke regel in de code van het voorbeeld wordt besproken.

Navigeer naar de bovenkant van de methode `MainAsync` in het bestand `Program.cs` van het *DotNetTutorial*-project om bij stap 1 te starten. Elke stap hieronder volgt min of meer de voortgang van de methodeaanroepen in `MainAsync`.

## Stap 1: opslagcontainers maken

![Containers maken in Azure Storage][1]
<br/>

Batch bevat ingebouwde ondersteuning voor interactie met Azure Storage. Containers in uw opslagaccount bevatten de bestanden die nodig zijn voor de taken die in uw Batch-account worden uitgevoerd. De containers bieden ook een plaats voor de opslag van de uitvoergegevens die de taken opleveren. Het eerste wat de clienttoepassing *DotNetTutorial* doet, is drie containers maken in [Azure Blob Storage](../storage/storage-introduction.md):

- **application**: in deze container worden de toepassing die door de taken wordt uitgevoerd, evenals alle afhankelijkheden hiervan, zoals DLL-bestanden, opgeslagen.
- **input**: taken downloaden de te verwerken gegevensbestanden uit de *invoer*container.
- **output**: nadat taken de verwerking van invoerbestanden hebben voltooid, uploaden zij de resultaten naar de *uitvoer*container.

Om met een opslagaccount te communiceren en containers te maken, gebruiken we de [Azure Storage-clientbibliotheek voor .NET][net_api_storage]. We maken een verwijzing naar het account met [CloudStorageAccount][net_cloudstorageaccount] en van hieruit maken we een [CloudBlobClient][net_cloudblobclient]:

```
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

We gebruiken in de toepassing de verwijzing `blobClient` en geven deze als een parameter door aan een aantal methoden. Een voorbeeld hiervan bevindt zich in het codeblok dat onmiddellijk na het bovenstaande blok volgt, waar we `CreateContainerIfNotExistAsync` aanroepen om de containers daadwerkelijk te maken.

```
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Zodra de containers zijn gemaakt, kan de toepassing nu de bestanden uploaden die door de taken worden gebruikt.

> [AZURE.TIP] [How to use Blob Storage from .NET](../storage/storage-dotnet-how-to-use-blobs.md) (Blob Storage gebruiken met .NET) biedt een goed overzicht van hoe u met Azure Storage-containers en -blobs werkt. Wanneer u met Batch begint te werken, moet dat artikel hoog in uw leeslijst zijn vermeld.

## Stap 2: taaktoepassings- en gegevensbestanden uploaden

![Taaktoepassings- en invoer(gegevens)bestanden uploaden naar containers][2]
<br/>

In de bewerking voor het uploaden van bestanden definieert *DotNetTutorial* eerst verzamelingen van bestandspaden naar **toepassing**sbestanden en **invoer**bestanden op de lokale computer. Daarna worden deze bestanden geüpload naar de containers die u in de vorige stap hebt gemaakt.

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Er zijn in `Program.cs` twee methoden die betrokken zijn bij het uploadproces:

- `UploadFilesToContainerAsync`: deze methode retourneert een verzameling [ResourceFile][net_resourcefile]-objecten (zie hieronder) en roept intern `UploadFileToContainerAsync` aan om elk bestand te uploaden dat in de parameter *filePaths* wordt doorgegeven.
- `UploadFileToContainerAsync`: dit is de methode die daadwerkelijk het bestand uploadt en de [ResourceFile][net_resourcefile]-objecten maakt. Nadat het bestand is geüpload, krijgt deze methode een Shared Access Signature (SAS) voor het bestand en retourneert deze een ResourceFile-object dat het bestand vertegenwoordigt. Ook Shared Access Signatures worden hieronder besproken.

```
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath, FileMode.Open);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### ResourceFiles

[ResourceFile][net_resourcefile] levert aan taken in Batch de URL naar een bestand in Azure Storage dat naar een rekenknooppunt zal worden gedownload voordat deze taak wordt uitgevoerd. De eigenschap [ResourceFile.BlobSource][net_resourcefile_blobsource] geeft de volledige URL van het bestand zoals dit zich in Azure Storage bevindt. De URL kan ook een Shared Access Signature (SAS) bevatten, die veilige toegang tot het bestand biedt. De meeste typen taken in Batch .NET bevatten een eigenschap *ResourceFiles*, waaronder:

- [CloudTask][net_task]
- [StartTask][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

De DotNetTutorial-voorbeeldtoepassing gebruikt niet de taaktypen JobPreparationTask of JobReleaseTask. Meer informatie hierover vindt u in [Run job preparation and completion tasks on Azure Batch compute nodes](batch-job-prep-release.md) (Jobvoorbereidings- en jobvoltooiingstaken uitvoeren op Azure Batch-rekenknooppunten).

### Shared Access Signature (SAS)

Shared Access Signatures zijn tekenreeksen die, wanneer ze deel uitmaken van een URL, beveiligde toegang bieden tot containers en blobs in Azure Storage. De DotNetTutorial-toepassing maakt gebruik van Shared Access Signature-URL's voor blobs en containers en toont aan hoe u deze SAS-tekenreeksen van de Storage-service kunt ophalen.

- **Shared Access Signatures voor blobs**: StartTask van de pool in DotNetTutorial maakt gebruik van Shared Access Signatures voor blobs wanneer het de binaire bestanden van toepassingen en invoergegevensbestanden uit Storage downloadt (zie stap 3 hieronder). De methode `UploadFileToContainerAsync` in het bestand `Program.cs` van DotNetTutorial bevat de code die de Shared Access Signature van elke blob verkrijgt. Dit gebeurt door het aanroepen van [CloudBlob.GetSharedAccessSignature][net_sas_blob].

- **Shared Access Signatures voor containers**: Nadat elke taak in het rekenknooppunt zijn werk heeft verricht, uploadt elke taak zijn uitvoerbestand naar de *uitvoer*container in Azure Storage. Hiertoe maakt TaskApplication gebruik van een Shared Access Signature voor containers die schrijftoegang biedt tot de container als deel van het pad wanneer TaskApplication het bestand uploadt. De Shared Access Signature voor containers wordt op een vergelijkbare manier verkregen als de Shared Access Signature voor blobs. In DotNetTutorial zult u zien dat de Help-methode `GetContainerSasUrl` hiervoor [CloudBlobContainer.GetSharedAccessSignature][net_sas_container] aanroept om dit te doen. Meer informatie over hoe TaskApplication de Shared Access Signature voor containers gebruikt, vindt u in 'Stap 6: taken controleren'.

> [AZURE.TIP] Bekijk de tweedelige reeks over Shared Access Signatures [Part 1: Understanding the shared access signature (SAS) model](../storage/storage-dotnet-shared-access-signature-part-1.md) (Deel 1: inzicht in het Shared Access Signature (SAS)-model) en [Part 2: Create and use a shared access signature (SAS) with the Blob service](../storage/storage-dotnet-shared-access-signature-part-2.md) (Deel 2: Een Shared Access Signature (SAS) maken en gebruiken met de Blob-service), voor meer informatie over het verstrekken van beveiligde toegang tot gegevens in uw opslagaccount.

## Stap 3: Batch-pool maken

![Batch-pool maken][3]
<br/>

Nadat *DotNetTutorial* de toepassing en gegevensbestanden naar het opslagaccount heeft geüpload, start het zijn interactie met de Batch-service met behulp van de Batch .NET-bibliotheek. Hiervoor wordt eerst een [BatchClient][net_batchclient] gemaakt:

```
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Daarna wordt een pool van rekenknooppunten gemaakt in het Batch-account met een aanroep naar `CreatePoolAsync`. `CreatePoolAsync` maakt gebruik van de methode [BatchClient.PoolOperations.CreatePool][net_pool_create] om daadwerkelijk een pool te maken in de Batch-service.

```
private static async Task CreatePoolAsync(
    BatchClient batchClient,
    string poolId,
    IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(),
    // no pool is actually created in the Batch service. This CloudPool instance is
    // therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicated: 3,           // 3 compute nodes
            virtualMachineSize: "small",  // single-core, 1.75 GB memory, 224 GB disk
            cloudServiceConfiguration:
                new CloudServiceConfiguration(osFamily: "4")); // Win Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join
    // the pool. In this case, we copy the StartTask's resource files (that will be
    // automatically downloaded to the node by the StartTask) into the shared
    // directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application
        // files to the node's shared directory. Every compute node in a Batch pool
        // is configured with several pre-defined environment variables that you can
        // reference by using commands or applications run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code
        // (e.g. 1 when one or more files were successfully copied) we need to
        // manually exit with a 0 for Batch to recognize StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

Wanneer u een pool maakt met [CreatePool][net_pool_create], geeft u een aantal parameters op, zoals het aantal rekenknooppunten, de [grootte van de knooppunten](../cloud-services/cloud-services-sizes-specs.md) en het besturingssysteem van de knooppunten. In *DotNetTutorial* gebruiken we [CloudServiceConfiguration][net_cloudserviceconfiguration] om Windows Server 2012 R2 op te geven vanuit [Cloud Services](../cloud-services/cloud-services-guestos-update-matrix.md). Door echter een [VirtualMachineConfiguration][net_virtualmachineconfiguration] op te geven, kunt u pools van knooppunten maken die zijn gemaakt op basis van installatiekopieën van Marketplace, die zowel Windows- als Linux-installatiekopieën bevatten. Zie [Provision Linux compute nodes in Azure Batch pools](batch-linux-nodes.md) (Linux-rekenknooppunten in Azure Batch-pools inrichten) voor meer informatie.

> [AZURE.IMPORTANT] De rekenresources in Batch worden in rekening gebracht. Om uw kosten te beperken, kunt u `targetDedicated` verlagen naar 1 voordat u het voorbeeld uitvoert.

Samen met deze fysieke knooppunteigenschappen kunt u ook [StartTask][net_pool_starttask] voor de pool opgeven. StartTask wordt in elk knooppunt uitgevoerd wanneer dat knooppunt aan de pool wordt toegevoegd en telkens wanneer dat knooppunt opnieuw wordt opgestart. StartTask is met name nuttig voor het installeren van toepassingen in rekenknooppunten voordat taken worden uitgevoerd. Als bijvoorbeeld uw taken gegevens verwerken met behulp van Python-scripts, kunt u StartTask gebruiken om Python te installeren in de rekenknooppunten.

In deze voorbeeldtoepassing kopieert StartTask de bestanden die het van Storage downloadt (die zijn opgegeven met behulp van de eigenschap [StartTask][net_starttask].[ResourceFiles][net_starttask_resourcefiles]) vanuit de StartTask-werkmap naar de gedeelde map waartoe *alle* taken die in het knooppunt worden uitgevoerd toegang hebben. In wezen worden hierdoor `TaskApplication.exe` en al de afhankelijkheden ervan gekopieerd naar de gedeelde map in elk knooppunt wanneer het knooppunt aan de pool wordt toegevoegd, zodat alle taken die in het knooppunt worden uitgevoerd er toegang toe hebben.

> [AZURE.TIP] De functie voor **toepassingspakketten** van Azure Batch biedt een andere manier om uw toepassing in rekenknooppunten in een pool te krijgen. Zie [Application deployment with Azure Batch application packages](batch-application-packages.md) (Toepassingsimplementatie met Azure Batch-toepassingspakketten) voor meer informatie.

Opmerkelijk in het bovenstaande codefragment is ook het gebruik van twee omgevingsvariabelen in de eigenschap *CommandLine* van StartTask: `%AZ_BATCH_TASK_WORKING_DIR%` en `%AZ_BATCH_NODE_SHARED_DIR%`. Elk rekenknooppunt in een Batch-pool wordt automatisch geconfigureerd met een aantal omgevingsvariabelen die specifiek zijn voor Batch. Elke proces dat door een taak wordt uitgevoerd, heeft toegang tot deze omgevingsvariabelen.

> [AZURE.TIP] Zie de secties 'Omgevingsinstellingen voor taken' en 'Bestanden en mappen' in [Overzicht van Azure Batch-functies](batch-api-basics.md) voor meer informatie over de omgevingsvariabelen die beschikbaar zijn in rekenknooppunten in een Batch-pool en voor meer informatie over taakwerkmappen.

## Stap 4: Batch-job maken

![Een Batch-job maken][4]<br/>

Een Batch-job is in feite een verzameling taken die zijn gekoppeld aan een pool van rekenknooppunten. U kunt een Batch-job niet alleen gebruiken om taken in gerelateerde workloads te organiseren en te volgen, maar ook om bepaalde beperkingen op te leggen, zoals de maximale runtime voor de job (en, bij uitbreiding, dus ook voor de taken ervan) en de prioriteit van de job in verhouding tot andere jobs in het Batch-account. In dit voorbeeld wordt de job echter alleen gekoppeld aan de pool die in stap 3 is gemaakt. Er worden geen aanvullende eigenschappen geconfigureerd.

Alle Batch-jobs worden gekoppeld aan een specifieke pool. Deze koppeling geeft aan op welke knooppunten de taken van de job worden uitgevoerd.  U geeft dit op met behulp van de eigenschap [CloudJob.PoolInformation][net_job_poolinfo], zoals in het onderstaande codefragment wordt getoond.

```
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Nu een job is gemaakt, worden er taken aan toegevoegd die het werk verrichten.

## Stap 5: taken toevoegen aan job

![Taken toevoegen aan job][5]<br/>
*(1) Taken worden toegevoegd aan de job, (2) de taken worden gepland om op knooppunten te worden uitgevoerd en (3) de taken downloaden de te verwerken gegevensbestanden*

Om daadwerkelijk werk te verrichten, moeten de taken aan een job worden toegevoegd. Elke [CloudTask][net_task] wordt geconfigureerd met behulp van een opdrachtregeleigenschap en [ResourceFiles][net_task_resourcefiles] (net als bij StartTask van de pool) die de taak downloadt naar het knooppunt voordat de opdrachtregel ervan automatisch wordt uitgevoerd. In het *DotNetTutorial*-voorbeeldproject verwerkt elke taak slechts één bestand. Hierdoor bevat de verzameling ResourceFiles één element.

```
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT] Wanneer ze toegang krijgen tot omgevingsvariabelen zoals `%AZ_BATCH_NODE_SHARED_DIR%` of een toepassing uitvoeren die niet op het `PATH` van het knooppunt wordt gevonden, moeten taakopdrachtregels het voorvoegsel `cmd /c` krijgen. Hiermee wordt de opdrachtinterpreter expliciet uitgevoerd en krijgt deze de instructie om te beëindigen nadat uw opdracht is uitgevoerd. Deze vereiste is niet nodig als uw taken een toepassing uitvoeren op het `PATH` van het knooppunt (zoals *robocopy.exe* of *powershell.exe*) en er geen omgevingsvariabelen worden gebruikt.

In de `foreach`-lus in het bovenstaande codefragment kunt u zien dat de opdrachtregel voor de taak zodanig is opgesteld dat drie opdrachtregelargumenten worden doorgegeven aan *TaskApplication.exe*:

1. Het **eerste argument** is het pad van het te verwerken bestand. Dit is het lokale pad naar het bestand, zoals zich dit op het knooppunt bevindt. Toen het ResourceFile-object `UploadFileToContainerAsync` voor het eerst werd gemaakt, werd de bestandsnaam voor deze eigenschap gebruikt (als een parameter voor de constructor ResourceFile). Dit geeft aan dat het bestand kan worden gevonden in dezelfde map als *TaskApplication.exe*.

2. Het **tweede argument** geeft aan dat de eerste *N* woorden naar het uitvoerbestand moeten worden geschreven. In het voorbeeld is dit in code vastgelegd, zodat de eerste drie woorden naar het uitvoerbestand worden geschreven.

3. Het **derde argument** is de Shared Access Signature (SAS) die schrijftoegang tot de **uitvoer**container in Azure Storage biedt. *TaskApplication.exe* maakt gebruik van deze Shared Access Signature-URL wanneer het uitvoerbestand naar Azure Storage uploadt. U vindt de code hiervoor in de methode `UploadFileToContainer` in het bestand `Program.cs` van het TaskApplication-project:

```
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath, FileMode.Open);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## Stap 6: taken controleren

![Taken controleren][6]<br/>
*De clienttoepassing (1) controleert taken op de status Voltooid en Geslaagd en (2) de taken uploaden resultaatgegevens naar Azure Storage.*

Wanneer aan een job taken worden toegevoegd, worden deze automatisch in de wachtrij geplaatst en gepland voor uitvoering op rekenknooppunten binnen de pool die aan de job is gekoppeld. Op basis van de instellingen die u opgeeft, zal Batch voor u alle taken in de wachtrij plaatsen, plannen en opnieuw proberen uit te voeren, evenals andere taakbeheerverrichtingen uitvoeren. Er zijn veel manieren om de uitvoering van taken te controleren. DotNetTutorial toont een eenvoudig voorbeeld waarbij alleen wordt gerapporteerd bij voltooiing en wanneer de taak mislukt of slaagt.

Binnen de methode `MonitorTasks` in het DotNetTutorial-bestand `Program.cs` zijn er drie Batch .NET-concepten die discussies garanderen. Ze worden hieronder weergegeven in de volgorde waarin ze voorkomen:

1. **ODATADetailLevel**: [ODATADetailLevel][net_odatadetaillevel] opgeven in lijstbewerkingen (zoals het verkrijgen van een lijst met taken van een job) is essentieel om Batch-toepassingen goed te laten presteren. Voeg het artikel [Query the Azure Batch service efficiently](batch-efficient-list-queries.md) (Efficiënt query's uitvoeren op de Azure Batch-service) toe aan uw leeslijst als u van plan bent een of andere vorm van statuscontrole uit te voeren in uw Batch-toepassingen.

2. **TaskStateMonitor**: [TaskStateMonitor][net_taskstatemonitor] biedt Batch .NET-toepassingen met Help-hulpprogramma's voor het controleren van taakstatuswaarden. In `MonitorTasks` wacht *DotNetTutorial* tot alle taken binnen een bepaalde tijd [TaskState.Completed][net_taskstate] hebben bereikt. Daarna wordt de job beëindigd.

3. **TerminateJobAsync**: Door een job te beëindigen met [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (of de blokkering JobOperations.TerminateJob), wordt deze job gemarkeerd als voltooid. Het is essentieel om dit te doen als uw Batch-oplossing gebruikmaakt van een [JobReleaseTask][net_jobreltask]. Dit is een speciaal type taak, dat wordt beschreven in [Job preparation and completion tasks](batch-job-prep-release.md) (Jobvoorbereidings- en jobvoltooiingstaken).

De methode `MonitorTasks` uit het *DotNetTutorial*-bestand `Program.cs` wordt hieronder weergegeven:

```
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...", timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
    bool timedOut = await taskStateMonitor.WaitAllAsync(
        tasks,
        TaskState.Completed,
        timeout);

    if (timedOut)
    {
        allTasksSuccessful = false;

        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);

        Console.WriteLine(failureMessage);
    }
    else
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

        // All tasks have reached the "Completed" state. However, this does not
        // guarantee that all tasks were completed successfully. Here we further
        // check each task's ExecutionInfo property to ensure that it did not
        // encounter a scheduling error or return a non-zero exit code.

        // Update the detail level to populate only the task id and executionInfo
        // properties. We refresh the tasks below, and need only this information
        // for each task.
        detail.SelectClause = "id, executionInfo";

        foreach (CloudTask task in tasks)
        {
            // Populate the task's properties with the latest info from the Batch service
            await task.RefreshAsync(detail);

            if (task.ExecutionInformation.SchedulingError != null)
            {
                // A scheduling error indicates a problem starting the task on the
                // node. It is important to note that the task's state can be
                // "Completed," yet the task still might have encountered a
                // scheduling error.

                allTasksSuccessful = false;

                Console.WriteLine(
                    "WARNING: Task [{0}] encountered a scheduling error: {1}",
                    task.Id,
                    task.ExecutionInformation.SchedulingError.Message);
            }
            else if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by
                // the task encountered an error during execution. As not every
                // application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                allTasksSuccessful = false;

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## Stap 7: taakuitvoer downloaden

![Taakuitvoer downloaden uit Storage][7]<br/>

Nu de taak is voltooid, kan de uitvoer van de taken worden gedownload uit Azure Storage. Dat gebeurt met een aanroep van `DownloadBlobsFromContainerAsync` in het *DotNetTutorial*-bestand `Program.cs`:

```
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE] De aanroep van `DownloadBlobsFromContainerAsync` in de *DotNetTutorial*-toepassing geeft aan dat de bestanden naar uw map `%TEMP%` moeten worden gedownload. U kunt eventueel deze uitvoerlocatie wijzigen.

## Stap 8: containers verwijderen

Omdat gegevens die zich in Azure Storage bevinden, in rekening worden gebracht, doet u er altijd goed aan alle blobs te verwijderen die niet langer nodig zijn voor uw Batch-taken. In het DotNetTutorial-bestand `Program.cs` wordt dit gedaan met drie aanroepen naar de Help-methode `DeleteContainerAsync`:

```
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

De methode zelf krijgt alleen een verwijzing naar de container en roept daarna [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete] aan:

```
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## Stap 9: de job en de pool verwijderen

In de laatste stap wordt de gebruiker gevraagd de job en de pool te verwijderen die door de DotNetTutorial-toepassing zijn gemaakt. Hoewel jobs en taken zelf niet in rekening worden gebracht, worden rekenknooppunten *wel* in rekening gebracht. Daarom is het raadzaam om knooppunten alleen toe te wijzen als dat nodig is. Het verwijderen van ongebruikte pools kan een onderdeel zijn van uw onderhoudsprocedure.

[JobOperations][net_joboperations] en [PoolOperations][net_pooloperations] van BatchClient hebben beide overeenkomstige verwijderingsmethoden, die worden aangeroepen wanneer de gebruiker de verwijdering bevestigt:

```
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT] Houd er rekening mee dat rekenresources in rekening worden gebracht. Door ongebruikte pools te verwijderen, beperkt u uw kosten tot een minimum. Denk er ook aan dat een pool alle rekenknooppunten in die pool verwijdert en dat alle gegevens in de knooppunten onherstelbaar zijn nadat de pool wordt verwijderd.

## Het *DotNetTutorial*-voorbeeld uitvoeren

Wanneer u de voorbeeldtoepassing uitvoert, lijkt de uitvoer van de console op die hieronder. Bij het uitvoeren wordt bij `Awaiting task completion, timeout in 00:30:00...` gewacht wanneer de rekenknooppunten van de pool worden gestart. Gebruik [Batch Explorer][github_batchexplorer] om uw pool, rekenknooppunten, jobs en taken tijdens en na de uitvoering te controleren. Gebruik [Azure Portal][azure_portal] of een van de [beschikbare Azure Storage Explorers][storage_explorers] om de Storage-resources (containers en blobs) weer te geven die met de toepassing zijn gemaakt.

Wanneer u de toepassing uitvoert in de standaardconfiguratie, bedraagt de uitvoeringstijd doorgaans **ongeveer 5 minuten**.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## Volgende stappen

Breng gerust wijzigingen aan in *DotNetTutorial* en *TaskApplication* om met verschillende rekenscenario's te experimenteren. Probeer bijvoorbeeld een uitvoeringsvertraging toe te voegen aan *TaskApplication*, bijvoorbeeld met [Thread.Sleep][net_thread_sleep], om langlopende taken te simuleren en ze te controleren met de Batch Explorer-functie *Heat Map*. Probeer meer taken toe te voegen of het aantal rekenknooppunten aan te passen. Voeg logica toe om te controleren op een bestaande pool en het gebruik ervan toe te staan om de uitvoeringssnelheid te verhogen (*hint*: bekijk het bestand `ArticleHelpers.cs` in het project [Microsoft.Azure.Batch.Samples.Common][github_samples_common] in [azure-batch-samples][github_samples]).

Nu u vertrouwd bent met de basiswerkstroom van een Batch-oplossing, is het tijd om kennis te maken met de aanvullende functies van de Batch-service.

- Lees het artikel [Overzicht van Azure Batch-functies](batch-api-basics.md). Dit is raadzaam als u niet vertrouwd bent met de service.
- Lees ook de andere artikelen over Batch-ontwikkeling die vermeld zijn onder **Development in-depth** (Ontwikkeling nader bekeken) in het [Batch-leertraject][batch_learning_path].
- Bekijk een andere implementatie van de verwerking van de workload 'eerste N woorden' met behulp van Batch in het voorbeeld [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_explorer_blog]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Containers maken in Azure Storage"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Taaktoepassings-en invoer(gegevens)bestanden uploaden naar containers"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Batch-pool maken"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Batch-job maken"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Taken toevoegen aan job"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Taken controleren"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Taakuitvoer downloaden uit Storage"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Werkstroom van de Batch-oplossing (volledige diagram)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Batch-referenties in Portal"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Storage-referenties in Portal"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Werkstroom van de Batch-oplossing (minimaal diagram)"



<!--HONumber=Jun16_HO2-->


