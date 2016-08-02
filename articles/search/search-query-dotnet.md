<properties
    pageTitle="Een query uitvoeren in uw Azure Search-index met behulp van de .NET SDK | Microsoft Azure | Gehoste service voor zoeken in de cloud"
    description="Een zoekquery samenstellen in Azure Search en gebruikmaken van zoekparameters om zoekresultaten te filteren en te sorteren."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/23/2016"
    ms.author="brjohnst"/>

# Een query uitvoeren in uw Azure Search-index met behulp van de .NET SDK
> [AZURE.SELECTOR]
- [Overzicht](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

In dit artikel wordt beschreven hoe u een query uitvoert in uw index met behulp van de [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Voordat u deze procedure begint, moet u al [een Azure Search-index hebben gemaakt](search-what-is-an-index.md) en moet deze index [gevuld zijn met gegevens](search-what-is-data-import.md).

Alle voorbeeldcode in dit artikel is geschreven in C#. U vindt de volledige broncode [op GitHub](http://aka.ms/search-dotnet-howto).

## I. De query api-sleutel voor de Azure Search-service vaststellen
Nu u een Azure Search-index hebt gemaakt, bent u bijna zover om query's uit te geven met behulp van de .NET SDK. Eerst moet u een van de query api-sleutels ophalen die is gegenereerd voor de zoekservice die u hebt ingericht. De .NET SDK verzendt deze api-sleutel bij elke aanvraag voor uw service. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

1. Om de api-sleutels van uw service te vinden, moet u inloggen op de [Azure Portal](https://portal.azure.com/)
2. Ga naar de blade van uw Azure Search-service
3. Klik op het pictogram "Sleutels"

Uw service heeft zowel *administratorsleutels* als *querysleutels*.

  - De primaire en secundaire *administratorsleutels* verlenen volledige rechten voor alle bewerkingen, inclusief de mogelijkheid voor het beheren van de service, het maken en verwijderen van indexen, indexeerfuncties en gegevensbronnen. Er zijn twee sleutels, zodat u de secundaire sleutel kunt gebruiken als u de primaire sleutel opnieuw aan het genereren bent en vice versa.
  - Uw *querysleutels* geven alleen-lezentoegang tot indexen en documenten. Deze sleutels worden doorgaans verleend aan clienttoepassingen die zoekaanvragen verlenen.

U kunt gebruikmaken van een van de query-sleutel om een query in een index uit te voeren. De administratorsleutels kunnen ook worden gebruikt voor query's, maar u moet gebruikmaken van een querysleutel in de toepassingscode, aangezien dit het [principe van minimale bevoegdheden](https://en.wikipedia.org/wiki/Principle_of_least_privilege) volgt.

## II. Een instantie van de klasse SearchIndexClient maken
Om query's uit te geven met de Azure Search .NET SDK, moet u een instantie van klasse `SearchIndexClient` maken. Deze klasse heeft verschillende constructors. De gewenste constructor krijgt de naam van uw zoekservice, de naam van de index en een `SearchCredentials`-object als parameters. `SearchCredentials` verpakt uw api-sleutel.

Met de code hieronder maakt u een nieuwe `SearchIndexClient` voor de index "hotels" (gemaakt in [Een Azure Search-index maken met behulp van de .NET SDK](search-create-index-dotnet.md)) met waarden voor de naam van de zoekservice en de api-sleutel die zijn opgeslagen in het configuratiebestand van de toepassing (`app.config` of `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient` heeft een `Documents`-eigenschap. Deze eigenschap bevat alle methoden die u nodig hebt om een query uit te voeren in een Azure Search-index.

## III. Een query uitvoeren in uw index
Zoeken met de .NET SDK is net zo eenvoudig als het aanroepen van de `Documents.Search`-methode op uw `SearchIndexClient`. Deze methode heeft een aantal parameters, waaronder de zoektekst, en een `SearchParameters`-object dat kan worden gebruikt om de query te verfijnen.

#### Typen query 's
De twee belangrijkste [querytypen](search-query-overview.md#types-of-queries) die u zult gebruiken zijn `search` en `filter`. Een `search`-query zoekt naar een of meer voorwaarden in alle _doorzoekbare_ velden in de index. Een `filter`-query evalueert een Booleaanse expressie op alle _filterbare_ velden in een index.

Zoekopdrachten en filters worden allebei uitgevoerd met behulp van de `Documents.Search`-methode. Een zoekopdracht kan worden doorgegeven aan de `searchText`-parameter, terwijl een filterexpressie kan worden doorgegeven in de eigenschap `Filter` van de `SearchParameters`-klasse. Als u wilt filteren zonder te zoeken, geeft u `"*"` door voor de `searchText`-parameter. Om te zoeken zonder te filteren, stelt u de eigenschap `Filter` niet in of geeft u niet door aan een `SearchParameters`-instantie.

#### Voorbeelden van query 's

De volgende voorbeeldcode laat zien hoe u op verschillende manieren een query kunt uitvoeren in de index "hotels", die is gedefinieerd in [Een Azure Search-index maken met behulp van de .NET SDK](search-create-index-dotnet.md#DefineIndex). De documenten die worden geretourneerd met de zoekresultaten zijn instanties van de klasse `Hotel`, die is gedefinieerd in [Gegevens importeren in Azure Search met behulp van de .NET SDK](search-import-data-dotnet.md#HotelClass). De voorbeeldcode maakt gebruik van een `WriteDocuments`-methode om de zoekresultaten in de console uit te voeren. Deze methode wordt in de volgende sectie beschreven.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## IV. Zoekresultaten verwerken
De methode `Documents.Search` retourneert een `DocumentSearchResult`-object dat de resultaten van de query bevat. In het voorbeeld in de vorige sectie is gebruikgemaakt van een methode met de naam `WriteDocuments` om de zoekresultaten in de console uit te voeren:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Hier ziet u hoe de resultaten voor de query's in de vorige sectie eruit zien, ervan uitgaande dat de index "hotels" is gevuld met de voorbeeldgegevens in [Gegevens importeren in Azure Search met behulp van de .NET SDK](search-import-data-dotnet.md):

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

De bovenstaande voorbeeldcode maakt gebruik van de console om de zoekresultaten uit te voeren. U moet ook zoekresultaten in uw eigen toepassing weergeven. Zie [dit voorbeeld op GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) voor een voorbeeld van hoe zoekresultaten in een ASP.NET MVC-gebaseerde webtoepassing worden weergegeven.



<!--HONumber=Jun16_HO2-->


