<properties
    pageTitle="Service Bus Relay gebruiken met .NET | Microsoft Azure"
    description="Meer informatie over het gebruik van de Azure Service Bus Relay-service om twee toepassingen te verbinden die worden gehost op verschillende locaties."
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


# De Azure Service Bus Relay-service gebruiken

In dit artikel wordt het gebruik van de Service Bus Relay-service beschreven. De voorbeelden zijn geschreven in C# en maken gebruik van de WCF-API (Windows Communication Foundation) met extensies die deel uitmaken van de Service Bus-assembly. Zie het overzicht [Service Bus Relayed Messaging](service-bus-relay-overview.md) voor meer informatie over Service Bus Relay.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Wat is Service Bus Relay?

U kunt met de [Service Bus *Relay*-service](service-bus-relay-overview.md) hybride toepassingen opbouwen die kunnen worden uitgevoerd in een Azure-datacenter en in uw eigen on-premises bedrijfsomgeving. Dit is mogelijk doordat u met Service Bus Relay WCF-services (Windows Communication Foundation) in een bedrijfsnetwerk veilig beschikbaar kunt stellen voor de openbare cloud zonder dat een firewallverbinding hoeft te worden geopend en zonder dat er tussenkomende wijzigingen in de infrastructuur van een bedrijfsnetwerk nodig zijn.

![Relay-concepten](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

U kunt met Service Bus Relay WCF-services hosten in uw bestaande bedrijfsomgeving. U kunt vervolgens het luisteren naar binnenkomende sessies en aanvragen voor deze WCF-services overdragen aan de Service Bus-service die actief is in Azure. Hierdoor kunt u deze services beschikbaar stellen voor toepassingscode die wordt uitgevoerd in Azure of voor mobiele werknemers of extranetpartneromgevingen. U kunt met Service Bus heel nauwkeurig veilig bepalen wie toegang heeft tot deze services. Service Bus biedt een krachtige en veilige manier om toepassingsfuncties en -gegevens van uw bestaande bedrijfsoplossingen beschikbaar te maken en hiervan te profiteren vanuit de cloud.

In dit artikel wordt gedemonstreerd hoe u Service Bus Relay gebruikt om een WCF-webservice te maken die beschikbaar wordt gemaakt met een TCP-kanaalbinding waarmee een beveiligde communicatie tussen twee partijen wordt geïmplementeerd.

## Een servicenaamruimte maken

Als u Service Bus Relay in Azure wilt gebruiken, moet u eerst een naamruimte maken. Een naamruimte biedt een scoping container voor het verwerken van Service Bus-resources in uw toepassing.

Een servicenaamruimte maken:

1.  Meld u aan bij de [klassieke Azure-portal][].

2.  Klik in het linkernavigatievenster van de portal op **Service Bus**.

3.  Klik in het onderste deelvenster van de portal op **Maken**.

    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)

4.  Voer in het dialoogvenster **Een nieuwe naamruimte toevoegen** een naamruimtenaam in.
    In het systeem wordt onmiddellijk gecontroleerd of de naam beschikbaar is.

    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png)

5.  Nadat u hebt gecontroleerd of de naam voor de naamruimte beschikbaar is, kiest u het land of de regio waarin uw naamruimte moet worden gehost. Zorg er daarbij voor dat u hetzelfde land of dezelfde regio gebruikt waarin u uw rekenresources implementeert.

    > [AZURE.IMPORTANT] Kies *dezelfde regio* die u van plan bent te kiezen voor het implementeren van uw toepassing. Hiermee krijgt u de beste prestaties.

6.  Laat de standaardwaarden van de andere velden in het dialoogvenster ongewijzigd (de lagen **Berichten** en **Standard**) en klik vervolgens op het vinkje. Uw naamruimte wordt nu gemaakt en ingeschakeld. U moet wellicht enkele minuten wachten terwijl de resources voor uw account worden ingericht.

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png)

    De naamruimte die u hebt gemaakt, wordt vervolgens weergegeven in de portal en het duurt even voordat deze wordt geactiveerd. Wacht totdat de status **Actief** is voordat u doorgaat.

## De standaardbeheerreferenties voor de naamruimte verkrijgen

Als u in de nieuwe naamruimte beheerbewerkingen wilt uitvoeren, zoals het maken van een relayverbinding, moet u de SAS-autorisatieregel (Shared Access Signature) voor de naamruimte configureren. Zie [Shared Access Signature-verificatie met Service Bus][] voor meer informatie over SAS.

1.  Klik in het linkernavigatievenster op het knooppunt **Service Bus** om de lijst met beschikbare naamruimten weer te geven.
    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)

