<properties
 pageTitle="Overzicht Azure IoT Hub | Microsoft Azure"
 description="Overzicht van de service Azure IoT Hub: wat is iot hub, verbindingsmogelijkheden voor apparaten, communicatiepatronen voor Internet of Things (IoT - Internet der dingen) en service-ondersteunde communicatiepatronen"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/06/2016"
 ms.author="dobett"/>

# Wat is Azure IoT Hub?

Welkom bij Azure IoT Hub. Dit artikel biedt een overzicht Azure IoT Hub en een beschrijving van waarom u deze service moet gebruiken wanneer u een Internet of Things-oplossing (IoT - Internet der dingen) gaat implementeren.

Azure IoT Hub is een volledig beheerde service die stabiele en veilige tweerichtingscommunicatie tussen miljoenen IoT-apparaten en de back-end van een oplossing mogelijk maakt. Azure IoT Hub:

- Biedt betrouwbare verzending van berichten op grote schaal tussen apparaat en cloud en tussen cloud en apparaat.
- Veilige communicatie met behulp van beveiligingsreferenties en toegangscontrole.
- Biedt uitgebreide bewaking voor verbindingsmogelijkheden van apparaten en gebeurtenissen voor het beheren van apparaat-id’s.
- Apparaatbibliotheken voor de meest populaire talen en platforms.

In het artikel [IoT Hub en Event Hubs vergeleken][lnk-compare] worden de voornaamste verschillende tussen de twee services beschreven en worden de voordelen van het gebruik van IoT Hub in uw IoT-oplossing uitgelicht.

![Azure IoT-Hub als cloudgateway in IoT-oplossing][img-architecture]

> [AZURE.NOTE] Zie [Microsoft Azure IoT Reference Architecture][lnk refarch] (Referentiearchitectuur voor Microsoft Azure IoT) voor een gedetailleerdere beschrijving van de IoT-architectuur.

## Uitdagingen met de connectiviteit van IoT-apparaten

Dankzij IoT Hub en de apparaatbibliotheken kunt u nu op een betrouwbare en veilige manier apparaten verbinden met de back-end van een oplossing. IoT-apparaten:

- Zijn vaak ingesloten systemen waarbij geen menselijke operator komt kijken.
- Kunnen zich op verafgelegen locaties bevinden, waar fysieke toegang bijzonder kostbaar is.
- Kunnen mogelijk alleen worden bereikt via de back-end van de oplossing.
- Hebben mogelijk een beperkt vermogen en een beperkt aantal verwerkingsbronnen.
- Hebben mogelijk een onstabiele, trage of dure netwerkverbinding.
- Vereisen mogelijk het gebruik van bedrijfseigen, aangepaste of branchespecifieke toepassingsprotocollen.
- Kunnen worden gemaakt met behulp van een groot aantal populaire hardware- en softwareplatforms.

Naast de bovenstaande vereisten moet een IoT-oplossing schaalbaar, veilig en betrouwbaar zijn. De resulterende reeks connectiviteitsvereisten is moeilijk te implementeren en tijdrovend als u conventionele technologieën gebruikt, zoals webcontainers en berichtenbrokers.

## Waarom Azure IoT Hub gebruiken?

Azure IoT Hub lost problemen met de connectiviteit van apparaten op de volgende manieren op:

-   **Verificatie per apparaat en beveiligde verbindingen**. U kunt elk apparaat voorzien van een eigen [beveiligingssleutel][lnk-devguide-security], zodat het verbinding kan maken met IoT Hub. Het [id-register van IoT Hub][lnk-devguide-identityregistry] slaat apparaat-id’s en sleutels op in een oplossing. De back-end van een oplossing kan afzonderlijke apparaten op een lijst met goedgekeurde en afgekeurde IP-adressen zetten, waardoor de toegang tot het apparaat volledig wordt beheerd.

-   **Bewaking van connectiviteitsbewerkingen van apparaten**. U kunt gedetailleerde bewerkingslogboeken over bewerkingen in het beheer van apparaat-id’s en  connectiviteitsgebeurtenissen van apparaten ontvangen. Hierdoor kan uw IoT-oplossing eenvoudig problemen met de netwerkverbinding signaleren, zoals apparaten die verbinding proberen te maken met de verkeerde referenties, die te vaak berichten verzenden of die alle cloud-naar-apparaat-berichten blokkeren.

-   **Een uitgebreide reeks apparaatbibliotheken**. [Apparaat-SKD’s van Azure IoT][lnk-apparaat-SDK‘s] zijn beschikbaar en worden ondersteund in verscheidene talen en door diverse platforms--C voor veel  Linux-distributies, Windows en real-time besturingssystemen. Apparaat-SDK’s van Azure IoT ondersteunen tevens beheerde talen, zoals C#, Java en JavaScript.

-   **IoT-protocollen en uitbreidingsmogelijkheden**. Als uw oplossing de apparaatbibliotheken niet kan gebruiken, toont IoT Hub een openbare protocol waarmee apparaten op systeemeigen wijze de protocollen MQTT v3.1.1, HTTP 1.1 of AMQP 1.0 kunnen gebruiken. U kunt  IoT Hub ook uitbreiden, zodat het aangepaste protocollen ondersteunt. Doe hiervoor het volgende:

    - Maak een veldgateway met de [Azure IoT Gateway SDK][lnk-gateway-sdk] die uw aangepaste protocol converteert naar een van de drie protocollen die IoT Hub kent. 
    - Pas de [protocolgateway van Azure IoT][protocol- gateway] aan; dit is een open source-component dat wordt uitgevoerd in de cloud.

-   **Schalen**. Azure IoT Hub schaalt naar miljoenen gelijktijdig verbonden apparaten en miljoenen gebeurtenissen per seconde.

