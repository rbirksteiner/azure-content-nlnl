<properties
    pageTitle="Een opslagaccount in de Azure Portal maken, beheren of verwijderen | Microsoft Azure"
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

Een Azure-opslagaccount biedt een unieke naamruimte voor het opslaan en openen van uw Azure Storage-gegevensobjecten. Alle objecten in een opslagaccount worden samen gefactureerd als een groep. De gegevens in uw account zijn standaard alleen beschikbaar voor u, de eigenaar van het account.

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## Facturering voor opslagaccounts

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [AZURE.NOTE] Wanneer u in Azure een virtuele machine maakt, wordt er automatisch een opslagaccount voor u gemaakt op de implementatielocatie als u op die locatie nog geen opslagaccount hebt. Het is dus niet nodig onderstaande stappen te volgen om een opslagaccount voor de schijven van uw virtuele machine te maken. De naam van het opslagaccount wordt gebaseerd op de naam van de virtuele machine. Zie [Documentatie bij Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) voor meer informatie.

## Eindpunten van opslagaccount

Elk object dat u in Azure Storage opslaat, heeft een uniek URL-adres. De naam van het opslagaccount vormt het subdomein van dat adres. De combinatie van subdomein- en domeinnaam, die specifiek zijn voor elke service, vormt een *eindpunt* voor uw opslagaccount.

Als de naam van uw opslagaccount bijvoorbeeld *mystorageaccount* is, zijn de standaardeindpunten voor uw opslagaccount:

- Blob-service: http://*mystorageaccount*.blob.core.windows.net

- Tabelservice: http://*mystorageaccount*.table.core.windows.net

- Queue-service: http://*mystorageaccount*.queue.core.windows.net

- File-service: http://*mystorageaccount*.file.core.windows.net

> [AZURE.NOTE] Een Blob Storage-account toont alleen het Blob-service-eindpunt.

De URL voor het openen van een object in een opslagaccount wordt samengesteld door de locatie van het object in de opslagaccount toe te voegen aan het eindpunt. Een blobadres kan bijvoorbeeld de volgende indeling hebben: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

