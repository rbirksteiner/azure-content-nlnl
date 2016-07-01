<properties
    pageTitle="Uw on-premises infrastructuur voor identiteiten in de cloud controleren."
    description="Dit is de Azure AD Connect Health-pagina waar wordt beschreven wat dit is en waarvoor u het kunt gebruiken."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="stevenpo"
    editor="karavar"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/21/2016"
    ms.author="vakarand"/>

# Uw on-premises infrastructuur voor identiteiten en synchronisatieservices in de cloud controleren

Azure AD Connect Health helpt u om inzicht te verkrijgen in uw on-premises infrastructuur voor identiteiten en de synchronisatieservices.  Hiermee kunt u een betrouwbare verbinding met Office 365 en Microsoft Online Services onderhouden door controlemogelijkheden te bieden voor uw belangrijkste identiteitsonderdelen, zoals AD FS-servers, Azure AD Connect-servers (ook bekend Sync Engine), Active Directory Domain Controllers enzovoort. Ook worden de belangrijkste gegevenspunten over deze componenten gemakkelijk toegankelijk gemaakt, zodat het gebruik en het verkrijgen van belangrijke informatie gemakkelijker worden.

Deze informatie vindt u in de [portal voor Azure AD Connect Health](https://aka.ms/aadconnecthealth). In de portal voor Azure AD Connect Health kunt u waarschuwingen bekijken, de prestaties controleren, het gebruik analyseren en nog veel meer. Met Azure AD Connect Health beschikt u in een oogopslag over de status van uw belangrijkste identiteitsonderdelen.

![Wat is Azure AD Connect Health?](./media/active-directory-aadconnect-health/aadconnecthealth2.png)

Toekomstige updates voor Azure AD Connect Health bevatten extra controle- en inzichtmogelijkheden voor aanvullende identiteitsonderdelen. Zo beschikt u over één dashboard als het gaat om identiteiten, waardoor u een meer robuuste, stabiele en geïntegreerde omgeving hebt waarvan uw gebruikers kunnen profiteren bij hun werk.

<!-- <center>![What is Azure AD Connect Health](./media/active-directory-aadconnect-health/logo1.png)</center> -->

## Waarom Azure AD Connect Health gebruiken?

Wanneer u uw on-premises directory's integreert met Azure AD, worden uw gebruikers productiever omdat zij één identiteit hebben voor toegang tot zowel cloud als on-premises resources. Bij deze integratie moet er echter wel voor worden gezorgd dat deze omgeving stabiel is, zodat gebruikers op een betrouwbare manier toegang hebben tot zowel on-premises als cloudresources vanaf elk apparaat. Azure AD Connect Health biedt een gemakkelijke, cloudgebaseerde benadering voor controle van en inzicht in uw on-premises infrastructuur, die wordt gebruikt voor toegang tot Office 365 of andere Azure AD-toepassingen. Het is net zo eenvoudig als het installeren van een agent op een van uw on-premises identiteitsservers.

## [Azure AD Connect Health voor AD FS](active-directory-aadconnect-health-adfs.md)

Azure AD Connect Health voor AD FS ondersteunt AD FS 2.0 op Windows Server 2008 R2, AD FS in Windows Server 2012 en Windows Server 2012R2. Dit omvat ook AD FS Proxy- of Web Application Proxy-servers die verificatie-ondersteuning bieden voor extranettoegang. Met een eenvoudige en voordelige installatie van de statusagent, biedt Azure AD Connect Health voor AD FS de volgende set kernfuncties:

- Controle met waarschuwingen over de status van AD FS- en AD FS Proxy-servers
- E-mailmeldingen voor kritieke waarschuwingen
- Weergave van trends wat betreft prestatiegegevens, hetgeen nuttig is voor de capaciteitsplanning van AD FS
- Gebruiksanalyse voor AD FS-aanmeldingen met een andere draaigrafiek (apps, gebruikers, netwerklocatie, enzovoort), wat handig is voor een beter begrip van de manier waarop AD FS wordt gebruikt.
- Rapporten voor AD FS, zoals 50 belangrijkste gebruikers die aanmeldingspogingen ondernemen met een ongeldige gebruikersnaam/ongeldig wachtwoord

De volgende video biedt een overzicht van Azure AD Connect Health voor AD FS

>[AZURE.VIDEO azure-ad-connect-health--monitor-you-identity-bridge]

## [Azure AD Connect Health voor synchroniseren](active-directory-aadconnect-health-sync.md)
Met Azure AD Connect Health voor synchroniseren wordt informatie gecontroleerd en geboden over de synchronisaties die worden uitgevoerd tussen uw on-premises Active Directory en Azure Active Directory. Azure AD Connect Health voor synchroniseren biedt de volgende set kernfuncties:

- Controle met waarschuwingen over de status van Azure AD Connect-servers of Sync Engine
- E-mailmeldingen voor kritieke waarschuwingen
- Operationele synchronisatie-inzichten, inclusief latentiegrafieken voor synchronisatiebewerkingen en trends in synchronisatiebewerkingen zoals toegevoegde en verwijderde items en updates.
- In een oogopslag informatie over synchronisatie-eigenschappen en de laatste succesvolle export naar Azure AD

De volgende video biedt een overzicht van Azure AD Connect Health voor synchroniseren

[Azure Active Directory Connect Health: de synchronisatie-engine controleren](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine)


## Aan de slag met Azure AD Connect Health
Het is heel eenvoudig om aan de slag te gaan met Azure AD Connect Health. Volg de onderstaande stappen:

1. [Installeer Azure AD Premium](active-directory-get-started-premium) of [start een proefversie](https://azure.microsoft.com/trial/get-started-active-directory/)

2. [Download en installeer Azure AD Connect Health-agents](#download-and-install-azure-ad-connect-health-agent) op uw servers voor identiteiten.

3. Ga naar [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth) om het Azure AD Connect Health-dashboard te bekijken

>[AZURE.NOTE]Houd er rekening mee dat u de Azure AD Connect Health-agents moet installeren op uw doelservers, voordat u gegevens ziet in uw Azure AD Connect Health-dashboard.

## Azure AD Connect Health-agent downloaden en installeren

- Zie de [vereisten](active-directory-aadconnect-health-agent-install.md#Requirements) voor Azure AD Connect Health

- Als u aan de slag wilt gaan met Azure AD Connect Health voor AD FS, kunt u de nieuwste versie van de agent hier downloaden: [Azure AD Connect Health-agent voor AD FS downloaden.](http://go.microsoft.com/fwlink/?LinkID=518973)
[](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)

- Als u aan de slag wilt gaan met Azure AD Connect Health voor synchroniseren, downloadt en installeert u de [nieuwste versie van Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771).  De statusagent wordt geïnstalleerd als onderdeel van de Azure AD Connect-installatie (versie 1.0.9125.0 of hoger).  Azure AD Connect ondersteunt een upgrade die u in plaats van eerdere versies kunt installeren.


## Portal voor Azure AD Connect Health
In de portal voor Azure AD Connect Health kunt u waarschuwingen weergeven, de prestaties controleren en het gebruik analyseren. Via de koppeling https://aka.ms/aadconnecthealth gaat u naar de hoofdblade van Azure AD Connect Health.  Een blade kunt u zien als een venster. Op de hoofdblade ziet u Snel starten, services in Azure AD Connect Health en extra configuratie-opties. Hieronder vindt u een schermafbeelding met een korte uitleg van elk van deze onderdelen.  Nadat u de agents hebt geïmplementeerd, worden de service-id's weergegeven van de services die met Azure AD Connect Health worden gecontroleerd.

![Portal voor Azure AD Connect Health](./media/active-directory-aadconnect-health/portal2.png)

- **Snel starten** - hiermee opent u de blade Snel starten. Hier kunt u de Azure AD Connect Health-agent downloaden door Hulpmiddelen ophalen te selecteren. Ook kunt u de documentatie openen en feedback geven.

- **Active Directory Federation Services** - dit verwijst naar alle AD FS-services die momenteel worden gecontroleerd door Azure AD Connect Health. Door een van de exemplaren te selecteren, wordt er een blade geopend met informatie over dat servicesexemplaar.  Deze informatie omvat een overzicht, eigenschappen, waarschuwingen, controle en gebruiksanalyses. U kunt [hier](active-directory-aadconnect-health-adfs.md) meer lezen over de functionaliteit.

- **Azure Active Directory Connect (Sync)** - dit verwijst naar de Azure AD Connect- servers die momenteel door Azure AD Connect Health worden gecontroleerd. Als u het item selecteert, wordt er een blade geopend met informatie over uw Azure AD Connect-servers. U kunt [hier](active-directory-aadconnect-health-sync.md) meer lezen over de functionaliteit.

- **Configureren** - hiermee kunt u de volgende opties in- of uitschakelen:

    1. Automatische updates zodat de Azure AD Connect Health-agent automatisch wordt bijgewerkt naar de nieuwste versie. Dit betekent dat uw systeem automatisch wordt bijgewerkt naar de nieuwste versie van Azure AD Connect Health Agent zodra deze beschikbaar is. Deze optie is standaard ingeschakeld.

    2. Microsoft alleen toegang geven tot de statusgegevens van uw Azure AD-directory voor het oplossen van problemen. Dit betekent dat als deze optie is ingeschakeld, Microsoft dezelfde gegevens kan zien als u. Dit kan handig zijn bij het oplossen van problemen en het verlenen van assistentie. Deze optie is standaard uitgeschakeld.


## Verwante koppelingen

* [Azure AD Connect Health Agent Installation (Engelstalig)](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health Operations (Engelstalig)](active-directory-aadconnect-health-operations.md)
* [Using Azure AD Connect Health with AD FS (Engelstalig)](active-directory-aadconnect-health-adfs.md)
* [Using Azure AD Connect Health for Sync (Engelstalig)](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect Health FAQ (Engelstalig)](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health Version History (Engelstalig)](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Jun16_HO2-->


