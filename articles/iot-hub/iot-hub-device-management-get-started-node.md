<properties
    pageTitle="Aan de slag met apparaatbeheer via IoT Hub | Microsoft Azure"
    description="Handleiding voor apparaatbeheer met C# via Azure IoT Hub. Implementeer apparaatbeheer met behulp van Azure IoT Hub en C# in combinatie met de Microsoft Azure IoT SDK's."
    services="iot-hub"
    documentationCenter=".net"
    authors="ellenfosborne"
    manager="timlt"
    editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Aan de slag met apparaatbeheer met node.js via Azure IoT Hub (preview)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Inleiding
Als u apparaten wilt gaan beheren met Azure IoT Hub, moet u een Azure IoT Hub maken, apparaten in de IoT-Hub invoeren en meerdere gesimuleerde apparaten starten. In deze handleiding ontdekt u stapsgewijs hoe u dit moet doen.

> [AZURE.NOTE]  U moet een nieuwe IoT Hub maken om apparaatbeheerfuncties in te kunnen schakelen, zelfs als u al een IoT Hub hebt. Dit is omdat bestaande IoT Hubs nog geen apparaatbeheerfuncties hebben. Zodra apparaatbeheer algemeen beschikbaar is, worden alle bestaande IoT Hubs bijgewerkt met apparaatbeheerfuncties.

## Vereisten

De volgende programma’s moeten geïnstalleerd zijn om de verschillende stappen te kunnen doorlopen:

- Git
- node
- npm
- CMake (versie 2.8 of hoger). Om CMake te kunnen installeren, dient u het programma eerst te downloaden van <https://cmake.org/download/>. Controleer of het selectievakje voor het toevoegen van CMake aan de huidige gebruikerspadvariabele is aangevinkt.
- Een actief Azure-abonnement.

    Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure][lnk-free-trial] voor meer informatie.

## Een IoT Hub met apparaatbeheerfunctie maken

U moet een IoT Hub met apparaatbeheerfunctie maken waarmee uw gesimuleerde apparaten verbinding kunnen maken. In de volgende stappen ziet u hoe u dit moet doen met behulp van de Azure Portal.

1.  Meld u aan bij de [Azure Portal].
2.  Klik in de snelbalk op **New**. Klik vervolgens op **Internet of Things** en klik vervolgens op **Azure IoT Hub**.

    ![][img-new-hub]

3.  In de blade **IoT Hub** kiest u de configuratie voor uw IoT Hub.

    ![][img-configure-hub]

  -   In het vak **Name** voert u een naam voor uw IoT Hub in. Als de **naam** geldig en beschikbaar is, verschijnt er een groen vinkje in het vak **Name**.
  -   Selecteer een **prijs- en schaalcategorie**. Voor deze handleiding is geen bepaalde categorie vereist.
  -   Geef in **Resource group** een nieuwe resourcegroep op of selecteer een bestaande. Zie [Resourcegroepen gebruiken om Azure-resources te beheren] voor meer informatie.
  -   Vink het selectievakje **Enable Device Management** aan.
  -   Bij **Location** selecteert u de locatie waar uw IoT Hub gehost wordt. Apparaatbeheer via IoT Hub is alleen beschikbaar in het oostelijk deel van de VS, Noord-Europa en Oost-Azië tijdens de openbare previewfase. In de toekomst zal de functie beschikbaar zijn voor alle regio's.

  > [AZURE.NOTE]  Als u het selectievakje **Enable Device Management** niet aanvinkt, werken de voorbeelden niet.

4.  Wanneer u de configuratieopties voor uw IoT Hub hebt gekozen, klikt u op **Create**. Het kan enkele minuten duren voordat Azure uw IoT Hub heeft gemaakt. Als u de status wilt zien, kunt u de voortgang bekijken via het **Startboard** of in het venster **Notifications**.

    ![][img-monitor]

5.  Wanneer het gelukt is om een IoT Hub te maken, opent u de blade van de nieuwe IoT Hub. Noteer de **hostnaam** en klik vervolgens op het pictogram **Keys**.

    ![][img-keys]

6.  Klik op het beleid **iothubowner**. Kopieer en noteer de verbindingsreeks in de blade **iothubowner**. Kopieer de verbindingsreeks naar een locatie die u later terug kunt vinden. U hebt deze nodig voor de rest van deze handleiding.

    > [AZURE.NOTE] Zorg ervoor dat u in productiescenario's de **iothubowner**-referenties niet gebruikt.

    ![][img-connection]

U hebt nu een IoT Hub met apparaatbeheerfunctie gemaakt. U hebt een verbindingsreeks nodig voor de rest van deze handleiding.

## Voorbeelden bouwen en apparaten invoeren in uw IoT Hub

In dit gedeelte gaat u een script uitvoeren dat het gesimuleerde apparaat en de voorbeelden bouwt en een aantal nieuwe apparaat-id’s invoert in het apparatenregister van uw IoT Hub. Een apparaat kan geen verbinding met IoT Hub maken, tenzij het vermeld staat in het apparatenregister.

