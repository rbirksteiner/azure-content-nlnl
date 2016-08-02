<properties
    pageTitle="Aan de slag met Azure IoT Hub voor Java | Microsoft Azure"
    description="Handleiding voor Azure IoT Hub met Java. Gebruik Azure IoT Hub en Java in combinatie met de Microsoft Azure IoT SDK's voor het implementeren van een Internet of Things-oplossing."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/06/2016"
     ms.author="dobett"/>

# Aan de slag met Azure IoT Hub voor Java

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

Aan het eind van deze handleiding beschikt u over drie Java-consoletoepassingen:

* **create-device-identity**: deze toepassing maakt een apparaat-id en de bijbehorende beveiligingssleutel waarmee uw gesimuleerde apparaat verbonden kan worden.
* **read-d2c-messages**: deze toepassing geeft de telemetrie weer die is verzonden door uw gesimuleerde apparaat.
* **simulated-device**: deze toepassing koppelt uw IoT-hub aan de apparaat-id die u eerder hebt gemaakt, en verzendt iedere seconde een telemetriebericht via het AMQPS-protocol.

> [AZURE.NOTE] Het artikel [IoT-Hub SDK's][lnk-hub-sdks] bevat informatie over de verschillende SDK's die u kunt gebruiken om beide toepassingen zo te maken dat ze zowel op het apparaat als op de back-end van uw oplossing kunnen worden uitgevoerd.

Voor deze handleiding hebt u het volgende nodig:

+ Java SE 8. <br/> In het gedeelte [Uw ontwikkelingsomgeving voorbereiden][lnk-dev-setup] staat beschreven hoe u Java voor deze handleiding kunt installeren op Windows of Linux.

+ Maven 3.  <br/> In het gedeelte [Uw ontwikkelingsomgeving voorbereiden][lnk-dev-setup] staat beschreven hoe u Maven voor deze handleiding kunt installeren op Windows of Linux.

+ Een actief Azure-account. <br/>Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure][lnk-free-trial] voor meer informatie.

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Klik ten slotte op **Instellingen** op de blade IoT Hub en klik vervolgens op **Messaging** op de blade **Instellingen**. Noteer in de blade **Messaging** de **Event Hub-compatibele naam** en het **Event Hub-compatibele eindpunt**. U hebt deze waarden nodig wanneer u uw **read-d2c-messages**-toepassing maakt.

![][6]

U hebt nu uw IoT-hub gemaakt en u beschikt over de hostnaam en verbindingsreeks van de IoT Hub, de Event Hub-compatibele naam en de Event Hub- compatibele eindpuntwaarden. Deze hebt u nodig voor de rest van deze handleiding.

## Een apparaat-id maken

In dit gedeelte gaat u een Java-consoletoepassing maken die een nieuwe apparaat-id kan maken in het identiteitenregister van uw IoT-hub. Een apparaat kan geen verbinding maken met de IoT-hub, tenzij het vermeld staat in het registers met apparaat-id’s. Raadpleeg het gedeelte **Register met apparaat-id’s** in de [Ontwikkelaarshandleiding voor IoT Hub][lnk-devguide-identiteit] voor meer informatie. Wanneer u deze consoletoepassing uitvoert, worden er een unieke apparaat-id en sleutel gegenereerd waarmee uw apparaat zichzelf kan identificeren wanneer het apparaat-naar-cloud-berichten naar IoT Hub verzendt.

1. Maak een nieuwe lege map genaamd iot-java-get-started. Maak een nieuw Maven-project in de map iot-java-get-started en noem dit **create-device-identity**. Gebruik hierbij de volgende opdracht in uw opdrachtvenster. Let op: dit is één enkele, lange opdracht:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigeer naar de nieuwe map create-device-identity vanuit het opdrachtvenster.

