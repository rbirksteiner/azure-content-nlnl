<properties 
    pageTitle="Overzicht van Azure Event Hubs | Microsoft Azure"
    description="Inleiding tot en overzicht van Azure Event Hubs."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="04/15/2016"
    ms.author="sethm" />

# Overzicht van Azure Event Hubs

Bij veel moderne oplossingen is het de bedoeling dat deze adaptieve gebruikerservaringen bieden of producten verbeteren door continue feedback en geautomatiseerde telemetrie. Dergelijke oplossingen moeten zeer grote hoeveelheden gegevens van veel gelijktijdige uitgevers veilig en betrouwbaar kunnen verwerken. Microsoft Azure Event Hubs is een beheerde platformservice en biedt een basis voor grootschalige gegevensopname in talloze scenario's. Voorbeelden van dergelijke scenario's zijn het bijhouden van het gebruik van mobiele apps, het verzamelen van informatie over gegevensverkeer van webfarms, het vastleggen van spelgebeurtenissen in consolegames of het verzamelen van telemetriegegevens van industriële machines of verbonden voertuigen. De algemene rol die Event Hubs in oplossingsarchitecturen speelt, is die van 'voordeur' van een gebeurtenispijplijn. We spreken ook wel van een *event ingestor*. Een event ingestor is een onderdeel dat of een service die zich tussen gebeurtenisuitgever en gebeurtenisconsumer bevindt en de productie van de gebeurtenisstroom loskoppelt van het gebruik van de betreffende gebeurtenissen.

![Event Hubs](./media/event-hubs-overview/IC759856.png)

Azure Event Hubs is een gebeurtenisverwerkingsservice die in de cloud zeer grote hoeveelheden gebeurtenissen en telemetriegegevens kan verzamelen, met een lage latentie en een hoge mate van betrouwbaarheid. In combinatie met andere downstreamservices is deze service bijzonder nuttig voor toepassingsinstrumentatie, verwerking van de gebruikerservaring of werkstroom en scenario's met betrekking tot het internet der dingen (IoT). Event Hubs biedt de mogelijkheid voor het verwerken van een berichtenstroom. Hoewel een Event Hub als entiteit vergelijkbaar is met wachtrijen en onderwerpen, wijken de kenmerken ervan sterk af van wat u gewend bent bij traditionele berichtenverzending voor bedrijven. Berichtenverzendingsscenario's voor bedrijven vereisen meestal diverse geavanceerde mogelijkheden, zoals sequentiëren, verwerking van onbestelbare berichten, transactieondersteuning en sterke leveringsgaranties, terwijl de belangrijkste zorg voor gebeurtenisopname hoge doorvoer en verwerkingsflexibiliteit voor gebeurtenisstromen is. De mogelijkheden van Event Hubs verschillen dan ook van de Service Bus-onderwerpen doordat ze sterk zijn gericht op scenario's met hoge doorvoer en de verwerking van gebeurtenissen. Om die reden biedt Event Hubs niet alle berichtenmogelijkheden die voor onderwerpen beschikbaar zijn. Als u deze mogelijkheden nodig hebt, blijven onderwerpen voor u de beste keuze.

Een Event Hub wordt gemaakt op het niveau van de naamruimte in de Service Bus en is vergelijkbaar met wachtrijen en onderwerpen. Event Hubs maakt gebruikt van AMQP en HTTP als primaire API-interfaces. Het volgende diagram geeft de relatie tussen Event Hubs en Service Bus weer.

![Event Hubs](./media/event-hubs-overview/IC741188.png)

## Conceptueel overzicht

