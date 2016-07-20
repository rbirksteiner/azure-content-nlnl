<properties
    pageTitle="Voorbeeld van Azure AD B2C | Microsoft Azure"
    description="De typen toepassingen die u kunt ontwikkelen in Azure Active Directory B2C Preview."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="dastrock"/>

# Azure Active Directory B2C Preview: Typen toepassingen

Azure Active Directory (Azure AD) B2C ondersteunt verificatie voor diverse moderne app-architecturen. Ze zijn allemaal gebaseerd op de protocollen volgens de industrienorm [OAuth 2.0](active-directory-b2c-reference-protocols.md) of [OpenID Connect](active-directory-b2c-reference-protocols.md). In dit document vindt u een korte beschrijving van de typen apps die u kunt maken, onafhankelijk van de taal of het platform waaraan u de voorkeur geeft. Het document geeft ook inzicht in geavanceerde scenario's voordat u [toepassingen gaat ontwikkelen](active-directory-b2c-overview.md#getting-started).

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## De basisbeginselen
Elke app die gebruikmaakt van Azure AD B2C moet via de [Azure Portal](https://portal.azure.com/) worden geregistreerd in uw [B2C-directory](active-directory-b2c-get-started.md). Tijdens het registratieproces van de app worden enkele waarden verzameld en toegewezen aan uw app:

- Een **toepassings-id** die de app op unieke wijze identificeert.
- Een **omleidings-URI** die kan worden gebruikt om antwoorden naar uw app terug te sturen.
- Alle andere scenariospecifieke waarden. Zie [Een app registreren](active-directory-b2c-app-registration.md) voor meer informatie.

Wanneer de app is geregistreerd, communiceert deze met Azure AD door verzoeken te sturen naar het Azure AD v2.0-eindpunt:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Elk verzoek dat naar Azure AD B2C wordt verzonden, geeft een **beleid** op. Een beleid regelt het gedrag van Azure AD. U kunt deze eindpunten ook gebruiken om een uiterst aanpasbare verzameling gebruikerservaringen te maken. Algemene beleidsregels zijn beleidsregels voor registratie, aanmelding en het bewerken van profielen. Als u niet bekend bent met beleidsregels, lees dan over het [uitbreidbaar beleidsframework](active-directory-b2c-reference-policies.md) van Azure AD B2C voordat u verder gaat.

De interactie van elke app met een v2.0-eindpunt volgt eenzelfde globaal patroon:

1. De app leidt de gebruiker naar het v2.0-eindpunt om een [beleid](active-directory-b2c-reference-policies.md) uit te voeren.
2. De gebruiker voltooit het beleid volgens de beleidsdefinitie.
4. De app ontvangt een soort beveiligingstoken van het v2.0-eindpunt.
5. De app gebruikt het beveiligingstoken om toegang te krijgen tot beveiligde gegevens of een beveiligde resource.
6. De resource-server valideert het beveiligingstoken om te controleren of toegang kan worden verleend.
7. Het beveiligingstoken wordt regelmatig door de app vernieuwd.

<!-- TODO: Need a page for libraries to link to -->
Deze stappen kunnen enigszins verschillen op basis van het type app dat u maakt. In open-source bibliotheken kunt u de details vinden.

## Web-apps
Voor web-apps (zoals .NET, PHP, Java, Ruby, Python en Node.js) die worden gehost op een server en worden geopend via een browser, ondersteunt Azure AD B2C [OpenID Connect](active-directory-b2c-reference-protocols.md) voor alle gebruikerservaringen. Hiertoe behoren aanmelden, registreren en profielbeheer. In de Azure AD B2C-implementatie van OpenID Connect initieert uw web-app deze gebruikerservaringen door verificatieaanvragen naar Azure AD te sturen. Het resultaat van de aanvraag is een `id_token`. Dit beveiligingstoken vertegenwoordigt de identiteit van de gebruiker. Het bevat ook informatie over de gebruiker in de vorm van claims:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

In de [Naslaginformatie over B2C-tokens](active-directory-b2c-reference-tokens.md) vindt u meer informatie over de typen tokens en claims die beschikbaar zijn voor een app.

In een web-app bestaat de uitvoering van een [beleid](active-directory-b2c-reference-policies.md) uit de volgende globale stappen:

![Afbeelding van web-app-banen](./media/active-directory-b2c-apps/webapp.png)

Validatie van het `id_token` met behulp van een openbare ondertekeningssleutel die is ontvangen van Azure AD is voldoende om de identiteit van de gebruiker te controleren. Hiermee wordt ook een sessiecookie ingesteld waarmee de gebruiker bij volgende pagina-aanvragen kan worden geïdentificeerd.

Als u wilt zien hoe dit scenario werkt, probeer dan een van de codevoorbeelden van web-app-aanmelding in de sectie [Aan de slag](active-directory-b2c-overview.md#getting-started).

Dit vergemakkelijkt niet alleen een eenvoudige aanmelding, maar soms heeft een webserver-app ook toegang nodig tot een back-endwebservice. In dat geval kan de web-app een iets andere [OpenID Connect-stroom](active-directory-b2c-reference-oidc.md) uitvoeren en tokens verkrijgen met behulp van autorisatiecodes en vernieuwingstokens. Dit scenario wordt beschreven in de volgende sectie over [Web-API's](#web-apis).

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## Web-API's
U kunt Azure AD B2C gebruiken om webservices te beveiligen, zoals de RESTful-web-API van uw app. Web-API's kunnen OAuth 2.0 gebruiken om hun gegevens te beveiligen. Ze kunnen ook inkomende HTTP-aanvragen verifiëren met behulp van tokens. De aanroeper van een web-API voegt een token toe in de autorisatie-header van een HTTP-aanvraag:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

De web-API kan het token vervolgens gebruiken om de identiteit van de API-aanroeper te verifiëren en om informatie over de aanroeper af te leiden uit de claims die in het token zijn gecodeerd. In de [Naslaginformatie over Azure AD B2C-tokens](active-directory-b2c-reference-tokens.md) vindt u meer informatie over de typen tokens en claims die beschikbaar zijn voor een app.

> [AZURE.NOTE]
    Op dit moment ondersteunt Azure AD B2C Preview alleen web-API's die worden gebruikt door hun eigen bekende clients. Zo zou uw volledige app bijvoorbeeld een iOS-app, een Android-app en een back-end web-API kunnen bevatten. Deze architectuur wordt volledig ondersteund. Op dit moment krijgt een partner-client, zoals een andere iOS-app, nog geen toegang tot dezelfde web-API. Alle onderdelen van de voltooide app moeten één toepassings-id delen.

Een web-API kan tokens ontvangen van tal van clients, waaronder web-apps, bureaublad- en mobiele apps, apps van één pagina, daemons aan serverzijde en andere web-API's. Hier volgt een voorbeeld van de volledige stroom voor een web-app die een web-API aanroept:

![Afbeelding van web-API van web-app-banen](./media/active-directory-b2c-apps/webapi.png)

Voor meer informatie over autorisatiecodes, vernieuwingstokens en de stappen voor het ophalen van tokens, leest u over het [OAuth 2.0-protocol](active-directory-b2c-reference-oauth-code.md).

Voor meer informatie over het beveiligen van een web-API met behulp van Azure AD B2C bekijkt u de web-API-zelfstudies in de sectie [Aan de slag](active-directory-b2c-overview.md#getting-started).

## Mobiele en systeemeigen apps
Apps die zijn geïnstalleerd op apparaten, zoals mobiele en bureaublad-apps, hebben dikwijls namens gebruikers toegang nodig tot back-endservices of web-API's. U kunt aangepaste ervaringen voor identiteitsbeheer aan uw systeemeigen apps toevoegen en op veilige wijze back-endservices aanroepen met behulp van Azure AD B2C en de [OAuth 2.0-autorisatiecodestroom](active-directory-b2c-reference-oauth-code.md).  

In deze stroom voert de app een [beleid](active-directory-b2c-reference-policies.md) uit en ontvangt de app een `authorization_code` van Azure AD wanneer de gebruiker het beleid heeft voltooid. De `authorization_code` vertegenwoordigt de machtiging van de app om back-endservices aan te roepen namens de gebruiker die op dat moment is aangemeld. De app kan vervolgens op de achtergrond de `authorization_code` uitwisselen voor een `id_token` en een `refresh_token`.  De app kan de `id_token` gebruiken om een back-end-web-API in HTTP-aanvragen te verifiëren. Het `refresh_token` kan ook worden gebruikt om nieuwe `id_token` te verkrijgen wanneer de oudere zijn verlopen.

> [AZURE.NOTE]
    Azure AD B2C Preview ondersteunt momenteel alleen id-tokens die worden gebruikt voor toegang tot de eigen back-endwebservice van de app. Zo zou uw volledige app bijvoorbeeld een iOS-app, een Android-app en een back-end web-API kunnen bevatten. Deze architectuur wordt volledig ondersteund. Uw iOS-app met behulp van OAuth 2.0-toegangstokens toegang geven tot de web-API van een partner wordt nog niet ondersteund. Alle onderdelen van de voltooide app moeten één toepassings-id delen.

![Afbeelding van banen voor systeemeigen web-app](./media/active-directory-b2c-apps/native.png)

## Current preview limitations (Engelstalig)
Azure AD B2C Preview biedt momenteel geen ondersteuning voor de volgende typen apps, maar ondersteuning hiervan zal op tijd algemeen beschikbaar zijn. Aanvullende voorwaarden en beperkingen met betrekking tot Azure AD B2C Preview worden beschreven in [Voorwaarden en beperkingen](active-directory-b2c-limitations.md).

### Apps met één pagina (JavaScript)
Veel moderne apps hebben een app-front-end van één pagina die voornamelijk in JavaScript is geschreven. Dikwijls wordt een framework, zoals AngularJS, Ember.js of Durandal gebruikt. De algemeen beschikbaar Azure AD-service ondersteunt deze apps met behulp van de impliciete OAuth 2.0-stroom. Deze stroom is echter nog niet beschikbaar in Azure AD B2C. Dat komt binnenkort.

### Daemons/apps aan serverzijde
Ook apps die langlopende processen bevatten of die werken zonder de aanwezigheid van een gebruiker, hebben een manier nodig om toegang te krijgen tot beveiligde resources, zoals web-API's. Deze apps kunnen tokens verifiëren en verkrijgen door de identiteit van de app (in plaats van een gedelegeerde gebruikersidentiteit) te gebruiken en door de referentiestroom van de OAuth 2.0-client te gebruiken.

Deze stroom wordt momenteel niet ondersteund door Azure AD B2C. Deze apps kunnen pas tokens verkrijgen nadat een interactieve gebruikersstroom is voorgekomen. De clientreferentiestroom wordt naar verwachting in de nabije toekomst toegevoegd.

### Web-API-ketens (namens-stroom)
Veel architecturen bevatten een web-API die een andere downstream web-API moet aanroepen, waarbij beide zijn beveiligd door Azure AD B2C. Dit scenario is gemeenschappelijk in systeemeigen clients met een web-API-back-end. Vervolgens wordt een onlineservice van Microsoft aangeroepen, zoals de Azure AD Graph API.

Dit scenario met web-API-keten kan worden ondersteund met behulp van de OAuth 2.0 JWT bearer-referentietoekenning, ook wel de namens-stroom genoemd.  De namens-stroom is momenteel echter niet geïmplementeerd in Azure AD B2C Preview.



<!--HONumber=Jun16_HO2-->


