<properties 
    pageTitle="Een web-app maken met Redis-cache | Microsoft Azure" 
    description="Informatie over het maken van een web-app met Redis-cache" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.date="05/18/2016" 
    ms.author="sdanie"/>

# Een web-app maken met Redis-Cache

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

In deze zelfstudie ziet u hoe u een ASP.NET-webtoepassing maakt en implementeert in een web-app in Azure App Service met behulp van Visual Studio 2015. In de voorbeeldtoepassing wordt een lijst met teamstatistieken uit een database weergegeven. U maakt kennis met verschillende manieren waarop u Azure Redis-cache kunt gebruiken om gegevens op te slaan in en op te halen uit de cache. Wanneer u de zelfstudie hebt voltooid, hebt u een actieve web-app die naar een database leest en schrijft. Deze web-app is geoptimaliseerd met Azure Redis-cache en wordt gehost in Azure.

U leert het volgende:

-   Een ASP.NET MVC 5-webtoepassing maken in Visual Studio.
-   Toegang krijgen tot gegevens uit een database met behulp van Entity Framework.
-   De doorvoer van gegevens verbeteren en de belasting van de database verminderen door gegevens op te slaan en op te halen met Azure Redis-cache.
-   Een in Redis gesorteerde set gebruiken om de beste vijf teams op te halen.
-   De Azure-resources voor de toepassing inrichten met een ARM-sjabloon.
-   De toepassing publiceren in Azure met Visual Studio.

## Vereisten

U hebt het volgende nodig om deze zelfstudie te voltooien:

-   [Azure-account](#azure-account)
-   [Visual Studio 2015 met de Azure-SDK voor .NET](#visual-studio-2015-with-the-azure-sdk-for-net)

### Azure-account

U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt:

* [Gratis een Azure-account openen](/pricing/free-trial/?WT.mc_id=redis_cache_hero). U ontvangt tegoed dat kan worden gebruikt om betaalde Azure-services uit te proberen. Zelfs nadat het tegoed is gebruikt, kunt u het account houden en de gratis Azure-services en -functies gebruiken.
* [Uw voordelen als Visual Studio-abonnee activeren](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Via uw MSDN-abonnement ontvangt u elke maand tegoeden die u voor betaalde Azure-services kunt gebruiken.

### Visual Studio 2015 met de Azure-SDK voor .NET

De zelfstudie is geschreven voor Visual Studio 2015 met de [Azure-SDK voor .NET](../dotnet-sdk.md) 2.8.2 of hoger. [Download hier de nieuwste Azure-SDK voor Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Als u Visual Studio nog niet hebt, wordt dit automatisch geïnstalleerd samen met de SDK.

Als u Visual Studio 2013 hebt, kunt u [de nieuwste Azure-SDK voor Visual Studio 2013 downloaden](http://go.microsoft.com/fwlink/?LinkID=324322). Sommige schermen zien er mogelijk anders uit dan wordt weergegeven in de afbeeldingen in deze zelfstudie.

>[AZURE.NOTE] Afhankelijk van hoeveel van de SDK-afhankelijkheden u al op uw computer hebt staan, kan het installeren van de SDK lang duren, van enkele minuten tot een halfuur of meer.

## Het Visual Studio-project maken

1. Open Visual Studio en klik op **File**, **New**, **Project**.

2. Vouw het knooppunt **Visual C#** uit in de lijst **Templates**, selecteer **Cloud** en klik op **ASP.NET Web Application**. Zorg ervoor dat **.NET Framework 4.5.2** is geselecteerd.  Typ **ContosoTeamStats** in het tekstvak **Name**. Klik vervolgens op **OK**.
 
    ![Project maken][cache-create-project]

3. Selecteer **MVC** als het projecttype. Schakel het selectievakje **Host in the cloud** uit. In de volgende stappen in deze zelfstudie [richt u de Azure-resources in](#provision-the-azure-resources) en [publiceert u de toepassing in Azure](#publish-the-application-to-azure). Voor een voorbeeld van een App Service-web-app die is ingericht vanuit Visual Studio door **Host in the cloud** aangevinkt te laten, bekijkt u [Aan de slag met web-apps in Azure App Service met ASP.NET en Visual Studio](../app-service-web/web-sites-dotnet-get-started.md).

    ![De projectsjabloon selecteren][cache-select-template]

4. Klik op **OK** om het project te maken.

## De ASP.NET MVC-toepassing maken

In dit gedeelte van de zelfstudie maakt u de basistoepassing die teamstatistieken leest en weergeeft vanuit een database.

-   [Het model toevoegen](#add-the-model)
-   [De controller toevoegen](#add-the-controller)
-   [De weergaven configureren](#configure-the-views)

### Het model toevoegen

1. Klik in **Solution Explorer** met de rechtermuisknop op **Models** en kies **Add**, **Class**. 

    ![Het model toevoegen][cache-model-add-class]

2. Voer `Team` in als de naam van de klasse en klik op **Add**.

    ![De modelklasse toevoegen][cache-model-add-class-dialog]

3. Vervang de `using`-instructies boven aan het bestand `Team.cs` door de volgende using-instructies.


        using System;
        using System.Collections.Generic;
        using System.Data.Entity;
        using System.Data.Entity.SqlServer;


4. Vervang de definitie van klasse `Team` door het volgende codefragment. Dit fragment bevat een bijgewerkte definitie voor klasse `Team` en een aantal andere helperklassen van Entity Framework. Zie [Code First naar een nieuwe database](https://msdn.microsoft.com/data/jj193542) voor meer informatie over de Code First-aanpak voor Entity Framework die in deze zelfstudie wordt gebruikt.


        public class Team
        {
            public int ID { get; set; }
            public string Name { get; set; }
            public int Wins { get; set; }
            public int Losses { get; set; }
            public int Ties { get; set; }
        
            static public void PlayGames(IEnumerable<Team> teams)
            {
                // Simple random generation of statistics.
                Random r = new Random();
        
                foreach (var t in teams)
                {
                    t.Wins = r.Next(33);
                    t.Losses = r.Next(33);
                    t.Ties = r.Next(0, 5);
                }
            }
        }
        
        public class TeamContext : DbContext
        {
            public TeamContext()
                : base("TeamContext")
            {
            }
        
            public DbSet<Team> Teams { get; set; }
        }
        
        public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
        {
            protected override void Seed(TeamContext context)
            {
                var teams = new List<Team>
                {
                    new Team{Name="Adventure Works Cycles"},
                    new Team{Name="Alpine Ski House"},
                    new Team{Name="Blue Yonder Airlines"},
                    new Team{Name="Coho Vineyard"},
                    new Team{Name="Contoso, Ltd."},
                    new Team{Name="Fabrikam, Inc."},
                    new Team{Name="Lucerne Publishing"},
                    new Team{Name="Northwind Traders"},
                    new Team{Name="Consolidated Messenger"},
                    new Team{Name="Fourth Coffee"},
                    new Team{Name="Graphic Design Institute"},
                    new Team{Name="Nod Publishers"}
                };
        
                Team.PlayGames(teams);
        
                teams.ForEach(t => context.Teams.Add(t));
                context.SaveChanges();
            }
        }
        
        public class TeamConfiguration : DbConfiguration
        {
            public TeamConfiguration()
            {
                SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            }
        }


2. Dubbelklik in **Solution Explorer** op **web.config** om het bestand te openen.

    ![Web.config][cache-web-config]

3.  Voeg de volgende verbindingsreeks toe aan het gedeelte `connectionStrings`. De naam van de verbindingsreeks moet overeenkomen met de naam van de contextklasse van de Entity Framework-database. Dit is `TeamContext`.

        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />


    Nadat u deze hebt toegevoegd, moet het gedeelte `connectionStrings` eruitzien zoals in het volgende voorbeeld.


        <connectionStrings>
            <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-ContosoTeamStats-20160216120918.mdf;Initial Catalog=aspnet-ContosoTeamStats-20160216120918;Integrated Security=True"
                providerName="System.Data.SqlClient" />
            <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"  providerName="System.Data.SqlClient" />
        </connectionStrings>

### De controller toevoegen

1. Druk op **F6** om het project te bouwen. 
2. Klik in **Solution Explorer** met de rechtermuisknop op de map **Controllers** en kies vervolgens **Add**, **Controller**.

    ![Controller toevoegen][cache-add-controller]

3. Kies **MVC 5 Controller with views, using Entity Framework** en klik op **Add**. Als er een foutbericht wordt weergegeven nadat u op **Add** hebt geklikt, zorgt u ervoor dat u eerst het project bouwt.

    ![Controllerklasse toevoegen][cache-add-controller-class]

5. Selecteer in de vervolgkeuzelijst **Model class** de optie **Team (ContosoTeamStats.Models)**. Selecteer in de vervolgkeuzelijst **Data context class** de optie **TeamContext (ContosoTeamStats.Models)**. Typ `TeamsController` in het tekstvak voor de naam van de **Controller** (als dit niet automatisch wordt ingevuld). Klik op **Add** om de controllerklasse te maken en de standaardweergaven toe te voegen.

    ![Controller configureren][cache-configure-controller]

4. Vouw in **Solution Explorer** het bestand **Global.asax** uit en dubbelklik op **Global.asax.cs** om het te openen.

    ![Global.asax.cs][cache-global-asax]

5. Voeg boven aan het bestand, onder de andere using-instructies, de volgende twee using-instructies toe.


        using System.Data.Entity;
        using ContosoTeamStats.Models;


6. Voeg de volgende regel code toe aan het einde van de `Application_Start`-methode.


        Database.SetInitializer<TeamContext>(new TeamInitializer());


7. Vouw in **Solution Explorer** `App_Start` uit en dubbelklik op `RouteConfig.cs`.

    ![RouteConfig.cs][cache-RouteConfig-cs]

8. Vervang `controller = "Home"` in de volgende code in de `RegisterRoutes`-methode door `controller = "Teams"`, zoals weergegeven in het volgende voorbeeld.


        routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
        );


### De weergaven configureren

1. Vouw in **Solution Explorer** de map **Views** uit. Vouw vervolgens de map **Shared** uit en dubbelklik op **_Layout.cshtml**. 

    ![_Layout.cshtml][cache-layout-cshtml]

2. Wijzig de inhoud van het element `title` en vervang `My ASP.NET Application` door `Contoso Team Stats`, zoals weergegeven in het volgende voorbeeld.


        <title>@ViewBag.Title - Contoso Team Stats</title>


3. Werk in de sectie `body` de eerste instructie `Html.ActionLink` bij, vervang `Application name` door `Contoso Team Stats` en vervang `Home` door `Teams`.
    -   Voor: `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
    -   Na: `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`

    ![Codewijzigingen][cache-layout-cshtml-code]

4. Druk op **Ctrl+F5** om de toepassing op te bouwen en uit te voeren. Deze versie van de toepassing leest de resultaten rechtstreeks uit de database. De acties **Nieuw**, **Bewerken**, **Details** en **Verwijderen** zijn automatisch aan de toepassing toegevoegd door de structuur **MVC 5 Controller with views, using Entity Framework**. In het volgende gedeelte van de zelfstudie voegt u Redis-cache toe om de gegevenstoegang te optimaliseren en om extra functies te bieden voor de toepassing.

![Beginnerstoepassing][cache-starter-application]

## De toepassing configureren voor het gebruik van Redis-cache

In dit gedeelte van de zelfstudie configureert u de voorbeeldtoepassing om met behulp van de cacheclient [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) Contoso-teamstatistieken op te slaan en op te halen uit een exemplaar van de Azure Redis-cache.

-   [De toepassing configureren voor gebruik van StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
-   [De klasse TeamsController zodanig bijwerken dat deze resultaten retourneert uit de cache of de database](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
-   [De methoden Maken, Bewerken en Verwijderen bijwerken voor gebruik met de cache](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
-   [De weergave Teamindex bijwerken voor gebruik met de cache](#update-the-teams-index-view-to-work-with-the-cache)


### De toepassing configureren voor gebruik van StackExchange.Redis

1. Als u in Visual Studio een clienttoepassing wilt configureren met het NuGet-pakket StackExchange.Redis, klikt u in **Solution Explorer** met de rechtermuisknop op het project en kiest u **Manage NuGet Packages**. 

    ![Manage NuGet Packages][redis-cache-manage-nuget-menu]

2. Typ **StackExchange.Redis** in het zoekvak, selecteer de gewenste versie in de resultaten en klik op **Install**.

    ![NuGet-pakket StackExchange.Redis][redis-cache-stack-exchange-nuget]

    Het NuGet-pakket downloadt de vereiste assembly-verwijzingen voor de clienttoepassing en voegt deze toe om met de cacheclient StackExchange.Redis toegang te krijgen tot de Azure Redis-cache. Als u liever een versie van de clientbibliotheek **StackExchange.Redis** gebruikt met een sterke naam, kiest u **StackExchange.Redis.StrongName**. Kies anders **StackExchange.Redis**.

3. Vouw in **Solution Explorer** de map **Controllers** uit en dubbelklik op **TeamsController.cs** om dit bestand te openen.

    ![TeamsController][cache-teamscontroller]

4. Voeg de volgende twee using-instructies toe aan **TeamsController.cs**.

        using System.Configuration;
        using StackExchange.Redis;

5. Voeg de volgende twee eigenschappen toe aan de klasse `TeamsController`.

        // Redis Connection string info
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
  
1. Maak op uw computer een bestand met de naam `WebAppPlusCacheAppSecrets.config`. Sla het op een locatie op die niet wordt ingecheckt met de broncode van uw voorbeeldtoepassing, mocht u besluiten deze ergens in te checken. In dit voorbeeld bevindt het bestand `AppSettingsSecrets.config` zich op de locatie `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.

    Bewerk het bestand `WebAppPlusCacheAppSecrets.config` en voeg de volgende inhoud toe. Als u de toepassing lokaal uitvoert, wordt deze informatie gebruikt om verbinding te maken met uw exemplaar van Azure Redis-cache. Verderop in de zelfstudie richt u een exemplaar van Azure Redis-cache in en werkt u de cachenaam en het wachtwoord bij. Als u de voorbeeldtoepassing niet lokaal wilt uitvoeren, kunt u het maken van dit bestand en de hierop volgende stappen die naar het bestand verwijzen, overslaan. Wanneer u in Azure implementeert, haalt de toepassing de verbindingsgegevens van de cache namelijk op uit de app-instelling voor de web-app en niet uit dit bestand. Aangezien de `WebAppPlusCacheAppSecrets.config` niet samen met uw toepassing in Azure wordt geïmplementeerd, hebt u deze niet nodig, tenzij u de toepassing lokaal wilt uitvoeren.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


2. Dubbelklik in **Solution Explorer** op **web.config** om het bestand te openen.

    ![Web.config][cache-web-config]

3. Voeg het volgende `file`-kenmerk toe aan het element `appSettings`. Als u een andere bestandsnaam of -locatie gebruikt, vervang deze waarden dan door de waarden die in het voorbeeld worden weergegeven.
    -   Voor: `<appSettings>`
    -   Na: ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`

    De ASP.NET-runtime voegt de inhoud van het externe bestand samen met de opmaak van het element `<appSettings>`. Als het opgegeven bestand niet kan worden gevonden, negeert de runtime het bestandskenmerk. Uw geheimen (de verbindingsreeks naar uw cache) worden niet opgenomen in de broncode van de toepassing. Wanneer u uw web-app in Azure implementeert, wordt het bestand `WebAppPlusCacheAppSecrests.config` niet geïmplementeerd (dat is de bedoeling). Er zijn verschillende manieren om deze geheimen op te geven in Azure. In deze zelfstudie worden ze automatisch voor u geconfigureerd wanneer u in een latere stap van de zelfstudie [de Azure-resources inricht](#provision-the-azure-resources). Raadpleeg [Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) (Aanbevolen procedures voor het implementeren van wachtwoorden en andere gevoelige gegevens naar ASP.NET en Azure App Service) voor meer informatie over het werken met geheimen in Azure.


### De klasse TeamsController zodanig bijwerken dat deze resultaten retourneert uit de cache of de database

In dit voorbeeld worden teamstatistieken opgehaald uit de database of uit de cache. Teamstatistieken worden opgeslagen in de cache als een geserialiseerde `List<Team>` en ook als een gesorteerde set met Redis-gegevenstypen. Bij het ophalen van items uit een gesorteerde set kunt u een query uitvoeren voor sommige items, voor alle items of alleen voor bepaalde items. In dit voorbeeld voert u een query uit voor de gesorteerde set voor de beste vijf teams, op basis van het aantal overwinningen.

>[AZURE.NOTE] Het is niet vereist om de teamstatistieken in verschillende indelingen in de cache op te slaan om de Azure Redis-cache te kunnen gebruiken. In deze zelfstudie wordt gebruikgemaakt van meerdere indelingen ter illustratie van de verschillende manieren en gegevenstypen die u kunt gebruiken om gegevens in de cache op te slaan.



1. Voeg de volgende twee using-instructies toe aan het bestand `TeamsController.cs`. Doe dit bovenaan, bij de andere using-instructies.

        using System.Diagnostics;
        using Newtonsoft.Json;

2. Vervang de huidige `public ActionResult Index()`-methode door de volgende implementatie.


        // GET: Teams
        public ActionResult Index(string actionType, string resultType)
        {
            List<Team> teams = null;
        
            switch(actionType)
            {
                case "playGames": // Play a new season of games.
                    PlayGames();
                    break;
        
                case "clearCache": // Clear the results from the cache.
                    ClearCachedTeams();
                    break;
        
                case "rebuildDB": // Rebuild the database with sample data.
                    RebuildDB();
                    break;
            }
        
            // Measure the time it takes to retrieve the results.
            Stopwatch sw = Stopwatch.StartNew();
        
            switch(resultType)
            {
                case "teamsSortedSet": // Retrieve teams from sorted set.
                    teams = GetFromSortedSet();
                    break;
        
                case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                    teams = GetFromSortedSetTop5();
                    break;
        
                case "teamsList": // Retrieve teams from the cached List<Team>.
                    teams = GetFromList();
                    break;
        
                case "fromDB": // Retrieve results from the database.
                default:
                    teams = GetFromDB();
                    break;
            }
        
            sw.Stop();
            double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

            // Add the elapsed time of the operation to the ViewBag.msg.
            ViewBag.msg += " MS: " + ms.ToString();
        
            return View(teams);
        }


3. Voeg de volgende drie methoden voor de klasse `TeamsController` toe om de actietypen `playGames`, `clearCache` en `rebuildDB` te implementeren uit de switch-instructie die in het vorige codefragment is toegevoegd.

    De `PlayGames`-methode werkt de teamstatistieken bij door een seizoen aan wedstrijden te simuleren, slaat de resultaten vervolgens op in de database en wist de nu verouderde gegevens uit de cache.


        void PlayGames()
        {
            ViewBag.msg += "Updating team statistics. ";
            // Play a "season" of games.
            var teams = from t in db.Teams
                        select t;
    
            Team.PlayGames(teams);
    
            db.SaveChanges();
    
            // Clear any cached results
            ClearCachedTeams();
        }


    De `RebuildDB`-methode initialiseert de database opnieuw met de standaardset teams, genereert statistieken voor ze en wist de nu verouderde gegevens uit de cache.
    
        void RebuildDB()
        {
            ViewBag.msg += "Rebuilding DB. ";
            // Delete and re-initialize the database with sample data.
            db.Database.Delete();
            db.Database.Initialize(true);

            // Clear any cached results
            ClearCachedTeams();
        }


    De `ClearCachedTeams`-methode verwijdert opgeslagen teamstatistieken uit de cache.

    
        void ClearCachedTeams()
        {
            IDatabase cache = Connection.GetDatabase();
            cache.KeyDelete("teamsList");
            cache.KeyDelete("teamsSortedSet");
            ViewBag.msg += "Team data removed from cache. ";
        } 


4. Voeg de volgende vier methoden toe aan klasse `TeamsController` om de verschillende manieren voor het ophalen van teamstatistieken uit de cache en de database te implementeren. Alle methoden retourneren een `List<Team>`, die vervolgens wordt weergegeven in de weergave.

    De `GetFromDB`-methode leest de teamstatistieken uit de database.

        List<Team> GetFromDB()
        {
            ViewBag.msg += "Results read from DB. ";
            var results = from t in db.Teams
                orderby t.Wins descending
                select t; 
    
            return results.ToList<Team>();
        }


    De `GetFromList`-methode leest de teamstatistieken uit de cache als een geserialiseerde `List<Team>`. Als er een cache ontbreekt, worden de teamstatistieken gelezen uit de database en vervolgens voor later gebruik opgeslagen in de cache. In dit voorbeeld wordt gebruikgemaakt van JSON.NET-serialisatie om de .NET-objecten naar en uit de cache te serialiseren. Zie [Werken met .NET-objecten in Azure Redis-cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) voor meer informatie.

        List<Team> GetFromList()
        {
            List<Team> teams = null;

            IDatabase cache = Connection.GetDatabase();
            string serializedTeams = cache.StringGet("teamsList");
            if (!String.IsNullOrEmpty(serializedTeams))
            {
                teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

                ViewBag.msg += "List read from cache. ";
            }
            else
            {
                ViewBag.msg += "Teams list cache miss. ";
                // Get from database and store in cache
                teams = GetFromDB();

                ViewBag.msg += "Storing results to cache. ";
                cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
            }
            return teams;
        }


    De `GetFromSortedSet`-methode leest de teamstatistieken uit een in de cache opgeslagen gesorteerde set. Als er een cache ontbreekt, worden de teamstatistieken uit de database gelezen en als een gesorteerde set opgeslagen in de cache.


        List<Team> GetFromSortedSet()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();
            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
            if (teamsSortedSet.Count() > 0)
            {
                ViewBag.msg += "Reading sorted set from cache. ";
                teams = new List<Team>();
                foreach (var t in teamsSortedSet)
                {
                    Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                    teams.Add(tt);
                }
            }
            else
            {
                ViewBag.msg += "Teams sorted set cache miss. ";
    
                // Read from DB
                teams = GetFromDB();
    
                ViewBag.msg += "Storing results to cache. ";
                foreach (var t in teams)
                {
                    Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                    cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
                }
            }
            return teams;
        }


    De `GetFromSortedSetTop5`-methode leest de beste vijf teams uit de in de cache opgeslagen gesorteerde set. Deze methode begint met het controleren van de cache op de aanwezigheid van de `teamsSortedSet`-sleutel. Als deze sleutel niet aanwezig is, wordt de `GetFromSortedSet`-methode aangeroepen om de teamstatistieken te lezen en op te slaan in de cache. Vervolgens wordt de in de cache opgeslagen gesorteerde set gevraagd om de beste vijf teams op te halen. Deze worden daarna geretourneerd.


        List<Team> GetFromSortedSetTop5()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();

            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            if(teamsSortedSet.Count() == 0)
            {
                // Load the entire sorted set into the cache.
                GetFromSortedSet();

                // Retrieve the top 5 teams.
                teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            }

            ViewBag.msg += "Retrieving top 5 teams from cache. ";
            // Get the top 5 teams from the sorted set
            teams = new List<Team>();
            foreach (var team in teamsSortedSet)
            {
                teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
            }
            return teams;
        }


### De methoden Maken, Bewerken en Verwijderen bijwerken voor gebruik met de cache

De ondersteuningscode die als onderdeel van dit voorbeeld is gegenereerd, bevat de methoden om teams toe te voegen, te bewerken en te verwijderen. Telkens wanneer er een team wordt toegevoegd, bewerkt of verwijderd, raken de gegevens in de cache verouderd. In deze sectie bewerkt u deze drie methoden, zodat de in de cache opgeslagen teams worden gewist en de cache synchroon loopt met de database.

1. Blader naar de `Create(Team team)`-methode in de klasse `TeamsController`. Voeg een aanroep toe aan de `ClearCachedTeams`-methode, zoals wordt weergegeven in het volgende voorbeeld.


        // POST: Teams/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Teams.Add(team);
                db.SaveChanges();
                // When a team is added, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
    
            return View(team);
        }


2. Blader naar de `Edit(Team team)`-methode in de klasse `TeamsController`. Voeg een aanroep toe aan de `ClearCachedTeams`-methode, zoals wordt weergegeven in het volgende voorbeeld.


        // POST: Teams/Edit/5
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Entry(team).State = EntityState.Modified;
                db.SaveChanges();
                // When a team is edited, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
            return View(team);
        }


3. Blader naar de `DeleteConfirmed(int id)`-methode in de klasse `TeamsController`. Voeg een aanroep toe aan de `ClearCachedTeams`-methode, zoals wordt weergegeven in het volgende voorbeeld.


        // POST: Teams/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(int id)
        {
            Team team = db.Teams.Find(id);
            db.Teams.Remove(team);
            db.SaveChanges();
            // When a team is deleted, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }


### De weergave Teamindex bijwerken voor gebruik met de cache

1. Vouw in **Solution Explorer** de map **Views** uit. Vouw vervolgens de map **Teams** uit en dubbelklik op **Index.cshtml**.

    ![Index.cshtml][cache-views-teams-index-cshtml]

2. Zoek boven in het bestand naar het volgende alinea-element.

    ![Actietabel][cache-teams-index-table]

    Dit is de koppeling waarmee u een nieuw team kunt maken. Vervang het alinea-element door de volgende tabel. Deze tabel bevat actiekoppelingen waarmee u een nieuw team kunt maken, een nieuw seizoen wedstrijden kunt spelen, de cache kunt wissen, de teams in verschillende indelingen kunt ophalen uit de cache, de teams kunt ophalen uit de database en de database opnieuw kunt opbouwen met de nieuwe voorbeeldgegevens.


        <table class="table">
            <tr>
                <td>
                    @Html.ActionLink("Create New", "Create")
                </td>
                <td>
                    @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
                </td>
                <td>
                    @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
                </td>
                <td>
                    @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
                </td>
                <td>
                    @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
                </td>
                <td>
                    @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
                </td>
                <td>
                    @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
                </td>
                <td>
                    @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
                </td>
            </tr>    
        </table>


3. Ga naar de onderkant van het bestand **Index.cshtml** en voeg het volgende `tr`-element toe, zodat dit de laatste rij in de laatste tabel in het bestand is.

        <tr><td colspan="5">@ViewBag.Msg</td></tr>

    Deze rij geeft de waarde van `ViewBag.Msg` weer. Deze bevat een statusrapport over de huidige bewerking, die wordt ingesteld wanneer u op een van de actiekoppelingen uit de vorige stap klikt.   

    ![Statusbericht][cache-status-message]

4. Druk op **F6** om het project te bouwen.

## De Azure-resources inrichten

Als u uw toepassing in Azure wilt hosten, moet u eerst de Azure-services inrichten die vereist zijn voor uw toepassing. De voorbeeldtoepassing in deze zelfstudie maakt gebruik van de volgende Azure-services.

-   Azure Redis-cache
-   App Service-web-app
-   SQL Database

Als u deze services wilt implementeren in een nieuwe of bestaande resourcegroep naar keuze, klikt u op de knop **Deploy to Azure**.

[! [Deploy to Azure] [deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

De knop **Deploy to Azure** gebruikt de sjabloon voor de [Azure-snelstartgids](https://github.com/Azure/azure-quickstart-templates) [Een web-app maken plus Redis-cache plus SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) om deze services in te richten en de verbindingsreeks in te stellen voor de SQL Database, evenals de toepassingsinstelling voor de verbindingsreeks voor de Azure Redis-cache.

>[AZURE.NOTE] Als u geen Azure-account hebt, kunt u binnen een paar minuten [een gratis Azure-account maken](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero).

Als u op de knop **Deploy to Azure** klikt, wordt u naar de Azure Portal geleid en start het proces voor het maken van de resources die in de sjabloon worden beschreven.

![Implementeren in Azure][cache-deploy-to-azure-step-1]

1. Selecteer op de blade **Aangepaste implementatie** het Azure-abonnement dat u wilt gebruiken. Selecteer daarnaast een bestaande resourcegroep of maak een nieuwe, en geef de locatie van de resourcegroep op.
2. Geef op de blade **Parameters** de naam van een beheerdersaccount (**ADMINISTRATORLOGIN** - niet **beheerder** gebruiken), een beheerderswachtwoord voor aanmelding (**ADMINISTRATORLOGINPASSWORD**) en de databasenaam (**DATABASENAME**) op. De andere parameters zijn geconfigureerd voor een gratis App Service-hostingplan en voor opties met lagere kosten voor de SQL Database en de Azure Redis-cache (deze worden niet geleverd met een gratis laag).
3. Wijzig, indien gewenst, de andere instellingen, of behoud de standaardinstellingen en klik op **OK**.


![Implementeren in Azure][cache-deploy-to-azure-step-2]

1. Klik op **Juridische voorwaarden bekijken**.
2. Lees de voorwaarden op de blade **Aanschaffen** en klik op **Aanschaffen**.
3. Klik op de blade **Aangepaste implementatie** op **Maken** om te beginnen met het inrichten van de resources.

Als u de voortgang van uw implementatie wilt bekijken, klikt u op het meldingspictogram en vervolgens op **Implementatie gestart**.

![Implementatie gestart][cache-deployment-started]

U kunt de status van uw implementatie bekijken op de blade **Microsoft.Template**.

![Implementeren in Azure][cache-deploy-to-azure-step-3]

Wanneer het inrichten is voltooid, kunt u uw toepassing in Azure publiceren vanuit Visual Studio.

>[AZURE.NOTE] Eventuele fouten die zich tijdens het inrichtingsproces voordoen, worden weergegeven op de blade **Microsoft.Template**. Veelvoorkomende fouten zijn te veel SQL Servers of te veel gratis App Service-hostingplannen per abonnement. Los eventuele fouten op en start het proces opnieuw door te klikken op **Opnieuw implementeren** op de blade **Microsoft.Template** of op de knop **Implementeren in Azure** in deze zelfstudie.

## De toepassing publiceren in Azure

In deze stap van de zelfstudie publiceert u de toepassing in Azure en voert u deze uit in de cloud.

1. Klik in Visual Studio met de rechtermuisknop op het project **ContosoTeamStats** en kies **Publish**.

    ![Publiceren][cache-publish-app]

2. Klik op **Microsoft Azure App Service**.

    ![Publiceren][cache-publish-to-app-service]

3. Selecteer het abonnement dat u hebt gebruikt bij het maken van de Azure-resources, vouw de resourcegroep met de resources uit, selecteer de gewenste web-app en klik op **OK**. Als u de knop **Implementeren in Azure** hebt gebruikt, begint de naam van uw web-app met **webSite**, gevolgd door een aantal extra tekens.

    ![Web-app selecteren][cache-select-web-app]

4. Klik op **Validate Connection** om uw instellingen te controleren. Klik vervolgens op **Publish**.

    ![Publiceren][cache-publish]

    Na enkele ogenblikken wordt het publicatieproces voltooid. Er wordt een browservenster geopend waarin de voorbeeldtoepassing wordt uitgevoerd. Als er tijdens het valideren of publiceren een DNS-fout optreedt en het inrichtingsproces voor de Azure-resources voor de toepassing nog maar net is voltooid, wacht dan even en probeer het opnieuw.

    ![Cache toegevoegd][cache-added-to-application]

De volgende tabel beschrijft elke actiekoppeling in de voorbeeldtoepassing.

| Actie                  | Beschrijving                                                                                                                                                      |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Create New              | Een nieuw team maken                                                                                                                                               |
| Play Season             | Speel een seizoen wedstrijden, werk de teamstatistieken bij en wis eventuele verouderde teamgegevens uit de cache.                                                                          |
| Clear Cache             | Wis de teamstatistieken uit de cache.                                                                                                                             |
| List from Cache         | Haal de teamstatistieken op uit de cache. Als er een cache ontbreekt, moet u de statistieken uit de database laden en in de cache opslaan voor later gebruik.                                        |
| Sorted Set from Cache   | Haal de teamstatistieken op uit de cache met behulp van een gesorteerde set. Als er een cache ontbreekt, moet u de statistieken uit de database laden en met behulp van een gesorteerde set opslaan in de cache.  |
| Top 5 Teams from Cache  | Haal de beste vijf teams op uit de cache met behulp van een gesorteerde set. Als er een cache ontbreekt, moet u de statistieken uit de database laden en met behulp van een gesorteerde set opslaan in de cache. |
| Load from DB            | Haal de teamstatistieken op uit de database.                                                                                                                       |
| Rebuild DB              | Bouw de database opnieuw en laad deze opnieuw met voorbeeldgegevens van de teams.                                                                                                        |
| Edit/Details/Delete | Bewerk een team, geef details van een team weer en verwijder een team.                                                                                                             |


Klik op een aantal acties en experimenteer met het ophalen van de gegevens vanuit de verschillende bronnen. Let op de verschillen in de tijd die nodig is om de gegevens op de diverse manieren op te halen uit de database en de cache.

## De resources verwijderen wanneer u klaar bent met de toepassing

Wanneer u klaar bent met de voorbeeldtoepassing uit de zelfstudie, kunt u de Azure-resources die u hebt gebruikt, verwijderen om kosten en resources te besparen. Als u de knop **Implementeren in Azure** in de sectie [De Azure-resources inrichten](#provision-the-azure-resources) hebt gebruikt en alle resources zich in dezelfde resourcegroep bevinden, kunt u deze in één keer verwijderen door de resourcegroep te verwijderen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en klik op **Resourcegroepen**.
2. Typ de naam van de resourcegroep in het tekstvak **Items filteren...**.
3. Klik op **...** aan de rechterkant van de resourcegroep.
4. Klik op **Verwijderen**.

    ![Verwijderen][cache-delete-resource-group]

5. Typ de naam van de resourcegroep en klik op **Verwijderen**.

    ![De verwijdering bevestigen][cache-delete-confirm]

Na enkele ogenblikken worden de resourcegroep en alle ingesloten bronnen verwijderd.

>[AZURE.IMPORTANT] Houd er rekening mee dat het verwijderen van een resourcegroep niet ongedaan kan worden gemaakt, en dat de resourcegroep en alle bijbehorende resources permanent worden verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources voor het hosten van dit voorbeeld in een bestaande resourcegroep hebt gemaakt, kunt u elke resource afzonderlijk verwijderen via hun respectievelijke blades.

## De voorbeeldtoepassing uitvoeren op uw lokale computer

Als u de toepassing lokaal wilt uitvoeren op uw computer, hebt u een exemplaar van Azure Redis-cache nodig waarin u de gegevens kunt opslaan. 

-   Als u uw toepassing in Azure hebt gepubliceerd, zoals beschreven in het vorige gedeelte, kunt u het exemplaar van Azure Redis-cache gebruiken dat tijdens die stap is ingericht.
-   Als u een ander bestaand exemplaar van Azure Redis-cache hebt, kunt u dit gebruiken om het voorbeeld lokaal uit te voeren.
-   Als u een exemplaar van Azure Redis-cache moet maken, kunt u de stappen volgen in [Een cache maken](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Nadat u de cache die u wilt gebruiken, hebt geselecteerd of gemaakt, kunt u naar de cache in de Azure Portal bladeren en de [hostnaam](cache-configure.md#properties) en de [toegangssleutels](cache-configure.md#access-keys) voor uw cache ophalen. Zie [Redis-cache-instellingen configureren](cache-configure.md#configure-redis-cache-settings) voor instructies.

1. Open het bestand `WebAppPlusCacheAppSecrets.config` dat u hebt gemaakt tijdens de stap [De toepassing configureren voor het gebruik van Redis-cache](#configure-the-application-to-use-redis-cache) in deze zelfstudie. Gebruik hiervoor een editor naar keuze.

2. Bewerk het kenmerk `value` en vervang `MyCache.redis.cache.windows.net` door de [hostnaam](cache-configure.md#properties) van de cache. Geef vervolgens de [primaire of secundaire sleutel](cache-configure.md#access-keys) van uw cache op als wachtwoord.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


3. Druk op **Ctrl+F5** om de toepassing uit te voeren.

>[AZURE.NOTE] Houd er rekening mee dat de toepassing en de database lokaal worden uitgevoerd en dat de Redis-cache wordt gehost in Azure. Hierdoor kan het lijken alsof de cache minder goed presteert dan de database. Voor optimale prestaties moeten de clienttoepassing en het exemplaar van Azure Redis-cache zich op dezelfde locatie bevinden. 

## Volgende stappen

-   Meer informatie over [hoe u aan de slag gaat met ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) vindt u op de website van [ASP.NET](http://asp.net/).
-   Meer informatie over de Entity Framework-werkwijze [Code First voor een nieuwe database](https://msdn.microsoft.com/data/jj193542) die in deze zelfstudie wordt gebruikt.
-   Meer informatie over [web-apps in Azure App Service](../app-service-web/app-service-web-overview.md).
-   Meer informatie over het [controleren](cache-how-to-monitor.md) van uw cache in de Azure Portal.

-   De premiumfuncties van de Azure Redis-cache verkennen
    -   [Persistentie configureren voor een Premium Azure Redis-cache](cache-how-to-premium-persistence.md)
    -   [Clustering configureren voor een Premium Azure Redis-cache](cache-how-to-premium-clustering.md)
    -   [Virtual Network-ondersteuning configureren voor een Premium Azure Redis-cache](cache-how-to-premium-vnet.md)
    -   Raadpleeg [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) (Veelgestelde vragen over Azure Redis-caches) voor meer informatie over de grootte, doorvoer en bandbreedte van premiumcaches.


<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png




<!--HONumber=Jun16_HO2-->


