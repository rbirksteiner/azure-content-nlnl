<properties 
    pageTitle="Belangrijke concepten van API Management" 
    description="Meer informatie over API's, producten, rollen, groepen en andere belangrijke concepten van API Management." 
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
    ms.date="05/24/2016" 
    ms.author="sdanie"/>

#Wat is API Management?

API Management helpt organisaties bij het publiceren van API's naar externe, partner- en interne ontwikkelaars om het potentieel van hun gegevens en services te ontsluiten. Veel bedrijven willen hun activiteiten uitbreiden als een digitaal platform, waarbij nieuwe kanalen worden gemaakt, nieuwe klanten worden gevonden en grotere betrokkenheid met bestaande klanten wordt bereikt. API Management biedt de kerncompetenties voor een geslaagd API-programma via ontwikkelaarsbetrokkenheid, zakelijke inzichten, analytische gegevens, beveiliging en bescherming.

Bekijk de volgende video voor een overzicht van Azure API Management en leer hoe u API Management gebruikt om vele functies aan uw API toe te voegen, inclusief toegangsbeheer, frequentiebeperking, bewaking, logboekregistratie en het in de cache opslaan van antwoorden, met minimale inspanning.

> [AZURE.VIDEO azure-api-management-overview]

Beheerders maken API's om API Management te gebruiken. Elke API bestaat uit een of meer bewerkingen en elke API kan aan een of meer producten worden toegevoegd. Als ontwikkelaars een API willen gebruiken, abonneren ze zich op een product dat die API bevat en vervolgens kunnen ze de bewerking van de API aanroepen. Deze is onderworpen aan gebruiksbeleidsregels die mogelijk van kracht zijn.

In dit onderwerp wordt een overzicht gegeven van de belangrijkste concepten van API Management.

