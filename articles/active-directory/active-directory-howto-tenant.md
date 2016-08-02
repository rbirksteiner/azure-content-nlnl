<properties
    pageTitle="Een Azure AD-tenant verkrijgen | Microsoft Azure"
    description="Een Azure Active Directory-tenant verkrijgen voor het registreren en maken van toepassingen."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="terrylan"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/28/2015"
    ms.author="dastrock"/>

# Een Azure Active Directory-tenant verkrijgen

In Azure Active Directory (Azure AD) is een [tenant](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) representatief voor een organisatie.  Het is een toegewezen exemplaar van de Azure AD-service die een organisatie ontvangt en waarvan de organisatie de eigenaar is wanneer deze zich registreert voor een Microsoft-cloudservice, zoals Azure, Microsoft Intune of Office 365.  Elke Azure AD-tenant is uniek en werkt afzonderlijk van andere Azure AD-tenants.  

Een tenant bevat alle gebruikers in een bedrijf en de bijbehorende informatie: hun wachtwoorden, gebruikersprofielgegevens, machtigingen enzovoort.  Het bevat ook groepen, toepassingen en andere informatie over een organisatie en de beveiliging.

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij uw toepassing, moet u uw toepassing registreren in een tenant van uzelf.  Het publiceren van een toepassing in een Azure AD-tenant is **helemaal gratis**.  De meeste ontwikkelaars maken verschillende tenants en toepassingen om te experimenten, te ontwikkelen, te faseren en te testen.  Organisaties die zich registreren voor uw toepassing en deze gebruiken, kunnen ervoor kiezen licenties aan te schaffen als ze gebruik willen maken van geavanceerde directoryfuncties.

Maar hoe kunt u een Azure AD-tenant verkrijgen?  De procedure kan enigszins afwijken als u:

- [Een bestaand Office 365-abonnement hebt](#use-an-existing-office-365-subscription)
- [Een bestaand Azure-abonnement hebt dat aan een Microsoft-Account is gekoppeld](#use-an-msa-azure-subscription)
- [Een bestaand Azure-abonnement hebt dat aan een organisatieaccount is gekoppeld](#use-an-organizational-azure-subscription)
- [Geen van de bovenstaande abonnementen hebt en de procedure vanaf het begin wilt uitvoeren](#start-from-scratch)

## Een bestaand Office 365-abonnement gebruiken
Als u een bestaand Office 365-abonnement hebt, maar geen Azure-abonnement (en u zich niet kunt aanmelden bij de [Azure Management Portal](https://manage.windowsazure.com)), volgt u [deze instructies](https://technet.microsoft.com/library/dn832618.aspx) om toegang te krijgen tot uw Azure AD-tenant.

## Een MSA Azure-abonnement gebruiken
Als u zich eerder hebt geregistreerd voor een Azure-abonnement met uw afzonderlijke Microsoft-account, hebt u al een tenant.  De [Azure Management Portal](https://manage.windowsazure.com) bevat een tenant met de naam Standaardtenant onder Alle items en Active Directory.  U kunt deze tenant naar wens gebruiken, maar mogelijk wilt u een beheerdersaccount voor de organisatie maken.

Voer hiervoor de volgende stappen uit.  U kunt ook een nieuwe tenant en een beheerder in deze tenant maken via een vergelijkbare procedure.

1.  Meld u aan bij de [Azure Management Portal](https://manage.windowsazure.com) met uw afzonderlijke account
2.  Ga naar de sectie Active Directory van de portal (op de linkernavigatiebalk)
3.  Selecteer de vermelding Standaarddirectory in de lijst met beschikbare directory's
4.  Klik op de koppeling Gebruikers boven aan de pagina.  U ziet één gebruiker in de lijst met de waarde Microsoft-account in de kolom Afkomstig uit
5.  Klik op Gebruiker toevoegen onder aan de pagina
6.  Geef in het formulier Gebruiker toevoegen de volgende informatie op:
    - Type gebruiker: nieuwe gebruiker in uw organisatie
    - Gebruikersnaam: (kies een gebruikersnaam op voor deze beheerder)
    - Voornaam/Achternaam/Weergavenaam: (kies de gewenste waarden)
    - Rol: hoofdbeheerder
    - Alternatief e-mailadres: (geef de juiste waarden op)
    - Optioneel: Multi-Factor Authentication inschakelen
    - Klik ten slotte op de groene knop MAKEN om het maken van de gebruiker te voltooien (en het tijdelijke wachtwoord weer te geven).
7.  Wanneer u het formulier Gebruiker toevoegen hebt ingevuld en het tijdelijke wachtwoord voor de nieuwe gebruiker met beheerdersrechten hebt ontvangen, noteert u dit wachtwoord omdat u zich moet aanmelden met deze nieuwe gebruiker om het wachtwoord te wijzigen. U kunt het wachtwoord ook rechtstreeks naar de gebruiker verzenden via een alternatief e-mailbericht.
8.  Als u het tijdelijke wachtwoord wilt wijzigen, meldt u zich aan bij https://login.microsoftonline.com met dit nieuwe gebruikersaccount en wijzigt u het wachtwoord als u hierom wordt gevraagd.


## Een Azure-abonnement voor de organisatie gebruiken
Als u zich eerder hebt geregistreerd voor een Azure-abonnement met uw organisatieaccount, hebt u al een tenant.  De [Azure Management Portal](https://manage.windowsazure.com) bevat een tenant onder Alle items en Active Directory.  U kunt deze tenant naar wens gebruiken.  Mogelijk wilt u ook een nieuwe tenant maken met de knop Nieuw linksonder in de portal.


## De procedure vanaf het begin uitvoeren
U hoeft zich geen zorgen te maken als u de bovenstaande informatie niet begrijpt.  Ga naar [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) om u aan te melden voor Azure met een nieuwe organisatie.  Wanneer u de procedure hebt voltooid, hebt u uw eigen Azure AD-tenant met de domeinnaam die u hebt gekozen tijdens het aanmelden.  In de [Azure Management Portal](https://manage.windowsazure.com) kunt u uw tenant zoeken door naar Active Directory in het linkernavigatievenster te gaan.

Tijdens de aanmeldingsprocedure voor Azure moet u uw creditcardgegevens opgeven.  U kunt gerust doorgaan. Er worden namelijk geen kosten in rekening gebracht voor het publiceren van toepassingen in Azure AD of het maken van nieuwe tenants.



<!--HONumber=Jun16_HO2-->


