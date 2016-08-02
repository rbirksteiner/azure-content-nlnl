<properties
    pageTitle="Een query uitvoeren op uw Azure Search-index met Azure Portal | Microsoft Azure | Gehoste service voor zoeken in de cloud"
    description="Een zoekopdracht in de Search Explorer van Azure Portal uitvoeren."
    services="search"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"
/>
# Een query uitvoeren op uw Azure Search-index met Azure Portal
> [AZURE.SELECTOR]
- [Overzicht](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

In dit artikel wordt beschreven hoe u een query op de Azure Search-index kunt uitvoeren in Azure Portal.

Voordat u deze procedure begint, moet u al [een Azure Search-index hebben gemaakt](search-what-is-an-index.md) en moet deze index [gevuld zijn met gegevens](search-what-is-data-import.md).

## I. Naar uw Azure Search-blade gaan
1. Klik op Alle resources in het menu aan de linkerkant van [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Selecteer uw Azure Search-service

## II. De index selecteren waarin u wilt zoeken
1. Selecteer de index waarin u wilt zoeken vanaf de tegel Indexen.

![](./media/search-explorer/pick-index.png)

## III. Op de tegel Search Explorer klikken
![](./media/search-explorer/search-explorer-tile.png)

## III. Beginnen met zoeken
1. Als u wilt zoeken in uw Azure Search-index, begint u te typen in het veld *Querytekenreeks* en kiest u vervolgens **Zoeken**.
 * Wanneer u de Search Explorer gebruikt, kunt u een van de [queryparameters](https://msdn.microsoft.com/library/dn798927.aspx) opgeven

2. De queryresultaten worden in de sectie *Resultaten* weergegeven in de onbewerkte JSON zoals u die zou ontvangen in een HTTP-antwoordtekst als u een zoekaanvraag zou doen in de Azure Search REST-API.
3. De queryreeks worden automatisch verdeeld in de juiste aanvraag-URL om een HTPP-aanvraag te verzenden naar de Azure Search REST-API.

![](./media/search-explorer/search-bar.png)



<!--HONumber=Jun16_HO2-->


