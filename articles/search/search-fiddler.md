<properties
    pageTitle="Fiddler gebruiken om Azure Search REST API's te evalueren en testen | Microsoft Azure | Gehoste service voor zoeken in de cloud"
    description="Gebruik Fiddler om zonder code de beschikbaarheid van Azure Search te verifiëren en de REST API's uit te proberen."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="02/18/2016"
    ms.author="heidist"/>

# Gebruik Fiddler om Azure Search REST API's te evalueren en te testen
> [AZURE.SELECTOR]
- [Overzicht](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

In dit artikel wordt uitgelegd hoe u Fiddler, beschikbaar als [gratis download via Telerik](http://www.telerik.com/fiddler), gebruikt om HTTP-aanvragen te verzenden en antwoorden weer te geven met de Azure Search REST API, zonder dat u code hoeft te schrijven. Azure Search is een volledig beheerde, gehoste Microsoft Azure-service voor zoeken in de cloud. De service is eenvoudig programmeerbaar via .NET en REST API's. Documentatie over de REST API's voor de Azure Search-service vindt u op [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

In de volgende stappen maakt u een index, uploadt u documenten, voert u een query uit op de index en voert u vervolgens een query uit op het systeem om servicegegevens te zoeken.

U hebt een Azure Search-service en `api-key` nodig om deze stappen te kunnen voltooien. Zie [Een Azure Search-service in de portal maken](search-create-service-portal.md) voor instructies om aan de slag gaan.

## Een index maken

1. Start Fiddler. Schakel in het menu **File** (Bestand) de optie **Capture Traffic** (Verkeer vastleggen) uit om de HTTP-activiteit te verbergen die niet van belang is voor de huidige taak.

3. Op het tabblad **Composer** (Opstellen) formuleert u een aanvraag die vergelijkbaar is met de volgende schermopname.

    ![][1]

2. Selecteer **PUT**.

3. Geef een URL op waarin de service-URL, aanvraagkenmerken en de API-versie worden gespecificeerd. Enkele punten om rekening mee te houden:
   + Gebruik HTTPS als voorvoegsel.
   + Het aanvraagkenmerk is '/indexes/hotels'. Zodoende weet Search dat de index met de naam 'hotels' moet worden gemaakt.
   + De API-versie moet worden opgegeven in kleine letters, als '?api-version=2015-02-28'. API-versies zijn belangrijk omdat Azure Search regelmatig updates implementeert. In uitzonderlijk gevallen introduceert een service-update een belangrijke wijziging in de API. Daarom vereist Azure Search bij elke aanvraag een API-versie, zodat u de volledige controle hebt over de API-versie die wordt gebruikt.

    De volledige URL moet er ongeveer uitzien als de URL in het volgende voorbeeld.

         https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.  Geef de aanvraagheader de host en de API-sleutel hebt vervangen door de geldige waarden voor uw service.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

5.  Plak de velden waaruit de indexdefinitie bestaat in de aanvraagtekst.

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
          {"name": "hotelName", "type": "Edm.String"},
          {"name": "category", "type": "Edm.String"},
          {"name": "tags", "type": "Collection(Edm.String)"},
          {"name": "parkingIncluded", "type": "Edm.Boolean"},
          {"name": "smokingAllowed", "type": "Edm.Boolean"},
          {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
          {"name": "rating", "type": "Edm.Int32"},
          {"name": "location", "type": "Edm.GeographyPoint"}
         ]
        }

6.  Klik op **Execute** (Uitvoeren).

Over een paar seconden wordt er een 201 HTTP-respons in de sessielijst weergegeven dat de index is gemaakt.

Als u een HTTP 504-respons ontvangt, controleert u of de URL HTTPS bevat. Als de HTTP-fout 400 of 404 wordt weergegeven ziet, controleert u of de aanvraagtekst op fouten die mogelijk zijn opgetreden tijden kopiëren en plakken. Een HTTP 403 duidt doorgaans op een probleem met de API-sleutel (een ongeldige sleutel of een syntaxisfout in de opgegeven API-sleutel).

## Documenten laden

Uw aanvraag op het tabblad **Composer** (Opstellen) om documenten te plaatsen, ziet er als volgt uit. De hoofdtekst van de aanvraag bevat de zoekgegevens voor vier hotels.

   ![][2]

1. Selecteer **POST**.

2.  Geef een URL op die begint met HTTPS, gevolgd door uw service-URL, gevolgd door '/indexes/<indexnaam>/docs/index?api-version=2015-02-28'. De volledige URL moet er ongeveer uitzien als de URL in het volgende voorbeeld.

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.  De aanvraagheader blijft ongewijzigd. Vergeet niet dat u de host en de API-sleutel hebt vervangen door waarden die geldig voor uw service zijn.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  De aanvraagtekst bevat vier documenten die moeten worden toegevoegd aan de index hotels.

        {
        "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
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
            "@search.action": "upload",
            "hotelId": "3",
            "baseRate": 279.99,
            "description": "Surprisingly expensive",
            "hotelName": "Dew Drop Inn",
            "category": "Bed and Breakfast",
            "tags": ["charming", "quaint"],
            "parkingIncluded": true,
            "smokingAllowed": false,
            "lastRenovationDate": null,
            "rating": 4,
            "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
          },
          {
            "@search.action": "upload",
            "hotelId": "4",
            "baseRate": 220.00,
            "description": "This could be the one",
            "hotelName": "A Hotel for Everyone",
            "category": "Basic hotel",
            "tags": ["pool", "wifi"],
            "parkingIncluded": true,
            "smokingAllowed": false,
            "lastRenovationDate": null,
            "rating": 4,
            "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
          }
         ]
        }

