<properties 
    pageTitle="Zelfstudie over Service Bus Relayed Messaging | Microsoft Azure"
    description="Bouw een Service Bus-clienttoepassing en -service met Service Bus Relayed Messaging."
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
    ms.date="05/17/2016"
    ms.author="sethm" />

# Zelfstudie over Service Bus Relayed Messaging

In deze zelfstudie wordt beschreven hoe u een eenvoudige Service Bus-clienttoepassing en -service maakt met de Service Bus Relay-mogelijkheden. Zie [Service Bus Brokered Messaging .NET-zelfstudie](service-bus-brokered-tutorial-dotnet.md); dit is een bijbehorende zelfstudie waarin gebruik wordt gemaakt van Service Bus [Brokered Messaging](service-bus-messaging-overview.md#Brokered-messaging).

In deze zelfstudie doorloopt u de verschillende stappen die nodig zijn om een Service Bus-client en een servicetoepassing te maken. Net als bij WCF is een service een middel met één of meer eindpunten. Elk eindpunt verwijst naar één of meer servicebewerkingen. Het eindpunt van een service bevat het adres van de service, een binding met de informatie die een client moet doorgeven aan de service en een contract waarin staat welke functionaliteit de service biedt aan de clients. Het belangrijkste verschil tussen een WCF- en een Service Bus-service is dat het eindpunt bij Service Bus in de cloud beschikbaar is in plaats van lokaal op uw computer.

Als u de onderwerpen in deze zelfstudie op de juiste volgorde doorloopt, hebt u een actieve service en een client waarmee de bewerkingen van de service kunnen worden aangeroepen. In het eerste onderwerp wordt beschreven hoe u een account instelt. In de volgende stappen wordt beschreven hoe u een service definieert die gebruikmaakt van een contract, hoe u de service implementeert en hoe u de service in code configureert. Er wordt ook beschreven hoe u kunt hosten en hoe u de service uitvoert. De service die is gemaakt, host zichzelf. De client en de service worden uitgevoerd op dezelfde computer. U kunt de service configureren met code of met een configuratiebestand.

In de laatste drie stappen wordt beschreven hoe u een clienttoepassing maakt, hoe u de clienttoepassing configureert en hoe u een client maakt en gebruikt die toegang heeft tot de functionaliteit van de host.

Voor alle onderwerpen in dit gedeelte wordt ervan uitgegaan dat u Visual Studio gebruikt als ontwikkelomgeving.

## Registreren voor een account

De eerste stap is het maken van een Service Bus-servicenaamruimte en het ophalen van een SAS-sleutel (Shared Access Signature). Een naamruimte biedt een toepassingsbegrenzing voor elke toepassing die toegankelijk is via Service Bus. De combinatie van naamruimte en SAS-sleutel biedt Service Bus een referentie voor het verifiëren van toegang tot een toepassing.

1. Voor het maken van een naamruimte gaat u naar de [klassieke Azure-portal][]. Klik op **Service Bus** aan de linkerkant en klik op **Maken**. Typ een naam voor de naamruimte, accepteer de standaardwaarden voor alle andere waarden en klik op het vinkje OK.

    >[AZURE.NOTE] U hoeft niet dezelfde naamruimte te gebruiken voor de client- en servicetoepassingen.

    ![][4]

1. Klik in het hoofdvenster van de portal op de naam van de naamruimte die u in de vorige stap hebt gemaakt.

2. Klik op het tabblad **Configureren** om het standaard gedeelde toegangsbeleid en de sleutels voor uw naamruimte te bekijken.

    ![][1]

3. Noteer de primaire sleutel voor het beleid **RootManageSharedAccessKey** of kopieer deze naar het klembord. U gebruikt deze waarde verderop in deze zelfstudie.

## Een WCF-servicecontract definiëren voor gebruik met Service Bus

In het servicecontract wordt aangegeven welke bewerkingen (de webserviceterminologie voor methoden of functies) de service ondersteuning biedt. Contracten worden gemaakt door een C++-, C#- of Visual Basic-interface te definiëren. Elke methode in de interface komt overeen met een specifieke servicebewerking. Op elke interface moet het kenmerk [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) worden toegepast en op elke bewerking moet het kenmerk [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) worden toegepast. Als een methode in een interface met het kenmerk [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) niet beschikt over het kenmerk [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), wordt die methode niet weergegeven. In het voorbeeld na de procedure wordt de code voor deze taken weergegeven. Zie [Services ontwerpen en implementeren](https://msdn.microsoft.com/library/ms729746.aspx) in de WCF-documentatie voor meer informatie over contracten en services.

### Een Service Bus-contract met een interface maken

1. Open Visual Studio als beheerder door met de rechtermuisknop achtereenvolgens op het programma in het menu **Start** en op **Als administrator uitvoeren** te klikken.

2. Maak een nieuw consoletoepassingsproject. Klik op het menu **Bestand**, selecteer **Nieuw** en klik vervolgens op **Project**. Klik in het dialoogvenster **Nieuw project** op **Visual C#** (als **Visual C#** niet wordt weergegeven, kijkt u bij **Andere talen**). Klik op het sjabloon **Consoletoepassing** en noem het **EchoService**. Klik op **OK** om het project te maken.

    ![][2]

3. Installeer het Service Bus-pakket NuGet. Met dit pakket worden automatisch verwijzingen naar de Service Bus-bibliotheken en naar het **System.ServiceModel** van WCF toegevoegd. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) is de naamruimte die programmatisch toegang biedt tot de basisfuncties van WCF. Service Bus maakt gebruik van veel van de objecten en kenmerken van WCF om servicecontracten te definiëren.

    Klik in Solution Explorer met de rechtermuisknop op de oplossing en kies **NuGet-pakketten beheren voor oplossing**. Klik op het tabblad **Bladeren** en zoek vervolgens naar `Microsoft Azure Service Bus`. Zorg ervoor dat de naam van het project is geselecteerd in het vak **Versie(s)**. Klik op **Installeren** en accepteer de gebruiksvoorwaarden.

    ![][3]

