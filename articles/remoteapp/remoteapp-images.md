<properties
    pageTitle="Wat bevatten de sjablooninstallatiekopieën van Azure RemoteApp? | Microsoft Azure"
    description="Meer informatie over de inhoud van de sjablooninstallatiekopieën die worden geleverd bij Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/05/2016"
    ms.author="elizapo" />

# Wat bevatten de sjablooninstallatiekopieën van Azure RemoteApp?

Uw abonnement op Azure RemoteApp omvat drie sjablooninstallatiekopieën:


- Windows Server 2012
- Microsoft Office 365 ProPlus (abonnement op Office 365 vereist)
- Microsoft Office 2013 Professional Plus (alleen proefversie)

> [AZURE.IMPORTANT]Uw Azure RemoteApp-abonnement biedt u toegang tot de software in de installatiekopieën, met uitzondering van Office 365 ProPlus, waarvoor een afzonderlijk abonnement is vereist, en Office 2013, dat niet kan worden gebruikt in productie. Dit betekent dat u de programma's of toepassingen op de sjablooninstallatiekopieën met uw gebruikers kunt delen. Als u bijvoorbeeld een verzameling maakt waarin de installatiekopie voor Windows Server 2012 R2 wordt gebruikt, kunt u System Center Endpoint Protection voor gebruikers publiceren zodat gebruikers hiertoe toegang hebben via RemoteApp.
>
> Bekijk de [Informatie over RemoteApp-licenties](remoteapp-licensing.md) voor meer informatie. En in [Office gebruiken met Azure RemoteApp](remoteapp-o365.md) kunt u informatie vinden over Office-licenties.

Lees verder voor meer informatie over de inhoud van elke installatiekopie.

## Windows Server 2012 R2 ('de vanilla-installatiekopie')
Deze installatiekopie is gebaseerd op het besturingssysteem Microsoft Windows Server 2012 R2 Datacenter en bevat de volgende rollen en functies om te voldoen aan de vereisten voor Azure RemoteApp-sjablooninstallatiekopieën:


- .NET Framework 4.5, 3.5.1, 3.5
- Bureaubladbelevenis
- Inkt- en handschriftservices
- Media Foundation
- Extern bureaublad-sessiehost
- Windows PowerShell 4.0
- Windows PowerShell ISE
- WoW64-ondersteuning

In deze installatiekopie zijn ook de volgende toepassingen geïnstalleerd:

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player


## Microsoft Office 365 ProPlus (abonnement vereist)
Office 365 is de meest aangevraagde toepassing en om deze reden hebben we een 'aangepaste' installatiekopie voor u gemaakt.

Deze installatiekopie is een uitbreiding op de vanilla-installatiekopie en bevat de volgende onderdelen van Microsoft Office 365 ProPlus, naast de onderdelen die worden beschreven in de installatiekopie van Windows Server 2012 R2:


- Access
- Excel
- Lync
- OneNote
- OneDrive voor Bedrijven
- Outlook
- PowerPoint
- Word
- Microsoft Office-taalprogramma's

De installatiekopie bevat ook Visio Pro en Project Pro.

Plus de volgende toepassingen:

- SQL Native Client
- ODBC-stuurprogramma
- SQL Server Data Mining Client
- MasterDataServices Client
- Microsoft Publisher
- PowerQuery
- PowerMap


Volledige functionaliteit van Office 365 ProPlus-apps is alleen beschikbaar voor gebruikers met een Office 365 ProPlus-abonnement. Zie [Service-abonnementen voor Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx) voor meer informatie over Office 365-abonnementen. Nog vragen? Bekijk de [Office 365 + RemoteApp](remoteapp-o365.md)-informatie. Lees ook het nieuwe artikel [Uw Office 365-abonnement gebruiken met Azure RemoteApp](remoteapp-officesubscription.md).

Houd er rekening mee dat u een aparte licentie nodig hebt voor Office 365 ProPlus Visio Pro en Project Pro (deze hebben elk een eigen licentie).

## Microsoft Office 2013 Professional Plus (alleen proefversie)
U kunt de service met de installatiekopie van het Office 2013 testen gedurende de gratis proefperiode.

Deze installatiekopie is een uitbreiding op de vanilla-installatiekopie en bevat de volgende onderdelen van Microsoft Office 2013 Professional Plus, naast de onderdelen die worden beschreven in de installatiekopie van Windows Server 2012 R2:


- Access
- Excel
- Lync
- OneNote
- OneDrive voor Bedrijven
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Microsoft Office-taalprogramma's

> [AZURE.IMPORTANT]**Juridische informatie:** Deze installatiekopie bevat geen licentie voor Microsoft Office en *kan niet worden gebruikt voor productie*. De Office 2013 Professional Plus-installatiekopie is alleen bedoeld als proefversie. Als u Office-apps in Azure RemoteApp wilt gebruiken voor productie, maak dan gebruik van de Office 365 ProPlus-installatiekopie. Zie [Office 365 gebruiken met Azure RemoteApp](remoteapp-o365.md) voor meer informatie over licentieverlening voor Office.



<!--HONumber=Jun16_HO2-->


