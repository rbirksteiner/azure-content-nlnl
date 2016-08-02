## Wat is Azure File Storage?

File Storage biedt gedeelde opslag voor toepassingen die gebruikmaken van het standaard SMB 2.1- of SMB 3.0-protocol. Microsoft Azure Virtual Machines en Cloud Services kunnen bestandsgegevens via gekoppelde shares delen tussen toepassingsonderdelen, en on-premises toepassingen hebben via de File Storage-API toegang tot bestandsgegevens in een share.

Toepassingen die worden uitgevoerd in Azure Virtual Machines of Cloud Services kunnen een File Storage-share koppelen voor toegang tot bestandsgegevens, net zoals een bureaubladtoepassing een gewone SMB-share zou koppelen. Een willekeurig aantal virtuele machines of rollen in Azure kunnen de File Storage-share tegelijkertijd koppelen en gebruiken.

Omdat een File Storage-share een standaardbestandsshare in Azure die het SMB-protocol gebruikt, hebben toepassingen die in Azure worden uitgevoerd via I/O API's toegang tot de gegevens in de share. Dat betekent dat ontwikkelaars bestaande code en vaardigheden kunnen inzetten voor het migreren van bestaande toepassingen. IT-professionals kunnen PowerShell-cmdlets gebruiken om File Storage-shares te maken, te koppelen en te beheren als onderdeel van het beheer van Azure-toepassingen. In deze gids vindt u voorbeelden van beide.

Veelvoorkomende toepassingen van File Storage zijn onder andere:

- De migratie van on-premises toepassingen die afhankelijk zijn van bestandsshares om zonder kostbare regeneraties te kunnen worden uitgevoerd op virtuele machines in Azure of cloudservices
- De opslag van gedeelde toepassingsinstellingen, bijvoorbeeld in configuratiebestanden
- De opslag op een gedeelde locatie van diagnostische gegevens, zoals logboeken, metrische gegevens en crashdumps 
- De opslag van hulpprogramma’s en functies die nodig zijn voor de ontwikkeling of het beheer van virtuele machines in Azure of cloudservices

## Concepten van File Storage

File Storage bevat de volgende onderdelen:

![bestanden-concepten][files-concepts]

-   **Opslagaccount:** Alle toegang tot Azure Storage vindt plaats via een opslagaccount. Zie [Azure Storage Scalability and Performance Targets](../articles/storage/storage-scalability-targets.md) (Schaalbaarheids- en prestatiedoeleinden in Azure Storage) voor meer informatie over opslagaccountcapaciteit.

-   **Share:** een File Storage-share is een SMB-bestandsshare in Azure. 
    Alle mappen en bestanden moeten worden gemaakt in een bovenliggende share. Een account kan een onbeperkt aantal shares bevatten en een bestandsshare kan een onbeperkt aantal bestanden bevatten, tot de totale capaciteit van 5 TB van de bestandsshare.

-   **Map:** een optionele hiërarchie van mappen. 

-   **Bestand:** Een bestand in de share. Een bestand mag maximaal 1 TB groot zijn.

-   **URL-indeling:** Bestanden kunnen worden opgevraagd met de volgende URL-indeling:   
    https://`<storage
    account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`  
    
    De volgende voorbeeld-URL kan worden gebruikt om een van de bestanden in het bovenstaande diagram op te vragen:  
    `http://samples.file.core.windows.net/logs/CustomLogs/Log1.txt`

Zie [Shares, mappen, bestanden en metagegevens een naam geven en hiernaar verwijzen](http://msdn.microsoft.com/library/azure/dn167011.aspx) voor meer informatie over de naamgeving van shares, mappen en bestanden.

[bestanden-concepten]: ./media/storage-file-concepts-include/files-concepts.png


<!--HONumber=Jun16_HO2-->