3. Dubbelklik in Solution Explorer op het bestand Program.cs om het in de editor te openen, als het bestand nog niet was geopend.

4. Voeg boven aan het bestand de volgende instructies toe:

    ```
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Wijzig de standaardnaam van de naamruimte (**EchoService**) naar **Microsoft.ServiceBus.Samples**.

    >[AZURE.IMPORTANT] In deze zelfstudie wordt gebruikgemaakt van de C#-naamruimte **Microsoft.ServiceBus.Samples**. Dit is de naamruimte van het via het contract beheerde type dat wordt gebruikt in het configuratiebestand in de stap [De WCF-client configureren](#configure-the-wcf-client). U kunt bij het maken van dit voorbeeld elke gewenste naamruimte opgeven. Voor de zelfstudie moet u echter wel de naamruimten van het contract en de service naar behoren wijzigen in het configuratiebestand van de toepassing. De opgegeven naamruimte in het bestand App.config moet gelijk zijn aan de opgegeven naamruimte in de C#-bestanden.

1. Direct na de `Microsoft.ServiceBus.Samples`-naamruimtedeclaratie, maar in de naamruimte, definieert u een nieuwe interface met de naam `IEchoContract`. Pas het kenmerk `ServiceContractAttribute` toe op de interface met een naamruimtewaarde van **http://samples.microsoft.com/ServiceModel/Relay/**. De naamruimtewaarde verschilt van de naamruimte die u in uw code gebruikt. In plaats daarvan wordt de naamruimtewaarde gebruikt als een unieke id voor dit contract. Door de naamruimte expliciet op te geven, wordt voorkomen dat de standaardnaamruimtewaarde wordt toegevoegd aan de naam van het contract.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    >[AZURE.NOTE] De naamruimte van het servicecontract bevat meestal een schematische naam die ook versie-informatie bevat. Als u versie-informatie in de naamruimte van het servicecontract gebruikt, kunnen services grote wijzigingen isoleren door een nieuw servicecontract te definiëren met een nieuwe naamruimte en het beschikbaar te maken op een nieuw eindpunt. Op die manier kunnen clients het oude servicecontract blijven gebruiken zonder dat het hoeft te worden bijgewerkt. De versie-informatie kan bestaan uit een datum of een buildnummer. Zie [Serviceversiebeheer](http://go.microsoft.com/fwlink/?LinkID=180498) voor meer informatie. Voor deze zelfstudie bevat de naam van de servicecontractnaamruimte geen versie-informatie.

1. Declareer in de `IEchoContract`-interface een methode voor de enkelvoudige bewerking die door het `IEchoContract`-contract wordt weergegeven in de interface en pas het kenmerk `OperationContractAttribute` toe op de methode die u wilt weergeven als onderdeel van het openbare Service Bus-contract.

    ```
    [OperationContract]
    string Echo(string text);
    ```

1. Direct na het definiëren van de `IEchoContract`-interface declareert u een kanaal dat eigenschappen overneemt van de interfaces `IEchoContract` en `IClientChannel`. Ga als volgt te werk:

    ```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Een kanaal is het WCF-object waarmee de host en de client informatie aan elkaar doorgeven. Later schrijft u code voor het kanaal om informatie als echo te verzenden tussen de twee toepassingen.

