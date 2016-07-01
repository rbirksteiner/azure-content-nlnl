<properties
    pageTitle="Azure Active Directory instellen voor toegangsbeheer met self-service voor toepassingen | Microsoft Azure"
    description="Met groepsbeheer met self-service kunnen gebruikers beveiligingsgroepen of Office 365-groepen maken en beheren in Azure Active Directory. Bovendien biedt het gebruikers de mogelijkheid om het lidmaatschap van een beveiligingsgroep of Office 365-groep aan te vragen."
    services="active-directory"
    documentationCenter=""
  authors="curtand"
    manager="stevenpo"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/26/2016"
    ms.author="curtand"/>

# Azure Active Directory instellen voor groepsbeheer met self-service

Met groepsbeheer met self-service kunnen gebruikers beveiligingsgroepen of Office 365-groepen maken en beheren in Azure Active Directory (Azure AD). Bovendien biedt het gebruikers de mogelijkheid om het lidmaatschap van een beveiligingsgroep of Office 365-groep aan te vragen. Deze aanvraag kan worden goedgekeurd of geweigerd door de eigenaar van de groep. Met de functies voor groepsbeheer met self-service kan het dagelijks beheer van het groepslidmaatschap worden gedelegeerd naar personen die de zakelijke context voor dat lidmaatschap kennen. Functies voor groepsbeheer met self-service zijn alleen beschikbaar voor beveiligingsgroepen en Office 365-groepen, niet voor beveiligingsgroepen die e-mail gebruiken of voor distributielijsten.

Groepsbeheer met self-service bestaat momenteel uit twee kernscenario's: gedelegeerd groepsbeheer en groepsbeheer met self-service.

- **Gedelegeerd groepsbeheer**: een voorbeeld is een beheerder die de toegang beheert tot een SaaS-toepassing die door haar bedrijf wordt gebruikt. Het beheren van deze gebruiksrechten is omslachtig en daarom vraagt de beheerder de eigenaar van het bedrijf om een nieuwe groep te maken. De beheerder wijst nu de toegang tot de toepassing toe aan een nieuwe groep die de eigenaar zojuist heeft gemaakt en plaatst alle personen die momenteel toegang hebben tot de toepassing in deze groep. De eigenaar van het bedrijf kan meer gebruikers toevoegen en de toepassing wordt enkele minuten later automatisch voor ze ingericht. De eigenaar van het bedrijf hoeft niet te wachten op de beheerder, maar kan zelf de toegang voor de gebruikers regelen. Hetzelfde kan de beheerder doen voor een administratief medewerker van een andere bedrijfsgroep, en zowel de eigenaar van het bedrijf als de administratief medewerker kunnen nu de toegang beheren voor hun gebruikers, zonder dat ze de gebruikers van anderen kunnen zien of beheren. De beheerder kan nog steeds alle gebruikers die toegang tot de toepassing hebben zien en zonodig de toegangsrechten blokkeren.

- **Groepsbeheer met self-service**: een voorbeeld van dit scenario is wanneer twee gebruikers die beide onafhankelijk van elkaar SharePoint Online-sites hebben ingesteld, graag elkaars teams toegang willen geven tot hun sites. Dit kunnen ze bereiken door één groep te maken in Azure AD. In SharePoint Online selecteren ze vervolgens die groep en geven die toegang tot hun sites. Wanneer iemand toegang wil, kan dit worden aangevraagd bij het toegangspaneel en na goedkeuring wordt automatisch toegang verleend tot beide SharePoint Online-sites. Later wil een van beide gebruikers dat alle personen met toegang tot de site ook toegang krijgen tot een bepaalde SaaS-toepassing. Hij of zij vraagt de beheerder van deze SaaS-toepassing om toegangsrechten voor deze toepassing toe te voegen aan de site. Vanaf dat moment bieden alle goedgekeurde aanvragen toegang tot de twee SharePoint Online-sites en ook tot de SaaS-toepassing.

## Een groep beschikbaar maken voor self-service door eindgebruikers

Stel in de [klassieke Azure-portal](https://manage.windowsazure.com) op het tabblad **Configureren** de optie **Gedelegeerd groepsbeheer** in op Ingeschakeld en stel vervolgens **Gebruikers kunnen beveiligingsgroepen maken** of **Gebruikers kunnen Office-groepen maken** in op Ingeschakeld.

Als **Gebruikers kunnen beveiligingsgroepen maken** is ingeschakeld, kunnen alle gebruikers in uw directory nieuwe beveiligingsgroepen maken en leden toevoegen aan deze groepen. Deze nieuwe groepen worden ook weergegeven in het toegangspaneel voor alle gebruikers en andere gebruikers kunnen deelname aan deze groepen aanvragen als de beleidsinstelling voor de groep dit toelaat. Als **Gebruikers kunnen beveiligingsgroepen maken** is uitgeschakeld, kunnen gebruikers geen groepen maken of bestaande groepen wijzigen waarvan zij de eigenaar zijn. Ze kunnen echter wel het lidmaatschap van die groepen beheren en aanvragen van andere gebruikers goedkeuren.

Ook kunt u **Gebruikers die self-service kunnen gebruiken voor beveiligingsgroepen** gebruiken om meer gedetailleerd toegangsbeheer te verkrijgen over mogelijkheden voor groepsbeheer met self-service voor uw gebruikers. Als **Gebruikers kunnen groepen maken** is ingeschakeld, kunnen alle gebruikers in uw directory nieuwe groepen maken en leden toevoegen aan deze groepen. Als u daarnaast de optie **Gebruikers die self-service kunnen gebruiken voor beveiligingsgroepen** instelt op Sommige, beperkt u het groepsbeheer tot een bepaalde groep gebruikers. Als deze optie wordt ingesteld op Sommige, wordt de groep SSGMSecurityGroupsUsers gemaakt in uw directory en kunnen alleen die gebruikers die u lid hebt gemaakt van deze groep, nieuwe beveiligingsgroepen maken en leden toevoegen aan deze groepen binnen uw directory. Door **Gebruikers die self-service kunnen gebruiken voor beveiligingsgroepen** in te stellen op Alle, kunt u alle gebruikers in uw directory de mogelijkheid geven om nieuwe groepen te maken.

Ook kunt u het selectievakje **Groep die self-service kan gebruiken voor beveiligingsgroepen** (standaard ingesteld op 'SSGMSecurityGroupsUsers') gebruiken om uw eigen aangepaste naam op te geven voor een groep die alle gebruikers omvat die self-service kunnen gebruiken en nieuwe groepen kunnen maken in uw directory.

## Aanvullende informatie

Deze artikelen bevatten aanvullende informatie over Azure Active Directory.

* [Managing access to resources with Azure Active Directory groups (Engelstalig)](active-directory-manage-groups.md)

* [Article Index for Application Management in Azure Active Directory (Engelstalig)](active-directory-apps-index.md)

* [What is Azure Active Directory? (Engelstalig)](active-directory-whatis.md)

* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](active-directory-aadconnect.md)



<!--HONumber=Jun16_HO2-->


