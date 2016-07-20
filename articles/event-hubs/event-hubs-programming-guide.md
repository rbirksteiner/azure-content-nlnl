<properties 
    pageTitle="Programmeerhandleiding voor Azure Event Hubs | Microsoft Azure"
    description="Hierin wordt beschreven hoe u kunt programmeren met Azure Event Hubs met behulp van de Azure .NET SDK."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="04/15/2016"
    ms.author="sethm" />

# Programmeerhandleiding voor Event Hubs

In dit onderwerp wordt beschreven hoe u kunt programmeren met Azure Event Hubs met behulp van de Azure .NET SDK. Er wordt uitgegaan van een basisbegrip van Event Hubs. Zie het [Overzicht van Event Hubs](event-hubs-overview.md) voor een conceptueel overzicht van Event Hubs.

## Gebeurtenisuitgevers

Het verzenden van gebeurtenissen naar een Event Hub verloopt via HTTP POST of via een AMQP 1.0-verbinding. Welke van deze twee methoden er het beste kan worden toegepast, is afhankelijk van het specifieke scenario. AMQP 1.0-verbindingen zijn brokered verbindingen in Service Bus. Ze zijn met name geschikt voor scenario‘s met vaak voorkomende hogere berichtvolumes en lagere latentievereisten, omdat ze een permanent berichtenkanaal bieden.

