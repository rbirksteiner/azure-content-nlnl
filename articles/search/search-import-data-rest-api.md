<properties
    pageTitle="Gegevens uploaden naar Azure Search met behulp van de REST-API | Microsoft Azure | Gehoste service voor zoeken in de cloud"
    description="Informatie over het uploaden van gegevens naar een index in Azure Search met behulp van de REST-API"
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"/>

# Gegevens uploaden naar Azure Search met behulp van de REST-API
> [AZURE.SELECTOR]
- [Overzicht](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)

In dit artikel wordt beschreven hoe u met behulp van de [Azure Search REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) gegevens in een Azure Search-index importeert.

Voordat u deze procedure begint, moet u al [een Azure Search-index hebben gemaakt](search-what-is-an-index.md).

Om documenten met behulp van de REST-API naar uw index te pushen, doet u een HTTP POST-aanvraag bij de eindpunt-URL van uw index. De hoofdtekst van de HTTP-aanvraag is een JSON-object met de documenten die moeten worden toegevoegd, gewijzigd of verwijderd.

## I. De admin api-sleutel voor de Azure Search-service vaststellen
Als u met behulp van de REST-API een HTTP-aanvraag bij uw service doet, moet *elke* API-aanvraag de api-sleutel bevatten die is gegenereerd door de zoekservice die u hebt ingericht.  Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

