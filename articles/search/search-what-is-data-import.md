<properties
    pageTitle="Gegevens uploaden in Azure Search | Microsoft Azure | Gehoste service voor zoeken in de cloud"
    description="Informatie over het uploaden van gegevens naar een index in Azure Search"
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"/>

# Gegevens uploaden naar Azure Search
> [AZURE.SELECTOR]
- [Overzicht](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)


## Modellen voor het uploaden van gegevens in Azure Search
Er zijn twee manieren om uw Azure Search-index te vullen met uw gegevens. De eerste optie is het handmatig pushen van de gegevens in de index met behulp van de Azure Search [REST-API](search-import-data-rest-api.md) of [.NET SDK](search-import-data-dotnet.md). De tweede optie is om [een ondersteunde gegevensbron](search-indexer-overview.md) te wijzen naar uw Azure Search-index en ervoor te zorgen dat Azure Search automatisch gegevens naar de zoekservice pusht.

Deze handleiding heeft alleen betrekking op instructies voor het gebruik van het pushmodel voor het uploaden van gegevens (dat alleen wordt ondersteund in de [REST-API](search-import-data-rest-api.md) en [.NET SDK](search-import-data-dotnet.md)). U kunt echter meer informatie opvragen over het onderstaande pull-model.

### Gegevens naar een index verzenden

Deze aanpak verwijst naar het programmatisch verzenden van uw gegevens naar Azure Search, zodat deze beschikbaar zijn voor een zoekopdracht. Voor toepassingen waarvoor een lage latentie is vereist (bijvoorbeeld wanneer en zoekopdracht gesynchroniseerd is met dynamische inventarisatiedatabases) is de pushoptie de enige mogelijkheid.

U kunt de [REST-API](https://msdn.microsoft.com/library/azure/dn798930.aspx) of [.NET SDK](search-import-data-dotnet.md) gebruiken om gegevens naar een index te pushen. Er is momenteel geen ondersteuning voor het pushen van gegevens via de portal.

Deze aanpak is flexibeler dan het pull-model, omdat u documenten afzonderlijk of in batches kunt uploaden (maximaal 1000 per batch of 16 MB, afhankelijk van waar de limiet ligt). Met het pushmodel kunt u documenten uploaden naar Azure Search, ongeacht waar uw gegevens zich bevinden.

### Gegevens in een index ophalen

Het pullmodel verkent een ondersteunde gegevensbron en uploadt automatisch de gegevens in uw Azure Search-index. Als u naast het herkennen van nieuwe documenten, wijzigingen en verwijderingen in een bestaand document bijhoudt, hoeft u de gegevens in een index via indexeerfuncties niet actief bij te houden.

In Azure Search is deze mogelijkheid geïmplementeerd via *indexeerfuncties*, die momenteel beschikbaar zijn voor [Blob Storage (preview-versie)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [Azure SQL database en SQL Server op Azure VM’s](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

De functionaliteit van de indexeerfunctie wordt weergegeven in [Azure Portal](search-import-data-portal.md) en de [REST-API](https://msdn.microsoft.com/library/azure/dn946891.aspx).



<!--HONumber=Jun16_HO2-->


