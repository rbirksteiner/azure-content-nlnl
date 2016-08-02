<properties
    pageTitle="Service Bus-onderwerpen gebruiken met .NET | Microsoft Azure"
    description="Meer informatie over het gebruik van Service Bus-onderwerpen en -abonnementen met .NET in Azure. Codevoorbeelden zijn geschreven voor .NET-toepassingen."
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
    ms.date="05/06/2016"
    ms.author="sethm"/>

# Service Bus-onderwerpen en -abonnementen gebruiken

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In dit artikel wordt beschreven hoe u Service Bus-onderwerpen en -abonnementen gebruikt. De voorbeelden zijn geschreven in C# en gebruiken de .NET API's. De behandelde scenario's hebben betrekking op het maken van onderwerpen en abonnementen, het maken van abonnementsfilters, het verzenden van berichten naar een onderwerp, het ontvangen van berichten van een abonnement en het verwijderen van onderwerpen en abonnementen. Zie de sectie [Volgende stappen](#Next-steps) voor meer informatie over onderwerpen en abonnementen.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## De toepassing configureren voor het gebruik van Service Bus

Wanneer u een toepassing maakt die gebruikmaakt van Service Bus, moet u een verwijzing naar de Service Bus-assembly toevoegen en de bijbehorende naamruimten opnemen. De eenvoudigste manier om dit te doen, is het juiste NuGet-pakket te downloaden.

## Het Service Bus NuGet-pakket ophalen

