<properties
   pageTitle="Managing groups in Azure Active Directory | Microsoft Azure"
   description="How to create and manage groups to manage Azure users using Azure Active Directory."
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
   ms.date="05/26/2016"
   ms.author="curtand"/>


# Groepen beheren in Azure Active Directory

Een van de functies van het gebruikersbeheer van Azure Active Directory (Azure AD) is de mogelijkheid voor het maken van groepen gebruikers. Vervolgens kunt u een groep gebruiken om licenties toe te wijzen aan een klasse gebruikers. Ook kunt u groepen gebruiken om toegangsmachtigingen toe te wijzen aan:

- Resources zoals objecten in de directory;
- Resources die zich buiten de directory bevinden, zoals SaaS-toepassingen, Azure-services, SharePoint-sites of on-premises resources.

Bovendien kan een resource-eigenaar toegang tot een resource toewijzen aan een Azure AD-groep. Hiermee wordt leden van die groep toegang verleend tot de resource. Daarna kan de eigenaar van de groep het lidmaatschap van de groep beheren. Dit houdt in dat de eigenaar van de resource de machtiging voor het toewijzen van gebruikers aan de resource delegeert naar de eigenaar van de groep.

## Hoe maak ik een groep?

Deze taak kan worden uitgevoerd door de Office 365-accountportal, de Windows Intune-accountportal of de klassieke Azure-portal te gebruiken, afhankelijk van de services waarop uw organisatie zich heeft geabonneerd. Zie [Uw Windows Azure AD-tenant beheren](active-directory-administer.md) voor meer informatie over het gebruik van andere portals dan de Azure-portal voor het beheren van uw Azure Active Directory.

1. Selecteer in de [klassieke Azure-portal](https://manage.windowsazure.com) de optie **Active Directory** en vervolgens de naam van de directory van uw organisatie.

2. Selecteer de tab **Groepen**.

3. Selecteer **Groep toevoegen**.

4. Geef in het venster **Groep toevoegen** de naam en de beschrijving van een groep op.


## Hoe kan ik afzonderlijke gebruikers in een beveiligingsgroep toevoegen of verwijderen?

**Een afzonderlijke gebruiker toevoegen aan een groep**

1. Selecteer in de [klassieke Azure-portal](https://manage.windowsazure.com) de optie **Active Directory** en vervolgens de naam van de directory van uw organisatie.

2. Selecteer de tab **Groepen**.

3. Open de groep waaraan u leden wilt toevoegen. Standaard wordt het tabblad **Leden** van de geselecteerde groep weergegeven.

4. Selecteer **Leden toevoegen**.

5. Selecteer op de pagina **Leden toevoegen** de naam van de gebruiker of groep die u als lid wilt toevoegen aan deze groep en zorg ervoor dat de naam wordt toegevoegd in het deelvenster **Geselecteerd**.


**Een afzonderlijke gebruiker verwijderen uit een groep**

1. Selecteer in de [klassieke Azure-portal](https://manage.windowsazure.com) de optie **Active Directory** en vervolgens de naam van de directory van uw organisatie.

2. Selecteer de tab **Groepen**.

3. Open de groep waaruit u leden wilt verwijderen.

4. Selecteer op het tabblad **Leden** de naam van het lid dat u wilt verwijderen uit deze groep en klik op **Verwijderen**.

6. Bevestig dat u dit lid uit de groep wilt verwijderen wanneer u hierom wordt gevraagd.


## Hoe kan ik het lidmaatschap van een groep dynamisch beheren?

In Azure AD kunt u heel gemakkelijk een eenvoudige regel instellen (een regel waarmee slechts één vergelijking wordt gemaakt) aan de hand waarvan wordt bepaald welke gebruikers lid worden van de groep. Als een groep bijvoorbeeld is toegewezen aan een SaaS-toepassing en u een regel instelt voor het toevoegen van gebruikers met de functienaam 'Vertegenwoordiger', krijgen alle gebruikers binnen uw Azure AD-directory met die functienaam toegang tot deze SaaS-toepassing.

> [AZURE.NOTE] U kunt een regel instellen voor dynamisch lidmaatschap voor beveiligingsgroepen of Office 365-groepen. Op dit moment wordt genest groepslidmaatschap niet ondersteund voor toewijzing aan een toepassing op basis van een groep.
>
> Bij dynamisch lidmaatschap voor groepen moet een Azure AD Premium-licentie worden toegewezen aan
>
> - De beheerder die de regel voor een groep beheert
> - Alle gebruikers die op basis van de regel zijn geselecteerd voor lidmaatschap van de groep

**Dynamische lidmaatschap inschakelen voor een groep**

1. Selecteer in de [klassieke Azure-portal](https://manage.windowsazure.com) de optie **Active Directory** en vervolgens de naam van de directory van uw organisatie.

2. Selecteer de tab **Groepen** en open de groep die u wilt bewerken.

3. Selecteer de tab **Configureren** en stel vervolgens **Dynamische lidmaatschappen inschakelen** in op **Ja**.

4. Stel een eenvoudige regel in voor de groep, waarmee u beheert hoe dynamisch lidmaatschap werkt voor deze groep. Zorg ervoor dat de optie **Gebruikers toevoegen als** is geselecteerd en selecteer vervolgens een gebruikerseigenschap in de lijst (bijvoorbeeld afdeling, functietitel, enzovoort),

5. Selecteer vervolgens een voorwaarde (Niet gelijk aan, Gelijk aan, Begint niet met, Begint met, Bevat niet, Bevat, Geen overeenkomst, Overeenkomst) en geef daarna een waarde op voor de geselecteerde gebruikerseigenschap.

Zie [Using attributes to create advanced rules](active-directory-accessmanagement-groups-with-advanced-rules.md) (Engelstalig) voor meer informatie over het maken van *geavanceerde* regels (regels met meerdere vergelijkingen).

## Aanvullende informatie

Deze artikelen bevatten aanvullende informatie over Azure Active Directory.

* [Managing access to resources with Azure Active Directory groups (Engelstalig)](active-directory-manage-groups.md)

* [Article Index for Application Management in Azure Active Directory (Engelstalig)](active-directory-apps-index.md)

* [What is Azure Active Directory? (Engelstalig)](active-directory-whatis.md)

* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](active-directory-aadconnect.md)



<!---HONumber=Jun16_HO2-->


