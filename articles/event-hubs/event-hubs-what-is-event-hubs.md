<properties
    pageTitle="Wat is Azure Event Hubs? | Microsoft Azure"
    description="Overzicht en de beschrijving van Azure Event Hubs"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/12/2016"
    ms.author="sethm;nberdy"/>

# Wat is Azure Event Hubs?

Azure Event Hubs is een zeer schaalbare service voor inkomende gegevens die miljoenen gebeurtenissen per seconde kan opnemen, voor verwerking en analyse van de enorme hoeveelheden gegevens die worden geproduceerd door verbonden apparaten en toepassingen. Event Hubs fungeert als de 'voordeur' voor een gebeurtenissenpijplijn. Zodra gegevens zijn verzameld in een Event Hub, kunnen die worden omgezet en opgeslagen met een realtime-analyseprovider of  batching-/opslagadapters. Event Hubs koppelt de productie van een gebeurtenissenstroom los van het gebruik van deze gebeurtenissen, zodat de consumenten ervan toegang hebben tot de gebeurtenissen op basis van hun eigen planning.

## Mogelijkheden van Event Hubs

Event Hubs is een verwerkingsservice van gebeurtenissen, die zeer grote hoeveelheden gebeurtenissen en telemetriegegevens verzamelt in de cloud, met een lage latentie en hoge betrouwbaarheid. Deze service is met name geschikt voor:

- Toepassingsinstrumentatie
- Verwerking van gebruikerservaringen of werkstromen
- Internet der dingen (IoT)-scenario’s

Andere belangrijke mogelijkheden van Event Hubs zijn het bijhouden van het gebruik van mobiele apps, verzamelen van informatie over gegevensverkeer van web-farms, het vastleggen van spelgebeurtenissen in consolegames of het verzamelen van telemetriegegevens van industriële machines of verbonden voertuigen.

In tegenstelling tot [Service Bus-wachtrijen en -onderwerpen](../service-bus/service-bus-messaging-overview.md) is Event Hubs gericht op de verwerking van berichtenstromen van elke gewenste omvang. De mogelijkheden van Event Hubs verschillen van de Service Bus-onderwerpen doordat ze bijvoorbeeld sterk zijn gericht op scenario's met hoge doorvoer en het verwerken van gebeurtenissen. Als gevolg biedt Event Hubs niet alle berichtenmogelijkheden die beschikbaar zijn voor [onderwerpen](../service-bus/service-bus-fundamentals-hybrid-solutions.md#topics). Als u deze mogelijkheden nodig hebt, blijft onderwerpen de beste keuze.

## Volgende stappen

Lees de volgende onderwerpen voor gedetailleerde informatie over Event Hubs.

- [Overzicht van Event Hubs](event-hubs-overview.md)
- [Event Hubs-programmeergids](event-hubs-programming-guide.md)
- [Beschikbaarheid van en veelgestelde vragen over Event Hubs](event-hubs-availability-and-support-faq.md)
- Aan de slag met een [Event Hubs-zelfstudie][]
- Een complete [voorbeeldtoepassing die gebruikmaakt van Event Hubs][]

[Event Hubs-zelfstudie]: event-hubs-csharp-ephcs-getstarted.md
[voorbeeldtoepassing die gebruikmaakt van Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097


<!--HONumber=Jun16_HO2-->