1. Klik in het menu **Bouwen ** op **Oplossing opbouwen** of druk op **Ctrl + Shift + B** om de juistheid van uw werk tot nu toe te controleren.

### Voorbeeld

Met de volgende code wordt een eenvoudige interface weergegeven waarmee een Service Bus-contract wordt gedefinieerd.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Nu de interface is gemaakt, kunt u de interface implementeren.

## Het WCF-contract implementeren om Service Bus te gebruiken

Als u een Service Bus Relay wilt maken, moet u eerst het contract maken. Dit wordt gedefinieerd door middel van een interface. Zie de vorige stap voor meer informatie over het maken van de interface. De volgende stap is het implementeren van de interface. Hiervoor moet u een klasse maken met de naam `EchoService` die de door de gebruiker gedefinieerde `IEchoContract`-interface implementeert. Nadat de interface is geïmplementeerd, configureert u de interface met een App.config-configuratiebestand. Het configuratiebestand bevat gegevens die de toepassing nodig heeft, zoals de naam van de service, de naam van het contract en het type protocol dat wordt gebruikt om te communiceren met Service Bus. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt. Zie [Servicecontracten implementeren](https://msdn.microsoft.com/library/ms733764.aspx) in de WCF-documentatie voor algemene informatie over het implementeren van een servicecontract.

1. Maak een nieuwe klasse met de naam `EchoService` direct nadat de `IEchoContract`-interface is gedefinieerd. Met de klasse `EchoService` wordt de `IEchoContract`-interface geïmplementeerd. 

    ```
    class EchoService : IEchoContract
    {
    }
    ```
    
    Net als bij andere interface-implementaties kunt u de definitie implementeren in een ander bestand. In deze zelfstudie staat de implementatie echter in hetzelfde bestand als de interfacedefinitie en de `Main`-methode.

1. Pas het kenmerk [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) toe op de `IEchoContract`-interface. Aan het kenmerk ziet u wat de servicenaam en naamruimte zijn. Wanneer u dit hebt gedaan, wordt de klasse `EchoService` als volgt weergegeven:

    ```
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implementeer de `Echo`-methode die is gedefinieerd in de interface `IEchoContract` in de klasse `EchoService`. 

    ```
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Klik op **Bouwen** en vervolgens op **Oplossing opbouwen** om de juistheid van uw werk te controleren.

### De configuratie voor de servicehost definiëren

1. Het configuratiebestand lijkt erg op een WCF-configuratiebestand. Het bevat de servicenaam, het eindpunt (dat wil zeggen, de locatie die Service Bus biedt voor de communicatie tussen clients en hosts) en de binding (het type protocol dat wordt gebruikt om te communiceren). Het belangrijkste verschil is dat het geconfigureerde service-eindpunt verwijst naar een [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx)-binding die geen deel uitmaakt van het .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) is één van de bindingen die is gedefinieerd door Service Bus.