3. Open het bestand pom.xml in de map create-device-identity met een teksteditor en voeg de volgende afhankelijkheden toe aan de node **dependencies**. Hiermee kunt u het iothub-service-sdk-pakket gebruiken in uw toepassing:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.2</version>
    </dependency>
    ```
    
4. Sla het bestand pom.xml op en sluit het af.

5. Open het bestand create-device-identity\src\main\java\com\mycompany\app\App.java met een teksteditor.

6. Voeg de volgende **import**instructies toe aan het bestand:

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang **{yourhubname}** en **{yourhubkey}** door de waarden die u hebt genoteerd:

    ```
    private static final String connectionString = "HostName={yourhubname}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. Wijzig de handtekening van de **belangrijkste** methode om de hieronder weergegeven uitzonderingen op te nemen:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Voeg de volgende code toe als hoofdtekst van de **belangrijkste** methode. Deze code maakt een apparaat in het identiteitsregister van de IoT Hub dat *javadevice* heet als dit nog niet bestaat. Vervolgens wordt het apparaat-id en de sleutel weergegeven. Deze hebt u later nodig:

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Sla het bestand App.java op en sluit het af.

11. Als u de toepassing **create-device-identity** wilt creëren met behulp van Maven, geef dan de volgende opdracht in het opdrachtvenster in de map create-device-identity:

    ```
    mvn clean package -DskipTests
    ```

12. Als u de toepassing **create-device-identity** wilt uitvoeren met behulp van Maven, geef dan de volgende opdracht in het opdrachtvenster in de map create-device-identity:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Noteer de **apparaat-id** en de **apparaatsleutel**. Deze hebt u later weer nodig wanneer u een toepassing gaat maken die verbinding maakt met IoT Hub als apparaat.

> [AZURE.NOTE] In het id-register van IoT Hub worden alleen apparaat-id’s opgeslagen waarmee veilig toegang tot de hub kan worden verkregen. De apparaat-id’s en sleutels worden opgeslagen en gebruikt als beveiligingsreferenties. Met de vlag voor ingeschakeld/uitgeschakeld kunt u toegang tot een afzonderlijk apparaat uitschakelen. Als uw toepassing andere apparaatspecifieke metagegevens moet opslaan, moet deze een toepassingsspecifieke opslagmethode gebruiken. Raadpleeg de [IoT Hub Developer Guide ][lnk-devguide-identiteit] (Ontwikkelaarshandleiding voor IoT Hub) voor meer informatie.

## Apparaat-naar-cloud-berichten ontvangen

In dit gedeelte maakt u een Java-consoletoepassing maken die apparaat-naar-cloud-berichten uit IoT Hub kan lezen. Een IoT-hub toont een [Event Hub][lnk-event-hubs-overview]-compatibel eindpunt waarmee u apparaat-naar-cloud-berichten kunt lezen. Om de zaken niet nodeloos ingewikkeld te maken, maakt u met deze handleiding een basislezer die niet geschikt is voor hoge doorvoersnelheden. In de handleiding [Apparaat-naar-cloud-berichten verwerken][lnk-process-d2c-tutorial] leert u hoe u op grote schaal apparaat-naar-cloud-berichten kunt verwerken. In de handleiding [Aan de slag met Event Hubs][lnk-eventhubs-tutorial] leest u meer over het verwerken van berichten van Event Hubs. Deze handleiding is van toepassing op de Event Hub-compatibele eindpunten van IoT Hub.

> [AZURE.NOTE] Het met Event Hub compatibele eindpunt voor het lezen van apparaat-naar-cloud-bericht maakt altijd gebruik van het AMQPS-protocol.

1. Maak een nieuw Maven-project in de map iot-java-get-started die u hebt gemaakt in het gedeelte *Een apparaat-id maken* en noem dit **read-d2c-messages**. Gebruik hierbij de volgende opdracht in uw opdrachtvenster. Let op: dit is één enkele, lange opdracht:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigeer naar de nieuwe map read-d2c-messages vanuit het opdrachtvenster.

