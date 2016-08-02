<properties 
    pageTitle="ASP.NET MVC-zelfstudie voor DocumentDB: webtoepassingsontwikkeling | Microsoft Azure" 
    description="Zelfstudie voor ASP.NET MVC om een MVC-webtoepassing met DocumentDB te maken. JSON opslaan en gegevens benaderen via een takenlijst-app die wordt gehost op Azure Websites - Stapsgewijze zelfstudie voor ASP NET MVC." 
    keywords="asp.net mvc tutorial, web application development, mvc web application, asp net mvc tutorial step by step"
    services="documentdb" 
    documentationCenter=".net" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="cgronlun"/>


<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="05/18/2016" 
    ms.author="andrl"/>

#<a name="_Toc395809351"></a>ASP.NET MVC-zelfstudie: webtoepassingsontwikkeling met DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md) 

Dit artikel biedt een end-to-end-overzicht waarin wordt getoond hoe u met Azure DocumentDB een to-do-app kunt maken. Zo ziet u hoe u effectief kunt gebruikmaken van Azure DocumentDB voor het opslaan van en uitvoeren van query’s op JSON-documenten. De taken worden opgeslagen als JSON-documenten in Azure DocumentDB.

![Schermopname van de takenlijst MVC-webtoepassing die is gemaakt met deze zelfstudie - Stapsgewijze zelfstudie voor ASP NET MVC.](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image1.png)

