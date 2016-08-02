<properties
    pageTitle="Gegevens uploaden naar Azure Search met behulp van de .NET SDK | Microsoft Azure | Gehoste service voor zoeken in de cloud"
    description="Informatie over het uploaden van gegevens naar een index in Azure Search met behulp van de .NET SDK."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/23/2016"
    ms.author="brjohnst"/>

# Gegevens uploaden naar Azure Search met behulp van de .NET SDK
> [AZURE.SELECTOR]
- [Overzicht](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)

In dit artikel wordt beschreven hoe u met behulp van de [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) gegevens in een Azure Search-index importeert.

Voordat u deze procedure begint, moet u al [een Azure Search-index hebben gemaakt](search-what-is-an-index.md). In dit artikel wordt ervan uitgegaan dat u al een `SearchServiceClient`-object hebt gemaakt, zoals uiteengezet in [Een Azure Search-index maken met de.NET SDK](search-create-index-dotnet.md#CreateSearchServiceClient).

Alle voorbeeldcode in dit artikel is geschreven in C#. U vindt de volledige broncode [op GitHub](http://aka.ms/search-dotnet-howto).

Om documenten in uw index te pushen met behulp van de .NET SDK, moet u het volgende doen:

  1. Maak een `SearchIndexClient`-object om verbinding te maken met uw zoekindex.
  2. Maak een `IndexBatch` met de documenten die moeten worden toegevoegd, gewijzigd of verwijderd.
  3. Roep de `Documents.Index`-methode van uw `SearchIndexClient` aan om de `IndexBatch` naar uw zoekindex te verzenden.

## I. Een instantie van de klasse SearchIndexClient maken
Om de Azure Search .NET SDK te kunnen gebruiken om gegevens in uw index te importeren, moet u een instantie van klasse `SearchIndexClient` maken. U kunt deze instantie zelf samenstellen, maar het is eenvoudiger als u al een `SearchServiceClient`-instantie hebt om de methode `Indexes.GetClient` aan te roepen. U kunt bijvoorbeeld op de volgende manier een `SearchIndexClient` verkrijgen voor de index met de naam "hotels" van een `SearchServiceClient` met de naam `serviceClient`:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [AZURE.NOTE] In een standaardzoektoepassing wordt het beheer van de index en de invulling daarvan afgehandeld door een afzonderlijk onderdeel van de zoekquery’s. `Indexes.GetClient` is handig voor het vullen van een index, omdat u geen andere `SearchCredentials` hoeft op te geven. Dit wordt uitgevoerd door de administratorsleutel die u hebt gebruikt om de `SearchServiceClient` te maken die u wilt doorgeven aan de nieuwe `SearchIndexClient`. In het gedeelte van uw toepassing waarmee u query's uitvoert, is het beter om de `SearchIndexClient` direct te maken, zodat u een querysleutel kunt toepassen in plaats van een administratorsleutel. Dit komt overeen met het [-principe van minimale bevoegdheden](https://en.wikipedia.org/wiki/Principle_of_least_privilege) en zorgt ervoor dat uw toepassing veilig is. U vindt meer informatie over administratorsleutels en querysleutels in [Azure Search REST-API-verwijzingen op MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx)

`SearchIndexClient` heeft een `Documents`-eigenschap. Deze eigenschap bevat de methoden die u wilt toevoegen, wijzigen, verwijderen, of om documenten in uw index op te vragen.

## II. Bepalen welke indexeerbewerking u moet gebruiken
Om gegevens te importeren met de .NET SDK, moet u uw gegevens in een `IndexBatch`-object verpakken. Een `IndexBatch` omvat een verzameling van `IndexAction`-objecten, die een document en een eigenschap bevatten die Azure Search aansturen om een bewerking op het document uit te voeren (uploaden, samenvoegen, verwijderen, enzovoort). Afhankelijk van welke van de onderstaande bewerkingen u kiest, moet u slechts bepaalde velden voor elk document opnemen:

Bewerking | Beschrijving | Vereiste velden voor elk document | Opmerkingen
--- | --- | --- | ---
`Upload` | Een `Upload`-actie is vergelijkbaar met een "upsert", waarbij het document wordt ingevoegd als het nieuw is en wordt bijgewerkt/vervangen als het al bestaat. | sleutel, plus andere velden die u wilt definiëren | Tijdens het bijwerken/vervangen van een bestaand document wordt elk veld dat niet is opgegeven in de aanvraag ingesteld op `null`. Dit gebeurt zelfs als het veld eerder is ingesteld op een niet-null-waarde.
`Merge` | Een bestaand document wordt bijgewerkt met de opgegeven velden. Als het document niet in de index bestaat, mislukt de samenvoeging. | sleutel, plus andere velden die u wilt definiëren | Alle velden die u in een samenvoeging opgeeft, vervangen de bestaande velden in het document, ook velden van het type `DataType.Collection(DataType.String)`. Als het document bijvoorbeeld een veld `tags` bevat met de waarde `["budget"]` en u een samenvoeging doet met de waarde `["economy", "pool"]` voor `tags`, wordt de uiteindelijke waarde van het veld `tags` `["economy", "pool"]`. Het wordt dus niet `["budget", "economy", "pool"]`.
`MergeOrUpload` | Deze bewerking gedraagt zich als `Merge` wanneer een document met de opgegeven sleutel al in de index bestaat. Als het document niet bestaat, gedraagt deze bewerking zich als `Upload` met een nieuw document. | sleutel, plus andere velden die u wilt definiëren | -
`Delete` | Het opgegeven document wordt uit de index verwijderd. | alleen sleutel | Alle andere velden worden genegeerd. Als u een afzonderlijk veld uit een document wilt verwijderen, gebruikt u `Merge` en stelt u het veld expliciet in op null.

U kunt opgeven welke actie u wilt gebruiken met de verschillende statische methoden van de klassen `IndexBatch` en `IndexAction`, zoals wordt weergegeven in de volgende sectie.

## III. Uw IndexBatch maken
Nu u weet welke bewerkingen u wilt uitvoeren op uw documenten, bent u klaar om de `IndexBatch` samen te stellen. Het volgende voorbeeld laat zien hoe u een batch met verschillende bewerkingen kunt maken. In dit voorbeeld wordt een aangepaste klasse gebruikt met de naam `Hotel`, die wordt toegewezen aan een document in de index "hotels".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

In dit geval gebruiken we `Upload`, `MergeOrUpload`, en `Delete` als de zoekacties, zoals opgegeven door de methoden die worden aangeroepen voor de `IndexAction`-klasse.

We gaan ervan uit dat deze voorbeeldindex "hotels" al is gevuld met een aantal documenten. We hoefden niet alle mogelijke documentvelden op te geven voor het gebruik van `MergeOrUpload`. We hebben alleen de documentsleutel (`HotelId`) opgegeven voor het gebruik van `Delete`.

U kunt tot 1000 documenten in een enkele indexeringsaanvraag opnemen.

> [AZURE.NOTE] In dit voorbeeld passen we verschillende bewerkingen op verschillende documenten toe. Als u dezelfde bewerkingen op alle documenten in de batch wilt uitvoeren, kunt u in plaats van `IndexBatch.New` aan te roepen, een andere statische methode van `IndexBatch` gebruiken. U kunt bijvoorbeeld batches maken door `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, of `IndexBatch.Delete` aan te roepen. Deze methoden maken gebruik van een verzameling van documenten (objecten van het type `Hotel` in dit voorbeeld) in plaats van `IndexAction`-objecten.

## IV. Gegevens naar de index importeren
Nu u een geïnitialiseerd `IndexBatch`-object hebt, kunt u het object naar de index sturen door `Documents.Index` aan te roepen op uw `SearchIndexClient`-object. Het volgende voorbeeld laat zien hoe u `Index` kunt aanroepen, plus een aantal extra stappen die u moet uitvoeren:

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Let op de `try`/`catch` rond de aanroep van de `Index`-methode. Het catch-blok verwerkt een belangrijke foutaanvraag voor indexering. Als uw Azure Search-service geen index van een aantal documenten in de batch kan maken, wordt een `IndexBatchException` verstuurd door `Documents.Index`. Dit kan gebeuren als u documenten indexeert terwijl uw service zwaar wordt belast. **Wij raden u aan deze aanvraag expliciet in uw code te behandelen.** U kunt de indexering van documenten die niet zijn geïndexeerd vertragen en vervolgens opnieuw uitvoeren, maar u kunt ook een logboek maken en doorgaan zoals in het voorbeeld. U kunt ook een andere bewerking uitvoeren, afhankelijk van de vereisten omtrent de gegevensconsistentie van de toepassing.

De code in bovenstaand voorbeeld wordt dan twee seconden vertraagd. Het indexeren verloopt asynchroon in uw Azure Search-service. De voorbeeldtoepassing moet even wachten totdat de documenten beschikbaar zijn voor een zoekbewerking. Dergelijke vertragingen zijn doorgaans alleen nodig is demo’s, testen en voorbeeldtoepassingen.

<a name="HotelClass"></a>
### De verwerking van documenten door .NET SDK

U vraagt zich misschien af hoe de Azure Search .NET SDK instanties van een door een gebruiker gedefinieerde klasse zoals `Hotel` naar de index kan uploaden. Om deze vraag te beantwoorden, bekijken we de `Hotel`-klasse van dichtbij. Deze klasse is toegewezen aan het indexschema dat is gedefinieerd in [Een Azure Search-index maken met behulp van de .NET SDK](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    public string HotelId { get; set; }

    public double? BaseRate { get; set; }

    public string Description { get; set; }

    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    public string HotelName { get; set; }

    public string Category { get; set; }

    public string[] Tags { get; set; }

    public bool? ParkingIncluded { get; set; }

    public bool? SmokingAllowed { get; set; }

    public DateTimeOffset? LastRenovationDate { get; set; }

    public int? Rating { get; set; }

    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

Het eerste dat opvalt, is dat elke openbare eigenschap van `Hotel` overeenkomt met een veld in de definitie van de index. Er is echter een cruciaal verschil: de naam van elk veld begint met een kleine letter ("kamelen"), terwijl de naam van elke openbare eigenschap van `Hotel` met een hoofdletter ("Pascal") begint. Dit is een algemeen scenario in .NET-toepassingen die gegevens koppelen waarbij het doelschema buiten de controle van de ontwikkelaar van de toepassing valt. In plaats van het schenden van de .NET-naamgevingsregels door een eigenschap met bijvoorbeeld de naam "kamelen" te maken, kunt u instellen dat de SDK de eigenschapsnamen automatisch moet toewijzen aan het `[SerializePropertyNamesAsCamelCase]`-kenmerk.

> [AZURE.NOTE] De Azure Search .NET SDK maakt gebruik van de [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm)-bibliotheek voor het serialiseren en deserialiseren van uw aangepaste modelobjecten naar en van JSON. U kunt deze serialisatie indien nodig aanpassen. U vindt meer informatie in [Bijwerken naar Azure Search .NET SDK-versie 1.1](search-dotnet-sdk-migration.md#WhatsNew). Een voorbeeld hiervan is het gebruik van het `[JsonProperty]`-kenmerk in de eigenschap `DescriptionFr` in de bovenstaande voorbeeldcode.

Wat ook belangrijk is in de `Hotel`-klasse, zijn de gegevenstypen van de openbare eigenschappen. De .NET-typen van deze eigenschappen worden toegewezen aan de gelijkwaardige veldtypen in de definitie van de index. De tekenreekseigenschap `Category` is bijvoorbeeld toegewezen aan het veld `category` van type `DataType.String`. Er zijn vergelijkbare type toewijzingen tussen `bool?` en `DataType.Boolean`, `DateTimeOffset?` en `DataType.DateTimeOffset`, enzovoort. De specifieke regels voor de toewijzing van het type zijn gedocumenteerd in de methode `Documents.Get` op [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

De mogelijkheid om uw eigen klassen te gebruiken als documenten werkt beide kanten op: u kunt ook zoekresultaten ophalen en ervoor zorgen dat de SDK de resultaten automatisch deserialiseert naar een type dat u hebt ingesteld, zoals wordt uitgelegd in het [volgende artikel](search-query-dotnet.md).

> [AZURE.NOTE] De Azure Search .NET SDK biedt ook ondersteuning voor dynamisch getypeerde documenten met behulp van de `Document`-klasse, die een sleutel/waarde-toewijst aan veldnamen naar waarden. Dit is handig in situaties waar u het schema van de index op het moment van ontwerp nog niet weet of wanneer het niet handig zou zijn om verbinding te maken met specifieke modelklassen Alle methoden in de SDK die werken met documenten hebben overloads die met werken de `Document`-klasse, evenals sterk getypeerde overloads die een generiek typeparameter moeten uitvoeren. Alleen de laatste worden in de voorbeeldcode in dit artikel gebruikt. U vindt meer informatie over de `Document`-klasse [op MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**Belangrijke opmerking over gegevenstypen**

Bij het ontwerpen van uw eigen modelklassen die u wilt toewijzen aan een Azure Search-index raden we u aan om eigenschappen van waardentypen `bool` en `int` in te stellen op null-waarden (bijvoorbeeld `bool?` in plaats van `bool`). Als u een niet-nullbare eigenschap gebruikt, moet u **garanderen** dat de documenten in de index geen null-waarde voor het betreffende veld bevatten. Noch de SDK noch de Azure Search-service helpt u om dit af te dwingen.

Dit is niet alleen een hypothetische probleem: Stel een scenario voor waarin u een nieuw veld toevoegt aan een bestaande index van het type `DataType.Int32`. Na het bijwerken van de indexdefinitie hebben alle documenten een null-waarde voor het nieuwe veld (omdat alle typen null in Azure Search zijn). Als u vervolgens een modelklasse met een niet-nullbare `int`-eigenschap voor dat veld gebruikt, ontvangt u een `JsonSerializationException` zoals deze bij het ophalen van documenten:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Daarom wordt u aangeraden nullbare typen in uw modelklassen te gebruiken.

## Volgende
Na het vullen van uw Azure Search-index bent u gereed om query's uit te geven om te zoeken naar documenten. Zie [Een query uitvoeren in uw Azure-zoekindex](search-query-overview.md) voor meer informatie.



<!--HONumber=Jun16_HO2-->


