<properties
    pageTitle="Hybride on-premises/cloudtoepassing (.NET) | Microsoft Azure"
    description="Informatie over het maken van een .NET hybride on-premises/cloudtoepassing met Azure Service Bus Relay."
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
    ms.topic="get-started-article"
    ms.date="05/23/2016"
    ms.author="sethm"/>

# Hybride .NET on-premises/cloudtoepassing met Azure Service Bus Relay

## Inleiding

In dit artikel wordt beschreven hoe u een hybride cloudtoepassing opbouwt met Microsoft Azure en Visual Studio. Bij deze zelfstudie wordt ervan uitgegaan dat u nog geen ervaring hebt met Azure. In minder dan 30 minuten beschikt u over een toepassing die meerdere Azure-resources gebruikt en wordt uitgevoerd in de cloud.

U leert:

-   Een bestaande webservice voor verbruik door een weboplossing maken of aanpassen.
-   Azure Service Bus Relay gebruiken voor het delen van gegevens tussen een Azure-toepassing en een webservice die ergens anders wordt gehost.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Hoe Service Bus Relay bij hybride oplossingen hulp biedt

Bedrijfsoplossingen bestaan meestal uit een combinatie van aangepaste code die is geschreven voor nieuwe en unieke zakelijke vereisten en bestaande functionaliteit van oplossingen en systemen die al aanwezig zijn.

Oplossingsarchitecten gaan nu vaak over op de cloud om gemakkelijk aan schaalvereisten te voldoen en operationele kosten te verlagen. Ze ondervinden dan dat bestaande serviceassets die ze willen gebruiken als bouwstenen voor hun oplossingen, zich binnen de firewall van het bedrijf bevinden en niet gemakkelijk bereikbaar zijn voor de cloudoplossing. Veel interne services worden niet zo gemaakt of gehost dat ze gemakkelijk beschikbaar kunnen worden gemaakt aan de rand van het bedrijfsnetwerk.

Service Bus Relay is bedoeld voor de gebruikstoepassing waarbij bestaande WCF-webservices (Windows Communication Foundation) veilig toegankelijk worden gemaakt voor oplossingen die zich buiten de bedrijfsperimeter bevinden zonder dat er tussenkomende wijzigingen in de infrastructuur van een bedrijfsnetwerk nodig zijn. Dergelijke Service Bus Relay-services worden nog steeds gehost binnen hun bestaande omgeving, maar ze dragen het luisteren naar binnenkomende sessies en aanvragen over aan Service Bus in de cloud. Ook beveiligt Service Bus deze services tegen onbevoegde toegang door [SAS](service-bus-sas-overview.md)-verificatie (Shared Access Signature) te gebruiken.

## Oplossingsscenario

In deze zelfstudie maakt u een ASP.NET-website waarmee u een lijst met producten op de pagina met de productinventaris kunt bekijken.

![][0]

In de zelfstudie wordt ervan uitgegaan dat u productgegevens in een bestaand on-premises systeem hebt en wordt Service Bus Relay gebruikt om dat systeem te bereiken. Dit wordt gesimuleerd door een webservice die in een eenvoudige consoletoepassing wordt uitgevoerd en wordt ondersteund door een set producten in het geheugen. U kunt deze consoletoepassing op uw eigen computer uitvoeren en de webrol in Azure implementeren. Als u dit doet, ziet u hoe de webrol die in het Azure-datacenter wordt uitgevoerd inderdaad uw computer aanroept, ondanks dat de computer bijna zeker achter ten minste één firewall en een NAT-laag (Network Address Translation) blijft.

Hier volgt een schermopname van de startpagina van de voltooide webtoepassing.

![][1]

## De ontwikkelomgeving instellen

Voordat u Azure-toepassingen kunt ontwikkelen, moet u de hulpprogramma's ophalen en uw ontwikkelomgeving instellen.

