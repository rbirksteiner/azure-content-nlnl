<properties
    pageTitle="Hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory | Microsoft Azure"
    description="Problemen met aanmelden bij Microsoft Azure en verwante problemen, zoals problemen met de relatie tussen een Azure-abonnement en de Azure Active Directory."
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
    ms.date="04/26/2016"
    ms.author="curtand"/>

# Hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory

In dit onderwerp wordt informatie geboden over problemen met het aanmelden bij Microsoft Azure en verwante problemen, zoals problemen met de relatie tussen een Azure-abonnement en de Azure Active Directory (Azure AD).

## Accounts die u kunt gebruiken om u aan te melden
We beginnen met de accounts die u kunt gebruiken om u aan te melden. Er zijn twee typen: een Microsoft-account (voorheen bekend als Microsoft Live ID) en een werk- of schoolaccount dat is opgeslagen in Azure AD.

 Microsoft-account  | Azure AD-account
    ------------- | -------------
Het klantidentiteitssysteem dat wordt uitgevoerd door Microsoft | Het bedrijfsidentiteitssysteem dat wordt uitgevoerd door Microsoft
Verificatie voor services die op de klant zijn gericht, zoals Hotmail en MSN | Verificatie voor services die op bedrijven zijn gericht, zoals Office 365
Klanten maken hun eigen Microsoft-accounts, zoals wanneer ze zich registreren voor een e-mailadres | Bedrijven en organisaties maken en beheren hun eigen werk- en schoolaccounts
Identiteiten worden gemaakt en opgeslagen in het Microsoft-accountsysteem | Identiteiten worden gemaakt met behulp van Azure of een andere service, zoals Office 365, en ze worden opgeslagen in een Azure AD-exemplaar dat is toegewezen aan de organisatie

Hoewel Azure oorspronkelijk alleen toegang verleende aan Microsoft-accountgebruikers, wordt nu toegang verleend aan gebruikers van *beide* systemen. Dit wordt mogelijk gemaakt door alle Azure-eigenschappen de Azure AD te laten vertrouwen voor verificatie, door Azure AD bedrijfsgebruikers te laten verifiëren en door een federatieve relatie te creëren waarbij Azure AD het klantidentiteitssysteem voor Microsoft-accounts vertrouwt om klantgebruikers te verifiëren. Als gevolg hiervan kan Azure AD zowel Microsoft-gastaccounts als systeemeigen Azure AD-accounts verifiëren.

Hier meldt bijvoorbeeld een gebruiker met een Microsoft-account zich aan bij de klassieke Azure Portal.

> [AZURE.NOTE]
> Als u zich met msmith@hotmail.com wilt aanmelden bij de klassieke Azure Portal, moet er een Azure-abonnement aan dat account gekoppeld zijn. Het account moet een servicebeheerder of medebeheerder van het abonnement zijn.

![][1]

Omdat dit Hotmail-adres een klantaccount is, wordt de verificatie van de aanmelding uitgevoerd door het klantidentiteitssysteem voor Microsoft-accounts. Het Azure AD-identiteitssysteem vertrouwt de verificatie die is uitgevoerd door het Microsoft-accountsysteem en geeft een token uit voor toegang tot de Azure-services.

## Hoe een Azure-abonnement is gerelateerd aan Azure AD

Voor elk Azure-abonnement is er een vertrouwensrelatie met een Azure AD-exemplaar. Dit betekent dat er op die directory wordt vertrouwd voor het verifiëren van gebruikers, services en apparaten. Meerdere abonnementen kunnen dezelfde directory vertrouwen, maar een abonnement vertrouwt slechts één directory. Op het tabblad Instellingen kunt u zien welke directory wordt vertrouwd door uw abonnement. U kunt de [abonnementsinstellingen bewerken](active-directory-understanding-resource-access.md) om te wijzigen welke directory wordt vertrouwd.