1. Dubbelklik in **Solution Explorer** op het bestand App.config om dit te openen in de Visual Studio-editor.

2. Vervang in het element `<appSettings>` de tijdelijke aanduidingen door de naam van uw servicenaamruimte en de SAS-sleutel die u in een eerdere stap hebt gekopieerd. 

1. In de `<system.serviceModel>`-tags voegt u een `<services>`-element toe. U kunt meerdere Service Bus-toepassingen definiëren in een enkel configuratiebestand. In deze zelfstudie wordt er echter maar één gedefinieerd.
 
    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Voeg in het `<services>`-element een `<service>`-element toe om de naam van de service te definiëren.

    ```
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Geef in het `<service>`-element de locatie op van het eindpuntcontract en het type binding voor het eindpunt.

    ```
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Het eindpunt definieert waar de client zoekt naar de hosttoepassing. Later in de zelfstudie wordt deze stap gebruikt om een URI te maken waarmee de host volledig beschikbaar wordt gemaakt via Service Bus. Aan de hand van de binding weet u dat TCP wordt gebruikt als protocol voor communicatie met Service Bus.

1. Klik in het menu **Bouwen** op **Oplossing opbouwen** om de juistheid van uw werk te controleren.

### Voorbeeld

In de volgende code staat de implementatie van het servicecontract.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

In de volgende code staat de basisindeling van het aan de servicehost gekoppelde bestand App.config.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## Een eenvoudige webservice voor registratie bij Service Bus hosten en uitvoeren

In deze stap wordt beschreven hoe u een eenvoudige Service Bus-service uitvoert.

### Service Bus-referenties maken

1. Maak in `Main()` twee variabelen waarin de naamruimte en de SAS-sleutel uit het consolevenster worden opgeslagen.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    De SAS-sleutel wordt later gebruikt voor toegang tot uw Service Bus-project. De naamruimte wordt als parameter doorgegeven aan `CreateServiceUri` voor het maken van een service-URI.

4. Met een [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx)-object geeft u aan dat u een SAS-sleutel gaat gebruiken als referentietype. Voeg de volgende code toe direct na de code die u in de vorige stap hebt toegevoegd.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### Een basisadres voor de service maken

1. Na de code die u hebt toegevoegd in de vorige stap, maakt u een `Uri`-exemplaar voor het basisadres van de service. Deze URI bevat het Service Bus-schema, de naamruimte en het pad naar de service-interface.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

    'sb' staat voor het Service Bus-schema en geeft aan dat TCP als protocol wordt gebruikt. Dit is ook eerder aangegeven in het configuratiebestand, toen [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) is opgegeven als binding.
    
    De URI voor deze zelfstudie is `sb://putServiceNamespaceHere.windows.net/EchoService`.

### De servicehost maken en configureren

1. Stel de connectiviteitsmodus in op `AutoDetect`.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    In de connectiviteitsmodus wordt het protocol beschreven dat de service gebruikt om te communiceren met Service Bus: HTTP of TCP. Met de standaardinstelling `AutoDetect` probeert de service via TCP verbinding te maken als deze optie beschikbaar is. HTTP wordt gebruikt als TCP niet beschikbaar is. Dit wijkt af van het protocol dat de service opgeeft voor clientcommunicatie. Dat protocol wordt bepaald op basis van de gebruikte binding. Een service kan bijvoorbeeld gebruikmaken van de binding [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx), wat betekent dat het eindpunt (beschikbaar gemaakt via Service Bus) met clients communiceert via HTTP. Voor diezelfde service zou **ConnectivityMode.AutoDetect** kunnen worden opgeven zodat de service via TCP met Service Bus communiceert.

