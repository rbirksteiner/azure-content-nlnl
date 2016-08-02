<properties
   pageTitle="Dezelfde Office 365-ervaring op elk apparaat met Azure RemoteApp | Microsoft Azure"
   description="Lees hoe u een Office 365-app deelt met uw gebruikers met behulp van Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="04/05/2016"
   ms.author="guscatal;elizapo"/>


# Dezelfde Office 365-ervaring op elk apparaat met Azure RemoteApp

In dit artikel wordt uitgelegd hoe u Office 365 implementeert op elk apparaat in uw bedrijf. Uw gebruikers beschikken op Android, Apple en Windows over dezelfde mogelijkheden en gebruikersinterface.

We kunnen dit met Azure RemoteApp bewerkstelligen door Office 365 te hosten op schaalbare virtuele machines in Azure, waarmee gebruikers verbinding kunnen maken. Deze set van virtuele machines noemen we een 'cloudverzameling'.

## Een cloudverzameling maken

Ga nadat u een Azure-account hebt gemaakt eerst naar **RemoteApp** door op de koppeling aan de linkerkant te klikken.
![Azure RemoteApp in Azure Portal](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Klik vervolgens onderaan op **Nieuw** en kies voor het 'snel maken' van een verzameling. Geef een naam, de regio, het abonnement, het plan en de 'Office Proffesional 2013'-installatiekopie die wij verstrekken op.
![Dialoogvenster Maken](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

Wanneer u het formulier hebt ingevuld, wordt het proces voor het maken van de verzameling gestart. Dit duurt ongeveer een uur.

![Wachten](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

Als dit proces is voltooid, ziet dit er ongeveer als volgt uit. Als u op **Publiceren** klikt, ziet u dat de meeste Office-toepassingen al zijn gepubliceerd.
![Verzameling gemaakt](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Gepubliceerde apps](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

Nu kunt u ook op **Gebruikerstoegang** klikken om meer gebruikers toe te voegen die toegang hebben tot deze verzameling.
![Gebruikerstoegang configureren](./media/remoteapp-tutorial-o365anywhere/6-user.png)

Nu gaan we proberen verbinding te maken met Office 365.

## Verbinding maken met Office 365

Ga naar [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), schuif naar beneden en klik op **Clients downloaden** om de Azure RemoteApp-client op uw apparaat te installeren. De onderstaande schermafbeeldingen zijn voor Windows.

Zodra de toepassing wordt gestart, wordt u gevraagd om u aan te melden met uw Microsoft-account (voorheen een 'Live-ID'). Gebruik nu uw Azure-account. Wanneer u bent aangemeld, ziet u een melding over nieuwe uitnodigingen. Klik op de melding, waarna een lijst zoals hieronder wordt weergegeven. Accepteer de uitnodiging die overeenkomt met het e-mailadres de eigenaar van uw Azure-account.

![Nieuwe uitnodiging](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

Zo ziet het eruit wanneer er nieuwe uitnodigingen zijn.

![Een toepassing accepteren](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Wanneer u de uitnodiging hebt geaccepteerd, worden alle Office-apps weergegeven in de Azure RemoteApp-client.

![Lijst met apps](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Wanneer u op een van deze toepassing klikt, wordt deze gestart op de virtuele machine van Azure en kunt u ermee aan de slag. Veel plezier!

![starten](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![PowerPoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)



<!--HONumber=Jun16_HO2-->