3. Open het bestand pom.xml in de map read-d2c-messages met een teksteditor en voeg de volgende afhankelijkheden toe aan de node **dependencies**. Hiermee kunt u het eventhubs-client-pakket in uw toepassing gebruiken om berichten te lezen van het eindpunt dat compatibel is met Event Hub:

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.7.1</version> 
    </dependency>
    ```

4. Sla het bestand pom.xml op en sluit het af.

5. Open het bestand read-d2c-messages\src\main\java\com\mycompany\app\App.java met een teksteditor.

6. Voeg de volgende **import**instructies toe aan het bestand:

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang **{youriothubkey}**, **{youreventhubcompatiblenamespace}**, en **{youreventhubcompatiblename}** door de waarden die u eerder hebt genoteerd. De aanduiding **{youreventhubcompatiblenamespace}** is afkomstig is van het **Event Hub-compatibele eindpunt**. Deze ziet er als volgt uit: **xyznamespace** (oftewel, verwijder **sb://** en **.servicebus.windows.net** van de waarde voor het Event Hub-compatibele ) eindpunt van de portal:

    ```
    private static String namespaceName = "{youreventhubcompatiblenamespace}";
    private static String eventHubName = "{youreventhubcompatiblename}";
    private static String sasKeyName = "iothubowner";
    private static String sasKey = "{youriothubkey}";
    private static long now = System.currentTimeMillis();
    ```

8. Voeg de volgende **receiveMessages**-methode toe aan de **App**-klasse. Met deze methode maakt u een **EventHubClient**-exemplaar dat verbinding maakt met het Event Hub-compatibele eindpunt. Vervolgens maakt u asynchroon een **PartitionReceiver**-exemplaar voor het lezen van een Event Hub-partitie. Dit wordt continu uitgevoerd en de berichtgegevens worden afgedrukt totdat de toepassing wordt gesloten.

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
        client = EventHubClient.createFromConnectionString(connStr.toString()).get();
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive().get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

    > [AZURE.NOTE] Deze methode maakt gebruik van een filter bij het maken van de ontvanger, zodat de ontvanger alleen de berichten leest die naar de IoT Hub zijn verzonden nadat de ontvanger actief is geworden. Dit is handig in een testomgeving, omdat u zo de huidige reeks berichten kunt zien. In een productieomgeving moet uw code er echter voor zorgen dat alle berichten worden verwerkt. Zie de handleiding [IoT Hub apparaat-naar-cloud-berichten verwerken][lnk-process-d2c-tutorial] voor meer informatie.

9. Wijzig de handtekening van de **belangrijkste** methode om de hieronder weergegeven uitzondering op te nemen:

    ```
    public static void main( String[] args ) throws IOException
    ```

10. Voeg de volgende code aan de **belangrijkste** methode in de **App**-klasse. Deze code wordt gemaakt van de twee **EventHubClient** en **PartitionReceiver** exemplaren. U kunt de toepassing sluiten als u klaar bent met het verwerken van berichten:

    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```

    > [AZURE.NOTE] Voor deze code wordt ervan uitgegaan dat u uw IoT-hub in de (gratis) F1-categorie hebt gemaakt. Een gratis IoT-hub heeft twee partities, genaamd "0" en "1".

11. Sla het bestand App.java op en sluit het af.

12. Als u de toepassing **read-d2c-messages** wilt creëren met behulp van Maven, geef dan de volgende opdracht in het opdrachtvenster in de map read-d2c-messages:

    ```
    mvn clean package -DskipTests
    ```

## Een gesimuleerde apparaattoepassing maken

In dit gedeelte maakt u een Java-consoletoepassing maken die een apparaat simuleert dat apparaat-naar-cloud-berichten naar een IoT Hub verzendt.

1. Maak een nieuw Maven-project in de map iot-java-get-started die u hebt gemaakt in het gedeelte *Een apparaat-id maken* en noem dit **simulated-device**. Gebruik hierbij de volgende opdracht in uw opdrachtvenster. Let op: dit is één enkele, lange opdracht:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigeer naar de nieuwe map simulated-device vanuit het opdrachtvenster.