Het [Service Bus NuGet-pakket](https://www.nuget.org/packages/WindowsAzure.ServiceBus) is de eenvoudigste manier om de Service Bus-API op te halen en om de toepassing te configureren met alle benodigde Service Bus-afhankelijkheden. Ga als volgt te werk om het NuGet-pakket in uw toepassing te installeren:

1.  Klik in Solution Explorer met de rechtermuisknop op **Verwijzingen** en klik vervolgens op **NuGet-pakketten beheren**.
2.  Zoek Service Bus en selecteer het item **Microsoft Azure Service Bus**. Klik op **Installeren** om de installatie te voltooien en sluit het volgende dialoogvenster.

    ![][7]

U bent nu klaar om code te schrijven voor Service Bus.

## Een Service Bus-verbindingsreeks instellen

Service Bus maakt gebruik van een verbindingsreeks om eindpunten en referenties op te slaan. U kunt de verbindingsreeks in een configuratiebestand plaatsen in plaats van hiervoor hard-coding te gebruiken:

- Wanneer u Azure-services gebruikt, wordt het aanbevolen om de verbindingsreeks met het Azure-serviceconfiguratiesysteem (.csdef- en .cscfg-bestanden) op te slaan.
- Wanneer u Azure Websites of Azure Virtual Machines gebruikt, wordt het aanbevolen om de verbindingsreeks met het .NET-configuratiesysteem (bijvoorbeeld het Web.config-bestand) op te slaan.

In beide gevallen kunt u de verbindingsreeks ophalen met de `CloudConfigurationManager.GetSetting`-methode (zie verderop in dit artikel).

### De verbindingsreeks configureren

U kunt met het serviceconfiguratiemechanisme configuratie-instellingen dynamisch wijzigen vanuit de [klassieke Azure-portal][] zonder dat u de toepassing opnieuw hoeft te implementeren. Voeg bijvoorbeeld een `Setting`-label toe aan het **.csdef**-bestand (het bestand met de servicedefinitie), zoals wordt weergegeven in het volgende voorbeeld.

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

Vervolgens geeft u waarden op in het bestand met de serviceconfiguratie (.cscfg).

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

Gebruik de SAS-sleutelnaam (Shared Access Signature) en sleutelwaarden die zijn opgehaald via de portal (zie de vorige sectie).

### De verbindingsreeks configureren bij gebruik van Azure Websites of Azure Virtual Machines

Bij gebruik van Azure Websites of Azure Virtual Machines wordt het aanbevolen het .NET-configuratiesysteem te gebruiken (bijvoorbeeld Web.config). U slaat de verbindingsreeks op met het `<appSettings>`-element.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Gebruik de waarden voor de SAS-naam en -sleutel die u hebt opgehaald via de [klassieke Azure-portal][] (zie de vorige sectie).

## Een onderwerp maken

U kunt voor Service Bus-onderwerpen en -abonnementen beheerbewerkingen uitvoeren met de klasse [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Deze klasse biedt methoden voor het maken, opsommen en verwijderen van onderwerpen.

In het volgende voorbeeld wordt een `NamespaceManager`-object gemaakt met de klasse `CloudConfigurationManager` van Azure met een verbindingsreeks die bestaat uit het basisadres van een Service Bus-naamruimte en de juiste SAS-referenties met machtigingen voor beheer. Deze verbindingsreeks heeft de volgende vorm.

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Gebruik het volgende voorbeeld met de configuratie-instellingen van de vorige sectie.

```
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Er zijn overloads van de [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx)-methode waarmee u eigenschappen van het onderwerp kunt instellen, bijvoorbeeld om de standaard TTL-waarde (time-to-live) in te stellen die moet worden toegepast op berichten die naar het onderwerp worden verzonden. Deze instellingen worden toegepast met de klasse [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx). Het volgende voorbeeld laat zien hoe u een onderwerp maakt met de naam **TestTopic** en een maximale grootte van 5 GB, en een standaardbericht-TTL van 1 minuut.

```
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [AZURE.NOTE] U kunt de [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx)-methode voor [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)-objecten gebruiken om te controleren of een onderwerp met een opgegeven naam al in een naamruimte bestaat.

## Een abonnement maken

U kunt ook onderwerpabonnementen maken met de klasse [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Abonnementen hebben een naam en kunnen een optioneel filter hebben waarmee de verzameling berichten wordt beperkt die aan de virtuele wachtrij van het abonnement wordt doorgegeven.

### Een abonnement maken met het standaardfilter (MatchAll)

Het **MatchAll**-filter is het standaardfilter dat wordt gebruikt als er bij het maken van een nieuw abonnement geen filter is opgegeven. Bij gebruik van het **MatchAll**-filter worden alle berichten die naar het onderwerp worden gepubliceerd, in de virtuele wachtrij van het abonnement geplaatst. Met het volgende voorbeeld maakt u een abonnement met de naam AllMessages en wordt het standaardfilter **MatchAll** gebruikt.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### Abonnementen met filters maken

U kunt ook filters instellen waarmee u kunt opgeven welke berichten die naar een onderwerp worden verzonden, moeten worden weergegeven in een bepaald onderwerpabonnement.

Het meest flexibele type filter dat wordt ondersteund door abonnementen, is de klasse [SqlFilter][] waarmee een subset van SQL92 wordt geïmplementeerd. SQL-filters worden uitgevoerd voor de eigenschappen van de berichten die worden gepubliceerd naar het onderwerp. Zie de [SqlFilter.SqlExpression][]-syntaxis voor meer informatie over de expressies die kunnen worden gebruikt met een SQL-filter.

In het volgende voorbeeld wordt een abonnement genaamd **HighMessages** gemaakt met een [SqlFilter][]-object dat alleen berichten selecteert die een aangepaste **MessageNumber**-eigenschap groter dan 3 hebben.

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

Op dezelfde manier wordt in het volgende voorbeeld een abonnement genaamd **LowMessages** gemaakt met een [SqlFilter][] dat alleen berichten selecteert die een **MessageNumber**-eigenschap minder dan of gelijk aan 3 hebben.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Wanneer er nu een bericht naar `TestTopic` wordt verzonden, wordt het altijd bezorgd bij ontvangers die zijn geabonneerd op het **AllMessages**-onderwerpabonnement en selectief bezorgd bij ontvangers die zijn geabonneerd op het **HighMessages**- en **LowMessages**-onderwerpabonnement (afhankelijk van de inhoud van het bericht).

## Berichten verzenden naar een onderwerp

Voor het verzenden van een bericht naar een Service Bus-onderwerp maakt een toepassing een [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)-object met de verbindingsreeks.

De volgende code laat zien hoe een [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)-object wordt gemaakt voor het **TestTopic**-onderwerp dat eerder is gemaakt met de [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx) API-aanroep.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

De berichten die worden verzonden naar Service Bus-onderwerpen, zijn exemplaren van de klasse [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)-objecten hebben een aantal standaardeigenschappen (zoals [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) en [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), een woordenlijst die wordt gebruikt om aangepaste toepassingsspecifieke eigenschappen te bewaren en de hoofdtekst met willekeurige toepassingsgegevens. De hoofdtekst van het bericht kan met een toepassing worden ingesteld door elk serialiseerbaar object door te geven aan de constructor van het [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)-object waarna de juiste **DataContractSerializer** wordt gebruikt om het object te serialiseren. Ook kan een **System.IO.Stream** worden geleverd.

Het volgende voorbeeld toont hoe vijf testberichten naar het **TestTopic** [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)-object worden verzonden dat is verkregen in het vorige codevoorbeeld. Merk op dat de [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx)-eigenschapswaarde van elk bericht varieert afhankelijk van de herhaling van de lus (deze bepaalt welke abonnementen het bericht ontvangen).

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een onderwerp, maar er is een limiet voor de totale grootte van de berichten in een onderwerp. De grootte van het onderwerp wordt gedefinieerd tijdens het maken, met een bovengrens van 5 GB. Als partitioneren is ingeschakeld, is de bovengrens hoger. Zie [Partitioned messaging entities](service-bus-partitioning.md) (Gepartitioneerde berichtentiteiten) voor meer informatie.

## Berichten van een abonnement ontvangen

De aanbevolen manier om berichten te ontvangen van een abonnement is het gebruik van een [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx)-object. [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx)-objecten kunnen werken in twee verschillende modi: [*ReceiveAndDelete* en *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Wanneer u de **ReceiveAndDelete**-modus gebruikt, wordt het ontvangen in één bewerking uitgevoerd; dat wil zeggen als Service Bus een leesaanvraag voor een bericht in een abonnement ontvangt, wordt voor het bericht aangegeven dat het is verbruikt en wordt het bericht naar de toepassing geretourneerd. De **ReceiveAndDelete**-modus is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing het niet verwerken van een bericht bij een fout kan tolereren. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt. Omdat Service Bus het bericht als verbruikt heeft gemarkeerd, ontbreekt het bericht dat voor het vastlopen is verbruikt wanneer de toepassing opnieuw wordt gestart en het verbruik van berichten opnieuw begint.

In de **PeekLock**-modus (dit is de standaardmodus) wordt het ontvangstproces een bewerking met twee fasen, waardoor er toepassingen kunnen worden ondersteund die geen ontbrekende berichten kunnen tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of het bericht veilig heeft opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangstproces voltooid door [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) aan te roepen voor het ontvangen bericht. Wanneer Service Bus de aanroep **Complete** waarneemt, wordt het bericht gemarkeerd als verbruikt en wordt het uit het abonnement verwijderd.

Het volgende voorbeeld laat zien hoe berichten kunnen worden ontvangen en verwerkt met de standaardmodus **PeekLock**. Als u een andere [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)-waarde wilt opgeven, kunt u een andere overload voor [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx) gebruiken. In dit voorbeeld wordt de [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx)-callback gebruikt om berichten te verwerken wanneer ze bij het **HighMessages**-abonnement aankomen.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

In dit voorbeeld wordt de [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx)-callback geconfigureerd met een [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx)-object. [AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) wordt ingesteld op **false** om handmatig beheer van het aanroepen van [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) in te schakelen voor het ontvangen bericht. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) is ingesteld op 1 minuut, zodat de client maximaal één minuut wacht voordat de functie voor automatisch verlengen wordt beëindigd en de client een nieuwe aanroep maakt om op berichten te controleren. Met deze eigenschapswaarde wordt het aantal keren gereduceerd dat de client toerekenbare aanroepen maakt waarmee geen berichten worden opgehaald.

## Het vastlopen van de toepassing en onleesbare berichten afhandelen

Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan de methode [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) voor het ontvangen bericht worden aangeroepen (in plaats van de methode [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Dit zorgt ervoor dat Service Bus het bericht in het abonnement ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde consumerende toepassing of door een andere consumerende toepassing.

Daarnaast is er een time-out gekoppeld aan een bericht dat in het abonnement is vergrendeld. Als de toepassing het bericht niet kan verwerken voordat de time-out van de vergrendeling verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en wordt het beschikbaar gemaakt om opnieuw te worden ontvangen.

In het geval dat de toepassing is vastgelopen na het verwerken van het bericht, maar voordat de aanvraag [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) is uitgegeven, wordt het bericht opnieuw bij de toepassing bezorgd wanneer de toepassing opnieuw wordt gestart. Dit wordt vaak *Ten minste eenmaal verwerken* genoemd; dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt maar dat hetzelfde bericht in sommige situaties opnieuw kan worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Dit wordt vaak bereikt met de [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx)-eigenschap van het bericht dat gelijk blijft bij meerdere bezorgingspogingen.

## Onderwerpen en abonnementen verwijderen

Het volgende voorbeeld laat zien hoe u het onderwerp **TestTopic** uit de **HowToSample**-servicenaamruimte verwijdert.

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Als een onderwerp wordt verwijderd, worden ook alle abonnementen verwijderd die zijn geregistreerd bij het onderwerp. Abonnementen kunnen ook afzonderlijk worden verwijderd. De volgende code laat zien hoe u een abonnement met de naam **HighMessages** uit het **TestTopic**-onderwerp verwijdert.

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## Volgende stappen

Nu u de basisprincipes van Service Bus-onderwerpen en -abonnementen hebt geleerd, volgt u deze koppelingen voor meer informatie.

-   [Wachtrijen, onderwerpen en abonnementen][].
-   [Voorbeeld van onderwerpfilters][]
-   API-naslaginformatie voor [SqlFilter][].
-   Bouw een werktoepassing op waarmee berichten naar en van een Service Bus-wachtrij worden verzonden en ontvangen: [Service Bus Brokered Messaging .NET-zelfstudie][].
-   Service Bus-voorbeelden: downloaden van [Azure-voorbeelden][] of raadpleeg het [overzicht](service-bus-samples.md).

  [klassieke Azure-portal]: http://manage.windowsazure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Wachtrijen, onderwerpen en abonnementen]: service-bus-queues-topics-subscriptions.md
  [Voorbeeld van onderwerpfilters]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Service Bus Brokered Messaging .NET-zelfstudie]: service-bus-brokered-tutorial-dotnet.md
  [Azure-voorbeelden]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2



<!--HONumber=Jun16_HO2-->


