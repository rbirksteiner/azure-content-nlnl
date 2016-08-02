<properties
    pageTitle="Wat is Azure Backup? | Microsoft Azure"
    description="Door gebruik te maken van Azure Backup en Recovery Services kunt u back-ups maken en herstellen van Windows-servers, Windows-clientcomputers, System Center DPM-servers en van virtuele machines van Azure."
    services="backup"
    documentationCenter=""
    authors="Jim-Parker"
    manager="jwhit"
    editor="tysonn"
    keywords="backup and restore; recovery services; backup solutions"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/10/2016"
    ms.author="jimpark; trinadhk"/>

# Wat is Azure Backup?
Azure Backup is de service die u gebruikt om back-ups te maken en te herstellen van uw gegevens in de Microsoft-cloud. Met Azure Backup vervangt u uw bestaande on-premises of off-site back-upoplossing door een betrouwbare, veilige en kostenbesparende cloudoplossing. Daarnaast draagt Azure Backup bij aan de beveiliging van de assets die worden uitgevoerd in de cloud. Azure Backup biedt herstelservices met een uitstekende infrastructuur die schaalbare, duurzaam en maximaal beschikbaar is.

[Een video-overzicht van Azure Backup bekijken](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## Waarom Azure Backup gebruiken?
Traditionele back-upoplossingen gebruiken de cloud als een eindpunt die vergelijkbaar is met schijven of bandgeheugen. Dit is een eenvoudige maar ook een beperkte methode. De methode profiteert niet optimaal van een onderliggend cloudplatform, wat zich vertaalt in een inefficiënte en dure oplossing.
Azure Backup biedt u daarentegen alle voordelen van een krachtige en betaalbare cloudback-upoplossing. Hier volgen enkele van de belangrijkste voordelen van Azure Backup.

| Functie | Voordeel |
| ------- | ------- |
| Automatische opslagbeheer | U hoeft geen kapitaal te investeren in on-premises opslagapparaten. De back-upopslag wordt automatisch door Azure Backup toegewezen en beheerd en u betaalt naar gebruik. |
| Onbeperkt schalen | Profiteer van hoge beschikbaarheidsgaranties zonder de overhead van onderhoud en bewaking. Azure Backup maakt gebruik van de onderliggende kracht en schaal van de Azure-cloud, die de mogelijkheid biedt om automatisch te schalen zonder dat u hier hinder van ondervindt. |
| Meerder opslagopties | Kies uw back-upopslag op basis van uw behoeften:<li>Een lokaal redundante opslagblok-blob is ideaal voor prijsbewuste klanten en beschermt de gegevens tegen lokale hardwarefouten. <li>Een geo-replicatieopslagblok-blob biedt drie extra kopieën in een gekoppeld datacenter. Deze extra kopieën zorgen voor een hoge beschikbaarheid van uw back-upgegevens, zelfs bij een noodgevel op het niveau van de Azure-site. |
| Onbeperkte gegevensoverdracht | Er worden geen kosten in rekening gebracht voor de uitgaande gegevensoverdracht tijdens een herstelbewerking vanuit de Backup-kluis. De inkomende gegevens naar Azure zijn ook gratis. Werkt met de importservice importeren waar deze beschikbaar is. |
| Gegevensversleuteling | Gegevensversleuteling zorgt voor een beveiligde overdracht en opslag van de gegevens van de klant in de openbare cloud. De wachtwoordzin voor versleuteling is opgeslagen in de bron en wordt nooit verzonden naar of opgeslagen in Azure. De versleutelingssleutel is vereist voor het herstellen van de gegevens en alleen de klant heeft volledige toegang tot de gegevens in de service. |  
| Toepassingsconsistente back-up | Toepassingsconsistente back-ups in Windows zorgen ervoor dat er geen correcties nodig zijn op het moment van de herstelbewerking, waardoor de beoogde hersteltijd wordt gereduceerd. Zodoende kunnen klanten sneller terugkeren naar een actieve status. |
| Lange bewaartermijn | In plaats dat klanten betalen voor externe back-upoplossingen met tapes, kunnen ze ook back-ups naar Azure maken. Azure biedt een fascinerende, tape-achtige oplossing tegen lage kosten. |

## Azure Backup-onderdelen
Omdat Backup een hybride back-upoplossing is, bestaat Backup uit verschillende onderdelen die samenwerken voor end-to-end back-up- en herstelwerkstromen.

![Azure Backup-onderdelen](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

### Implementatiescenario's

| Onderdeel | Kan worden geïmplementeerd in Azure? | Kan on-premises worden geïmplementeerd? | Doelopslag ondersteund|
| --- | --- | --- | --- |
| Azure Backup-agent | <p>**Ja**</p> <p>De Azure Backup-agent kan worden geïmplementeerd op elke virtuele machine van Windows Server die wordt uitgevoerd in Azure.</p> | <p>**Ja**</p> <p>De Azure Backup-agent kan worden geïmplementeerd op elke virtuele machine van Windows Server of fysieke computer.</p> | <p>Azure Backup-kluis</p> |
| System Center Data Protection Manager (DPM) | <p>**Ja**</p><p>Meer informatie over [het beveiligen van workloads in Azure met System Center DPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx).</p> | <p>**Ja**</p> <p>Meer informatie over [het beveiligen van workloads en VM's in uw datacenter](https://technet.microsoft.com/library/hh758173.aspx).</p> | <p>Lokaal gekoppelde schijf,</p> <p>Azure Backup-kluis,</p> <p>tape (alleen on-premises)</p> |
| Azure Backup-server | <p>**Ja**</p><p>Meer informatie over [het beveiligen van workloads in Azure met een Azure Backup-server](backup-azure-microsoft-azure-backup.md).</p> | <p>**Ja**</p> <p>Meer informatie over [het beveiligen van workloads in Azure met een Azure Backup-server](backup-azure-microsoft-azure-backup.md).</p> | <p>Lokaal gekoppelde schijf,</p> <p>Azure Backup-kluis</p> |
| Azure Backup (VM-extensie) | <p>**Ja**</p><p>Onderdeel van de Azure-infrastructuur</p><p>Speciaal voor [back-ups van de virtuele machines van Azure IaaS (Infrastructure as a Service)](backup-azure-vms-introduction.md).</p> | <p>**Nee**</p> <p>Gebruik System Center DPM om back-ups van virtuele machines in uw datacenter te maken.</p> | <p>Azure Backup-kluis</p> |

### Voordelen en beperkingen op onderdeelniveau

| Onderdeel | Voordelen | Beperkingen | Herstelgranulariteit |
| --- | --- | --- | --- |
| Azure Backup-agent (MARS) | <li>Kan een back-up van bestanden en mappen op een Windows-computer maken, zowel een fysieke als virtuele machine (VM's kunnen zich zowel on-premises als in Azure bevinden)<li>Geen afzonderlijk back-upserver vereist<li>Gebruikt Azure Backup-kluis, | <li>Drie keer per dag een dag back-up/herstel op bestandsniveau<li>Alleen herstelbewerkingen op bestands-/map-/volumeniveau, niet toepassingsbewust<li>Geen ondersteuning voor Linux | bestanden/mappen/volumes |
| System Center Data Protection Manager | <li>App-gerichte momentopnamen (VSS)<li>Volledige flexibiliteit met betrekking tot het moment waarop u back-ups wilt maken<li>Herstelgranulariteit (alles)<li>Kan de Azure Backup-kluis gebruiken<li>Linux-ondersteuning (indien gehost op Hyper-V) | <li>Een gebrek aan heterogene ondersteuning (back-up van een virtuele machine van VMware, back-up van Oracle-workload).  | bestanden/mappen/volumes<br>VM's/toepassingen |
| Microsoft Azure Backup-server | <li>App-gerichte momentopnamen (VSS)<li>Volledige flexibiliteit met betrekking tot het moment waarop u back-ups wilt maken<li>Herstelgranulariteit (alles)<li>Kan de Azure Backup-kluis gebruiken<li>Linux-ondersteuning (indien gehost op Hyper-V)<li>Vereist geen System Center-licentie | <li>Een gebrek aan heterogene ondersteuning (back-up van een virtuele machine van VMware, back-up van Oracle-workload).<li>Altijd een live Azure-abonnement nodig<li>Geen ondersteuning voor tape met back-up | bestanden/mappen/volumes<br>VM's/toepassingen |
| Back-up van virtuele machines van Azure IaaS | <li>Systeemeigen back-ups voor Windows/Linux<li>Er zijn geen specifieke agentinstallatie vereist<li>Back-ups op infrastructuurniveau zonder dat er een back-upinfrastructuur nodig is | <li>Eén back-up per dag/herstel op schijfniveau<li>Geen back-up on-premises | VM's<br>Alle schijven (met PowerShell) |

## Van welke toepassingen en workloads kan een back-up worden gemaakt?

| Workload | Broncomputer | Azure Backup-oplossing |
| --- | --- |---|
| Bestanden en mappen | Windows Server | <p>[Azure Backup-agent](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ de Azure Backup-agent),</p> <p>[Azure Backup-server](backup-azure-microsoft-azure-backup.md) (bevat de Azure Backup-agent)</p>  |
| Bestanden en mappen | Windows-client | <p>[Azure Backup-agent](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ de Azure Backup-agent),</p> <p>[Azure Backup-server](backup-azure-microsoft-azure-backup.md) (bevat de Azure Backup-agent)</p>  |
| Virtuele Hyper-V-machine (Windows) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ de Azure Backup-agent),</p> <p>[Azure Backup-server](backup-azure-microsoft-azure-backup.md) (bevat de Azure Backup-agent)</p> |
| Virtuele Hyper-V-machine (Linux) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ de Azure Backup-agent),</p> <p>[Azure Backup-server](backup-azure-microsoft-azure-backup.md) (bevat de Azure Backup-agent)</p>  |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ de Azure Backup-agent),</p> <p>[Azure Backup-server](backup-azure-microsoft-azure-backup.md) (bevat de Azure Backup-agent)</p>  |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ de Azure Backup-agent),</p> <p>[Azure Backup-server](backup-azure-microsoft-azure-backup.md) (bevat de Azure Backup-agent)</p>   |
| Microsoft Exchange |  Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ de Azure Backup-agent),</p> <p>[Azure Backup-server](backup-azure-microsoft-azure-backup.md) (bevat de Azure Backup-agent)</p>   |
| Virtuele machines van Azure IaaS (Windows) | - | [Azure Backup (VM-extensie)](backup-azure-vms-introduction.md) |
| Virtuele machines van Azure IaaS (Linux) | - | [Azure Backup (VM-extensie)](backup-azure-vms-introduction.md) |