3. Open het bestand pom.xml in de map simulated-device met een teksteditor en voeg de volgende afhankelijkheden toe aan de node **dependencies**. Hiermee kunt u het iothub-java-clients pakket in uw toepassing gebruiken om te communiceren met uw IoT Hub en om Java-objecten te serialiseren naar JSON:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.2</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Sla het bestand pom.xml op en sluit het af.

5. Open het bestand simulated-device\src\main\java\com\mycompany\app\App.java met een teksteditor.

6. Voeg de volgende **import**instructies toe aan het bestand:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.Random;
    import javax.naming.SizeLimitExceededException;
    import com.google.gson.Gson;
    ```

7. Voeg de volgende variabelen op klasseniveau toe aan de **App**-klasse. Vervang daarbij **{youriothubname}** door de naam van uw IoT-hub en **{yourdeviceid}** en **{yourdevicekey}** door waarden van het apparaat die u hebt gegenereerd tijdens het gedeelte *Een apparaat-id maken*:

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
    ```

    Deze voorbeeldtoepassing gebruikt de variabele **protocol** bij het creëren van een **DeviceClient**-object. U kunt het HTTPS- of AMQPS-protocol gebruiken om te communiceren met de IoT-Hub.

8. Voeg de volgende geneste **TelemetryDataPoint**-klasse toe binnen de **App**-klasse om de telemetriegegevens op te geven die uw apparaat verzendt naar uw IoT-hub:

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. Voeg de volgende geneste **EventCallback**-klasse toe binnen de **App**-klasse om de bevestigingsstatus weer te geven die wordt geretourneerd door de IoT-hub wanneer deze een bericht verwerkt van het gesimuleerde apparaat. Met deze methode wordt ook een bericht verstuurd aan de hoofdthread in de toepassing wanneer het bericht is verwerkt:

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Voeg de volgende geneste **MessageSender**-klasse toe binnen de **App**-klasse. De **uitvoeren**-methode in deze klasse genereert voorbeeldtelemetrie die u kunt verzenden naar uw IoT-hub en die wacht op een bevestiging voordat het volgende bericht wordt verzonden:

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;

      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          DeviceClient client;
          client = new DeviceClient(connString, protocol);
          client.open();
        
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = "myFirstDevice";
            telemetryDataPoint.windSpeed = currentWindSpeed;
      
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println(msgStr);
        
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
          client.close();
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    }
    ```

    Deze methode verzendt één seconde nadat de IoT-hub het vorige bericht erkent een nieuw apparaat-naar-cloud bericht. Het bericht bevat een JSON-geserialiseerd object met de apparaat-id en een willekeurig gegenereerd nummer om een windsnelheidssensor te simuleren.

11. Vervang de **belangrijkste** methode door de volgende code die een thread maakt om apparaat-naar-cloudberichten te verzenden naar uw IoT-hub:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
    
      MessageSender ms0 = new MessageSender();
      Thread t0 = new Thread(ms0);
      t0.start(); 
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      ms0.stopThread = true;
    }
    ```

12. Sla het bestand App.java op en sluit het af.

13. Als u de toepassing **simulated-device** wilt creëren met behulp van Maven, geef dan de volgende opdracht in het opdrachtvenster in de map simulated-device:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. Bij de productiecode moet u een beleid voor opnieuw proberen implementeren (zoals exponentieel uitstel), zoals aangegeven in het MSDN-artikel [Transient Fault Handling ][lnk-transient-faults] (Afhandeling van tijdelijke fouten).

## De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Voer bij een opdrachtprompt in de map read-d2c de volgende opdracht uit om de eerste partitie in uw IoT-hub te kunnen volgen:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"  -Dexec.args="0"
    ```

    ![][7]

1. Voer bij een opdrachtprompt in de map read-d2c de volgende opdracht uit om de tweede partitie in uw IoT-hub te kunnen volgen:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"  -Dexec.args="1"
    ```

    ![][7]

2. Voer bij een opdrachtprompt in de map simulated-device de volgende opdracht uit om telemetriegegevens naar uw IoT-hub te verzenden:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
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
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identiteit]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/



<!--HONumber=Jun16_HO2-->