1.  Installeer de Azure SDK voor .NET via [Hulpprogramma's en SDK ophalen][].

2.  Klik op **De SDK installeren** voor de versie van Visual Studio die u gebruikt. In de stappen in deze zelfstudie wordt Visual Studio 2015 gebruikt.

4.  Klik op **Uitvoeren** wanneer u wordt gevraagd of u het installatieprogramma wilt uitvoeren of opslaan.

5.  Klik in het **webplatforminstallatieprogramma** op **Installeren** om door te gaan met de installatie.

6.  Nadat de installatie is voltooid, hebt u alles wat u nodig hebt om te starten met het ontwikkelen van de app. De SDK bevat hulpprogramma's waarmee u eenvoudig Azure-toepassingen kunt ontwikkelen in Visual Studio. Als Visual Studio nog niet is geïnstalleerd, wordt met de SDK ook het gratis programma Visual Studio Express geïnstalleerd.

## Een naamruimte maken

Als u Service Bus-functies in Azure wilt gebruiken, moet u eerst een servicenaamruimte maken. Een naamruimte biedt een scoping container voor het verwerken van Service Bus-resources in uw toepassing.

1.  Meld u aan bij de [klassieke Azure-portal][].

2.  Klik in het linkernavigatievenster van de portal op **Service Bus**.

3.  Klik in het onderste deelvenster van de portal op **Maken**.

    ![][5]

4.  Voer in het dialoogvenster **Een nieuwe naamruimte toevoegen** een naamruimtenaam in.
    In het systeem wordt onmiddellijk gecontroleerd of de naam beschikbaar is.
    ![][6]

5.  Nadat u hebt gecontroleerd of de naam voor de naamruimte beschikbaar is, kiest u het land of de regio waarin uw naamruimte moet worden gehost. Zorg er daarbij voor dat u hetzelfde land of dezelfde regio gebruikt waarin u uw rekenresources implementeert.

    > [AZURE.IMPORTANT] Kies *dezelfde regio* die u van plan bent te kiezen voor het implementeren van uw toepassing. Hiermee krijgt u de beste prestaties.

6.  Laat de standaardwaarden van de andere velden ongewijzigd en klik vervolgens op het vinkje OK. Uw naamruimte wordt gemaakt en ingeschakeld. U moet wellicht enkele minuten wachten terwijl de resources voor uw account worden ingericht.

De naamruimte die u hebt gemaakt, wordt weergegeven in de portal, maar het duurt even voordat deze is geactiveerd. Wacht totdat de status **Actief** is voordat u doorgaat.

## De standaardbeheerreferenties voor de naamruimte verkrijgen

Als u beheerbewerkingen voor de nieuwe naamruimte wilt uitvoeren, zoals het maken van berichtentiteiten, moet u referenties voor de naamruimte verkrijgen.

1.  Klik in het hoofdvenster op de naamruimte die u in de vorige stap hebt gemaakt.

2.  Klik onder aan de pagina op **Verbindingsgegevens**.

3.  Zoek in het deelvenster **Toegang tot verbindingsgegevens** de verbindingsreeks die de SAS-sleutel en de naam van de sleutel bevat.

    ![][45]

4.  Kopieer de verbindingsreeks en plak deze ergens. U hebt de verbindingsreeks later in deze zelfstudie nodig.

5. Klik op dezelfde portalpagina op het tabblad **Configureren** boven aan de pagina.

6. Kopieer de primaire sleutel voor het beleid **RootManageSharedAccessKey** naar het klembord of plak deze in Kladblok. U gebruikt deze waarde verderop in deze zelfstudie.

    ![][46]

## Een on-premises server maken

U bouwt eerst een on-premises (model)systeem voor de productcatalogus op. Dit is redelijk eenvoudig; u kunt dit zien als het representeren van een werkelijk on-premises productcatalogussysteem met een volledige serviceoppervlak dat we proberen te integreren.

Dit project is een Visual Studio-consoletoepassing en gebruikt het [Azure Service Bus NuGet-pakket](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) om de Service Bus-bibliotheken en configuratie-instellingen op te nemen.

### Het project maken

1.  Start Microsoft Visual Studio met administratorbevoegdheden. U kunt Visual Studio starten met administratorbevoegdheden door met de rechtermuisknop op het programmapictogram **Visual Studio** te klikken en vervolgens te klikken op **Als administrator uitvoeren**.

2.  Klik in het menu **Bestand** van Visual Studio op **Nieuw** en klik vervolgens op **Project**.

3.  Klik in **Geïnstalleerde sjablonen** onder **Visual C#** op **Consoletoepassing**. Typ in het vak **Naam** de naam **ProductsServer**:

    ![][11]

4.  Klik op **OK** om het project **ProductsServer** te maken.

7.  Als u NuGet Package Manager voor Visual Studio al hebt geïnstalleerd, slaat u de volgende stap over. Anders gaat u naar [NuGet][] en klikt u op [NuGet installeren](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Volg de aanwijzingen om NuGet Package Manager te installeren en vervolgens Visual Studio opnieuw te starten.

7.  Klik in Solution Explorer met de rechtermuisknop op het project **ProductsServer** en klik vervolgens op **NuGet-pakketten beheren**.

8.  Klik op het tabblad **Bladeren** en zoek vervolgens naar `Microsoft Azure Service Bus`. Klik op **Installeren** en accepteer de gebruiksvoorwaarden.

    ![][13]

    Er wordt nu naar de vereiste clientassembly's verwezen.

9.  Voeg een nieuwe klasse voor uw productcontract toe. Klik in Solution Explorer met de rechtermuisknop op het project **ProductsServer** en klik achtereenvolgens op **Toevoegen** en **Klasse**.

10. Typ in het vak **Naam** de naam **ProductsContract.cs**. Klik vervolgens op **Toevoegen**.

11. Vervang in **ProductsContract.cs** de naamruimtedefinitie door de volgende code waarmee het contract voor de service wordt gedefinieerd.

    ```
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;
    
        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }
    
        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();
    
        }
    
        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

12. Vervang in Program.cs de naamruimtedefinitie door de volgende code waarmee de profielservice en de host voor deze service worden toegevoegd.

    ```
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;
    
        // Implement the IProducts interface.
        class ProductsService : IProducts
        {
    
            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };
    
            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }
    
        }
    
        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();
    
                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();
    
                sh.Close();
            }
        }
    }
    ```

13. Dubbelklik in Solution Explorer op het bestand **App.config** om dit te openen in de Visual Studio-editor. Aan de onderkant van het **&lt;system.ServiceModel&gt;**-element (maar nog steeds binnen &lt;system.ServiceModel&gt;) voegt u de volgende XML-code toe. Zorg ervoor dat u *yourServiceNamespace* vervangt door de naam van uw naamruimte en *yourKey* door de SAS-sleutel die u eerder hebt opgehaald via de portal:

    ```
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
14. Vervang verder in App.config in het **&lt;appSettings&gt;**-element de verbindingsreekswaarde door de verbindingsreeks die u eerder hebt verkregen via de portal. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

