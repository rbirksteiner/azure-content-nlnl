<properties
    pageTitle="Overzicht van Azure CDN"
    description="Meer informatie over Azure Content Delivery Network (CDN) en hoe u inhoud met een hoge bandbreedte via CDN kunt leveren door blobs en statische inhoud in de cache op te slaan."
    services="cdn"
    documentationCenter=".NET"
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="06/06/2016"
    ms.author="casoper"/>

# Overzicht van Azure Content Delivery Network (CDN)

> [AZURE.NOTE] In dit document wordt uitgelegd wat Azure Content Delivery Network (CDN) is, hoe het werkt en wat de functies van elk Azure CDN-product zijn.  Zie [Azure CDN gebruiken](cdn-create-new-endpoint.md) als u deze informatie wilt overslaan om rechtstreeks naar een zelfstudie over het maken van een CDN-eindpunt te aan.  Zie [Azure CDN POP-locaties](cdn-pop-locations.md) als u een lijst met de huidige CDN-knooppuntlocaties wilt weergeven.

Azure Content Delivery Network (CDN) slaat op strategisch geplaatste locaties statische webinhoud in de cache om een maximale doorvoer voor de levering van inhoud te waarborgen.  Het CDN biedt ontwikkelaars een globale oplossing voor de levering van inhoud met een hoge bandbreedte door de inhoud op fysieke knooppunten over de hele wereld op te slaan in de cache. 

Enkele voordelen van het gebruik van de CDN om website-assets op te slaan in de cache:

- Betere prestaties en gebruikerservaring voor eindgebruikers wanneer er toepassingen worden gebruikt waarbij meerdere retouren zijn vereist om inhoud te laden.
- Grote schaalbaarheid zodat een korte hoge belasting, bijvoorbeeld wanneer een product wordt gestart, beter kan worden verwerkt.
- Door de gebruikersaanvragen te distribueren en de inhoud uit te voeren vanaf randservers wordt er minder verkeer naar de oorsprong verzonden.


## Hoe werkt het?

![Overzicht van CDN](./media/cdn-overview/cdn-overview.png)

1. Een gebruiker (Els) gebruikt een URL met een speciale domeinnaam, zoals `<endpointname>.azureedge.net`, om een bestand (ook wel een asset genoemd) aan te vragen.  De aanvraag wordt door DNS naar de best presterende POP-locatie (Point-of-Presence) gerouteerd.  Doorgaans is dit het POP dat zie geografisch gezien het dichtst bij de gebruiker bevindt.

2. Als het bestand niet beschikbaar is in het cachegeheugen van de randservers in het POP, vraagt de randserver het bestand aan bij de oorsprong.  De oorsprong kan Azure-web-app, Azure Cloud-service, Azure Storage-account of een openbaar toegankelijke webserver zijn.

3. De oorsprong retourneert het bestand naar de randserver, inclusief optionele HTTP-headers met een beschrijving van de TTL (Time-to-Live) van het bestand.

4. De randserver neemt het bestand op in de cache en retourneert het bestand naar de oorspronkelijke aanvrager (Alice).  Het bestand blijft in cache op de randserver totdat de TTL verloopt.  Als de oorsprong geen TTL heeft opgegeven, is de standaard-TTL 7 dagen.

5. Extra gebruikers (zoals Bob) kunnen dan diezelfde URL gebruiken om hetzelfde bestand aan te vragen en worden mogelijk ook omgeleid naar hetzelfde POP.

6. Als de TTL voor het bestand niet is verlopen, retourneert de randserver het bestand uit de cache.  Dit resulteert in een snellere, responsievere gebruikerservaring.


## Functies van Azure CDN

Er zijn drie Azure CDN-producten: **Azure CDN Standard van Akamai**, **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon**.  De volgende tabel bevat de functies die beschikbaar zijn voor elk product.

|       | Standard Akamai | Standard Verizon | Premium Verizon |
|-------|-----------------|------------------|-----------------|
| Eenvoudige integratie met Azure-services, zoals [Storage](cdn-create-a-storage-account-with-cdn.md), [Cloud Services](cdn-cloud-service-with-cdn.md), [Web Apps](../app-service-web/cdn-websites-with-cdn.md) en [Media Services](../media-services/media-services-manage-origins.md#enable_cdn) | **&#x2713;** | **&#x2713;** | **&#x2713;**|
| HTTPS-ondersteuning | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| Taakverdeling | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| DDoS-beveiliging | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| IPv4/IPv6 dual stack | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Ondersteuning voor aangepaste domeinnamen](cdn-map-content-to-custom-domain.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Opslaan in cache van queryreeks](cdn-query-string.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Landen filteren](cdn-restrict-access-by-country.md) |  | **&#x2713;** | **&#x2713;** |
| [Snel leegmaken](cdn-purge-endpoint.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Vooraf laden van assets](cdn-preload-endpoint.md) |  | **&#x2713;** | **&#x2713;** |
| [Basisanalyse](cdn-analyze-usage-patterns.md) |  | **&#x2713;** | **&#x2713;** |
| [Beheer via REST API](https://msdn.microsoft.com/library/mt634456.aspx) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Aanpasbare, op regels gebaseerde engine voor contentlevering](cdn-rules-engine.md) | | | **&#x2713;** |
| [Geavanceerde HTTP-rapporten](cdn-advanced-http-reports.md) | | | **&#x2713;** |
| [Realtime statistieken](cdn-real-time-stats.md) | | | **&#x2713;** |


## Volgende stappen

Zie [Azure CDN gebruiken](./cdn-create-new-endpoint.md) om aan de slag te gaan met CDN.

Als u een bestaande CDN-klant bent, kunt u uw CDN-eindpunten nu beheren via [Microsoft Azure Portal](https://portal.azure.com).

Bekijk de [video van de Build 2016-sessie](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/) om CDN in actie te zien.

Zie [Prijzen van CDN](https://azure.microsoft.com/pricing/details/cdn/) voor informatie over de prijzen.

Is er een functie die u graag zou willen zien in Azure CDN?  [Geef ons feedback](https://feedback.azure.com/forums/169397-cdn). 



<!--HONumber=Jun16_HO2-->