Volg de onderstaande stappen om de voorbeelden te bouwen en apparaten in te voeren in uw IoT Hub:

1.  Open de terminal.

2.  Kloon de github-opslagplaats. **Zorg ervoor dat u de opslagplaats kloont naar een map zonder spaties.**

      ```
      git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
      ```

3.  Vanuit de hoofdmap waar u de opslagplaats **azure-iot-sdks** gekopieerd hebt, gaat u naar de map **azure-iot-sdks/node/service/samples** en voert u het script uit, waarbij u de aanduidingswaarde vervangt door uw verbindingsreeks uit het vorige gedeelte:

      ```
      setup.bat <IoT Hub Connection String>
      ```

Dit script doet het volgende:

1.  **cmake** wordt uitgevoerd om de bestanden te maken die nodig zijn voor het creëren van het gesimuleerde apparaat. Het uitvoerbare bestand bevindt zich in **azure-iot-sdks/node/service/samples/cmake/iotdm\_client/samples/iotdm\_simple\_sample**. Let op: de bronbestanden staan in de map **azure-iot-sdks/c/iotdm\_client/samples/iotdm\_simple\_sample**.

2.  Het gesimuleerde uitvoerbare apparaatbestand **iotdm\_simple\_sample** wordt gemaakt.

3.  ``` npm install ``` wordt uitgevoerd om de vereiste pakketten te installeren.

4.  ```node generate_devices.js``` wordt uitgevoerd om apparaat-id’s in uw IoT-Hub in te richten. De apparaten worden beschreven in **sampledevices.json**. Nadat de apparaten die zijn ingericht, worden de referenties opgeslagen in het bestand **devicecreds.txt** (dit bestand bevindt zich in de map **azure-iot-sdks/node/service/samples**).

## Uw gesimuleerde apparaten starten

Wanneer de apparaten zijn toegevoegd aan het apparatenregister, kunt u de gesimuleerde beheerde apparaten starten. Er moet één gesimuleerd apparaat worden gestart per apparaat-id die is ingericht in de Azure IoT Hub.

Voer in de map **azure-iot-sdks/node/service/samples** het volgende uit met behulp van de terminal:

  ```
  simulate.sh
  ```

Dit script levert de opdrachten die u moet uitvoeren om  **iotdm\_simple\_sample** te starten voor elk apparaat dat wordt weergegeven in het bestand **devicecreds.txt**. Voer de opdrachten afzonderlijk uit vanuit een apart terminalvenster voor elk gesimuleerd apparaat. Het gesimuleerde apparaat blijft actief totdat u het opdrachtvenster sluit.

De toepassing **iotdm\_simple\_sample** is gemaakt met de apparaatbeheerclientbibliotheek voor C van de Azure IoT Hub. Hiermee kunnen IoT-apparaten worden gemaakt die beheerd kunnen worden door de Azure IoT Hub. Apparatenmakers kunnen met behulp van deze bibliotheek apparaateigenschappen melden en de uitgevoerde acties implementeren die vereist zijn door apparaattaken. Deze bibliotheek is een component dat wordt geleverd als onderdeel van de open source Azure IoT Hub SDK's.

Wanneer u **simulate.sh** uitvoert, ziet u een stream met gegevens in het uitvoervenster. Deze uitvoer toont het binnenkomende en uitgaande verkeer, evenals **printf**-instructies in de callback-functies die specifiek zijn voor de toepassing. Hiermee kunt u binnenkomend en uitgaand verkeer bekijken en ziet u hoe de voorbeeldtoepassing de gedecodeerde pakketten verwerkt. Als het apparaat verbinding maakt met de IoT Hub, wordt de service automatisch gestart om resources op het apparaat te observeren. De DM-clientbibliotheek van IoT Hub roept vervolgens de callbacks van het apparaat op om de meest recente waarden van het apparaat op te halen.

Hieronder vindt u de uitvoer van de voorbeeldtoepassing **iotdm\_simple\_sample**. Bovenaan ziet u het bericht dat de **REGISTRATIE** geslaagd is. In dit bericht wordt tevens het apparaat met id **Device11 7ce4a850** getoond dat verbinding maakt met IoT Hub.

> [AZURE.NOTE]  Voor een minder uitgebreide uitvoer maakt u de retailconfiguratie en voert u deze uit.

![][img-output]

Zorg ervoor dat alle gesimuleerde apparaten actief blijven wanneer u 'Volgende stappen' in deze handleiding volgt.

## Volgende stappen

Voor meer informatie over apparaatbeheerfuncties van de Azure IoTHub, kunt u de volgende handleidingen raadplegen:

- [Bij elkaar horende apparaten gebruiken][lnk-tutorial-twin]

- [Bij elkaar horende apparaten zoeken aan de hand van query’s][lnk-tutorial-queries]

- [Apparaattaken gebruiken om firmware voor apparaten bij te werken][lnk-tutorial-jobs]

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure Portal]: https://portal.azure.com/
[Resourcegroepen gebruiken om Azure-resources te beheren]: ../azure-portal/resource-group-portal.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md



<!--HONumber=Jun16_HO2-->