2.  Dubbelklik in de weergegeven lijst op de naam van de naamruimte die u zojuist hebt gemaakt.
    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png)

3.  Klik op het tabblad **Configureren** boven aan de pagina.

4.  Wanneer een Service Bus-naamruimte is ingericht, wordt standaard een **SharedAccessAuthorizationRule** gemaakt waarbij **KeyName** is ingesteld op **RootManageSharedAccessKey**. Op deze pagina worden die sleutel en de primaire en secundaire sleutel voor de standaardregel weergegeven.

## Het Service Bus NuGet-pakket ophalen

Het [Service Bus NuGet-pakket](https://www.nuget.org/packages/WindowsAzure.ServiceBus) is de eenvoudigste manier om de Service Bus-API op te halen en om de toepassing te configureren met alle Service Bus-afhankelijkheden. Ga als volgt te werk om het NuGet-pakket in uw toepassing te installeren:

1.  Klik in Solution Explorer met de rechtermuisknop op **Verwijzingen** en klik vervolgens op **NuGet-pakketten beheren**.
2.  Zoek Service Bus en selecteer het item **Microsoft Azure Service Bus**. Klik op **Installeren** om de installatie te voltooien en sluit het volgende dialoogvenster.

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)


## Service Bus gebruiken om een SOAP-webservice met TCP beschikbaar te maken en te verbruiken

Als u een bestaande WCF SOAP-webservice voor extern verbruik beschikbaar wilt maken, moet u wijzigingen aanbrengen aan de servicebindingen en -adressen. Hiervoor zijn mogelijk wijzigingen aan uw configuratiebestand of codewijzigingen nodig, afhankelijk van hoe u de WCF-services hebt ingesteld en geconfigureerd. Met WCF kunt u meerdere netwerkeindpunten via dezelfde service hebben, zodat u de bestaande interne eindpunten kunt behouden terwijl u tegelijk Service Bus-eindpunten voor externe toegang toevoegt.

In deze taak bouwt u een eenvoudige WCF-service op en voegt u hieraan een Service Bus-listener toe. Bij deze oefening wordt ervan uitgegaan dat u enigszins bekend bent met Visual Studio. Daarom wordt het maken van een project niet in detail behandeld. In plaats daarvan ligt de nadruk op de code.

Voordat u met de volgende stappen begint, voert u de volgende procedure uit om uw omgeving in te stellen:

1.  Maak in Visual Studio een consoletoepassing die twee projecten, Client en Service, binnen de oplossing bevat.
2.  Voeg het Microsoft Azure Service Bus NuGet-pakket aan beide projecten toe. Hiermee voegt u alle benodigde assemblyverwijzingen aan uw projecten toe.

### De service maken

Maak eerst de service zelf. Een WCF-service bestaat uit ten minste drie onderdelen:

-   Definitie van een contract waarmee wordt beschreven welke berichten worden uitgewisseld en welke bewerkingen moeten worden aangeroepen.
-   Implementatie van dit contract.
-   Host voor de WCF-service waarop een aantal eindpunten beschikbaar wordt gemaakt.

De codevoorbeelden in deze sectie hebben betrekking op elk van deze onderdelen.

Met het contract wordt één bewerking, `AddNumbers`, gedefinieerd waarmee twee getallen worden opgeteld en het resultaat wordt geretourneerd. De client kan met de `IProblemSolverChannel`-interface eenvoudig de levensduur van de proxy beheren. Het wordt aanbevolen een dergelijke interface te maken. U kunt deze contractdefinitie het beste in een afzonderlijk bestand plaatsen zodat u naar dat bestand kunt verwijzen vanuit zowel het project Client als het project Service, maar u kunt de code ook naar beide projecten kopiëren.

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

De implementatie van het contract is heel eenvoudig.

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### Een servicehost via een programma configureren

Met het geïmplementeerde contract kunt u nu de service hosten. Hosting vindt plaats in een [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx)-object waarmee exemplaren van de service worden beheerd en dat als host fungeert voor de eindpunten die naar berichten luisteren. Met de volgende code wordt de service geconfigureerd met zowel een regulier lokaal eindpunt als een Service Bus-eindpunt om de interne en externe eindpunten naast elkaar weer te geven. Vervang de tekenreeks *namespace* door de naamruimtenaam en *yourKey* door de SAS-sleutel die u in de vorige instellingsstap hebt verkregen.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "yourKey")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

