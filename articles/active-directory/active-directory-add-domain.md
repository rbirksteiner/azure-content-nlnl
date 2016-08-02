<properties
    pageTitle="Uw aangepaste domeinnaam toevoegen aan Azure Active Directory | Microsoft Azure"
    description="Hier leest u hoe u de domeinnamen van uw bedrijf kunt toevoegen aan Azure Active Directory en hoe u de domeinnaam kunt verifiëren."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/20/2016"
    ms.author="curtand;jeffsta"/>

# Uw aangepaste domeinnaam toevoegen aan Azure Active Directory

U hebt een of meer domeinnamen die door uw organisatie worden gebruikt voor zakelijke activiteiten en uw gebruikers melden zich aan bij uw bedrijfsnetwerk via uw bedrijfsdomeinnaam. Nu u Azure Active Directory (Azure AD) gebruikt, kunt u uw zakelijke domeinnaam ook toevoegen aan Azure AD. U kunt dan gebruikersnamen toewijzen in de directory die bekend is bij uw gebruikers, zoals 'gwenda@contoso.com'. Het proces is eenvoudig:

- Voeg uw domeinnaam toe in de wizard **Domein toevoegen** in de klassieke Azure-portal

- Een DNS-vermelding instellen in de klassieke Azure AD-portal of via het hulpprogramma Azure AD Connect

- De DNS-vermelding voor de domeinnaam in het DNS-zone-bestand op de website van de DNS-registratieservice toevoegen

- De domeinnaam verifiëren in de klassieke Azure AD-portal of met het hulpprogramma Azure AD Connect


Voordat u uw aangepaste domeinnaam verifieert, moeten uw gebruikers zich aanmelden met gebruikersnamen als 'gwenda@contoso.onmicrosoft.com', waarbij de initiële domeinnaam voor uw directory wordt gebruikt. Als u meerdere aangepaste domeinnamen nodig hebt, zoals 'contoso.com' en 'contosobank.com', kunt u tot maximaal 900 domeinnamen toevoegen. Gebruik de stappen in dit artikel om een domeinnaam toe te voegen.

## Een aangepaste domeinnaam toevoegen aan uw directory

