<properties
    pageTitle="Aan de slag met Azure IoT Hub voor Node.js | Microsoft Azure"
    description="Instructiehandleiding voor Azure IoT Hub met Node.js. Gebruik Azure IoT Hub en Node.js in combinatie met de Microsoft Azure IoT SDK's voor het implementeren van een Internet of Things-oplossing."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Aan de slag met Azure IoT Hub voor Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Inleiding

Azure IoT Hub is een volledig beheerde service die stabiele en veilige tweerichtingscommunicatie tussen miljoenen Internet of Things-apparaten en de back-end van een oplossing mogelijk maakt. Een van de grootste uitdagingen van IoT-projecten is het op stabiele en veilige wijze verbinden van apparaten met de back-end van een oplossing. Als oplossing voor deze uitdaging biedt IoT Hub het volgende:

- Stabiele verzending van berichten op hyperschaal tussen apparaat en cloud en tussen cloud en apparaat.
- Veilige communicatie met behulp van beveiligingsreferenties en toegangscontrole.
- Apparaatbibliotheken voor de meest populaire talen en platforms.

In deze handleiding ontdekt u hoe u:

- Met de Azure Portal een IoT-hub kunt maken.
- Een apparaat-id maakt in uw IoT-hub.
- Een gesimuleerd apparaat maakt dat telemetrie naar de back-end van uw cloud verzendt.

Nadat u deze handleiding volledig hebt doorlopen, beschikt u over drie Node.js-consoletoepassingen:

* **CreateDeviceIdentity.js**: deze toepassing maakt een apparaat-id en de bijbehorende beveiligingssleutel waarmee uw gesimuleerde apparaat verbonden kan worden.
* **ReadDEviceToCloudMessages.js**: deze toepassing geeft de telemetrie weer die is verzonden door uw gesimuleerde apparaat.
* **SimulatedDevice.js**: deze toepassing koppelt uw IoT-hub aan de apparaat-id die u eerder hebt gemaakt, en verzendt iedere seconde een telemetriebericht via het AMQPS-protocol.

> [AZURE.NOTE] Het artikel [IoT-Hub SDK's][lnk-hub-sdks] bevat informatie over de verschillende SDK's die u kunt gebruiken om beide toepassingen zo te maken dat ze zowel op het apparaat als op de back-end van uw oplossing kunnen worden uitgevoerd.

Voor deze handleiding hebt u het volgende nodig:

+ Node.js-versie 0.12.x of hoger. <br/> In het gedeelte [Uw ontwikkelingsomgeving voorbereiden][lnk-dev-setup] staat beschreven hoe u Node.js voor deze handleiding moet installeren op Windows of Linux.

+ Een actief Azure-account. <br/>Als u geen account hebt, kunt u binnen enkele minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure][lnk-free-trial] voor meer informatie.

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Klik ten slotte op **Instellingen** op de blade IoT Hub en klik vervolgens op **Messaging** op de blade **Instellingen**. Noteer in de blade **Messaging** de **Event Hub-compatibele naam** en het **Event Hub-compatibele eindpunt**. U hebt deze waarden nodig wanneer u uw **read-d2c-messages**-toepassing maakt.

![][6]

U hebt nu uw IoT-hub gemaakt en u beschikt over de hostnaam en verbindingsreeks van de IoT Hub, de Event Hub-compatibele naam en de Event Hub- compatibele eindpuntwaarden. Hiermee kunt u de rest van deze handleiding doorlopen.

## Een apparaat-id maken

In dit gedeelte gaat u een Node.js-consoletoepassing  maken die een nieuwe apparaat-id kan maken in het identiteitenregister van uw IoT-hub. Een apparaat kan geen verbinding maken met de IoT-hub, tenzij het vermeld staat in het registers met apparaat-id’s. Raadpleeg het gedeelte **Register met apparaat-id’s** in de [Ontwikkelaarshandleiding voor IoT Hub][lnk-devguide-identiteit] voor meer informatie. Wanneer u deze consoletoepassing uitvoert, worden er een unieke apparaat-id en sleutel gegenereerd waarmee uw apparaat zichzelf kan identificeren wanneer het apparaat-naar-cloud-berichten naar IoT Hub verzendt.

