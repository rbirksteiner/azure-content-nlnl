<properties
    pageTitle="Een opslagaccount in de klassieke Azure Portal maken, beheren of verwijderen | Microsoft Azure"
    description="Maak een nieuw opslagaccount, beheer de toegangssleutels van uw account of verwijder een opslagaccount in de Azure Portal. Meer informatie over Standard en Premium Storage-accounts."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="robinsh"/>


# Over Azure-opslagaccounts

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

## Overzicht

Een Azure-opslagaccount biedt u toegang tot de Azure-services Blob, Queue, Table en File in Azure Storage. Uw opslagaccount biedt een unieke naamruimte voor uw Azure Storage-gegevensobjecten. De gegevens in uw account zijn standaard alleen beschikbaar voor u, de eigenaar van het account.

Er zijn twee typen opslagaccounts:

- Een Standard-opslagaccount omvat Blob Storage, Table Storage, Queue Storage en File Storage.
- Een Premium Storage-account biedt momenteel alleen ondersteuning voor schijven van virtuele Azure-machines. Zie [Premium Storage: krachtige opslag voor Azure Virtual Machine-werkbelasting](storage-premium-storage.md) voor een gedetailleerd overzicht van Premium Storage.

## Facturering voor opslagaccounts

U wordt gefactureerd voor het gebruik van Azure Storage op basis van uw opslagaccount. De opslagkosten worden gebaseerd op vier factoren: opslagcapaciteit, replicatieschema, opslagtransacties en uitgaande gegevens.

- De opslagcapaciteit verwijst naar welk aandeel van uw opslagaccount u gebruikt voor het opslaan van gegevens. De kosten van het opslaan van uw gegevens worden bepaald op basis van hoeveel gegevens u opslaat en hoe deze worden gerepliceerd.
- Replicatie staat voor hoeveel kopieën van uw gegevens gelijktijdig worden bewaard en op welke locaties.
- Transacties verwijst naar alle lees- en schrijfbewerkingen naar Azure Storage.
- Uitgaande gegevens verwijst naar alle gegevens die buiten een Azure-regio zijn overgedragen. Wanneer de gegevens in uw opslagaccount worden geopend door een toepassing die niet wordt uitgevoerd in dezelfde regio, of de toepassing nou een cloudservice is of een ander type toepassing, wordt er een bedrag in rekening gebracht voor uitgaande gegevens. (Bij Azure-services kunt u stappen ondernemen om uw gegevens en services te groeperen in dezelfde datacenters om de kosten voor uitgaande gegevens te verlagen of elimineren.)  

Op de pagina [Prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage) staat gedetailleerde informatie over de prijzen voor opslagcapaciteit, replicatie en transacties. Op de pagina [Prijsinformatie over gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/) staat gedetailleerde informatie over de prijzen voor uitgaande gegevens.

Zie [Schaalbaarheids- en prestatiedoelen in Azure Storage](storage-scalability-targets.md) voor meer informatie over opslagaccountcapaciteit en prestatiedoelen.

> [AZURE.NOTE] Wanneer u in Azure een virtuele machine maakt, wordt er automatisch een opslagaccount voor u gemaakt op de implementatielocatie als u op die locatie nog geen opslagaccount hebt. Het is dus niet nodig onderstaande stappen te volgen om een opslagaccount voor de schijven van uw virtuele machine te maken. De naam van het opslagaccount wordt gebaseerd op de naam van de virtuele machine. Zie [Documentatie bij Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) voor meer informatie.

## Een opslagaccount maken

