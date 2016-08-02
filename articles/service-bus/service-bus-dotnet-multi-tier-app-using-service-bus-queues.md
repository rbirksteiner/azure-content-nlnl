<properties
    pageTitle=".NET-toepassing met meerdere lagen | Microsoft Azure"
    description="Een .NET-zelfstudie waarmee u in Azure een app met meerdere lagen kunt ontwikkelen die Service Bus-wachtrijen gebruikt voor communicatie tussen lagen."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/27/2016"
    ms.author="sethm"/>

# .NET-toepassing met meerdere lagen die Azure Service Bus-wachtrijen gebruikt

## Inleiding

Ontwikkelen voor Microsoft Azure is eenvoudig met Visual Studio en de gratis Azure SDK voor .NET. In deze zelfstudie doorloopt u de stappen voor het maken van een toepassing die meerdere Azure-resources in uw lokale omgeving gebruikt. Bij deze stappen wordt ervan uitgegaan dat u nog geen ervaring hebt met Azure.

U leert het volgende:

-   De computer met een enkele download en installatie instellen voor het ontwikkelen voor Azure.
-   Visual Studio gebruiken om te ontwikkelen voor Azure.
-   Een toepassing met meerdere lagen maken in Azure met de web- en werkrollen.
-   Communiceren tussen lagen met Service Bus-wachtrijen.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

