<properties
    pageTitle="Service Bus-wachtrijen gebruiken (.NET) | Microsoft Azure"
    description="Informatie over het gebruiken van Service Bus-wachtrijen in Azure Codevoorbeelden geschreven in C# met de .NET API."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sethm"/>

# Service Bus-wachtrijen gebruiken

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In dit artikel wordt het gebruik van de Service Bus-wachtrijen beschreven. De voorbeelden zijn geschreven in C# en gebruiken de .NET API. De scenario's die aan bod komen zijn onder meer het maken van wachtrijen en het verzenden en ontvangen van berichten. Zie de sectie [Volgende stappen](#next-steps) voor meer informatie over wachtrijen.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Het Service Bus NuGet-pakket toevoegen

Het [Service Bus NuGet-pakket](https://www.nuget.org/packages/WindowsAzure.ServiceBus) is de eenvoudigste manier om de Service Bus-API op te halen en om de toepassing te configureren met alle Service Bus-afhankelijkheden. Ga als volgt te werk om het NuGet-pakket in uw toepassing te installeren:

1.  Klik in Solution Explorer met de rechtermuisknop op **Verwijzingen** en klik vervolgens op **NuGet-pakketten beheren**.
2.  Zoek ‘Service Bus’ en selecteer het item **Microsoft Azure Service Bus**. Klik op **Installeren** om de installatie te voltooien en sluit vervolgens dit dialoogvenster.

    ![][7]

U bent nu klaar om code te schrijven voor Service Bus.

## Een Service Bus-verbindingsreeks instellen

Service Bus maakt gebruik van een verbindingsreeks om eindpunten en referenties op te slaan. U kunt de verbindingsreeks in een configuratiebestand plaatsen in plaats van hiervoor hard-coding te gebruiken:

- Wanneer u Azure Cloud Services gebruikt, wordt het aanbevolen om de verbindingsreeks met het Azure-serviceconfiguratiesysteem (.csdef- en .cscfg-bestanden) op te slaan.
- Wanneer u Azure-websites of virtuele Azure-machines gebruikt, wordt het aanbevolen om de verbindingsreeks met het .NET-configuratiesysteem (bijvoorbeeld het Web.config-bestand) op te slaan.

In beide gevallen kunt u de verbindingsreeks ophalen met de [CloudConfigurationManager.GetSetting][GetSetting]-methode (zie verderop in dit artikel).

### De verbindingsreeks configureren

U kunt met het serviceconfiguratiemechanisme configuratie-instellingen dynamisch wijzigen vanuit de [klassieke Azure-portal][] zonder dat u de toepassing opnieuw hoeft te implementeren. Voeg bijvoorbeeld een `Setting`-label toe aan het .csdef-bestand (het bestand met de servicedefinitie), zoals wordt weergegeven in het volgende voorbeeld.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```
Vervolgens geeft u waarden op in het .cscfg-bestand (het bestand met de serviceconfiguratie), zoals in het volgende voorbeeld.

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Gebruik de SAS-sleutelnaam (Shared Access Signature) en sleutelwaarden die zijn opgehaald via de Klassieke Azure-portal (zie de vorige sectie).

### De verbindingsreeks configureren bij gebruik van websites of virtuele Azure-machines

Bij gebruik van websites of virtuele machines wordt het aanbevolen het .NET-configuratiesysteem te gebruiken (bijvoorbeeld **Web.config**). U slaat de verbindingsreeks op met het `<appSettings>`-element.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Gebruik de SAS-naam en sleutelwaarden die u hebt opgehaald via de klassieke Azure-portal (zie de vorige sectie).

## Een wachtrij maken

U kunt beheerbewerkingen uitvoeren voor Service Bus-wachtrijen met de [NamespaceManager][]-klasse. Deze klasse biedt methoden voor het maken, opsommen en verwijderen van wachtrijen.

In dit voorbeeld wordt een [NamespaceManager][]-object gemaakt met behulp van de [CloudConfigurationManager][]-klasse van Azure met een verbindingsreeks die bestaat uit het basisadres van een Service Bus-servicenaamruimte en de juiste SAS-referenties met machtigingen voor beheer. Deze verbindingsreeks heeft de volgende vorm.

````
Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey
````

Gebruik het volgende voorbeeld met de configuratie-instellingen uit de vorige sectie.

```
// Create the queue if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue("TestQueue");
}
```

Er zijn overloads van de [CreateQueue](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx)-methode waarmee u eigenschappen van de wachtrij kunt optimaliseren, bijvoorbeeld om de standaard TTL-waarde (time-to-live) in te stellen die moet worden toegepast op berichten die naar de wachtrij worden verzonden. Deze instellingen worden toegepast met de [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx)-klasse. Het volgende voorbeeld laat zien hoe u een wachtrij met de naam `TestQueue` en een maximale grootte van 5 GB, en een standaardbericht-TTL van 1 minuut maakt.

```
// Configure queue settings.
QueueDescription qd = new QueueDescription("TestQueue");
qd.MaxSizeInMegabytes = 5120;
qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new queue with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue(qd);
}
```

> [AZURE.NOTE] U kunt de [QueueExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx)-methode voor [NamespaceManager][]-objecten gebruiken om te controleren of een wachtrij met een opgegeven naam al in een servicenaamruimte bestaat.

## Berichten verzenden naar een wachtrij

Voor het verzenden van een bericht naar een Service Bus-wachtrij maakt een toepassing met de verbindingsreeks een [QueueClient][]-object.

De volgende code toont hoe u een [QueueClient][]-object maakt voor de `TestQueue`-wachtrij die u zojuist hebt gemaakt met de API-aanroep [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx).

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

Client.Send(new BrokeredMessage());
```

