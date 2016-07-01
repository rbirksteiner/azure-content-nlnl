<properties
    pageTitle="Voorbeeld van Azure Active Directory B2C: een Azure Active Directory B2C-tenant maken | Microsoft Azure"
    description="In dit artikel wordt beschreven hoe u een Azure Active Directory B2C-tenant kunt maken"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/16/2016"
    ms.author="swkrish"/>

# Voorbeeld van Azure Active Directory B2C: een Azure AD B2C-tenant maken

Als u aan de slag wilt met Microsoft Azure Active Directory (Azure AD) B2C, voert u de drie stappen uit die in dit artikel worden beschreven.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Stap 1: aanmelden voor een Azure-abonnement

Deze stap kunt u overslaan als u al een Azure-abonnement hebt. Is dit niet het geval, dan meldt u zich aan voor een [Azure-abonnement](../active-directory/sign-up-organization.md) en krijgt u toegang tot Azure AD B2C.

> [AZURE.NOTE]
Op dit moment kunt u gratis gebruikmaken van het voorbeeld van Azure AD B2C, maar het gebruik is beperkt tot 50.000 gebruikers per tenant. Voor toegang tot de [klassieke Azure-portal](http://manage.windowsazure.com/) is een Azure-abonnement vereist.

## Stap 2: een Azure AD B2C-tenant maken

Met de volgende stappen maakt u een nieuwe Azure AD B2C-tenant. Momenteel kunnen B2C-functies niet worden ingeschakeld in uw bestaande directory's, als u die hebt.

1. Meld u als abonnementsbeheerder aan bij de [klassieke Azure-portal](https://manage.windowsazure.com/). Dit is hetzelfde werk- of schoolaccount of hetzelfde Microsoft-account dat u gebruikt voor het aanmelden bij Azure.
2. Klik op **Nieuw** > **App Services** > **Active Directory** > **Directory** > **Aangepast item maken**.

    ![Schermafbeelding van het begin van het maken van een tenant](./media/active-directory-b2c-get-started/new-directory.png)

3. Kies de **Naam**, de **domeinnaam** en het **land en de regio** voor uw tenant.
4. Schakel de optie **Dit is een B2C-directory** in.
5. Klik op het vinkje om de bewerking te voltooien.

    ![Schermafbeelding van het selectievakje voor het maken van een B2C-directory](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Uw tenant is nu gemaakt en wordt weergegeven in de Active Directory-extensie. Ook bent u nu een hoofdbeheerder van de tenant. U kunt naar believen hoofdbeheerders toevoegen.

## Stap 3: ga naar de blade met B2C-functies in de Azure-portal

1. Navigeer naar de Active Directory-extensie in de navigatiebalk aan de linkerkant.
2. Ga naar uw tenant op het tabblad **Directory** en klik erop.
3. Klik op de tab **Configureren**.
4. Klik op de koppeling **B2C-instellingen beheren** in de sectie **B2C-beheer**.

    ![Schermafbeelding van directoryconfiguratie voor B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. De Azure-portal met de blade met B2C-functies wordt geopend in een nieuw browsertabblad of -venster.

    > [AZURE.IMPORTANT]
    Het kan maximaal 2-3 minuten duren voordat uw tenant toegankelijk is op de Azure-portal. Dit wordt opgelost als u deze stappen na enige tijd nogmaals uitvoert. Neem contact op met de ondersteuning als dit niet het geval is.

6. Maak deze blade voor gemakkelijke toegang vast aan uw Startboard. (Het hulpprogramma voor vastmaken is rood gemarkeerd in de rechterbovenhoek van de functiesblade.)

    ![Schermafbeelding van de B2C-functiesblade](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    U kunt gebruikers en groepen beheren, het herstellen van wachtwoorden via self-service configureren en huisstijlfuncties van uw tenant beheren in de [klassieke Azure-portal](https://manage.windowsazure.com/).

## Volgende stappen

Lees [Azure Active Directory B2C preview: Register your application](active-directory-b2c-app-registration.md) (Engelstalig) voor informatie over het registreren van een toepassing met Azure AD B2C en het bouwen van een toepassing voor Snel starten.



<!--HONumber=Jun16_HO2-->


