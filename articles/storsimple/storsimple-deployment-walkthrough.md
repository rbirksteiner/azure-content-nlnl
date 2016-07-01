<properties
   pageTitle="Een on-premises StorSimple-apparaat implementeren | Microsoft Azure"
   description="Hier vindt u de stappen en aanbevolen procedures voor het implementeren van het StorSimple-apparaat en de bijbehorende service. (Is van toepassing op Microsoft Azure StorSimple versie .3 en eerder.)"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/26/2016"
   ms.author="alkohli" />

# Een StorSimple-apparaat implementeren

> [AZURE.SELECTOR]
- [Update 2](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [Update 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [GA-release](../articles/storsimple/storsimple-deployment-walkthrough.md)

## Overzicht

Welkom bij de implementatie van Microsoft Azure StorSimple-apparaten. Deze zelfstudies zijn van toepassing op StorSimple 8000 Serie releaseversie, StorSimple 8000 Serie Update 0.1, StorSimple 8000 Serie Update 0.2 en StorSimple 8000 Serie Update 0.3. In deze reeks zelfstudies wordt beschreven hoe u een StorSimple-apparaat configureert en bevat een configuratiecontrolelijst, configuratievereisten en gedetailleerde configuratiestappen.


Bij de informatie in deze zelfstudies wordt ervan uitgegaan dat u de voorzorgsmaatregelen hebt gelezen en uw StorSimple-apparaat hebt uitgepakt, geplaatst en alle kabels hebt aangesloten. Als u dit nog moet doen, lees dan eerst de [voorzorgsmaatregelen](storsimple-safety.md). Afhankelijk van het model kunt u het apparaat vervolgens uitpakken, op het rek monteren en bekabelen door de instructies te volgen in:

- [De 8100 uitpakken, op het rek monteren en bekabelen](storsimple-8100-hardware-installation.md)
- [De 8600 uitpakken, op het rek monteren en bekabelen](storsimple-8600-hardware-installation.md)

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. U wordt geadviseerd om de configuratiecontrolelijst te raadplegen voordat u begint. Het implementatie- en configuratieproces kan enige tijd duren.

> [AZURE.NOTE] De StorSimple-implementatiegegevens die zijn gepubliceerd op de website van Microsoft Azure zijn alleen van toepassing op apparaten uit de StorSimple 8000-serie. Voor volledige informatie over apparaten uit de 5000- en 7000-serie gaat u naar: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Raadpleeg de [Introductiehandleiding van het StorSimple-systeem ](http://onlinehelp.storsimple.com/111_Appliance/) voor informatie over de implementatie van de 5000- en 7000-serie.

## Implementatiestappen

Voer de volgende verplichte stappen uit om uw StorSimple-apparaat te configureren en te verbinden met uw StorSimple Manager-service. Naast de verplichte stappen zijn er nog optionele stappen en procedures die u tijdens de implementatie misschien nodig hebt. In de stapsgewijze implementatie-instructies wordt aangeven wanneer u deze optionele stappen moet uitvoeren.


| Stap                                                                                   | Beschrijving                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Vereisten**                                                                      | Deze moeten zijn worden voltooid ter voorbereiding van de implementatie.                                                                                        |
| Configuratiecontrolelijst voor implementatie.                                                     | Gebruik deze controlelijst om informatie te verzamelen en te registreren voorafgaand aan en tijdens de implementatie.                                                                       |
| Vereisten voor implementatie.                                                               | Hiermee wordt gecontroleerd of de omgeving gereed is voor implementatie.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **Stapsgewijze implementatie**                                                                   | Deze stappen zijn vereist om uw StorSimple-apparaat in productie te nemen.                                                                                      |
| Stap 1: een nieuwe service maken.                                                         | Stel cloudbeheer en -opslag voor uw StorSimple-apparaat in. U kunt deze stap overslaan als u al een service voor andere StorSimple-apparaten hebt.                |
| Stap 2: de serviceregistratiesleutel ophalen.                                               | Gebruik deze sleutel om uw StorSimple-apparaat te registreren en te verbinden met de managementservice.                                                                         |
| Stap 3: het apparaat configureren en registreren via Windows PowerShell voor StorSimple.    | Verbind het apparaat met uw netwerk en registreer het bij Azure om de installatie met behulp van de managementservice te voltooien.                                            |
| Stap 4: minimale apparaatconfiguratie voltooien</br>Optioneel: het StorSimple-apparaat bijwerken.      | Gebruik de managementservice om de installatie van het apparaat te voltooien en het in te schakelen voor opslag.                                                                      |
| Stap 5: een volumecontainer maken.                                                      | Maak een container voor het inrichten van volumes. Een volumecontainer heeft opslagaccount, bandbreedte en versleutelingsinstellingen voor alle volumes in de container.    |
| Stap 6: een volume maken.                                                                | Richt opslagvolume(s) op het StorSimple-apparaat in voor uw servers.                                                                                        |
| Stap 7: een volume koppelen, initialiseren en formatteren.</br>Optioneel: MPIO configureren.            | Verbind uw servers met de iSCSI-opslag die is geleverd door het apparaat. U kunt MPIO desgewenst configureren om ervoor te zorgen dat uw servers koppelings-, netwerk- en interfacefouten kunnen tolereren.                                                                                                                                                              |
| Stap 8: een back-up maken.                                                                  | Stel een back-upbeleid in om uw gegevens te beschermen.                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **Overige procedures**                                                                   | Mogelijk moet u deze procedures raadplegen tijdens de implementatie van uw oplossing.                                                                                        |
| Een nieuw opslagaccount voor de service configureren.                                      |                                                                                                                                                               |
| PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat.                                    |                                                                                                                                                               |
| Het IQN ophalen van een Windows Server-host.                                                   |                                                                                                                                                               |
| Een handmatige back-up maken.                                                                 |


## Configuratiecontrolelijst voor implementatie

In de volgende controlelijst voor de implementatie-configuratie wordt beschreven welke informatie u moet verzamelen voordat en terwijl u de software op uw StorSimple-apparaat configureert. De implementatie van het StorSimple-apparaat in uw omgeving verloopt gestroomlijnder wanneer u deze gegevens zoveel mogelijk op voorhand voorbereidt. Gebruik deze controlelijst eveneens om de configuratiegegevens te noteren terwijl u het apparaat implementeert.

| Fase                                  | Parameter                                         | Details                                                                                                                                                                | Waarden |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **Uw apparaat bekabelen**                      | Seriële toegang                                     | Eerste apparaatconfiguratie                                                                  | Ja/Nee |
|   |   |  |  |
| **Apparaat configureren en registreren**          | Netwerkinstellingen Data 0                           | IP-adres Data 0:</br>Subnetmasker:</br>Gateway:</br>Primaire DNS-server:</br>Primaire NTP-server:</br> IP/FQDN van webproxyserver (optioneel):</br>Webproxypoort:|        |
|                                        | Wachtwoord apparaatbeheerder                     | Wachtwoord moet tussen 8 en 15 tekens lang zijn en kleine letters, hoofdletters, numerieke en speciale tekens bevatten. |        |
|                                        | Wachtwoord StorSimple Snapshot Manager              | Wachtwoord moet 14 of 15 tekens lang zijn en kleine letters, hoofdletters, numerieke en speciale tekens bevatten.|        |
|                                        | Serviceregistratiesleutel                          | Deze sleutel wordt gegenereerd via de klassieke Azure-portal.    |        |
|                                        | Gegevensversleutelingssleutel van service                       | Deze sleutel wordt gemaakt wanneer het apparaat via de Windows PowerShell voor StorSimple wordt geregistreerd bij de managementservice. Kopieer deze sleutel en bewaar deze op een veilige plaats.|  |
|   |   |  |  |
| **Minimale apparaatconfiguratie voltooien**          | Beschrijvende naam voor uw apparaat                     | Dit is een beschrijvende naam voor het apparaat. |        |
|                                        | Tijdzone                                          | Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.  |        |
|                                        | Secundaire DNS-server                              | Dit is een vereiste configuratie.                                  |        |
|                                        | Netwerkinterface: Vaste IP's Data 0-controller                                     | Deze IP moet routeerbaar zijn naar internet.</br>Vaste IP-adres Controller 0:</br>Vaste IP-adres Controller 1:|
|   |   |  |  |
| **Aanvullende netwerkinterface-instellingen**  | Netwerkinterface: Data 1</br>Als iSCSI is ingeschakeld, hoeft u de gateway niet te configureren.      | Doel: Cloud/iSCSI/Niet gebruikt</br>IP-adres:</br>Subnetmasker:</br>Gateway:|
|                                        | Netwerkinterface: Data 2</br>Als iSCSI is ingeschakeld, hoeft u de gateway niet te configureren.      | Doel: Cloud/iSCSI/Niet gebruikt</br>IP-adres:</br>Subnetmasker:</br>Gateway:|
|                                        | Netwerkinterface: Data 3</br>Als iSCSI is ingeschakeld, hoeft u de gateway niet te configureren.      | Doel: Cloud/iSCSI/Niet gebruikt</br>IP-adres:</br>Subnetmasker:</br>Gateway:|
|                                        | Netwerkinterface: Data 4</br>Als iSCSI is ingeschakeld, hoeft u de gateway niet te configureren.      | Doel: Cloud/iSCSI/Niet gebruikt</br>IP-adres:</br>Subnetmasker:</br>Gateway:|
|                                        | Netwerkinterface: Data 5</br>Als iSCSI is ingeschakeld, hoeft u de gateway niet te configureren.      | Doel: Cloud/iSCSI/Niet gebruikt</br>IP-adres:</br>Subnetmasker:</br>Gateway:|
|   |   |  |  |
| ** Een volumecontainer maken**                      | Naam volumecontainer                            | Naam van de container                                                                                                                                                 |        |
|                                        | Azure Storage-account:                            | Opslagnaam en toegangssleutel die aan deze volumecontainer moet worden gekoppeld                                                                                              |        |
|                                        | Coderingssleutel voor cloudopslag:                     | Versleutelingssleutel voor opslag in elke container                                                                                                                           |        |
|   |   |  |  |
| ** Een volume maken**                        | Details voor elk volume                           | Volumenaam:                                                                                                                                                           |        |
|                                        |                                                   | Grootte:                                                                                                                                                                  |        |
|                                        |                                                   | Gebruikstype:                                                                                                                                                            |        |
|                                        |                                                   | ACR-naam:                                                                                                                                                              |        |
|                                        |                                                   | Standaardbeleid voor back-up:                                                                                                                                                 |        |
|   |   |  |  |
| **Een volume koppelen, initialiseren en formatteren** | Details voor elke hostserver die verbinding maakt met de opslag | Naam Windows Server:                                                                                                                                                   |        |
|                                        |                                                   | IQN Windows Server:                                                                                                                                                    |        |
|                                        |                                                   | Naam Windows Server-volume:                                                                                                                                                   |        |
|                                        |                                                   | NTFS-koppelpunt/-stationsletter:                                                                                                                                      |        |

## Vereisten voor implementatie

In de volgende secties worden de configuratievereisten voor de StorSimple Manager-service, het StorSimple-apparaat en het netwerk in het datacenter toegelicht.

### Voor de StorSimple Manager-service

Zorg voordat u begint voor het volgende:

- U hebt een Microsoft-account met toegangsreferenties.

- U hebt een Microsoft Azure Storage-account met toegangsreferenties.

- Uw Microsoft Azure-abonnement is ingeschakeld voor de StorSimple Manager-service. Uw abonnement moet zijn aangeschaft via de [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/).

- U hebt toegang tot terminalemulatiesoftware, zoals PuTTY.

### Voor het apparaat in het datacenter

Zorg voordat u het apparaat configureert voor het volgende:

- Het apparaat is volledig uitgepakt, gemonteerd op het rek en volledig bekabeld voor voeding, netwerk- en seriële toegang zoals is beschreven in:

    -  [Unpack, rack mount, and cable your 8100 device (Een 8100-apparaat uitpakken, op het rek monteren en bekabelen)](storsimple-8100-hardware-installation.md)
    -  [Unpack, rack mount, and cable your 8600 device (Een 8600-apparaat uitpakken, op het rek monteren en bekabelen)](storsimple-8600-hardware-installation.md)


### Voor het netwerk in het datacenter

Zorg voordat u begint voor het volgende:

- De poorten in de firewall van het netwerkcentrum zijn geopend om iSCSI- en cloud-verkeer toe te staan, zoals is beschreven in [Netwerkvereisten voor uw StorSimple-apparaat](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
- Het apparaat in het datacenter kan verbinding maken met een extern netwerk. Voer de volgende [Windows PowerShell 4.0](http://www.microsoft.com/download/details.aspx?id=40855)-cmdlets (zie tabel hieronder) uit om de verbinding met het externe netwerk te valideren. Voer deze validatie uit op een computer (in datacenternetwerk) die verbinding heeft met Azure en waarop u het StorSimple-apparaat gaat implementeren.  

| Voor deze parameter...       | Om de geldigheid te controleren...                                                                                                                                                                                | Voert u deze opdrachten/cmdlets uit.                                                                                                                                                             |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **IP**</br>**Subnet**</br>**Gateway** | Is dit een geldig IPv4- of IPv6-adres?</br>Is dit een geldig subnetmasker?</br>Is dit een geldige gateway?</br>Is dit een dubbele IP in het netwerk?                                                                          | `ping ip`</br>`arp -a`</br>De opdrachten `ping` en `arp` mislukken wanneer er geen apparaat in het datacenternetwerk is dat deze IP gebruikt.
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **DNS**                       | Is dit een geldige DNS die Azure-URL's kan oplossen?                                                                                                                    | `Resolve-DnsName -Name www.bing.com -Server <DNS server IP address>` </br>Een andere opdracht die kan worden gebruikt, is:</br>`nslookup --dns-ip=<DNS server IP address> www.bing.com`      |
|                           | Controleer of poort 53 is geopend. Dit is alleen van toepassing als u een externe DNS-server voor uw apparaat gebruikt. Interne DNS moet automatisch de externe URL's oplossen.  | `Test-Port -comp dc1 -port 53 -udp -UDPtimeout 10000`  </br>[Meer informatie over deze cmdlet](http://learn-powershell.net/2011/02/21/querying-udp-ports-with-powershell/)|
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **NTP**                       | Er wordt een tijdsynchronisatie geactiveerd zodra NTP-server wordt ingevoerd. Controleer of UDP-poort 123 is geopend wanneer u `time.windows.com` of openbare tijdservers invoert). | [Download en gebruik dit script](https://gallery.technet.microsoft.com/scriptcenter/Get-Network-NTP-Time-with-07b216ca).                                                                                                                                                           |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **Proxy (optioneel)**          | Is dit een geldige proxy-URI en poort? </br> Is de verificatiemodus correct?                                                                                                                                | <code>wget http://bing.com &#124; % {$_.StatusCode}</code></br>Deze opdracht moet onmiddellijk na de configuratie van de webproxy worden uitgevoerd. Als een statuscode 200 wordt geretourneerd, betekent dit dat de verbinding is geslaagd.                                                                                                                                 |
|                           | Kan verkeer worden gerouteerd via proxy?                                                                                                                                 | Voer de DNS-validatie, NTP-controle of HTTP-controle eenmaal na configuratie van proxy op uw apparaat uit. U krijgt zo een helder beeld als verkeer wordt geblokkeerd bij proxy of elders.                                                                                                                      |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **Registratie**              | Controleer of uitgaande TCP-poort 443, 80, 9354 zijn geopend.                                                                                                                |  `Test-NetConnection -Port   443 -InformationLevel Detailed`</br>[Meer informatie voor cmdlet Test-NetConnection](https://technet.microsoft.com/library/dn372891.aspx)                                                                           |

## Stapsgewijze implementatie

Gebruik de volgende stapsgewijze instructies om uw StorSimple-apparaat te implementeren in het datacenter.

## Stap 1: een nieuwe service maken

Met een StorSimple Manager-service kunt u meerdere StorSimple-apparaten beheren. Voor de implementatie van uw eerste StorSimple-apparaat moet u een nieuwe StorSimple Manager-service maken.

> [AZURE.IMPORTANT] Sla deze stap over als u al een StorSimple Manager-service hebt en u uw StorSimple-apparaat met die service wilt implementeren.

Voer de volgende stappen uit om een nieuw exemplaar van de StorSimple Manager-service uit te voeren.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Als u de service niet hebt ingeschakeld om automatisch een opslagaccount te maken, moet u minimaal één opslagaccount maken nadat u een service hebt gemaakt. Dit opslagaccount wordt gebruikt bij het maken van een volumecontainer.
>
> Als u niet automatisch een opslagaccount hebt gemaakt, gaat u naar [Een nieuw opslagaccount voor de service configureren](#configure-a-new-storage-account-for-the-service) voor gedetailleerde instructies.
> Als u het automatisch maken van een opslagaccount hebt ingeschakeld, gaat u naar [Stap 2: de serviceregistratiesleutel ophalen](#step-2:-get-the-service-registration-key).

## Stap 2: de serviceregistratiesleutel ophalen

Wanneer de StorSimple Manager-service bedrijfsklaar is, moet u de serviceregistratiesleutel ophalen. Deze sleutel wordt gebruikt om het StorSimple-apparaat te registreren en te verbinden met de service.

Voer de volgende stappen uit in de klassieke Azure-portal.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## Stap 3: het apparaat configureren en registreren via Windows PowerShell voor StorSimple

> [AZURE.IMPORTANT] Koppel alle netwerkinterfaces, behalve Data 0, los op beide (actieve en passieve) controllers voordat u deze configuratie uitvoert.

Gebruik Windows PowerShell voor StorSimple om de eerste installatie van uw StorSimple-apparaat uit te voeren zoals wordt uitgelegd in de volgende procedure. U moet terminalemulatiesoftware gebruiken om deze stap uit te voeren. Zie [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](#use-putty-to-connect-to-the-device-serial-console) voor meer informatie.

[AZURE.INCLUDE [storsimple-configure-and-register-device](../../includes/storsimple-configure-and-register-device.md)]

## Stap 4: minimale apparaatconfiguratie voltooien

Voor de minimale apparaatconfiguratie van uw StorSimple-apparaat moet u het volgende doen:

- De secundaire DNS-server instellen
- ISCSI inschakelen op ten minste één netwerkinterface
- Vaste IP-adressen toewijzen aan beide domeincontrollers

Voer de volgende stappen in de klassieke Azure-portal uit om de minimale configuratie van het apparaat uit te voeren.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup.md)]

Wanneer de configuratie van het apparaat is voltooid, moet u scannen op updates en alle gevonden updates installeren. De uitvoering van updates kan enkele uren in beslag nemen. Volg de instructies in [Updates zoeken en toepassen](#scan-for-and-apply-updates).


## Stap 5: een volumecontainer maken

Een volumecontainer heeft opslagaccount, bandbreedte en versleutelingsinstellingen voor alle volumes in de container. U moet een volumecontainer maken voordat u de volumes op uw StorSimple-apparaat kunt gaan inrichten.

Voer de volgende stappen uit in de klassieke Azure-portal om een volumecontainer te maken.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## Stap 6: een volume maken

Wanneer u een volumecontainer hebt gemaakt, kunt u een opslagvolume op het StorSimple-apparaat voor uw servers inrichten. Voer de volgende stappen uit in de klassieke Azure-portal om een volume te maken.

> [AZURE.IMPORTANT] Met StorSimple Manager kunt u alleen thin ingerichte volumes maken.  U kunt geen volledig of gedeeltelijk ingerichte volumes maken.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## Stap 7: een volume koppelen, initialiseren en formatteren

> [AZURE.IMPORTANT]

> - Voor hoge beschikbaarheid van uw StorSimple-oplossing wordt u aangeraden om de MPIO op de Windows Server-host te configureren (optioneel) voordat u iSCSI op uw Windows Server-host configureert. MPIO-configuratie op hostservers zorgt ervoor dat de servers een koppelings-, netwerk- of interfacefout kunnen tolereren.

> - Voor MPIO- en iSCSI-installatie- en configuratie-instructies gaat u naar [MPIO configureren voor uw StorSimple-apparaat](storsimple-configure-mpio-windows-server.md). Deze omvatten ook de stappen voor het koppelen, initialiseren en formatteren van StorSimple-volumes.

Als u besluit geen MPIO te configureren, voer dan de volgende stappen uit om uw StorSimple-volumes te koppelen, te initialiseren en te formatteren.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## Stap 8: een back-up maken

Back-ups bieden tijdgebonden bescherming van volumes en verbeteren de herstelmogelijkheden met minimale hersteltijden. U kunt twee soorten back-ups uitvoeren op een StorSimple-apparaat: lokale momentopnamen en cloudmomentopnamen. Beide back-uptypen kunnen **gepland** of **handmatig** zijn.

Voer de volgende stappen uit in de klassieke Azure-portal om een geplande back-up te maken.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

U kunt op elk moment een handmatige back-up maken. Voor procedures gaat u naar [Een handmatige back-up maken](#Create-a-manual-backup).

## Een nieuw opslagaccount voor de service configureren

Dit is een optionele stap die u alleen hoeft uit te voeren als u het automatisch maken van een opslagaccount met uw service niet hebt ingeschakeld. U hebt een Microsoft Azure Storage-account nodig om een StorSimple-volumecontainer te maken.

Als u een Azure Storage-account in een andere regio wilt maken, raadpleeg dan [Over Azure Storage-accounts](../storage/storage-create-storage-account.md) voor stapsgewijze instructies.

Voer de volgende stappen uit op de pagina **StorSimple Manager-service** van de klassieke Azure-portal.

[AZURE.INCLUDE [storsimple-configure-new-storage-account](../../includes/storsimple-configure-new-storage-account.md)]


## PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat

U moet terminalemulatiesoftware, zoals PuTTY, gebruiken om verbinding te maken met Windows PowerShell voor StorSimple. U kunt PuTTY gebruiken wanneer u het apparaat rechtstreeks benadert via de seriële console of door een Telnet-sessie te openen vanaf een externe computer.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## Updates zoeken en toepassen

Het bijwerken van een apparaat kan 1 tot 4 uur duren. Voer de volgende stappen uit als u wilt scannen op updates en deze wilt toepassen op uw apparaat.

> [AZURE.NOTE] Als er een gateway is geconfigureerd op een andere netwerkinterface dan Data 0, moet u Data 2- en Data 3-netwerkinterfaces uitschakelen voordat u de update installeert. Ga naar **Apparaten > Configureren** en schakel Data 2- en Data 3-interfaces uit. U moet deze interfaces weer inschakelen wanneer het apparaat is bijgewerkt.

#### Uw apparaat bijwerken
1.  Klik op de pagina **Snel starten** van het apparaat op **Apparaten**. Selecteer het fysieke apparaat, klik op **Onderhoud** en vervolgens op **Updates zoeken**.  
2.  Er wordt een taak gemaakt om te zoeken naar beschikbare updates. Als er updates beschikbaar zijn, verandert **Updates zoeken** in **Updates installeren**. Klik op **Updates installeren**. Mogelijk wordt u gevraagd Data 2 en Data 3 uit te schakelen voordat u de updates installeert. U moet deze netwerkinterfaces uitschakelen, anders worden de updates niet uitgevoerd.
3.  Er wordt een updatetaak gemaakt. U bewaakt de status van de update door te navigeren naar **Taken**.

    > [AZURE.NOTE] Wanneer de update start, wordt onmiddellijk de status als 50 procent weergegeven. De status verandert vervolgens in 100 procent wanneer de update is voltooid. Er is geen realtime-status voor het updateproces.

4.  Wanneer het apparaat is bijgewerkt, schakelt u netwerkinterfaces Data 2 en Data 3 in als deze zijn uitgeschakeld.



## Het IQN ophalen van een Windows Server-host

Voer de volgende stappen uit om de IQN (iSCSI Qualified Name) van een Windows-host te verkrijgen waarop Windows Server 2012 wordt uitgevoerd.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]


## Een handmatige back-up maken

Voer de volgende stappen uit in de klassieke Azure-portal als u voor één volume op het StorSimple-apparaat een handmatige back-up op aanvraag wilt maken.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]


## Volgende stappen

- Configureer een [virtueel apparaat](storsimple-virtual-device-u2.md).

- Gebruik de [StorSimple Manager-service](https://msdn.microsoft.com/library/azure/dn772396.aspx) om uw StorSimple-apparaat te beheren.



<!--HONumber=Jun16_HO2-->