14. Druk op **Ctrl+Shift+B** of klik in het menu **Bouwen** op **Oplossing opbouwen** en controleer de juistheid van uw werk tot nu toe.

## Een ASP.NET-toepassing maken

In deze sectie bouwt u een eenvoudige ASP.NET-toepassing op waarmee gegevens worden weergegeven die u uit uw productservice hebt opgehaald.

### Het project maken

1.  Zorg ervoor dat Visual Studio met administratorbevoegdheden wordt uitgevoerd.

2.  Klik in het menu **Bestand** van Visual Studio op **Nieuw** en klik vervolgens op **Project**.

3.  Klik in **Geïnstalleerde sjablonen** onder **Visual C#** op **ASP.NET-webtoepassing**. Noem het project **ProductsPortal**. Klik vervolgens op **OK**.

    ![][15]

4.  Selecteer in de lijst **Een sjabloon selecteren** de optie **MVC**. 

6.  Schakel het selectievakje voor **In de cloud hosten** in.

    ![][16]

5. Klik op de knop **Verificatie wijzigen**. Klik in het dialoogvenster **Verificatie wijzigen** op **Geen verificatie** en vervolgens op **OK**. In deze zelfstudie implementeert u een app waarvoor geen gebruikersaanmelding nodig is.

    ![][18]