>[AZURE.NOTE] Zie voor meer informatie het technische PDF-document [Cloud-based API Management: Harnessing the Power of APIs](http://j.mp/ms-apim-whitepaper) (API Management in de cloud: de kracht van API's aanwenden). In dit inleidende technische document over API Management door CITO Research wordt het volgende besproken: 
>
> - Algemene API-vereisten en -uitdagingen
>     - API's loskoppelen en façades presenteren
>     - Ontwikkelaars snel aan de slag helpen
>     - Toegang beveiligen
>     - Analytische en metrische gegevens
> - Controle en inzicht verkrijgen met een API Management-platform
> - Cloud- versus on-premises oplossingen gebruiken
> - Azure API Management

## <a name="apis"> </a>API's en bewerkingen

API's vormen de basis van een service-exemplaar van API Management. Elke API vertegenwoordigt een reeks bewerkingen die beschikbaar zijn voor ontwikkelaars. Elke API bevat een verwijzing naar de back-endservice waarmee de API wordt geïmplementeerd, en de bewerkingen zijn toegewezen aan de bewerkingen die zijn geïmplementeerd door de back-endservice. Bewerkingen in API Management zijn zeer goed te configureren, met controle over URL-toewijzing, query- en padparameters, aanvraag- en antwoordinhoud en het in de cache opslaan van bewerkingsantwoorden. Beleidsregels voor frequentielimiet, quota en IP-restrictie kunnen ook op API-niveau of op het niveau van de afzonderlijke bewerking worden geïmplementeerd.

Zie voor meer informatie [API's maken][] en [Bewerkingen toevoegen aan een API][].


## <a name="products"> </a> Producten

Producten zijn de manier waarop de API's worden opgehaald voor ontwikkelaars. Producten in API Management hebben een of meer API's en worden geconfigureerd met een titel, beschrijving en gebruiksvoorwaarden. Producten kunnen **open** of **beveiligd** zijn. Voor beveiligde producten is een abonnement nodig voordat ze kunnen worden gebruikt, terwijl open producten zonder abonnement kunnen worden gebruikt. Wanneer een product gereed is voor gebruik door ontwikkelaars, kan het worden gepubliceerd. Zodra het is gepubliceerd, kan het worden bekeken (en in het geval van beveiligde producten kan er een abonnement op worden genomen) door ontwikkelaars. Goedkeuring van abonnementen wordt geconfigureerd op productniveau en er kan beheerdersgoedkeuring voor vereist zijn of abonnementen kunnen automatisch worden goedgekeurd.

Groepen worden gebruikt voor het beheren van de zichtbaarheid van producten voor ontwikkelaars. Voor producten wordt zichtbaarheid aan groepen verleend en ontwikkelaars kunnen de producten bekijken en zich abonneren op de producten die zichtbaar zijn voor de groepen waartoe de ontwikkelaars behoren. 

Zie voor meer informatie [Een product maken en publiceren][] en de volgende video.

> [AZURE.VIDEO using-products]

## <a name="groups"> </a> Groepen

Groepen worden gebruikt voor het beheren van de zichtbaarheid van producten voor ontwikkelaars. API Management heeft de volgende onveranderbare systeemgroepen.

-   **Beheerders** - Azure-abonnementbeheerders zijn lid van deze groep. Beheerders beheren service-exemplaren van API Management, door het maken van de API's, bewerkingen en producten die door ontwikkelaars worden gebruikt.
-   **Ontwikkelaars** - Geverifieerde gebruikers van de ontwikkelaarsportal vallen in deze groep. Ontwikkelaars zijn de klanten die toepassingen bouwen met uw API's. Ontwikkelaars krijgen toegang tot de ontwikkelaarsportal en bouwen toepassingen waarmee de bewerkingen van een API worden aangeroepen.
-   **Gasten** - Niet-geverifieerde gebruikers van de ontwikkelaarsportal, zoals potentiële klanten die de ontwikkelaarsportal van een API Management-exemplaar bezoeken, vallen in deze groep. Ze kunnen bepaalde alleen-lezentoegang krijgen, zoals de mogelijkheid om API's te bekijken maar niet om ze aan te roepen.

Naast deze systeemgroepen kunnen beheerders aangepaste groepen maken of [gebruikmaken van externe groepen in gekoppelde Azure Active Directory-tenants](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). Aangepaste en externe groepen kunnen naast systeemgroepen worden gebruikt om ontwikkelaars zichtbaarheid van en toegang tot API-producten te geven. U kunt bijvoorbeeld één aangepaste groep maken voor ontwikkelaars die zijn gekoppeld aan een specifieke partnerorganisatie en hen toegang geven tot de API's vanuit een product dat alleen relevante API's bevat. Een gebruiker kan lid zijn van meerdere groepen.

Zie voor meer informatie [Groepen maken en gebruiken][].

## <a name="developers"> </a> Ontwikkelaars

Ontwikkelaars vertegenwoordigen de gebruikersaccounts in een service-exemplaar van API Management. Ontwikkelaars kunnen worden gemaakt of worden uitgenodigd voor deelname door beheerders, of ze kunnen zich registreren vanuit de [ontwikkelaarsportal][]. Elke ontwikkelaar is lid van een of meer groepen en mag zich abonneren op de producten die zichtbaarheid aan deze groepen verlenen.

Als ontwikkelaars zich op een product abonneren, krijgen ze de primaire en secundaire sleutel voor het product. Deze sleutel wordt gebruikt bij het aanroepen van de API's van het product.

Zie voor meer informatie [Ontwikkelaars maken of uitnodigen][] en [Groepen koppelen aan ontwikkelaars][].

## <a name="policies"> </a> Beleidsregels

Beleidsregels zijn een krachtige mogelijkheid van API Management waarmee de uitgever het gedrag van de API via configuratie kan wijzigen. Beleidsregels zijn een verzameling instructies die sequentieel worden uitgevoerd op de aanvraag of het antwoord van een API. Populaire instructies omvatten indelingsconversie van XML in JSON en beperking van de aanroepfrequentie om de hoeveelheid inkomende aanroepen van een ontwikkelaar te beperken. Er zijn nog vele andere beleidsregels beschikbaar.

Beleidsexpressies kunnen worden gebruikt als kenmerkwaarden of tekstwaarden in API Management-beleidsregels, tenzij het beleid iets anders aangeeft. Sommige beleidsregels, zoals de beleidsregels [Stroom controleren](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) en [Variabele instellen](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable), zijn gebaseerd op beleidsexpressies. Zie voor meer informatie [Geavanceerde beleidsregels](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies), [Beleidsexpressies](https://msdn.microsoft.com/library/azure/dn910913.aspx) en bekijk de volgende video.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

Zie [Naslaginformatie over beleid][] voor een volledige lijst met API Management-beleidsregels. Zie [API Management-beleidsregels][]voor meer informatie over het gebruiken en configureren van beleidsregels. Zie [Geavanceerde productinstellingen maken en configureren][] voor een zelfstudie over het maken van een product met beleidsregels voor frequentielimiet en quotum. Zie de volgende video voor een demo.

> [AZURE.VIDEO rate-limits-and-quotas]

## <a name="developer-portal"> </a> ontwikkelaarsportal

De ontwikkelaarsportal is de plek waar ontwikkelaars meer informatie kunnen vinden over uw API's, bewerkingen kunnen bekijken en aanroepen en zich op producten kunnen abonneren. Potentiële klanten kunnen de ontwikkelaarsportal bezoeken, API's en bewerkingen bekijken en zich registreren. De URL voor uw ontwikkelaarsportal bevindt zich in het dashboard in de klassieke Azure-portal voor uw service-exemplaar van API Management.

U kunt het uiterlijk van uw ontwikkelaarsportal aanpassen door aangepaste inhoud toe te voegen, stijlen aan te passen en uw huisstijl toe te voegen.

## API Management en de API-economie

Bekijk de volgende presentatie van de conferentie Microsoft Ignite 2015 voor meer informatie over API Management.

> [AZURE.VIDEO microsoft-ignite-2015-azure-api-management-and-the-api-economy]

[API's en bewerkingen]: #apis
[Producten]: #products
[Groepen]: #groups
[Ontwikkelaars]: #developers
[Beleidsregels]: #policies
[ontwikkelaarsportal]: #developer-portal

[API's maken]: api-management-howto-create-apis.md
[Bewerkingen toevoegen aan een API]: api-management-howto-add-operations.md
[Een product maken en publiceren]: api-management-howto-add-products.md
[Groepen maken en gebruiken]: api-management-howto-create-groups.md
[Groepen koppelen aan ontwikkelaars]: api-management-howto-create-groups.md#associate-group-developer
[Geavanceerde productinstellingen maken en configureren]: api-management-howto-product-with-rules.md
[Ontwikkelaars maken of uitnodigen]: api-management-howto-create-or-invite-developers.md
[Naslaginformatie over beleid]: api-management-policy-reference.md
[API Management-beleidsregels]: api-management-howto-policies.md
[Service-exemplaar van API Management maken]: api-management-get-started.md#create-service-instance



 



<!--HONumber=Jun16_HO2-->


