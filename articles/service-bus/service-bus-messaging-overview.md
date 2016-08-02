<properties
    pageTitle="Overzicht van Service Bus-berichtenservice | Microsoft Azure"
    description="Service Bus-berichtenservice: flexibele levering van gegevens in de cloud"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="03/09/2016"
    ms.author="sethm"/>


# Service Bus-berichtenservice: flexibele levering van gegevens in de cloud

De Azure Service Bus-berichtenservice is een betrouwbare service voor de levering van informatie. Het doel van deze service is om communicatie te vergemakkelijken. Wanneer twee of meer partijen informatie willen uitwisselen, hebben ze een communicatiemechanisme nodig. De Service Bus-berichtenservice is een brokered communicatiemechanisme, of een communicatiemechanisme van derden. Het is vergelijkbaar met een postservice in de fysieke wereld. Postservices maken het gemakkelijk om verschillende soorten brieven en pakketten met tal van verschillende leveringsgaranties overal ter wereld te leveren.

Net zoals een postservice die brieven levert, is de Service Bus-berichtenservice een flexibele leverancier van informatie voor zowel de afzender als de ontvanger. De berichtenservice zorgt ervoor dat de informatie wordt afgeleverd, zelfs als de twee partijen nooit allebei op hetzelfde moment online zijn of als ze niet op precies hetzelfde moment beschikbaar zijn. In die zin is het verzenden van berichten vergelijkbaar met het verzenden van een brief, terwijl niet-brokered communicatie vergelijkbaar is met een telefonische oproep (uit de tijd dat er nog geen wachtstand en beller-id was, want die services komen meer in de richting van Brokered Messaging).

De afzender van het bericht kan ook een aantal verschillende leveringskenmerken vereisen, zoals transacties, detectie van duplicaten, vervallen van berichten op basis van tijd en batchverzending. Ook deze patronen laten zich vergelijken met een postservice: herhaalde levering, vereisen van een handtekening, adreswijziging of intrekken van het bericht.

Service Bus ondersteunt twee verschillende berichtpatronen: *Relayed* Messaging en *Brokered* Messaging.

## Relayed Messaging

Het [Relay](service-bus-relay-overview.md)-onderdeel van Service Bus is een gecentraliseerde service (echter met maximale taakverdeling) die ondersteuning biedt voor tal van verschillende transportprotocollen en webservicestandaarden, waaronder SOAP, WS-* en zelfs REST. De [Relay-service](service-bus-dotnet-how-to-use-relay.md) biedt tal van verschillende Relay-connectiviteitsopties en kan helpen bij het onderhandelen over directe peer-to-peer-verbindingen wanneer dit mogelijk is. Service Bus is geoptimaliseerd voor .NET-ontwikkelaars die gebruikmaken van WCF (Windows Communication Foundation), zowel op het gebied van prestaties als op het gebied van bruikbaarheid, en biedt volledige toegang tot de bijbehorende Relay-service via SOAP- en REST-interfaces. Daardoor kunnen SOAP- of REST-programmeeromgevingen worden geïntegreerd met Service Bus.

De Relay-service ondersteunt traditionele berichten in één richting, aanvraag-/antwoordberichten en peer-to-peerberichten. De service ondersteunt tevens gebeurtenisdistributie via internet voor scenario's voor publiceren/abonneren en bidirectionele socket-communicatie voor verbeterde point-to-point-efficiëntie. In het Relayed Messaging-patroon maakt een on-premises service verbinding met de Relay-service via een uitgaande poort en wordt een bidirectionele socket voor communicatie gemaakt die is gekoppeld aan een bepaald rendezvous-adres. De client kan vervolgens met de on-premises service communiceren door berichten te verzenden naar de Relay-service die gericht is op het rendezvous-adres. De Relay-service stuurt vervolgens berichten door ('relay') naar de on-premises service via de reeds aanwezige bidirectionele socket. De client heeft geen rechtstreekse verbinding met de on-premises service nodig en hoeft niet te weten waar de service zich bevindt. Voor de on-premises service is niet vereist dat poorten voor inkomend verkeer zijn geopend in de firewall.

U start de verbinding tussen uw on-premises service en de Relay-service met een reeks WCF 'Relay'-bindingen. Achter de schermen worden de Relay-bindingen toegewezen aan nieuwe transportbindingselementen die zijn ontworpen om WCF-kanaalonderdelen te maken die kunnen worden geïntegreerd met Service Bus in de cloud.

Relayed Messaging biedt veel voordelen. Hiervoor is echter wel vereist dat de server en client allebei op hetzelfde moment online zijn om berichten te kunnen verzenden en ontvangen. Dit is niet optimaal voor HTTP-communicatie, waarbij de aanvragen doorgaans mogelijk geen lange levensduur hebben, noch voor clients die maar zo nu en dan verbinding maken, zoals browsers, mobiele toepassingen enzovoort. Brokered Messaging ondersteunt ontkoppelde communicatie en heeft voordelen op zich. Clients en servers kunnen wanneer nodig verbinding maken en hun bewerkingen asynchroon uitvoeren.

## Brokered Messaging

In tegenstelling tot het schema voor Relayed Messaging, kan [Brokered Messaging](service-bus-fundamentals-hybrid-solutions.md) worden beschouwd als asynchroon of 'tijdelijk ontkoppeld'. Producenten (afzenders) en consumenten (ontvangers) hoeven niet gelijktijdig online te zijn. De berichteninfrastructuur slaat berichten veilig op in een 'broker' (zoals een wachtrij) tot de ontvangende partij gereed is om ze te ontvangen. Hierdoor kunnen de onderdelen van de gedistribueerde toepassing worden losgekoppeld - hetzij vrijwillig, bijvoorbeeld voor onderhoud, hetzij vanwege het vastlopen van een onderdeel - zonder dat dit van invloed is op het hele systeem. Bovendien hoeft de ontvangende toepassing slechts op bepaalde tijdstippen gedurende de dag online te zijn. Denk bijvoorbeeld aan een systeem voor voorraadbeheer, dat alleen aan het einde van de dag hoeft te worden uitgevoerd.

De belangrijkste onderdelen van de Brokered Messaging-infrastructuur van Service Bus zijn wachtrijen, onderwerpen en abonnementen.  Het belangrijkste verschil is dat onderwerpen mogelijkheden voor publiceren/abonneren ondersteunen die kunnen worden gebruikt voor geavanceerde, op inhoud gebaseerde routering en logica voor aflevering, met inbegrip van verzending naar meerdere ontvangers. Deze onderdelen faciliteren nieuwe scenario’s voor asynchrone berichtverzending, zoals tijdelijke ontkoppeling, publiceren/abonneren en taakverdeling. Zie [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md) voor meer informatie over deze berichtentiteiten.

Net als bij de Relayed Messaging-infrastructuur wordt de Brokered Messaging-functie aangeboden voor programmeurs van WCF en .NET Framework en via REST.

## Volgende stappen

Zie de volgende onderwerpen voor meer informatie over de Service Bus-berichtenservice

- [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
- [Grondbeginselen van Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus-architectuur](service-bus-architecture.md)
- [Service Bus-wachtrijen gebruiken](service-bus-dotnet-how-to-use-queues.md)
- [Service Bus-onderwerpen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
 



<!--HONumber=Jun16_HO2-->