De vertrouwensrelatie die een abonnement heeft met een directory is anders dan de relatie die een abonnement heeft met andere resources in Azure (websites, databases, enzovoort); deze resources lijken meer op onderliggende resources van een abonnement. Als een abonnement is verlopen, wordt toegang tot de andere resources die zijn gekoppeld aan het abonnement ook geblokkeerd. De directory blijft echter wel aanwezig in Azure, en u kunt er een ander abonnement aan koppelen om de directorygebruikers te blijven beheren.

De Azure AD-extensie die u in uw abonnement ziet, werkt ook niet op dezelfde manier als de andere extensies in de klassieke Azure Portal. Andere extensies in de klassieke Azure Portal zijn gericht op het Azure-abonnement. Wat u in de Azure AD-extensie ziet, is niet afhankelijk van het abonnement; er worden directory’s weergegeven op basis van welke gebruiker is aangemeld.

Alle gebruikers hebben één basisdirectory waarmee ze worden geverifieerd, maar ze kunnen ook gasten zijn in andere directory’s. U ziet in de Azure AD-extensie alle directory’s waar uw gebruikersaccount lid van is. Directory’s waar uw account geen lid van is, worden niet weergegeven. Met een directory kunnen tokens worden uitgegeven voor werk- of schoolaccounts in Azure AD of voor Microsoft-accountgebruikers (omdat Azure AD is gefedereerd met het Microsoft-accountsysteem).

In dit diagram ziet u het abonnement van Michael Smith nadat hij zich heeft geregistreerd met een werkaccount voor Contoso.

![][2]

## Een abonnement en een directory beheren
Met de beheerdersrollen voor een Azure-abonnement worden de resources beheerd die zijn gekoppeld aan het Azure-abonnement. Deze rollen en aanbevolen procedures voor het beheer van uw abonnement worden besproken in [Beheerdersrollen toewijzen in Azure Active Directory](active-directory-assign-admin-roles.md).

De rol Servicebeheerder wordt standaard aan u toegewezen wanneer u zich registreert. Als anderen zich moeten aanmelden en services willen gebruiken met hetzelfde abonnement, kunt u hen toevoegen als medebeheerders. De servicebeheerder en medebeheerders kunnen Microsoft-accounts of werk- of schoolaccounts zijn uit de directory waaraan het Azure-abonnement is gekoppeld.

Azure AD heeft een andere set beheerdersrollen voor het beheren van de directory en identiteitsgerelateerde functies. De hoofdbeheerder van een directory kan bijvoorbeeld gebruikers en groepen toevoegen aan de directory, of Multi-Factor Authentication vereisen voor gebruikers. Wanneer een gebruiker een directory maakt, wordt deze automatisch de hoofdbeheerder. De hoofdbeheerder kan beheerdersrollen toewijzen aan andere gebruikers.

Azure AD-beheerders kunnen net als abonnementsbeheerders mensen zijn met een Microsoft-account of een werk- of schoolaccount. Azure AD-beheerdersrollen worden ook gebruikt door andere services, zoals Office 365 en Microsoft Intune. Zie [Beheerdersrollen toewijzen](active-directory-assign-admin-roles.md) voor meer informatie.

Wat belangrijk is om te onthouden is dat Azure-abonnementbeheerders en Azure AD-directorybeheerders twee verschillende dingen zijn. Azure-abonnementsbeheerders kunnen resources in Azure beheren en kunnen de Active Directory-extensie in de klassieke Azure Portal bekijken (omdat de klassieke Azure Portal een Azure-resource is). Directorybeheerders kunnen eigenschappen in de directory beheren.

Een persoon kan beide rollen hebben, maar dit is niet vereist. De rol van hoofdbeheerder van een directory kan aan een gebruiker worden toegewezen, maar deze komt dan niet meer in aanmerking voor de rol van servicebeheerder of medebeheerder van een Azure-abonnement. Deze gebruiker kan zich niet aanmelden bij de klassieke Azure Portal als deze geen beheerder is van het abonnement. De gebruiker kan echter wel directorybeheertaken uitvoeren met andere hulpprogramma’s, zoals Azure AD PowerShell en het Office 365-beheercentrum.