8.  Klik op **Execute** (Uitvoeren).

Over enkele seconden verschijnt er een HTTP 200-respons in de sessielijst. Dit geeft aan dat de documenten zijn gemaakt. Als u een 207-respons ontvang, is minimaal één document niet geüpload. Als u een 404-respons ontvangt, bevat de header of de hoofdtekst van de aanvraag een syntaxisfout.

## Een query op de index uitvoeren

Nu er een index en documenten zijn geladen, kunt u hier query's op uitvoeren.  Een **GET**-opdracht op het tabblad**Composer** (Opstellen), waarmee een query op uw service wordt uitgevoerd, is vergelijkbaar met de volgende schermopname.

   ![][3]

1.  Selecteer **GET**.

2.  Geef een URL op die begint met HTTPS, gevolgd door uw service-URL, gevolgd door '/indexes/<indexnaam>/docs/index?', gevolgd door queryparameters. Gebruik als bijvoorbeeld de volgende URL en vervang de naam van de voorbeeldhost door een geldige naam voor uw service.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Met deze query wordt naar de term 'motel' en worden er facetcategorieën voor beoordelingen opgehaald.

3.  De aanvraagheader blijft ongewijzigd. Vergeet niet dat u de host en de API-sleutel hebt vervangen door waarden die geldig voor uw service zijn.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

De responscode moet 200 zijn en de responsuitvoer moet er ongeveer hetzelfde uitzien als in de volgende schermopname.

   ![][4]

De volgende voorbeeldquery is afkomstig uit de [Search Index-bewerking (Azure Search API)](http://msdn.microsoft.com/library/dn798927.aspx) op MSDN. Veel van de voorbeeldquery's in dit onderwerp bevatten spaties. Deze zijn niet toegestaan in Fiddler. Vervang elke spatie door een plusteken (+) voordat u de queryreeks in Fiddler plakt en uitvoert.

**Voordat de spaties zijn vervangen:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Nadat de spaties zijn vervangen door +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## Een query uitvoeren op het systeem

U kunt ook een query op het systeem uitvoeren om het aantal documenten en opslagverbruik op te vragen. Uw aanvraag op het tabblad **Composer** (Opstellen) ziet er ongeveer als volgt uit en de respons retourneert een getal voor het aantal documenten en de gebruikte ruimte.

 ![][5]

1.  Selecteer **GET**.

2.  Geef een URL op die uw service-URL bevat, gevolgd door '/indexes/hotels/stats?api-version=2015-02-28':

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.  Geef de aanvraagheader de host en de API-sleutel hebt vervangen door de geldige waarden voor uw service.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  Laat de aanvraagtekst leeg.

5.  Klik op **Execute** (Uitvoeren). Er wordt een HTTP 200-statuscode weergegeven in de sessielijst. Selecteer de vermelding van uw opdracht.

6.  Klik op het tabblad **Inspectors** (Inspecteurs), klik op het tabblad **Headers** en selecteer vervolgens de JSON-indeling. Als het goed is wordt het aantal documenten en de opslaggrootte (in kB) weergegeven.

## Volgende stappen

Zie [Uw Search-service op Azure beheren](search-manage.md) om te zien hoe u Azure Search zonder code kunt beheren en gebruiken.


<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png



<!--HONumber=Jun16_HO2-->