1. Maak een nieuwe lege map met de naam **createdeviceidentity**. In de map **createdeviceidentity** maakt u een nieuw package.json-bestand met de volgende opdracht bij de opdrachtprompt. Accepteer alle standaardwaarden:

    ```
    npm init
    ```

2. Bij de opdrachtprompt in de map **createdeviceidentity** voert u de volgende opdracht uit om het **azure-iothub**-pakket te installeren:

    ```
    npm install azure-iothub --save
    ```

3. Maak met een tekstverwerker een nieuw **CreateDeviceIdentity.js**-bestand in de map **createdeviceidentity**.

4. Voeg de volgende `require` instructie toe aan het begin van het bestand **CreateDeviceIdentity.js**-bestand:

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Voeg de code toe aan het bestand **CreateDeviceIdentity.js** en vervang de aanduidingswaarde door de verbindingsreeks voor de  IoT-hub die u hebt gemaakt in het vorige gedeelte: 

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Voeg de volgende code toe om een nieuwe apparaatdefinitie te maken in het register met apparaat-id’s van uw IoT-hub. Met deze code wordt een nieuw apparaat gemaakt als de apparaat-id nog niet voorkomt in het register. Anders wordt de sleutel van het reeds bestaande apparaat teruggekoppeld:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Sla het bestand **CreateDeviceIdentity.js** op en sluit het.

8. Als u de toepassing **createdeviceidentity** wilt uitvoeren, geef dan de volgende opdracht bij de opdrachtprompt in de map createdeviceidentity:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Noteer de **apparaat-id** en de **apparaatsleutel**. Deze hebt u later weer nodig wanneer u een toepassing gaat maken die verbinding maakt met IoT Hub als apparaat.

> [AZURE.NOTE] In het id-register van IoT Hub worden alleen apparaat-id’s opgeslagen waarmee veilig toegang tot de hub kan worden verkregen. De apparaat-id’s en sleutels worden opgeslagen en gebruikt als beveiligingsreferenties. Met de vlag voor ingeschakeld/uitgeschakeld kunt u toegang tot een afzonderlijk apparaat uitschakelen. Als uw toepassing andere apparaatspecifieke metagegevens moet opslaan, moet deze een toepassingsspecifieke opslagmethode gebruiken. Raadpleeg de [IoT Hub Developer Guide ][lnk-devguide-identiteit] (Ontwikkelaarshandleiding voor IoT Hub) voor meer informatie.

## Apparaat-naar-cloud-berichten ontvangen

In dit gedeelte gaat u een Node.js-consoletoepassing maken die apparaat-naar-cloud-berichten uit IoT Hub kan lezen. Een IoT-hub toont een [Event Hubs][lnk-event-hubs-overview]-compatibel eindpunt waarmee u apparaat-naar-cloud-berichten kunt lezen. Om de zaken niet nodeloos ingewikkeld te maken, maakt u met deze handleiding een basislezer die niet geschikt is voor hoge doorvoersnelheden. In de handleiding [Apparaat-naar-cloud-berichten verwerken][lnk-process-d2c-tutorial] leert u hoe u op grote schaal apparaat-naar-cloud-berichten kunt verwerken. In de handleiding [Aan de slag met Event Hubs][lnk-eventhubs-tutorial] leest u meer over het verwerken van berichten van Event Hubs. Deze handleiding is van toepassing op de Event Hub-compatibele eindpunten van IoT Hub.

> [AZURE.NOTE] Het met Event Hubs compatibele eindpunt voor het lezen van apparaat-naar-cloud-bericht maakt altijd gebruik van het AMQPS-protocol.