6.  Zorg er in het gedeelte **Microsoft Azure** van het dialoogvenster **Nieuw ASP.NET-project** voor dat **Hosten in de cloud** is geselecteerd en dat **App Service** is geselecteerd in de vervolgkeuzelijst.

    ![][19]

7. Klik op **OK**. 

8. U moet nu Azure-resources configureren voor een nieuwe web-app. Volg alle stappen in de sectie [Azure-resources configureren voor een nieuwe web-app](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app). Ga vervolgens terug naar deze zelfstudie en ga door met de volgende stap.

5.  Klik in Solution Explorer met de rechtermuisknop op **Modellen** en klik achtereenvolgens op **Toevoegen** en **Klasse**. Typ in het vak **Naam** de naam **Product.cs**. Klik vervolgens op **Toevoegen**.

    ![][17]

### De webtoepassing wijzigen

1.  In het bestand Product.cs in Visual Studio vervangt u de bestaande naamruimtedefinitie door de volgende code.

    ```
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
        public class Product
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public string Quantity { get; set; }
        }
    }
    ```

2.  Vouw in Solution Explorer de map **Controllers** uit en dubbelklik vervolgens op het bestand **HomeController.cs** om het te openen in Visual Studio.

3. In **HomeController.cs** vervangt u de bestaande naamruimtedefinitie door de volgende code.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;
    
        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

3.  Vouw in Solution Explorer de map Views\Shared uit en dubbelklik vervolgens op **_Layout.cshtml** om dit in de Visual Studio-editor te openen.

5.  Wijzig alle instanties van **Mijn ASP.NET-toepassing** in **Producten van LITWARE**.

6. Verwijder de koppelingen **Start**, **Info** en **Contact**. Verwijder de gemarkeerde code in het volgende voorbeeld.

    ![][41]

7.  Vouw in Solution Explorer de map Views\Home uit en dubbelklik vervolgens op **Index.cshtml** om dit in de Visual Studio-editor te openen.
    Vervang de volledige inhoud van het bestand door de volgende code.

    ```
    @model IEnumerable<ProductsWeb.Models.Product>
    
    @{
            ViewBag.Title = "Index";
    }
    
    <h2>Prod Inventory</h2>
    
    <table>
            <tr>
                <th>
                    @Html.DisplayNameFor(model => model.Name)
                </th>
                  <th></th>
                <th>
                    @Html.DisplayNameFor(model => model.Quantity)
                </th>
            </tr>
    
    @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Name)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Quantity)
                </td>
            </tr>
    }
    
    </table>
    ```

9.  U kunt de nauwkeurigheid van uw werk tot nu toe controleren door op **Ctrl+Shift+B** te drukken om het project op te bouwen.


### De app lokaal uitvoeren

Voer de toepassing uit om te controleren of deze werkt.

1.  Zorg ervoor dat **ProductsPortal** het actieve project is. Klik met de rechtermuisknop op de projectnaam in Solution Explorer en selecteer **Instellen als opstartproject**.
2.  Druk in Visual Studio op F5.
3.  Uw toepassing moet dan in een browser worden weergegeven.

    ![][21]

## De softwareonderdelen samenstellen

In de volgende stap koppelt u de on-premises productenserver aan de ASP.NET-toepassing.

1.  Als het project **ProductsPortal** dat u hebt gemaakt in de sectie 'Een ASP.NET-toepassing maken' nog niet open is, opent u het opnieuw in Visual Studio.

