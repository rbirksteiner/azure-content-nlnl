<properties 
    pageTitle="Azure Redis-cache gebruiken | Microsoft Azure" 
    description="Meer informatie over het verbeteren van de prestaties van uw Azure-toepassingen met Azure Redis-cache" 
    services="redis-cache,app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="05/31/2016" 
    ms.author="sdanie"/>

# Azure Redis-cache gebruiken

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Deze handleiding beschrijft hoe u aan de slag kunt gaan met **Azure Redis-cache**. Microsoft Azure Redis-cache is gebaseerd op de populaire open-source Redis-cache. Via Microsoft Azure Redis-cache hebt u toegang tot een beveiligde, toegewezen Redis-cache, beheerd door Microsoft. Een cache die u met Azure Redis-cache hebt gemaakt, is toegankelijk vanuit elke toepassing in Microsoft Azure.

Microsoft Azure Redis-cache is beschikbaar in de volgende lagen:

-   **Basic**: één knooppunt. Meerdere groottes tot 53 GB.
-   **Standard**: twee knooppunten (primair/replica). Meerdere groottes tot 53 GB. 99,9% SLA.
-   **Premium**: twee knooppunten (primair/replica) met maximaal 10 shards. Meerdere groottes van 6 GB tot 530 GB (neem voor meer informatie contact met ons op). Alle functies van de lagen Standard en Premium bieden ondersteuning voor [Redis-cluster](cache-how-to-premium-clustering.md), [Redis-persistentie](cache-how-to-premium-persistence.md) en [Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9% SLA.

Elke laag verschilt wat functies en prijzen betreft. Zie [Prijsdetails voor caches][] voor prijsinformatie.

In deze handleiding wordt beschreven hoe u de client [StackExchange.Redis][] kunt gebruiken met C\#-code. De volgende scenario's worden behandeld: **een cache maken en configureren**, **cacheclients configureren** en **objecten toevoegen aan en verwijderen uit de cache**. Bekijk het gedeelte [Volgende stappen][] voor meer informatie over het gebruik van Azure Redis-cache.  Zie [Een web-app maken met Redis-cache](cache-web-app-howto.md) voor een stapsgewijze zelfstudie over het maken van een ASP.NET MVC-web-app met Redis-cache.

<a name="getting-started-cache-service"></a>
## Aan de slag met Azure Redis-cache

Het is gemakkelijk om aan de slag te gaan met Azure Redis-cache Allereerst richt u een cache in en configureert u deze. Vervolgens configureert u de cacheclients, zodat deze toegang krijgen tot de cache. Nadat de cacheclients zijn geconfigureerd, kunt u deze gaan gebruiken.

-   [De cache maken][]
-   [De cacheclients configureren][]

<a name="create-cache"></a>
## Een cache maken

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### Voor toegang tot de cache nadat deze is gemaakt

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Zie [Azure Redis-cache configureren](cache-configure.md) voor meer informatie over het configureren van uw cache. 

<a name="NuGet"></a>
## De cacheclients configureren

Een cache die u met Azure Redis-cache hebt gemaakt, is toegankelijk vanuit elke Azure-toepassing. .NET-toepassingen die in Visual Studio zijn ontwikkeld, kunnen de cacheclient **StackExchange.Redis** gebruiken. Deze kan worden geconfigureerd met een NuGet-pakket, dat de configuratie van de cacheclienttoepassingen eenvoudiger maakt. 

>[AZURE.NOTE] Zie de GitHub-pagina [StackExchange.Redis][] en de [documentatie over de cacheclient StackExchange.Redis][] voor meer informatie.

Als u in Visual Studio een clienttoepassing wilt configureren met het NuGet-pakket StackExchange.Redis, klikt u in **Solution Explorer** met de rechtermuisknop op het project en kiest u **Manage NuGet Packages**. 

![Manage NuGet Packages][NuGetMenu]

Typ in het zoekvak **StackExchange.Redis** of **StackExchange.Redis.StrongName**, selecteer de gewenste versie in de resultaten en klik op **Install**.

>[AZURE.NOTE] Als u liever een versie van de clientbibliotheek **StackExchange.Redis** gebruikt met een sterke naam, kiest u **StackExchange.Redis.StrongName**. Kies anders **StackExchange.Redis**.

![NuGet-pakket StackExchange.Redis][StackExchangeNuget]

Het NuGet-pakket downloadt de vereiste assembly-verwijzingen voor de clienttoepassing en voegt deze toe om met de cacheclient StackExchange.Redis toegang te krijgen tot de Azure Redis-cache.

Nadat uw clientproject is geconfigureerd voor het opslaan in de cache, kunt u de technieken die in de volgende gedeelten worden beschreven, gebruiken om met uw cache te werken.

<a name="working-with-caches"></a>
## Werken met caches

De stappen in deze sectie beschrijven hoe u met uw cache algemene taken uitvoert.

-   [Verbinding maken met de cache][]
-   [Objecten toevoegen aan en ophalen uit de cache][]
-   [Werken met .NET-objecten in de cache](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## Verbinding maken met de cache

Als u via een programma met een cache wilt werken, hebt u een verwijzing naar de cache nodig. Voeg het volgende toe boven aan alle bestanden die u wilt gebruiken om met de client StackExchange.Redis toegang te krijgen tot een Azure Redis-cache.

    using StackExchange.Redis;

>[AZURE.NOTE] Voor de client StackExchange.Redis is .NET Framework 4 of hoger vereist.

De verbinding met de Azure Redis-cache wordt beheerd door de klasse `ConnectionMultiplexer`. Deze klasse is ontworpen om te worden gedeeld en opnieuw te worden gebruikt in uw clienttoepassing en hoeft niet per bewerking te worden gemaakt. 

Als u verbinding wilt maken met een Azure Redis-cache en een exemplaar van een verbonden `ConnectionMultiplexer` wilt ontvangen, roept u de statische `Connect`-methode aan en geeft u het cache-eindpunt en -sleutel door, zoals in het volgende voorbeeld. Gebruik de sleutel die vanuit de Azure Portal is gegenereerd, als wachtwoordparameter.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Waarschuwing: sla nooit referenties op in de broncode. Ik geef ze hier weer in de broncode om dit voorbeeld eenvoudig te houden. Zie [Tekenreeksen van toepassingen en verbindingsreeksen][] voor informatie over het opslaan van referenties.

Als u geen gebruik wilt maken van SSL, kunt u `ssl=false` instellen of de parameter `ssl` weglaten.

>[AZURE.NOTE] De poort zonder SSL-beveiliging is standaard uitgeschakeld voor nieuwe caches. Zie [Toegangspoorten](cache-configure.md#access-ports) voor instructies over het inschakelen van de poort zonder SSL-beveiliging.

U kunt een exemplaar van `ConnectionMultiplexer` in uw toepassing delen door een statische eigenschap in te stellen die een verbonden exemplaar retourneert, zoals in het volgende voorbeeld. Dit is een thread-veilige manier om slechts één verbonden exemplaar van `ConnectionMultiplexer` te initialiseren. In deze voorbeelden is `abortConnect` ingesteld op false. Dit betekent dat de aanroep slaagt, zelfs als er geen verbinding is gemaakt met de Azure Redis-cache. Een belangrijke functie van `ConnectionMultiplexer` is dat deze de verbinding met de cache automatisch herstelt als het netwerkprobleem of de andere oorzaken zijn opgelost.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

Zie [Configuratiemodel StackExchange.Redis][] voor meer informatie over geavanceerde opties voor de configuratie van verbindingen. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

Wanneer de verbinding is gemaakt, moet u een verwijzing retourneren naar de database van Redis-cache door de `ConnectionMultiplexer.GetDatabase`-methode aan te roepen. Het object dat door de `GetDatabase`-methode wordt geretourneerd, is een lichtgewicht doorvoerobject en hoeft niet te worden opgeslagen.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Nu u weet hoe u verbinding maakt met een exemplaar van een Azure Redis-cache en hoe u een verwijzing naar de cachedatabase retourneert, gaat u leren hoe u de cache gebruikt.

<a name="add-object"></a>
## Objecten toevoegen aan en ophalen uit de cache

Items kunnen worden opgeslagen in en opgehaald uit de cache met behulp van de `StringSet`- en `StringGet`-methoden.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis slaat de meeste gegevens op als Redis-tekenreeksen, maar deze tekenreeksen kunnen veel soorten gegevens bevatten, waaronder geserialiseerde binaire gegevens die kunnen worden gebruikt voor het opslaan van .NET-objecten in de cache.

Als u `StringGet` aanroept en het object bestaat, wordt het geretourneerd. Als dit niet het geval is, wordt `null` geretourneerd. In dit geval kunt u de waarde van de gewenste gegevensbron ophalen en voor later gebruik opslaan in de cache. Dit wordt het 'cache-aside'-patroon genoemd.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Als u de vervaldatum van een item in de cache wilt opgeven, gebruikt u de parameter `TimeSpan` van `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## Werken met .NET-objecten in de cache

Azure Redis-cache kan .NET-objecten en oudere gegevenstypen opslaan in de cache, maar voordat een .NET-object in de cache kan worden opgeslagen, moet het worden geserialiseerd. Dit is de verantwoordelijkheid van de ontwikkelaar van de toepassing. Het geeft de ontwikkelaar flexibiliteit bij de keuze van de serializer.

Een eenvoudige manier om objecten te serialiseren is met de serialisatiemethoden `JsonConvert` in [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1). Hiermee serialiseert u van en naar JSON. In het volgende voorbeeld worden gegevens opgehaald en ingesteld met een exemplaar van het object `Employee`.


    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    
        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## Volgende stappen

Nu u de basisprincipes hebt geleerd, kunt u deze koppelingen volgen voor meer informatie over Azure Redis-cache.

-   Bekijk de ASP.NET-providers voor Azure Redis-cache.
    -   [State-provider voor Azure Redis-sessies](cache-aspnet-session-state-provider.md)
    -   [Cacheprovider voor ASP.NET-uitvoer in Azure Redis-cache](cache-aspnet-output-cache-provider.md)
-   [Schakel de diagnostische gegevens van de cache in](cache-how-to-monitor.md#enable-cache-diagnostics), zodat u de status van de cache kunt [bewaken](cache-how-to-monitor.md). U kunt de metrische gegevens weergeven in de Azure Portal. U kunt ze ook [downloaden en bekijken](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) met de hulpprogramma's van uw keuze.
-   Bekijk de [documentatie over de cacheclient StackExchange.Redis][].
    -   Azure Redis-cache is toegankelijk vanuit veel Redis-clients en ontwikkelingstalen. Zie voor meer informatie [http://redis.io/clients][].
    -   Azure Redis-cache kan ook worden gebruikt met services zoals Redsmin. Zie voor meer informatie [Een Azure Redis-verbindingsreeks ophalen en deze gebruiken met Redsmin][].
-   Zie de [Redis][]-documentatie en lees meer informatie over de [Redis-gegevenstypen][] en [een inleiding van vijftien minuten tot de Redis-gegevenstypen][].



<!-- INTRA-TOPIC LINKS -->
[Volgende stappen]: #next-steps
[Inleiding tot Azure Redis-cache (video)]: #video
[Wat is Azure Redis-cache?]: #what-is
[Een Azure-cache maken]: #create-cache
[Op welke manier kan ik gegevens het beste in een cache opslaan?]: #choosing-cache
[Een Visual Studio-project voorbereiden om Azure-caches te gebruiken]: #prepare-vs
[Uw toepassing configureren voor gebruik met caches]: #configure-app
[Aan de slag met Azure Redis-cache]: #getting-started-cache-service
[De cache maken]: #create-cache
[De cache configureren]: #enable-caching
[De cacheclients configureren]: #NuGet
[Werken met caches]: #working-with-caches
[Verbinding maken met de cache]: #connect-to-cache
[Objecten toevoegen aan en ophalen uit de cache]: #add-object
[De vervaldatum van een object opgeven in de cache]: #specify-expiration
[De ASP.NET-sessiestatus in de cache opslaan]: #store-session

  
<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png






   
<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[In andere talen ontwikkelen voor Azure Redis-cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Een Azure Redis-verbindingsreeks ophalen en deze gebruiken met Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[State-provider voor Azure Redis-sessies]: http://go.microsoft.com/fwlink/?LinkId=398249
[Een cacheclient via een programma configureren]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[State-provider voor Azure-cachesessies]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric-cache: sessiestatus van cache]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Cacheprovider voor Azure-cache-uitvoer]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Teamblog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[Groottes van virtuele machines configureren]: http://go.microsoft.com/fwlink/?LinkId=164387
[Overwegingen bij het plannen van de capaciteitsplanning van Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[De cachefunctie van een ASP.NET-pagina verklarend instellen]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[De cachefunctie van een pagina via een programma instellen]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Een cache configureren in Azure Redis-cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Configuratiemodel StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Werken met .NET-objecten in de cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[Installatie van NuGet-pakketmanager]: http://go.microsoft.com/fwlink/?LinkId=240311
[Prijsdetails voor caches]: http://www.windowsazure.com/pricing/details/cache/
[Azure Portal]: https://portal.azure.com/

[Overzicht van Azure Redis-cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis-cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migreren naar Azure Redis-cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Voorbeelden van Azure Redis-cache]: http://go.microsoft.com/fwlink/?LinkId=320840
[Resourcegroepen gebruiken om Azure-resources te beheren]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[documentatie over de cacheclient StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis-gegevenstypen]: http://redis.io/topics/data-types
[een inleiding van vijftien minuten tot de Redis-gegevenstypen]: http://redis.io/topics/data-types-intro

[Tekenreeksen van toepassingen en verbindingsreeksen]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/





<!--HONumber=Jun16_HO2-->


