<properties 
    pageTitle="Service Bus-architectuur | Microsoft Azure"
    description="Beschrijft de berichtverwerkingsarchitectuur van Azure Service Bus."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="04/19/2016"
    ms.author="sethm" />

# Service Bus-architectuur

Dit artikel beschrijft de berichtverwerkingsarchitectuur van Azure Service Bus.

## Service Bus-schaaleenheden

Service Bus is georganiseerd op *schaaleenheden*. Een schaaleenheid is een implementatie-eenheid en bevat alle vereiste onderdelen om de service uit te voeren. Elke regio implementeert een of meer Service Bus-schaaleenheden.

Een Service Bus-naamruimte is toegewezen aan een schaaleenheid. De schaaleenheid verwerkt alle typen Service Bus-entiteiten: relays en Brokered Messaging-entiteiten (wachtrijen, onderwerpen, abonnementen). Een Service Bus-schaaleenheid bestaat uit de volgende onderdelen:

- **Een set van gateway-knooppunten.** Gateway knooppunten verifiëren inkomende aanvragen en verwerken relay-aanvragen. Elk gateway-knooppunt heeft een openbaar IP-adres.

- **Een set berichtbrokerknooppunten.** Berichtbrokerknooppunten verwerken aanvragen over berichtentiteiten.

- **Een gateway-store.** De gateway-store bevat de gegevens voor elke entiteit die is gedefinieerd in deze schaaleenheid. De gateway-store is bovenop een SQL Azure-database geïmplementeerd.

- **Meerdere berichten-stores.** Berichten-stores bevatten de berichten van alle wachtrijen, onderwerpen en abonnementen die zijn gedefinieerd in deze schaaleenheid. Het bevat ook alle abonnementsgegevens. Tenzij [gepartitioneerde berichtentiteiten](service-bus-partitioning.md) is ingeschakeld, wordt een wachtrij of onderwerp toegewezen aan één berichten-store. Abonnementen worden opgeslagen in dezelfde berichten-store als het bovenliggende onderwerp. Met uitzondering van de Service Bus [Premium Messaging](service-bus-premium-messaging.md), worden berichten-stores bovenop SQL Azure-databases geïmplementeerd.

## Containers

Aan elke berichtentiteit is een specifieke container toegewezen. Een container is een logische constructie die gebruikmaakt van precies één berichten-store om alle relevante gegevens voor deze container op te slaan. Elke container is toegewezen aan een berichtbrokerknooppunt. Er zijn meestal meer containers dan berichtbrokerknooppunten. Elk berichtbrokerknooppunt laadt daarom meerdere containers. De distributie van containers naar een berichtbrokerknooppunt is zodanig georganiseerd dat alle berichtbrokerknooppunten evenwichtig worden belast. Als het belastingspatroon verandert (als bijvoorbeeld een van de containers erg wordt belast), of als een berichtbrokerknooppunt tijdelijk niet beschikbaar is, worden de containers herverdeeld over de berichtbrokerknooppunten.

## Verwerken van inkomende berichtaanvragen

Wanneer een client een verzoek naar Service Bus verzendt, wordt het via de Azure Load Balancer doorgestuurd naar een van de gateway-knooppunten. Het gateway-knooppunt machtigt de aanvraag. Als de aanvraag heeft betrekking op een berichtentiteit (wachtrij, onderwerp, abonnement), zoekt het gateway-knooppunt de entiteit op in de gateway-store en bepaalt in welke bericht-store de entiteit zich bevindt. Vervolgens controleert het knooppunt welk berichtbrokerknooppunt deze container gebruikt en stuurt het bericht naar de betreffende berichtbrokerknooppunt. Het berichtbrokerknooppunt verwerkt de aanvraag en werkt de entiteitsstatus bij in de container-store. Het berichtbrokerknooppunt verzendt vervolgens het antwoord terug naar het gateway-knooppunt, wat een juiste reactie terugstuurt naar de client die de oorspronkelijke aanvraag heeft gedaan.

![Verwerken van inkomende berichtaanvragen](./media/service-bus-architecture/IC690644.png)

## Verwerken van inkomende relay-aanvragen

Wanneer een client een verzoek naar Service Bus verzendt, wordt het via de Azure Load Balancer doorgestuurd naar een van de gateway-knooppunten. Als de aanvraag een aanvraag voor luisteren is, maakt het gateway-knooppunt een nieuwe relay. Als de aanvraag een verbindingsaanvraag is voor een specifieke relay, verzendt het gateway-knooppunt de verbindingsaanvraag door naar het gateway-knooppunt dat eigenaar is van de relay. Het gateway-knooppunt dat eigenaar is van de relay verzendt een ontmoetingsaanvraag naar de luisterende client om een tijdelijk kanaal te maken naar het gateway-knooppunt dat de verbindingsaanvraag heeft ontvangen.

Wanneer de relay-verbinding tot stand is gebracht, kunnen de clients berichten uitwisselen via het gateway-knooppunt dat wordt gebruikt voor de ontmoeting.

![Verwerken van inkomende relay-aanvragen](./media/service-bus-architecture/IC690645.png)

## Volgende stappen

Nu dat u een overzicht heeft van de werking van Service Bus, kunt u via de volgende koppelingen aan de slag:

- [Overzicht van Service Bus-berichten](service-bus-messaging-overview.md)
- [Grondbeginselen van Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Een oplossing voor berichten in de wachtrij met behulp van Service Bus-wachtrijen.](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)



<!--HONumber=Jun16_HO2-->


