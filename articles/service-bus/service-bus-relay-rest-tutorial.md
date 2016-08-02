<properties
    pageTitle="Zelfstudie Service Bus REST met Relayed Messaging | Microsoft Azure"
    description="Een eenvoudige Service Bus Relay-hosttoepassing ontwikkelen met een op REST gebaseerde interface."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/03/2016"
    ms.author="sethm" />

# Zelfstudie Service Bus REST

In deze zelfstudie wordt beschreven hoe u een eenvoudige Service Bus-hosttoepassing bouwt met een op REST gebaseerde interface. Met REST kan een webclient, zoals een webbrowser, toegang krijgen tot de Service Bus-API's via HTTP-aanvragen.

In deze zelfstudie wordt het WCF (Windows Communication Foundation) REST-programmeermodel gebruikt om een REST-service in Service Bus te construeren. Zie [WCF REST Programming Model](https://msdn.microsoft.com/library/bb412169.aspx) (WCF REST-programmeermodel) en [Designing and Implementing Services](https://msdn.microsoft.com/library/ms729746.aspx) (Services ontwerpen en implementeren) in de WCF-documentatie voor meer informatie.

## Stap 1: een Service Bus-naamruimte maken

De eerste stap is het maken van een naamruimte en ophalen van een SAS-sleutel (Shared Access Signature). Een naamruimte biedt een toepassingsbegrenzing voor elke toepassing die toegankelijk is via Service Bus. Een SAS-sleutel wordt automatisch door het systeem gegenereerd wanneer een servicenaamruimte wordt gemaakt. De combinatie van naamruimte en SAS-sleutel biedt Service Bus de benodigde referenties voor het verifiëren van toegang tot een toepassing.

1. Voor het maken van een servicenaamruimte gaat u naar de [klassieke Azure-portal][]. Klik op **Service Bus** aan de linkerkant en klik op **Maken**. Voer een naam voor de naamruimte in en klik vervolgens op het vinkje.

2. Klik in het hoofdvenster van de [klassieke Azure-portal][] op de naam van de naamruimte die u in de vorige stap hebt gemaakt.

3. Klik op het tabblad **Configureren**.

4. Noteer in het gedeelte **sleutel genereren voor gedeelde toegang** de **Primaire sleutel** die is gekoppeld aan het beleid **RootManageSharedAccessKey**, of kopieer deze sleutel naar het klembord. U gebruikt deze waarde verderop in deze zelfstudie.

## Stap 2: een op REST gebaseerd WCF-servicecontract voor gebruik met Service Bus definiëren

Net zoals bij andere Service Bus-services, moet u het contract definiëren wanneer u een service in REST-stijl maakt. Het contract geeft aan welke bewerkingen door de host worden ondersteund. Een servicebewerking kan worden beschouwd als een webservicemethode. Contracten worden gemaakt door een C++-, C#- of Visual Basic-interface te definiëren. Elke methode in de interface komt overeen met een specifieke servicebewerking. Op elke interface moet het kenmerk [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) worden toegepast en op elke bewerking moet het kenmerk [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) worden toegepast. Als een methode in een interface met het kenmerk [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) niet beschikt over het kenmerk [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), wordt die methode niet weergegeven. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

Het belangrijkste verschil tussen een Basic Service Bus-contract en een REST-stijlcontract is de toevoeging van een eigenschap aan het kenmerk [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Met deze eigenschap kunt u een methode in uw interface toewijzen aan een methode aan de andere kant van de interface. In dit geval wordt [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) gebruikt om een methode te koppelen aan HTTP GET. Hierdoor kan Service Bus opdrachten die naar de interface worden verzonden, correct ophalen en interpreteren.

### Een Service Bus-contract met een interface maken

1. Open Visual Studio als beheerder: klik met de rechtermuisknop op het programma in het menu **Start** en klik vervolgens op **Als administrator uitvoeren**.

2. Maak een nieuw consoletoepassingsproject. Klik op het menu **Bestand**, selecteer **Nieuw** en selecteer vervolgens **Project**. Klik in het dialoogvenster **Nieuw project** op **Visual C#**, selecteer de sjabloon **Consoletoepassing** en geef deze de naam **ImageListener**. Gebruik de standaardwaarde voor **Locatie**. Klik op **OK** om het project te maken.

3. Voor een C#-project maakt Visual Studio een `Program.cs`-bestand. Deze klasse bevat een lege `Main()`-methode, vereist voor de juiste opbouw van een consoletoepassingsproject.

4. Voeg verwijzingen naar Service Bus en **System.ServiceModel.dll** aan het project toe door het Service Bus NuGet-pakket te installeren. Met dit pakket worden automatisch verwijzingen naar de Service Bus-bibliotheken en naar het **System.ServiceModel** van WCF toegevoegd. Klik in Solution Explorer met de rechtermuisknop op het project **ImageListener** en klik vervolgens op **NuGet-pakketten beheren**. Klik op het tabblad **Bladeren** en zoek vervolgens naar `Microsoft Azure Service Bus`. Klik op **Installeren** en accepteer de gebruiksvoorwaarden.

5. U moet expliciet een verwijzing naar **System.ServiceModel.Web.dll** toevoegen aan het project:

    a. Klik in Solution Explorer met de rechtermuisknop op de map **Verwijzingen** in de projectmap en klik vervolgens op **Verwijzing toevoegen**.

    b. Klik in het dialoogvenster **Verwijzing toevoegen** op het tabblad **Framework** aan de linkerkant en typ **System.ServiceModel.Web** in het vak **Zoeken**. Schakel het selectievakje **System.ServiceModel.Web** in en klik vervolgens op **OK**.

6. Voeg aan het begin van het bestand Program.cs de volgende `using`-instructies toe:

    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) is de naamruimte die programmatisch toegang biedt tot de basisfuncties van WCF. Service Bus maakt gebruik van veel van de objecten en kenmerken van WCF om servicecontracten te definiëren. U gebruikt deze naamruimte in de meeste van uw Service Bus Relay-toepassingen. Op ongeveer dezelfde manier helpt [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) bij het definiëren van het kanaal. Dit is het object dat u gebruikt voor communicatie met Service Bus en de clientwebbrowser. Tot slot bevat [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) de typen waarmee u webtoepassingen kunt maken.

7. Wijzig de naam van de naamruimte `ImageListener` in **Microsoft.ServiceBus.Samples**.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

8. Definieer meteen na de openingsaccolade van de naamruimtedeclaratie een nieuwe interface met de naam **IImageContract** en pas het kenmerk **ServiceContractAttribute** met de waarde `http://samples.microsoft.com/ServiceModel/Relay/` toe op de interface. De naamruimtewaarde verschilt van de naamruimte die u in uw code gebruikt. De naamruimtewaarde wordt gebruikt als een unieke id voor dit contract en moet versie-informatie bevatten. Zie [Serviceversiebeheer](http://go.microsoft.com/fwlink/?LinkID=180498) voor meer informatie. Door de naamruimte expliciet op te geven, wordt voorkomen dat de standaardnaamruimtewaarde wordt toegevoegd aan de naam van het contract.

    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

9. Declareer in de `IImageContract`-interface een methode voor de enkelvoudige bewerking die door het `IImageContract`-contract wordt weergegeven in de interface en pas het kenmerk `OperationContractAttribute` toe op de methode die u wilt weergeven als onderdeel van het openbare Service Bus-contract.

    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

10. Voeg de waarde **WebGet** toe aan het kenmerk **OperationContract**.

    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

    Dit stelt Service Bus in staat om HTTP GET-aanvragen te routeren naar `GetImage` en de retourwaarden van `GetImage` om te zetten in een HTTP GETRESPONSE-antwoord. Verderop in de zelfstudie gebruikt u een webbrowser om toegang te krijgen tot deze methode en de installatiekopie weer te geven in de browser.

11. Declareer direct na de `IImageContract`-definitie een kanaal dat de eigenschappen overneemt van de `IImageContract`- en `IClientChannel`-interface.

    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

    Een kanaal is het WCF-object waarmee de service en de client informatie aan elkaar doorgeven. U maakt het kanaal later in uw hosttoepassing. Service Bus gebruikt dit kanaal om de HTTP GET-aanvragen van uw browser aan uw **GetImage**-implementatie door te geven. Service Bus gebruikt dit kanaal ook om de retourwaarde **GetImage** om te zetten in een HTTP GETRESPONSE voor de clientbrowser.

12. Klik in het menu **Bouwen** op **Oplossing opbouwen** om de juistheid van uw werk tot nu toe te controleren.

### Voorbeeld

Met de volgende code wordt een eenvoudige interface weergegeven waarmee een Service Bus-contract wordt gedefinieerd.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## Stap 3: een op REST gebaseerd WCF-servicecontract voor gebruik met Service Bus implementeren

Als u een Service Bus-service in REST-stijl wilt maken, moet u eerst het contract maken. Dit wordt gedefinieerd door middel van een interface. De volgende stap is het implementeren van de interface. Hiervoor moet u een klasse met de naam **ImageService** maken die de door de gebruiker gedefinieerde **IImageContract**-interface implementeert. Nadat het contract is geïmplementeerd, configureert u de interface met een App.config-bestand. Het configuratiebestand bevat gegevens die de toepassing nodig heeft, zoals de naam van de service, de naam van het contract en het type protocol dat wordt gebruikt om te communiceren met Service Bus. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

Net als bij de vorige stappen, is er weinig verschil tussen het implementeren van een REST-stijlcontract en een Basic Service Bus-contract.

### Een Service Bus-contract in REST-stijl implementeren

1. Maak een nieuwe klasse met de naam **ImageService** direct na de definitie van de **IImageContract**-interface. Met de klasse **ImageService** wordt de **IImageContract**-interface geïmplementeerd.

    ```
    class ImageService : IImageContract
    {
    }
    ```
    Net als bij andere interface-implementaties kunt u de definitie implementeren in een ander bestand. In deze zelfstudie wordt de implementatie echter weergegeven in hetzelfde bestand als de interfacedefinitie en de `Main()`-methode.

2. Pas het kenmerk [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) op de klasse **IImageService** toe om aan te geven dat de klasse een implementatie is van een WCF-contract.

    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Zoals eerder is vermeld, is deze naamruimte geen traditionele naamruimte, maar maakt deze deel uit van de WCF-architectuur waarmee het contract wordt geïdentificeerd. Zie het onderwerp [Data Contract Names](https://msdn.microsoft.com/library/ms731045.aspx) (Gegevenscontractnamen) in de WCF-documentatie voor meer informatie.

3. Voeg een JPG-afbeelding toe aan uw project.  

    Dit is een afbeelding die de service in de ontvangende browser weergeeft. Klik met de rechtermuisknop op het project en klik op **Toevoegen**. Klik vervolgens op **Bestaand item**. Gebruik het dialoogvenster **Bestaand item toevoegen** om te bladeren naar een geschikt JPG-bestand en klik vervolgens op **Toevoegen**.

    Als u het bestand toevoegt, moet u ervoor zorgen dat **Alle bestanden** is geselecteerd in de vervolgkeuzelijst naast het veld **Bestandsnaam:**. In de rest van deze zelfstudie wordt ervan uitgegaan dat de naam van de afbeelding 'image.jpg' is. Als u een ander bestand gebruikt, moet u de naam van de afbeelding wijzigen of moet u uw code wijzigen.

4. Klik in **Solution Explorer** met de rechtermuisknop op het afbeeldingsbestand en klik op **Eigenschappen** om te controleren of de service die wordt uitgevoerd het affbeeldingsbestand kan vinden. Stel **Naar uitvoermap kopiëren** in het deelvenster **Eigenschappen** in op **Kopiëren indien nieuwer**.

5. Voeg een verwijzing naar de **System.Drawing.dll**-assembly toe aan het project en voeg tevens de volgende gekoppelde `using`-instructies toe.  

    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

6. Voeg in de klasse **ImageService** de volgende constructor toe waarmee de bitmap wordt geladen en voorbereid op verzending naar de clientbrowser.

    ```
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

7. Voeg direct na de vorige code de volgende **GetImage**-methode toe aan de klasse **ImageService** om een HTTP-bericht met de afbeelding te retourneren.

    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);

        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

        return stream;
    }
    ```

    Deze implementatie maakt gebruik van **MemoryStream** om de afbeelding op te halen en deze voor te bereiden op streaming naar de browser. De streampositie start bij nul, de stream wordt gedeclareerd als JPEG en de gegevens worden gestreamd.

8. Klik in het menu **Bouwen** op **Oplossing opbouwen**.

### De configuratie voor het uitvoeren van de webservice in Service Bus definiëren

1. Dubbelklik in **Solution Explorer** op het bestand **App.config** om dit te openen in de Visual Studio-editor.

    Het bestand **App.config** lijkt op een WCF-configuratiebestand en bevat de servicenaam, het eindpunt (dat wil zeggen, de locatie die Service Bus biedt voor de communicatie tussen clients en hosts) en de binding (het type protocol dat wordt gebruikt om te communiceren). Hier is het belangrijkste verschil dat het geconfigureerde service-eindpunt verwijst naar een [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx)-binding die geen deel uitmaakt van het .NET Framework.

2. Het `<system.serviceModel>` XML-element is een WCF-element waarmee een of meer services worden gedefinieerd. Hier wordt het gebruikt om de servicenaam en het eindpunt te definiëren. Voeg onder aan het `<system.serviceModel>`-element (maar nog wel binnen `<system.serviceModel>`) een `<bindings>`-element toe met de volgende inhoud. Hiermee definieert u de bindingen die in de toepassing worden gebruikt. U kunt meerdere bindingen definiëren, maar in deze zelfstudie definieert u er slechts één.

    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    In deze stap definieert u een [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx)-binding voor Service Bus waarbij **relayClientAuthenticationType** is ingesteld op **Geen**. Met deze instelling geeft u aan dat voor een eindpunt dat deze binding gebruikt, geen clientreferentie is vereist.

3. Na het `<bindings>`-element voegt u een `<services>`-element toe. Net zoals bij bindingen kunt u meerdere services definiëren in een enkel configuratiebestand. In deze zelfstudie definieert u slechts een service.

    ```
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    In deze stap configureert u een service die de vooraf gedefinieerde **webHttpRelayBinding**-binding gebruikt. Deze service gebruikt ook de standaard **sbTokenProvider**, die in de volgende stap wordt gedefinieerd.

4. Na het `<services>`-element maakt u een `<behaviors>`-element met de volgende inhoud en wordt de 'SAS_KEY' vervangen door de *Shared Access Signature*-sleutel (SAS) die u in stap 1 hebt verkregen via de [klassieke Azure-portal][].

    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

5. Terwijl App.config actief is, vervangt u in het `<appSettings>`-element de hele verbindingsreekswaarde door de verbindingsreeks die u eerder hebt verkregen via de portal. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

6. Klik in het menu **Bouwen** op **Oplossing opbouwen** om de volledige oplossing op te bouwen.

### Voorbeeld

De volgende code toont het contract en de service-implementatie voor een op REST gebaseerde service die in Service Bus wordt uitgevoerd met de binding **WebHttpRelayBinding**.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

In het volgende voorbeeld wordt het aan de service gekoppelde bestand App.config weergegeven.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## Stap 4: de WCF-service op basis van REST hosten voor gebruik van Service Bus

In deze stap wordt beschreven hoe u een webservice uitvoert met een consoletoepassing in Service Bus. Een volledig overzicht van de code die in deze stap wordt geschreven, vindt u in het voorbeeld na de procedure.

### Een basisadres voor de service maken

1. In de declaratie van de functie `Main()` maakt u een variabele om de naamruimte van uw Service Bus-project op te slaan. Vervang `yourNamespace` door de naam van de servicenaamruimte die u eerder hebt gemaakt.

    ```
    string serviceNamespace = "yourNamespace";
    ```
    Service Bus gebruikt de naam van uw naamruimte om een unieke URI te maken.

2. Maak een `Uri`-exemplaar voor het basisadres van de service die is gebaseerd op de naamruimte.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### De webservicehost maken en configureren

- Maak de webservicehost met het URI-adres dat u eerder in dit gedeelte hebt gemaakt.

    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    De servicehost is het WCF-object waarmee de hosttoepassing wordt gestart. In dit voorbeeld wordt het type host dat u wilt maken (een **ImageService**), doorgegeven en ook het adres waarop u de hosttoepassing wilt weergeven.

### De webservicehost uitvoeren

1. Open de service.

    ```
    host.Open();
    ```
    De service wordt nu uitgevoerd.

2. Geef een bericht weer waarin wordt aangegeven dat de service wordt uitgevoerd en hoe deze kan worden gestopt.

    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

3. Sluit de servicehost wanneer u klaar bent.

    ```
    host.Close();
    ```

## Voorbeeld

Het volgende voorbeeld bevat het servicecontract en de implementatie uit de vorige stappen in de zelfstudie. Hierin wordt de service gehost in een consoletoepassing. Compileer de volgende code in een uitvoerbaar bestand met de naam ImageListener.exe.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### De code compileren

Nadat u de oplossing hebt opgebouwd, gaat u als volgt te werk om de toepassing uit te voeren:

1. Druk op **F5** of blader naar de locatie van het uitvoerbare bestand (ImageListener\bin\Debug\ImageListener.exe) om de service uit te voeren. Zorg ervoor dat de app actief blijft. Dit is vereist voor het uitvoeren van de volgende stap.

2. Kopieer en plak het adres van de opdrachtprompt in een browser om de afbeelding te zien.

3. Druk op **Enter** in het opdrachtpromptvenster om de app te sluiten wanneer u klaar bent.

## Volgende stappen

U hebt nu een toepassing ontwikkeld die gebruikmaakt van de Service Bus Relay-service. Zie de volgende artikelen voor meer informatie over Relayed Messaging:

- [Overzicht van Azure Service Bus-architectuur](service-bus-fundamentals-hybrid-solutions.md#relays)

- [De Service Bus Relay-service gebruiken](service-bus-dotnet-how-to-use-relay.md)

[klassieke Azure-portal]: http://manage.windowsazure.com


<!--HONumber=Jun16_HO2-->