Veel communicatiepatronen bieden deze voordelen. Met IoT Hub kunt u nu de volgende specifieke communicatiepatronen implementeren:

-   **Opname van apparaat-naar-cloud op basis van gebeurtenissen.** IoT Hub kan op betrouwbare wijze miljoenen gebeurtenissen per seconde ontvangen van uw apparaten. De service kan deze vervolgens op uw hot pad verwerken met behulp van een gebeurtenisprocessor-engine. De gebeurtenissen kunnen ook op uw koude pad worden opgeslagen voor analysedoeleinden. IoT Hub bewaart gebeurtenisgegevens maximaal zeven dagen, zodat ze betrouwbaar kunnen worden verwerkt en pieken in de workload kunnen worden opgevangen.

-   **Betrouwbaar berichten verzenden van cloud naar apparaat (of *opdrachten*).** De back-end van de oplossing kan met IoT Hub berichten verzenden met de garantie dat ze ten minste eenmaal worden geleverd op afzonderlijke apparaten. Elk bericht heeft zijn eigen time to live-instelling en de back-end kan bewijzen voor zowel het afleveren als het verstrijken opvragen. Hiermee krijgt u volledig inzicht in de levenscyclus van een cloud-naar-apparaat-bericht. Vervolgens kunt u bedrijfslogica implementeren die bewerkingen bevatten die op apparaten kunnen worden uitgevoerd.

-   **Upload bestanden en sensorgegevens in de cache naar de cloud.** Uw apparaten kunnen bestanden uploaden naar Azure Storage met SAS URI's die voor u worden beheerd door IoT Hub. IoT Hub kan meldingen genereren wanneer bestanden in de cloud binnenkomen, zodat de back-end deze kan verwerken.

## Gateways

Een gateway in een IoT-oplossing is doorgaans een [protocolgateway][lnk-gateway] die geïmplementeerd is in de cloud of een [veldgateway][lnk-field-gateway] die lokaal is geïmplementeerd in uw apparaten. Een protocolgateway vertaalt protocollen, bijvoorbeeld MQTT naar AMQP. Een veldgateway kan analyses op de rand uitvoeren,  tijdgebonden beslissingen nemen die de latentie kunnen verminderen, apparaatbeheerservices bieden, beveiligings- en privacybeperkingen opleggen en protocollen vertalen. Beide gatewaytypen fungeren als schakels tussen uw apparaten en uw IoT-hub.

Het enige verschil bij een veldgateway is een eenvoudig routeringsmechanisme (zoals een netwerkadresomzetting (NAT) of firewall), omdat deze gateway normaliter een actieve rol speelt bij het beheren van de toegang en de informatiestroom in uw oplossing.

Een oplossing kan zowel een protocolgateway als een veldgateway bevatten.

## Hoe werkt IoT Hub?

Azure IoT Hub implementeert het [service-ondersteunde communicatiepatroon][lnk-service-assisted-pattern] dat interacties tussen uw apparaten en de back-end van uw oplossing overbrengt. Het doel van de service-ondersteunde communicatie is het opzetten van betrouwbare tweerichtingscommunicatiepaden tussen een besturingssysteem, zoals IoT Hub, en apparaten voor speciale doeleinden die worden geïmplementeerd in mogelijk niet betrouwbare fysieke ruimtes. Het patroon gaat uit van de volgende principes:

- Beveiliging gaat voor alle andere functies.
- Apparaten accepteren geen ongevraagde netwerkinformatie. Een apparaat brengt alle verbindingen tot stand en routeert alleen uitgaand verkeer. Als een apparaat een opdracht moet kunnen ontvangen van de back-end, moet het apparaat regelmatig opnieuw verbinding maken om te controleren of er nog opdrachten in afwachting zijn van verwerking.
- Apparaten mogen alleen verbinding maken of routes tot stand brengen met bekende, gelijkwaardige services, zoals IoT Hub.
- Het communicatiepad tussen het apparaat en de service of tussen het apparaat en de gateway is beveiligd op de laag van het toepassingsprotocol.
- Autorisatie en verificatie op systeemniveau geschiedt op basis van de id van een apparaat. Hierdoor kunnen toegangsreferenties en machtigingen bijna onmiddellijk worden ingetrokken.
- Tweerichtingscommunicatie tussen apparaten die slechts sporadisch verbinding maken vanwege problemen met de stroom of de connectiviteit, wordt mogelijk gemaakt door opdrachten en apparaatmeldingen vast te houden totdat het apparaat weer verbinding maakt en de meldingen en opdrachten ontvangt. IoT Hub heeft apparaatspecifieke wachtrijen voor de opdrachten die worden verzonden.
- Gegevens over de nettolading van toepassing worden afzonderlijk beveiligd, zodat ze veilig via gateways worden verzonden naar een bepaalde service.

De mobiele industrie heeft op zeer grote schaal het service-ondersteunde communicatiepatroon gebruikt  voor het implementeren van pushmeldingservices, zoals [Windows Push Notification Services][lnk-wns], [Google Cloud Messaging][lnk-google-messaging] en [Apple Push Notification Service][lnk-apple-push].

## Volgende stappen

Voor meer informatie over Azure IoT Hub klikt u op de volgende links:

* [Aan de slag met IoT Hub][lnk-get-started]
* [Uw apparaat verbinden][lnk-connect-device]
* [Apparaat-naar-cloud-berichten verwerken][lnk-d2c]

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-d2c]: iot-hub-csharp-csharp-process-d2c.md
[protocol- gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Service-ondersteunde communicatie, blogpost van Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-guidance.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-apparaat-SDK‘s]: https://github.com/Azure/azure-iot-sdks
[lnk refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk



<!--HONumber=Jun16_HO2-->