Event Hubs biedt berichtstromen via een gepartitioneerd gebruikspatroon. Wachtrijen en onderwerpen maken gebruik van een model op basis van [concurrerend gebruik](https://msdn.microsoft.com/library/dn568101.aspx), waarbij elke consumer uit dezelfde wachtrij of resource probeert te lezen. Deze concurrentie voor resources resulteert uiteindelijk in complexiteit en schaallimieten voor toepassingen die de stroom verwerken. Event Hubs daarentegen maakt gebruik van een model op basis van gepartitioneerd gebruik, waarbij elke consumer slechts een specifieke subset of partitie van de berichtenstroom leest. Dit patroon maakt een horizontale schaal voor de verwerking van gebeurtenissen mogelijk en biedt andere stroomgerichte functies die niet beschikbaar zijn in wachtrijen en onderwerpen.

### Partities

Een partitie is een geordende reeks gebeurtenissen die in een Event Hub wordt bewaard. Als er nieuwere gebeurtenissen plaatsvinden, worden deze toegevoegd aan het einde van deze reeks. Een partitie kan worden beschouwd als een 'doorvoerlogboek'.

![Event Hubs](./media/event-hubs-overview/IC759857.png)

Partities bewaren gegevens voor een geconfigureerde bewaartijd die is ingesteld op het niveau van de Event Hub. Deze instelling geldt voor alle partities in de Event Hub. Gebeurtenissen verlopen op basis van tijd. U kunt ze niet expliciet verwijderen. Een Event Hub bevat meerdere partities. Elke partitie is onafhankelijk en bevat een eigen reeks gegevens. Als gevolg hiervan groeien partities vaak met verschillende snelheden.

![Event Hubs](./media/event-hubs-overview/IC759858.png)

Het aantal partities wordt opgegeven tijdens de aanmaaktijd van de Event Hub en moet tussen 2 en 32 liggen (het standaardaantal is 4). Partities zijn een mechanisme voor gegevensordening. Ze hebben meer betrekking op de mate van downstreamparallelheid die is vereist in de betrokken toepassingen, dan op de doorvoer van Event Hubs. Hierdoor houdt de keuze van het aantal partities in een Event Hub rechtstreeks verband met het aantal verwachte gelijktijdige lezers. Nadat de Event Hub is gemaakt, kan het aantal partities niet meer worden gewijzigd. Stel het benodigde aantal partities daarom vast op basis van het verwachte gebruik op de lange termijn. Als u meer dan 32 partities nodig hebt, neemt u contact op met het Service Bus-team.

Hoewel partities te herkennen zijn en gegevens er rechtstreeks naartoe kunnen worden verzonden, is het doorgaans beter om het verzenden van gegevens naar specifieke partities te voorkomen. In plaats daarvan kunt u constructies op een hoger niveau gebruiken. Deze vindt u in de secties [Gebeurtenisuitgever](#event-publisher) en [Uitgeversbeleid](#capacity-and-security).

In de context van Event Hubs worden berichten aangeduid als *gebeurtenisgegevens*. Gebeurtenisgegevens bevatten de hoofdtekst van de gebeurtenis, een door de gebruiker gedefinieerde eigenschappenverzameling en verschillende metagegevens van de gebeurtenis, zoals de offset in de partitie en het nummer in de stroomreeks. Partities worden gevuld met een reeks gebeurtenisgegevens.

## Gebeurtenisuitgever

Een *gebeurtenisuitgever* is een entiteit die gebeurtenissen of gegevens naar een Event Hub verzendt. Gebeurtenisuitgevers kunnen gebeurtenissen publiceren met HTTPS of AMQP 1.0. Gebeurtenisuitgevers gebruiken een Shared Access Signature-token (SAS) om zichzelf te identificeren bij een Event Hub en kunnen een unieke identiteit hebben of een algemene SAS-token gebruiken. Dit hangt af van de vereisten van het scenario.

Zie [Shared Access Signature-verificatie met Service Bus](../service-bus/service-bus-shared-access-signature-authentication.md) voor meer informatie over werken met SAS.

### Algemene uitgeverstaken

In deze sectie worden algemene taken voor gebeurtenisuitgevers beschreven.

#### Een SAS-token ophalen

Shared Access Signature (SAS) is het verificatiemechanisme voor Event Hubs. Service Bus biedt SAS-beleid op het niveau van de naamruimte en Event Hub. Een SAS-token wordt gegenereerd uit een SAS-sleutel en is een SHA-hash of URL. gecodeerd in een specifieke indeling. Service Bus kan de hash opnieuw genereren met de naam van de sleutel (het beleid) en de token, en op deze manier de afzender verifiëren. Normaal gesproken worden SAS-tokens voor gebeurtenisuitgevers alleen gemaakt met bevoegdheden voor **verzenden** voor een specifieke Event Hub. Dit URL-mechanisme met SAS-token vormt de basis voor de uitgeversidentificatie die in het uitgeversbeleid wordt geïntroduceerd. Zie [Shared Access Signature-verificatie met Service Bus](../service-bus/service-bus-shared-access-signature-authentication.md) voor meer informatie over werken met SAS.

#### Een gebeurtenis publiceren

U kunt een gebeurtenis publiceren met AMQP 1.0 of HTTPS. Service Bus biedt een [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx)-klasse voor het publiceren van gebeurtenissen naar een Event Hub vanaf .NET-clients. Voor andere runtimes en platforms kunt u een AMQP 1.0-client gebruiken, zoals [Apache Qpid](http://qpid.apache.org/). U kunt gebeurtenissen afzonderlijk of batchgewijs publiceren. Eén publicatie (exemplaar met gebeurtenisgegevens) heeft een limiet van 256 kB, ongeacht of het om één gebeurtenis of om een batch gaat. Als u grotere gebeurtenissen publiceert, resulteert dit in een fout. Het is voor uitgevers een best practice om niets te weten over de partities binnen Event Hub en alleen een *partitiesleutel* (zie volgende sectie) of hun identiteit via de SAS-token op te geven.

De keuze om AMQP of HTTPS te gebruiken, geldt specifiek voor het gebruiksscenario. AMQP vereist de inrichting van een permanente bidirectionele socket naast Transport Layer Security (TLS) of SSL/TLS. In verband met de hoeveelheid netwerkverkeer kan dit kostbaar zijn, maar dit is alleen van toepassing op het begin van een AMQP-sessie. HTTPS heeft in het begin een lagere overhead, maar vereist extra SSL-overhead voor elke aanvraag. Voor uitgevers die vaak gebeurtenissen publiceren, biedt AMQP aanzienlijke prestaties, latentie en doorvoerbesparingen.

### Partitiesleutel

Een partitiesleutel is een waarde die wordt gebruikt om inkomende gebeurtenisgegevens toe te wijzen aan specifieke partities, zodat de gegevens kunnen worden geordend. De partitiesleutel is een door de afzender opgegeven waarde die aan een Event Hub wordt doorgegeven. De partitiesleutel wordt verwerkt door een statische hash-functie. Het resultaat hiervan zorgt voor de partitietoewijzing. Als u bij het publiceren van een gebeurtenis geen partitiesleutel opgeeft, wordt er gebruikgemaakt van round robin-toewijzing. Wanneer u partitiesleutels gebruikt, is de gebeurtenisuitgever alleen op de hoogte van de partitiesleutel en niet van de partitie waarop de gebeurtenissen worden gepubliceerd. Deze ontkoppeling van sleutel en partitie schermt de afzender af, zodat deze niet te veel te weten hoeft te komen over de downstreamverwerking en de opslag van gebeurtenissen. Partitiesleutels zijn belangrijk voor het ordenen van gegevens voor de downstreamverwerking, maar zijn totaal niet gerelateerd aan de partities zelf. Goede partitiesleutels zijn bijvoorbeeld een apparaatspecifieke of een gebruikersspecifieke identiteit, maar voor het groeperen van gerelateerde gebeurtenissen in dezelfde partitie kunnen ook andere kenmerken, zoals geografie, worden gebruikt. In de volgende afbeelding ziet u hoe afzenders van gebeurtenissen partitiesleutels gebruiken om gebeurtenissen aan partities te koppelen.

![Event Hubs](./media/event-hubs-overview/IC759859.png)

Event Hubs zorgt ervoor dat alle gebeurtenissen met dezelfde partitiesleutelwaarde op volgorde en aan dezelfde partitie worden geleverd. Houd voor ogen dat de identiteit van de uitgever en de waarde van de partitiesleutel overeen moeten komen als er partitiesleutels met uitgeversbeleid worden gebruikt. Het uitgeversbeleid wordt in de volgende sectie beschreven. Als deze niet overeenkomen, treedt er een fout op.

### Gebeurtenisconsumer

Elke entiteit die gebeurtenisgegevens van een Event Hub leest, is een gebeurtenisconsumer. Alle gebeurtenisconsumers lezen de gebeurtenisstroom via partities in een consumergroep. Elke partitie moet slechts één actieve lezer per keer hebben. Alle Event Hubs-consumers maken verbinding via de AMQP 1.0-sessie, waarin gebeurtenissen worden geleverd zodra deze beschikbaar komen. De client hoeft de beschikbaarheid van de gegevens niet te peilen.

#### Consumergroepen

Het Event Hubs-mechanisme voor publiceren/abonneren wordt geactiveerd via consumergroepen. Een consumergroep is een weergave (status, positie of offset) van een volledige Event Hub. Consumergroepen maken het mogelijk dat meerdere consumers beschikken over een afzonderlijke weergave van de gebeurtenisstroom. De toepassingen kunnen de stroom onafhankelijk, in hun eigen tempo en met hun eigen offsets lezen. In een architectuur waarin de stroom wordt verwerkt, is elke downstream-toepassing gelijk aan een consumergroep. Als u gebeurtenisgegevens naar de langetermijnopslag wilt schrijven, is de schrijftoepassing die hiervoor wordt gebruikt, een consumergroep. De complexe verwerking van gebeurtenissen wordt uitgevoerd door een andere, afzonderlijke consumergroep. U hebt alleen toegang tot partities via een consumergroep. Een Event Hub bevat altijd een standaardconsumergroep. Voor een Event Hub van het type Standaard kunt u maximaal 20 consumergroepen maken.

Dit zijn voorbeelden van de URI-conventie voor consumergroepen:

    //<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
    //<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>

De volgende afbeelding geeft de gebeurtenisconsumers binnen consumergroepen weer.

![Event Hubs](./media/event-hubs-overview/IC759860.png)

#### Stroom-offsets

Een offset is de positie van een gebeurtenis binnen een partitie. U kunt een offset beschouwen als een clientcursor. De offset is een bytenummering van de gebeurtenis. Hiermee kan een gebeurtenisconsumer (lezer) een punt in de gebeurtenisstroom opgeven vanwaaruit begonnen moet worden met het lezen van gebeurtenissen. U kunt de offset opgeven als een tijdstempel of als een offsetwaarde. Consumers zijn zelf verantwoordelijk voor het opslaan van hun eigen offsetwaarden buiten de Event Hubs-service.

![Event Hubs](./media/event-hubs-overview/IC759861.png)

Binnen een partitie bevat elke gebeurtenis een offset. Deze offset wordt door consumers gebruikt om de locatie in de gebeurtenisvolgorde voor een bepaalde partitie weer te geven. Offsets kunnen aan de Event Hub worden doorgegeven als een getal of als een tijdstempelwaarde wanneer een lezer verbinding maakt.

#### Controlepunten plaatsen

*Het plaatsen van controlepunten* is een proces waarbij lezers hun positie binnen de gebeurtenisvolgorde van een partitie markeren of vastleggen. Het plaatsen van controlepunten is de verantwoordelijkheid van de consumer en vindt plaats per partitie binnen een consumergroep. Dit betekent dat elke partitielezer voor elke consumergroep de huidige positie in de gebeurtenisstroom moet bijhouden en de service kan informeren wanneer de gegevensstroom is voltooid. Als een lezer van een partitie is losgekoppeld en er vervolgens weer verbinding wordt gemaakt, begint het lezen bij het controlepunt dat eerder is verzonden door de laatste lezer van de betreffende partitie in de consumergroep. Wanneer de lezer verbinding maakt, wordt deze offset aan de Event Hub doorgegeven om de locatie op te geven waar u wilt beginnen met lezen. Op deze manier kunt u het plaatsen van controlepunten gebruiken om gebeurtenissen te markeren als 'voltooid' door downstream-toepassingen. Bovendien beschikt u met controlepunten over tolerantie bij een failover tussen lezers die op verschillende apparaten worden uitgevoerd. Omdat gebeurtenisgegevens worden bewaard gedurende het retentie-interval dat u opgeeft bij het maken van de Event Hub, is het mogelijk om terug te keren naar oudere gegevens. Dit doet u door een lagere offset uit het proces voor het plaatsen van controlepunten op te geven. Via dit mechanisme zorgt het plaatsen van controlepunten voor failover-tolerantie en voor gestuurde herhaling van gebeurtenisstromen.

#### Algemene taken voor consumers

In deze sectie worden algemene taken voor gebeurtenisconsumers of -lezers van Event Hubs beschreven. Alle consumers van Event Hubs maken verbinding via AMQP 1.0. AMQP 1.0 is een sessie- en statusbewust bidirectioneel communicatiekanaal. Elke partitie heeft een AMQP 1.0-koppelingssessie die het mogelijk maakt partitiespecifieke gebeurtenissen te transporteren.

##### Verbinding maken met een partitie

Een consumer moet verbinding maken met een partitie om gebeurtenissen van een Event Hub te kunnen gebruiken. Zoals eerder vermeld, verloopt de toegang tot een partitie altijd via een consumergroep. Het model op basis van gepartitioneerd gebruik schrijft voor dat er op elk willekeurig moment slechts één lezer actief mag zijn op een partitie binnen een consumergroep. Het is gebruikelijk om een leasemechanisme te gebruiken wanneer u rechtstreeks verbinding maakt met partities, zodat u het verbinden van lezers aan specifieke partities kunt coördineren. Op deze manier maakt u het mogelijk dat elke partitie in een consumergroep slechts één actieve lezer heeft. Het voor een lezer beheren van de positie in de volgorde is een belangrijke taak. Hiervoor wordt gebruikgemaakt van het plaatsen van controlepunten. Deze functionaliteit wordt vereenvoudigd door toepassing van de [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx)-klasse voor .NET-clients. [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) is een intelligente consumeragent. In de volgende sectie leest u hier meer over.

##### Gebeurtenissen lezen

Nadat er voor een specifieke partitie een AMQP 1.0-sessie en -koppeling zijn geopend, worden de gebeurtenissen door de Event Hubs-service aan de AMQP 1.0-client geleverd. Dit leveringsmechanisme maakt hogere doorvoer en lagere latentie mogelijk dan pull-mechanismen zoals HTTP GET. Tijdens het verzenden van gebeurtenissen naar de client wordt elk gebeurtenisgegeven voorzien van belangrijke metagegevens, zoals de offset en het volgnummer. Deze worden gebruikt om het plaatsen van controlepunten in de gebeurtenisvolgorde mogelijk te maken.

![Event Hubs](./media/event-hubs-overview/IC759862.png)

Het is de verantwoordelijkheid van de gebruiker om deze offset zodanig te beheren dat de stroom op optimale wijze wordt verwerkt.

## Capaciteit en beveiliging

Event Hubs is een uiterst schaalbare parallelle architectuur voor inkomende stromen. Ten aanzien hiervan zijn er diverse belangrijke aspecten die u voor ogen moet houden wanneer u een oplossing op basis van Event Hubs dimensioneert of de schaal ervan aanpast. Bepalend voor de capaciteit is in de eerste plaats het aantal beschikbare *doorvoereenheden*. Deze worden in de volgende sectie beschreven.

### Doorvoereenheden

De doorvoercapaciteit van Event Hubs wordt bepaald door het aantal beschikbare doorvoereenheden. Doorvoereenheden zijn vooraf aangeschafte capaciteitseenheden. Eén doorvoereenheid bevat het volgende:

- Inkomende gegevens: maximaal 1 MB per seconde of 1000 gebeurtenissen per seconde.

- Uitgaande gegevens: tot 2 MB per seconde.

De inkomende gegevens worden beperkt door de capaciteit die door het aantal aangeschafte doorvoereenheden wordt geleverd. Als u meer gegevens verzendt, resulteert dit in de uitzondering 'quotum overschreden'. Het quotum is 1 MB per seconde of 1000 gebeurtenissen per seconde, afhankelijk van wat het eerst wordt bereikt. Bij de uitgaande gegevens doen zich geen quotumgerelateerde uitzonderingen voor, maar de gegevensoverdracht is beperkt tot de capaciteit van de aangeschafte doorvoereenheden: 2 MB per seconde per doorvoereenheid. Als zich uitzonderingen met betrekking tot de publicatiesnelheid voordoen of als u meer uitgaande gegevens verwacht, controleert u hoeveel doorvoereenheden u hebt aangeschaft voor de naamruimte waarin de Event Hub is gemaakt. Als u meer doorvoereenheden wilt, past u de betreffende instelling in de [klassieke Azure Portal][] aan op de pagina **Naamruimten** op het tabblad **Schaal**. U kunt deze instelling ook wijzigen met de Azure-API's.

Partities zijn een concept voor gegevensordening. Doorvoereenheden zijn uitsluitend een capaciteitsconcept. Doorvoereenheden worden per uur in rekening gebracht en zijn vooraf aangeschaft. Nadat u doorvoereenheden hebt aangeschaft, worden deze voor minimaal één uur in rekening gebracht. U kunt voor een Service Bus-naamruimte maximaal 20 doorvoereenheden aanschaffen. Per Azure-account geldt een limiet van 20 doorvoereenheden. Deze doorvoereenheden worden verdeeld over alle Event Hubs in een bepaalde naamruimte.

Doorvoereenheden worden ingericht op basis van 'best effort' en zijn mogelijk niet altijd beschikbaar voor onmiddellijke aankoop. Als u een specifieke capaciteit nodig hebt, doet u er verstandig aan de benodigde doorvoereenheden van tevoren aan te schaffen. Als u meer dan 20 doorvoereenheden nodig hebt, neemt u contact op met de Service Bus-ondersteuning om aanvullende doorvoereenheden aan te schaffen. Tot de eerste 100 extra doorvoereenheden vindt aanschaf plaats in blokken van 20. Daarna kunt u ook blokken van 100 doorvoereenheden aanschaffen.

We raden u aan het aantal doorvoereenheden en partities zorgvuldig op elkaar af te stemmen, zodat u met Event Hubs een optimale schaal bereikt. Per partitie is maximaal één doorvoereenheid mogelijk. Het aantal doorvoereenheden moet daarom kleiner zijn dan of gelijk zijn aan het aantal partities in een Event Hub.

Zie [Prijzen van Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) voor gedetailleerde informatie over prijzen.

### Uitgeversbeleid

In Event Hubs kunt u gebeurtenisuitgevers nauwkeurig beheren met behulp van *uitgeversbeleid*. Uitgeversbeleid bestaat uit een reeks runtimefuncties die zijn ontworpen om grote aantallen onafhankelijke gebeurtenisuitgevers mogelijk te maken. Als u uitgeversbeleid implementeert, gebruikt elke uitgever zijn eigen unieke id bij het publiceren van gebeurtenissen naar een Event Hub. Hierbij wordt het volgende mechanisme gebruikt:

    //<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>

Het is niet nodig om van tevoren uitgeversnamen te maken. De namen moeten echter wel overeenkomen met het SAS-token dat wordt gebruikt wanneer een gebeurtenis wordt gepubliceerd. Hiermee wordt voor onafhankelijke uitgeversidentiteiten gezorgd. Zie [Shared Access Signature-verificatie met Service Bus](../service-bus/service-bus-shared-access-signature-authentication.md) voor meer informatie over SAS. Als u uitgeversbeleid gebruikt, is de waarde **Partitiesleutel** ingesteld op de naam van de uitgever. Voor een goede werking moeten deze waarden overeenkomen.

## Samenvatting

Azure Event Hubs is een verwerkingsservice voor grote hoeveelheden gebeurtenissen en telemetriegegevens. Deze kan worden gebruikt voor monitoring van algemene toepassingen en gebruikerswerkstromen op elke schaal. Event Hubs biedt mogelijkheden voor klein- en grootschalige scenario’s voor publiceren/abonneren. Hierbij fungeert de service als toegangspunt voor big data. Met uitgeversspecifieke identiteiten en intrekkingslijsten kunnen deze mogelijkheden worden uitgebreid naar algemene scenario’s voor het internet der dingen (IoT). Zie de [Programmeerhandleiding voor Event Hubs](event-hubs-programming-guide.md) voor meer informatie over het ontwikkelen van Event Hubs-toepassingen.

## Volgende stappen

Nu u bekend bent met de concepten van Event Hubs, kunt u verdergaan met de volgende scenario's:

- Aan de slag met een [Event Hubs-zelfstudie].
- Een complete [voorbeeldtoepassing die gebruikmaakt van Event Hubs].
- Een [oplossing voor berichten in de wachtrij] met behulp van Service Bus-wachtrijen.

[klassieke Azure Portal]: http://manage.windowsazure.com
[Event Hubs-zelfstudie]: event-hubs-csharp-ephcs-getstarted.md
[voorbeeldtoepassing die gebruikmaakt van Event Hubs]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[oplossing voor berichten in de wachtrij]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 



<!--HONumber=Jun16_HO2-->


