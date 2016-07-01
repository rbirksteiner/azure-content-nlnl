<properties
    pageTitle="Azure Active Directory B2C Preview: toepassingsregistratie | Microsoft Azure"
    description="Uw toepassing registreren met Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/16/2016"
    ms.author="swkrish"/>


# Azure Active Directory B2C Preview: uw toepassing registreren

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Vereiste

Als u een toepassing wilt maken waarin consumenten zich kunnen registreren en aanmelden, moet u de toepassing eerst registreren bij een Azure Active Directory B2C-tenant. Haal uw eigen tenant op aan de hand van de stappen in [Een Azure AD B2C-tenant maken](active-directory-b2c-get-started.md). Als u alle stappen in dit artikel hebt uitgevoerd, is de blade B2C-functies vastgemaakt aan uw Startboard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Ga naar de blade B2C-functies

U kunt naar de blade B2C-functies gaan vanuit van de Azure Portal of de klassieke Azure-portal.

### 1. Toegang via de Azure Portal

Als u de blade B2C-functies hebt vastgemaakt aan uw Startboard, ziet u de blade zodra u zich aanmeldt bij de [Azure Portal](https://portal.azure.com/) als globale beheerder van de B2C-tenant.

U kunt de blade ook openen door achtereenvolgens op **Bladeren** en **Azure AD B2C** te klikken in het linkernavigatiedeelvenster in de [Azure Portal](https://portal.azure.com/).

U kunt de blade ook rechtstreeks openen door naar [https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/](https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/) te gaan, waarbij **{tenant}** moet worden vervangen door de naam die wordt gebruikt wanneer de tenant wordt gemaakt (bijvoorbeeld contosob2c). U kunt een bladwijzer voor deze koppeling maken voor later gebruik.

    > [AZURE.IMPORTANT]
    U moet een globale beheerder van de B2C-tenant zijn om de blade B2C-functies te kunnen openen. Een globale beheerder van andere tenant of een gebruiker van een tenant heeft hiertoe geen toegang.

### 2. Toegang via de klassieke Azure-portal

Meld u aan bij de [klassieke Azure-portal](https://manage.windowsazure.com/) als abonnementsbeheerder. (Dit is hetzelfde werk- of schoolaccount of hetzelfde Microsoft-account dat u gebruikt voor het aanmelden bij Azure.) Ga naar de Active Directory-extensie aan de linkerkant en klik op uw B2C-tenant. Klik op het tabblad **Snel starten** (het eerste tabblad dat wordt geopend) op **B2C-instellingen beheren** onder **Beheren**. Hiermee wordt de blade B2C-functies geopend in een nieuw browservenster of -tabblad.

U kunt de koppeling **B2C-instellingen beheren** ook vinden in de sectie **B2C-beheer** op het tabblad **Configureren**.

## Een toepassing registreren

1. Klik op de blade B2C-functies in de Azure Portal op **Toepassingen**.
2. Klik op **+Toevoegen** boven aan de blade.
3. Geef een **Naam** op voor de toepassing waarmee deze wordt beschreven voor uw consumenten. U kunt bijvoorbeeld Contoso B2C-app opgeven.
4. Als u een webtoepassing schrijft, stelt u de schakeloptie **Web-app/web-API opnemen** in op **Ja**. De **Antwoord-URL's** zijn eindpunten waarop Azure AD B2C tokens retourneert die door uw toepassing worden aangevraagd. Geef bijvoorbeeld `https://localhost:44321/` op. Als uw toepassing een serveronderdeel (API) bevat dat moet worden beveiligd, maakt (en kopieert) u ook een **Toepassingsgeheim** door te klikken op de knop **Sleutel genereren**.

    > [AZURE.NOTE]
    **Toepassingsgeheim** is een belangrijke beveiligingsreferentie.

5. Als u een mobiele toepassing maakt, stelt u de schakeloptie **Systeemeigen client opnemen** in op **Ja**. Noteer de standaard**omleidings-URI** die automatisch wordt gemaakt.
6. Klik op **Maken** om uw toepassing te registreren.
7. Klik op de toepassing die u net hebt gemaakt en noteer de globaal unieke **toepassingsclient-id** die u later in uw code gebruikt.

## Een snelstarttoepassing maken

Nu u een toepassing hebt die is geregistreerd bij Azure AD B2C, kunt u snel aan de slag gaan aan de hand van een van onze snelstartzelfstudies. Hier volgen enkele aanbevelingen:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]



<!--HONumber=Jun16_HO2-->