2.  Vergelijkbaar met de stap in de sectie 'Een on-premises server maken' voegt u het NuGet-pakket aan de projectverwijzingen toe. Klik in Solution Explorer met de rechtermuisknop op het project **ProductsPortal** en klik vervolgens op **NuGet-pakketten beheren**.

3.  Zoek Service Bus en selecteer het item **Microsoft Azure Service Bus**. Vervolgens voltooit u de installatie en sluit u dit dialoogvenster.

4.  Klik in Solution Explorer met de rechtermuisknop op het project **ProductsPortal** en klik achtereenvolgens op **Toevoegen** en **Bestaand item**.

5.  Ga naar het bestand **ProductsContract.cs** vanuit het **ProductsServer**-consoleproject. Klik om ProductsContract.cs te markeren. Klik op de pijl-omlaag naast **Toevoegen** en klik vervolgens op **Toevoegen als koppeling**.

    ![][24]

6.  Open nu het bestand **HomeController.cs** in de Visual Studio-editor en vervang de naamruimtedefinitie door de volgende code. Zorg ervoor dat u *yourServiceNamespace* vervangt door de naam van uw servicenaamruimte en *yourKey* door de SAS-sleutel. Hierdoor kan de client de on-premises service aanroepen waarbij het resultaat van de aanroep wordt geretourneerd.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Linq;
        using System.ServiceModel;
        using System.Web.Mvc;
        using Microsoft.ServiceBus;
        using Models;
        using ProductsServer;
    
        public class HomeController : Controller
        {
            // Declare the channel factory.
            static ChannelFactory<IProductsChannel> channelFactory;
    
            static HomeController()
            {
                // Create shared access signature token credentials for authentication.
                channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                    "sb://yourServiceNamespace.servicebus.windows.net/products");
                channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                    TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey") });
            }
    
            public ActionResult Index()
            {
                using (IProductsChannel channel = channelFactory.CreateChannel())
                {
                    // Return a view of the products inventory.
                    return this.View(from prod in channel.GetProducts()
                                     select
                                         new Product { Id = prod.Id, Name = prod.Name,
                                             Quantity = prod.Quantity });
                }
            }
        }
    }
    ```

7.  Klik in Solution Explorer met de rechtermuisknop op de oplossing **ProductsPortal** en klik achtereenvolgens op **Toevoegen** en **Bestaand project**.

8.  Ga naar het **ProductsServer**-project en dubbelklik op het **ProductsServer.csproj**-oplossingsbestand om het toe te voegen.

9.  **ProductsServer** moet worden uitgevoerd om de gegevens in **ProductsPortal** weer te geven. Klik in Solution Explorer met de rechtermuisknop op de oplossing **ProductsPortal** en klik op **Eigenschappen**. Het dialoogvenster **Eigenschappenvensters** wordt weergegeven.

10. Klik aan de linkerkant op **Opstartproject**. Klik aan de rechterkant op **Meerdere opstartprojecten**. Zorg ervoor dat **ProductsServer** en **ProductsPortal** in de juiste volgorde worden weergegeven waarbij voor beide **Starten** als actie is ingesteld.

      ![][25]

11. Klik ook in het dialoogvenster **Eigenschappen** op **Projectafhankelijkheden** aan de linkerkant.

12. Klik in de lijst **Projecten** op **ProductsServer**. Zorg ervoor dat **ProductsPortal** **niet** is geselecteerd.

14. Klik in de lijst **Projecten** op **ProductsPortal**. Zorg ervoor dat **ProductsServer** is geselecteerd. 

    ![][26]

15. Klik op **OK** in het dialoogvenster **Eigenschappenvensters**.

## Het project lokaal uitvoeren

Test de toepassing lokaal door in Visual Studio op **F5** te drukken. De on-premises server (**ProductsServer**) moet eerst worden gestart en vervolgens moet de **ProductsPortal**-toepassing worden gestart in een browservenster. De productinventaris bevat nu de gegevens die zijn opgehaald uit het on-premises systeem van de productservice.

![][10]

Klik op **Vernieuwen** op de pagina **ProductsPortal**. Elke keer dat u de pagina vernieuwt, wordt met de server-app een bericht weergegeven wanneer `GetProducts()` vanuit **ProductsServer** wordt aangeroepen.

## Het project ProductsPortal in een Azure-web-app implementeren

De volgende stap is het converteren van de **ProductsPortal**-frontend naar een Azure-web-app. Implementeer eerst het **ProductsPortal**-project waarbij u alle stappen in de sectie [Het webproject implementeren in de Azure-web-app](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app) uitvoert. Ga terug naar deze zelfstudie als de implementatie is voltooid en ga door met de volgende stap. 

Kopieer de URL van de geïmplementeerde web-app. Deze hebt u in de volgende stap nodig. Deze URL is ook beschikbaar in het venster Azure App Service-activiteit in Visual Studio:

![][9] 
   

> [AZURE.NOTE] Mogelijk wordt er een foutbericht weergegeven in het browservenster wanneer het **ProductsPortal**-webproject automatisch wordt gestart na de implementatie. Dit is normaal en doet zich voor omdat de **ProductsServer**-toepassing nog niet wordt uitgevoerd.

### ProductsPortal instellen als web-app

Voordat u de toepassing in de cloud uitvoert, moet u ervoor zorgen dat **ProductsPortal** vanuit Visual Studio als een web-app wordt uitgevoerd.

1. Klik in Visual Studio met de rechtermuisknop op het **ProjectsPortal**-project en klik vervolgens op **Eigenschappen**.

3. Klik in de kolom links op **Web**.

5. Klik in de sectie **Actie starten** op de knop **Start-URL** en voer in het tekstvak de URL voor uw eerder geïmplementeerde web-app (bijvoorbeeld `http://productsportal1234567890.azurewebsites.net/`) in.

    ![][27]