In dit overzicht kunt u zien hoe u de DocumentDB-service van Azure kunt gebruiken om gegevens op te slaan en te openen uit een ASP.NET MVC-webtoepassing die wordt gehost op Azure. Zie [Een DocumentDB C#-consoletoepassing bouwen](documentdb-get-started.md) als u een zelfstudie zoekt die volledig is gericht op DocumentDB en niet op de ASP.NET MVC-onderdelen.

> [AZURE.TIP] Voor deze zelfstudie wordt ervan uitgegaan dat u ervaring hebt met ASP.NET MVC en Azure Websites. Als u niet bekend met ASP.NET of de [vereiste hulpprogramma's](#_Toc395637760) bent, is het raadzaam het volledige voorbeeldproject via [GitHub][] te downloaden en de instructies in dit voorbeeld te volgen. Zodra u klaar bent, kunt u dit artikel lezen voor meer informatie over de code in de context van het project.

## <a name="_Toc395637760"></a>Vereisten voor deze databasezelfstudie

Voordat u de instructies in dit artikel uitvoert, moet u beschikken over het volgende:

- Een actief Azure-account. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
- [Visual Studio 2013](http://www.visualstudio.com/) update 4 of hoger.
- Azure SDK voor .NET versie 2.5.1 of hoger, beschikbaar via het [webplatforminstallatieprogramma van Microsoft][].

Alle schermopnamen in dit artikel zijn gemaakt in Visual Studio 2013 met update 4 en de Azure SDK voor .NET versie 2.5.1. Als uw systeem is geconfigureerd met verschillende versies, is het mogelijk dat de schermen en opties niet volledig overeenkomen, maar als u aan de bovenstaande vereisten voldoet, moet deze oplossing werken.

## <a name="_Toc395637761"></a>Stap 1: een DocumentDB-databaseaccount maken

Begin met het maken van een DocumentDB-account. Als u al een account hebt, kunt u doorgaan met [Een nieuwe ASP.NET MVC-toepassing maken](#_Toc395637762).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

<br/>
U kunt nu zien hoe u een compleet nieuwe ASP.NET MVC-toepassing maakt. 

## <a name="_Toc395637762"></a>Stap 2: een nieuwe ASP.NET MVC-toepassing maken

Nu u een account hebt, kunt u een nieuw ASP.NET-project maken.

1. Wijs in het menu **Bestand** van Visual Studio de optie **Nieuw** aan en klik vervolgens op **Project**.

    Het dialoogvenster **Nieuw project** wordt weergegeven.
2. Vouw in het deelvenster **Projecttypen** achtereenvolgens **Sjablonen**, **Visual C#** en **Web** uit en selecteer vervolgens**ASP.NET-webtoepassing**.

    ![Schermopname van het dialoogvenster met daarin het projecttype ASP.NET-webtoepassing gemarkeerd](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image10.png)

3. Typ in het vak **Naam** de naam van het project. In deze zelfstudie wordt de naam 'todo' gebruikt. Als u een andere naam gebruikt, moet u waar in deze zelfstudie over de naamruimte todo wordt gesproken, de codevoorbeelden aanpassen met de naam die u voor uw toepassing gebruikt. 

4. Klik op **Bladeren** om naar de map te navigeren waarin u het project wilt maken en klik vervolgens op **OK**.

    Het dialoogvenster **New ASP.NET Project** (Nieuw ASP.NET-project) wordt weergegeven.

    ![Schermopname van het dialoogvenster Nieuw ASP.NET-project met de MVC-toepassingssjabloon gemarkeerd en het selectievakje Host in the cloud (Hosten in de cloud) ingeschakeld](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image11.png)

5. Selecteer in het deelvenster met sjablonen **MVC**.

6. Als u van plan bent de toepassing in Azure te hosten, selecteert u in de rechterbenedenhoek **Host in de cloud** (Hosten in de cloud) om de toepassing te hosten in Azure. Er is voor gekozen om in de cloud te hosten en de toepassing uit te voeren door deze in een Azure-website te hosten. Als deze optie selecteert, wordt er vooraf een Azure-website voor u ingericht en kunt u de uiteindelijke werkende toepassing veel eenvoudiger implementeren. Als u de toepassing ergens anders wilt hosten of Azure niet vooraf wilt configureren, schakelt u het selectievakje **Host in the Cloud** (Hosten in de cloud) uit.

7. Klik op **OK** om de scaffolding van de lege ASP.NET MVC-sjabloon aan Visual Studio over te laten. 

8. Als u ervoor hebt gekozen om de toepassing in de cloud te hosten, wordt er ten minste één extra scherm weergegeven waarin u wordt gevraagd u aan te melden bij uw Azure-account en enkele waarden voor uw nieuwe website op te geven. Geef alle aanvullende waarden op en ga door. 

    Ik heb hier geen databaseserver gekozen, omdat er geen Azure SQL Database-server wordt gebruikt. We zullen later een nieuw Azure DocumentDB-account in Azure Portal maken.

    Zie [Uitgebreid overzicht van Azure App Service-plannen](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) voor meer informatie over het kiezen van een **App Service-plan** en **resourcegroep**.

    ![Schermopname van het dialoogvenster Configure Microsoft Azure Website (Microsoft Azure-website configureren)](./media/documentdb-dotnet-application/image11_1.png)

9. Zodra de Visual Studio de standaard MVC-toepassing heeft gemaakt, beschikt u over een lege ASP.NET-toepassing die u lokaal kunt uitvoeren.

    We zullen het project niet lokaal uitvoeren, aangezien iedereen waarschijnlijk wel bekend is met de ASP.NET-toepassing Hello World. Laten we meteen DocumentDB aan dit project toevoegen en onze toepassing bouwen.

## <a name="_Toc395637767"></a>Stap 3: DocumentDB aan uw project met de MVC-toepassing toevoegen

Nu de meeste ASP.NET MVC-werkzaamheden voor deze oplossing zijn voltooid, kunnen we ons richten op het werkelijke doel van deze zelfstudie, namelijk het toevoegen van Azure DocumentDB aan de MVC-webtoepassing.

1. De .NET DocumentDB SDK wordt verpakt en gedistribueerd als een NuGet-pakket. Als u het NuGet-pakket aan Visual Studio wilt toevoegen, gebruikt u NuGet-pakketbeheer in Visual Studio door in **Solution Explorer** met de rechtermuisknop op het project te klikken en vervolgens op **NuGet-pakketten beheren** te klikken.

    ![Schermopname van de opties voor klikken met de rechtermuisknop voor het webtoepassingsproject in Solution Explorer, met NuGet-pakketten beheren gemarkeerd.](./media/documentdb-dotnet-application/image21.png)

    Het dialoogvenster **NuGet-pakketten beheren** wordt weergegeven.

2. Typ in het NuGet-vak **Bladeren** ***Azure DocumentDB***.
    
    Installeer vanuit de resultaten het pakket met de **Microsoft Azure DocumentDB-clientbibliotheek**. Zodoende worden het DocumentDB-pakket en alle afhankelijkheden, zoals Newtonsoft.Json, gedownload en geïnstalleerd. Klik op **OK** in het venster **Voorbeeld** en op **I Accept** (Ik ga akkoord) in het venster **License Acceptance** (Licentie accepteren) om de installatie te voltooien.

    ![Schermopname van het venster NuGet-pakketten beheren met de Microsoft Azure DocumentDB-clientbibliotheek gemarkeerd](./media/documentdb-dotnet-application/nuget.png)

    U kunt eventueel ook de console voor Pakketbeheer gebruiken om het pakket te installeren. Hiervoor klikt u in het menu **Extra** op **NuGet Package Manager** (NuGet-pakketbeheer) en vervolgens op **Package Manager Console** (Pakketbeheer-console). Typ achter de prompt het volgende.

        Install-Package Microsoft.Azure.DocumentDB

3. Nadat het pakket is geïnstalleerd, moet uw Visual Studio-oplossing er ongeveer als volgt uitzien, met de twee nieuwe verwijzingen Microsoft.Azure.Documents.Client en Newtonsoft.Json.

    ![Schermopname van de twee verwijzingen die zijn toegevoegd aan het JSON-gegevensproject in Solution Explorer](./media/documentdb-dotnet-application/image22.png)


##<a name="_Toc395637763"></a>Stap 4: de ASP.NET MVC-toepassing instellen
 
U kunt nu de modellen, weergaven en controllers toevoegen aan deze MVC-toepassing:

- [Een model toevoegen](#_Toc395637764).
- [Een controller toevoegen](#_Toc395637765).
- [Weergaven toevoegen](#_Toc395637766).


### <a name="_Toc395637764"></a>Een JSON-gegevensmodel toevoegen

Als eerste wordt de **M** in MVC gemaakt, het model. 

1. Klik in **Solution Explorer** met de rechtermuisknop op de map **Modellen** en klik achtereenvolgens op **Toevoegen** en **Klasse**.

    Het dialoogvenster **Nieuw item toevoegen** wordt weergegeven.

2. Geef een naam op voor uw nieuwe klasse **Item.cs** en klik op **Toevoegen**. 

3. Voeg in het nieuwe bestand **Item.cs** achter de laatste *gebruiksinstructie* het volgende toe.
        
        using Newtonsoft.Json;
    
4. Vervang deze code nu 
        
        public class Item
        {
        }

    door de volgende code.

        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
             
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }

            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }

            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }

    Alle gegevens in DocumentDB worden doorgegeven via de kabel en opgeslagen als JSON. U kunt het kenmerk **JsonProperty** gebruiken, zoals wordt beschreven in de klasse **Item** die we zojuist hebben gemaakt, om te bepalen hoe uw objecten door JSON.NET worden geserialiseerd/gedeserialiseerd. U **hoeft** dit niet te doen, maar ik wil ervoor zorgen dat mijn eigenschappen de naamgevingsconventie van JSON camelCase volgen. 
    
    U kunt niet alleen de indeling van de eigenschapsnaam voor JSON bepalen, maar ook de naam van uw .NET-eigenschappen volledig wijzigen, zoals ik deed met de **Description**. 
    

### <a name="_Toc395637765"></a>Een controller toevoegen

Nu we de **M** hebben gehad, kunnen we de **C** in MVC, een controllerklasse, maken.

1. Klik in **Solution Explorer** met de rechtermuisknop op de map **Controllers** en klik achtereenvolgens op **Toevoegen** en **Controller**.

    Het dialoogvenster **Add Scaffold** (Scaffold toevoegen) wordt weergegeven.

2. Selecteer **MVC 5 Controller - Empty** (MVC 5-controller - Leeg) en klik vervolgens op **Toevoegen**.

    ![Schermopname van het dialoogvenster Add Scaffold met de optie MVC 5 Controller - Empty gemarkeerd](./media/documentdb-dotnet-application/image14.png)

3. Noem uw nieuwe controller **ItemController.**.

    ![Schermopname van het dialoogvenster Add Controller (Controller toevoegen)](./media/documentdb-dotnet-application/image15.png)

    Zodra het bestand is gemaakt, wordt het nieuwe bestand ItemController.cs in **Solution Explorer** weergegeven en ziet uw Visual Studio-oplossing er ongeveer als volgt uit. Het nieuwe bestand Item.cs file, dat eerder is gemaakt, wordt ook weergegeven.

    ![Schermopname van de Visual Studio-oplossing - Solution Explorer met het nieuwe bestand ItemController.cs gemarkeerd](./media/documentdb-dotnet-application/image16.png)

    U kunt ItemController.cs sluiten. Hier komen we later op terug. 

### <a name="_Toc395637766"></a>Weergaven toevoegen

Laten we nu de **V**, de weergaven, in MVC maken:

- [Een weergave toevoegen voor een itemindex ](#AddItemIndexView).
- [Een weergave toevoegen voor nieuwe items](#AddNewIndexView).
- [Een weergave toevoegen voor het bewerken van items](#_Toc395888515).


#### <a name="AddItemIndexView"></a>Een weergave voor een itemindex toevoegen

1. Vouw in **Solution Explorer** de map **Weergaven** uit en klik met de rechtermuisknop op de lege map **Item** die Visual Studio voor u heeft gemaakt toen u **ItemController** hebt toegevoegd. Klik vervolgens op **Toevoegen** en **Weergave**.

    ![Schermopname van Solution Explorer waarin de map Item wordt weergegeven die Visual Studio heeft gemaakt en waarin de opdrachten voor het toevoegen van een weergave zijn gemarkeerd](./media/documentdb-dotnet-application/image17.png)

2. Voer in het dialoogvenster **Weergave toevoegen** de volgende handelingen uit:
    - In het vak **Weergavenaam** typt u ***Index***.
    - Selecteer in het vak **Sjabloon** de optie ***Lijst***.
    - Selecteer in het vak **Modelklasse** de optie ***Item (todo.Models)***.
    - Laat het vak **Data context class** (Gegevenscontextklasse) leeg. 
    - Typ in het veld voor de indelingspagina ***~/Views/Shared/_Layout.cshtml***.
    
    ![Schermopname van het dialoogvenster Weergave toevoegen](./media/documentdb-dotnet-application/image18.png)

3. Zodra al deze waarden zijn ingesteld, klikt u op **Toevoegen** en wordt er een nieuwe sjabloonweergave in Visual Studio gemaakt. Vervolgens wordt het cshtml-bestand geopend dat is gemaakt. Dit bestand in Visual Studio kan voorlopig worden gesloten, aangezien dit pas later aan bod komt.

#### <a name="AddNewIndexView"></a>Een weergave toevoegen voor nieuwe items

We kunnen op ongeveer dezelfde manier als voor de weergave **Itemindex** nu een nieuwe weergave voor het maken van nieuwe **Items** maken.

1. Klik in **Solution Explorer** met de rechtermuisknop nogmaals op de map **Item** en klik achtereenvolgens op **Toevoegen** en **Weergave**.

2. Voer in het dialoogvenster **Weergave toevoegen** de volgende handelingen uit:
    - Typ in het vak **Weergavenaam** ***Maken***.
    - Selecteer in het vak **Sjabloon** de optie ***Maken***.
    - Selecteer in het vak **Modelklasse** de optie ***Item (todo.Models)***.
    - Laat het vak **Data context class** (Gegevenscontextklasse) leeg.
    - Typ in het veld voor de indelingspagina ***~/Views/Shared/_Layout.cshtml***.
    - Klik op **Toevoegen**.

#### <a name="_Toc395888515"></a>Een weergave toevoegen voor het bewerken van items

Tot slot voegt u op dezelfde manier als hiervoor een weergave toe waarin u **items** kunt bewerken.

1. Klik in **Solution Explorer** met de rechtermuisknop nogmaals op de map **Item** en klik achtereenvolgens op **Toevoegen** en **Weergave**.

2. Voer in het dialoogvenster **Weergave toevoegen** de volgende handelingen uit:
    - Typ in het vak **Weergavenaam** ***Bewerken***.
    - Selecteer in het vak **Sjabloon** de optie ***Bewerken***.
    - Selecteer in het vak **Modelklasse** de optie ***Item (todo.Models)***.
    - Laat het vak **Data context class** (Gegevenscontextklasse) leeg. 
    - Typ in het veld voor de indelingspagina ***~/Views/Shared/_Layout.cshtml***.
    - Klik op **Toevoegen**.

Zodra dit is gebeurd, sluit u alle cshtml-documenten in Visual Studio. We komen later op deze weergaven terug.

## <a name="_Toc395637769"></a>Stap 5: DocumentDB voorbereiden

Nu we de standaardwerkzaamheden voor MVC hebben voltooid, kunnen we de code voor DocumentDB toevoegen. 

In deze sectie voegen we code toe voor de verwerking van het volgende:

- [Vermelden van onvolledige items](#_Toc395637770).
- [Items toevoegen](#_Toc395637771).
- [Items bewerken](#_Toc395637772).

### <a name="_Toc395637770"></a>Onvolledige objecten in uw MVC-webtoepassing vermelden

Allereerst moet u een klasse toevoegen die de logica bevat voor de verbinding met en het gebruik van DocumentDB. Voor deze zelfstudie voegen we alle logica toe aan een opslagplaatsklasse met de naam DocumentDBRepository. 

1. Klik in **Solution Explorer** met de rechtermuisknop op het project, klik op **Toevoegen** en klik vervolgens op **Klasse**. Geef een naam voor de nieuwe klasse **DocumentDBRepository** op en klik op **Toevoegen**.
 
2. Voeg de volgende *gebruiksinstructies* boven de *naamruimtedeclaratie* toe in de klasse **DocumentDBRepository** die we zojuist hebben gemaakt.
        
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;

    Vervang deze code nu 

        public class DocumentDBRepository
        {
        }

    door de volgende code.

        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
    
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
    
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
    
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }

    > [AZURE.TIP] Wanneer u een nieuwe DocumentCollection maakt, kunt u een optionele parameter RequestOptions van OfferType opgeven, zodat u het prestatieniveau van de nieuwe verzameling kunt opgeven. Als deze parameter niet wordt doorgegeven, wordt het standaardaanbiedingstype gebruikt. Zie [DocumentDB-prestatieniveaus](documentdb-performance-levels.md) voor meer informatie over DocumentDB-aanbiedingstypen.

3. Aangezien er enkele waarden uit de configuratie worden gelezen, opent u het bestand **Web.config** van de toepassing en voegt u de volgende regels onder de sectie `<AppSettings>` toe.
    
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
    
4. Werk nu de waarden voor *endpoint* en *authKey* bij door gebruik te maken van de blade Sleutels van Azure Portal. Gebruik de **URI** op de blade Sleutels als waarde voor de endpoint-instelling en gebruik de **PRIMAIRE SLEUTEL** of **SECUNDAIRE SLEUTEL** op de blade Sleutels als waarde voor authKey-instelling.


    Hiermee is de DocumentDB-opslagplaats gereed en kunnen we de toepassingslogica toevoegen.

5. Op de eerste plaats willen we natuurlijk de onvolledige items kunnen weergeven met een takenlijsttoepassing.  Kopieer het volgende codefragment en plak dit ergens in de klasse **DocumentDBRepository**.

        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();

            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }

            return results;
        }


6. Open de **ItemController** die eerder is toegevoegd en voeg de volgende *Using-instructies* toe boven de naamruimtedeclaratie.

        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;

    Als u het project een andere naam dan 'todo' hebt gegeven, moet u 'todo.Models' bijwerken om hier de naam van uw project weer te geven.

    Vervang deze code nu

        //GET: Item
        public ActionResult Index()
        {
            return View();
        }

    door de volgende code.

        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
    
7. Open **Global.asax.cs** en voeg de volgende regel aan de methode **Application_Start** toe. 
 
        DocumentDBRepository<todo.Models.Item>.Initialize();
    
Op dit moment moet uw oplossing opbouwbewerking kunnen uitvoeren zonder dat er fouten optreden.

Als u de toepassing nu hebt uitgevoerd, gaat u naar de weergaven **HomeController** en **Index** van die controller. Dit is het standaardgedrag voor het MVC-sjabloonproject dat we aan het begin hebben gekozen, maar dit is niet het gewenste gedrag. U kunt de routering op deze MVC-toepassing wijzigen om dit gedrag te veranderen.

Open ***App\_Start\RouteConfig.cs*** en zoek de regel die begint met "defaults:" en pas deze als volgt aan.

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Hiermee stelt u in dat ASP.NET MVC niet **Home** maar **Item** als controller gebruikt en **Index** als weergave gebruikt als u in de URL geen waarde hebt opgegeven voor het routeringsgedrag.

Als u de toepassing nu uitvoert, wordt uw **ItemController** aangeroepen, die vervolgens de opslagplaatsklasse aanroept en de methode GetItems gebruikt om alle onvolledige items naar de weergave **Weergaven**\\**Item**\\**Index** te retourneren. 

Als u dit project nu maakt en uitvoert, ziet u iets dat vergelijkbaar is met het volgende.    

![Schermopname van de takenlijstwebtoepassing die is gemaakt met deze databasezelfstudie](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>Items toevoegen

Laten we enkele items toevoegen aan de database zodat we niet tegen een leeg raster aankijken.

U kunt nu code toevoegen aan DocumentDBRepository en ItemController om de record in DocumentDB te houden.

1.  Voeg de volgende methode toe aan uw klasse **DocumentDBRepository**.

        public static async Task<Document> CreateItemAsync(T item)
        {
            return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
        }

    Met deze methode wordt een doorgegeven object gebruikt in DocumentDB.

2. Open het bestand ItemController.cs en voeg het volgende codefragment toe binnen de klasse. Zodoende weet ASP.NET MVC wat er voor de actie **Create** moet worden gedaan. In dit geval geeft u de bijbehorende weergave Create.cshtml weer die eerder is gemaakt.

        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }

    Er is nu meer code in deze controller nodig die de inzending vanuit de weergave **Create** accepteert.

2. Voeg het volgende codeblok toe aan de klasse ItemController.cs waarmee ASP.NET MVC wordt geïnstrueerd wat er moet gebeuren met een form POST voor deze controller.
    
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }

            return View(item);
        }

    Deze code roept de DocumentDBRepository aan en gebruikt de methode CreateItemAsync om het nieuwe takenlijstitem door te geven aan de database. 
 
    **Opmerking over de beveiliging**: het kenmerk **ValidateAntiForgeryToken** wordt hier gebruikt om deze toepassing te beschermen tegen aanvallen via aanvraagvervalsing op meerdere sites. Het volstaat echter niet om dit kenmerk alleen toe te voegen. Uw weergaven moeten samenwerken met dit anti-vervalsingstoken. Zie [Voorkomen van aanvraagvervalsing op meerdere sites][] voor meer informatie over dit onderwerp en voorbeelden van een juiste implementatie. De broncode op [GitHub][] beschikt over de volledige implementatie.

    **Opmerking over de beveiliging**: we gebruiken ook het kenmerk **Bind** voor de methodeparameter om u te beschermen tegen over-postingaanvallen. Zie [Eenvoudige CRUD-bewerkingen in ASP.NET MVC][] voor meer informatie.

Hiermee is de benodigde code toegevoegd om nieuwe items aan de database toe te voegen.


### <a name="_Toc395637772"></a>Items bewerken

Tot slot moeten we ervoor zorgen dat we **Items** in de database kunnen bewerken en dat we ze kunnen markeren als voltooid. De weergave voor het bewerken van items was al toegevoegd aan het project. U hoeft daarom alleen code toe te voegen aan de controller en de klasse **DocumentDBRepository**.

1. Voeg het volgende toe aan de klasse **DocumentDBRepository**.

        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }

        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
    
    Met de eerste methode, **GetItem** wordt een item uit DocumentDB opgehaald die wordt doorgegeven aan de **ItemController** en vervolgens aan de weergave **Bewerken**.
    
    Met de tweede methode die zojuist is toegevoegd, wordt het **document** in DocumentDB vervangen door de versie van het **document** dat is doorgegeven via de **ItemController**.

2. Voeg het volgende toe aan de klasse **ItemController**.

        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }

            return View(item);
        }

        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }

            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }

            return View(item);
        }
    
    De eerste methode verwerkt de Http GET die plaatsvindt wanneer de gebruiker klikt op de koppeling **Bewerken** in de weergave **Index**. Met deze methode wordt er een [**document**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) uit DocumentDB opgehaald en doorgegeven aan de weergave **Bewerken**.

    In de weergave **Bewerken** wordt vervolgens een Http POST naar de **IndexController** uitgevoerd. 
    
    Voor de tweede methode zijn er ingangen toegevoegd om het object door te geven aan DocumentDB, zodat het object wordt bewaard in de database.

