<properties
    pageTitle="Overzicht van prijscategorieën voor Service Bus Premium en Standard Messaging | Microsoft Azure"
    description="Service Bus Premium en Standard Messaging"
    services="service-bus"
    documentationCenter=".net"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/16/2016"
    ms.author="darosa;sethm"/>

# Prijscategorieën voor Service Bus Premium en Standard Messaging 

Service Bus Brokered Messaging, dat berichtentiteiten zoals wachtrijen en onderwerpen omvat, combineert functies voor bedrijfsberichten met krachtige semantiek voor publiceren/abonneren in de cloud. Service Bus Brokered Messaging wordt gebruikt als de communicatie-backbone voor veel geavanceerde cloudoplossingen.

De *Premium*-laag van de Service Bus-berichtenservice zorgt voor de afhandeling van algemene klantaanvragen met betrekking tot de schaal, prestaties en beschikbaarheid van essentiële toepassingen. Hoewel de functiesets bijna identiek zijn, zijn deze twee lagen van de Service Bus-berichtenservice ontworpen voor verschillende gebruiksscenario’s.

In de onderstaande tabel worden enkele belangrijke verschillen uitgelicht.

| Premium                               | Standard                       |
|---------------------------------------|--------------------------------|
| Hoge doorvoersnelheid                       | Variabele doorvoersnelheid            |
| Voorspelbare prestaties               | Variabele latentie               |
| Voorspelbare prijzen                   | Variabel omslagstelsel voor betalen per gebruik |
| Mogelijkheid om de workload omhoog en omlaag te schalen | N.v.t.                            |
| Berichtgrootte > 256 kB                  | De berichtgrootte is 256 kB          |

**Azure Service Bus Premium Messaging** biedt isolatie van resources op de laag van CPU en geheugen, zodat elke workload van een klant geïsoleerd wordt uitgevoerd. Deze resourcecontainer wordt een *Messaging-eenheid* genoemd. Aan elke Premium-naamruimte wordt ten minste één Messaging-eenheid toegewezen. U kunt voor elke Service Bus Premium-naamruimte 1, 2 of 4 Messaging-eenheden aanschaffen. Een enkele workload of entiteit kan meerdere Messaging-eenheden omspannen en het aantal Messaging-eenheden kan naar wens worden gewijzigd, hoewel facturering plaatsvindt tegen een 24-uurs of dagelijks tarief. Dit resulteert in voorspelbare en herhaalbare prestaties voor uw Service Bus-oplossing.

Niet alleen zijn de prestaties beter voorspelbaar en beschikbaar, ze zijn ook sneller.  Service Bus Premium Messaging bouwt voort op de opslag-engine die in [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) werd geïntroduceerd. Met de Premium-laag zijn de piekprestaties veel sneller dan met de Standard-laag.

## Technische verschillen Premium Messaging

Hierna volgen een aantal verschillen tussen Premium en Standard Messaging.

### Gepartitioneerde entiteiten

Gepartitioneerde entiteiten worden ondersteund in Premium Messaging, maar ze werken niet hetzelfde als in de Standard- en Basic-lagen van de Service Bus-berichtenservice. Premium Messaging gebruikt geen SQL als gegevensarchief en biedt niet meer de mogelijke concurrentie voor resources die hoort bij een gedeeld platform. Partitioneren is daardoor niet nodig. Daarnaast is het aantal partities gewijzigd van 16 partities in de Standard-laag naar twee partities in Premium. Het hebben van 2 partities garandeert beschikbaarheid. Dit aantal is beter geschikt voor de Premium-runtime-omgeving. Zie [Partitioned messaging entities](service-bus-partitioning.md) (Gepartitioneerde berichtentiteiten) voor meer informatie over partitioneren.

### Express-entiteiten

Omdat Premium Messaging wordt uitgevoerd in een volledig geïsoleerde runtime-omgeving, zijn geen express-entiteiten meer nodig. Express-entiteiten worden daarom niet ondersteund in Premium-naamruimten. Zie de eigenschap [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx) voor meer informatie over de Express-functie.

## Volgende stappen

Zie de volgende onderwerpen voor meer informatie over de Service Bus-berichtenservice

- [Introducing Azure Service Bus Premium messaging (blogbericht)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introducing Azure Service Bus Premium messaging (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Overzicht van Service Bus-berichten](service-bus-messaging-overview.md)
- [Overzicht van Azure Service Bus-architectuur](service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus-wachtrijen gebruiken](service-bus-dotnet-how-to-use-queues.md)



<!--HONumber=Jun16_HO2-->


