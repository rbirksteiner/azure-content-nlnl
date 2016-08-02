<properties
    pageTitle="Uw eerste API beheren in Azure API Management | Microsoft Azure"
    description="Informatie over het maken van API's, het toevoegen van bewerkingen en het aan de slag gaan met API Management."
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/25/2016"
    ms.author="sdanie"/>

# Uw eerste API beheren in Azure API Management

## <a name="overview"> </a>Overzicht

In deze handleiding wordt getoond hoe u snel aan de slag kunt met Azure API Management en uw eerste API-aanroep kunt maken.

## <a name="concepts"> </a>Wat is Azure API Management?

U kunt Azure API Management gebruiken om elke back-end te nemen en een volwaardig API-programma te starten dat hierop is gebaseerd.

Algemene scenario's omvatten de volgende:

* **Mobiele infrastructuur beveiligen** door het beperken van toegang met API-sleutels, het voorkomen van DOS-aanvallen door middel van beperking, of het gebruiken van geavanceerd beveiligingsbeleid zoals validatie van JWT-tokens.
* **ISV-partnerecosystemen inschakelen** door snelle onboarding van partners aan te bieden via de ontwikkelaarsportal en een API-façade te bouwen om los te koppelen van interne implementaties die niet gereed zijn voor gebruik door partners.
* **Een intern API-programma uitvoeren** door het aanbieden van een centrale locatie voor de organisatie om te communiceren over de beschikbaarheid en de meest recente wijzigingen in API's, toegang te beperken op basis van organisatieaccounts. Dit alles is op basis van een veilig kanaal tussen de API-gateway en de back-end.


Het systeem bestaat uit de volgende onderdelen:

* De **API-gateway** is het eindpunt waarmee:
  * API-aanroepen worden geaccepteerd en naar uw back-ends worden doorgestuurd.
  * API-sleutels, JWT-tokens, certificaten en andere referenties worden geverifieerd.
  * Quota voor gebruik en frequentielimieten worden afgedwongen.
  * Uw API snel kan worden getransformeerd zonder codewijzigingen.
  * Antwoorden van de back-end in de cache worden opgeslagen indien ingesteld.
  * Aanroepmetagegevens worden voor analysedoeleinden aan het logboek toegevoegd.

* De **publicatieportal** is de beheerinterface waar u uw API-programma instelt. Gebruik deze voor het volgende:
    * API-schema definiëren of importeren.
    * API's verpakken in producten.
    * Beleidsregels instellen zoals quota of transformaties voor de API's.
    * Inzicht krijgen van analytische gegevens.
    * Gebruikers beheren.

* De **ontwikkelaarsportal** fungeert als de belangrijkste aanwezigheid op het web voor ontwikkelaars. Ze kunnen hier het volgende:
    * API-documentatie lezen.
    * Een API uitproberen via de interactieve console.
    * Een account maken en zich abonneren om API-sleutels op te halen.
    * Analytische gegevens openen over hun eigen gebruik.


## <a name="create-service-instance"> </a>Een API Management-exemplaar maken

>[AZURE.NOTE] U hebt een Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis account maken. Zie [Gratis proefversie van Azure][] voor meer informatie.

De eerste stap voor het werken met API Management is het maken van een service-exemplaar. Meld u aan bij de [klassieke Azure-portal][] en klik op **Nieuw**, **App Services**, **API Management**, **Maken**.

![Nieuw API Management-exemplaar][api-management-create-instance-menu]

Geef voor **URL** een unieke subdomeinnaam op om te gebruiken voor de service-URL.

Kies het gewenste **Abonnement** en de gewenste **Regio** voor uw service-exemplaar. Nadat u deze hebt geselecteerd, klikt u op de knop **Volgende**.

![Nieuwe API Management-service][api-management-create-instance-step1]

Voer **Contoso Ltd.** in voor de **Organisatienaam** en voer uw e-mailadres in het veld **E-mail beheerder** in.

>[AZURE.NOTE] Dit e-mailadres wordt gebruikt voor meldingen van het API Management-systeem. Zie voor meer informatie [Meldingen en e-mailsjablonen configureren in Azure API Management][].

![Nieuwe API Management-service][api-management-create-instance-step2]

Service-exemplaren van API Management zijn beschikbaar in drie categorieën: Developer, Standard en Premium. Standaard worden nieuwe service-exemplaren van API Management in de categorie Developer gemaakt. Als u de categorie Standard of Premium wilt selecteren, schakelt u het selectievakje **Geavanceerde instellingen** in en selecteert u de gewenste categorie in het volgende scherm.

>[AZURE.NOTE] De categorie Developer is voor het ontwikkeling, tests en pilots van API-programma's waarbij grote beschikbaarheid niet van belang is. In de categorieën Standard en Premium kunt u het aantal gereserveerde eenheden schalen om meer verkeer te kunnen verwerken. De categorieën Standard en Premium bieden de meeste verwerkingskracht en de beste prestaties voor uw API Management-service. U kunt elke categorie gebruiken om deze zelfstudie te voltooien. Zie voor meer informatie over API Management-categorieën [API Management-prijzen][].

