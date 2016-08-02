<properties
   pageTitle="Ontwikkelaarshandleiding voor Azure Active Directory | Microsoft Azure"
   description="Dit artikel bevat een uitgebreide handleiding voor bronnen voor ontwikkelaars voor Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/02/2016"
   ms.author="mbaldwin"/>


# Ontwikkelaarshandleiding voor Azure Active Directory

## Overzicht
Azure Active Directory is een IDMaaS-platform (Identity Management as a Service) waarop ontwikkelaars efficiënt identiteitsbeheer kunnen integreren in hun toepassingen. De volgende artikelen bevatten overzichten van de implementatie en belangrijke functies van Azure Active Directory. We raden u aan deze in de juiste volgorde te lezen. Ga naar [Aan de slag](#getting-started) als u direct aan de slag wilt gaan.


1. [De voordelen van Azure Active Directory-integratie](active-directory-how-to-integrate.md): ontdek waarom de integratie met Azure Active Directory de beste oplossing is voor beveiligde aanmelding en autorisatie.

1. [Scenario's voor Active Directory-verificatie](active-directory-authentication-scenarios.md): profiteer van vereenvoudigde verificatie in Azure Active Directory om eenmalige aanmelding voor uw toepassing te bieden.

1. [Toepassingen integreren met Azure Active Directory](active-directory-integrating-applications.md): meer informatie over het toevoegen, bijwerken en verwijderen van toepassingen in Azure Active Directory en de huisstijlrichtlijnen voor geïntegreerde apps.

1. [Azure Active Directory Graph API](active-directory-graph-api.md): gebruik de Azure Active Directory Graph API om via een programma toegang te krijgen tot Azure Active Directory via REST API-eindpunten. Azure AD Graph API is ook toegankelijk via [Microsoft Graph](https://graph.microsoft.io/), een geïntegreerde API waarmee u toegang hebt tot meerdere API's voor Microsoft-cloudservices via één REST API-eindpunt en met één toegangstoken.

1. [Azure Active Directory-verificatiebibliotheken](active-directory-authentication-libraries.md): u kunt eenvoudig gebruikers verifiëren om toegangstokens te verkrijgen met Azure AD-verificatiebibliotheken voor .NET, JavaScript, Objective-C, Android en meer.


## Aan de slag

Deze zelfstudies zijn geschikt voor meerdere platforms en hiermee kunt u snel leren te ontwikkelen met Azure Active Directory. Als vereiste moet u [een Azure Active Directory-tenant ophalen](active-directory-howto-tenant.md).

### Snelstartgidsen voor mobiele toepassingen en pc-toepassingen

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Windows Store](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)

### Snelstartgidsen voor webtoepassingen

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![Javascript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md)
|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)

### Snelstartgidsen voor web-API's

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### De snelstartgids voor Directory opvragen

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[Graph API](active-directory-graph-api-quickstart.md)|

## Procedures

In deze artikelen wordt beschreven hoe u bepaalde taken uitvoert met Azure Active Directory:

- [Een Azure Active Directory-tenant ophalen](active-directory-howto-tenant.md)
- [Uw toepassing weergeven in de Azure Active Directory-toepassingsgalerie](active-directory-app-gallery-listing.md)
- [Het Azure Active Directory-toepassingsmanifest](active-directory-application-manifest.md)
- [Een app maken met Office 365-API's](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Web-apps voor Office 365 verzenden naar het verkoperdashboard](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- Informatie over het inschakelen van eenmalige aanmelding in verschillende apps met ADAL op [Android](active-directory-sso-android.md)- en [iOS](active-directory-sso-ios.md)-apparaten
- [Voorbeeld: apps maken waarmee gebruikers zich aanmelden met persoonlijke accounts en werk- of schoolaccounts](active-directory-appmodel-v2-overview.md)
- [Voorbeeld: apps maken waarmee gebruikers zich registreren en aanmelden](../active-directory-b2c/active-directory-b2c-overview.md)


## Naslaginformatie

Deze artikelen bevatten naslaginformatie over REST API's en API's voor verificatiebibliotheken, protocollen, fouten, codevoorbeelden en eindpunten.  

###  Ondersteuning
- [Vragen met tags](http://stackoverflow.com/questions/tagged/azure-active-directory): vind Azure Active Directory-oplossingen over stackoverloop door te zoeken naar de tags [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) en [adal](http://stackoverflow.com/questions/tagged/adal).

### Code

- [Open source-bibliotheken van Azure Active Directory](http://github.com/AzureAD): de eenvoudigste manier om een bibliotheekbron te zoeken, is via onze [bibliotheeklijst](active-directory-authentication-libraries.md).

- [Azure Active Directory-voorbeelden](https://github.com/azure-samples?query=active-directory): de eenvoudigste manier om door de lijst met voorbeelden te bladeren, is via de [index met codevoorbeelden](active-directory-code-samples.md).

- [ADAL voor .NET](https://msdn.microsoft.com/library/azure/mt417579.aspx): documentatie voor de .NET-verificatiebibliotheek.

### Graph API

- [Naslaginformatie over Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx): naslaginformatie over REST voor Azure Active Directory Graph API. [De interactieve naslaginformatie over Graph API weergeven](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Graph API-machtigingsbereiken](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): OAuth 2.0-machtigingsbereiken waarmee de toegang van een app tot directorygegevens in een tenant wordt beheerd.

### Verificatieprotocollen

- [Azure Active Directory-verificatieprotocollen](active-directory-protocols.md): meer informatie over de verschillende verificatie- en autorisatieprotocollen die worden ondersteund door Azure Active Directory.

- [Naslaginformatie over het SAML 2.0-protocol](active-directory-saml-protocol-reference.md): met het SAML 2.0-protocol beschikken gebruikers in toepassingen over eenmalige aanmelding.

- [Naslaginformatie over het OAuth 2.0-protocol](active-directory-protocols-oauth-code.md): met het OAuth 2.0-protocol kunt u toegang verlenen tot webtoepassingen en web-API's in uw Azure Active Directory-tenant.

- [Naslaginformatie over het OpenID Connect 1.0-protocol](active-directory-protocols-openid-connect-code.md): met het OpenID Connect 1.0-protocol kunt u OAuth 2.0 uitbreiden voor gebruik als verificatieprotocol.

- [WS-Federation 1.2-protocol](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory ondersteunt WS-Federation 1.2 vanaf de specificatie Webservices-federatie versie 1.2.

- [Ondersteund token en ondersteunde claimtypen](active-directory-token-and-claims.md): in deze handleiding worden de claims in de tokens SAML 2.0 en JSON Web Tokens (JWT) beschreven en geëvalueerd.

## Video's

### Ontwikkelen

In deze overzichtspresentaties over het ontwikkelen van apps met behulp van Azure Active Directory ziet u sprekers die rechtstreeks deel uitmaken van het technische team. De presentaties gaan over belangrijke onderwerpen, waaronder IDMaaS, verificatie, identiteitsfederatie en eenmalige aanmelding.

- [Microsoft Identity: plannen voor de toekomst](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Azure Active Directory: Identity Management als een service voor moderne toepassingen](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Moderne webtoepassingen ontwikkelen met Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Moderne systeemeigen toepassingen ontwikkelen met Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) is een videoserie die elke vrijdag wordt uitgezonden en waarin experts kort worden geïnterviewd (10-15 minuten) over diverse Azure-onderwerpen.  Gebruik de functie Servicefilter op de pagina om alle Azure Active Directory-video's te bekijken.

- [Azure Identity 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Azure Identity 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Azure Identity 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Sociaal

- [Active Directory-teamblog](http://blogs.technet.com/b/ad/): de nieuwste wereldwijde ontwikkelingen van Azure Active Directory.

- [Azure Active Directory Graph-teamblog](http://blogs.msdn.com/b/aadgraphteam): specifieke Azure Active Directory-informatie voor Graph API.

- [Cloudidentiteit](http://www.cloudidentity.net): ideeën over Identity Management als een service van een belangrijke PM van Azure Active Directory.  

- [Azure Active Directory op Twitter](https://twitter.com/azuread): Azure Active Directory-aankondigingen van 140 tekens of minder.



<!--HONumber=Jun16_HO2-->