Meer hoeft u niet te doen om uw toepassing uit te voeren, onvolledige **Items** weer te geven, nieuwe **Items** toe te voegen en **Items** te bewerken.

## <a name="_Toc395637773"></a>Stap 6: de toepassing lokaal uitvoeren

Ga als volgt te werk als u de toepassing wilt testen op een lokale machine:

1. Druk in Visual Studio op F5 om de toepassing in de foutopsporingsmodus op te bouwen. De toepassing wordt opgebouwd en wordt er een browser gestart met het lege rasterpagina dat we eerder hebben gezien:

    ![Schermopname van de takenlijstwebtoepassing die is gemaakt met deze databasezelfstudie](./media/documentdb-dotnet-application/image24.png)

    Als er op dit moment fouten worden aangetroffen, vergelijkt u de code met het voorbeeldproject op [GitHub][].

2. Klik op de koppeling **Nieuw maken** en voeg waarden toe aan de velden **Naam** en **Beschrijving**. Schakel het selectievakje **Voltooid** niet in, anders wordt het nieuwe **Item** toegevoegd met een onvoltooide status en wordt het niet weergegeven in de aanvankelijke lijst.

    ![Schermopname van de weergave Maken](./media/documentdb-dotnet-application/image25.png)

3. Als u op **Maken** klikt, keert u terug naar de weergaven **Index** en wordt uw **Item** weergegeven in de lijst.

    ![Schermopname van de weergave Index](./media/documentdb-dotnet-application/image26.png)

    U kunt gerust nog enkele **Items** aan uw takenlijst toevoegen.