Schakel het selectievakje in om uw service-exemplaar te maken.

![Nieuwe API Management-service][api-management-instance-created]

Zodra het service-exemplaar is gemaakt, is de volgende stap het maken of importeren van een API.

## <a name="create-api"> </a>Een API importeren

Een API bestaat uit een reeks bewerkingen die vanuit een clienttoepassing kunnen worden aangeroepen. API-bewerkingen worden geproxied naar bestaande webservices.

API's kunnen handmatig worden gemaakt (en bewerkingen kunnen handmatig worden toegevoegd) of ze kunnen worden geïmporteerd. In deze zelfstudie importeren we de API voor een voorbeeldrekenmachinewebservice die wordt geleverd door Microsoft en wordt gehost in Azure.

>[AZURE.NOTE] Zie voor instructies over het maken van een API en het handmatig toevoegen van bewerkingen [API's maken](api-management-howto-create-apis.md) en [Bewerkingen toevoegen aan een API](api-management-howto-add-operations.md).

API's worden geconfigureerd in de publicatieportal, die wordt geopend via de klassieke Azure-portal. Klik voor het bereiken van de publicatieportal op **Beheren** in de klassieke Azure-portal voor uw API Management-service.

![Publicatieportal][api-management-management-console]

Klik voor het importeren van de rekenmachine-API op **API's** in het menu **API Management** aan de linkerkant en klik vervolgens op **API importeren**.

![Knop API importeren][api-management-import-api]

Voer de volgende stappen uit om de rekenmachine-API te configureren:

1. Klik op **Van URL**, voer **http://calcapi.cloudapp.net/calcapi.json** in het tekstvak **URL specificatiedocument** in en klik op het keuzerondje **Swagger**.
2. Typ **calc** in het tekstvak **Achtervoegsel URL web-API**.
3. Klik in het vak **Producten (optioneel)** en kies **Starter**.
4. Klik op **Opslaan** om de API te importeren.

![Nieuwe API toevoegen][api-management-import-new-api]

>[AZURE.NOTE] In **API Management** wordt momenteel zowel versie 1.2 als versie 2.0 van het Swagger-document voor import ondersteund. Hoewel in de [Swagger 2.0-specificatie](http://swagger.io/specification) wordt aangegeven dat eigenschappen `host`, `basePath` en `schemes` optioneel zijn, **MOET** uw Swagger 2.0-document deze eigenschappen bevatten, anders wordt het niet geïmporteerd. 

Zodra de API is geïmporteerd, wordt de overzichtspagina voor de API weergegeven in de publicatieportal.

![API-overzicht][api-management-imported-api-summary]

De API-sectie bevat meerdere tabbladen. Op het tabblad **Overzicht** worden basismetrieken en informatie over de API weergegeven. Het tabblad [Instellingen](api-management-howto-create-apis.md#configure-api-settings) wordt gebruikt om de configuratie voor een API te bekijken en te bewerken. Het tabblad [Bewerkingen](api-management-howto-add-operations.md) wordt gebruikt om de bewerkingen van de API te beheren. Het tabblad **Beveiliging** kan worden gebruikt om gatewayverificatie voor de back-endserver te configureren met behulp van basisverificatie of [wederzijdse certificaatverificatie](api-management-howto-mutual-certificates.md), en om [gebruikersautorisatie met behulp van OAuth 2.0](api-management-howto-oauth2.md) te configureren.  Het tabblad **Problemen** wordt gebruikt om problemen te bekijken die zijn gemeld door de ontwikkelaars die uw API's gebruiken. Het tabblad **Producten** wordt gebruikt voor het configureren van de producten die deze API bevatten.

Standaard wordt elk API Management-exemplaar geleverd met twee voorbeeldproducten:

-   **Starter**
-   **Onbeperkt**

In deze zelfstudie is de basisrekenmachine-API toegevoegd aan het Starter-product toen de API werd geïmporteerd.

Voor het maken van aanroepen naar een API moeten ontwikkelaars zich eerst abonneren op een product dat hen toegang geeft tot de API. Ontwikkelaars kunnen zich abonneren op producten in de ontwikkelaarsportal, of beheerders kunnen ontwikkelaars abonneren op producten in de publicatieportal. U bent een beheerder omdat u het API Management-exemplaar hebt gemaakt in de vorige stappen in de zelfstudie, dus u bent standaard al geabonneerd op elk product.

## <a name="call-operation"> </a>Een bewerking aanroepen vanuit de ontwikkelaarsportal

Bewerkingen kunnen rechtstreeks vanuit de ontwikkelaarsportal worden aangeroepen. Deze biedt een gemakkelijke manier om de bewerkingen van een API te bekijken en te testen. In deze zelfstudiestap roept u de bewerking **Twee gehele getallen toevoegen** van de basisrekenmachine-API aan. Klik op **ontwikkelaarsportal** in het menu rechtsboven in de publicatieportal.

![Ontwikkelaarsportal][api-management-developer-portal-menu]

Klik op **API's** in het bovenste menu en klik vervolgens op **Basisrekenmachine** om de beschikbare bewerkingen te bekijken.

![Ontwikkelaarsportal][api-management-developer-portal-calc-api]

Noteer de voorbeeldbeschrijvingen en -parameters die samen met de API en de bewerkingen zijn geïmporteerd, om documentatie te bieden voor de ontwikkelaars die deze bewerking gaan gebruiken. Deze beschrijvingen kunnen ook worden toegevoegd wanneer bewerkingen handmatig worden toegevoegd.

Als u de bewerking **Twee gehele getallen toevoegen** wilt aanroepen, klikt u op **Probeer het nu**.

![Probeer het nu][api-management-developer-portal-calc-api-console]

U kunt enkele waarden invoeren voor de parameters of de standaardwaarden behouden. Klik vervolgens op **Verzenden**.

![HTTP Get][api-management-invoke-get]

Nadat een bewerking is aangeroepen, worden in de ontwikkelaarsportal de **antwoordstatus**, de **antwoordheaders** en eventuele **antwoordinhoud** weergegeven.

![Antwoord][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Analytische gegevens bekijken

Als u analytische gegevens voor de basisrekenmachine wilt bekijken, gaat u terug naar de publicatieportal door **Beheren** te selecteren in het menu rechtsboven in de ontwikkelaarsportal.

![Beheren][api-management-manage-menu]

De standaardweergave voor de publicatieportal is het **Dashboard**, waarin een overzicht van uw API Management-exemplaar wordt gegeven.

![Dashboard][api-management-dashboard]

Beweeg de muisaanwijzer over de grafiek voor **Basisrekenmachine** om de specifieke metrische gegevens te zien voor het gebruik van de API voor een bepaalde periode.

>[AZURE.NOTE] Als u geen lijnen in uw grafiek ziet, gaat u terug naar de ontwikkelaarsportal en voert u enkele aanroepen in de API uit. Wacht enkele ogenblikken en ga dan terug naar het dashboard.

Klik op **Details weergeven** om de overzichtspagina voor de API te bekijken, met inbegrip van een grotere versie van de weergegeven metrische gegevens.

![Analytische gegevens][api-management-mouse-over]

![Samenvatting][api-management-api-summary-metrics]

Voor gedetailleerde metrische gegevens en rapporten klikt u op **Analytische gegevens** in het menu **API Management** aan de linkerkant.

![Overzicht][api-management-analytics-overview]

De sectie **Analytische gegevens** bevat de volgende vier tabbladen:

-   **In één oogopslag** biedt algemene metrische gegevens voor gebruik en status, evenals de belangrijkste ontwikkelaars, producten, API's en bewerkingen.
-   **Gebruik** biedt een diepgaande blik op API-aanroepen en bandbreedte, met inbegrip van een geografische weergave.
-   **Status** richt zich op statuscodes, succespercentages van de cache, reactietijden, en reactietijden van de API en de service.
-   **Activiteit** biedt rapporten die inzoomen op de specifieke activiteit per ontwikkelaar, product, API en bewerking.

## <a name="next-steps"> </a>Volgende stappen

-   Bekijk de andere onderwerpen in de zelfstudie [Aan de slag met geavanceerde API-configuratie][].

[Gratis proefversie van Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Een API Management-exemplaar maken]: #create-service-instance
[Een API maken]: #create-api
[Een bewerking toevoegen]: #add-operation
[De nieuwe API toevoegen aan een product]: #add-api-to-product
[Abonneren op het product dat de API bevat]: #subscribe
[Een bewerking aanroepen vanuit de ontwikkelaarsportal]: #call-operation
[Analytische gegevens bekijken]: #view-analytics
[Volgende stappen]: #next-steps


[Ontwikkelaarsaccounts in Azure API Management beheren]: api-management-howto-create-or-invite-developers.md
[API-instellingen configureren]: api-management-howto-create-apis.md#configure-api-settings
[Meldingen en e-mailsjablonen configureren in Azure API Management]: api-management-howto-configure-notifications.md
[Antwoorden]: api-management-howto-add-operations.md#responses
[Een product maken en publiceren]: api-management-howto-add-products.md
[Aan de slag met geavanceerde API-configuratie]: api-management-get-started-advanced.md
[API Management-prijzen]: http://azure.microsoft.com/pricing/details/api-management/

[klassieke Azure-portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png



<!--HONumber=Jun16_HO2-->


