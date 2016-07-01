<properties
    pageTitle="Azure RemoteApp-licentieverlening | Microsoft Azure"
    description="Informatie over licentieverlening in Azure RemoteApp."
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
    ms.date="05/18/2016"
    ms.author="elizapo" />


# Hoe werkt licentieverlening in Azure RemoteApp?


U bent klaar met het instellen van uw Azure RemoteApp-service, u hebt sjablonen gemaakt en kunt apps voor uw gebruikers gaan publiceren. U hoeft nu nog maar een ding te regelen: de licentieverlening. Maar hoe werkt licentieverlening voor RemoteApp en de apps die u via RemoteApp deelt?

Voor RemoteApp zijn geen Windows-licenties of Extern bureaublad-licenties vereist. Dat is allemaal geregeld binnen uw abonnement. (Raadpleeg de informatie over [prijsstelling](https://azure.microsoft.com/pricing/details/remoteapp).)

Als u een van de installatiekopieën gebruikt die deel uitmaken van uw abonnement, kunt u alle apps die zijn geïnstalleerd op die installatiekopie delen zonder aparte licenties. Als u bijvoorbeeld de Windows Server 2012 R2-sjablooninstallatiekopie gebruikt voor het bouwen van uw verzameling, kunt u System Center Endpoint Protection delen met uw gebruikers. De enige uitzonderingen op deze regel zijn Office 365 ProPlus, waarvoor een apart abonnement is vereist, en Office 2013, dat niet in een productieverzameling kan worden gedeeld.

Als u de Office 365-sjablooninstallatiekopie die wordt geleverd met RemoteApp wilt gebruiken, moet u een *bestaand* Office 365 ProPlus-abonnement hebben. Hetzelfde geldt voor elke Office 365-app die u met een aangepaste sjabloon publiceert. U moet de apps met uw eigen abonnement activeren. Dit geldt voor proefabonnementen en betaalde abonnementen. Als u de Office 365-sjablooninstallatiekopie wilt gebruiken tijdens de evaluatieperiode *en u hebt nog geen abonnement*, gaat u naar de Office 365-pagina, waar u zich kunt [aanmelden](https://go.microsoft.com/fwlink/p/?LinkID=403802) voor een proefabonnement. Zie [How do RemoteApp and Office work together?](remoteapp-o365.md) (Hoe werken RemoteApp en Office samen?) voor meer informatie.

Als u tijdens de proefperiode geen proefabonnement op Office 365 wilt nemen, gebruikt u de Office 2013 Professional Plus-sjablooninstallatiekopie die wordt geleverd met RemoteApp. Deze installatiekopie kan slechts 30 dagen worden gebruikt en kan niet worden omgezet naar een betaalde verzameling.

Voor andere apps moet u ervoor zorgen dat u beschikt over de licentie om de app te delen.

Dat klinkt logisch, toch? U kunt elke app publiceren waarvoor u het wettelijk recht hebt om dat te doen. En u moet er zeker van zijn dat u het recht hebt om uw programma’s te delen.

Houd er rekening mee dat u geen CAL of volumelicentieovereenkomst kunt gebruiken in een cloudverzameling. U kunt *wel* een volumelicentieovereenkomst gebruiken om toepassingen in uw hybride verzameling te activeren (met uitzondering van Office). Daarvoor moet u ze alleen installeren op uw sjablooninstallatiekopie van het volumelicentiemedium. Lees de informatie van de leverancier van de toepassing voor het installeren van licenties in een omgeving met extern bureaublad.



<!--HONumber=Jun16_HO2-->


