<properties
    pageTitle="Een index voor Azure Search maken met behulp van de .NET SDK | Microsoft Azure | Gehoste service voor zoeken in de cloud"
    description="Een index in code maken met behulp van de Azure Search .NET SDK."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="brjohnst"/>

# Een index voor Azure Search maken met behulp van de .NET SDK
> [AZURE.SELECTOR]
- [Overzicht](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


Dit artikel begeleidt u stapsgewijs door het proces van het maken van een [index](https://msdn.microsoft.com/library/azure/dn798941.aspx) voor Azure Search met behulp van de [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Voordat u de stappen in dit artikel uitvoert en een index maakt, moet u eerst [een Azure Search-service](search-create-service-portal.md) hebben gemaakt.

Alle voorbeeldcode in dit artikel is geschreven in C#. U vindt de volledige broncode [op GitHub](http://aka.ms/search-dotnet-howto).

## I. De admin api-sleutel voor de Azure Search-service vaststellen
Nu u een Azure Search-service hebt ingericht, bent u bijna klaar om aanvragen te verzenden voor uw service-eindpunt met de .NET SDK. Eerst moet u een van de admin api-sleutels ophalen die is gegenereerd voor de zoekservice die u hebt ingericht. De .NET SDK verzendt deze api-sleutel bij elke aanvraag voor uw service. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

1. Als u de API-sleutels van uw service wilt opzoeken, moet u zich aanmelden bij [Azure Portal](https://portal.azure.com/)
2. Ga naar de blade van uw Azure Search-service
3. Klik op het pictogram Sleutels

Uw service heeft zowel *administratorsleutels* als *querysleutels*.

  - De primaire en secundaire *administratorsleutels* verlenen volledige rechten voor alle bewerkingen, inclusief de mogelijkheid voor het beheren van de service, het maken en verwijderen van indexen, indexeerfuncties en gegevensbronnen. Er zijn twee sleutels, zodat u de secundaire sleutel kunt gebruiken als u de primaire sleutel opnieuw aan het genereren bent en vice versa.
  - Uw *querysleutels* geven alleen-lezentoegang tot indexen en documenten. Deze sleutels worden doorgaans verleend aan clienttoepassingen die zoekaanvragen verlenen.

Als u een index wilt maken, kunt u de primaire of secundaire administratorsleutel gebruiken.

<a name="CreateSearchServiceClient"></a>
## II. Een instantie van de klasse SearchServiceClient maken
Als u de Azure Search .NET SDK wilt gebruiken, moet u een instantie van de klasse `SearchServiceClient` maken. Deze klasse heeft verschillende constructors. De gewenste constructor krijgt u de naam van uw zoekservice en een `SearchCredentials`-object als parameters. `SearchCredentials` verpakt uw api-sleutel.

Met de code hieronder wordt een nieuwe `SearchServiceClient` gemaakt die werkt met de naam van de zoekservice en de api-sleutel, die zijn opgeslagen in het configuratiebestand van de toepassing (`app.config` of `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` heeft een `Indexes`-eigenschap. Deze eigenschap bevat alle methoden die u nodig hebt om een index voor Azure Search te maken, weergeven, bijwerken of verwijderen.

> [AZURE.NOTE] De klasse `SearchServiceClient` beheert verbindingen met uw zoekservice. Als u wilt voorkomen dat er te veel verbindingen worden geopend, kunt u proberen één instantie van `SearchServiceClient` in uw toepassing te delen, indien mogelijk. De bijbehorende methoden zijn thread-safe om delen in te schakelen.

<a name="DefineIndex"></a>
## III. Een index voor Azure Search definiëren met behulp van de klasse `Index`
Uw index is met één aanroep naar de methode `Indexes.Create` gemaakt. Deze methode gebruikt een `Index`-object als parameter voor uw Azure Search-index. U moet een `Index`-object maken en dit als volgt initialiseren:

1. Stel de eigenschap `Name` van het `Index`-object in op de naam van de index.
2. Stel de eigenschap `Fields` van het `Index`-object in op een matrix van `Field`-objecten. Elk van de `Field`-objecten definieert het gedrag van een veld in de index. U kunt de naam van het veld aan de constructor doorgeven, samen met het gegevenstype (of analyse voor tekenreeksvelden). U kunt ook andere eigenschappen instellen, zoals `IsSearchable`, `IsFilterable`, enzovoort.

Als u uw index opzet, is het belangrijk dat u in uw achterhoofd houdt wat de gebruiker en uw bedrijf nodig hebben, aangezien aan elk `Field` de [relevante eigenschappen](https://msdn.microsoft.com/library/azure/dn798941.aspx) moeten worden toegewezen. Deze eigenschappen bepalen welke zoekfuncties (filteren, facetten, zoekacties in volledige tekst sorteren enzovoort) beschikbaar zijn in velden. Als u een eigenschap niet expliciet instelt, worden de bijbehorende zoekfuncties door de `Field`-klasse uitgeschakeld, tenzij u deze functies specifiek hebt ingeschakeld.

In ons voorbeeld hebben we onze index hotels genoemd en de velden als volgt ingesteld:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

We hebben de waarde van eigenschappen zorgvuldig ingesteld voor elk `Field` op basis van onze verwachting van het gebruik ervan in een toepassing. Het is bijvoorbeeld waarschijnlijk dat gebruikers die een hotel zoeken, geïnteresseerd zijn in overeenkomende sleutelwoorden in het veld `description`. Daarom hebben we ingesteld dat gebruikers in dat veld in volledige tekst kunnen zoeken door `IsSearchable` in te stellen op `true`.

In een index van het type `DataType.String` moet precies één veld zijn aangewezen als het _sleutel_veld door `IsKey` in te stellen op `true` (zie `hotelId` in het bovenstaande voorbeeld).

De bovenstaande indexdefinitie maakt gebruik van een aangepaste taalanalyse voor het veld `description_fr`, omdat dit veld is bedoeld voor het opslaan van Franse tekst. Zie [het onderwerp Language support (Taalondersteuning) op MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) en het bijbehorende [blogbericht](https://azure.microsoft.com/blog/language-support-in-azure-search/) voor meer informatie over taalanalyse.

> [AZURE.NOTE]  Als u `AnalyzerName.FrLucene` opgeeft in de constructor, wordt het `Field` automatisch van het type `DataType.String` en is `IsSearchable` ingesteld op `true`.

## IV. De index maken
Nu u een geïnitialiseerd `Index`-object hebt, kunt u de index maken door `Indexes.Create` aan te roepen op uw `SearchServiceClient`-object:

```csharp
serviceClient.Indexes.Create(definition);
```

Als de aanvraag is gelukt, wordt de methode als normaal geretourneerd. Als er een probleem met de aanvraag is, zoals een ongeldige parameter, genereert de methode `CloudException`.

Als u klaar bent met een index en deze weer wilt verwijderen, roept u de `Indexes.Delete`-methode aan op uw `SearchServiceClient`. De index hotels zou bijvoorbeeld als volgt kunnen worden verwijderd:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] In de voorbeeldcode in dit artikel is voor het gemak gebruikgemaakt van de synchrone methoden van de Azure Search .NET SDK. Het wordt aanbevolen in uw eigen toepassingen asynchrone methoden te gebruiken, zodat de toepassingen schaalbaar zijn en goed reageren. In de bovenstaande voorbeelden kunt u bijvoorbeeld `CreateAsync` en `DeleteAsync` gebruiken in plaats van `Create` en `Delete`.

## Volgende
Als u een index voor Azure Search hebt gemaakt, kunt u [de inhoud naar de index uploaden](search-what-is-data-import.md), zodat u kunt beginnen met het zoeken van gegevens.



<!--HONumber=Jun16_HO2-->