3. Klik op **Bewerken** naast een **Item** in de lijst, zodat u wordt omgeleid naar de weergave **Bewerken**. Hier kunt u de eigenschappen van uw object bijwerken, inclusief de vlag **Voltooid**. Als u de vlag **Voltooid** markeert en op **Opslaan** klikt, wordt het **Item** verwijderd uit de lijst met onvolledige taken.

    ![Schermopname van de weergave Index met het selectievakje Voltooid ingeschakeld](./media/documentdb-dotnet-application/image27.png)

4. Zodra u de app hebt getest, drukt u op Ctrl + F5 om de foutopsporing voor de app te stoppen. U kunt de app nu implementeren.

##<a name="_Toc395637774"></a>Stap 7: de toepassing implementeren naar Azure Websites

Zodra de volledige toepassing correct werkt met DocumentDB, kunt u de web-app implementeren naar Azure Websites. Als u **Host in the cloud** (In de cloud hosten) hebt geselecteerd toen u het lege ASP.NET MVC-project hebt gemaakt, kunt u de web-app vrij eenvoudig met Visual Studio implementeren en doet Visual Studio het meeste werk voor u. 

1. Als u deze toepassing wilt publiceren, hoeft u alleen maar met de rechtermuisknop op het project in **Solution Explorer** te klikken en vervolgens op **Publiceren** te klikken.

    ![Schermopname van de optie Publiceren in Solution Explorer](./media/documentdb-dotnet-application/image28.png)

