<properties
    pageTitle="Query uitvoeren voor gegevens uit HDFS-compatibele Blob Storage | Microsoft Azure"
    description="HDInsight gebruikt Azure Blob Storage als het big data-archief voor HDFS. Informatie over het opvragen van gegevens uit Blob Storage en het opslaan van resultaten van de analyse."
    keywords="blob storage,hdfs,structured data,unstructured data"
    services="hdinsight,storage"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/18/2016"
    ms.author="jgao"/>


# HDFS-compatibele Azure Blob Storage met Hadoop in HDInsight gebruiken

Informatie over het gebruik van een voordelige Azure Blob Storage met HDInsight, het maken van een Azure Storage-account en de Blob Storage-container om vervolgens de interne gegevens te adresseren.

Azure Blob Storage is een robuuste, algemene opslagoplossing die naadloos kan worden geïntegreerd met HDInsight. Via een HDFS-interface (Hadoop Distributed File System) kan de volledige set onderdelen in HDInsight rechtstreeks op gestructureerde of ongestructureerde gegevens in Blob Storage worden uitgevoerd.

Door gegevens op te slaan in Blob Storage kunt u de HDInsight-clusters die worden gebruikt voor berekeningen, veilig verwijderen zonder dat er gebruikersgegevens verloren gaan.

> [AZURE.NOTE]  De syntaxis *asv://* wordt niet ondersteund door clusters van HDInsight versie 3.0. Dit betekent dat alle taken die worden verzonden naar een HDInsight versie 3.0-cluster en expliciet gebruikmaken van de syntaxis *asv://*, zullen mislukken. In plaats daarvan moet de syntaxis *wasb://* worden gebruikt. Ook de taken die worden verzonden naar HDInsight versie 3.0-clusters die zijn gemaakt met een bestaande metastore en expliciete verwijzingen naar resources bevatten die de syntaxis asv:// gebruiken, zullen mislukken. Deze metastores moeten opnieuw worden gemaakt met de syntaxis wasb:// om resources te adresseren.

> HDInsight ondersteunt momenteel alleen blok-blobs.

> De meeste HDFS-opdrachten (bijvoorbeeld <b>ls</b>, <b>copyFromLocal</b> en <b>mkdir</b>) blijven gewoon naar verwachting werken. Alleen de opdrachten die specifiek voor de systeemeigen HDFS-implementatie zijn (ook wel DFS genoemd), zoals <b>fschk</b> en <b>dfsadmin</b>, vertonen afwijkend gedrag in Azure Blob Storage.

Zie [Aan de slag met HDInsight][hdinsight-get-started] of [HDInsight-clusters maken][hdinsight-creation] voor informatie over het maken van HDInsight-clusters.


## HDInsight-opslagarchitectuur
Het volgende diagram biedt een abstracte weergave van de HDInsight-opslagarchitectuur:

![Hadoop-clusters gebruiken de HDFS API voor de toegang tot en opslag van gestructureerde en ongestructureerde gegevens in Blob Storage.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight Storage Architecture")

HDInsight biedt toegang tot het Distributed File System dat lokaal wordt gekoppeld aan de rekenknooppunten. Dit bestandssysteem is toegankelijk via de volledig gekwalificeerde URI, bijvoorbeeld:

    hdfs://<namenodehost>/<path>

Daarnaast biedt HDInsight toegang tot gegevens die zijn opgeslagen in Azure Blob Storage. De syntaxis is:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


Hadoop ondersteunt een notatie van het standaardbestandssysteem. Het standaardbestandssysteem impliceert een standaardschema en instantie. De toepassing kan ook worden gebruikt om relatieve paden om te zetten. Tijdens het HDInsight-creatieproces zal er een Azure Storage-account en een specifieke Azure Blob Storage-container uit dat account worden aangewezen als het standaardbestandssysteem.

Naast dit opslagaccount kunt u tijdens het maakproces extra opslagaccounts toevoegen uit hetzelfde Azure-abonnement of uit andere Azure-abonnementen. Zie [HDInsight-clusters maken][hdinsight-creation] voor instructies over het toevoegen van extra opslagaccounts.

- **Containers in de opslagaccounts die zijn verbonden met een cluster:** omdat de accountnaam en de sleutel tijdens het maken worden gekoppeld aan het cluster, hebt u volledige toegang tot de blobs in deze containers.

- **Openbare containers of openbare blobs in opslagaccounts die NIET zijn verbonden met een cluster:** u hebt een alleen-lezen-machtiging voor de blobs in de containers.

    > [AZURE.NOTE]
        > Met openbare containers kunt u een lijst met alle beschikbare blobs in de desbetreffende container en containermetagegevens ophalen. Openbare blobs zijn alleen toegankelijk als u de exacte URL weet. Zie <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">Restrict access to containers and blobs</a> (De toegang tot containers en blobs beperken) voor meer informatie.

- **Persoonlijke containers in opslagaccounts die NIET zijn verbonden met een cluster:** u hebt geen toegang tot de blobs in de containers, tenzij u het opslagaccount definieert wanneer u de WebHCat-taken verzendt. Dit wordt verderop in dit artikel uitgelegd.


De opslagaccounts die worden gedefinieerd tijdens het creatieproces en de bijbehorende sleutels worden opgeslagen in %HADOOP_HOME%/conf/core-site.xml op de clusterknooppunten. HDInsight gebruikt standaard de opslagaccounts die zijn gedefinieerd in het bestand core-site.xml. Het is niet raadzaam om het bestand core site.xml te bewerken, omdat het hoofdknooppunt van het cluster (master) op elk moment kan worden teruggezet of gemigreerd en de wijzigingen die in deze bestanden zijn aangebracht, verloren gaan.

Meerdere WebHCat-taken, waaronder Hive, MapReduce, Hadoop-streaming en Pig, kunnen een beschrijving van opslagaccounts en metagegevens bevatten. (Dit werkt momenteel voor Pig met opslagaccounts, maar niet voor metagegevens.) In de sectie [Toegang tot blobs via Azure PowerShell](#powershell) van dit artikel vindt u een voorbeeld van deze functie. Zie [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (Een HDInsight-cluster gebruiken met alternatieve opslagaccounts en metastores) voor meer informatie.

Blob Storage kan worden gebruikt voor gestructureerde en ongestructureerde gegevens. De gegevens in Blob Storage-containers worden opgeslagen als sleutel-waardeparen en er is geen maphiërarchie. Maar het slash-teken (/) kan echter worden gebruikt binnen de sleutelnaam deze weergegeven, zodat het lijkt alsof een bestand is opgeslagen in een mapstructuur. De sleutel van de blob kan bijvoorbeeld *input/log1.txt* zijn. Er is niet echt een *invoermap* aanwezig, maar als gevolg van de aanwezigheid van het slash-teken in de naam van de sleutel ziet dit eruit als een bestandspad.

###<a id="benefits"></a>Voordelen van Blob Storage
De kosten als gevolg van de verschillende locaties voor de rekenclusters en opslagbronnen worden beperkt door de manier waarop de rekenclusters dicht bij de resources van het opslagaccount in de Azure-regio worden geplaatst. Dankzij het netwerk met hoge snelheid kunnen de rekenknooppunten zich op efficiënte wijze toegang verschaffen tot de gegevens in Azure Blob Storage.

Het opslaan van gegevens in Azure Blob Storage in plaats van HDFS heeft enkele voordelen:

* **Hergebruik en delen van gegevens:** de gegevens in HDFS bevinden zich in het rekencluster. Alleen de toepassingen die toegang tot het rekencluster hebben, kunnen de gegevens met HDFS API's gebruiken. De gegevens in Azure Blob Storage zijn toegankelijk via de HDFS API's of via de [Blob Storage REST-API's][blob-storage-restAPI]. Zo kan een grotere set toepassingen (inclusief andere HDInsight-clusters) en hulpprogramma's worden gebruikt om de gegevens te produceren en te gebruiken.
* **Gegevensarchivering:** door gegevens op te slaan in Azure Blob Storage kunnen de HDInsight-clusters die worden gebruikt voor berekeningen, veilig worden verwijderd zonder dat er gegevens verloren gaan.
* **Kosten voor gegevensopslag:** het langdurig opslaan van gegevens in DFS is duurder dan wanneer de gegevens worden opgeslagen in Azure Blob Storage, omdat de kosten van een rekencluster hoger zijn dan de kosten van de Azure Blob Storage-container. Daarnaast bespaart u op de kosten voor het laden van gegevens omdat de gegevens niet opnieuw hoeven te worden geladen voor elk rekencluster dat wordt gegenereerd.
* **Elastisch uitbreiden:** hoewel HDFS u een uitgebreid bestandssysteem biedt, wordt de schaal bepaald door het aantal knooppunten dat u voor het cluster maakt. Het wijzigen van de schaal is mogelijk een complexer proces dan te vertrouwen op de elastische schalingsmogelijkheden waarover u automatisch beschikt in Azure Blob Storage.
* **Geo-replicatie:** uw Azure Blob Storage-containers kunnen geografisch worden gerepliceerd. Hoewel u dit de mogelijkheid van geografisch herstel en gegevensredundantie biedt, zal een failover naar de geografisch gerepliceerde locatie van grote invloed zijn op uw prestaties en kan dit tot extra kosten leiden. U wordt daarom aangeraden de keuze van geo-replicatie goed te overwegen en alleen te gebruiken als de waarde van de gegevens de extra kosten waard zijn.

Bepaalde MapReduce-taken en -pakketten kunnen tussenliggende resultaten genereren die u niet wilt opslaan in Azure Blob Storage. In dat geval kunt u ervoor kiezen de gegevens op te slaan in de lokale HDFS. HDInsight gebruikt DFS voor verschillende tussenliggende resultaten in Hive-taken en andere processen.



## Blob-containers maken

Als u blobs wilt gebruiken, maakt u eerst een [Azure Storage-account][azure-storage-create]. Als onderdeel hiervan geeft u een Azure-regio op waarin de objecten worden opgeslagen die u met dit account maakt. Het cluster en het opslagaccount moeten worden gehost in dezelfde regio. De SQL Server-database van de Hive-metastore en Oozie-metastore moeten zich ook in dezelfde regio bevinden.

Elke blob die u maakt, behoort tot een container in uw Azure Storage-account, ongeacht de locatie van de blob. Deze container kan een bestaande blob zijn die buiten HDInsight is gemaakt. Het kan echter ook een container zijn die is gemaakt voor een HDInsight-cluster.


In de standaard blob-container wordt clusterspecifieke informatie opgeslagen, zoals de taakgeschiedenis en logboekbestanden. Deel een standaard blob-container niet met meerdere HDInsight-clusters. Hierdoor raakt de taakgeschiedenis mogelijk beschadigd en vertoont het cluster ongewenst gedrag. U kunt voor elk cluster het beste een andere container gebruiken en de gedeelde gegevens in een gekoppeld opslagaccount plaatsen dat is opgegeven in de implementatie van alle relevante cluster, in plaats van het standaardopslagaccount. Zie [HDInsight-clusters maken][hdinsight-creation] voor meer informatie over het configureren van gekoppelde opslagaccounts. U kunt een standaardopslagcontainer echter opnieuw gebruiken nadat het oorspronkelijke HDInsight-cluster is verwijderd. Voor HBase-clusters kunt u het HBase-tabelschema en de bijbehorende gegevens behouden door een nieuw HBase-cluster te maken met de standaard Blob Storage-container die wordt door een verwijderd HBase-cluster.


### Azure Portal gebruiken

Wanneer u een HDInsight-cluster maakt vanuit de portal, kunt u een bestaand opslagaccount gebruiken of een nieuw opslagaccount maken:

![gegevensbron voor het maken van hdinsight hadoop](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

###Azure CLI gebruiken

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

Als u [de Azure CLI hebt geïnstalleerd en geconfigureerd](../xplat-cli-install.md), kan de volgende opdracht worden gebruikt om een opslagaccount en container te maken.

    azure storage account create <storageaccountname> --type LRS

> [AZURE.NOTE] De parameter `--type` geeft aan hoe de opslagaccount wordt gerepliceerd. Zie [Azure Storage-replicatie](../storage/storage-redundancy.md) voor meer informatie. U kunt ZRS beter niet gebruiken, aangezien ZRS de pagina-blob, het bestand, de tabel of de wachtrij niet ondersteunt.

U wordt gevraagd de geografische regio op te geven waar het opslagaccount zich bevindt. U moet de opslagaccount in dezelfde regio maken waarin u ook uw HDInsight-cluster wilt maken.

Zodra het opslagaccount is gemaakt, gebruikt u de volgende opdracht om de sleutels voor de opslagaccount op te halen:

    azure storage account keys list <storageaccountname>

Gebruik de volgende opdracht om een container te maken:

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

### Azure PowerShell gebruiken

Als u [Azure PowerShell hebt geïnstalleerd en geconfigureerd][powershell-install], kunt u het volgende achter de Azure PowerShell-prompt typen om een opslagaccount en container te maken:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    
    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID
    
    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location
    
    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 
    
    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

## Bestanden in Blob Storage adresseren

Het URI-schema om bestanden in Blob Storage vanuit HDInsight te openen:

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE] De syntaxis voor het adresseren van de bestanden op de opslagemulator (die wordt uitgevoerd op de HDInsight-emulator) is <i>wasb://&lt;ContainerName&gt;@storageemulator</i>.



Het URI-schema biedt niet-versleutelde toegang (met het voorvoegsel *wasb:*) en SSL-versleutelde toegang (met *wasbs*). Waar mogelijk kunt u het beste *wasbs* gebruiken, zelfs voor de toegang tot gegevens die zich in dezelfde regio in Azure bevinden.

Met &lt;BlobStorageContainerName&gt; wordt de naam van de container in Azure Blob Storage aangeduid.
Met &lt;StorageAccountName&gt; wordt de naam van het Azure Storage-account aangeduid. Een FQDN (Fully Qualified Domain Name) is vereist.

Als &lt;BlobStorageContainerName&gt; of &lt;StorageAccountName&gt; niet zijn opgegeven, wordt het standaardbestandssysteem gebruikt. Voor de bestand op het standaardbestandssysteem kunt u een relatief of een absoluut pad gebruiken. U kunt bijvoorbeeld als volgt verwijzen naar het bestand *hadoop-mapreduce-examples.jar* dat bij HDInsight-clusters wordt geleverd:

    wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] De bestandsnaam is <i>hadoop examples.jar</i> in HDInsight versie 2.1- en 1.6-clusters.


Het &lt;pad&gt; is de HDFS-padnaam van het bestand of de map. Aangezien containers in Azure Blob Storage gewoon sleutel-waardearchieven zijn, is er geen echt hiërarchisch bestandssysteem. Een slash (/) in een blob-sleutel wordt geïnterpreteerd als een teken voor mapscheiding. De blob-naam voor bijvoorbeeld *hadoop-mapreduce-examples.jar* is:

    example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] Als u buiten HDInsight met blobs werkt, zullen de meeste hulpprogramma's de indeling WASB niet herkennen en wordt er in plaats daarvan een standaardpadindeling verwacht, zoals `example/jars/hadoop-mapreduce-examples.jar`.

## Toegang tot blobs met Azure CLI

Gebruik de volgende opdracht voor een lijst met blob-gerelateerde opdrachten:

    azure storage blob

**Voorbeeld van het gebruik van Azure CLI om een bestand te uploaden**

    azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Voorbeeld van het gebruik van Azure CLI om een bestand te downloaden**

    azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**Voorbeeld van het gebruik van Azure CLI om een bestand te verwijderen**

    azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Voorbeeld van het gebruik van Azure CLI om een lijst met bestanden weer te geven**

    azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

## Toegang tot blobs met Azure PowerShell

> [AZURE.NOTE] De opdrachten in deze sectie bieden een eenvoudige voorbeeld van het gebruik van PowerShell voor toegang tot gegevens die zijn opgeslagen in blobs. Zie de [HDInsight Tools](https://github.com/Blackmist/hdinsight-tools) voor een uitgebreider voorbeeld dat is aangepast voor het werken met HDInsight.

Gebruik de volgende opdracht voor een lijst met blob-gerelateerde cmdlets:

    Get-Command *blob*

![Lijst met blob-gerelateerde PowerShell-cmdlets.][img-hdi-powershell-blobcommands]

###Bestanden uploaden

Zie [Gegevens uploaden naar HDInsight][hdinsight-upload-data].

###Bestanden downloaden

Met het volgende script wordt een blok-blob gedownload naar de huidige map. Voordat u het script uitvoert, wijzigt u de directory in een map waarvoor u over schrijfmachtiging beschikt.

    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.
    
    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    Login-AzureRmAccount 
    Select-AzureRmSubscription -SubscriptionID "<Your Azure Subscription ID>"
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

U kunt de volgende code gebruiken om de naam van de resourcegroep en de clusternaam op te geven:

    $resourceGroupName = "<AzureResourceGroupName>"
    $clusterName = "<HDInsightClusterName>"
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.
    
    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
    
    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force

###Bestanden verwijderen


    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob

###Bestanden in een lijst weergeven

    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"

###Hive-query's uitvoeren met een niet-gedefinieerd opslagaccount

In dit voorbeeld ziet u hoe u een map uit het opslagaccount weergeeft die niet is gedefinieerd tijdens het creatieproces.
$clusterName = <HDInsightClusterName>

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureRmHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## Volgende stappen

In dit artikel hebt u geleerd hoe u een HDFS-compatibele Azure Blob Storage gebruikt met HDInsight en hebt u geleerd dat Azure Blob Storage een fundamenteel onderdeel van HDInsight is. Zodoende kunt u een schaalbare, duurzame, archiveringsoplossing voor gegevensverzameling bouwen met Azure Blob Storage en HDInsight gebruiken om de informatie te ontsluiten in de opgeslagen gestructureerde en ongestructureerde gegevens.

Ga voor meer informatie naar:

* [Aan de slag met Azure HDInsight][hdinsight-get-started]
* Zie [Gegevens uploaden naar HDInsight][hdinsight-upload-data]
* [Hive gebruiken met HDInsight][hdinsight-use-hive]
* [Pig gebruiken met HDInsight][hdinsight-use-pig]
* [Handtekeningen voor gedeelde toegang van Azure Storage gebruiken om de toegang tot gegevens te beperken met HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: ../powershell-install-configure.md
[hdinsight-creation]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  



<!--HONumber=Jun16_HO2-->