1. Maak de servicehost met de URI die u eerder in dit gedeelte hebt gemaakt.

    ```
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    De servicehost is het WCF-object dat de service start. Hier geeft u aan welk type service u wilt maken (een `EchoService`-type) en het adres waarmee u de service beschikbaar wilt maken.

1. Boven aan het bestand Program.cs voegt u verwijzingen toe naar [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) en [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

    ```
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Configureer vervolgens in `Main()` het eindpunt voor openbare toegang.

    ```
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Met deze stap laat u Service Bus weten dat uw toepassing openbaar te vinden is door de Service Bus-ATOM-feed voor uw project te doorzoeken. Als u **DiscoveryType** instelt op **private**, hebben clients nog steeds toegang tot de service. De service wordt echter niet weergegeven wanneer er wordt gezocht naar de Service Bus-naamruimte. In plaats daarvan moet de client op voorhand al op de hoogte zijn van het eindpuntpad.

1. Pas de servicereferenties toe op de service-eindpunten die zijn opgegeven in het bestand App.config:

    ```
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Zoals vermeld in de vorige stap, kan het zijn dat u meerdere services en eindpunten hebt opgegeven in het configuratiebestand. Als dit het geval is, is met de code het hele configuratiebestand doorzocht naar eindpunten waarop uw referenties kunnen worden toegepast. Voor deze zelfstudie heeft het configuratiebestand echter slechts één eindpunt.

### De servicehost openen

1. Open de service.

    ```
    host.Open();
    ```

1. Stel de gebruiker ervan op de hoogte dat de service wordt uitgevoerd en leg uit hoe de service kan worden afgesloten.

    ```
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Sluit de servicehost wanneer u klaar bent.

    ```
    host.Close();
    ```

1. Druk op **Ctrl + Shift + B** om het project op te bouwen.

### Voorbeeld

Het volgende voorbeeld bevat het servicecontract en de implementatie uit de vorige stappen in de zelfstudie. Hierin wordt de service gehost in een consoletoepassing. Maak van het volgende een uitvoerbaar bestand met de naam EchoService.exe.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## Een WCF-client maken voor het servicecontract

In de volgende stap maakt u een eenvoudige Service Bus-clienttoepassing en definieert u het servicecontract dat u in latere stappen gaat implementeren. Veel van deze stappen lijken op de stappen voor het maken van een service: een contract opgeven, een App.config-bestand bewerken, referenties gebruiken om verbinding te maken met Service Bus, enzovoort. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

1. Maak een nieuw project in de huidige Visual Studio-oplossing voor de client. Dit doet u als volgt:
    1. Klik in Solution Explorer, in de oplossing die de service bevat, met de rechtermuisknop op de huidige oplossing (niet op het project). Klik vervolgens op **Toevoegen**. Klik vervolgens op **Nieuw project**.
    2. Klik in het dialoogvenster **Nieuw project toevoegen** op **Visual C#** (kijk bij **Andere talen** als **Visual C#** niet wordt weergegeven), selecteer het sjabloon **Consoletoepassing** en geef het de naam **EchoClient**.
    3. Klik op **OK**.
<br />

1. Dubbelklik in Solution Explorer op het bestand Program.cs in het project **EchoClient** om het in de editor te openen, als het bestand nog niet was geopend.

1. Verander de naamruimtenaam van de standaardnaam van `EchoClient` in `Microsoft.ServiceBus.Samples`.

1. Installeer het [Service Bus-pakket NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Klik in Solution Explorer met de rechtermuisknop op het project **EchoClient** en klik vervolgens op **NuGet-pakketten beheren**. Klik op het tabblad **Bladeren** en zoek vervolgens naar `Microsoft Azure Service Bus`. Klik op **Installeren** en accepteer de gebruiksvoorwaarden.

    ![][3]

1. Voeg een `using`-instructie toe voor de naamruimte [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) in het bestand Program.cs. 

    ```
    using System.ServiceModel;
    ```

1. Voeg de definitie van het servicecontract toe aan de naamruimte, zoals wordt weergegeven in het volgende voorbeeld. Deze definitie is identiek aan de definitie die is gebruikt in het project **Service**. U moet deze code toevoegen aan de bovenkant van de `Microsoft.ServiceBus.Samples`-naamruimte.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Druk op **Ctrl + Shift + B** om de client op te bouwen.

### Voorbeeld

In de volgende code ziet u de huidige status van het bestand Program.cs in het project EchoClient.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## De WCF-client configureren

In deze stap maakt u een App.config-bestand voor een eenvoudige clienttoepassing die de service gebruikt die u eerder in deze zelfstudie hebt gemaakt. Dit App.config-bestand definieert het contract, de binding en de naam van het eindpunt. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

1. Dubbelklik in Solution Explorer in het project **EchoClient** op het bestand **App.config** om dit te openen in de Visual Studio-editor.

2. Vervang in het element `<appSettings>` de tijdelijke aanduidingen door de naam van uw servicenaamruimte en de SAS-sleutel die u in een eerdere stap hebt gekopieerd.

1. Voeg in het element system.serviceModel een `<client>`-element toe.

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Tijdens deze stap wordt aangegeven dat u een clienttoepassing van het type WCF maakt.

1. Geef in het `client`-element de naam, het contract en het type binding op voor het eindpunt.

    ```
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Tijdens deze stap wordt de naam van het eindpunt gedefinieerd, wordt het contract van de service gedefinieerd en wordt gedefinieerd dat de clienttoepassing gebruikmaakt van TCP om met Service Bus te communiceren. De naam van het eindpunt wordt in de volgende stap gebruikt om deze eindpuntconfiguratie te koppelen aan de service-URI.