## Ondersteuning voor ARM en Linux

| Onderdeel | ARM-ondersteuning | Ondersteuning voor Linux (goedgekeurd door Azure) |
| --- | --- | --- |
| Azure Backup-agent (MARS) | Ja | Nee (alleen Windows-agent) |
| System Center Data Protection Manager | Ja (agent in gast) | Alleen Hyper-V (geen Azure VM), alleen bestandsconsistente back-ups mogelijk |
| Azure Backup-server (MABS) | Ja (agent in gast) | Alleen Hyper-V (geen Azure VM), alleen bestandsconsistente back-ups mogelijk (hetzelfde als DPM) |
| Back-up van virtuele machines van Azure IaaS | Openbare preview | Openbare preview - Implementatiemodel met Linux VM's in Resource Manager <br>(consistentie op bestandssysteemniveau)<br><br>Ja voor Linux VM's in het klassieke implementatiemodel |

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]


## Back-ups van VM’s voor Premium-opslag maken en herstellen

De Azure Backup-service beschermt nu VM's voor Premium-opslag.

### Een back-up van VM's voor Premium-opslag maken

Tijdens een back-up van VM's voor Premium-opslag, maakt de Backup-service een tijdelijke faseringslocatie in het Premium-opslagaccount. De faseringslocatie, AzureBackup- genoemd, is gelijk aan de totale gegevensgrootte van de Premium-schijven die aan de VM zijn gekoppeld.

