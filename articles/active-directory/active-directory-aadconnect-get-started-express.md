<properties
    pageTitle="Azure AD Connect: aan de slag met expresinstellingen | Microsoft Azure"
    description="In dit artikel kunt u lezen hoe u de installatiewizard downloadt, installeert en uitvoert voor Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/26/2016"
    ms.author="billmath;andkjell"/>

# Aan de slag met Azure AD Connect met expresinstellingen
**Expresinstellingen** van Azure AD Connect worden gebruikt wanneer u een singleforesttopologie hebt en [wachtwoordsynchronisatie](active-directory-aadconnectsync-implement-password-synchronization.md) voor verificatie. **Expresinstellingen** is de standaardoptie en wordt gebruikt voor het meest geïmplementeerde scenario. U bent slechts enkele snelle klikken verwijderd van uitbreiding van uw on-premises directory naar de cloud.

Zorg voordat u begint met de installatie van Azure AD Connect dat u [Azure AD Connect downloadt](http://go.microsoft.com/fwlink/?LinkId=615771) en de vereiste stappen in [Azure AD Connect: Hardware en vereisten](active-directory-aadconnect-prerequisites.md) voltooit.

Zie [verwante documentatie](#related-documentation) voor andere scenario's als de expresinstellingen niet met uw topologie overeenkomen.

## Snelle installatie van Azure AD Connect
In de sectie met [video's](#videos) kunt u zien hoe deze stappen in de praktijk worden uitgevoerd.

1. Meld u aan als lokale beheerder op de server waarop u Azure AD Connect wilt installeren. Doe dit op de server die u wilt gebruiken als synchronisatieserver.
2. Ga naar **AzureADConnect.msi** en dubbelklik erop.
3. Selecteer in het welkomstscherm het vakje waarmee u aangeeft akkoord te gaan met de licentievoorwaarden en klik op **Doorgaan**.  
4. Klik in het scherm Expresinstellingen op **Expresinstellingen gebruiken**.  
![Welkom bij Azure AD Connect](./media/active-directory-aadconnect-get-started-express/express.png)
5. Voer in het scherm Verbinding maken met Azure AD de gebruikersnaam en het wachtwoord in van een hoofdbeheerder voor Azure AD. Klik op **Volgende**.  
![Verbinding maken met Azure AD](./media/active-directory-aadconnect-get-started-express/connectaad.png) Als u een foutbericht ontvangt en problemen met de connectiviteit hebt, raadpleegt u [Connectiviteitsproblemen oplossen](active-directory-aadconnect-troubleshoot-connectivity.md).
6. Voer in het scherm Verbinding maken met Azure AD de gebruikersnaam en het wachtwoord in voor een enterprisebeheerdersaccount. U kunt het domeingedeelte in NetBios- of FQDN-indeling invoeren, dat wil zeggen FABRIKAM\administrator of fabrikam.com\administrator. Klik op **Volgende**.  
![Verbinding maken met AD DS](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. De pagina [**Configuratie van aanmelding bij Azure AD**](active-directory-aadconnect-user-signin.md#azure-ad-sign-in-configuration) wordt alleen weergegeven als u de [domeinen niet hebt geverifieerd](active-directory-add-domain.md) bij de [vereisten](active-directory-aadconnect-prerequisites.md).
![Niet-geverifieerde domeinen](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png)  
Als deze pagina wordt weergegeven, controleert u elk domein dat is gemarkeerd met **Niet toegevoegd** en **Niet geverifieerd**. Zorg ervoor dat de domeinen die u gebruikt in Azure AD zijn geverifieerd. Klik op het symbool Vernieuwen wanneer u uw domeinen hebt geverifieerd.
8. Klik in het venster Gereed om te configureren op **Installeren**.
    - Optioneel kunt u op de pagina Gereed voor configuratie het vakje **Start het synchronisatieproces zodra de configuratie is voltooid** uitschakelen. Schakel dit selectievakje uit als u een aanvullende configuratie wilt uitvoeren, zoals [filteren](active-directory-aadconnectsync-configure-filtering.md). Als u deze optie uitschakelt, wordt de synchronisatie geconfigureerd met de wizard, maar blijft de planner uitgeschakeld. Deze wordt niet uitgevoerd tenzij u deze handmatig inschakelt door de [installatiewizard opnieuw uit te voeren](active-directory-aadconnectsync-installation-wizard.md).
    - Als u Exchange hebt ingeschakeld in uw on-premises Active Directory, hebt u ook een optie voor het inschakelen van [**Hybride implementatie voor Exchange**](https://technet.microsoft.com/library/jj200581.aspx). Schakel deze optie in als u van plan bent Exchange-postvakken tegelijkertijd in de cloud en on-premises te gebruiken.
![Klaar voor het configureren van Azure AD Connect](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
9. Wanneer de installatie is voltooid, klikt u op **Afsluiten**.
10. Nadat de installatie is voltooid, dient u zich af te melden en weer aan te melden voordat u de Synchronization Service Manager of Synchronization Rule Editor gaat gebruiken.

## Video's

Voor een video over het gebruik van expresinstallatie gaat u naar:

>[AZURE.VIDEO azure-active-directory-connect-express-settings]

## Volgende stappen
Nu u Azure AD Connect geïnstalleerd hebt, kunt u [de installatie verifiëren en licenties toewijzen](active-directory-aadconnect-whats-next.md).

Lees meer over deze functies, die tijdens de installatie zijn ingeschakeld: [Automatische upgrade](active-directory-aadconnect-feature-automatic-upgrade.md), [Onopzettelijk verwijderen voorkomen](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) en [Azure AD Connect Health](active-directory-aadconnect-health-sync.md).

Lees meer over deze veelvoorkomende onderwerpen: [Scheduler en het activeren van de synchronisatie](active-directory-aadconnectsync-feature-scheduler.md).

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory ](active-directory-aadconnect.md).

## Verwante documentatie

Onderwerp |  
--------- | ---------
Overzicht Azure AD Connect | [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](active-directory-aadconnect.md)
Installeren met behulp van aangepaste instellingen | [Custom installation of Azure AD Connect (Engelstalig)](active-directory-aadconnect-get-started-custom.md)
Upgraden van DirSync | [Upgrade van Azure AD-synchronisatiehulpprogramma (DirSync) (Engelstalig artikel)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Accounts die worden gebruikt voor installatie | [Meer informatie over accounts en machtigingen van Azure AD Connect (Engelstalig artikel)](active-directory-aadconnect-accounts-permissions.md)



<!--HONumber=Jun16_HO2-->