1. Meld u aan bij de [klassieke Azure Portal](https://manage.windowsazure.com).

2. Klik op **Nieuw** in de taakbalk onder aan de pagina. Kies **Data Services** | **Opslag** en klik vervolgens op **Snelle invoer**.

    ![NewStorageAccount](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)

3. Voer in **URL** een naam in voor het opslagaccount.

    > [AZURE.NOTE] Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten.
    >  
    > De naam van uw opslagaccount moet uniek zijn binnen Azure. Het wordt in de klassieke Azure Portal aangegeven als de opslagaccountnaam die u selecteert al wordt gebruikt.

    Zie [Eindpunten van opslagaccounts](#storage-account-endpoints) hieronder voor meer informatie over hoe de naam van het opslagaccount wordt gebruikt om uw objecten in Azure Storage te adresseren.

4. In **Locatie/affiniteitsgroep** selecteert u een locatie voor uw opslagaccount die zich dicht bij u of uw klanten bevindt. Als er gegevens in uw opslagaccount worden geopend via een andere Azure-service, zoals een virtuele machine van Azure of een cloudservice, is het een goed idee om een affiniteitsgroep uit de lijst te selecteren om uw opslagaccount te groeperen in hetzelfde datacentrum als andere Azure-services die u gebruikt, om de prestaties te verbeteren en de kosten te verlagen.

    U moet een affiniteitsgroep selecteren bij het aanmaken van een opslagaccount. U kunt bestaande accounts niet verplaatsen naar een affiniteitsgroep. Voor meer informatie over affiniteitsgroepen ziet u [Serviceco-locatie met een affiniteitsgroep](#service-co-location-with-an-affinity-group) hieronder.

    >[AZURE.IMPORTANT] Om te bepalen welke locaties beschikbaar zijn voor uw abonnement, kunt u de bewerking [Alle resourceproviders vermelden](https://msdn.microsoft.com/library/azure/dn790524.aspx) aanroepen. als u alle providers via PowerShell wilt bekijken, roept u [Get-AzureLocation](https://msdn.microsoft.com/library/azure/dn757693.aspx) aan. Gebruik in .NET de methode [Lijst](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx) van de klasse ProviderOperationsExtensions.
    >
    >Zie ook [Azure-gebieden](https://azure.microsoft.com/regions/#services) voor meer informatie over welke services beschikbaar zijn in welke regio.


5. Als u meer dan één Azure-abonnement hebt, wordt het veld **Abonnement** weergegeven. Voer in **Abonnement** het Azure-abonnement in waarmee u het opslagaccount wilt gebruiken.

6. Selecteer in **Replicatie** het gewenste replicatieniveau voor uw opslagaccount. De aanbevolen replicatieoptie is geografisch redundante replicatie; deze optie biedt maximale duurzaamheid voor uw gegevens. Zie [Azure Storage-replicatie](storage-redundancy.md) voor meer informatie over Azure Storage-replicatieopties.

6. Klik op **Opslagaccount maken**.

    Het kan enkele minuten duren om uw opslagaccount te maken. Als u de status wilt controleren, bewaakt u de meldingen onder aan de klassieke Azure Portal. Wanneer het opslagaccount is gemaakt, krijgt uw nieuwe opslagaccount de status **Online** en is deze klaar voor gebruik.

![StoragePage](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)


### Eindpunten van opslagaccount

Elk object dat u in Azure Storage opslaat, heeft een uniek URL-adres. De naam van het opslagaccount vormt het subdomein van dat adres. De combinatie van subdomein- en domeinnaam, die specifiek zijn voor elke service, vormt een *eindpunt* voor uw opslagaccount.

Als de naam van uw opslagaccount bijvoorbeeld *mystorageaccount* is, zijn de standaardeindpunten voor uw opslagaccount:

- Blob-service: http://*mystorageaccount*.blob.core.windows.net

- Tabelservice: http://*mystorageaccount*.table.core.windows.net

- Queue-service: http://*mystorageaccount*.queue.core.windows.net

- File-service: http://*mystorageaccount*.file.core.windows.net

De eindpunten van uw opslagaccount staan op het opslagdashboard in de [klassieke Azure Portal](https://manage.windowsazure.com), zodra het account is gemaakt.

De URL voor het openen van een object in een opslagaccount wordt samengesteld door de locatie van het object in de opslagaccount toe te voegen aan het eindpunt. Een blobadres kan bijvoorbeeld de volgende indeling hebben: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

U kunt ook een aangepaste domeinnaam configureren voor gebruik met uw opslagaccount. Zie [Een aangepaste domeinnaam configureren voor het eindpunt voor Blob Storage](storage-custom-domain-name.md) voor meer informatie.

### Serviceco-locatie met een affiniteitsgroep

Een *affiniteitsgroep* is een geografische groepering van uw Azure-services en virtuele machines met uw Azure-opslagaccount. Met een affiniteitsgroep kunt u de serviceprestaties verbeteren door computerwerkbelastingen in hetzelfde datacentrum te plaatsen, of in de buurt van de gebruikersdoelgroep. Er worden bovendien geen kosten in rekening gebracht voor uitgaande gegevens wanneer gegevens in een opslagaccount worden geopend via een andere service die deel uitmaakt van de dezelfde affiniteitsgroep.

> [AZURE.NOTE]  Als u een affiniteitsgroep wilt maken, opent u het gedeelte <b>Instellingen</b> van de [klassieke Azure Portal](https://manage.windowsazure.com), klikt u op <b>Affiniteitsgroepen</b> en klikt u ofwel op <b>Een affiniteitsgroep toevoegen</b> ofwel op <b>Toevoegen</b>. U kunt ook affiniteitsgroepen maken en beheren met de Azure Service Management-API. Zie <a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">Bewerkingen voor affiniteitsgroepen</a> voor meer informatie.

## Toegangssleutels voor opslag weergeven, kopiëren en opnieuw genereren

Wanneer u een opslagaccount maakt, genereert Azure twee 512-bits opslagtoegangssleutels, die worden gebruikt voor verificatie wanneer het opslagaccount wordt geopend. Dankzij de twee opslagtoegangssleutels biedt Azure u de mogelijkheid de sleutels opnieuw te genereren zonder onderbreking van uw opslagservice of de toegang hiertoe.

> [AZURE.NOTE] We raden u aan uw opslagtoegangssleutels met niemand anders te delen. Om toegang te verlenen tot opslagresources zonder uw opslagtoegangssleutels uit handen te geven, kunt u een *Shared Access Signature* gebruiken. Een Shared Access Signature biedt toegang tot een resource in uw account gedurende een door u gedefinieerde periode en met de machtigingen die u opgeeft. Zie [Shared Access Signatures: inzicht in het SAS-model](storage-dotnet-shared-access-signature-part-1.md) voor meer informatie.

In de [klassieke Azure Portal](https://manage.windowsazure.com) gebruikt u **Sleutels beheren** op het dashboard of de pagina **Opslag** om de toegangssleutels voor opslag te bekijken, te kopiëren en opnieuw te genereren die worden gebruikt voor toegang tot de services Blob, Table en Queue.

### Een toegangssleutel voor opslag kopiëren  

U kunt **Sleutels beheren** gebruiken om een toegangssleutel voor opslag te kopiëren voor gebruik in een verbindingsreeks. Voor de verbindingsreeks zijn de naam van het opslagaccount en een sleutel nodig voor gebruik bij verificatie. Voor meer informatie over het configureren van verbindingsreeksen voor toegang tot Azure Storage-services ziet u [Azure Storage-verbindingsreeksen configureren](storage-configure-connection-string.md).

1. In de [klassieke Azure Portal](https://manage.windowsazure.com) klikt u op **Opslag** en vervolgens op de naam van het opslagaccount om het dashboard te openen.

2. Klik op **Sleutels beheren**.

    **Toegangssleutels beheren** wordt geopend.

    ![Sleutels beheren](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)


3. Als u een toegangssleutel voor opslag wilt kopiëren, selecteert u de sleuteltekst. Klik met de rechtermuisknop en klik op **Kopiëren**.

### Opslagtoegangssleutels opnieuw genereren
We raden u aan de toegangssleutels voor uw opslagaccount regelmatig te wijzigen om uw opslagverbindingen veilig te houden. Er worden twee toegangssleutels toegewezen, zodat u tijdens het opnieuw genereren van één toegangssleutel de verbinding met het opslagaccount in stand kunt houden met behulp van uw andere toegangssleutel.

> [AZURE.WARNING] Het opnieuw genereren van uw toegangssleutels kan invloed hebben op services in Azure en op uw eigen toepassingen die afhankelijk zijn van het opslagaccount. Alle clients die gebruikmaken van de toegangssleutel voor toegang tot het opslagaccount, moeten worden bijgewerkt om de nieuwe sleutel te kunnen gebruiken.

**Media Services**: als u mediaservices hebt die afhankelijk zijn van uw opslagaccount, moet u de toegangssleutels opnieuw synchroniseren met uw mediaservice nadat u de sleutels opnieuw hebt gegenereerd.

**Toepassingen**: als u webtoepassingen of cloudservices hebt die gebruikmaken van het opslagaccount, wordt de verbinding verbroken als u opnieuw sleutels genereert, tenzij u uw sleutels rouleert. 

**Opslagverkenner**: als u werkt met [opslagverkennertoepassingen](storage-explorers.md), moet u waarschijnlijk de opslagsleutel bijwerken die door deze toepassingen wordt gebruikt.

Dit is het proces voor roulatie van uw toegangssleutels voor opslag:

1. Werk de verbindingsreeksen in uw toepassingscode bij, zodat wordt verwezen naar de secundaire toegangssleutel van het opslagaccount.

2. Genereer de primaire toegangssleutel voor uw opslagaccount opnieuw. In de [klassieke Azure Portal](https://manage.windowsazure.com) klikt u via het dashboard of de pagina **Configureren** op **Sleutels beheren**. Klik op **Opnieuw genereren** onder de primaire toegangssleutel en klik vervolgens op **Ja** om te bevestigen dat u een nieuwe sleutel wilt genereren.

3. Werk de verbindingsreeksen in uw code bij, zodat deze verwijzen naar de nieuwe primaire toegangssleutel.

4. Genereer de secundaire toegangssleutel opnieuw.

## Een opslagaccount verwijderen

Als u een opslagaccount wilt verwijderen dat u niet meer gebruikt, gebruikt u **Verwijderen** op het dashboard of de pagina **Configureren**. Met **Verwijderen** wordt het hele opslagaccount verwijderd, inclusief alle blobs, tabellen en wachtrijen in het account.

> [AZURE.WARNING] Het is niet mogelijk om een verwijderd opslagaccount te herstellen of om inhoud terug te halen die het account vóór verwijdering bevatte. Zorg ervoor dat u een back-up maakt van alles dat u wilt opslaan, voordat u het account verwijdert. Dit geldt ook voor alle resources in het account: als u blobs, tabellen, wachtrijen of bestanden verwijdert, worden deze permanent verwijderd.
>
> Als uw opslagaccount VHD-bestanden bevat voor een virtuele Azure-machine, moet u alle installatiekopieën en schijven verwijderen die gebruikmaken van die VHD-bestanden. Daarna kunt u het opslagaccount pas verwijderen. Stop eerst de virtuele machine indien deze wordt uitgevoerd. Vervolgens verwijdert u deze. Als u schijven wilt verwijderen, gaat u naar het tabblad **Schijven** en verwijdert u de gewenste schijven. Als u installatiekopieën wilt verwijderen, gaat u naar het tabblad **Installatiekopieën** en verwijdert u alle installatiekopieën die zijn opgeslagen in het account.

1. Klik in de [klassieke Azure Portal](https://manage.windowsazure.com) op **Opslag**.

2. Klik ergens in de opslagaccountvermelding (niet op de naam) en klik vervolgens op **Verwijderen**.

     - Of -

    Klik op de naam van het opslagaccount om het dashboard te openen en klik vervolgens op **Verwijderen**.

3. Klik op **Ja** om te bevestigen dat u het opslagaccount wilt verwijderen.

## Volgende stappen

- Voor meer informatie over Azure Storage ziet u de [documentatie bij Azure Storage](https://azure.microsoft.com/documentation/services/storage/).
- Ga naar de [blog van het Azure Storage-team](http://blogs.msdn.com/b/windowsazurestorage/).
- [Gegevensoverdracht met het AzCopy-opdrachtregelprogramma](storage-use-azcopy.md)



<!--HONumber=Jun16_HO2-->