## Waarom kan ik de directory niet beheren met mijn huidige gebruikersaccount?

Soms probeert een gebruiker zich bij de klassieke Azure Portal aan te melden met een werk- of schoolaccount voordat deze heeft geregistreerd voor een Azure-abonnement. In dit geval ontvangt de gebruiker het bericht dat er geen abonnement is voor dat account. Het bericht bevat een koppeling naar een gratis proefversie van een abonnement.

Wanneer de gebruiker zich heeft geregistreerd voor de gratis proefversie, krijgt hij/zij de directory van de organisatie te zien in de klassieke Azure Portal, maar kan de gebruiker deze niet beheren (er kunnen geen gebruikers worden toegevoegd en bestaande gebruikerseigenschappen kunnen niet worden bewerkt) omdat de gebruiker geen hoofdbeheerder is van de directory. Met het abonnement kan de gebruiker de klassieke Azure Portal gebruiken en de Azure Active Directory-extensie bekijken, maar er zijn aanvullende machtigingen van een hoofdbeheerder nodig om de directory te beheren.

## Uw werk- of schoolaccount gebruiken om een Azure-abonnement te beheren dat is gemaakt met een Microsoft-account

Het is een best practice om u [als organisatie te registreren voor Azure](sign-up-organization.md) en om een werk- of schoolaccount te gebruiken om resources te beheren in Azure. Werk- of schoolaccounts hebben de voorkeur omdat deze centraal kunnen worden beheerd door de organisatie die de accounts beschikbaar heeft gesteld. De accounts bieden meer functionaliteit dan Microsoft-accounts en worden rechtstreeks geverifieerd door Azure AD. Datzelfde account biedt toegang tot andere online Microsoft-services die worden aangeboden aan bedrijven en organisaties, zoals Office 365 en Microsoft Intune. Als u al een account hebt dat u met deze andere diensten gebruikt, wilt u waarschijnlijk datzelfde account gebruiken voor Azure. Deze diensten zijn dan ook al gekoppeld aan een Active Directory-exemplaar, welke waarschijnlijk ook door uw Azure-abonnement vertrouwd moet worden.

Werk- of schoolaccounts kunnen op meer manieren worden beheerd dan Microsoft-accounts. Een beheerder kan bijvoorbeeld het wachtwoord van een werk- of schoolaccounts opnieuw instellen, of er Multi-Factor Authentication voor vereisen.

