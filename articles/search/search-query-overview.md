<properties
    pageTitle="Een query uitvoeren in uw Azure Search-index | Microsoft Azure | Gehoste service voor zoeken in de cloud"
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

# Een query uitvoeren in uw Azure Search-index
> [AZURE.SELECTOR]
- [Overzicht](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Als u zoekaanvragen verzendt naar Azure Search, kunt u naast de werkelijke woorden die in het zoekvak van uw toepassing worden getypt een aantal parameters opgeven. Deze queryparameters bieden een betere controle over de functie voor het zoeken in volledige tekst.

Hieronder volgt een lijst met een korte beschrijving van het algemeen gebruik van de queryparameters in Azure Search. Zie de gedetailleerde pagina's over de [REST-API](https://msdn.microsoft.com/library/azure/dn798927.aspx) en [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx) voor een uitgebreid overzicht van queryparameters en de functie ervan.

## Typen query's

Azure Search biedt verschillende opties om krachtige query's te maken. De twee belangrijkste querytypen die u zult gebruiken zijn `search` en `filter`. Met een `search`-query zoekt u naar een of meerdere termen in alle _doorzoekbare_ velden in uw index. Deze query werkt min of meer als een zoekmachine als Google of Bing. Een `filter`-query evalueert een Booleaanse expressie op alle _filterbare_ velden in een index. In tegenstelling tot `search`-query 's vergelijken `filter`-query's de exacte inhoud van een veld. Dit betekent dat deze query's hoofdlettergevoelig zijn voor tekenreeksvelden.

U kunt zoekopdrachten en filters samen of afzonderlijk gebruiken. Als u zoekopdrachten en filters samen gebruikt, wordt het filter eerst op de gehele index toegepast. Vervolgens wordt de zoekopdracht uitgevoerd op het resultaat van het filter. Filters zijn dus nuttig om de resultaten van de zoekopdracht te verbeteren, doordat het aantal documenten dat moet worden doorzocht, wordt verminderd.

De syntaxis voor filterexpressies is een subset van de [OData-filtertaal](https://msdn.microsoft.com/library/azure/dn798921.aspx). Voor zoekquery's kunt u de [vereenvoudigde syntaxis](https://msdn.microsoft.com/library/azure/dn798920.aspx) of de [Lucene-querysyntaxis](https://msdn.microsoft.com/library/azure/mt589323.aspx) gebruiken. Deze worden hieronder behandeld.

### Vereenvoudigde querysyntaxis
De [vereenvoudigde querysyntaxis](https://msdn.microsoft.com/library/azure/dn798920.aspx) is de standaardtaal voor een query in Azure Search. De vereenvoudigde querysyntaxis ondersteunt een aantal algemene zoekoperators, zoals de operators EN, OF, NIET, woordgroep, achtervoegsel en prioriteit.

### Lucene-querysyntaxis
Met de [Lucene-querysyntaxis](https://msdn.microsoft.com/library/azure/mt589323.aspx) kunt u algemene en expressieve querytaal gebruiken die ontwikkeld is als onderdeel van [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Met deze querysyntaxis beschikt u over de volgende mogelijkheden: [veldgerelateerde query's](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields), [fuzzy zoeken](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy), [zoeken bij benadering](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity), [versterking van termen](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost), [zoeken op reguliere expressies](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex), [zoeken met jokertekens](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard), [syntaxisgrondbeginselen](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax) en [query's waarbij gebruikt wordt gemaakt van Booleaanse operators](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean).



## Resultaten ordenen
De resultaten van een zoekopdracht kunnen door Azure Search worden geordend op de waarde in een bepaald veld. Standaard ordent Azure Search zoekresultaten op basis van de positie van de zoekscore van elk document, die is afgeleid van [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Als u de zoekresultaten op een andere waarde wilt ordenen, kunt u de `orderby`-zoekparameter gebruiken. U kunt opgeven dat in de waarde van de `orderby`-parameter de veldnamen en aanroepen naar de [`geo.distance()`-functie](https://msdn.microsoft.com/library/azure/dn798921.aspx) voor georuimtelijke waarden worden opgenomen. Elke expressie kan worden gevolgd door `asc` om aan te geven dat de resultaten zijn aangevraagd in oplopende volgorde of door `desc` om aan te geven dat de resultaten in aflopende volgorde worden aangevraagd. De standaard oplopende volgorde.

## Zoekresultaten oproepen
Met Azure Search kunt u gemakkelijk zoekresultaten oproepen. Met behulp van de `top`- en `skip`-parameter kunt u eenvoudig zoekaanvragen uitgeven waarmee u alle zoekresultaten in een beheersbare, geordende subset kunt weergeven om een goede zoekopdracht uit te voeren. Tijdens het ontvangen van deze kleinere subsets van resultaten kunt u ook het aantal documenten weergeven in het totale aantal zoekresultaten.

Zie het artikel [Zoekresultaten oproepen in Azure Search](search-pagination-page-layout.md) voor meer informatie over het oproepen van zoekresultaten.


## Markeren
U kunt in Azure Search eenvoudig het exacte aantal zoekresultaten weergeven met behulp van de `highlight`-, `highlightPreTag`- en `highlightPostTag`-parameters. U kunt aangeven in welke _doorzoekbare_ velden de tekst moet worden benadrukt. Ook kunt u de exacte tekenreekslabels opgeven die moeten worden toegevoegd aan het begin en einde van de overeenkomstige tekst die Azure Search retourneert.



<!--HONumber=Jun16_HO2-->


