<properties
    pageTitle="Gebruikers uit andere directory's of partnerbedrijven toevoegen in Azure Active Directory | Microsoft Azure"
    description="In dit artikel wordt uitgelegd hoe u gebruikers kunt toevoegen of gebruikersinformatie kunt wijzigen in Azure Active Directory, inclusief externe en gastgebruikers."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/31/2016"
    ms.author="curtand;viviali"/>

# Add users from other directories or partner companies in Azure Active Directory (Engelstalig)

Gebruikers toevoegen om uw directory te vullen. In dit artikel wordt uitgelegd hoe u gebruikers kunt toevoegen van andere directory's in Azure Active Directory of gebruikers van partnerbedrijven. Zie [Add new users to Azure Active Directory](active-directory-create-users.md) (Engelstalig) voor meer informatie over het toevoegen van nieuwe gebruikers in uw organisatie en het toevoegen van gebruikers die Microsoft-accounts hebben. Toegevoegde gebruikers hebben standaard geen gebruikersrechten, maar u kunt op elk gewenst moment rollen aan ze toewijzen.

## Een gebruiker toevoegen

1. Meld u aan bij de [klassieke Azure-portal](https://manage.windowsazure.com) met een account met globale beheerdersrechten voor de directory.

2. Selecteer **Active Directory** en open vervolgens uw directory.

3. Selecteer de tab **Gebruikers** en selecteer vervolgens in de opdrachtbalk **Gebruiker toevoegen**.

4. Selecteer op de pagina **Vertel ons meer over deze gebruiker** onder **Type gebruiker** een van de volgende opties:

    - **Gebruiker in een andere Azure AD-directory**: hiermee voegt u een gebruikersaccount toe aan uw directory dat afkomstig is uit een andere Azure AD-directory. U kunt alleen een gebruiker selecteren in een andere directory als u ook lid van die directory bent.
    - **Gebruikers in partnerbedrijven** - gebruik deze optie om gebruikers van partnerbedrijven uit te nodigen in uw directory (zie [Azure Active Directory B2B collaboration](active-directory-b2b-what-is-azure-ad-b2b.md) (Engelstalig)). Hiervoor moet u moet [een CSV-bestand met e-mailadressen uploaden](active-directory-b2b-references-csv-file-format.md).

6. Geef op de **profielpagina** van de gebruiker een voor- en achternaam op, een gebruiksvriendelijke naam en een gebruikersrol uit de lijst **Rollen**. Zie [Assigning administrator roles in Azure AD](active-directory-assign-admin-roles.md) (Engelstalig) voor meer informatie over gebruikers- en beheerdersrollen. Activeer desgewenst de optie **Meervoudige verificatie inschakelen** voor de gebruiker.

7. Selecteer op de pagina **Tijdelijk wachtwoord instellen** de optie **Maken**.

> [AZURE.IMPORTANT] Als uw organisatie meer dan één domein gebruikt, is het nuttig op de hoogte te zijn van de volgende problemen die zich kunnen voordoen bij het toevoegen van een gebruikersaccount:
>
> - Als u gebruikersaccounts met dezelfde UPN (user principal name) wilt toevoegen in meerdere domeinen, voegt u **eerst** bijvoorbeeld egbertborsboom@contoso.onmicrosoft.com, **gevolgd door** egbertborsboom@contoso.com.
> - Voeg egbertborsboom@contoso.com **niet** toe voordat u egbertborsboom@contoso.onmicrosoft.com toevoegt. De volgorde is belangrijk en het kan lastig zijn verkeerd ingevoerde adressen ongedaan te maken.

Als u informatie wijzigt voor een gebruiker wiens identiteit is gesynchroniseerd met uw on-premises Active Directory-service, kunt u de gebruikersinformatie niet wijzigen in de klassieke Azure-portal. Gebruik uw beheerhulpprogramma's voor on-premises Active Directory om de gebruikersinformatie te wijzigen.

## Externe gebruikers toevoegen

U kunt ook gebruikers toevoegen van een andere Azure AD-directory waar u lid van bent, of van partnerbedrijven. Hiervoor uploadt u een CSV-bestand. Als u een externe gebruiker wilt toevoegen, selecteert u voor **Type gebruiker** de optie **Gebruiker in een andere Microsoft Azure AD-directory** of **Gebruikers in partnerbedrijven**.

Gebruikers van beide typen zijn afkomstig van een andere directory en worden toegevoegd als **externe gebruikers**. Externe gebruikers kunnen samenwerken met andere gebruikers in een directory, zonder dat nieuwe accounts of referenties hoeven worden toegevoegd. Externe gebruikers melden zich aan met de gegevens van hun eigen directory, en die verificatie werkt ook voor andere directory's waaraan ze zijn toegevoegd.

## Beheer van externe gebruikers en beperkingen

Wanneer u een gebruiker vanuit een andere directory toevoegt aan uw directory, wordt die gebruiker beschouwd als een externe gebruiker in uw directory. De weergavenaam en de gebruikersnaam worden gekopieerd vanuit hun oorspronkelijke directory en gebruikt voor extern gebruik in uw directory. Vanaf dat moment zijn de eigenschappen van het externe gebruikersaccount geheel onafhankelijk. Als de eigenschappen van de gebruiker worden gewijzigd in de oorspronkelijke directory, gelden die wijzigingen niet voor het externe gebruikersaccount in uw directory.

De enige connectie tussen de twee accounts is dat de gebruiker altijd wordt geverifieerd met de oorspronkelijke directory of met een Microsoft-account. Daarom wordt er voor een externe gebruiker geen optie weergegeven voor het herstellen van het wachtwoord of voor het inschakelen van meervoudige verificatie. Op dit moment is het verificatiebeleid van de oorspronkelijke directory of het Microsoft-account het enige dat wordt geëvalueerd wanneer de gebruiker zich aanmeldt.

> [AZURE.NOTE] U kunt de externe gebruiker nog steeds uitschakelen in de directory. Op deze manier blokkeert u de toegang tot uw directory.

Als een gebruiker is verwijderd uit de oorspronkelijke directory of als hij of zij het Microsoft-account opzegt, bestaat de externe gebruiker nog steeds in uw directory. De gebruiker in uw directory heeft echter geen toegang meer tot resources, omdat hij of zij zich niet kan aanmelden met een oorspronkelijke directory of een Microsoft-account.

### Services die momenteel toegang door externe gebruikers van Azure AD ondersteunen

- De **klassieke Azure-portal**: hier kan een externe gebruiker die beheerder is van meerdere directory's die directory's beheren.
- **SharePoint Online**: als extern delen is ingeschakeld, heeft een externe gebruiker toegang tot geautoriseerde resources van SharePoint Online.
- **Dynamics CRM**: externe gebruikers die via PowerShell over een licentie beschikken, hebben toegang tot geautoriseerde resources in Dynamics CRM.
- **Dynamics AX**: externe gebruikers die via PowerShell over een licentie beschikken, hebben toegang tot geautoriseerde resources in Dynamics AX. De beperkingen voor [externe gebruikers van Azure AD](#known-limitations-of-azure-ad-external-users) en [gastgebruikers](#guest-user-management-and-limitations) zijn ook van toepassing op gebruikers in Dynamics AX.

### Bekende beperkingen voor externe gebruikers van Azure AD

- Externe gebruikers die beheerders zijn, kunnen geen gebruikers van partnerbedrijven toevoegen aan directory's (B2B-samenwerking) buiten de oorspronkelijke directory
- Externe gebruikers kunnen niet instemmen met multitenant-toepassingen in directory's buiten hun oorspronkelijke directory
- PowerBI ondersteunt momenteel geen toegang voor externe gebruikers
- De Office-portal biedt geen ondersteuning voor de licentieverlening van externe gebruikers
- Wat betreft Azure AD PowerShell zijn externe gebruikers aangemeld via hun oorspronkelijke directory en kunnen ze geen directory's beheren waarin zij externe gebruikers zijn


## Wat er volgt

- [Nieuwe gebruikers toevoegen aan Azure Active Directory (Engelstalig artikel)](active-directory-create-users.md)
- [Azure AD beheren (Engelstalig artikel)](active-directory-administer.md)
- [Wachtwoorden beheren in Azure AD (Engelstalig)](active-directory-manage-passwords.md)
- [Groepen beheren in Azure AD (Engelstalig artikel)](active-directory-manage-groups.md)



<!--HONumber=Jun16_HO2-->