Event Hubs worden gemaakt en beheerd via de klasse [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Wanneer de beheerde .NET-API‘s worden gebruikt, zijn de klassen [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) en [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) de primaire constructs voor het publiceren van gegevens naar Event Hubs. [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) biedt het AMQP-communicatiekanaal dat wordt gebruikt voor het verzenden van gebeurtenissen naar de Event Hub. De klasse [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) vertegenwoordigt een gebeurtenis en wordt gebruikt om berichten te publiceren naar een Event Hub. Deze klasse bevat de hoofdtekst, bepaalde metagegevens en headerinformatie over de gebeurtenis. Tijdens het doorgeven van het object via een Event Hub worden er nog andere eigenschappen aan het [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx)-object toegevoegd.

## Aan de slag

De .NET-klassen die ondersteuning bieden voor Event Hubs, worden aangeboden in de Microsoft.ServiceBus.dll-assembly. Downloaden van het [Service Bus NuGet-pakket](https://www.nuget.org/packages/WindowsAzure.ServiceBus) is de eenvoudigste manier om te verwijzen naar de Service Bus-API en om de toepassing te configureren met alle Service Bus-afhankelijkheden. U kunt echter ook de [Pakketbeheerconsole](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) in Visual Studio gebruiken. Hiervoor voert u de volgende opdracht uit in het venster voor de [Pakketbeheerconsole](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```
Install-Package WindowsAzure.ServiceBus
```

## Een Event Hub maken

U kunt de klasse [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) gebruiken om Event Hubs te maken. Bijvoorbeeld:

```
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

In de meeste gevallen wordt aanbevolen om de methoden [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx) te gebruiken. Zo voorkomt u dat er uitzonderingen worden gegenereerd bij het opnieuw starten van de service. Bijvoorbeeld:

```
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Voor alle bewerkingen met betrekking tot het maken van Event Hubs, inclusief [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx), zijn in de betreffende naamruimte machtigingen voor **Beheren** vereist. Als u de machtigingen van de publicatie- of consumertoepassingen wilt beperken, kunt u het aanroepen van deze bewerkingen in productiecode vermijden door referenties met beperkte machtigingen te gebruiken.

De klasse [EventHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubdescription.aspx) bevat gegevens over een Event Hub, zoals de autorisatieregels, het bewaarinterval voor het bericht, de partitie-id‘s, de status en het pad U kunt deze klasse gebruiken om de metagegevens voor een Event Hub bij te werken.

## Een Event Hubs-client maken

De primaire klasse voor interactie met Event Hubs is [Microsoft.ServiceBus.Messaging.EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx). Deze klasse biedt mogelijkheden voor zowel verzenden als ontvangen. U kunt deze klasse instantiëren met behulp van de methode [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx), zoals wordt weergegeven in het volgende voorbeeld.

```
var client = EventHubClient.Create(description.Path);
```

Deze methode maakt gebruik van de Service Bus-verbindingsinformatie in het App-configuratiebestand in de sectie `appSettings`. Zie de documentatie voor de methode [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) voor een voorbeeld van de `appSettings`-XML die wordt gebruikt voor het opslaan van de Service Bus-verbindingsinformatie.

Een andere optie is om de client te maken uit een verbindingsreeks. Deze optie werkt goed bij het gebruik van Azure-werkrollen, omdat u de verbindingsreeks kunt opslaan in de configuratie-eigenschappen voor de werkrol. Bijvoorbeeld:

```
EventHubClient.CreateFromConnectionString("your_connection_string");
```

De verbindingsreeks heeft dezelfde indeling als in het App-configuratiebestand voor de vorige methoden:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=Manage;SharedAccessKey=[key]
```

Ten slotte is het ook mogelijk om een [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx)-object te maken op basis van een [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx)-exemplaar, zoals in het volgende voorbeeld wordt weergegeven.

```
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

Houd er rekening mee dat extra [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx)-objecten die zijn gemaakt op basis van een MessagingFactory-exemplaar, gebruikmaken van dezelfde onderliggende TCP-verbinding. Voor deze objecten geldt daarom een doorvoerlimiet aan clientzijde. De methode [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) maakt gebruik van één MessagingFactory. Als u van één afzender een zeer hoge doorvoersnelheid nodig hebt, kunt u meerdere MessagingFactory-exemplaren maken en vervolgens van elk MessagingFactory-exemplaar één [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx)-object.

## Gebeurtenissen verzenden naar een Event Hub

U kunt gebeurtenissen verzenden naar een Event Hub door een [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx)-exemplaar te maken en dit te verzenden via de methode [Send](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx). Deze methode heeft één [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx)-instantieparameter en verzendt deze synchroon naar een Event Hub.

## Gebeurtenisserialisatie

De klasse [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) heeft [vier overbelaste constructors](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.eventdata.aspx) die verschillende parameters hebben, zoals een object en serializer, een bytematrix of een stroom. Het is ook mogelijk om de klasse [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) te instantiëren en de hoofdstroom later in te stellen. Als u JSON gebruikt met [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx), kunt u **Encoding.UTF8.GetBytes()** gebruiken om de bytematrix op te halen voor een met JSON gecodeerde tekenreeks.

## Partitiesleutel

De klasse [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) heeft een eigenschap [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) waarmee de afzender een waarde kan opgeven die wordt gehasht om een partitietoewijzing te produceren. Gebruik van een partitiesleutel zorgt ervoor dat alle gebeurtenissen met dezelfde sleutel naar dezelfde partitie in de Event Hub worden verzonden. Vaak gebruikte partitiesleutels zijn gebruikerssessie-id's en unieke afzender-id‘s. De eigenschap [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) is optioneel en kan worden opgegeven bij het gebruik van de methode [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) of de methode [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx). Als u geen waarde opgeeft voor [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx), worden verzonden gebeurtenissen gedistribueerd naar partities die gebruikmaken van een round robin-model.

## Batchbewerkingen voor het verzenden van gebeurtenissen

Het verzenden van gebeurtenissen in batches kan de doorvoer aanzienlijk verhogen. De methode [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) heeft een **IEnumerable**-parameter van het type [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx). Hierbij wordt de hele batch als een atomic-bewerking verzonden naar de Event Hub.

```
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Let op: één batch mag niet groter zijn dan de limiet van 256 kB voor een gebeurtenis. Daarnaast maakt elk bericht in de batch gebruik van dezelfde uitgever-id. Het is de verantwoordelijkheid van de afzender om ervoor te zorgen dat de batch de maximale gebeurtenisgrootte niet overschrijdt. Als dit wel gebeurt, wordt aan clientzijde een **Verzendfout** gegenereerd.

## Asynchroon verzenden en op schaal verzenden

U kunt ook gebeurtenissen asynchroon verzenden naar een Event Hub. Asynchroon verzenden kan de snelheid verhogen waarmee een client gebeurtenissen verzendt. Zowel de methode [Send](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx) als de methode [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) is beschikbaar in asynchrone versies die een [Taak](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx)-object retourneren. Hoewel dit de doorvoer kan verhogen, kan het er ook voor zorgen dat de client gebeurtenissen blijft verzenden terwijl deze wordt beperkt door de Event Hubs-service. Bij onjuiste implementatie kan dit resulteren in fouten of verloren berichten. Daarnaast kunt u voor de client de eigenschap [RetryPolicy](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.cliententity.retrypolicy.aspx) gebruiken om de opties van de client voor direct opnieuw proberen te beheren.

## Partitieafzender maken

Hoewel gebeurtenissen meestal naar een Event Hub worden verzonden met behulp van een partitiesleutel, wilt u in sommige gevallen gebeurtenissen wellicht rechtstreeks naar een bepaalde partitie verzenden. Bijvoorbeeld:

```
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.createpartitionedsender.aspx) retourneert een [EventHubSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubsender.aspx)-object dat u kunt gebruiken om gebeurtenissen te publiceren in een specifieke Event Hub-partitie.

## Gebeurtenisconsumers

Event Hubs heeft twee primaire modellen voor gebeurtenisgebruik: directe ontvangers en abstracties van een hoger niveau, zoals [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx). Directe ontvangers zijn verantwoordelijk voor hun eigen coördinatie van de toegang tot partities binnen een consumergroep.

### Directe consumer

De meest directe manier om een partitie in een consumergroep te lezen, is met behulp van de klasse [EventHubReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.aspx). Als u een instantie van deze klasse wilt maken, moet u een exemplaar van de klasse [EventHubConsumerGroup](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.aspx) gebruiken. In het volgende voorbeeld moet de partitie-id worden opgegeven bij het maken van de ontvanger voor de consumergroep.

```
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

De methode [CreateReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.createreceiver.aspx) heeft verschillende overloads die het beheer vergemakkelijken van de lezer die wordt gemaakt. Deze methoden omvatten het opgeven van een offset als een tekenreeks of tijdstempel, en de mogelijkheid om op te geven of deze opgegeven offset moet worden opgenomen in de geretourneerde stroom of later moet worden gestart. Nadat u de ontvanger hebt gemaakt, kunt u gebeurtenissen ontvangen op het geretourneerde object. De methode[Receive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.receive.aspx) heeft vier overloads die de ontvangstparameters beheren, zoals batchgrootte en wachttijd. U kunt de asynchrone versies van deze methoden gebruiken om de doorvoer van een consumer te verhogen. Bijvoorbeeld:

```
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
}
```

De berichten worden in een specifieke partitie ontvangen in de volgorde waarin ze naar de Event Hub zijn verzonden. De offset is een tekenreekstoken dat wordt gebruikt voor het identificeren van een bericht in een partitie.

Let op: een afzonderlijke partitie in een consumergroep mag niet meer dan 5 lezers tegelijk hebben. Wanneer lezers verbinding maken of de verbinding verbreken, kunnen hun sessies gedurende enkele minuten actief blijven voordat wordt herkend dat de verbinding is verbroken. Gedurende deze tijd kan het opnieuw verbinding maken met een partitie mislukken. Zie [Directe ontvangers voor Service Bus Event Hubs](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6) voor een compleet voorbeeld van het schrijven van een directe ontvanger voor Event Hubs.

### Gebeurtenisprocessorhost

Met de klasse [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) worden gegevens uit Event Hubs verwerkt. Gebruik deze implementatie bij het bouwen van gebeurtenislezers op het .NET-platform. [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) biedt een thread-veilige, beveiligde runtimeomgeving met meerdere processen voor implementaties van gebeurtenisprocessors die ook beheer biedt van controlepunten en partitielease.

Als u de klasse [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) wilt gebruiken, kunt u [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx) implementeren. Deze interface bevat drie methoden:

- [OpenAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.openasync.aspx)

- [CloseAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.closeasync.aspx)

- [ProcessEventsAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.processeventsasync.aspx)

Instantieer [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) en bied de juiste parameters voor de Event Hub om de verwerking van de gebeurtenis te starten. Roep vervolgens [RegisterEventProcessorAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.registereventprocessorasync.aspx) aan om de [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx)-implementatie te registreren met de runtime. Vervolgens probeert de host met behulp van een greedy-algoritme een lease te verkrijgen op elke partitie in de Event Hub. Deze leases zijn gedurende een bepaald tijdsbestek geldig en moeten daarna worden vernieuwd. Zodra nieuwe knooppunten, in dit geval werkrolexemplaren, online komen, worden er reserveringen voor leases geplaatst. Later verschuift de werklast tussen de knooppunten wanneer elk knooppunt probeert om meer leases te verkrijgen.

![Gebeurtenisprocessorhost](./media/event-hubs-programming-guide/IC759863.png)

In de loop van de tijd komt er een evenwicht tot stand. Dankzij deze dynamische mogelijkheid kan er op consumers automatisch schalen op basis van CPU worden toegepast, zowel voor omhoog als voor omlaag schalen. Omdat Event Hubs niet beschikken over een direct concept voor berichtentelling, is het gemiddelde CPU-gebruik vaak de beste manier om back-end- of consumerschaling te meten. Als uitgevers meer gebeurtenissen publiceren dan consumers kunnen verwerken, kan de toename van het CPU-gebruik voor consumers worden gebruikt om het aantal werkrolexemplaren automatisch te schalen.

Met de klasse [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) wordt ook een op Azure Storage gebaseerd mechanisme van controlepunten geïmplementeerd. Hiermee wordt de offset opgeslagen op basis van partitie, zodat elke consumer kan bepalen wat het laatste controlepunt van de vorige consumer was. Terwijl partities via leases schakelen tussen knooppunten, maakt dit synchronisatiemechanisme de verschuiving van werklasten mogelijk.

## Uitgever intrekken

Naast de geavanceerde runtimeonderdelen van [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) maken Event Hubs het intrekken van uitgevers mogelijk om ervoor te zorgen dat specifieke uitgevers geen gebeurtenis kunnen verzenden naar een Event Hub. Deze onderdelen zijn vooral nuttig als een token van een uitgever is aangetast of als dit niet naar behoren functioneert vanwege een software-update. In deze gevallen kan de uitgever-id, die onderdeel is van het bijbehorende SAS-token, worden geblokkeerd voor het uitgeven van gebeurtenissen.

Zie het voorbeeld [Op grote schaal veilig publiceren met Service Bus Event Hubs](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) voor meer informatie over het intrekken van uitgevers en over het als uitgever verzenden naar Event Hubs.

## Volgende stappen

Volg deze koppelingen voor meer informatie over Event Hubs-scenario‘s:

- [Event Hubs-API-overzicht](event-hubs-api-overview.md)
- [Event Hubs-overzicht](event-hubs-overview.md)
- [Event Hubs-codevoorbeelden](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hub&f[0].Type=SearchText&ac=5)
- [API-verwijzing voor de gebeurtenisprocessorhost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx)



<!--HONumber=Jun16_HO2-->