1. Klik op **Bestand** en vervolgens op **Alles opslaan**.

## Voorbeeld

In de volgende code staat het App.config-bestand voor de Echo-client.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## De WCF-client implementeren om Service Bus aan te roepen

In deze stap implementeert u een eenvoudige clienttoepassing die de service gebruikt die u eerder in deze zelfstudie hebt gemaakt. De service en client voeren veel van dezelfde bewerkingen uit om Service Bus te openen:

1. De connectiviteitsmodus wordt ingesteld.
1. De URI wordt gemaakt waarmee de hostservice wordt gezocht.
1. De beveiligingsreferenties worden gedefinieerd.
1. De referenties worden toegepast op de verbinding.
1. De verbinding wordt geopend.
1. De toepassingsspecifieke taken worden uitgevoerd.
1. De verbinding wordt gesloten.

Een van de belangrijkste verschillen is echter dat de clienttoepassing een kanaal gebruikt om verbinding te maken met Service Bus, terwijl de service **ServiceHost** aanroept. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

### Een clienttoepassing implementeren

1. Stel de connectiviteitsmodus in op **AutoDetect**. Voeg de volgende code toe in de `Main()`-methode van de **EchoClient**-toepassing.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Geef variabelen op voor de waarden van de naamruimte van de service. Geef ook de SAS-sleutel op die in de console wordt weergegeven.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Maak de URI die de locatie definieert van de host in uw Service Bus-project.

    ```
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Maak het referentieobject voor het eindpunt van uw servicenaamruimte.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Maak de kanaalfactory die de configuratie laadt die wordt beschreven in het App.config-bestand.

    ```
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Een kanaalfactory is een WCF-object dat een kanaal maakt waardoor de service- en clienttoepassingen communiceren.

