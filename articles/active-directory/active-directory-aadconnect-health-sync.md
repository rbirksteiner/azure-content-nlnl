
<properties
    pageTitle="Azure AD Connect Health gebruiken met synchronisatie | Microsoft Azure"
    description="Dit is de Azure AD Connect Health-pagina waarop wordt besproken hoe u synchronisatie met Azure AD Connect kunt controleren."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/12/2016"
    ms.author="billmath"/>

# Azure AD Connect Health gebruiken voor synchronisatie
De volgende documentatie is specifiek voor het controleren van Azure AD Connect-synchronisatie met Azure AD Connect Health.  Zie [Using Azure AD Connect Health with AD FS](active-directory-aadconnect-health-adfs.md) (Engelstalig) voor informatie over het controleren van AD FS met Azure AD Connect Health.

![Azure AD Connect Health voor synchroniseren](./media/active-directory-aadconnect-health-sync/sync.png)

## Waarschuwingen voor Azure AD Connect Health voor synchroniseren
In de sectie Waarschuwingen van Azure AD Connect Health voor synchroniseren vindt u een lijst met actieve waarschuwingen. Elke waarschuwing omvat relevante informatie, stappen voor het oplossen van het probleem en koppelingen naar gerelateerde documentatie. Als u een actieve of opgeloste waarschuwing selecteert, ziet u een nieuwe blade met aanvullende informatie. Ook ziet u stappen die u kunt nemen voor het oplossen van het probleem en koppelingen naar aanvullende documentatie. U kunt ook u historische gegevens bekijken voor waarschuwingen die in het verleden zijn opgelost.

Als u een waarschuwing selecteert, ziet u aanvullende informatie. Ook ziet u stappen die u kunt nemen voor het oplossen van het probleem en koppelingen naar aanvullende documentatie.

![Azure AD Connect-synchronisatiefout](./media/active-directory-aadconnect-health-sync/alert.png)

### Beperkte evaluatie van waarschuwingen
Als Azure AD Connect NIET wordt gebruikt met de standaardconfiguratie (bijvoorbeeld als het kenmerkfilter is gewijzigd van de standaardconfiguratie in een aangepaste configuratie), worden met de Azure AD Connect Health-agent geen fouten geüpload die zijn gerelateerd aan Azure AD Connect. 

Dit beperkt de evaluatie van waarschuwingen door de service. In de Azure-portal onder uw service ziet u een banner waarin deze situatie wordt beschreven.

![Azure AD Connect Health voor synchroniseren](./media/active-directory-aadconnect-health-sync/banner.png)

U kunt dit wijzigen door te klikken op 'Instellingen' en de Azure AD Connect Health-agent toestemming te verlenen om alle foutlogboeken te uploaden.

![Azure AD Connect Health voor synchroniseren](./media/active-directory-aadconnect-health-sync/banner2.png)

## Inzicht in synchronisatie
Aan de nieuwste versie van Azure AD Connect Health voor synchroniseren zijn de volgende nieuwe mogelijkheden toegevoegd:

- Latentie van de synchronisatiebewerkingen
- Trend van objectwijziging

### Synchronisatielatentie
Met deze functie wordt een grafische trend weergegeven van de latentie van de synchronisatiebewerkingen (import, export, enzovoort) voor connectoren.  Dit biedt een snelle en eenvoudige manier om niet alleen inzicht te krijgen in de latentie van uw bewerkingen (handig als er een grote set wijzigingen moet worden uitgevoerd), maar het is ook een manier om onregelmatigheden in de latentie te ontdekken die nader moeten worden onderzocht.

![Synchronisatielatentie](./media/active-directory-aadconnect-health-sync/synclatency.png)

Standaard wordt alleen de latentie van de bewerking 'Exporteren' voor de Azure AD-connector weergegeven.  Als u meer bewerkingen wilt bekijken voor de connector of als u bewerkingen van andere connectoren wilt zien, klikt u met de rechtermuisknop op de grafiek en kiest u de specifieke bewerking en de connector.

### Synchronisatie van objectwijzigingen
Deze functie biedt een grafisch weergegeven trend van het aantal wijzigingen dat wordt geëvalueerd en geëxporteerd naar Azure AD.  Op dit moment is het verzamelen van deze informatie uit de synchronisatielogboeken nog moeilijk.  De grafiek biedt niet alleen een eenvoudigere manier om het aantal wijzigingen in uw omgeving te controleren, maar ook een visuele weergave van de fouten die zich voordoen.

![Synchronisatielatentie](./media/active-directory-aadconnect-health-sync/syncobjectchanges.png)

## Verwante koppelingen

* [Azure AD Connect Health (Engelstalig artikel)](active-directory-aadconnect-health.md)
* [Azure AD Connect Health Agent Installation (Engelstalig)](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health Operations (Engelstalig)](active-directory-aadconnect-health-operations.md)
* [Using Azure AD Connect Health with AD FS (Engelstalig)](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health FAQ (Engelstalig)](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health Version History (Engelstalig)](active-directory-aadconnect-health-version-history.md)




<!--HONumber=Jun16_HO2-->