De berichten die worden verzonden naar en ontvangen van Service Bus-wachtrijen, zijn exemplaren van de [BrokeredMessage][]-klasse. [BrokeredMessage][]-objecten hebben een aantal standaardeigenschappen (zoals [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) en [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), een woordenlijst die wordt gebruikt om aangepaste toepassingsspecifieke eigenschappen te bewaren, en een hoofdtekst met willekeurige toepassingsgegevens. Een toepassing kan de hoofdtekst van het bericht instellen door elk serialiseerbaar object door te geven aan de constructor van het [BrokeredMessage][]-object waarna de juiste **DataContractSerializer** wordt gebruikt om het object te serialiseren. U kunt ook een **System.IO.Stream**-object opgeven.

Het volgende voorbeeld toont hoe u vijf testberichten verzendt naar het `TestQueue` [QueueClient][]-object dat is verkregen in het vorige codevoorbeeld.

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set some addtional custom app-specific properties.
  message.Properties["TestProperty"] = "TestValue";
  message.Properties["Message number"] = i;

  // Send message to the queue.
  Client.Send(message);
}
```

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een limiet voor de totale grootte van de berichten in een wachtrij. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB. Als partitioneren is ingeschakeld, is de bovengrens hoger. Zie [Partitioned messaging entities](service-bus-partitioning.md) (Gepartitioneerde berichtentiteiten) voor meer informatie.

## Berichten van een wachtrij ontvangen

De aanbevolen manier om berichten te ontvangen van een wachtrij is het gebruik van een [QueueClient][]-object. [QueueClient][]-objecten kunnen werken in twee verschillende modi: [ReceiveAndDelete en PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Wanneer u de **ReceiveAndDelete**-modus gebruikt, wordt het ontvangen in één bewerking uitgevoerd; dat wil zeggen als Service Bus een leesaanvraag voor een bericht in een wachtrij ontvangt, wordt voor het bericht aangegeven dat het is verbruikt en wordt het bericht naar de toepassing geretourneerd. **ReceiveAndDelete** is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing het niet verwerken van een bericht bij een fout kan tolereren. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt. Omdat Service Bus het bericht als verbruikt heeft gemarkeerd, ontbreekt het bericht dat voor het vastlopen is verbruikt wanneer de toepassing opnieuw wordt gestart en het verbruik van berichten opnieuw begint.

In de **PeekLock**-modus (dit is de standaardmodus) wordt de ontvangst een bewerking met twee fasen, waardoor er toepassingen kunnen worden ondersteund die geen ontbrekende berichten kunnen tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of het bericht veilig heeft opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangstproces voltooid door [Complete][] aan te roepen voor het ontvangen bericht. Wanneer Service Bus de aanroep [Complete][] waarneemt, wordt het bericht gemarkeerd als verbruikt en wordt het uit de wachtrij verwijderd.

Het volgende voorbeeld laat zien hoe berichten kunnen worden ontvangen en verwerkt met de standaardmodus **PeekLock**. Als u een andere [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)-waarde wilt opgeven, kunt u een andere overload van [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx) gebruiken. In dit voorbeeld wordt de [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) callback gebruikt om berichten te verwerken wanneer ze bij `TestQueue` aankomen.

```
string connectionString =
  CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