>[AZURE.NOTE] Wijzig of bewerk de faseringslocatie niet.

Zodra de back-uptaak is voltooid, wordt de faseringslocatie verwijderd. De opslagkosten die in rekening worden gebracht voor de faseringslocatie zijn consistent met de [Prijzen voor een Premium-opslag](../storage/storage-premium-storage.md#pricing-and-billing).

### VM's voor Premium-opslag herstellen

De gebruikelijke herstelprocedure voor een VM voor Premium-opslag is om het herstelpunt te herstellen naar Premium-opslag. Het kan echter rendabel zijn om een herstelpunt voor een VM voor Premium-opslag te herstellen naar de standaardopslag. Dit type herstel kan worden gebruikt als u een subset bestanden van de VM nodig hebt.

## Functionaliteit
In deze vijf tabellen kunt u zien hoe de back-functionaliteit in elk onderdeel wordt verwerkt.

### Storage

| Functie | Azure Backup-agent | System Center DPM | Azure Backup-server | Azure Backup (VM-extensie) |
| ------- | --- | --- | --- | ---- |
| Azure Backup-kluis | ![Ja][green] | ![Ja][green] | ![Ja][green] | ![Ja][green] |
| File Storage | | ![Ja][green] | ![Ja][green] |  |
| Bandgeheugen | | ![Ja][green] |  | |
| Compressie (in de Backup-kluis) | ![Ja][green] | ![Ja][green]| ![Ja][green] | |
| Incrementele back-up | ![Ja][green] | ![Ja][green] | ![Ja][green] | ![Ja][green] |
| Schijfontdubbeling | | ![Gedeeltelijk][yellow] | ![Gedeeltelijk][yellow]| | |

![tabelsleutel](./media/backup-introduction-to-azure-backup/table-key.png)

De Backup-kluis is de gewenste doelopslag voor alle onderdelen. System Center DPM en de Backup-server bieden ook de mogelijkheid voor een kopie op de lokale schijf. Alleen System Center DPM biedt de mogelijkheid om gegevens naar een opslagapparaat met een tape te schrijven.

#### Incrementele back-up
Elk onderdeel biedt ondersteuning voor incrementele back-ups, ongeacht de doelopslag (schijf, tape, back-upkluis). Een incrementele back-up zorgt ervoor dat back-ups opslag- en tijdsefficiënt zijn door alleen de wijzigingen sinds de laatste back-up over te dragen.

#### Compressie
Back-ups worden gecomprimeerd zodat er minder opslagruimte nodig is. Het enige onderdeel dat u geen compressie gebruikt, is de VM-extensie. Met VM-extensie worden alle back-upgegevens gekopieerd van het opslagaccount van de klant naar de back-upkluis in dezelfde regio, zonder dat de gegevens worden gecomprimeerd. Hoewel er iets meer opslagruimte wordt gebruikt als de gegevens niet worden gecomprimeerd, kunnen de gegevens sneller worden hersteld.

#### Ontdubbeling
Ontdubbeling wordt ondersteund voor System Center DPM en de Backup-server, mits [geïmplementeerd op een virtuele Hyper-V-machine](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). Ontdubbeling wordt uitgevoerd op hostniveau door gebruik te maken van Windows Server-ontdubbeling op virtuele harde schijven (VHD's) die als back-upopslag zijn gekoppeld aan de virtuele machine.

>[AZURE.WARNING] Ontdubbeling is niet beschikbaar in Azure voor de Backup-onderdelen. Wanneer de System Center DPM en de Bakckup-server zijn geïmplementeerd in Azure, kunnen de opslagschijven die aan de virtuele machine zijn gekoppeld, niet worden ontdubbeld.

### Beveiliging

| Functie | Azure Backup-agent | System Center DPM | Azure Backup-server | Azure Backup (VM-extensie) |
| ------- | --- | --- | --- | ---- |
| Netwerkbeveiliging (naar Azure) | ![Ja][green] |![Ja][green] | ![Ja][green] | ![Gedeeltelijk][yellow]|
| Netwerkbeveiliging (in Azure) | ![Ja][green] |![Ja][green] | ![Ja][green] | ![Gedeeltelijk][yellow]|

![tabelsleutel](./media/backup-introduction-to-azure-backup/table-key.png)

Alle back-upverkeer van uw servers naar de Backup-kluis wordt versleuteld met Advanced Encryption Standard 256. De gegevens worden verzonden via een beveiligde HTTPS-koppeling. De back-upgegevens worden ook versleuteld opgeslagen in de Backup-kluis. Alleen de klant beschikt over de wachtwoordzin waarmee deze gegevens kunnen worden ontgrendeld. De back-upgegevens kunnen niet door Microsoft ontsleuteld.

>[AZURE.WARNING] Alleen de klant beschikt over de sleutel die wordt gebruikt om de back-upgegevens te versleutelen. Microsoft bewaart geen kopie in Azure en heeft geen toegang tot de sleutel. Als de sleutel is verkeerd wordt geplaatst, kan Microsoft de back-upgegevens niet herstellen.

Als u back-ups van virtuele machines van Azure wilt maken, moet u de versleuteling configureren *in* de virtuele machine. Gebruik BitLocker voor virtuele machines van Windows en **dm-crypt** voor virtuele machines van Linux. De gegevens die via dit pad worden verzonden, worden niet automatisch door Azure Backup versleuteld.

### Ondersteunde workloads

| Functie | Azure Backup-agent | System Center DPM | Azure Backup-server | Azure Backup (VM-extensie) |
| ------- | --- | --- | --- | ---- |
| Windows Server-computer: bestanden en mappen | ![Ja][green] | ![Ja][green] | ![Ja][green] | |
| Windows-clientcomputer: bestanden en mappen | ![Ja][green] | ![Ja][green] | ![Ja][green] | |
| Virtuele Hyper-V-machine (Windows) | | ![Ja][green] | ![Ja][green] | |
| Virtuele Hyper-V-machine (Linux) | | ![Ja][green] | ![Ja][green] | |
| Microsoft SQL Server | | ![Ja][green] | ![Ja][green] | |
| Microsoft SharePoint | | ![Ja][green] | ![Ja][green] | |
| Microsoft Exchange  | | ![Ja][green] | ![Ja][green] | |
| Virtuele machine van Azure (Windows) | | | | ![Ja][green] |
| Virtuele machine van Azure (Linux) | | | | ![Ja][green] |

![tabelsleutel](./media/backup-introduction-to-azure-backup/table-key-2.png)

### Netwerk

| Functie | Azure Backup-agent | System Center DPM | Azure Backup-server | Azure Backup (VM-extensie) |
| ------- | --- | --- | --- | ---- |
| Netwerkcompressie (naar de back-upserver) | | ![Ja][green] | ![Ja][green] | |
| Netwerkcompressie (naar de back-upkluis) | ![Ja][green] | ![Ja][green] | ![Ja][green] | |
| Netwerkprotocol (naar de back-upserver) | | TCP | TCP | |
| Netwerkprotocol (naar de back-upkluis) | HTTPS | HTTPS | HTTPS | HTTPS |

![tabelsleutel](./media/backup-introduction-to-azure-backup/table-key-2.png)

Omdat de VM-extensie de gegevens rechtstreeks vanuit het Azure Storage-account via het opslagnetwerk leest, hoeft dit verkeer niet te worden geoptimaliseerd. Het verkeer verloopt via het lokale opslagnetwerk in het datacenter Azure, waardoor er met het oog op de bandbreedte weinig behoefte is aan compressie.

Als u een back-up van uw gegevens naar een back-upserver maakt (DPM of Backup Server), kan het verkeer van de primaire server naar de back-upserver worden gecomprimeerd om bandbreedte te besparen.

#### Netwerkbeperking
De Azure Backup-agent biedt beperkingsmogelijkheden, zodat u kunt bepalen hoe de netwerkbandbreedte tijdens de gegevensoverdracht wordt gebruikt. Beperking kan handig zijn wanneer u onder werktijd een back-up van gegeven moet maken, maar niet wilt dat het back-upproces het andere internetverkeer verstoort. De beperking voor de gegevensoverdracht is van toepassing op back-up- en herstelbewerkingen.

### Back-up en retentie

|  | Azure Backup-agent | System Center DPM | Azure Backup-server | Azure Backup (VM-extensie) |
| --- | --- | --- | --- | --- |
| Back-upfrequentie (naar de back-upkluis) | Drie back-ups per dag | Twee back-ups per dag |Twee back-ups per dag | Eén back-up per dag |
| Back-upfrequentie (naar schijf) | Niet van toepassing | <p>Om de 15 minuten voor SQL Server</p> <p>Elk uur voor andere workloads</p> | <p>Om de 15 minuten voor SQL Server</p> <p>Elk uur voor andere workloads</p> |Niet van toepassing |
| Bewaaropties | Dagelijks, wekelijks, maandelijks, jaarlijks | Dagelijks, wekelijks, maandelijks, jaarlijks | Dagelijks, wekelijks, maandelijks, jaarlijks |Dagelijks, wekelijks, maandelijks, jaarlijks |
| Bewaarperiode | Maximaal 99 jaar | Maximaal 99 jaar | Maximaal 99 jaar | Maximaal 99 jaar |
| Herstelpunten in Backup-kluis | Onbeperkt | Onbeperkt | Onbeperkt | Onbeperkt |
| Herstelpunten op lokale schijf | Niet van toepassing | 64 voor bestandsservers,<br><br>448 voor toepassingsservers | 64 voor bestandsservers,<br><br>448 voor toepassingsservers |Niet van toepassing |
| Herstelpunten op tape | Niet van toepassing | Onbeperkt | Niet van toepassing | Niet van toepassing |

## Wat is het kluisreferentiebestand?

Het kluisreferentiebestand is een certificaat dat de portal voor elke back-upkluis genereert. De portal uploadt de openbare sleutel vervolgens naar de Access Control Service (ACS). De gebruiker ontvangt de persoonlijke sleutel bij het downloaden van de referenties en moet vervolgens worden opgegeven tijdens de registratie van de machine. De persoonlijke sleutel verifieert de machine om de back-upgegevens naar een geïdentificeerde kluis in de Azure Backup-service te verzenden.

De kluisreferenties worden alleen gebruikt tijdens de registratiewerkstroom. Het is uw verantwoordelijkheid om ervoor te zorgen dat het kluisreferentiebestand veilig wordt bewaard. Als het kluisreferentiebestand in handen valt van een rogue-gebruiker, kan het bestand worden gebruikt om andere machines bij dezelfde kluis te registreren. Aangezien de back-upgegevens worden versleuteld met een wachtwoordzin waarover alleen de klant beschikt, kan er geen inbreuk op de beveiliging van de bestaande back-upgegevens worden gemaakt. De kluisreferenties verlopen na 48 uur om eventuele problemen te voorkomen. Hoewel u de kluisreferenties van een back-upkluis zo vaak kunt downloaden als u wilt, is alleen het meest recente bestand van toepassing tijdens de registratiewerkstroom.

## Wat is het verschil tussen Azure Backup en Azure Site Recovery?
Veel klanten verwarren het terugzetten van een back-up (backup recovery) met het herstel na een noodgeval (disaster recovery). In beide gevallen worden er gegevens vastgelegd en bieden ze een herstelsemantiek, maar in de kern zijn het twee afzonderlijke services.

Azure Backup maakt zowel on-premises als in de cloud back-ups van gegevens. Azure Site Recovery coördineert de replicatie, failover en failback van virtuele machines en fysieke servers. Beide services zijn belangrijk omdat uw noodhersteloplossing uw gegevens veilig en herstelbaar (Backup) *en* uw workloads in het geval van een storing beschikbaar moet houden (Site Recovery).

De volgende concepten helpen u belangrijke beslissingen te nemen met betrekking tot het herstellen van back-ups en herstel na een noodgeval.

| Concept | Details | Back-up | Herstel na noodgeval (DR; disaster recovery) |
| ------- | ------- | ------ | ----------------- |
| Recovery Point Objective (RPO) | Acceptabele hoeveelheid gegevens die verloren gaat wanneer een herstelbewerking moet worden uitgevoerd. | Back-oplossingen hebben grote variabiliteit met betrekking tot hun acceptabele RPO. Back-ups van virtuele machines hebben doorgaans een RPO van één dag, terwijl de RPO bij databaseback-up soms maar vijftien minuten is. | Oplossingen voor herstel na noodgevallen hebben lage RPO's. De DR-kopie ligt maar een paar seconden of paar minuten achter. |
| Beoogde hersteltijd (RTO) | De hoeveelheid tijd die nodig is om een terugzet- of herstelbewerking te voltooien. | Vanwege de grotere RPO is de hoeveelheid gegevens die een back-upoplossing moet verwerken doorgaans veel grote, wat resulteert in langere RTO's. Het kan bijvoorbeeld dagen duren om gegevens te herstellen vanaf tapes, afhankelijk van de tijd die nodig is voor het transport van de tape vanaf een externe locatie. | Noodhersteloplossingen hebben kleinere RTO's omdat ze beter zijn gesynchroniseerd met de bron. Er hoeven minder wijzigingen te worden verwerkt. |
| Bewaartermijn | Hoe lang gegevens moeten worden bewaard | Voor scenario's waarvoor een operationele herstelbewerking is vereist (beschadigde gegevens, onbedoeld verwijderen van bestanden, OS-fouten), moeten de back-upgegevens doorgaans 30 dagen of korter worden bewaard.<br>Vanuit het oogpunt van de naleving moeten gegevens maanden of zelfs jaren worden bewaard. In dergelijke gevallen kunnen de back-upgegevens het beste worden gearchiveerd. | Voor het herstel na een noodgeval zijn alleen operationele herstelgegevens nodig, die doorgaans een paar uur tot maximaal een dag vergen. Vanwege de verfijnde manier waarop de gegevens door noodhersteloplossingen worden vastgelegd, kunt u de gegevens voor noodherstel beter niet langdurig bewaren. |

## Volgende stappen

Probeer een eenvoudige Azure-back-up uit. Zie een van deze zelfstudies voor instructies:

- [Azure Backup proberen](backup-try-azure-backup-in-10-mins.md)
- [Azure VM Backup proberen](backup-azure-vms-first-look.md)

Omdat in deze zelfstudies wordt uitgelegd hoe u snel een back-up kunt maken, worden alleen de meest directe paden getoond om een back-up van uw gegevens te maken. Zie de volgende onderwerpen voor meer informatie over het type back-up dat u wilt uitvoeren:

- [Een back-up van een Windows-computer maken](backup-configure-vault.md)
- [Een back-up toepassingsworkloads maken](backup-azure-microsoft-azure-backup.md)
- [Virtuele machines van Azure IaaS](backup-azure-vms-prepare.md)



[groen]: ./media/backup-introduction-to-azure-backup/green.png
[geel]: ./media/backup-introduction-to-azure-backup/yellow.png
[rood]: ./media/backup-introduction-to-azure-backup/red.png



<!--HONumber=Jun16_HO2-->