1. Pas de Service Bus-referenties toe.

    ```
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Maak en open het kanaal naar de service.

    ```
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Schrijf de algemene gebruikersinterface en de functionaliteit voor de echo.

    ```
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Voor de code wordt het exemplaar van het kanaalobject gebruikt als proxy voor de service.

1. Sluit het kanaal en sluit de factory.

    ```
    channel.Close();
    channelFactory.Close();
    ```

## De toepassingen uitvoeren

1. Druk op **Ctrl + Shift + B** om de oplossing op te bouwen. Hiermee worden zowel het clientproject als het serviceproject opgebouwd die u in de vorige stappen hebt gemaakt.

2. Voordat u de clienttoepassing uitvoert, moet u ervoor zorgen dat de servicetoepassing wordt uitgevoerd. Klik in Visual Studio Solution Explorer met de rechtermuisknop op de oplossing **EchoService**. Klik vervolgens op **Eigenschappen**.

3. Klik in het dialoogvenster Oplossingseigenschappen op **Opstartproject** en vervolgens op de knop **Meerdere opstartprojecten**. Controleer of **EchoService** bovenaan staat in de lijst. 

4. Stel het vak **Actie** van de projecten **EchoService** en **EchoClient** in op **Start**.

    ![][5]

5. Klik op **Projectafhankelijkheden**. Selecteer in het vak **Projecten** de optie **EchoClient**. Zorg dat in het vak **Is afhankelijk van** **EchoService** is geselecteerd.

    ![][6]

6. Klik op **OK** om het dialoogvenster **Eigenschappen** te sluiten.

7. Druk op **F5** om beide projecten uit te voeren.

8. Beide consolevensters worden geopend en u wordt gevraagd de naam van de naamruimte op te geven. De service moet eerst worden uitgevoerd. Voer daarom in het **EchoService**-consolevenster de naam van de naamruimte in en druk vervolgens op **Enter**.

9. U wordt vervolgens nogmaals om uw SAS-sleutel gevraagd. Voer de SAS-sleutel in en druk op ENTER.

    Hier volgt een voorbeeld van de uitvoer van het consolevenster. De waarden die hier worden weergegeven, dienen slechts als voorbeeld.

    `Your Service Namespace: myNamespace`
    `Your SAS Key: <SAS key value>`

    De servicetoepassing geeft in het consolevenster het adres weer dat wordt gebruikt, zoals in het volgende voorbeeld te zien is.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`
    `Press [Enter] to exit`
    
10. Voer in het consolevenster **EchoClient** dezelfde gegevens in als de gegevens die u eerder al hebt ingevoerd voor de servicetoepassing. Volg de vorige stappen om dezelfde servicenaamruimte en SAS-sleutel in te voeren voor de clienttoepassing.

11. Wanneer u deze waarden hebt ingevoerd, opent de client een kanaal naar de service en wordt u gevraagd om tekst in te voeren, zoals te zien is in het volgende console-uitvoervoorbeeld.

    `Enter text to echo (or [Enter] to exit):` 

    Voer tekst in om naar de servicetoepassing te verzenden en druk op Enter. Deze tekst wordt naar de service verzonden via een Echo-servicebewerking en wordt in het serviceconsolevenster weergegeven, zoals in de volgende voorbeelduitvoer te zien is.

    `Echoing: My sample text`

    De clienttoepassing ontvangt de geretourneerde waarde van de `Echo`-bewerking: de oorspronkelijke tekst. Deze wordt vervolgens weergegeven in het consolevenster. Hier volgt een voorbeeld van de uitvoer van het clientconsolevenster.

    `Server echoed: My sample text`

12. U kunt op deze manier doorgaan met het verzenden van berichten van de client naar de service. Wanneer u klaar bent, drukt u op Enter in de client- en serviceconsolevensters om beide toepassingen te sluiten.

## Voorbeeld

In het volgende voorbeeld ziet u hoe u een clienttoepassing kunt maken, hoe u de bewerkingen van de service aanroept en hoe u de client sluit nadat het aanroepen van de bewerkingen is voltooid.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Service Bus-clienttoepassing en -service maakt met de Service Bus Relay-mogelijkheden. Zie de [Service Bus Brokered Messaging .NET-zelfstudie](service-bus-brokered-tutorial-dotnet.md) voor een vergelijkbare zelfstudie waarin gebruik wordt gemaakt van Service Bus [Brokered Messaging](service-bus-messaging-overview.md#Brokered-messaging).

Zie de volgende onderwerpen voor meer informatie over Service Bus.

- [Overzicht van Service Bus-berichten](service-bus-messaging-overview.md)
- [Grondbeginselen van Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus-architectuur](service-bus-architecture.md)

[klassieke Azure-portal]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png



<!--HONumber=Jun16_HO2-->


