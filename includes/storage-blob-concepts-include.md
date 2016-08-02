## Wat is Blob Storage?

Azure Blob Storage is een service voor het opslaan van grote hoeveelheden ongestructureerde objectgegevens, zoals tekst of binaire gegevens, die via HTTP of HTTPS overal vandaan kunnen worden opgevraagd. U kunt Blob Storage gebruiken om gegevens openbaar te maken of om toepassingsgegevens privé op te slaan.

Veelvoorkomende toepassingen van Blob Storage zijn onder andere:

- Het rechtstreeks aan een browser leveren van afbeeldingen of documenten
- De opslag van bestanden voor gedistribueerde toegang
- Streaming van video en audio
- De opslag van gegevens voor back-up en herstel, herstel na noodgevallen en archivering
- De opslag van gegevens voor analyse door een on-premises in Azure gehoste service

## Concepten van Blob-service

De Blob-service bevat de volgende onderdelen:

![Blob1][Blob1]

- **Opslagaccount:** Alle toegang tot Azure Storage vindt plaats via een opslagaccount. Dit opslagaccount kan een **algemeen opslagaccount** zijn of een **Blob-opslagaccount** dat speciaal is bedoeld voor de opslag van objecten/blobs. Zie [Azure-opslagaccount](../articles/storage/storage-create-storage-account.md) voor meer informatie over opslagaccounts.

- **Container:** Een container is een groepering van een reeks blobs. Alle blobs moeten zich in een container bevinden. Een account kan een onbeperkt aantal containers bevatten. Een container kan een onbeperkt aantal blobs bevatten. De naam van een container mag alleen kleine letters bevatten.

- **Blob:** Een bestand van willekeurig type en willekeurige grootte. Azure Storage biedt drie typen blobs: blok-blobs, pagina-blobs en toevoeg-blobs.

    *Blok-blobs* zijn ideaal voor de opslag van tekst- of binaire bestanden, zoals documenten en mediabestanden. *Toevoeg-blobs* zijn vergelijkbaar met blok-blobs in dat opzicht dat ze bestaan uit blokken, maar ze zijn geoptimaliseerd voor toevoegbewerkingen; ze zijn daarom nuttig voor logboekscenario's. Eén blok-blob of toevoeg-blob kan maximaal 50.000 blokken van maximaal 4 MB bevatten. Dat is in totaal iets meer dan 195 GB (4 MB x 50.000).

    *Pagina-blobs* kunnen 1 TB groot zijn, en zijn efficiënter voor regelmatige lees- en schrijfbewerkingen. In Azure Virtuele Machines worden pagina-blobs gebruikt als besturingssysteem- en gegevensschijven.

    Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](https://msdn.microsoft.com/library/azure/dd135715.aspx) voor meer informatie over de naamgeving van containers en blobs.


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg



<!--HONumber=Jun16_HO2-->


