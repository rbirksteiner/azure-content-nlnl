<properties 
   pageTitle="Een StorSimple-apparaat implementeren (Update 2) | Microsoft Azure"
   description="Hier vindt u de stappen en best practices voor het implementeren van het StorSimple Update 2-apparaat en de bijbehorende service."
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
   ms.date="04/20/2016"
   ms.author="alkohli" />

# Een StorSimple-apparaat implementeren (Update 2)

> [AZURE.SELECTOR]
- [Update 2](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [Update 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [GA-release](../articles/storsimple/storsimple-deployment-walkthrough.md)

## Overzicht

Welkom bij de implementatie van Microsoft Azure StorSimple-apparaten. Deze zelfstudies voor implementatie zijn van toepassing op StorSimple 8000 Series Update 2. Deze reeks zelfstudies bevat een configuratiecontrolelijst, configuratievereisten en gedetailleerde configuratiestappen voor uw StorSimple-apparaat.

Bij de informatie in deze zelfstudies wordt ervan uitgegaan dat u de voorzorgsmaatregelen hebt gelezen en uw StorSimple-apparaat hebt uitgepakt, geplaatst en alle kabels hebt aangesloten. Als u dit nog moet doen, lees dan eerst de [voorzorgsmaatregelen](storsimple-safety.md). Volg de specifieke instructies van het apparaat om het apparaat uit te pakken, in het rek te plaatsen en alle kabels aan te sluiten.

- [De 8100 uitpakken, op het rek monteren en bekabelen](storsimple-8100-hardware-installation.md)
- [De 8600 uitpakken, op het rek monteren en bekabelen](storsimple-8600-hardware-installation.md)

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. U wordt geadviseerd om de configuratiecontrolelijst te raadplegen voordat u begint. Het implementatie- en configuratieproces kan enige tijd duren.

> [AZURE.NOTE] De StorSimple-implementatiegegevens die zijn gepubliceerd op de website van Microsoft Azure zijn alleen van toepassing op apparaten uit de StorSimple 8000-serie. Voor volledige informatie over apparaten uit de 7000-serie gaat u naar: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Raadpleeg de [Introductiehandleiding van het StorSimple-systeem](http://onlinehelp.storsimple.com/111_Appliance/) voor informatie over de implementatie van de 7000-serie.

## Implementatiestappen

Voer de volgende verplichte stappen uit om uw StorSimple-apparaat te configureren en te verbinden met uw StorSimple Manager-service. Naast de verplichte stappen zijn er nog optionele stappen en procedures die u tijdens de implementatie misschien nodig hebt. In de stapsgewijze implementatie-instructies wordt aangeven wanneer u deze optionele stappen moet uitvoeren.


| Stap                                                                                   | Beschrijving                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Vereisten**                                                                      | Deze moeten zijn worden voltooid ter voorbereiding van de implementatie.                                                                                        |
| [Configuratiecontrolelijst voor implementatie](#deployment-configuration-checklist)                                                     | Gebruik deze controlelijst om informatie te verzamelen en te registreren voorafgaand aan en tijdens de implementatie.                                                                       |
| [Vereisten voor implementatie](#deployment-prerequisites)                                                               | Hiermee wordt gecontroleerd of de omgeving gereed is voor implementatie.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **Stapsgewijze implementatie**                                                                   | Deze stappen zijn vereist om uw StorSimple-apparaat in productie te nemen.                                                                                      |
| [Stap 1: een nieuwe service maken](#step-1-create-a-new-service)                                                         | Stel cloudbeheer en -opslag voor uw StorSimple-apparaat in. *U kunt deze stap overslaan als u al een service voor andere StorSimple-apparaten hebt*.                |
| [Stap 2: de serviceregistratiesleutel ophalen](#step-2-get-the-service-registration-key)                                               | Gebruik deze sleutel om uw StorSimple-apparaat te registreren en te verbinden met de managementservice.                                                                         |
| [Stap 3: het apparaat configureren en registreren via Windows PowerShell voor StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)    | Verbind het apparaat met uw netwerk en registreer het bij Azure om de installatie met behulp van de managementservice te voltooien.                                            |
| [Stap 4: minimale apparaatconfiguratie voltooien](#step-4-complete-minimum-device-setupd)</br>[Optioneel: het StorSimple-apparaat bijwerken](#scan-for-and-apply-updates)      | Gebruik de managementservice om de installatie van het apparaat te voltooien en het in te schakelen voor opslag.                                                                      |
| [Stap 5: een volumecontainer maken](#step-5-create-a-volume-container)                                                      | Maak een container voor het inrichten van volumes. Een volumecontainer heeft opslagaccount, bandbreedte en versleutelingsinstellingen voor alle volumes in de container.    |
| [Stap 6: een volume maken](#step-6-create-a-volume)                                                                | Richt opslagvolume(s) op het StorSimple-apparaat in voor uw servers.                                                                                        |
| [Stap 7: een volume koppelen, initialiseren en formatteren](#step-7-mount-initialize-and-format-a-volume)</br>[Optioneel: MPIO configureren](storsimple-configure-mpio-windows-server.md)            | Verbind uw servers met de iSCSI-opslag die is geleverd door het apparaat. U kunt MPIO desgewenst configureren om ervoor te zorgen dat uw servers koppelings-, netwerk- en interfacefouten kunnen tolereren.                                                                                                                                                              |
| [Stap 8: een back-up maken](#step-8-take-a-backup)                                                                  | Stel een back-upbeleid in om uw gegevens te beschermen.                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **Overige procedures**                                                                   | Mogelijk moet u deze procedures raadplegen tijdens de implementatie van uw oplossing.                                                                                        |
| [Een nieuw opslagaccount voor de service configureren](#configure-a-new-storage-account-for-the-service)                                      |                                                                                                                                                               |
| [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](#use-putty-to-connect-to-the-device-serial-console)                                    |                                                                                                                                                               |
| [Het IQN ophalen van een Windows Server-host](#get-the-iqn-of-a-windows-server-host)                                                   |                                                                                                                                                               |
| [Een handmatige back-up maken](#create-a-manual-backup)                                                                 | 


## Configuratiecontrolelijst voor implementatie

Voordat u uw apparaat implementeert, moet u informatie verzamelen om de software op uw StorSimple-apparaat te configureren. De implementatie van het StorSimple-apparaat in uw omgeving verloopt gestroomlijnder wanneer u deze gegevens zoveel mogelijk op voorhand voorbereidt. Download en gebruik deze controlelijst om de configuratiegegevens te noteren terwijl u het apparaat implementeert.

- [Configuratiecontrolelijst voor implementatie van StorSimple downloaden](http://www.microsoft.com/download/details.aspx?id=49159)


## Vereisten voor implementatie

In de volgende secties worden de configuratievereisten voor de StorSimple Manager-service en het StorSimple-apparaat toegelicht.

### Voor de StorSimple Manager-service

Zorg voordat u begint voor het volgende:

- U hebt een Microsoft-account met toegangsreferenties.

- U hebt een Microsoft Azure Storage-account met toegangsreferenties.

- Uw Microsoft Azure-abonnement is ingeschakeld voor de StorSimple Manager-service. Uw abonnement moet zijn aangeschaft via de [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/).

- U hebt toegang tot terminalemulatiesoftware, zoals PuTTY.

### Voor het apparaat in het datacenter

Zorg voordat u het apparaat configureert, dat uw apparaat volledig is uitgepakt, is gemonteerd op het rek en volledig is bekabeld voor voeding, netwerk- en seriële toegang zoals is beschreven in:

-  [Unpack, rack mount, and cable your 8100 device (Een 8100-apparaat uitpakken, op het rek monteren en bekabelen)](storsimple-8100-hardware-installation.md)
-  [Unpack, rack mount, and cable your 8600 device (Een 8600-apparaat uitpakken, op het rek monteren en bekabelen)](storsimple-8600-hardware-installation.md)


### Voor het netwerk in het datacenter

Zorg voordat u begint voor het volgende:

- De poorten in de firewall van het netwerkcentrum zijn geopend om iSCSI- en cloud-verkeer toe te staan, zoals is beschreven in [Netwerkvereisten voor uw StorSimple-apparaat](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).


## Stapsgewijze implementatie

Gebruik de volgende stapsgewijze instructies om uw StorSimple-apparaat te implementeren in het datacenter.

## Stap 1: een nieuwe service maken

Met een StorSimple Manager-service kunt u meerdere StorSimple-apparaten beheren. Voer de volgende stappen uit om een nieuw exemplaar van de StorSimple Manager-service uit te voeren.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Als u de service niet hebt ingeschakeld om automatisch een opslagaccount te maken, moet u minimaal één opslagaccount maken nadat u een service hebt gemaakt. Dit opslagaccount wordt gebruikt bij het maken van een volumecontainer. 
>
> * Als u niet automatisch een opslagaccount hebt gemaakt, gaat u naar [Een nieuw opslagaccount voor de service configureren](#configure-a-new-storage-account-for-the-service) voor gedetailleerde instructies. 
> * Als u het automatisch maken van een opslagaccount hebt ingeschakeld, gaat u naar [Stap 2: de serviceregistratiesleutel ophalen](#step-2-get-the-service-registration-key).

## Stap 2: de serviceregistratiesleutel ophalen

Wanneer de StorSimple Manager-service bedrijfsklaar is, moet u de serviceregistratiesleutel ophalen. Deze sleutel wordt gebruikt om het StorSimple-apparaat te registreren en te verbinden met de service.

Voer de volgende stappen uit in de beheerportal.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## Stap 3: het apparaat configureren en registreren via Windows PowerShell voor StorSimple

Gebruik Windows PowerShell voor StorSimple om de eerste installatie van uw StorSimple-apparaat uit te voeren zoals wordt uitgelegd in de volgende procedure. U moet terminalemulatiesoftware gebruiken om deze stap uit te voeren. Zie [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](#use-putty-to-connect-to-the-device-serial-console) voor meer informatie.

[AZURE.INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## Stap 4: minimale apparaatconfiguratie voltooien

Voor de minimale apparaatconfiguratie van uw StorSimple-apparaat moet u het volgende doen: 

- De secundaire DNS-server instellen
- ISCSI inschakelen op ten minste één netwerkinterface
- Vaste IP-adressen toewijzen aan beide domeincontrollers

Voer de volgende stappen in de beheerportal uit om de minimale configuratie van het apparaat uit te voeren.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## Stap 5: een volumecontainer maken

Een volumecontainer heeft opslagaccount, bandbreedte en versleutelingsinstellingen voor alle volumes in de container. U moet een volumecontainer maken voordat u de volumes op uw StorSimple-apparaat kunt gaan inrichten. 

Voer de volgende stappen uit in de beheerportal om een volumecontainer te maken.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## Stap 6: een volume maken

Wanneer u een volumecontainer hebt gemaakt, kunt u een opslagvolume op het StorSimple-apparaat voor uw servers inrichten. Voer de volgende stappen uit in de beheerportal om een volume te maken.

> [AZURE.IMPORTANT] Met StorSimple Manager kunt u zowel thin als volledig ingerichte volumes maken. U kunt echter geen gedeeltelijk ingerichte volumes maken. 

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume-u2.md)]

## Stap 7: een volume koppelen, initialiseren en formatteren

De volgende stappen worden uitgevoerd op uw Windows Server-host. 


> [AZURE.IMPORTANT]

> - Voor hoge beschikbaarheid van uw StorSimple-oplossing wordt u aangeraden om de MPIO op de hostservers te configureren (optioneel) voordat u iSCSI configureert. MPIO-configuratie op hostservers zorgt ervoor dat de servers een koppelings-, netwerk- of interfacefout kunnen tolereren.

> - Voor MPIO- en iSCSI-installatie- en configuratie-instructies op een Windows Server-host gaat u naar [MPIO configureren voor uw StorSimple-apparaat](storsimple-configure-mpio-windows-server.md). Deze omvatten ook de stappen voor het koppelen, initialiseren en formatteren van StorSimple-volumes.

> - Voor MPIO- en iSCSI-installatie- en configuratie-instructies op een Linux-host gaat u naar [MPIO configureren voor uw StorSimple Linux-host](storsimple-configure-mpio-on-linux.md).

Als u besluit geen MPIO te configureren, voer dan de volgende stappen uit om uw StorSimple-volumes te koppelen, te initialiseren en te formatteren op een Windows Server-host.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## Stap 8: een back-up maken

Back-ups bieden tijdgebonden bescherming van volumes en verbeteren de herstelmogelijkheden met minimale hersteltijden. U kunt twee soorten back-ups uitvoeren op een StorSimple-apparaat: lokale momentopnamen en cloudmomentopnamen. Beide back-uptypen kunnen **gepland** of **handmatig** zijn. 

Voer de volgende stappen uit in de beheerportal om een geplande back-up te maken.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

U kunt op elk moment een handmatige back-up maken. Voor procedures gaat u naar [Een handmatige back-up maken](#create-a-manual-backup). 

## Een nieuw opslagaccount voor de service configureren

Dit is een optionele stap die u alleen hoeft uit te voeren als u het automatisch maken van een opslagaccount met uw service niet hebt ingeschakeld. U hebt een Microsoft Azure Storage-account nodig om een StorSimple-volumecontainer te maken.

Als u een Azure Storage-account in een andere regio wilt maken, raadpleeg dan [Over Azure Storage-accounts](../storage/storage-create-storage-account.md) voor stapsgewijze instructies.

Voer de volgende stappen uit op de pagina **StorSimple Manager-service** van de beheerportal.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat

U moet terminalemulatiesoftware, zoals PuTTY, gebruiken om verbinding te maken met Windows PowerShell voor StorSimple. U kunt PuTTY gebruiken wanneer u het apparaat rechtstreeks benadert via de seriële console of door een Telnet-sessie te openen vanaf een externe computer.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]


## Updates zoeken en toepassen

Het bijwerken van een apparaat kan enkele uren duren. Voer de volgende stappen uit als u wilt scannen op updates en deze wilt toepassen op uw apparaat.
<!--can take 1-4 hours--> 

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### Uw apparaat bijwerken

1.  Klik op de pagina **Snel starten** van het apparaat op **Apparaten**. Selecteer het fysieke apparaat, klik op **Onderhoud** en vervolgens op **Updates zoeken**.  

2.  Er wordt een taak gemaakt om te zoeken naar beschikbare updates. Als er updates beschikbaar zijn, verandert **Updates zoeken** in **Updates installeren**. Klik op **Updates installeren**. 

3.  Er wordt een updatetaak gemaakt. U bewaakt de status van de update door te navigeren naar **Taken**.

    > [AZURE.NOTE] Wanneer de update start, wordt onmiddellijk de status als 50 procent weergegeven. De status verandert alleen in 100 procent wanneer de update is voltooid. Er is geen realtime-status voor het updateproces.

4.  Wanneer het apparaat is bijgewerkt, schakelt u netwerkinterfaces Data 2 en Data 3 in als deze zijn uitgeschakeld.

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## Het IQN ophalen van een Windows Server-host

Voer de volgende stappen uit om de IQN (iSCSI Qualified Name) van een Windows-host te verkrijgen waarop Windows Server® 2012 wordt uitgevoerd.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## Een handmatige back-up maken

Voer de volgende stappen uit in de beheerportal als u voor één volume op het StorSimple-apparaat een handmatige back-up op aanvraag wilt maken.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]


## Volgende stappen

- Configureer een [virtueel apparaat](storsimple-virtual-device-u2.md).

- Gebruik de [StorSimple Manager-service](storsimple-manager-service-administration.md) om uw StorSimple-apparaat te beheren.
 



<!--HONumber=Jun16_HO2-->