In deze zelfstudie zult u de toepassing met meerdere lagen ontwikkelen en uitvoeren in een cloudservice van Azure. De front-end wordt een ASP.NET MVC-webrol en de back-end een werkrol die gebruikmaakt van een Service Bus-wachtrij. U kunt dezelfde toepassing met meerdere lagen maken met de front-end als een webproject dat wordt geïmplementeerd op een Azure-website in plaats van een cloudservice. Zie het gedeelte [Volgende stappen](#nextsteps) voor instructies over de stappen die anders moeten op een Azure-website-front-end. U kunt ook de zelfstudie [.NET on-premises/hybride cloud-toepassing](service-bus-dotnet-hybrid-app-using-service-bus-relay.md) gebruiken.

In de volgende schermafbeelding wordt de voltooide toepassing weergegeven.

![][0]

## Scenario-overzicht: communicatie tussen rollen

Als u een order wilt indienen voor verwerking, moet het front-end UI-onderdeel, dat wordt uitgevoerd in de webrol, communiceren met de logica van de middelste laag, die wordt uitgevoerd in de werkrol. In dit voorbeeld wordt Service Bus Brokered Messaging gebruikt voor communicatie tussen de lagen.

Met behulp van Brokered Messaging tussen de weblaag en de middelste laag worden de twee onderdelen losgekoppeld. In tegenstelling tot Direct Messaging (dat wil zeggen, TCP of HTTP) hoeft de weblaag niet rechtstreeks verbinding te maken met de middelste laag. In plaats daarvan worden werkeenheden, als berichten, naar Service Bus gepusht. Daar worden ze veilig bewaard totdat de middelste laag gereed is om ze te ontvangen en te verwerken.

Service Bus biedt twee entiteiten ter ondersteuning van Brokered Messaging: wachtrijen en onderwerpen. Met wachtrijen wordt elk bericht dat naar de wachtrij wordt verzonden, verbruikt door een enkele ontvanger. Onderwerpen ondersteunen het patroon voor publiceren/abonneren waarin elk gepubliceerde bericht beschikbaar wordt gesteld aan een abonnement dat bij het onderwerp is geregistreerd. Elk abonnement onderhoudt logisch gezien zijn eigen wachtrij met berichten. Abonnementen kunnen ook worden geconfigureerd met filterregels die de set berichten die wordt doorgegeven aan de abonnementenwachtrij beperken tot berichten die overeenkomen met het filter. In het volgende voorbeeld wordt gebruikgemaakt van Service Bus-wachtrijen.

![][1]

Dit communicatiemechanisme heeft verschillende voordelen ten opzichte van Direct Messaging:

-   **Tijdelijke ontkoppeling.** Met het asynchrone berichtenpatroon hoeven producenten en consumenten niet op hetzelfde moment online te zijn. Service Bus slaat de berichten veilig op totdat de verbruikende partij gereed is om de berichten te ontvangen. Hierdoor kunnen de onderdelen van de gedistribueerde toepassing worden losgekoppeld - hetzij vrijwillig, bijvoorbeeld voor onderhoud, hetzij vanwege het vastlopen van een onderdeel - zonder dat dit van invloed is op het systeem als geheel. Bovendien hoeft de betreffende toepassing mogelijk alleen online te komen op bepaalde tijdstippen gedurende de dag.

-   **Herverdeling van taken.** In veel toepassingen varieert de systeembelasting gedurende de tijd, terwijl de benodigde verwerkingstijd voor elke werkeenheid doorgaans constant blijft. Door een wachtrij tussen producenten en consumenten van berichten te plaatsen, hoeft de verbruikende toepassing (de werkrol) alleen te worden ingericht voor het opvangen van een gemiddelde belasting in plaats van een piekbelasting. De lengte van de wachtrij neemt toe of af, al naargelang het binnenkomende verkeer. Dit betekent een rechtstreekse besparing op de kosten voor de benodigde infrastructuur om de toepassingsbelasting te verwerken.

-   **Taakverdeling.** Naarmate het verkeer toeneemt, kunnen meer werkprocessen worden toegevoegd om uit de wachtrij te lezen. Elk bericht worden door slechts één van de werkprocessen verwerkt. Bovendien biedt deze pull-gebaseerde taakverdeling optimaal gebruik van de werkmachines, zelfs als de verwerkingskracht van de werkmachines verschilt en ze op eigen maximale snelheid berichten ophalen. Dit patroon wordt vaak aangeduid als het *concurrerend consumenten*-patroon.

    ![][2]

In de volgende gedeelten wordt de code besproken waarmee deze architectuur wordt geïmplementeerd.

## De ontwikkelomgeving instellen

Voordat u Azure-toepassingen kunt ontwikkelen, moet u de hulpprogramma's ophalen en uw ontwikkelomgeving instellen.

1.  Installeer de Azure SDK voor .NET via [Hulpprogramma's en SDK ophalen][].

2.  Klik op **De SDK installeren** voor de versie van Visual Studio die u gebruikt. In de stappen in deze zelfstudie wordt Visual Studio 2015 gebruikt.

4.  Klik op **Uitvoeren** wanneer u wordt gevraagd of u het installatieprogramma wilt uitvoeren of opslaan.

5.  Klik in het **webplatforminstallatieprogramma** op **Installeren** om door te gaan met de installatie.

6.  Nadat de installatie is voltooid, hebt u alles wat u nodig hebt om te starten met het ontwikkelen van de app. De SDK bevat hulpprogramma's waarmee u eenvoudig Azure-toepassingen kunt ontwikkelen in Visual Studio. Als Visual Studio nog niet is geïnstalleerd, wordt met de SDK ook het gratis programma Visual Studio Express geïnstalleerd.

## Een Service Bus-naamruimte maken

De volgende stap is het maken van een servicenaamruimte en ophalen van een SAS-sleutel (Shared Access Signature). Een naamruimte biedt een toepassingsbegrenzing voor elke toepassing die toegankelijk is via Service Bus. Een SAS-sleutel wordt gegenereerd door het systeem als een servicenaamruimte wordt gemaakt. De combinatie van naamruimte en SAS-sleutel biedt Service Bus de benodigde referenties voor het verifiëren van toegang tot een toepassing.

### De naamruimte instellen via de klassieke Azure-portal

1.  Meld u aan bij de [klassieke Azure-portal][].

2.  Klik in het linkernavigatievenster van de portal op **Service Bus**.

3.  Klik in het onderste deelvenster van de portal op **Maken**.

    ![][6]

4.  Voer op de pagina **Een nieuwe naamruimte toevoegen** een naamruimtenaam in. In het systeem wordt onmiddellijk gecontroleerd of de naam beschikbaar is.

    ![][7]

5.  Nadat u hebt gecontroleerd of de naam voor de naamruimte beschikbaar is, kiest u het land of de regio waarin uw naamruimte moet worden gehost. Zorg er daarbij voor dat u hetzelfde land of dezelfde regio gebruikt waarin u uw rekenresources implementeert. Zorg er ook voor dat u **Berichten** selecteert in het naamruimteveld **Type** en **Standaard** in het veld **Berichtenlaag**.

    > [AZURE.IMPORTANT] Kies **dezelfde regio** die u van plan bent te kiezen voor het implementeren van uw toepassing. Hiermee krijgt u de beste prestaties.

6.  Klik op het vinkje OK. Uw servicenaamruimte wordt nu gemaakt en ingeschakeld. U moet wellicht enkele minuten wachten terwijl de resources voor uw account worden ingericht.

7.  Klik op de naam van de servicenaamruimte in het hoofdvenster.

8. Klik op **Verbindingsgegevens**.

9.  Zoek in het deelvenster **Toegang tot verbindingsgegevens** de verbindingsreeks die de SAS-sleutel en de naam van de sleutel bevat.

    ![][35]

10.  Noteer deze referenties of kopieer ze naar het klembord.

11. Klik op dezelfde portalpagina op het tabblad **Configureren** boven aan de pagina.

12. Kopieer de primaire sleutel voor het beleid **RootManageSharedAccessKey** naar het klembord of plak deze in Kladblok. U gebruikt deze waarde verderop in deze zelfstudie.

    ![][36]

## Een webrol maken

In dit gedeelte maakt u de front-end van uw toepassing. U maakt eerst de pagina's die door uw toepassing worden weergegeven.
Vervolgens voegt u code toe waarmee items worden verzonden naar een Service Bus-wachtrij en waarmee statusinformatie over de wachtrij wordt weergegeven.

### Het project maken

1.  Start Microsoft Visual Studio met administratorbevoegdheden. U kunt Visual Studio starten met administratorbevoegdheden door met de rechtermuisknop op het programmapictogram **Visual Studio** te klikken en vervolgens te klikken op **Als administrator uitvoeren**. Voor de Azure-rekenemulator, die verderop in dit artikel wordt besproken, is vereist dat Visual Studio wordt gestart met administratorbevoegdheden.

    Klik in het menu **Bestand** van Visual Studio op **Nieuw** en klik vervolgens op **Project**.

2.  Klik vanuit **Geïnstalleerde sjablonen** onder **Visual C#** op **Cloud** en klik vervolgens op **Azure Cloud Service**. Geef het project de naam **MultiTierApp**. Klik vervolgens op **OK**.

    ![][9]

3.  Dubbelklik vanuit **.NET Framework 4.5**-rollen op **ASP.NET-webrol**.

    ![][10]

4.  Beweeg de muisaanwijzer over **WebRole1** onder **Azure Cloud Service-oplossing**, klik op het potloodpictogram en wijzig de naam van de webrol in **FrontendWebRole**. Klik vervolgens op **OK**. (Zorg ervoor dat u 'Frontend' invoert met een kleine letter 'e', dus niet 'FrontEnd'.)

    ![][11]

5.  Klik in het dialoogvenster **Nieuw ASP.NET-project** in de lijst **Een sjabloon selecteren** op **MVC**.

    ![][12]

6. Klik nog steeds vanuit het dialoogvenster **Nieuw ASP.NET-project** op de knop **Verificatie wijzigen**. Klik in het dialoogvenster **Verificatie wijzigen** op **Geen verificatie** en vervolgens op **OK**. In deze zelfstudie implementeert u een app waarvoor geen gebruikersaanmelding nodig is.

    ![][16]

7. Ga terug naar het dialoogvenster **Nieuw ASP.NET-project** en klik op **OK** om het project te maken.

6.  Klik in **Solution Explorer** in het project **FrontendWebRole** met de rechtermuisknop op **Verwijzingen** en klik vervolgens op **NuGet-pakketten beheren**.

7.  Klik op het tabblad **Bladeren** en zoek vervolgens naar `Microsoft Azure Service Bus`. Klik op **Installeren** en accepteer de gebruiksvoorwaarden.

    ![][13]

    Na de installatie wordt verwezen naar de vereiste clientassembly's en zijn enkele nieuwe codebestanden toegevoegd.

9.  Klik in **Solution Explorer** met de rechtermuisknop op **Modellen** en klik achtereenvolgens op **Toevoegen** en **Klasse**. Typ in het vak **Naam** de naam **OnlineOrder.cs**. Klik vervolgens op **Toevoegen**.

### De code voor de webrol schrijven

In dit gedeelte maakt u de verschillende pagina's die door uw toepassing worden weergegeven.

1.  In het bestand OnlineOrder.cs in Visual Studio vervangt u de bestaande naamruimtedefinitie door de volgende code:

    ```
    namespace FrontendWebRole.Models
    {
        public class OnlineOrder
        {
            public string Customer { get; set; }
            public string Product { get; set; }
        }
    }
    ```

2.  Dubbelklik in **Solution Explorer** op **Controllers\HomeController.cs**. Voeg de volgende **using**-instructies aan het begin van het bestand toe om de naamruimten op te nemen voor het model dat u zojuist hebt gemaakt, maar ook voor Service Bus.

    ```
    using FrontendWebRole.Models;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    ```

3.  In het bestand HomeController.cs in Visual Studio vervangt u bovendien de bestaande naamruimtedefinitie door de volgende code. Deze code bevat methoden voor het afhandelen van de verzending van items naar de wachtrij.

    ```
    namespace FrontendWebRole.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                // Simply redirect to Submit, since Submit will serve as the
                // front page of this application.
                return RedirectToAction("Submit");
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            // GET: /Home/Submit.
            // Controller method for a view you will create for the submission
            // form.
            public ActionResult Submit()
            {
                // Will put code for displaying queue message count here.
    
                return View();
            }
    
            // POST: /Home/Submit.
            // Controller method for handling submissions from the submission
            // form.
            [HttpPost]
            // Attribute to help prevent cross-site scripting attacks and
            // cross-site request forgery.  
            [ValidateAntiForgeryToken]
            public ActionResult Submit(OnlineOrder order)
            {
                if (ModelState.IsValid)
                {
                    // Will put code for submitting to queue here.
    
                    return RedirectToAction("Submit");
                }
                else
                {
                    return View(order);
                }
            }
        }
    }
    ```

4.  Klik in het menu **Bouwen** op **Oplossing opbouwen** om de juistheid van uw werk tot nu toe te controleren.

5.  Maak nu de weergave voor de methode voor `Submit()` die u eerder hebt gemaakt. Klik met de rechtermuisknop in de methode voor `Submit()` (de overbelasting van `Submit()` waarvoor geen parameters zijn vereist) en kies vervolgens **Weergave toevoegen**.

    ![][14]

6.  Een dialoogvenster voor het maken van de weergave wordt weergegeven. Kies **Maken** in de lijst **Sjabloon**. Klik in de lijst **Modelklasse** op de klasse **OnlineOrder**.

    ![][15]

7.  Klik op **Toevoegen**.

8.  Wijzig nu de weergegeven naam van uw toepassing. Dubbelklik in **Solution Explorer** op het bestand **Views\Shared\\_Layout.cshtml** om dit te openen in de Visual Studio-editor.

9.  Vervang alle instanties van **Mijn ASP.NET-toepassing** door **Producten van LITWARE**.

10. Verwijder de koppelingen **Start**, **Info** en **Contact**. Verwijder de gemarkeerde code:

    ![][28]

11. Wijzig tot slot de verzendpagina om enige informatie over de wachtrij op te nemen. Dubbelklik in **Solution Explorer** op het bestand **Views\Home\Submit.cshtml** om dit te openen in de Visual Studio-editor. Voeg de volgende regel toe na `<h2>Submit</h2>`. Op dit moment is `ViewBag.MessageCount` leeg. U vult dit later in.

    ```
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```

12. U hebt nu de gebruikersinterface geïmplementeerd. Druk op **F5** om uw toepassing uit te voeren en te controleren of deze voldoet aan uw verwachting.

    ![][17]

### De code schrijven voor het indienen van items aan een Service Bus-wachtrij

Voeg nu code toe voor het indienen van items aan een wachtrij. U maakt eerst een klasse die de verbindingsgegevens van de Service Bus-wachtrij bevat. Vervolgens initialiseert u de verbinding vanuit Global.aspx.cs. Tot slot werkt u de verzendcode bij die u eerder hebt gemaakt in HomeController.cs, zodat items daadwerkelijk naar een Service Bus-wachtrij worden verzonden.

1.  Klik in **Solution Explorer** met de rechtermuisknop op **FrontendWebRole** (klik met de rechtermuisknop op het project, niet op de rol). Klik op **Toevoegen** en klik vervolgens op **Klasse**.

2.  Geef de klasse de naam **QueueConnector.cs**. Klik op **Toevoegen** om de klasse te maken.

3.  Voeg nu code toe waarin de verbindingsgegevens zijn opgenomen en waarmee de verbinding met een Service Bus-wachtrij wordt geïnitialiseerd. Vervang de volledige inhoud van QueueConnector.cs door de volgende code en voer waarden in voor `your Service Bus namespace` (uw naamruimtenaam) en `yourKey`. Dit is de **primaire sleutel** die u eerder van de [klassieke Azure-portal][] hebt gekregen in stap 12 van het gedeelte 'Een Service Bus-naamruimte maken'.

    ```
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    
    namespace FrontendWebRole
    {
        public static class QueueConnector
        {
            // Thread-safe. Recommended that you cache rather than recreating it
            // on every request.
            public static QueueClient OrdersQueueClient;
    
            // Obtain these values from the portal.
            public const string Namespace = "your Service Bus namespace";
    
            // The name of your queue.
            public const string QueueName = "OrdersQueue";
    
            public static NamespaceManager CreateNamespaceManager()
            {
                // Create the namespace manager which gives you access to
                // management operations.
                var uri = ServiceBusEnvironment.CreateServiceUri(
                    "sb", Namespace, String.Empty);
                var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                    "RootManageSharedAccessKey", "yourKey");
                return new NamespaceManager(uri, tP);
            }
    
            public static void Initialize()
            {
                // Using Http to be friendly with outbound firewalls.
                ServiceBusEnvironment.SystemConnectivity.Mode =
                    ConnectivityMode.Http;
    
                // Create the namespace manager which gives you access to
                // management operations.
                var namespaceManager = CreateNamespaceManager();
    
                // Create the queue if it does not exist already.
                if (!namespaceManager.QueueExists(QueueName))
                {
                    namespaceManager.CreateQueue(QueueName);
                }
    
                // Get a client to the queue.
                var messagingFactory = MessagingFactory.Create(
                    namespaceManager.Address,
                    namespaceManager.Settings.TokenProvider);
                OrdersQueueClient = messagingFactory.CreateQueueClient(
                    "OrdersQueue");
            }
        }
    }
    ```

4.  Controleer nu of uw methode voor **Initialiseren** wordt aangeroepen. Dubbelklik in **Solution Explorer** op **Global.asax\Global.asax.cs**.

5.  Voeg de volgende coderegel toe aan het einde van de methode **Application_Start**.

    ```
    FrontendWebRole.QueueConnector.Initialize();
    ```

6.  Werk tot slot de webcode die u eerder hebt gemaakt, bij om items naar de wachtrij te verzenden. Dubbelklik in **Solution Explorer** op **Controllers\HomeController.cs**.

7.  Werk de methode `Submit()` (de overbelasting waarvoor geen parameters zijn vereist) als volgt bij om het aantal berichten voor de wachtrij te verkrijgen.

    ```
    public ActionResult Submit()
    {
        // Get a NamespaceManager which allows you to perform management and
        // diagnostic operations on your Service Bus queues.
        var namespaceManager = QueueConnector.CreateNamespaceManager();
    
        // Get the queue, and obtain the message count.
        var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
        ViewBag.MessageCount = queue.MessageCount;
    
        return View();
    }
    ```

8.  Werk de methode `Submit(OnlineOrder order)` (de overbelasting waarvoor één parameter moet worden gebruikt) als volgt bij om ordergegevens naar de wachtrij te verzenden.

    ```
    public ActionResult Submit(OnlineOrder order)
    {
        if (ModelState.IsValid)
        {
            // Create a message from the order.
            var message = new BrokeredMessage(order);
    
            // Submit the order.
            QueueConnector.OrdersQueueClient.Send(message);
            return RedirectToAction("Submit");
        }
        else
        {
            return View(order);
        }
    }
    ```

9.  U kunt nu de toepassing opnieuw uitvoeren. Het aantal berichten neemt toe elke keer wanneer u een order verzendt.

    ![][18]

## De werkrol maken

U maakt nu de werkrol die de orderverzendingen verwerkt. In dit voorbeeld wordt de Visual Studio-projectsjabloon **Werkrol met Service Bus-wachtrij** gebruikt. U hebt al de vereiste referenties ontvangen van de portal.

1. Zorg ervoor dat u Visual Studio aan uw Azure-account hebt gekoppeld.

2.  Klik in Visual Studio in **Solution Explorer** met de rechtermuisknop op de map **Rollen** onder het **MultiTierApp**-project.

3.  Klik op **Toevoegen** en klik vervolgens op **Nieuw werkrolproject**. Het dialoogvenster **Nieuw rolproject toevoegen** wordt weergegeven.

    ![][26]

4.  Klik in het dialoogvenster **Nieuw rolproject toevoegen** op **Werkrol met Service Bus-wachtrij**.

    ![][23]

5.  Voer in het vak **Naam** de naam **OrderProcessingRole** voor het project in. Klik vervolgens op **Toevoegen**.

6.  Kopieer de verbindingsreeks die u hebt verkregen in stap 9 van het gedeelte 'Een Service Bus-naamruimte maken' naar het klembord.

7.  Klik in **Solution Explorer** met de rechtermuisknop op de **OrderProcessingRole** die u in stap 5 hebt gemaakt (klik met de rechtermuisknop op **OrderProcessingRole** onder **Rollen** en niet onder de klasse). Klik vervolgens op **Eigenschappen**.

8.  Klik op het tabblad **Instellingen** van het dialoogvenster **Eigenschappen** in het vak **Waarde** voor **Microsoft.ServiceBus.ConnectionString**. Plak vervolgens de eindpuntwaarde die u in stap 6 hebt gekopieerd.

    ![][25]

9.  Maak een klasse **OnlineOrder** die de orders vertegenwoordigt tijdens het verwerken van de wachtrij. U kunt een klasse die u al hebt gemaakt opnieuw gebruiken. Klik in **Solution Explorer** met de rechtermuisknop op de klasse **OrderProcessingRole** (klik met de rechtermuisknop op het klassepictogram en niet op de rol). Klik op **Toevoegen** en vervolgens op **Bestaand item**.

10. Blader naar de submap voor **FrontendWebRole\Models** en dubbelklik vervolgens op **OnlineOrder.cs** om het bestand toe te voegen aan dit project.

11. Wijzig in **WorkerRole.cs** de waarde van de variabele **QueueName** van `"ProcessingQueue"` in `"OrdersQueue"`, zoals weergegeven in de volgende code.

    ```
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```

12. Voeg de volgende instructie toe aan het begin van het bestand WorkerRole.cs.

    ```
    using FrontendWebRole.Models;
    ```

13. In de functie `Run()` binnen de aanroep `OnMessage()` vervangt u de inhoud van de `try`-component door de volgende code.

    ```
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```

14. U hebt de toepassing nu voltooid. U kunt de volledige toepassing testen door met de rechtermuisknop te klikken op het MultiTierApp-project in Solution Explorer, **Instellen als opstartproject** te selecteren en vervolgens op F5 te drukken. Houd er rekening mee dat het aantal berichten niet toeneemt, omdat de werkrol items uit de wachtrij verwerkt en als voltooid markeert. U ziet de trace-uitvoer van uw werkrol door de gebruikersinterface van de Azure-rekenemulator weer te geven. Klik hiervoor met de rechtermuisknop op het emulatorpictogram in het systeemvak van de taakbalk en selecteer **Gebruikersinterface van de rekenemulator weergeven**.

    ![][19]

    ![][20]

## Volgende stappen  

Zie de volgende resources voor meer informatie over Service Bus:  

* [Azure Service Bus][sbmsdn]  
* [Service Bus-servicepagina][sbwacom]  
* [Service Bus-wachtrijen gebruiken][sbwacomqhowto]  

Zie voor meer informatie over scenario's voor meerdere lagen:  

* [.NET-toepassing met meerdere lagen die opslagtabellen, wachtrijen en blobs gebruikt][mutitierstorage]  

  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Hulpprogramma's en SDK ophalen]: http://go.microsoft.com/fwlink/?LinkId=271920


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [klassieke Azure-portal]: http://manage.windowsazure.com
  [6]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [35]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/multi-web-45.png
  [36]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/service-bus-policies.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: service-bus-dotnet-how-to-use-queues.md  
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  


<!--HONumber=Jun16_HO2-->


