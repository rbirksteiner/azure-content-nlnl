<properties
    pageTitle="Overzicht van Azure Batch-functies | Microsoft Azure"
    description="Informatie over de functies van de Batch-service en de API's ervan, vanuit het standpunt van ontwikkelaars."
    services="batch"
    documentationCenter=".net"
    authors="yidingzhou"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="05/12/2016"
    ms.author="yidingz;marsma"/>

# Overzicht van Azure Batch-functies

Dit artikel biedt een basisoverzicht van de belangrijkste API-functies van de Azure Batch-service. Of u nu een gedistribueerde rekenkundige oplossing ontwikkelt met behulp van de API's van [Batch REST][batch_rest_api] of [Batch .NET][batch_net_api], u zult gebruikmaken van vele entiteiten en functies die hieronder worden besproken.

> [AZURE.TIP] Zie [Basisbeginselen van Azure Batch](batch-technical-overview.md) voor een overzicht van Batch op een hoger technisch niveau.

## <a name="workflow"></a>Werkstroom van de Batch-service

De volgende werkstroom op hoog niveau is doorgaans deze die wordt gebruikt door vrijwel alle gedistribueerde rekenkundige scenario's die in de Batch-service worden ontwikkeld:

1. Upload de *gegevensbestanden* die u wilt gebruiken in uw gedistribueerde rekenkundige scenario naar een [Azure-opslagaccount][azure_storage]. Deze bestanden moeten zich in het opslagaccount bevinden, zodat de Batch-service er toegang toe kan krijgen. Taken zullen deze bestanden downloaden naar [rekenknooppunten](#computenode) wanneer ze worden uitgevoerd.

2. Upload de afhankelijke *binaire bestanden* naar uw opslagaccount. Deze binaire bestanden bevatten het programma dat door de taken moet worden uitgevoerd en alle afhankelijke assembly's. Deze bestanden moeten ook worden geopend vanuit uw opslagaccount zodat ze door de taken naar de rekenknooppunten kunnen worden gedownload.

3. Maak een [pool](#pool) van rekenknooppunten. U geeft de [grootte van de rekenknooppunten][cloud_service_sizes] op die moet worden gebruikt wanneer de pool wordt gemaakt. Wanneer een taak wordt uitgevoerd, wordt hieraan een knooppunt in deze pool toegewezen.

4. Maak een [job](#job). Met behulp van een job kunt u een verzameling taken beheren.

5. Voeg meerdere [taken](#task) toe aan de job. Elke taak maakt gebruik van het programma dat u hebt geüpload om informatie te verwerken in de gegevensbestanden die u naar uw opslagaccount hebt geüpload.

6. Controleer de vooruitgang van de job en haal de resultaten op.

> [AZURE.NOTE] U hebt een [Batch-account](batch-account-create-portal.md) nodig om de Batch-service te gebruiken. Bovendien gebruiken vrijwel alle oplossingen een [Azure-opslagaccount][azure_storage] om bestanden op te slaan en op te halen. Batch ondersteunt momenteel alleen het opslagaccounttype **Algemeen**, zoals beschreven in stap 5 [Een opslagaccount maken](../storage/storage-create-storage-account.md#create-a-storage-account) in [Over Azure-opslagaccounts](../storage/storage-create-storage-account.md).

In de onderstaande secties vindt u uitleg over elk van de resources die in de bovenstaande werkstroom zijn vermeld, evenals over vele andere functies van Batch die uw gedistribueerde rekenkundige scenario mogelijk zullen maken.

## <a name="resource"></a> Bronnen van de Batch-service

Bij het gebruik van Batch maakt u gebruik van vele van de volgende resources. Sommige van deze resources, zoals accounts, rekenknooppunten, pools, jobs en taken worden gebruikt in alle Batch-oplossingen. Andere resources, zoals jobplanningen en toepassingspakketten, zijn nuttige, maar optionele functies.

- [Account](#account)
- [Rekenknooppunt](#computenode)
- [Pool](#pool)
- [Job](#job)
- [Taak](#task)
    - [Begintaak](#starttask)
    - [Jobbeheertaak](#jobmanagertask)
    - [Jobvoorbereidingstaken en jobvrijgevingstaken](#jobpreprelease)
    - [Taken met meerdere instanties](#multiinstance)
    - [Taakafhankelijkheden](#taskdep)
- [Jobplanningen](#jobschedule)
- [Toepassingspakketten](#appkg)

### <a name="account"></a>Account

Een Batch-account is een uniek geïdentificeerde entiteit in de Batch-service. Alle verwerkingen zijn gekoppeld aan een Batch-account. Wanneer u met de Batch-service bewerkingen wilt uitvoeren, hebt u de accountnaam en -sleutel nodig. Zie [Azure Batch-account in Azure Portal maken en beheren](batch-account-create-portal.md) als u een Batch-account wilt maken.

### <a name="computenode"></a>Rekenknooppunt

Een rekenknooppunt is een virtuele machine van Azure die aan een specifieke workload voor uw toepassing is toegewezen. De grootte van een knooppunt bepaalt het aantal CPU-kernen, de geheugencapaciteit en de grootte van het lokale bestandssysteem die aan het knooppunt worden toegewezen. Een knooppunt kan net zo groot zijn als een van de [knooppuntgrootten voor een cloudservice][cloud_service_sizes], met uitzondering van A0.

Knooppunten kunnen uitvoerbare bestanden en scripts uitvoeren, zoals uitvoerbare bestanden (.exe), opdrachtbestanden (.cmd), batchbestanden (.bat) en PowerShell-scripts. Een knooppunt heeft ook de volgende kenmerken:

- Op elk rekenknooppunt worden een standaard**mapstructuur** en hieraan gekoppelde **omgevingsvariabelen** met gedetailleerde informatie over de paden gemaakt. Zie [Bestanden en mappen](#files) hieronder voor meer informatie.
- **Omgevingsvariabelen**, beschikbaar voor verwijzing door taken.
- **Firewall**instellingen die zijn geconfigureerd om de toegang te beheren.
- Als **externe toegang** naar een rekenknooppunt is vereist (bijvoorbeeld voor foutopsporing), kan een RDP-bestand worden verkregen, dat vervolgens kan worden gebruikt voor toegang tot het knooppunt via *Extern bureaublad*.

### <a name="pool"></a>Pool

Een pool is een verzameling van knooppunten waarop uw toepassing wordt uitgevoerd. De pool kan door u handmatig worden gemaakt of automatisch door de Batch-service worden gemaakt wanneer u het werk opgeeft dat moet worden uitgevoerd. U kunt een pool maken en beheren die voldoet aan de behoeften van uw toepassing. Pools kunnen alleen worden gebruikt door het Batch-account waarin ze zijn gemaakt. Een Batch-account kan meer dan één pool hebben.

Azure Batch-pools bouwen voort op het Azure-kernrekenplatform: Batch-pools bieden grootschalige toewijzing, installatie van toepassingen, gegevensdistributie en statuscontrole, evenals de flexibele aanpassing van het aantal rekenknooppunten binnen een pool (vergroten/verkleinen).

Aan elk knooppunt dat aan een pool wordt toegevoegd, wordt een unieke naam en een uniek IP-adres toegewezen. Wanneer een knooppunt uit een pool wordt verwijderd, gaan alle wijzigingen in het besturingssysteem of bestanden verloren. De naam en het IP-adres van het verwijderde knooppunt worden vrijgegeven voor toekomstig gebruik. Wanneer een knooppunt een pool verlaat, is de levensduur ervan beëindigd.

U kunt een pool zodanig configureren dat communicatie tussen de knooppunten in de pool is toegestaan. Als voor een pool communicatie binnen de pool is aangevraagd, kan de Batch-service op elk knooppunt in de pool poorten hoger dan 1100 inschakelen. Elk knooppunt in de pool is zodanig geconfigureerd dat inkomende verbindingen alleen voor dit poortbereik worden toegestaan, en alleen vanuit andere knooppunten in de pool. Als voor uw toepassing geen communicatie tussen knooppunten is vereist, kan de Batch-service een potentieel groter aantal knooppunten aan de pool toewijzen vanuit vele verschillende clusters en datacenters, om meer parallelle verwerkingskracht mogelijk te maken.

Wanneer u een pool maakt, kunt u de volgende kenmerken opgeven:

- **Grootte van de knooppunten** in de pool
    - Kies knooppunten van de geschikte grootte. Houd daarbij rekening met de kenmerken en vereisten van de toepassingen die op de knooppunten zullen worden uitgevoerd. De knooppuntgrootte wordt doorgaans geselecteerd in de veronderstelling dat op het knooppunt één taak tegelijk wordt uitgevoerd. Houd ook rekening met het feit of bijvoorbeeld de toepassing meerdere threads heeft en hoeveel geheugen deze gebruikt. Zo kunt u gemakkelijker de meest geschikte en voordeligste knooppuntgrootte bepalen. Het is mogelijk om meerdere taken toe te wijzen en meerdere exemplaren van een toepassing parallel uit te voeren. Voor dergelijke gevallen wordt doorgaans een groter knooppunt gekozen. Zie 'Taakplanningsbeleid' hieronder voor meer informatie.
    - Alle knooppunten in een pool moeten even groot zijn. Als verschillende toepassingen met verschillende systeemvereisten en/of workloadniveaus moeten worden uitgevoerd, moeten afzonderlijke pools worden gemaakt.
    - Voor een pool kunnen alle [knooppuntgrootten voor een cloudservice][cloud_service_sizes] worden geconfigureerd, met uitzondering van A0.

- **Type** en **versie van besturingssysteem** dat op de knooppunten wordt uitgevoerd
    - Net als bij werkrollen in Cloud Services kan het *type besturingssysteem* en de *versie van het besturingssysteem* worden opgegeven. (Zie de sectie [Tell me about cloud services][about_cloud_services] (Informatie over cloudservices) in *Compute Hosting Options Provided by Azure* (Door Azure verstrekte hostingopties berekenen) voor meer informatie over werkrollen).
    - Het type besturingssysteem bepaalt ook welke versies van .NET bij het besturingssysteem zijn geïnstalleerd.
    - Net als bij werkrollen verdient het aanbeveling om `*` op te geven voor de versie van het besturingssysteem, zodat de knooppunten automatisch worden bijgewerkt en er geen werk vereist is om tegemoet te komen aan nieuwe versies. Er wordt voornamelijk voor een specifieke versie van een besturingssysteem gekozen om ervoor te zorgen dat toepassingen compatibel blijven, en om compatibiliteitstests met eerdere versies te kunnen uitvoeren alvorens toe te staan dat de versie mag worden bijgewerkt. Na de validatie kan de versie van het besturingssysteem voor de pool worden bijgewerkt en kan de nieuwe installatiekopie van het besturingssysteem worden geïnstalleerd – elke actieve taak wordt onderbroken en opnieuw in de wachtrij geplaatst.

- **Doelaantal knooppunten** dat beschikbaar moet zijn voor de pool

- **Schaalbeleid** voor de pool
    - Naast het aantal knooppunten kunt u voor een pool ook een [formule voor automatisch schalen](batch-automatic-scaling.md) opgeven. De Batch-service zal de formule uitvoeren en het aantal knooppunten in de pool aanpassen op basis van verschillende pool-, job- en taakparameters die u kunt opgeven.

- **Taakplanning**sbeleid
    - De configuratieoptie [Maximum aantal taken per knooppunt](batch-parallel-node-tasks.md) bepaalt het maximum aantal taken dat parallel kan worden uitgevoerd op elk knooppunt in de pool.
    - Bij de standaardconfiguratie wordt één taak tegelijk op een rekenknooppunt uitgevoerd, maar er zijn scenario's waarin het nuttig is om meer dan één taak tegelijk op een knooppunt uit te voeren. Een voorbeeld hiervan is het verhogen van het knooppuntgebruik als een toepassing moet wachten op I/O. Door meer dan één toepassing tegelijk te laten uitvoeren, wordt het CPU-gebruik verhoogd. Een ander voorbeeld is het verlagen van het aantal knooppunten in de pool. Hierdoor kan de grootte van de vereiste gegevensoverdracht voor grote sets van verwijzingsgegevens worden gereduceerd - als een knooppuntgrootte A1 volstaat voor een toepassing, kan in plaats daarvan de knooppuntgrootte A4 worden gekozen en kan de pool worden geconfigureerd voor acht parallelle taken, die elk gebruikmaken van een kern.
    - U kunt ook een 'opvultype' opgeven dat bepaalt of Batch de taken gelijkmatig verspreid over alle knooppunten of in elk knooppunt het maximum aantal taken gebruikt alvorens taken toe te wijzen aan een ander knooppunt in de pool.

- **Communicatiestatus** van de knooppunten in de pool
    - Een pool kan worden geconfigureerd om communicatie toe te staan tussen de knooppunten in de pool die de onderliggende netwerkinfrastructuur bepaalt. Houd er rekening mee dat dit ook van invloed is op de plaatsing van de knooppunten binnen clusters.
    - In de meeste scenario's werken taken los van elkaar en hoeven ze niet met elkaar te communiceren. Er kunnen echter bepaalde toepassingen zijn waarin taken wel moeten communiceren.

- **Begintaak** voor knooppunten in de pool
    - Er kan een *begintaak* worden opgegeven die wordt uitgevoerd elke keer dat een rekenknooppunt aan de pool wordt toegevoegd en wanneer een knooppunt opnieuw wordt gestart. Dit wordt vaak gedaan om een toepassing te installeren die moet worden gebruikt door de taken die op het knooppunt worden uitgevoerd.

### <a name="job"></a>Job

Een job is een verzameling taken en geeft aan hoe een berekening wordt uitgevoerd op rekenknooppunten in een pool.

- De job bepaalt de **pool** waarin het werk wordt uitgevoerd. De pool kan een bestaande pool zijn, die eerder is gemaakt voor gebruik door vele jobs, of op aanvraag worden gemaakt voor elke job die aan een jobplanning is gekoppeld, of voor alle jobs die aan een jobplanning zijn gekoppeld.
- Daarbij kan optioneel een **jobprioriteit** worden opgegeven. Wanneer een job wordt verzonden met een hogere prioriteit dan de jobs die in uitvoering zijn, worden de taken van de job met hogere prioriteit in de wachtrij ingevoegd voor de taken van de job met lagere prioriteit. Taken met lagere prioriteit die al in uitvoering zijn, kunnen niet worden verschoven.
- **Beperkingen** voor jobs stellen bepaalde limieten aan uw taken.
    - Voor jobs kan een **maximale kloktijd** worden ingesteld. Als de jobs langer worden uitgevoerd dan de maximale kloktijd die is opgegeven, worden de job en alle gekoppelde taken beëindigd.
    - Azure Batch kan taken detecteren die mislukken en ze opnieuw proberen uit te voeren. Het **maximum aantal nieuwe pogingen voor een taak** kan worden opgegeven als een beperking. U kunt er ook voor kiezen om altijd of nooit een poging te doen om een taak opnieuw uit te voeren. Wanneer u een taak opnieuw probeert uit te voeren, wordt deze opnieuw in de wachtrij geplaatst om opnieuw te worden uitgevoerd.
- Taken kunnen aan de job worden toegevoegd door uw clienttoepassing. U kunt ook een [jobbeheertaak](#jobmanagertask) opgegeven. Een jobbeheertaak maakt gebruik van de Batch-API en bevat de benodigde informatie om de vereiste taken voor een job te maken, waarbij de taak wordt uitgevoerd op een van de rekenknooppunten binnen de pool. De jobbeheertaak wordt specifiek door Batch verwerkt en wordt in de wachtrij geplaatst zodra de job is gemaakt, en wordt opnieuw opgestart als deze mislukt. Een jobbeheertaak is vereist voor jobs die door een jobplanning zijn gemaakt omdat dit de enige manier om de taken te definiëren voordat de job wordt geïnstantieerd. Hieronder vindt u meer informatie over jobbeheertaken.

### <a name="task"></a>Taak

Een taak is een rekeneenheid die aan een job is gekoppeld en die op een knooppunt wordt uitgevoerd. Taken worden toegewezen aan een knooppunt om te worden uitgevoerd of in een wachtrij geplaatst tot een knooppunt beschikbaar is. Een taak maakt gebruik van de volgende resources:

- De toepassing die is opgegeven in de **opdrachtregel** van de taak.

- **Bronbestanden** die de te verwerken gegevens bevatten. Deze bestanden worden automatisch gekopieerd naar het knooppunt van Blob Storage in een Azure-opslagaccount van het type **Algemeen**. Zie *Begintaak* en [Bestanden en mappen](#files) hieronder voor meer informatie.

- De **omgevingsvariabelen** die zijn vereist voor de toepassing. Zie [Omgevingsinstellingen voor taken](#environment) hieronder voor meer informatie.

- De **beperkingen** waarin de berekening moet plaatsvinden. Bijvoorbeeld de maximale duur dat de taak mag worden uitgevoerd, het maximum aantal keren dat een taak opnieuw moet worden uitgevoerd indien deze mislukt en de maximale duur dat bestanden in de werkmap behouden blijven.

Naast de taken die u definieert om een berekening op een knooppunt uit te voeren, stelt de Batch-service ook de volgende speciale taken beschikbaar:

- [Begintaak](#starttask)
- [Jobbeheertaak](#jobmanagertask)
- [Jobvoorbereidingstaken en jobvrijgevingstaken](#jobmanagertask)
- [Taken met meerdere instanties](#multiinstance)
- [Taakafhankelijkheden](#taskdep)

#### <a name="starttask"></a>Begintaak

Door een **begintaak** aan een pool te koppelen, kunt u de besturingsomgeving van de knooppunten ervan configureren en acties uitvoeren zoals het installeren van software of het starten van achtergrondprocessen. De begintaak wordt uitgevoerd telkens als een knooppunt wordt gestart zolang deze in de pool blijft, ook wanneer het knooppunt voor het eerst aan de pool wordt toegevoegd. Een groot voordeel van de begintaak is dat deze alle informatie bevat die nodig is voor het configureren van rekenknooppunten en het installeren van toepassingen die nodig zijn voor het uitvoeren van de taak van een job. Hierdoor kan het aantal knooppunten in een pool net zo gemakkelijk worden verhoogd als dat een nieuw doelaantal knooppunten wordt opgegeven. Batch heeft alle informatie die nodig is om de nieuwe knooppunten te configureren en ze gereed te maken voor het accepteren van taken.

Net als bij elke Batch-taak kan een lijst met **bronbestanden** in [Azure Storage][azure_storage] worden opgegeven, naast een uit te voeren **opdrachtregel**. Azure Batch zal eerst de bestanden van Azure Storage kopiëren en daarna de opdrachtregel uitvoeren. Voor een begintaak van een pool bevat de lijst met bestanden doorgaans het toepassingspakket of de toepassingsbestanden, maar de begintaak kan ook referentiegegevens bevatten voor gebruik door alle taken die op de rekenknooppunten worden uitgevoerd. De opdrachtregel van de begintaak kan een PowerShell-script of een `robocopy`-bewerking uitvoeren, bijvoorbeeld om toepassingsbestanden naar de map 'gedeeld' te kopiëren, en vervolgens een MSI of `setup.exe` uitvoeren.

> [AZURE.IMPORTANT] Batch ondersteunt momenteel *alleen* het opslagaccounttype **Algemeen**, zoals beschreven in stap 5 [Een opslagaccount maken](../storage/storage-create-storage-account.md#create-a-storage-account) in [Over Azure-opslagaccounts](../storage/storage-create-storage-account.md). Uw Batch-taken (inclusief standaardtaken, begintaken, jobvoorbereidingstaken en jobvrijgevingstaken) moeten bronbestanden opgeven die zich *alleen* in opslagaccounts van het type **Algemeen** bevinden.

Het is doorgaans wenselijk dat de Batch-service wacht tot de begintaak is voltooid voordat het knooppunt als gereed wordt beschouwd om er taken aan toe te wijzen, maar dit kan worden geconfigureerd.

Als een begintaak op een rekenknooppunt mislukt, wordt de status van het knooppunt bijgewerkt om de fout aan te geven en zal het knooppunt niet beschikbaar zijn om taken toe te wijzen. Een begintaak kan mislukken als er een probleem optreedt bij het kopiëren van de bronbestanden van de begintaak uit de opslag, of als het proces dat door de opdrachtregel ervan wordt uitgevoerd een andere afsluitcode dan nul retourneert.

#### <a name="jobmanagertask"></a>Jobbeheertaak

Een **jobbeheertaak** wordt doorgaans gebruikt om de uitvoering van een job te beheren en/of te controleren. Bijvoorbeeld: maken en verzenden van de taken voor een job, bepalen van aanvullende taken die moeten worden uitgevoerd en bepalen wanneer werk is voltooid. Een jobbeheertaak is niet beperkt tot deze activiteiten, maar is echter een volledig uitgewerkte taak die alle acties kan uitvoeren die zijn vereist voor de job. Een jobbeheertaak kan bijvoorbeeld een bestand downloaden dat als een parameter is opgegeven, de inhoud van dat bestand analyseren en op basis van die inhoud aanvullende taken verzenden.

Een jobbeheertaak wordt voor alle andere taken gestart en heeft de volgende kenmerken:

- Een jobbeheertaak wordt automatisch door de Batch-service verzonden als een taak wanneer de job wordt gemaakt.

- De uitvoering van een jobbeheertaak wordt gepland voor de andere taken in een job.

- Het hieraan gekoppelde knooppunt is het laatste dat moeten worden verwijderd uit een pool wanneer de pool wordt verkleind.

- De beëindiging ervan kan worden gekoppeld aan de beëindiging van alle taken in de job.

- De jobbeheertaak krijgt de hoogste prioriteit wanneer deze opnieuw moet worden gestart. Als een niet-actief knooppunt niet beschikbaar is, kan de Batch-service een van de andere actieve taken in de pool beëindigen om ruimte te maken voor het uitvoeren van de jobbeheertaak.

- Een jobbeheertaak in de ene job heeft geen hogere prioriteit dan de taken van andere jobs. Tussen jobs worden alleen prioriteiten op jobniveau in acht genomen.

#### <a name="jobpreprelease"></a>Jobvoorbereidingstaken en jobvrijgevingstaken

Batch biedt een jobvoorbereidingstaak om de job in te stellen voordat deze wordt uitgevoerd, en een jobvrijgevingstaak voor onderhoud of opschoning nadat de job is uitgevoerd.

- **Jobvoorbereidingstaak** - De jobvoorbereidingstaak wordt uitgevoerd op alle rekenknooppunten die zijn gepland om taken uit te voeren, voordat een van de andere taken van de job wordt uitgevoerd. Gebruik de jobvoorbereidingstaak om bijvoorbeeld gegevens te kopiëren die door alle taken worden gedeeld, maar uniek zijn voor de job.
- **Jobvrijgevingstaak** - Wanneer een job is voltooid, wordt de jobvrijgevingstaak uitgevoerd op elk knooppunt in de pool dat ten minste één taak heeft uitgevoerd. Gebruik de jobvrijgevingstaak om bijvoorbeeld gegevens te verwijderen die door de jobvoorbereidingstaak zijn gekopieerd of om diagnostische logboekgegevens te comprimeren en te uploaden.

Bij jobvoorbereidings- en jobvrijgevingstaken kunt u een opdrachtregel opgeven die moet worden uitgevoerd wanneer de taak wordt aangeroepen. Beide soorten taken bieden mogelijkheden zoals bestanden downloaden, uitvoeren met verhoogde gebruikersrechten, aangepaste omgevingsvariabelen, maximale duur voor het uitvoeren, aantal nieuwe pogingen en bewaartijd voor bestanden.

Zie [Run job preparation and completion tasks on Azure Batch compute nodes](batch-job-prep-release.md)  (Jobvoorbereidings- en jobvrijgevingstaken uitvoeren op Azure Batch-rekenknooppunten) voor meer informatie over jobvoorbereidings- en jobvrijgevingstaken.

#### <a name="multiinstance"></a>Taken met meerdere instanties

Een [taak met meerdere instanties](batch-mpi.md) is een taak die is geconfigureerd om op meer dan één rekenknooppunt tegelijk te worden uitgevoerd. Bij taken met meerdere instanties kunt u High Performance Computing-scenario's zoals Message Passing Interface (MPI) inschakelen, waarvoor een groep rekenknooppunten samen moet worden toegewezen om één workload te verwerken.

Zie [Use multi-instance tasks to run Message Passing Interface (MPI) applications in Azure Batch](batch-mpi.md) (Taken met meerdere instanties gebruiken om Message Passing Interface (MPI)-toepassingen uit te voeren in Azure Batch) voor gedetailleerde informatie over het uitvoeren van MPI-jobs in Batch met behulp van de Batch .NET-bibliotheek.

#### <a name="taskdep"></a>Taakafhankelijkheden

Zoals de naam al aangeeft, kunt u met taakafhankelijkheden opgeven dat een taak afhangt van de voltooiing van andere taken voordat deze wordt uitgevoerd. Deze functie biedt ondersteuning voor situaties waarin een 'downstream'-taak gebruikmaakt van de uitvoer van een 'upstream'-taak, of wanneer een 'upstream'-taak initialisaties uitvoert die zijn vereist voor een 'downstream'-taak. Als u deze functie wilt gebruiken, moet u eerst taakafhankelijkheden inschakelen in uw Batch-job. Daarna geeft u voor elke taak die afhankelijk is van een andere (of vele andere), de taken op waarvan die taak afhankelijk is.

Bij taakafhankelijkheden kunt u scenario's zoals de volgende configureren:

* *taakB* hangt af van *taakA* (*taakB* kan pas worden uitgevoerd nadat *taakA* is voltooid)
* *taakC* is afhankelijk van *taakA* én *taakB*
* *taakD* is afhankelijk van een bereik van taken, zoals taken *1* tot *10*, voordat deze wordt uitgevoerd

Bekijk het codevoorbeeld van [taakafhankelijkheden][github_sample_taskdeps] in de GitHub-opslagplaats [azure-batch-samples][github_samples]. Daar wordt uitgelegd hoe u met behulp van de bibliotheek [Batch .NET][batch_net_api] taken configureert die afhankelijk zijn van andere taken.

### <a name="jobschedule"></a>Geplande jobs

Met behulp van jobplanningen kunt u in de Batch-service terugkerende jobs maken. Een jobplanning geeft aan wanneer u jobs moet uitvoeren en bevat de specificaties voor de uit te voeren jobs. Met een jobplanning kunt u de duur van de planning opgeven (hoe lang en wanneer de planning geldt) en hoe vaak in dat tijdsbestek jobs zouden moeten worden gemaakt.

### <a name="appkg"></a>Toepassingspakketten

Met de functie voor [toepassingspakketten](batch-application-packages.md) kunt u toepassingen in de rekenknooppunten in uw pools eenvoudig beheren en implementeren. Met toepassingspakketten kunt u eenvoudig meerdere versies uploaden en beheren van de toepassingen die door uw taken worden uitgevoerd, zoals binaire bestanden en ondersteuningsbestanden, en vervolgens een of meer van deze toepassingen automatisch implementeren in de rekenknooppunten in uw pool.

Batch regelt de samenwerking met Azure Storage op de achtergrond om uw toepassingspakketten veilig in rekenknooppunten op te slaan en te implementeren, zodat uw code en beheeroverhead kunnen worden vereenvoudigd.

Zie [Application deployment with Azure Batch application packages](batch-application-packages.md) (Toepassingsimplementatie met Azure Batch-toepassingspakketten) voor meer informatie over de functie voor toepassingspakketten.

## <a name="files"></a>Bestanden en mappen

Elke taak heeft een werkmap waarin de taak nul of meer bestanden en mappen maakt voor de opslag van het programma dat door de taak wordt uitgevoerd, de gegevens die erdoor worden verwerkt en de uitvoer van de verwerking die door de taak wordt uitgevoerd. Deze bestanden en mappen zijn daarna beschikbaar voor gebruik door andere taken bij het uitvoeren van een job. Alle taken, bestanden en mappen in een knooppunt zijn eigendom van één gebruikersaccount.

De Batch-service geeft een deel van het bestandssysteem in een knooppunt weer als de hoofdmap. De hoofdmap is beschikbaar voor een taak, die er toegang toe kan krijgen met behulp van de omgevingsvariabele `%AZ_BATCH_NODE_ROOT_DIR%`. Zie [Omgevingsinstellingen voor taken](#environment) voor meer informatie over het gebruik van omgevingsvariabelen.

![Mapstructuur rekenknooppunt][1]

De hoofdmap bevat de volgende mapstructuur:

- **Shared** – Deze locatie is een gedeelde map voor alle taken die in een knooppunt worden uitgevoerd, ongeacht de job. In het knooppunt is de gedeelde map toegankelijk via `%AZ_BATCH_NODE_SHARED_DIR%`. Deze map biedt lees-/schrijftoegang tot alle taken die in het knooppunt worden uitgevoerd. Taken kunnen in deze map bestanden maken, lezen, bijwerken en verwijderen.

- **Startup** – Deze locatie wordt door een begintaak gebruikt als werkmap. Alle bestanden die door de Batch-service worden gedownload om de begintaak te starten, worden ook in deze map opgeslagen. De beginmap in het knooppunt is beschikbaar via de omgevingsvariabele `%AZ_BATCH_NODE_STARTUP_DIR%`. De begintaak kan in deze map bestanden maken, lezen, bijwerken en verwijderen en deze map kan door begintaken worden gebruikt om het besturingssysteem te configureren.

- **Taken** - Voor elke taak die in het knooppunt wordt uitgevoerd, wordt een map gemaakt, die toegankelijk is via `%AZ_BATCH_TASK_DIR%`. Binnen elk taakmap maakt de Batch-service een werkmap (`wd`) waarvan het unieke pad wordt opgegeven door de omgevingsvariabele `%AZ_BATCH_TASK_WORKING_DIR%`. Deze map biedt lees-/schrijftoegang tot de taak. De taak kan in deze map bestanden maken, lezen, bijwerken en verwijderen. Deze map blijft behouden op basis van de beperking voor bewaartijd *RetentionTime* die voor de taak is opgegeven.
  - `stdout.txt` en `stderr.txt` - Deze bestanden worden naar de taakmap geschreven bij het uitvoeren van de taak.

Wanneer een knooppunt uit de pool wordt verwijderd, worden alle bestanden verwijderd die in het knooppunt zijn opgeslagen.

## <a name="lifetime"></a>Levensduur van pool en rekenknooppunt

Bij het ontwerp van uw Azure Batch-oplossing moet u een ontwerpbeslissing maken over hoe en wanneer pools worden gemaakt en hoe lang rekenknooppunten in die pools beschikbaar blijven.

Aan het ene uiteinde van het spectrum kan een pool worden gemaakt voor elke job wanneer deze wordt verzonden en kunnen de knooppunten ervan worden verwijderd zodra taken zijn uitgevoerd. Hiermee maximaliseert u het gebruik omdat de knooppunten alleen worden toegewezen wanneer dit echt nodig is en worden afgesloten wanneer ze inactief zijn. Hoewel dit betekent dat de job moet wachten tot de knooppunten zijn toegewezen, is het belangrijk om te weten dat taken voor knooppunten worden gepland zodra ze afzonderlijk beschikbaar zijn, toegewezen zijn en de begintaak is voltooid. Batch wacht *niet* tot alle knooppunten in een pool beschikbaar zijn alvorens taken toe te wijzen, zodat alle beschikbare knooppunten maximaal worden gebruikt.

Als, aan het andere uiteinde van het spectrum, het onmiddellijk starten van jobs de hoogste prioriteit heeft, kan een pool voortijdig worden gemaakt en de knooppunten ervan beschikbaar worden gesteld voordat jobs worden verzonden. In dit scenario kunnen taken van jobs onmiddellijk worden gestart, maar blijven knooppunten mogelijk inactief terwijl wordt gewacht tot taken worden toegewezen.

Een gecombineerde benadering (die doorgaans wordt gebruikt voor het verwerken van een variabele, maar continue workload) bestaat erin een pool te gebruiken waarnaar meerdere jobs worden verzonden, maar het aantal knooppunten te verhogen of te verlagen op basis van de job workload (zie *Toepassingen vergroten/verkleinen* hieronder). Dit kan reactief worden gedaan, op basis van de huidige workload, of proactief als de workload kan worden voorspeld.

## <a name="scaling"></a>Toepassingen vergroten/verkleinen

Door [automatisch te vergroten/verkleinen](batch-automatic-scaling.md) kunt u het aantal rekenknooppunten in een pool dynamisch laten aanpassen door de Batch-service op basis van de huidige workload en het resourcegebruik van uw rekenscenario. Zo kunt u de totale kosten van het uitvoeren van uw toepassing verlagen door alleen de benodigde resources te gebruiken en de niet-gebruikte resources vrij te geven. U kunt de instellingen voor het automatisch vergroten/verkleinen voor een pool opgeven wanneer u deze maakt of deze instellingen later inschakelen. U kunt ook de instellingen voor het vergroten/verkleinen bijwerken van een pool die al op automatisch vergroten/verkleinen is ingesteld.

Het automatisch vergroten/verkleinen wordt uitgevoerd door een **formule voor automatisch vergroten/verkleinen** voor een pool op te geven. De Batch-service gebruikt deze formule om het doelaantal knooppunten in de pool te bepalen voor het volgende interval voor vergroten/verkleinen (een interval dat u kunt opgeven).

Voor een bepaalde job moet u bijvoorbeeld een groot aantal taken verzenden om de uitvoering ervan te plannen. U kunt aan de pool een formule voor vergroten/verkleinen toewijzen die het aantal knooppunten in de pool aanpast op basis van het huidige aantal taken in uitvoering en de snelheid waarmee deze taken worden voltooid. De Batch-service evalueert periodiek de formule en vergroot/verkleint de pool op basis van de workload en uw formule-instellingen.

Een formule voor vergroten/verkleinen kan op de volgende metrische gegevens worden gebaseerd:

- **Metrische gegevens voor tijd** – op basis van statistieken die om de vijf minuten worden verzameld binnen het opgegeven aantal uren.

- **Metrische gegevens voor resources** – op basis van CPU-gebruik, bandbreedtegebruik, geheugengebruik en het aantal knooppunten.

- **Metrische gegevens voor taken** – op basis van de status van taken, zoals Actief, In behandeling en Voltooid.

Wanneer bij automatisch vergroten/verkleinen het aantal rekenknooppunten in een pool wordt verlaagd, moet rekening worden gehouden met actieve taken. Hiervoor kan in de formule een beleidsinstelling worden opgenomen voor het ongedaan maken van de toewijzing van een knooppunt, die aangeeft of actieve taken onmiddellijk worden gestopt of mogen worden voltooid voordat het knooppunt uit de pool wordt verwijderd.

> [AZURE.TIP] Als u het gebruik van rekenresources wilt maximaliseren, stelt u het doelaantal knooppunten aan het einde van een job in op nul, maar staat u toe dat actieve taken mogen worden voltooid.

Zie [Automatically scale compute nodes in an Azure Batch pool](batch-automatic-scaling.md) (Rekenknooppunten in een Azure Batch-pool automatisch vergroten/verkleinen) voor meer informatie over het automatisch vergroten/verkleinen van een toepassing.

## <a name="cert"></a>Beveiliging met certificaten

Certificaten gebruikt u doorgaans bij het versleutelen of ontsleutelen van gevoelige gegevens voor taken, zoals de sleutel voor een [Azure-opslagaccount][azure_storage]. Ter ondersteuning hiervan kunnen certificaten in knooppunten worden geïnstalleerd. Versleutelde geheimen worden via opdrachtregelparameters doorgegeven aan taken of worden ingesloten in een van de taakresources. De geïnstalleerde certificaten kunnen dan worden gebruikt om ze te ontsleutelen.

U gebruikt de bewerking[Certificaat toevoegen][rest_add_cert] (Batch REST-API) of de methode [CertificateOperations.CreateCertificate][net_create_cert] (Batch .NET API) om een certificaat aan een Batch-account toe te voegen. Daarna kunt u het certificaat aan een nieuwe of bestaande pool koppelen. Wanneer een certificaat aan een pool is gekoppeld, wordt het door de Batch-service in elk knooppunt in de pool geïnstalleerd. De Batch-service installeert de juiste certificaten wanneer het knooppunt wordt gestart, voordat een taak wordt gestart, met inbegrip van begintaken en jobbeheertaken.

## <a name="scheduling"></a>Planningsprioriteit

Aan jobs die u in Batch maakt, kunt u een prioriteit toewijzen. De Batch-service gebruikt de prioriteit van de job om de volgorde van de jobplanning binnen een account te bepalen. De prioriteitswaarden gaan van -1000 tot 1000, waarbij -1000 de laagste prioriteit is en 1000 de hoogste. U kunt de prioriteit van een job bijwerken door gebruik te maken van de bewerking [Update the properties of a job (De eigenschappen van een job bijwerken)][rest_update_job] (Batch REST-API) of door de eigenschap [CloudJob.Priority][net_cloudjob_priority] (Batch .NET API) te wijzigen.

Binnen hetzelfde account hebben jobs met hogere prioriteit in de planning voorrang op jobs met lagere prioriteit. Een job met hogere prioriteit in het ene account heeft geen planningsvoorrang op een andere job met een lagere prioriteitswaarde in een ander account.

De jobplanning tussen pools verloopt onafhankelijk. Tussen verschillende pools is er geen garantie dat een job met hogere prioriteit eerst wordt gepland als de gekoppelde pool over te weinig niet-actieve knooppunten beschikt. Binnen dezelfde pool hebben jobs met dezelfde prioriteit evenveel kans om gepland te worden.

## <a name="environment"></a>Omgevingsinstellingen voor taken

Elke taak die in een Batch-job wordt uitgevoerd, heeft toegang tot omgevingsvariabelen die worden ingesteld door de Batch-service (door het systeem gedefinieerd, zie de onderstaande tabel) en door omgevingsvariabelen die door de gebruiker worden gedefinieerd. Toepassingen en scripts die door taken op rekenknooppunten worden uitgevoerd, hebben toegang tot deze omgevingsvariabelen bij de uitvoering in het knooppunt.

Stel door de gebruiker gedefinieerde omgevingsvariabelen in bij gebruik van de bewerking [Add a task to a job (Een taak toevoegen aan een job)][rest_add_task] (Batch REST-API) of door de eigenschap [CloudTask.EnvironmentSettings][net_cloudtask_env] (Batch .NET API) te wijzigen bij het toevoegen van taken aan een job.

Haal de door het systeem en door de gebruiker gedefinieerde omgevingsvariabelen van een taak op met behulp van de bewerking [Get information about a task (Informatie over een taak ophalen)][rest_get_task_info] (Batch REST-API) of via de eigenschap [CloudTask.EnvironmentSettings][net_cloudtask_env] (Batch .NET API). Zoals al is vermeld, kunnen processen die op een rekenknooppunt worden uitgevoerd ook toegang krijgen tot alle omgevingsvariabelen, bijvoorbeeld met behulp van de vertrouwde syntaxis `%VARIABLE_NAME%`.

Voor elke taak die in een job is gepland, wordt de volgende set van door het systeem gedefinieerde omgevingsvariabelen ingesteld door de Batch-service:

| Naam van omgevingsvariabele       | Beschrijving                                                              |
|---------------------------------|--------------------------------------------------------------------------|
| `AZ_BATCH_ACCOUNT_NAME`         | De naam van het account waartoe de taak behoort.                       |
| `AZ_BATCH_JOB_ID`               | De id van de job waar de taak deel van uitmaakt.                             |
| `AZ_BATCH_JOB_PREP_DIR`         | Het volledige pad naar de map van de jobvoorbereidingstaak in het knooppunt.         |
| `AZ_BATCH_JOB_PREP_WORKING_DIR` | Het volledige pad naar de werkmap van de jobvoorbereidingstaak in het knooppunt. |
| `AZ_BATCH_NODE_ID`              | De id van het knooppunt waarin de taak wordt uitgevoerd.                         |
| `AZ_BATCH_NODE_ROOT_DIR`        | Het volledige pad naar de hoofdmap in het knooppunt.                         |
| `AZ_BATCH_NODE_SHARED_DIR`      | Het volledige pad van de gedeelde map in het knooppunt.                       |
| `AZ_BATCH_NODE_STARTUP_DIR`     | Het volledige pad naar de map van de begintaak van het rekenknooppunt in het knooppunt.    |
| `AZ_BATCH_POOL_ID`              | De id van de pool waarin de taak wordt uitgevoerd.                         |
| `AZ_BATCH_TASK_DIR`             | Het volledige pad naar de map van de taak in het knooppunt.                         |
| `AZ_BATCH_TASK_ID`              | De id van de huidige taak.                                              |
| `AZ_BATCH_TASK_WORKING_DIR`     | Het volledige pad naar de werkmap van de taak in het knooppunt.                 |

>[AZURE.NOTE] U kunt de bovenstaande door het systeem gedefinieerde variabelen niet overschrijven. Ze kunnen alleen worden gelezen.

## <a name="errorhandling"></a>Foutafhandeling

Soms is het nodig om in uw Batch-oplossing taak- en toepassingsfouten af te handelen.

### Afhandeling van taakfouten
Taakfouten kunnen worden onderverdeeld in deze categorieën:

- **Planningsfouten**
    - Als de overdracht van opgegeven bestanden om een bepaalde reden mislukt, wordt voor de taak een planningsfout ingesteld.
    - Planningsfouten kunnen worden veroorzaakt doordat bestanden zijn verplaatst, het opslagaccount niet langer beschikbaar is of omdat een ander probleem ertoe heeft geleid dat bestanden niet naar het knooppunt kunnen worden gekopieerd.
- **Toepassingsfouten**
    - Het proces dat door de opdrachtregel van de taak wordt opgegeven, kan ook mislukken. Het proces wordt als mislukt beschouwd wanneer het proces dat door de taak wordt uitgevoerd, een andere afsluitcode dan nul retourneert.
    - Voor toepassingsfouten is het mogelijk om Batch automatisch een opgegeven aantal pogingen te laten doen om de taak opnieuw uit te voeren.
- **Beperkingsfouten**
    - De beperking *maxWallClockTime* kan worden ingesteld om te bepalen hoe lang een job of taak maximaal kan worden uitgevoerd. Deze maximale kloktijd kan handig zijn om vastgelopen taken te beëindigen.
    - Wanneer de maximale hoeveelheid tijd wordt overschreden, wordt de taak als *voltooid* gemarkeerd, maar wordt de afsluitcode ingesteld op `0xC000013A` en wordt het veld *schedulingError* gemarkeerd als `{ category:"ServerError", code="TaskEnded"}`.

### Foutopsporing van toepassingsfouten

Bij het uitvoeren van een toepassing kan deze een diagnostische uitvoer bieden die handig is voor het oplossen van problemen. Zoals eerder is vermeld in [Bestanden en mappen](#files), verzendt de Batch-service de uitvoer stdout en stderr naar de bestanden `stdout.txt` en `stderr.txt` in de taakmap in het rekenknooppunt. Met behulp van [ComputeNode.GetNodeFile][net_getfile_node] en [CloudTask.GetNodeFile][net_getfile_task] in de .NET API van Batch kunt u deze en andere bestanden ophalen om problemen op te lossen.

Nog uitgebreidere foutopsporing kan worden uitgevoerd door bij een rekenknooppunt aan te melden met behulp van *Extern bureaublad*. U kunt een [Extern bureaublad-protocolbestand (.rdp) ophalen uit een knooppunt][rest_rdp] (Batch REST-API) of gebruikmaken van de methode [ComputeNode.GetRDPFile][net_rdp] (Batch .NET API) voor een externe aanmelding.

>[AZURE.NOTE] Als u via RDP verbinding wilt maken met een knooppunt, moet u in het knooppunt eerst een gebruiker maken. [Voeg een gebruikersaccount toe aan een knooppunt][rest_create_user] in de REST-API van Batch of maak gebruik van de methode [ComputeNode.CreateComputeNodeUser][net_create_user] in Batch .NET.

### Verklaring voor mislukte taken of onderbrekingen van taken

Van tijd tot tijd mislukken taken of worden ze onderbroken. In de taaktoepassing zelf kan een fout optreden, het knooppunt waarin de taak wordt uitgevoerd kan opnieuw zijn opgestart of het knooppunt is mogelijk uit de pool verwijderd bij een vergroot-/verkleinbewerking indien het beleid voor het ongedaan maken van de toewijzing van een knooppunt is ingesteld om knooppunten onmiddellijk te verwijderen zonder te wachten tot taken zijn voltooid. In alle gevallen moet de taak door Batch automatisch opnieuw in de wachtrij worden geplaatst voor uitvoering in een ander knooppunt.

Het is ook mogelijk dat een onregelmatig probleem ertoe leidt dat een taak vastloopt of dat de uitvoering ervan te lang duurt. De maximale uitvoeringstijd voor een taak kan worden ingesteld en wanneer deze wordt overschreden, zal Batch de taaktoepassing onderbreken.

### Problemen met onjuiste rekenknooppunten oplossen

In situaties waarin een aantal taken mislukken, kan uw Batch-clienttoepassing of -service de metagegevens van de mislukte taken onderzoeken om een knooppunt te identificeren dat zich niet normaal gedraagt. Elk knooppunt in een pool krijgt een unieke id en het knooppunt waarin een taak wordt uitgevoerd, is opgenomen in de metagegevens van de taak. Zodra dit is vastgesteld, kunt u verschillende acties ondernemen:

- **Het knooppunt opnieuw opstarten** ([REST][rest_reboot] | [.NET][net_reboot])

    Soms kunnen latente problemen zoals vastgelopen of gecrashte processen worden opgelost door het knooppunt opnieuw op te starten. Houd er rekening mee dat als uw pool een begintaak gebruikt of als uw job een jobvoorbereidingstaak gebruikt, deze worden uitgevoerd wanneer het knooppunt opnieuw wordt opgestart.

- **Installatiekopie van knooppunt terugzetten** ([REST][rest_reimage] | [.NET][net_reimage])

    Hiermee wordt het besturingssysteem opnieuw geïnstalleerd in het knooppunt. Net als bij het opnieuw opstarten van een knooppunt worden begintaken en jobvoorbereidingstaken opnieuw uitgevoerd nadat de installatiekopie van het knooppunt is teruggezet.

- **Het knooppunt uit de pool verwijderen** ([REST][rest_remove] | [.NET][net_remove])

    Soms is het nodig om het knooppunt volledig uit de pool te verwijderen.

- **Taakplanning in het knooppunt uitschakelen** ([REST][rest_offline] | [.NET][net_offline])

    Hierdoor wordt het knooppunt effectief offline geplaatst, zodat er geen taken meer aan worden toegewezen, maar kan het knooppunt actief en in de pool blijven. Zo kunt u verder onderzoek verrichten naar de oorzaak van de fouten zonder verlies van gegevens van de mislukte taak en zonder dat het knooppunt extra taakfouten veroorzaakt. U kunt bijvoorbeeld de taakplanning in het knooppunt uitschakelen en vervolgens extern aanmelden om de gebeurtenislogboeken van het knooppunt te onderzoeken of een andere probleemoplossing uitvoeren. Wanneer u klaar bent met het onderzoek, kunt u het knooppunt weer online plaatsen door de taakplanning in te schakelen ([REST][rest_online], [.NET][net_online]) of een van de andere acties uitvoeren die hierboven zijn besproken.

> [AZURE.IMPORTANT] Met elk van de bovenstaande acties (opnieuw opstarten, installatiekopie terugzetten, verwijderen, taakplanning uitschakelen) kunt u opgeven hoe taken die momenteel in het knooppunt worden uitgevoerd, worden afgehandeld wanneer u de actie uitvoert. Wanneer u bijvoorbeeld de taakplanning uitschakelt op een knooppunt met de clientbibliotheek Batch .NET, kunt u een enum-waarde [DisableComputeNodeSchedulingOption][net_offline_option] opgeven om aan te geven of u actieve taken wilt **beëindigen**, **opnieuw in de wachtrij wilt plaatsen** voor planning op andere knooppunten of wilt toestaan dat taken worden voltooid voordat de actie wordt uitgevoerd (**TaskCompletion**).

## Volgende stappen

- Uw eerste Batch-toepassing maken door de stappen uit te voeren die vermeld zijn in [Aan de slag met de Azure Batch-bibliotheek voor .NET](batch-dotnet-get-started.md)
- Download en bouw het [Batch Explorer][batch_explorer_project]-voorbeeldproject voor gebruik bij het ontwikkelen van uw Batch-oplossingen. Met behulp van Batch Explorer kunt u het volgende en nog veel meer uitvoeren:
  - Pools, jobs en taken in uw Batch-account controleren en bewerken
  - `stdout.txt`, `stderr.txt` en andere bestanden van knooppunten downloaden
  - Gebruikers maken op knooppunten en RDP-bestanden downloaden voor externe aanmelding

[1]: ./media/batch-api-basics/node-folder-structure.png

[about_cloud_services]: ../cloud-services/cloud-services-choose-me.md
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies

[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx



<!--HONumber=Jun16_HO2-->


