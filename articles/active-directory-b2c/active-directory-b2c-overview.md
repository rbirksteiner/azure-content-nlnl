<properties
    pageTitle="Azure Active Directory B2C Preview: overzicht | Microsoft Azure"
    description="Consumententoepassingen ontwikkelen met Azure Active Directory B2C"
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
    ms.topic="hero-article"
    ms.date="06/06/2016"
    ms.author="swkrish"/>

# Azure Active Directory B2C Preview: consumenten registreren en aanmelden bij uw toepassingen

Azure Active Directory B2C is een uitgebreide oplossing voor cloudidentiteitsbeheer voor uw webtoepassingen en mobiele toepassingen voor consumenten. Dit is een maximaal beschikbare, wereldwijde service voor honderden miljoenen consumentidentiteiten. Azure Active Directory B2C is ontwikkeld op een hoogwaardig, veilig platform en is bedoeld om uw toepassingen, uw bedrijf en uw klanten te beveiligen.

Voorheen schreven toepassingsontwikkelaars die consumenten in hun toepassingen wilden registreren en aanmelden, hun eigen code. Bovendien gebruikten ze on-premises databases of systemen om gebruikersnamen en wachtwoorden op te slaan. Met Azure Active Directory B2C kunnen ontwikkelaars beter identiteitsbeheer van consumenten integreren met hun toepassingen met behulp van een beveiligd, op standaarden gebaseerd platform en een uitgebreide set uitbreidbare beleidsregels. Wanneer u Azure Active Directory B2C gebruikt, kunnen uw consumenten zich registreren voor uw toepassingen met hun bestaande sociale accounts (Facebook, Google, Amazon, LinkedIn) of door nieuwe referenties te maken (e-mailadres en wachtwoord of gebruikersnaam en wachtwoord). Deze laatste noemen we 'lokale accounts'.

Azure Active Directory B2C wordt nog verder ontwikkeld. In deze periode horen we graag uw feedback en ervaringen bij het uitproberen van het product. Op basis van deze feedback kunnen we grote wijzigingen aanbrengen om de service te verbeteren.  Breng gedurende deze periode geen productietoepassing uit die gebruikmaakt van de preview-versie. Geef ons uw mening via [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/).

## Aan de slag

Als u een toepassing wilt maken waarin consumenten zich kunnen registreren en aanmelden, moet u de toepassing eerst registreren bij een Azure Active Directory B2C-tenant. Haal uw eigen tenant op aan de hand van de stappen in [Create an Azure AD B2C tenant](active-directory-b2c-get-started.md) (Een Azure AD B2C-tenant maken).

U kunt uw toepassing schrijven op basis van de Azure Active Directory B2C-service door ervoor te kiezen om protocolberichten rechtstreeks te verzenden met [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) of [Open ID Connect](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow) of door onze bibliotheken het werk voor u te laten doen. Kies uw favoriete platform in de volgende tabel en ga aan de slag.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## Nieuwe functies

Kijk regelmatig of er nieuwe informatie is over toekomstige wijzigingen in Azure Active Directory B2C Preview. We tweeten ook over eventuele updates via @AzureAD.

- Lees meer over ons [uitbreidbare beleidsframework](active-directory-b2c-reference-policies.md) en over de typen beleidsregels die u kunt maken en gebruiken in uw toepassingen.
- Huidige [beperkingen in de preview-versie](active-directory-b2c-limitations.md).

## Artikelen met procedures

Lees meer over het gebruik van specifieke functies van Azure Active Directory B2C Preview:

- Configureer accounts van [Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) en [LinkedIn](active-directory-b2c-setup-li-app.md) voor gebruik in uw consumententoepassingen.
- [Gebruik aangepaste kenmerken om informatie over uw consumenten te verzamelen](active-directory-b2c-reference-custom-attr.md).
- [Schakel Azure Multi-Factor Authentication in uw consumententoepassingen in](active-directory-b2c-reference-mfa.md).
- [Stel selfservice wachtwoordherstel in voor uw consumenten](active-directory-b2c-reference-sspr.md).
- [Pas het uiterlijk aan van de registratie- en aanmeldingspagina en andere consumentenpagina's](active-directory-b2c-reference-ui-customization.md) die door Azure Active Directory B2C worden verwerkt.
- [Gebruik Azure Active Directory Graph API om consumenten programmatisch te maken, te lezen, bij te werken en te verwijderen](active-directory-b2c-devquickstarts-graph-dotnet.md) in uw Azure Active Directory B2C-tenant.

## Volgende stappen

Deze koppelingen zijn handig als u de service uitgebreid wilt verkennen:

- Zie de [Prijsinformatie over Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- Vraag hulp bij Stack Overflow met de tags [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) of [adal](http://stackoverflow.com/questions/tagged/adal).
- Geef ons uw mening over de preview-versie via [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/), we horen uw mening graag! Typ 'AzureADB2C:' in de titel van uw bericht, zodat we dit kunnen vinden.
- Azure Active Directory B2C ondersteunt protocollen die voldoen aan industriële standaarden, OpenID Connect en OAuth 2.0 met een model voor toepassingsregistratie met de naam App Model v2.0.
  - [Protocolnaslaginformatie voor App Model v2.0](active-directory-b2c-reference-protocols.md)
  - [Tokennaslaginformatie voor App Model v2.0](active-directory-b2c-reference-tokens.md)
- Lees de [veelgestelde vragen over Azure Active Directory B2C](active-directory-b2c-faqs.md).
- [Aanvragen voor bestandsondersteuning voor Azure Active Directory B2C](active-directory-b2c-support.md).

## Beveiligingsupdates voor onze producten downloaden

We raden u aan in te stellen dat u meldingen ontvangt wanneer er beveiligingsincidenten optreden door naar [deze pagina](https://technet.microsoft.com/security/dd252948) te gaan en u te abonneren op Security Advisory Alerts.



<!--HONumber=Jun16_HO2-->