In sommige gevallen wilt u mogelijk dat een gebruiker uit uw organisatie de resources kan beheren die zijn gekoppeld aan een Azure-abonnement ten behoeve van een Microsoft-account van een klant. Zie [De directory voor uw Office 365-abonnement in Azure beheren](#manage-the-directory-for-your-office-365-subscription-in-azure) voor meer informatie over het overstappen naar meerdere accounts voor het beheer van abonnementen of directory’s.


## Aanmelden als u uw werke-mailadres hebt gebruikt voor uw Microsoft-account

Als u op een bepaald moment in het verleden een Microsoft-klantaccount hebt gemaakt met uw werke-mailadres als gebruikers-id, kan het zijn dat u de vraag krijgt om te kiezen tussen het Microsoft Azure-accountsysteem en het Microsoft-accountsysteem.

![][3]

U hebt gebruikersaccounts met dezelfde naam, één in Azure AD en één in het Microsoft-klantaccountsysteem. U moet het account kiezen dat aan het Azure-abonnement is gekoppeld dat u wilt gebruiken. Als u de foutmelding krijgt dat er geen abonnement bestaat voor deze gebruiker, hebt u waarschijnlijk de verkeerde optie gekozen. Meld u af en probeer het opnieuw. Zie [Problemen oplossen als er geen abonnementen zijn gevonden die zijn gekoppeld aan uw account](https://social.msdn.microsoft.com/Forums/en-US/f952f398-f700-41a1-8729-be49599dd7e2/troubleshooting-we-were-unable-to-find-any-subscriptions-associated-with-your-account-errors-in?forum=windowsazuremanagement) voor meer informatie over fouten die het aanmelden kunnen verhinderen.

## De directory voor uw Office 365-abonnement in Azure beheren

Stel dat u zich hebt geregistreerd bij Office 365, vóórdat u zich hebt geregistreerd bij Azure. Nu wilt u de directory van het Office 365-abonnement beheren in de klassieke Azure Portal. Er zijn twee manieren om dit te doen, afhankelijk van of u zich hebt geregistreerd bij Azure of niet.

### Ik heb geen abonnement op Azure

In dit geval [registreert u zich bij Azure](sign-up-organization.md) met het werk- of schoolaccount waarmee u zich aanmeldt bij Office 365. De relevante informatie uit het Office 365-account wordt vooraf ingevuld in het Azure-registratieformulier. Uw account krijgt voor dit abonnement de rol Servicebeheerder toegewezen.  

### Ik heb een abonnement voor Azure via mijn Microsoft-account

Als u zich voor Office 365 hebt geregistreerd met een werk- of schoolaccount en u zich vervolgens voor Azure hebt geregistreerd met een Microsoft-account, hebt u twee directory’s: één voor werk of school en een standaarddirectory die is gemaakt tijdens het registeren voor Azure.

Als u beide directory’s wilt beheren in de klassieke Azure Portal, voltooit u deze stappen.

> [AZURE.NOTE]
> Deze stappen kunnen alleen worden uitgevoerd wanneer een gebruiker is aangemeld met een Microsoft-account. Als de gebruiker is aangemeld met een werk- of schoolaccount, is de optie **Bestaande directory gebruiken** niet beschikbaar omdat een werk- of schoolaccount alleen kan worden geverifieerd met de basisdirectory (de directory waarin het werk- of schoolaccount is opgeslagen en die het eigendom is van werk of school).

1. Meld u aan bij de klassieke Azure Portal met behulp van uw Microsoft-account.

2. Klik achtereenvolgens op **Nieuw** > **App Services** > **Active Directory** > **Directory** > **Aangepast maken**.

3. Klik op **Bestaande directory gebruiken** en schakel het selectievakje in bij **Ik ben er klaar voor om te worden afgemeld**. Klik daarna op het vinkje om de bewerking te voltooien.

4. Meld u aan bij de klassieke Azure Portal met een account met hoofdbeheerdersrechten voor de werk- of schooldirectory.

5. Wanneer u wordt gevraagd **De Contoso-directory gebruiken met Azure?**, klikt u op **Doorgaan**.

6. Klik op **Nu afmelden**.

7. Meld u weer aan bij de klassieke Azure Portal met behulp van uw Microsoft-account. Beide directory’s worden nu weergegeven in de Active Directory-extensie.


## Volgende stappen

- Als u meer wilt weten over het wijzigen van de beheerders van een Azure-abonnement, ziet u [Azure-beheerdersrollen toevoegen of wijzigen](../billing-add-change-azure-subscription-administrator.md)

- Als u meer wilt weten over hoe de toegang tot resources wordt beheerd in Microsoft Azure, ziet u [Inzicht krijgen in toegang tot resources in Azure](active-directory-understanding-resource-access.md)

- Zie [Beheerdersrollen toewijzen in Azure Active Directory](active-directory-assign-admin-roles.md) voor meer informatie over het toewijzen van rollen in Azure AD.

- [Als organisatie registreren voor Azure](sign-up-organization.md)


<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG



<!--HONumber=Jun16_HO2-->


