<properties
    pageTitle="Een query uitvoeren in uw Azure Search-index met behulp van de REST-API | Gehoste service voor zoeken in de cloud"
    description="Een zoekquery samenstellen in Azure Search en gebruikmaken van zoekparameters om zoekresultaten te filteren en te sorteren."
    services="search"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"/>

# Een query uitvoeren in uw Azure Search-index met behulp van de REST-API
> [AZURE.SELECTOR]
- [Overzicht](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

In dit artikel wordt beschreven hoe u een query uitvoert in uw index met behulp van de [Azure Search REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Voordat u deze procedure begint, moet u al [een Azure Search-index hebben gemaakt](search-what-is-an-index.md) en moet deze index [gevuld zijn met gegevens](search-what-is-data-import.md).

## I. De query api-sleutel voor de Azure Search-service vaststellen
Een belangrijk onderdeel van elke zoekbewerking in de REST-API van Azure Search is de *api-sleutel* die is gegenereerd voor de service die u hebt ingericht. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

1. Om de api-sleutels van uw service te vinden, moet u inloggen op de [Azure Portal](https://portal.azure.com/)
2. Ga naar de blade van uw Azure Search-service
3. Klik op het pictogram "Sleutels"

Uw service heeft zowel *administratorsleutels* als *querysleutels*.

 - De primaire en secundaire *administratorsleutels* verlenen volledige rechten voor alle bewerkingen, inclusief de mogelijkheid voor het beheren van de service, het maken en verwijderen van indexen, indexeerfuncties en gegevensbronnen. Er zijn twee sleutels, zodat u de secundaire sleutel kunt gebruiken als u de primaire sleutel opnieuw aan het genereren bent en vice versa.
 - Uw *querysleutels* geven alleen-lezentoegang tot indexen en documenten. Deze sleutels worden doorgaans verleend aan clienttoepassingen die zoekaanvragen verlenen.

U kunt gebruikmaken van een van de query-sleutel om een query in een index uit te voeren. De administratorsleutels kunnen ook worden gebruikt voor query's, maar u moet gebruikmaken van een querysleutel in de toepassingscode, aangezien dit het [principe van minimale bevoegdheden](https://en.wikipedia.org/wiki/Principle_of_least_privilege) volgt.

## II. Uw query formuleren
Er zijn twee manieren om [in de index te zoeken met behulp van de REST-API](https://msdn.microsoft.com/library/azure/dn798927.aspx). De ene manier is om een HTTP POST-aanvraag uit te geven waarbij uw queryparameters worden gedefinieerd in een JSON-object in de aanvraagtekst. De andere manier is om een HTTP GET-aanvraag uit te geven waarbij uw queryparameters worden gedefinieerd in de aanvraag-URL. POST heeft meer [soepele limieten](https://msdn.microsoft.com/library/azure/dn798927.aspx) met betrekking tot de grootte van queryparameters dan GET. Daarom wordt u aangeraden POST te gebruiken, tenzij er speciale omstandigheden zijn waarin het gebruik van GET beter zou zijn.

Voor zowel POST als GET moet u in de aanvraag-URL de *servicenaam*, de *indexnaam* en de juiste *API-versie* (de huidige API-versie is `2015-02-28` op het moment van publicatie van dit document) opgeven. Voor GET geeft u in de *querytekenreeks* aan het einde van de URL de queryparameters op. Hieronder vindt u de URL-indeling:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

De indeling voor POST is hetzelfde, maar met alleen de api-versie in de queryreeksparameters.



#### Voorbeelden van query 's

Hier volgen een paar voorbeeldquery's op een index met de naam "hotels". Deze query's worden weergegeven in zowel de GET als POST-indeling.

Zoeken in de hele index op de term 'budget' en alleen het veld `hotelName` retourneren:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

Een filter toepassen op de index om te zoeken naar hotels goedkoper dan €150 per nachten de `hotelId` en `description` retourneren:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Zoeken in de gehele index, ordenen op een bepaald veld (`lastRenovationDate`) in aflopende volgorde, de twee bovenste resultaten selecteren en alleen `hotelName` and `lastRenovationDate` weergeven:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## III. De HTTP-aanvraag verzenden
Nu u uw query hebt geformuleerd als onderdeel van uw HTTP-aanvraag-URL (voor GET) of hoofdtekst (voor POST), kunt u uw aanvraagheaders definiëren en uw query verzenden.

#### Aanvragen en aanvraagheaders
U moet twee aanvraagheaders definiëren voor GET en drie voor POST:
1. De `api-key`-header moet worden ingesteld op de querysleutel uit stap I hierboven. U kunt ook een administratorsleutel gebruiken voor de `api-key`-header, maar het wordt aanbevolen de querysleutel te gebruiken aangezien deze exclusieve alleen-lezentoegang biedt tot indexen en documenten.
2. De `Accept`-header moet worden ingesteld op `application/json`.
3. Alleen voor POST moet de `Content-Type`-header ook worden ingesteld op `application/json`.

Hieronder vindt u een HTTP GET-aanvraag voor de index "hotels" met behulp van de Azure Search REST-API met een eenvoudige query waarin wordt gezocht naar de term "motel":

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

Dit is dezelfde voorbeeldquery voor HTTP POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Als een queryaanvraag is gelukt, wordt de statuscode `200 OK` weergegeven. De zoekresultaten worden geretourneerd als JSON in de hoofdtekst van het antwoord. Hier ziet u hoe de resultaten voor de bovenstaande query's eruit zien, ervan uitgaande dat de index "hotels" is gevuld met de voorbeeldgegevens in [Gegevens importeren in Azure Search met behulp van de REST-API](search-import-data-rest-api.md) (de JSON is voor de duidelijkheid geformatteerd).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Ga naar de sectie "Antwoord" van [Documenten zoeken](https://msdn.microsoft.com/library/azure/dn798927.aspx) voor meer informatie. Zie [HTTP-statuscodes (Azure Search)](https://msdn.microsoft.com/library/azure/dn798925.aspx) voor meer informatie over andere HTTP-statuscodes die kunnen worden geretourneerd in geval van storing.



<!--HONumber=Jun16_HO2-->