6. Klik in het menu **Bestand** in Visual Studio op **Alles opslaan**.

7. Klik in het menu Bouwen in Visual Studio op **Oplossing opnieuw opbouwen**.

## De toepassing uitvoeren

2.  Druk op F5 om de toepassing op te bouwen en uit te voeren. De on-premises server (de **ProductsServer**-consoletoepassing) moet eerst worden gestart en vervolgens moet de **ProductsPortal**-toepassing worden gestart in een browservenster (zie de volgende schermopname). U ziet weer dat de productinventaris gegevens bevat die zijn opgehaald uit het on-premises systeem van de productservice en dat die gegevens in de web-app worden weergegeven. Controleer de URL om ervoor te zorgen dat **ProductsPortal** als een Azure-web-app wordt uitgevoerd in de cloud. 

    ![][1]

    > [AZURE.IMPORTANT] De **ProductsServer**-consoletoepassing moet worden uitgevoerd en moet de gegevens aan de **ProductsPortal**-toepassing kunnen leveren. Als een fout wordt weergegeven in de browser, wacht u enkele seconden tot **ProductsServer** is geladen en het volgende bericht wordt weergegeven. Klik vervolgens op **Vernieuwen** in de browser.

    ![][37]

3. Als de browser opnieuw wordt weergegeven, klikt u op **Vernieuwen** op de **ProductsPortal**-pagina. Elke keer dat u de pagina vernieuwt, wordt met de server-app een bericht weergegeven wanneer `GetProducts()` vanuit **ProductsServer** wordt aangeroepen.

    ![][38]

## Volgende stappen  

Zie de volgende resources voor meer informatie over Service Bus:  

* [Azure Service Bus][sbwacom]  
* [Service Bus-wachtrijen gebruiken][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Hulpprogramma's en SDK ophalen]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  
  [klassieke Azure-portal]: http://manage.windowsazure.com
  [5]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png


  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
  [19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
  [9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png
  
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [45]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-45.png
  [46]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/service-bus-policies.png

  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: service-bus-dotnet-how-to-use-queues.md




<!--HONumber=Jun16_HO2-->