2. Alles moet al zijn geconfigureerd op basis van uw referenties. De website in Azure is al gemaakt op de **doel-URL** die wordt weergegeven. U hoeft alleen nog maar op **Publiceren** te klikken.

    ![Schermopname van het dialoogvenster Publish Web (Publiceren naar het web) in Visual Studio - Stapsgewijze zelfstudie voor ASP NET MVC](./media/documentdb-dotnet-application/image29.png)

Over een paar seconden zal Visual Studio de publicatie van uw webtoepassing voltooien en een browser starten waarin u kunt zien hoe uw werk in Azure wordt uitgevoerd.

##<a name="_Toc395637775"></a>Volgende stappen

Gefeliciteerd. U hebt zojuist uw eerste ASP.NET MVC-webtoepassing gebouwd met Azure DocumentDB en deze gepubliceerd naar Azure Websites. De broncode voor de volledige toepassing, met inbegrip van de functionaliteit voor details en verwijderen die niet zijn opgenomen in deze zelfstudie, kan worden gedownload of gekloond via [GitHub][]. Als dit wilt toevoegen aan uw app, kunt u de code ophalen en toevoegen aan deze app.

Als u de functionaliteit van uw toepassing wilt uitbreiden, bekijkt u de beschikbare API's in de [Document DB .NET-bibliotheek](https://msdn.microsoft.com/library/azure/dn948556.aspx). U bent tevens van harte welkom om een bijdrage te leveren aan de DocumentDB .NET-bibliotheek op [GitHub][]. 


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[webplatforminstallatieprogramma van Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
[Voorkomen van aanvraagvervalsing op meerdere sites]: http://go.microsoft.com/fwlink/?LinkID=517254
[Eenvoudige CRUD-bewerkingen in ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app



<!--HONumber=Jun16_HO2-->