1. Maak een nieuwe lege map met de naam **readdevicetocloudmessages**. In de map **readdevicetocloudmessages** maakt u een nieuw package.json-bestand met de volgende opdracht bij de opdrachtprompt. Accepteer alle standaardwaarden:

    ```
    npm init
    ```

2. Voer bij de opdrachtprompt in de map **readdevicetocloudmessages** de volgende opdracht uit om de pakketten **amqp10** en **bluebird** te installeren:

    ```
    npm install amqp10 bluebird --save
    ```

3. Maak met een tekstverwerker een nieuw bestand **ReadDeviceToCloudMessages.js** in de map **readdevicetocloudmessages**.

4. Voeg de volgende `require` instructies toe aan het begin van het bestand **ReadDeviceToCloudMessages.js**:

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. Voeg de volgende variabelendeclaraties toe en vervang de aanduidingen door de waarden die u al eerder hebt genoteerd. De aanduidingswaarde **{uw event hub-compatibele naamruimte}** is afkomstig uit het veld **Event Hub-compatibel eindpunt** in de portal - deze krijgt de volgende vorm: **namespace.servicebus.windows.net** (zonder het voorvoegsel *sb: / /*).

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] Voor deze code wordt ervan uitgegaan dat u uw IoT-hub in de (gratis) F1-categorie hebt gemaakt. Een gratis IoT-hub heeft twee partities, genaamd "0" en "1". Als u uw IoT-hub hebt gemaakt met een van de andere prijscategorieën, moet u de code aanpassen om een **MessageReceiver** te maken voor elke partitie.

6. Voeg de volgende filterdefinitie toe. Deze toepassing gebruikt een filter bij het maken van een ontvanger, zodat de ontvanger alleen berichten leest die naar IoT Hub worden verzonden wanneer de ontvanger is geactiveerd. Dit is handig in een testomgeving, omdat u zo de huidige reeks berichten kunt zien. In een productieomgeving moet uw code er echter voor zorgen dat alle berichten worden verwerkt. Zie de handleiding [IoT Hub apparaat-naar-cloud-berichten verwerken][lnk-process-d2c-tutorial] voor meer informatie.

    ```
    var filterOffset = new Date().getTime();
    var filterOption;
    if (filterOffset) {
      filterOption = {
      attach: { source: { filter: {
      'apache.org:selector-filter:string': translator(
        ['described', ['symbol', 'apache.org:selector-filter:string'], ['string', "amqp.annotation.x-opt-enqueuedtimeutc > " + filterOffset + ""]])
        } } }
      };
    }
    ```

7. Voeg de volgende code toe om het adres voor het ontvangen en een AMQP-client te maken:

    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
    
    var client = new AMQPClient(Policy.EventHub);
    ```

8. Voeg de volgende twee functies toe die de uitvoer naar de console afdrukken:

    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
    
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```

9. Voeg de volgende functie toe die fungeert als ontvanger voor een bepaalde partitie met behulp van het filter:

    ```
    var createPartitionReceiver = function(partitionId, receiveAddress, filterOption) {
      return client.createReceiver(receiveAddress, filterOption)
        .then(function (receiver) {
          console.log('Listening on partition: ' + partitionId);
          receiver.on('message', messageHandler.bind(null, partitionId));
          receiver.on('errorReceived', errorHandler.bind(null, partitionId));
        });
    };
    ```

10. Voeg de volgende code toe om verbinding te maken met het Event Hub-compatibele eindpunt en de ontvangers te starten:

    ```
    client.connect(uri)
      .then(function () {
        var partitions = [];
        for (var i = 0; i < numPartitions; ++i) {
          partitions.push(createPartitionReceiver(i, recvAddr + i, filterOption));
        }
        return Promise.all(partitions);
    })
    .error(function (e) {
        console.warn('Connection error: ', e);
    });
    ```

11. Sla het bestand **ReadDeviceToCloudMessages.js** op en sluit het.

## Een gesimuleerde apparaattoepassing maken

In dit gedeelte gaat u een Node.js-consoletoepassing maken die een apparaat simuleert dat apparaat-naar-cloud-berichten naar een IoT Hub verzendt.

1. Maak een nieuwe lege map met de naam **simulateddevice**. In de map **simulateddevice** maakt u een nieuw package.json-bestand met de volgende opdracht bij de opdrachtprompt. Accepteer alle standaardwaarden:

    ```
    npm init
    ```

2. Bij de opdrachtprompt in de map **simulateddevice** voert u de volgende opdracht uit om het **azure-iot-device-amqp**-pakket te installeren:

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. Maak met een tekstverwerker een nieuw bestand **SimulatedDevice.js** in de map **simulateddevice**.

4. Voeg de volgende `require` instructies toe aan het begin van het bestand **SimulatedDevice.js**:

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Voeg een **connectionString**-variabele toe en gebruik deze om een apparaat-client te maken. Vervang **{youriothubname}** door de naam van uw IoT-hub en **{yourdeviceid}** en **{yourdevicekey}** door waarden van het apparaat die u hebt gegenereerd tijdens het gedeelte *Een apparaat-id maken*:

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Voeg de volgende functie toe om uitvoer van de toepassing weer te geven:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Maak een callback en verzend iedere seconde met de functie **setInterval** een nieuw bericht naar uw IoT-hub:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'mydevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 2000);
      }
    };
    ```

8. Maak verbinding met uw IoT Hub en begin met het verzenden van berichten:

    ```
    client.open(connectCallback);
    ```

9. Sla het bestand **SimulatedDevice.js** op en sluit het.

> [AZURE.NOTE] Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. Bij de productiecode moet u een beleid voor opnieuw proberen implementeren (zoals exponentieel uitstel), zoals aangegeven in het MSDN-artikel [Transient Fault Handling ][lnk-transient-faults] (Afhandeling van tijdelijke fouten).


## De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Voer bij een opdrachtprompt in de map **readdevicetocloudmessages** de volgende opdracht uit om uw IoT-hub te kunnen volgen:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. Voer bij een opdrachtprompt in de map **simulateddevice** de volgende opdracht uit om telemetriegegevens naar uw IoT-hub te verzenden:

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. De tegel **Usage** in de [Azure Portal][lnk-portal] toont het aantal berichten dat is verzonden naar de hub:

    ![][43]

## Volgende stappen

In deze handleiding hebt u een nieuwe IoT-hub geconfigureerd in de portal en vervolgens hebt u een apparaat-id gemaakt in het id-register van de hub. U hebt deze apparaat-id gebruikt om de gesimuleerde apparaattoepassing in staat te stellen om apparaat-naar-cloud-berichten te verzenden naar de hub en u hebt een toepassing gemaakt die de berichten toont die de hub heeft ontvangen. U kunt nog andere functies van de IoT-hub en andere IoT-scenario’s bekijken in de volgende handleidingen:

- In de handleiding [Cloud-naar-apparaat-berichten verzenden met IoT Hub][lnk-c2d-tutorial] leert u hoe u berichten moet verzenden naar apparaten en hoe u de leveringsfeedback verwerkt die door de IoT Hub is gegenereerd.
- In de handleiding [Apparaat-naar-cloud-berichten verwerken][lnk-process-d2c-tutorial] leert u hoe u op betrouwbare wijze telemetriegegevens en interactieve berichten kunt verwerken die afkomstig zijn van apparaten.
- In de handleiding [Bestanden van apparaten uploaden][lnk-upload-tutorial] wordt een patroon beschreven dat cloud-naar-apparaat-berichten gebruikt om bestanden van apparaten te kunnen uploaden.

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identiteit]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/



<!--HONumber=Jun16_HO2-->