QueueClient Client =
  QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

// Callback to handle received messages.
Client.OnMessage((message) =>
{
    try
    {
        // Process message from queue.
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Test Property: " +
        message.Properties["TestProperty"]);

        // Remove message from queue.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in queue.
        message.Abandon();
    }
}, options);
```

In dit voorbeeld wordt de [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) callback geconfigureerd met een [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx)-object. [AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) wordt ingesteld op **false** om handmatig beheer van het aanroepen van [Complete][] in te schakelen voor het ontvangen bericht. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) is ingesteld op 1 minuut, zodat de client maximaal één minuut wacht op een bericht voordat de functie wordt beëindigd en de client een nieuwe aanroep doet om op berichten te controleren. Met deze eigenschapswaarde wordt het aantal keren gereduceerd dat de client toerekenbare aanroepen doet waarmee geen berichten worden opgehaald.

## Het vastlopen van de toepassing en onleesbare berichten afhandelen

Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan de methode [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) voor het ontvangen bericht worden aangeroepen (in plaats van de methode [Complete][]). Dit zorgt ervoor dat Service Bus het bericht in de wachtrij ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde consumerende toepassing of door een andere consumerende toepassing.

Daarnaast is er een time-out gekoppeld aan een bericht dat in de wachtrij is vergrendeld. Als de toepassing het bericht niet kan verwerken voordat de time-out van de vergrendeling verloopt (bijvoorbeeld als de toepassing vastloopt), dan ontgrendelt Service Bus het bericht automatisch en wordt het beschikbaar gemaakt om opnieuw te worden ontvangen.

In het geval dat de toepassing is vastgelopen na het verwerken van het bericht, maar voordat de aanvraag [Complete][] is uitgegeven, wordt het bericht opnieuw bij de toepassing bezorgd wanneer de toepassing opnieuw wordt gestart. Dit wordt vaak **Ten minste eenmaal verwerken** genoemd; dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt, maar dat hetzelfde bericht in sommige situaties opnieuw kan worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, dan moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Dit wordt vaak bereikt met de [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx)-eigenschap van het bericht dat gelijk blijft bij meerdere bezorgingspogingen.

## Volgende stappen

Nu u de basisprincipes van Service Bus-wachtrijen hebt geleerd, kunt u deze koppelingen volgen voor meer informatie.

-   Meer informatie over Service Bus-berichtentiteiten in [Queues, topics, and subscriptions][].
-   Bouw met de [Service Bus Brokered Messaging .NET-zelfstudie][] een werkende toepassing waarmee berichten naar en van een Service Bus-wachtrij worden verzonden en ontvangen.
-   Download Service Bus-voorbeelden van [Azure-voorbeelden][] of raadpleeg het [overzicht van Service Bus-voorbeelden][].

  [klassieke Azure-portal]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
  [Service Bus Brokered Messaging .NET-zelfstudie]: service-bus-brokered-tutorial-dotnet.md
  [Azure-voorbeelden]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [overzicht van Service Bus-voorbeelden]: service-bus-samples.md
  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [Complete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx


<!--HONumber=Jun16_HO2-->


