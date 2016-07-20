<properties 
    pageTitle="Veelgestelde vragen over Azure RemoteApp | Microsoft Azure" 
    description="Lees de antwoorden op de meest gestelde vragen over Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="04/08/2016" 
    ms.author="elizapo"/>

# Veelgestelde vragen over Azure RemoteApp
De volgende vragen zijn gesteld over Azure RemoteApp. Hebt u nog andere vragen? Ga naar de [RemoteApp-forums](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) en laat ons weten wat u wilt weten of laat hieronder een opmerking achter.

## Wat is Azure RemoteApp? ##


- **Wat is Azure RemoteApp?** RemoteApp is een service waarmee u vanaf een groot aantal verschillende gebruikersapparaten veilige externe toegang kunt bieden tot toepassingen. Lees meer over [Azure RemoteApp](remoteapp-whatis.md).
- **Wat zijn de implementatie-opties?** Er zijn twee soorten Azure RemoteApp-verzamelingen: cloud en hybride. Welke u nodig hebt, is afhankelijk van een aantal factoren, zoals of lidmaatschap van een domein vereist is. Deze beslissingen komen [hier](remoteapp-collections.md) aan bod.

## Snelle tips voor het gebruik van Azure RemoteApp ##
- **Hoe lang duurt het tot ik geen verbinding meer heb? Hoe lang moet ik inactief zijn voordat ik eruit word gegooid?** 4 uur. Als u of een van uw gebruikers gedurende 4 uur niet actief is, wordt u automatisch afgemeld bij Azure RemoteApp. Bekijk de andere standaardinstellingen in [Limieten, quota’s en beperkingen voor Azure-abonnementen en services](../azure-subscription-service-limits.md).
- **Kan ik deze service gratis uitproberen?** Ja. Er is een gratis proefversie beschikbaar voor 30 dagen. Wanneer de proefperiode is afgelopen, kunt u overstappen op een betaald account (dat u in productie kunt gebruiken) of stoppen met de service. U start uw gratis proefversie door naar [portal.azure.com](http://portal.azure.com) te gaan en een nieuw exemplaar van RemoteApp te maken. Met de gratis proefversie kunt u 2 exemplaren van RemoteApp met 10 gebruikers per exemplaar instellen. Deze proefversie is echter maar 30 dagen geldig.
## Informatie over Azure RemoteApp-abonnement ##

- **Wat zijn de servicebeperkingen?** In [Limieten, quota’s en beperkingen voor Azure-abonnementen en services](../azure-subscription-service-limits.md) vindt u meer informatie over de standaardinstellingen en servicelimieten van Azure RemoteApp. Laat het ons weten als u meer vragen hebt.
- **Hoeveel gebruikers moet ik hebben?** Er is een minimum van 20 gebruikers. Ik herhaal: het MINIMUM is 20. U wordt gefactureerd voor 20 gebruikers. 
- **Wat kost RemoteApp?** Deze informatie vindt u in [Azure RemoteApp-prijsinformatie](https://azure.microsoft.com/pricing/details/remoteapp/).
- **Is het ene type verzameling duurder dan het andere?** Ja, dat is mogelijk, maar dat is afhankelijk van de vereisten van de verzameling. Een hybride verzameling vereist een verbinding tussen Azure RemoteApp en uw on-premises netwerk. Als u een bestaande VNET/Expresss Route gebruikt, zijn er geen extra kosten. Maar als u een nieuwe Azure VNET en een gateway of Express Route gebruikt, wordt u gefactureerd voor de [VPN-gateway](https://azure.microsoft.com/pricing/details/vpn-gateway) of [Express Route](../../../pricing/details/expressroute/). Deze kosten (gespecificeerd in de koppelingen) komen bij uw maandelijkse kosten voor Azure RemoteApp.

## Verzamelingen: wat er wordt ondersteund, welke u moet gebruiken en andere informatie
- **Worden aangepaste LOB-toepassingen (Line-of-business) ondersteund?** Ja. Als u een aangepaste toepassing in Azure RemoteApp wilt gebruiken, maakt u een [aangepaste sjablooninstallatiekopie](remoteapp-create-custom-image.md) en uploadt u deze naar de RemoteApp-verzameling.
- **Werkt mijn aangepaste LOB-toepassing in Azure RemoteApp?** De beste manier om dat te weten te komen, is door het uit te proberen. Raadpleeg het [RD Compatibility Center](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Welke implementatiemethode (cloud of hybride) is voor mijn organisatie het meest geschikt?** Hybride verzamelingen bieden de meest uitgebreide ervaring als u volledige integratie wilt met eenmalige aanmelding (SSO) en een veilige on-premises netwerkverbinding. Cloudverzamelingen bieden een flexibele en eenvoudige manier om een implementatie te isoleren met behulp van meerdere verificatiemethoden. Lees meer over de [implementatieopties](remoteapp-whatis.md).
- **We hebben een SQL- of andere database on-premises of in Azure. Welk implementatietype moeten we dan gebruiken?** Dat is afhankelijk van waar de SQL- of back-enddatabase zich bevindt. Als de database zich in een particulier netwerk bevindt, gebruik dan de hybride verzameling. Als de database wordt blootgesteld aan internet en clientverbindingen hiermee verbinding kunnen maken, kunt u de cloudverzameling gebruiken.
- **Hoe zit het met stationstoewijzing, USB- en seriële poorten, delen via het Klembord en printeromleiding?** Al deze functies worden ondersteund in Azure RemoteApp. Klembord delen en printeromleiding zijn standaard ingeschakeld. Meer informatie over omleiding vindt u [hier](remoteapp-redirection.md). 


## Sjablooninstallatiekopieën
- **Kan ik een virtuele cloudmachine of een bestaande virtuele machine als de sjabloon voor mijn RemoteApp-verzameling gebruiken?** Ja. U kunt een installatiekopie maken op basis van een virtuele machine van Azure, een van de installatiekopieën gebruiken die in uw abonnement zijn opgenomen, of een aangepaste installatiekopie maken. Bekijk de [Opties voor RemoteApp-installatiekopieën](remoteapp-imageoptions.md).


## Netwerkopties
- **De hybride verzameling vereist een VNET. Kunnen we ons bestaande VNET gebruiken?** Dat kan, als het bestaande VNET een Azure-VNET is. Zie 'Stap 1: Een virtueel netwerk instellen' in de [Instructies voor hybride verzamelingen](remoteapp-create-hybrid-deployment.md) voor meer informatie.
- **Kan ik een VNET gebruiken met een cloudverzameling?** Dat kunt u inderdaad. Lees [Een cloudverzameling maken](remoteapp-create-cloud-deployment.md), met name stap 1, voor meer informatie.

## Verificatieopties



- **Hoe zit het met verificatie? Welke methoden worden er ondersteund?** De cloudverzameling biedt ondersteuning voor Microsoft-accounts en Azure Active Directory-accounts, wat eveneens Office 365-accounts zijn. De hybride verzameling ondersteunt alleen Azure Active Directory-accounts die zijn gesynchroniseerd (met een hulpprogramma als [Azure Active Directory-synchronisatie](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) vanuit een Windows Server Active Directory-implementatie; en in het bijzonder met de optie Wachtwoordsynchronisatie of terwijl AD FS-federatie (Active Directory Federation Services) is geconfigureerd. U kunt ook [Multi-Factor Authentication (MFA)](https://azure.microsoft.com/services/multi-factor-authentication/) configureren.

>[AZURE.NOTE]De Azure Active Directory-gebruikers moeten afkomstig zijn van de tenant die aan uw abonnement is gekoppeld. (U kunt uw abonnement bekijken en wijzigen op het tabblad **Instellingen** in de portal. Zie [De Azure Active Directory-tenant wijzigen die wordt gebruikt door RemoteApp](remoteapp-changetenant.md) voor meer informatie.)

- **Waarom kan ik mijn Azure Active Directory-account geen toegang verlenen?** De Azure Active Directory-gebruikers dienen te zijn opgenomen in de directory die is gekoppeld aan uw abonnement. U kunt de directory bekijken of wijzigen op het tabblad Instellingen van de portal. Zie [De Azure Active Directory-tenant wijzigen die wordt gebruikt door RemoteApp](remoteapp-changetenant.md) voor meer informatie.

## Clients: vanaf welk apparaat heb ik toegang tot Azure RemoteApp?
U vindt goede clientinformatie, inclusief stappen voor het installeren van de verschillende clients in [Uw apps openen in Azure RemoteApp](remoteapp-clients.md).

- **Welke apparaten en besturingssystemen worden door de clienttoepassingen ondersteund?**
Eerst de computers en tablets: 
    - Windows 10 (client-preview)
    - Windows 8.1 en Windows 8
    - Windows 7 servicepack 1
    - Mac OS X
    - Windows RT
    - Android-tablets
    - iPads. Vervolgens, de telefoons:
    - iPhone
    - Android-telefoons
    - Windows Phone
 
    [Download](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) nu een RemoteApp-client.
- **Biedt Azure RemoteApp ondersteuning voor Thin Clients?** Ja, de volgende Windows Embedded thin clients worden ondersteund:
    - Windows Embedded Standard 7
    - Windows Embedded 8 Standard
    - Windows Embedded 8.1 Industry Pro
    - Windows 10 IoT Enterprise

- **Welke versie van Windows Server wordt ondersteund voor de Remote Desktop Session Host (RDSH)?** Windows Server 2012 R2.

##Ondersteuning en feedback


- **Wat is het ondersteuningsplan voor RemoteApp?** Ondersteuning voor factuur- en abonnementbeheer wordt gratis aangeboden. Technische ondersteuning is beschikbaar via de [Azure-serviceplannen](https://azure.microsoft.com/support/plans/). U kunt ook gratis communityondersteuning krijgen via ons [Azure-discussieforum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
- **Hoe geef ik feedback?** Daarvoor gaat u naar het [Feedbackforum](https://feedback.azure.com/forums/247748-azure-remoteapp/).
- **Waar kan ik meer vragen stellen als ik meer wil weten over Azure RemoteApp?** Naast ons [discussieforum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), dat een goede plaats is om vragen te stellen, kunt u deelnemen aan het wekelijkse webinar [Vraag het de expert](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), waar allerlei zaken over RemoteApp aan bod komen.
- **Is er ook RemoteApp-documentatie?** We zijn blij dat u dat vraagt. Naast de Help-inhoud in de Help-sectie van de portal (klik daarvoor op het **?** op een pagina in de portal), zijn de volgende artikelen beschikbaar waarin u alles kunt leren over RemoteApp:
    - **Aan de slag:**
        - [What is Azure RemoteApp? (Wat is Azure RemoteApp?)](remoteapp-whatis.md)
        - [What is in the RemoteApp template images? (Wat bevatten de sjablooninstallatiekopieën van RemoteApp?)](remoteapp-images.md)
        - [How does licensing work in Azure RemoteApp? (Hoe werkt licentieverlening?)](remoteapp-licensing.md)
        - [How do RemoteApp and Office work together? (Hoe werken RemoteApp en Office samen?)](remoteapp-o365.md)
        - [How does redirection work in RemoteApp](remoteapp-redirection.md)? (Hoe werkt omleiding in RemoteApp?)
    - **Implementatie:**
        - [Create a custom template image (Een aangepaste sjablooninstallatiekopie maken)](remoteapp-create-custom-image.md)
        - [Create a hybrid collection (Een hybride verzameling maken)](remoteapp-create-hybrid-deployment.md)
        - [Create a cloud collection (Een cloudverzameling maken)](remoteapp-create-cloud-deployment.md)
        - [Configure Azure Active Directory for RemoteApp (Azure Active Directory configureren voor RemoteApp)](remoteapp-ad.md)
        - [Publish an app in RemoteApp (Een app publiceren in RemoteApp)](remoteapp-publish.md)
    - **Beheren:**
        - [Add users (Gebruikers toevoegen)](remoteapp-user.md)
        - [Best practices for configuring and using RemoteApp (Aanbevolen procedures voor het configureren en gebruiken van RemoteApp)](remoteapp-bestpractices.md)  

    Video's We hebben ook een aantal video's over RemoteApp. Sommige geven inleidende informatie ([Introduction to Azure RemoteApp](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/) (Inleiding tot Azure RemoteApp) terwijl andere u helpen bij de implementatie ([Cloud implementation](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) (Implementatie in de cloud) en [Hybrid implementation](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be) (Hybride implementatie). Bekijk ze allemaal.

 
### Help ons u te helpen 
Wist u dat u niet alleen dit artikel kunt beoordelen en opmerkingen kunt toevoegen, maar zelfs wijzigingen kunt aanbrengen in het artikel zelf? Ontbreekt er iets? Is er iets niet juist? Heb ik iets geschreven dat niet duidelijk is? Blader omhoog en klik op **Edit on GitHub** als u wijzigingen wilt aanbrengen. Deze worden eerst nagekeken en wanneer ze zijn goedgekeurd, worden uw wijzigingen en verbeteringen hier weergegeven.



<!--HONumber=Jun16_HO2-->


