Er zijn twee typen opslagaccounts:

### Opslagaccounts voor algemeen gebruik

Een opslagaccount voor algemeen gebruik biedt onder één account toegang tot Azure Storage-services zoals Tables, Queues, Files, Blobs en virtuele-machineschijven van Azure. Dit type opslagaccount heeft twee prestatielagen:

- Een laag voor standaardopslagprestaties, waarmee u Tables, Queues, Files, Blobs en virtuele-machineschijven van Azure kunt opslaan.
- Een Premium-opslagaccount, dat op dit moment alleen virtuele-machineschijven van Azure ondersteunt. Zie [Premium-opslag: krachtige opslag voor Azure Virtual Machine-werkbelasting](../articles/storage/storage-premium-storage.md) voor een gedetailleerd overzicht van Premium-opslag.

### Blob Storage-accounts

Een Blob Storage-account is een gespecialiseerd opslagaccount voor het opslaan van ongestructureerde gegevens als blobs (objecten) in Azure Storage. Blob Storage-accounts zijn vergelijkbaar met de bestaande opslagaccounts voor algemeen gebruik en bieden dezelfde hoogwaardige kenmerken op het gebied van duurzaamheid, beschikbaarheid, schaalbaarheid en prestaties waarover u nu al beschikt, inclusief 100 procent API-consistentie voor blok-blobs en toevoeg-blobs. Voor toepassingen die alleen blok- of toevoeg-blob-opslag nodig hebben, wordt het gebruik van Blob-opslagaccounts aangeraden.

> [AZURE.NOTE] Blob Storage-accounts ondersteunen alleen blok-blobs en toevoeg-blobs. Pagina-blobs worden niet ondersteund.

Blob-opslagaccounts hebben het kenmerk **Toegangslaag**, dat kan worden opgegeven wanneer het account wordt gemaakt en later naar behoefte kan worden gewijzigd. Er zijn twee soorten toegangslagen die u op basis van uw gegevenstoegangspatroon kunt opgeven:

- De toegangslaag **Hot**, waarmee wordt aangegeven dat de objecten in het opslagaccount vaker worden gebruikt. Hiermee kunt u gegevens tegen lagere toegangskosten opslaan.
- De toegangslaag **Cool**, waarmee wordt aangegeven dat de objecten in het opslagaccount minder vaak worden gebruikt. Hiermee kunt u gegevens tegen lagere gegevensopslagkosten opslaan.

Als er een wijziging optreedt in het gebruikspatroon van de gegevens, kunt u op elk gewenst moment schakelen tussen deze toegangslagen. Aan het wijzigen van de toegangslaag kunnen extra kosten zijn verbonden. Zie [Blob Storage-accounts: prijzen en facturering](../articles/storage/storage-blob-storage-tiers.md#pricing-and-billing) voor meer informatie.

Zie [Azure Blob Storage: Cool Storage-laag en Hot Storage-laag](../articles/storage/storage-blob-storage-tiers.md) voor meer informatie.

Voordat u een opslagaccount kunt maken, moet u een Azure-abonnement hebben, een abonnement dat u toegang geeft tot diverse Azure-services. Met een [gratis account](https://azure.microsoft.com/pricing/free-trial/) kunt u direct aan de slag met Azure. Als u besluit om een abonnement aan te schaffen, kunt u kiezen uit een aantal [aanschafopties](https://azure.microsoft.com/pricing/purchase-options/). Als u een [MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) bent, krijgt u gratis maandelijkse tegoeden die u kunt gebruiken met Azure-services, waaronder Azure Storage. Zie [Prijzen van Azure Storage](https://azure.microsoft.com/pricing/details/storage/) voor meer informatie over volumeprijzen.

Zie [Een opslagaccount maken](../articles/storage/storage-create-storage-account.md#create-a-storage-account) voor informatie over het maken van een opslagaccount. Met één abonnement kunt u maximaal 100 opslagaccounts met een unieke naam maken. Zie [Azure Storage Scalability and Performance Targets](../articles/storage/storage-scalability-targets.md) (Schaalbaarheids- en prestatiedoeleinden in Azure Storage) voor meer informatie over opslagaccountlimieten.



<!--HONumber=Jun16_HO2-->