In het voorbeeld maakt u twee eindpunten die deel uitmaken van dezelfde contractimplementatie. Het ene eindpunt is lokaal en het andere eindpunt is geprojecteerd via Service Bus. De belangrijkste verschillen tussen de eindpunten zijn de bindingen; [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) voor het lokale eindpunt en [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) voor het Service Bus-eindpunt en de adressen. Het lokale eindpunt heeft een lokaal netwerkadres met een afzonderlijke poort. Het Service Bus-eindpunt heeft een eindpuntadres dat bestaat uit de tekenreeks `sb`, uw naamruimtenaam en het pad 'solver'. Dit resulteert in de URI `sb://[serviceNamespace].servicebus.windows.net/solver` waarmee het service-eindpunt wordt aangeduid als een Service Bus TCP-eindpunt met een volledig gekwalificeerde externe DNS-naam. Als u de code in de `Main`-functie van de **Service**-toepassing plaatst en de tijdelijke aanduidingen vervangt zoals hierboven is uitgelegd, hebt u een functionele service. Als de service uitsluitend op Service Bus moet luisteren, verwijdert u de declaratie van het lokale eindpunt.

### Een servicehost configureren in het bestand App.config

U kunt de host ook configureren met het bestand App.config. De servicehostingcode in dit geval wordt weergegeven in het volgende voorbeeld.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

De eindpuntdefinities worden verplaatst naar het bestand App.config. Houd er rekening mee dat het pakket NuGet al een reeks definities aan het bestand App.config heeft toegevoegd die de vereiste configuratie-extensies voor Service Bus zijn. Het volgende voorbeeld, dat het exacte equivalent van de vorige code is, moet direct onder het **system.serviceModel**-element worden weergegeven. In dit voorbeeld wordt ervan uitgegaan dat uw project C#-naamruimte de naam **Service** heeft.
Vervang de tijdelijke aanduidingen door uw Service Bus-servicenaamruimte en SAS-sleutel.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Nadat u deze wijzigingen hebt aangebracht, start de service als voorheen maar met twee live eindpunten: een lokaal eindpunt en een eindpunt waarmee in de cloud wordt geluisterd.

### De client maken

#### Een client via een programma configureren

U kunt voor het verbruik van de service een WCF-client maken met een [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx)-object. Service Bus maakt gebruik van een beveiligingsmodel op basis van tokens dat wordt geïmplementeerd met SAS. De klasse [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) vertegenwoordigt een beveiligingstokenprovider met ingebouwde factorymethoden waarmee een aantal bekende tokenproviders wordt geretourneerd. In het voorbeeld hieronder wordt de methode [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) gebruikt voor het verwerken van het ophalen van het juiste SAS-token. De naam en de sleutel zijn verkregen via de portal, zoals beschreven in de vorige sectie.

Verwijs eerst naar de `IProblemSolver`-contractcode van de service in uw clientproject of kopieer deze.

Vervang vervolgens de code in de `Main`-methode van de client. Vervang ook hier de tekst van de tijdelijke aanduidingen door uw Service Bus-naamruimte en SAS-sleutel.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","yourKey") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

U kunt de client en de service nu opbouwen en uitvoeren (voer de service eerst uit). De client roept de service aan en drukt **9** af. U kunt de client en server op verschillende computers uitvoeren, zelfs in netwerken. De communicatie zal dan nog steeds goed verlopen. De clientcode kan in de cloud of lokaal worden uitgevoerd.

#### Een client configureren in het bestand App.config

De volgende code laat zien hoe u de client met het bestand App.config configureert.

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

De eindpuntdefinities worden verplaatst naar het bestand App.config. Het volgende voorbeeld met dezelfde code die eerder is weergegeven, moet direct onder het **system.serviceModel**-element worden weergegeven. Ook hier moet u weer de tijdelijke aanduidingen door uw Service Bus-naamruimte en SAS-sleutel vervangen.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## Volgende stappen

Nu u de basisprincipes van de Service Bus Relay-service hebt geleerd, volgt u deze koppelingen voor meer informatie.

- [Overzicht van Service Bus Relayed Messaging](service-bus-relay-overview.md)
- [Overzicht van Azure Service Bus-architectuur](service-bus-fundamentals-hybrid-solutions.md)
- Download Service Bus-voorbeelden van [Azure-voorbeelden][] of raadpleeg het [overzicht van Service Bus-voorbeelden][].

  [klassieke Azure-portal]: http://manage.windowsazure.com
  [Shared Access Signature-verificatie met Service Bus]: service-bus-shared-access-signature-authentication.md
  [Azure-voorbeelden]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [overzicht van Service Bus-voorbeelden]: service-bus-samples.md


<!--HONumber=Jun16_HO2-->