1. Meld u aan bij de [klassieke Azure-portal](https://manage.windowsazure.com/) met een gebruikersaccount met de rechten voor globale beheerder van uw Azure AD-directory.

2. Open in **Active Directory** uw directory en selecteer de tab **Domeinen**.

3. Selecteer op de opdrachtbalk de optie **Toevoegen** en geef vervolgens de naam op van uw aangepaste domein, bijvoorbeeld 'contoso.com'. Vergeet niet de extensie .com, .net of een andere extensie van het hoogste niveau toe te voegen.

4. Als u van plan bent dit domein te configureren voor [federatieve aanmelding](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) met uw on-premises Active Directory, schakelt u het selectievakje in.

5. Selecteer **Toevoegen**.

Nu u een domeinnaam hebt toegevoegd, moet met Azure AD worden gecontroleerd of uw organisatie de eigenaar is van de domeinnaam. Voordat deze verificatie met Azure AD kan worden uitgevoerd, moet u een DNS-vermelding toevoegen in het DNS-zonebestand voor de domeinnaam. Deze taak wordt voor de domeinnaam uitgevoerd op de website voor domeinnaamregistrar.

## De DNS-vermeldingen voor de domeinnaam ophalen

De DNS-vermeldingen staan in het tweede venster van de wizard **Domein toevoegen**, als u geen federatie uitvoert met een on-premises Windows Server Active Directory.

Als u het domein configureert voor de federatie, wordt u omgeleid voor het downloaden van het hulpprogramma Azure AD Connect. Voer het hulpprogramma Azure AD Connect uit om [de DNS-vermeldingen op te halen die u moet toevoegen aan uw domeinnaamregistrar](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Met het hulpprogramma Azure AD Connect wordt ook de domeinnaam voor Azure AD gecontroleerd.

## De DNS-vermelding toevoegen aan het DNS-zonebestand

1.  Meld u aan bij de domeinnaamregistrar voor het domein. Als u niet over de juiste machtigingen beschikt om de DNS-vermelding bij te werken, vraagt dan de persoon die of het team dat wel toegang heeft om de DNS-vermelding toe te voegen.

2.  Werk het DNS-zonebestand voor het domein bij, door de DNS-vermelding toe te voegen die u van Azure AD hebt ontvangen. Met deze DNS-vermelding kan Azure AD controleren of u eigenaar bent van het domein. De DNS-vermelding leidt niet tot veranderingen in het gedrag van bijvoorbeeld mailroutering of webhosting. Het kan maximaal een uur duren voordat de DNS-records zijn doorgegeven.

[Instructies voor het toevoegen van een DNS-vermelding bij populaire DNS-registrars](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## De domeinnaam verifiëren met Azure AD

Nadat u de DNS-vermelding hebt toegevoegd, moet u controleren of de domeinnaam is geverifieerd met Azure AD. Dit is de laatste stap op weg naar een geslaagde toevoeging van de domeinnaam.

Als u de wizard **Domein toevoegen** nog geopend hebt, selecteert u **Verifiëren** in het derde venster van de wizard. U dient maximaal een uur te wachten op het doorgeven van de DNS-vermelding voordat u verdergaat met verifiëren.

Als de wizard **Domein toevoegen** niet meer is geopend, kunt u het domein verifiëren in de [klassieke Azure-portal](https://manage.windowsazure.com/):

1.  Meld u aan met een gebruikersaccount met de rechten voor globale beheerder van uw Azure AD-directory.

2.  Open uw directory en selecteer de tab **Domeinen**.

3.  Selecteer het domein dat u wilt verifiëren.

4.  Selecteer **Verifiëren** op de opdrachtbalk en selecteer vervolgens **Verifiëren** in het dialoogvenster.

Gefeliciteerd! Nu kunt u [gebruikersnamen toewijzen die uw aangepaste domeinnaam omvatten](active-directory-add-domain-add-users.md). Zie de sectie [Problemen oplossen](#troubleshooting) als u problemen had met het verifiëren van de domeinnaam.

## Problemen oplossen
Als u een aangepaste domeinnaam niet kunt verifiëren, is er een aantal mogelijke oorzaken. We beginnen met de meest voorkomende en werken de lijst af naar de minst voorkomende.

- U hebt geprobeerd de domeinnaam te verifiëren voordat de DNS-vermelding was doorgegeven. Wacht even en probeer het opnieuw.

- De DNS-record is helemaal niet ingevoerd. Verifieer de DNS-vermelding en wacht tot deze is doorgegeven. Probeer het vervolgens opnieuw.

- De domeinnaam was al geverifieerd in een andere directory. Ga naar de domeinnaam en verwijder deze uit de andere directory. Probeer het vervolgens opnieuw.

- De DNS-record bevat een fout. Corrigeer de fout en probeer het opnieuw.

- U beschikt niet over de juiste machtigingen voor het bijwerken van DNS-records. Deel de DNS-vermeldingen met de persoon die of het team dat deze toegang heeft binnen uw organisatie en vraag de persoon of het team om de DNS-vermelding toe te voegen.


## Volgende stappen

-   [Gebruikersnamen toewijzen die uw aangepaste domeinnaam omvatten](active-directory-add-domain-add-users.md)
-   [Aangepaste domeinnamen beheren](active-directory-add-manage-domain-names.md)
-   [Meer informatie over concepten wat betreft domeinbeheer in Azure AD](active-directory-add-domain-concepts.md)
-   [De huisstijl van uw bedrijf weergeven wanneer uw gebruikers zich aanmelden](active-directory-add-company-branding.md)
-   [PowerShell gebruiken voor het beheren van domeinnamen in Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)



<!--HONumber=Jun16_HO2-->