U kunt ook een aangepaste domeinnaam configureren voor gebruik met uw opslagaccount. Voor klassieke opslagaccounts raadpleegt u [Een aangepaste domeinnaam configureren voor het eindpunt voor Blob Storage](storage-custom-domain-name.md) voor meer informatie. Voor ARM-opslagaccounts is deze mogelijkheid niet toegevoegd aan de [Azure Portal](https://portal.azure.com), maar u kunt deze configureren met PowerShell. Zie de cmdlet [Set AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx) voor meer informatie.  

## Een opslagaccount maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Selecteer in het menu Hub achtereenvolgens **Nieuw** -> **Gegevens en opslag** -> **Opslagaccount**.

3. Voer een naam in voor het opslagaccount. Zie [Eindpunten van opslagaccounts](#storage-account-endpoints) voor meer informatie over hoe de naam van het opslagaccount wordt gebruikt om uw objecten in Azure Storage te adresseren.

    > [AZURE.NOTE] Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten.
    >  
    > De naam van uw opslagaccount moet uniek zijn binnen Azure. Er wordt een waarschuwing weergegeven in de Azure Portal als de opslagaccountnaam die u kiest, al wordt gebruikt.

4. Geef het implementatiemodel op dat moet worden gebruikt: **Resource Manager** of **Klassiek**. **Resource Manager** is het aanbevolen implementatiemodel. Zie [Resource Manager-implementatie en klassieke implementatie begrijpen](../resource-manager-deployment-model.md) voor meer informatie.

    > [AZURE.NOTE] Blob Storage-accounts kunnen alleen worden gemaakt met het Resource Manager-implementatiemodel.

5. Selecteer het type opslagaccount: **Algemeen gebruik** of **Blob Storage**. **Algemeen gebruik** is de standaardinstelling.

    Als **Algemeen gebruik** is geselecteerd, geeft u vervolgens de prestatielaag aan: **Standard** of **Premium**. **Standard** is de standaardinstelling. Zie [Inleiding tot Microsoft Azure Storage](storage-introduction.md) en [Premium Storage: krachtige opslag voor Azure Virtual Machine-werkbelasting](storage-premium-storage.md) voor meer informatie over Standard en Premium Storage-accounts.

    Als **Blob Storage** is geselecteerd, geeft u vervolgens de toegangslaag op: **Hot** of **Cool**. **Hot** is de standaardinstelling. Zie [Azure Blob Storage: Hot Storage-laag en Cool Storage-laag](storage-blob-storage-tiers.md) voor meer informatie.

6. Selecteer de replicatieoptie voor het opslagaccount: **LRS**, **GRS**, **RA-GRS**, of **ZRS**. **RA-GRS** is de standaardinstelling. Zie [Azure Storage-replicatie](storage-redundancy.md) voor meer informatie over Azure Storage-replicatieopties.

7. Selecteer het abonnement waarin u het nieuwe opslagaccount wilt maken.

8. Geef een nieuwe resourcegroep op of selecteer een bestaande resourcegroep. Zie [De Azure Portal gebruiken om Azure-resources te beheren](../azure-portal/resource-group-portal.md) voor meer informatie over resourcegroepen.

9. Selecteer de geografische locatie voor het opslagaccount.

10. Klik op **Maken** om het opslagaccount te maken.

## Uw opslagaccount beheren

### De configuratie van uw account wijzigen

Nadat u uw opslagaccount hebt gemaakt, kunt u de configuratie ervan wijzigen. U kunt bijvoorbeeld de replicatie-optie aanpassen die voor het account wordt gebruikt of de toegangslaag voor een Blob Storage-account wijzigen. Navigeer in de [Azure Portal](https://portal.azure.com) naar uw opslagaccount, klik op **Alle instellingen** en klik vervolgens op **Configuratie** om de accountconfiguratie te bekijken en/of te wijzigen.

> [AZURE.NOTE] Afhankelijk van de prestatielaag die u hebt gekozen bij het maken van het opslagaccount, zijn sommige replicatieopties mogelijk niet beschikbaar.

Het wijzigen van de replicatieoptie is van invloed op het tarief dat u betaalt. Zie de pagina [Prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/) voor meer informatie.

Voor Blob Storage-accounts brengt het wijzigen van de toegangslaag naast wijziging van het tarief dat u betaalt, mogelijk ook wijzigingskosten met zich mee. Zie [Blob Storage-accounts: prijzen en facturering](storage-blob-storage-tiers.md#pricing-and-billing) voor meer informatie.

### De toegangssleutels van uw opslagaccount beheren

Wanneer u een opslagaccount maakt, genereert Azure twee 512-bits opslagtoegangssleutels, die worden gebruikt voor verificatie wanneer het opslagaccount wordt geopend. Dankzij de twee opslagtoegangssleutels biedt Azure u de mogelijkheid de sleutels opnieuw te genereren zonder onderbreking van uw opslagservice of de toegang hiertoe.

> [AZURE.NOTE] We raden u aan uw opslagtoegangssleutels met niemand anders te delen. Om toegang te verlenen tot opslagresources zonder uw opslagtoegangssleutels uit handen te geven, kunt u een *Shared Access Signature* gebruiken. Een Shared Access Signature biedt toegang tot een resource in uw account gedurende een door u gedefinieerde periode en met de machtigingen die u opgeeft. Zie [Shared Access Signatures: inzicht in het SAS-model](storage-dotnet-shared-access-signature-part-1.md) voor meer informatie.

#### Opslagtoegangssleutels bekijken en kopiëren

Navigeer in de [Azure Portal](https://portal.azure.com) naar uw opslagaccount, klik op **Alle instellingen** en klik vervolgens op **Toegangssleutels** om de toegangssleutels van uw account te bekijken, te kopiëren en opnieuw te genereren. De blade **Toegangssleutels** bevat ook vooraf geconfigureerde verbindingsreeksen die gebruikmaken van uw primaire en secundaire sleutels. U kunt deze reeksen kopiëren voor gebruik in uw toepassingen.

#### Opslagtoegangssleutels opnieuw genereren

We raden u aan de toegangssleutels voor uw opslagaccount regelmatig te wijzigen om uw opslagverbindingen veilig te houden. Er worden twee toegangssleutels toegewezen, zodat u tijdens het opnieuw genereren van één toegangssleutel de verbinding met het opslagaccount in stand kunt houden met behulp van uw andere toegangssleutel.

> [AZURE.WARNING] Het opnieuw genereren van uw toegangssleutels kan invloed hebben op services in Azure en op uw eigen toepassingen die afhankelijk zijn van het opslagaccount. Alle clients die gebruikmaken van de toegangssleutel voor toegang tot het opslagaccount, moeten worden bijgewerkt om de nieuwe sleutel te kunnen gebruiken.

**Media Services**: als u mediaservices hebt die afhankelijk zijn van uw opslagaccount, moet u de toegangssleutels opnieuw synchroniseren met uw mediaservice nadat u de sleutels opnieuw hebt gegenereerd.

**Toepassingen**: als u webtoepassingen of cloudservices hebt die gebruikmaken van het opslagaccount, wordt de verbinding verbroken als u opnieuw sleutels genereert, tenzij u uw sleutels rouleert.

**Opslagverkenner**: als u werkt met [opslagverkennertoepassingen](storage-explorers.md), moet u waarschijnlijk de opslagsleutel bijwerken die door deze toepassingen wordt gebruikt.

Dit is het proces voor roulatie van uw toegangssleutels voor opslag:

1. Werk de verbindingsreeksen in uw toepassingscode bij, zodat wordt verwezen naar de secundaire toegangssleutel van het opslagaccount.

2. Genereer de primaire toegangssleutel voor uw opslagaccount opnieuw. Klik op de blade **Toegangssleutels** op **Sleutel 1 opnieuw genereren**. Klik vervolgens op **Ja** om te bevestigen dat u een nieuwe sleutel wilt genereren.

3. Werk de verbindingsreeksen in uw code bij, zodat deze verwijzen naar de nieuwe primaire toegangssleutel.

4. Genereer de secundaire toegangssleutel op dezelfde manier opnieuw.

## Een opslagaccount verwijderen

Als u een opslagaccount dat u niet langer gebruikt  wilt verwijderen, gaat u in de [Azure Portal](https://portal.azure.com) naar het opslagaccount en klikt u op **Verwijderen**. Als u een opslagaccount verwijdert, wordt het hele account verwijderd, inclusief alle gegevens in het account.

> [AZURE.WARNING] Het is niet mogelijk om een verwijderd opslagaccount te herstellen of om inhoud terug te halen die het account vóór verwijdering bevatte. Zorg ervoor dat u een back-up maakt van alles dat u wilt opslaan, voordat u het account verwijdert. Dit geldt ook voor alle resources in het account: als u blobs, tabellen, wachtrijen of bestanden verwijdert, worden deze permanent verwijderd.

Als u een opslagaccount wilt verwijderen dat is gekoppeld aan een virtuele machine in Azure, moet u eerst controleren of de schijven van de virtuele machine zijn verwijderd. Als u de schijven van de virtuele machine niet eerst verwijdert, ziet u wanneer u uw opslagaccount probeert te verwijderen, een foutbericht met de volgende strekking:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Als het opslagaccount gebruikmaakt van het klassieke implementatiemodel, kunt u de schijf van de virtuele machine verwijderen via de volgende stappen in de [Azure Portal](https://manage.windowsazure.com):

1. Navigeer naar de [Klassieke Azure Portal](https://manage.windowsazure.com).
2. Navigeer naar het tabblad Virtuele machines.
3. Klik op het tabblad Schijven.
4. Selecteer de gegevensschijf en klik vervolgens op Schijf verwijderen.
5. Als u schijfinstallatiekopieën wilt verwijderen, gaat u naar het tabblad Installatiekopieën en verwijdert u alle installatiekopieën die in het account zijn opgeslagen.

Zie [Documentatie bij Azure Virtual Machines](http://azure.microsoft.com/documentation/services/virtual-machines/) voor meer informatie.

## Volgende stappen

- [Azure Blob Storage: Cool Storage-laag en Hot Storage-laag](storage-blob-storage-tiers.md)
- [Azure Storage-replicatie](storage-redundancy.md)
- [Azure Storage-verbindingsreeksen configureren](storage-configure-connection-string.md)
- [Gegevensoverdracht met het AzCopy-opdrachtregelprogramma](storage-use-azcopy.md)
- Ga naar de [blog van het Azure Storage-team](http://blogs.msdn.com/b/windowsazurestorage/).



<!--HONumber=Jun16_HO2-->