1. Om de api-sleutels van uw service te vinden, moet u inloggen op de [Azure Portal](https://portal.azure.com/)
2. Ga naar de blade van uw Azure Search-service
3. Klik op het pictogram "Sleutels"

Uw service heeft zowel *administratorsleutels* als *querysleutels*.

  - De primaire en secundaire *administratorsleutels* verlenen volledige rechten voor alle bewerkingen, inclusief de mogelijkheid voor het beheren van de service, het maken en verwijderen van indexen, indexeerfuncties en gegevensbronnen. Er zijn twee sleutels, zodat u de secundaire sleutel kunt gebruiken als u de primaire sleutel opnieuw aan het genereren bent en vice versa.
  - Uw *querysleutels* geven alleen-lezentoegang tot indexen en documenten. Deze sleutels worden doorgaans verleend aan clienttoepassingen die zoekaanvragen verlenen.

Als u gegevens in een index wilt importeren, kunt u de primaire of secundaire administratorsleutel gebruiken.

## II. Bepalen welke indexeerbewerking u moet gebruiken
Wanneer u de REST-API gebruikt, doet u HTTP POST-aanvragen met JSON-aanvraagtekst bij de eindpunt-URL van uw Azure Search-index. Het JSON-object in de hoofdtekst van uw HTTP-aanvraag bevat een enkele JSON-matrix met de naam ''waarde''. Deze matrix bevat JSON-objecten die de documenten vertegenwoordigen die u aan uw index wilt toevoegen, wilt bijwerken of wilt wijzigen.

Elk JSON-object in de matrix ''waarde'' vertegenwoordigt een document dat moet worden geïndexeerd. Elk van deze objecten bevat de sleutel van het document en bepaalt de gewenste indexeringsbewerking (uploaden, samenvoegen, verwijderen, enzovoort). Afhankelijk van welke van de onderstaande bewerkingen u kiest, moet u slechts bepaalde velden voor elk document opnemen:

@search.action | Beschrijving | Vereiste velden voor elk document | Opmerkingen
--- | --- | --- | ---
`upload` | Een `upload`-actie is vergelijkbaar met een "upsert", waarbij het document wordt ingevoegd als het nieuw is en wordt bijgewerkt/vervangen als het al bestaat. | sleutel, plus andere velden die u wilt definiëren | Tijdens het bijwerken/vervangen van een bestaand document wordt elk veld dat niet is opgegeven in de aanvraag ingesteld op `null`. Dit gebeurt zelfs als het veld eerder is ingesteld op een niet-null-waarde.
`merge` | Een bestaand document wordt bijgewerkt met de opgegeven velden. Als het document niet in de index bestaat, mislukt de samenvoeging. | sleutel, plus andere velden die u wilt definiëren | Alle velden die u in een samenvoeging opgeeft, vervangen de bestaande velden in het document, ook velden van het type `Collection(Edm.String)`. Als het document bijvoorbeeld een veld `tags` bevat met de waarde `["budget"]` en u een samenvoeging doet met de waarde `["economy", "pool"]` voor `tags`, wordt de uiteindelijke waarde van het veld `tags` `["economy", "pool"]`. Het wordt dus niet `["budget", "economy", "pool"]`.
`mergeOrUpload` | Deze bewerking gedraagt zich als `merge` wanneer een document met de opgegeven sleutel al in de index bestaat. Als het document niet bestaat, gedraagt deze bewerking zich als `upload` met een nieuw document. | sleutel, plus andere velden die u wilt definiëren | -
`delete` | Het opgegeven document wordt uit de index verwijderd. | alleen sleutel | Alle andere velden worden genegeerd. Als u een afzonderlijk veld uit een document wilt verwijderen, gebruikt u `merge` en stelt u het veld expliciet in op null.

## III. De HTTP-aanvraag en de hoofdtekst opstellen
Nu u de benodigde veldwaarden voor uw indexbewerkingen hebt verzameld, kunt u de hoofdtekst voor uw HTTP-aanvraag en JSON-aanvraag opstellen om de gegevens te importeren.

#### Aanvragen en aanvraagheaders
U moet in de URL de servicenaam, de indexnaam ("hotels" in dit geval) en de juiste API-versie (de huidige API-versie is `2015-02-28` op het moment van publicatie van dit document) opgeven. U moet de aanvraagheaders `Content-Type` en `api-key` definiëren. Voor deze laatste moet u gebruikmaken van een van de administratorsleutels van uw service.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### Aanvraagtekst

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

In dit geval gebruiken we `upload`, `mergeOrUpload` en `delete` als onze zoekacties.

We gaan ervan uit dat deze voorbeeldindex "hotels" al is gevuld met een aantal documenten. We hoefden niet alle mogelijke documentvelden op te geven voor het gebruik van `mergeOrUpload`. We hebben alleen de documentsleutel (`hotelId`) opgegeven voor het gebruik van `delete`.

U kunt tot 1000 documenten (of 16 MB) in een enkele indexeringsaanvraag opnemen.

## IV. De HTTP-antwoordcode begrijpen
#### 200
Na een geslaagde indexeringsaanvraag ontvangt u een HTTP-antwoord met de statuscode `200 OK`. De JSON-hoofdtekst van het HTTP-antwoord is als volgt:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### 207
Statuscode `207` wordt geretourneerd wanneer ten minste één item is niet geïndexeerd. De JSON-hoofdtekst van het HTTP-antwoord bevat informatie over het document of de documenten die niet zijn geïndexeerd.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [AZURE.NOTE] Dit betekent vaak dat de belasting van uw zoekservice een punt bereikt waarbij indexeringsaanvragen `503`-antwoorden retourneren. In dit geval is het aanbevolen om de clientcode uit te stellen en te wachten voordat u het opnieuw probeert. Hierdoor krijgt het systeem de tijd om te herstellen, waardoor toekomstige aanvragen waarschijnlijk wel mogelijk zijn. Als u uw aanvraag snel opnieuw probeert te doen, wordt de situatie alleen maar verlengd.

#### 429
Statuscode `429` wordt geretourneerd wanneer u het quotum van het aantal documenten per index hebt overschreden.

#### 503
Statuscode `503` wordt geretourneerd als geen van de items in de aanvraag zijn geïndexeerd. Deze fout betekent dat het systeem zwaar belast wordt en uw aanvraag op dit moment niet kan worden verwerkt.

> [AZURE.NOTE] In dit geval is het aanbevolen om de clientcode uit te stellen en te wachten voordat u het opnieuw probeert. Hierdoor krijgt het systeem de tijd om te herstellen, waardoor toekomstige aanvragen waarschijnlijk wel mogelijk zijn. Als u uw aanvraag snel opnieuw probeert te doen, wordt de situatie alleen maar verlengd.

Zie [Documenten toevoegen, bijwerken of verwijderen](https://msdn.microsoft.com/library/azure/dn798930.aspx) voor meer informatie over bewerkingen en antwoorden voor een geslaagde of mislukte bewerking. Zie [HTTP-statuscodes (Azure Search)](https://msdn.microsoft.com/library/azure/dn798925.aspx) voor meer informatie over andere HTTP-statuscodes die kunnen worden geretourneerd in geval van storing.

## Volgende
Na het vullen van uw Azure Search-index bent u gereed om query's uit te geven om te zoeken naar documenten. Zie [Een query uitvoeren in uw Azure-zoekindex](search-query-overview.md) voor meer informatie.



<!--HONumber=Jun16_HO2-->


