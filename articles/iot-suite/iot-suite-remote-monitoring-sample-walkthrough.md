<properties
 pageTitle="Walkthrough over vooraf geconfigureerde oplossing voor externe controle | Microsoft Azure"
 description="Een beschrijving van de vooraf geconfigureerde oplossing voor externe controle IoT Azure en de bijbehorende architectuur."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="03/02/2016"
 ms.author="stevehob"/>

# Walkthrough over vooraf geconfigureerde oplossing voor externe controle

## Inleiding

De vooraf geconfigureerde oplossing voor externe controle IoT Suite is een elementaire end-to-end-oplossing voor controle en past in een bedrijfsscenario waar meerdere computers op externe locaties worden gebruikt. De oplossing combineert belangrijke Azure IoT Suite-services voor het uitvoeren van een algemene implementatie van het bedrijfsscenario en is een beginpunt voor klanten die van plan zijn dit type IoT-oplossing te implementeren om aan hun specifieke zakelijke vereisten tegemoet te komen.

## Logische architectuur

Het volgende diagram biedt een overzicht van de logische onderdelen van de vooraf geconfigureerde oplossing:

![](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


### Gesimuleerde apparaten

In de vooraf geconfigureerde oplossing vertegenwoordigt het gesimuleerde apparaat een koelapparaat (zoals een airconditioner of een luchtververser in een gebouw). Elk van de gesimuleerde apparaten verzendt de volgende telemetrieberichten naar IoT Hub:


| Bericht  | Beschrijving |
|----------|-------------|
| Opstarten  | Wanneer het apparaat wordt gestart, verzendt het een bericht met **apparaatgegevens** met informatie over het apparaat zelf zoals de apparaat-id, de metagegevens van het apparaat, een lijst met opdrachten die door het apparaat worden ondersteund en de huidige configuratie van het apparaat. |


De gesimuleerde apparaten verzenden de volgende eigenschappen van het apparaat als metagegevens:

| Eigenschap               |  Doel |
|------------------------|--------- |
| Apparaat-id              | De id die met het apparaat wordt verstrekt of die is toegewezen als een apparaat in de oplossing wordt gemaakt. |
| Fabrikant           | Fabrikant van apparaat |
| Modelnummer           | Modelnummer van het apparaat |
| Serienummer          | Serienummer van het apparaat |
| Firmware               | Huidige versie van de firmware op het apparaat |
| Platform               | Platformarchitectuur van het apparaat |
| Processor              | Processor waarop het apparaat wordt uitgevoerd |
| Geïnstalleerd RAM-geheugen          | Hoeveelheid RAM-geheugen dat op het apparaat is geïnstalleerd |
| Status hub ingeschakeld      | Eigenschap IoT Hub-status van het apparaat |
| Gemaakt om           | Tijd waarop het apparaat in de oplossing is gemaakt |
| Bijgewerkt om           | De tijd waarop de laatste keer de eigenschappen van het apparaat zijn bijgewerkt |
| Breedtegraad               | Breedtegraad van locatie waar het apparaat zich bevindt |
| Lengtegraad              | Lengtegraad van locatie waar het apparaat zich bevindt |

De simulator voorziet deze eigenschappen in de gesimuleerde apparaten van voorbeeldwaarden.  Telkens wanneer de simulator een gesimuleerd apparaat initialiseert, plaatst het apparaat de vooraf gedefinieerde metagegevens in IoT Hub. Houd er rekening mee dat hiermee alle in de portal van het apparaat bijgewerkte metagegevens worden overschreven.


De gesimuleerde apparaten kunnen de volgende opdrachten verwerken die van een IoT Hub zijn verzonden:

| Opdracht                | Beschrijving                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | Hiermee wordt een _ping_ verzonden naar het apparaat om te controleren of het actief is   |
| StartTelemetry         | Geeft het apparaat opdracht om te beginnen met het verzenden van telemetriegegevens                 |
| StopTelemetry          | Geeft het apparaat opdracht om te stoppen met het verzenden van telemetriegegevens             |
| ChangeSetPointTemp     | Hiermee wordt de waarde van het instelpunt gewijzigd waaromheen de willekeurige gegevens worden gegenereerd |
| DiagnosticTelemetry    | Hiermee wordt de apparaatsimulator geactiveerd voor het verzenden van een extra telemetriewaarde (externalTemp) |
| ChangeDeviceState      | Hiermee wordt een eigenschap uitgebreide status voor het apparaat gewijzigd en wordt het bericht met apparaatgegevens vanaf het apparaat verzonden |


De bevestiging van de opdracht door het apparaat vindt plaats via IoT Hub.


### Azure Stream Analytics-taken


**Taak 1: apparaatgegevens** filtert berichten met apparaatgegevens uit de binnenkomende berichtenstroom en stuurt deze naar een Event Hub-eindpunt. Een apparaat verzendt berichten met apparaatgegevens bij het opstarten en in reactie op een opdracht **SendDeviceInfo**. Tijdens deze taak wordt gebruikgemaakt van de volgende querydefinitie:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

**Taak 2: regels** evalueren de binnenkomende telemetriegegevens over temperatuur en vochtigheid aan de hand van de drempelwaarden voor elk apparaat. Drempelwaarden kunnen worden ingesteld met de regeleditor die met de oplossing wordt meegeleverd. Elk koppel apparaat/waarde wordt per tijdstempel opgeslagen in een blob die door Stream Analytics wordt gelezen als **referentiegegevens**. De taak vergelijkt elke niet-lege waarde met de ingestelde drempelwaarde voor het apparaat. Als deze de voorwaarde '>' overschrijdt, bestaat de taakuitvoer uit een **waarschuwings**gebeurtenis die aangeeft dat de drempelwaarde is overschreden, en worden tevens de waarde voor het apparaat, de waarde en de tijdstempelwaarde gegeven. Tijdens deze taak wordt gebruikgemaakt van de volgende querydefinitie:

```
WITH AlarmsData AS 
(
SELECT
     Stream.DeviceID,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.DeviceID,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

**Taak 3: telemetrie** is op twee manieren actief op de binnenkomende stroom telemetriegegevens van het apparaat. Bij de eerste worden alle telemetrieberichten vanaf de apparaten naar een permanente blobopslag verzonden. Bij de tweede worden de gemiddelde, de minimale en de maximale vochtigheidswaarden in een sliding window van vijf minuut berekend. Deze gegevens worden ook verzonden naar de blobopslag. Tijdens deze taak wordt gebruikgemaakt van de volgende querydefinitie:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM 
      [IoTHubStream] 
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    *
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    DeviceId,
    AVG (Humidity) AS [AverageHumidity], 
    MIN(Humidity) AS [MinimumHumidity], 
    MAX(Humidity) AS [MaxHumidity], 
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM
    [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    DeviceId, 
    SlidingWindow (mi, 5)
```

### Gebeurtenisprocessor

De **gebeurtenisprocessor** verwerkt berichten met apparaatgegevens en reacties op opdrachten. Hierbij:

- Worden berichten met apparaatgegevens gebruikt om het apparaatregister bij te werken (opgeslagen in de DocumentDB-database) met de huidige apparaatgegevens.
- Worden berichten met reacties op opdrachten gebruikt om de opdrachtgeschiedenis van het apparaat bij te werken (opgeslagen in de DocumentDB-database).

## Stap voor stap

In deze sectie maakt u kennis met de onderdelen van de oplossing, wordt het beoogde gebruiksscenario beschreven en vindt u voorbeelden.

### Het dashboard Externe controle
Deze pagina in de webtoepassing maakt gebruik van Power BI javascript-besturingselementen (zie [PowerBI-visuals reports](https://www.github.com/Microsoft/PowerBI-visuals)) om de uitvoergegevens van de Stream Analytics-taken in de blob-opslag te visualiseren.


### Portal voor apparaatbeheer

Met deze web-app kunt u:

- Een nieuw apparaat inrichten waarmee het unieke apparaat-id wordt ingesteld en de verificatiesleutel wordt gegenereerd.
- Eigenschappen van een apparaat beheren, onder andere door het bekijken van bestaande eigenschappen en het bijwerken met nieuwe eigenschappen.
- Opdrachten naar een apparaat verzenden.
- De opdrachtgeschiedenis voor een apparaat weergeven.

### Het gedrag van de cloudoplossing observeren
U kunt uw ingerichte resources weergeven door eerst naar de [Azure Portal](https://portal.azure.com) te gaan en daarna naar de resourcegroep met de naam van de oplossing die u hebt gekozen.

![](media/iot-suite-remote-monitoring-sample-walkthrough/azureportal_01.png)

Wanneer u de oplossing voor het eerst uitvoert, zijn er vier gesimuleerde apparaten:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_01.png)

U kunt de portal voor apparaatbeheer gebruiken om een nieuw gesimuleerd apparaat toe te voegen:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_02.png)

In eerste instantie is de status van het nieuwe apparaat in de portal voor apparaatbeheer **In behandeling**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_03.png)

Als de app klaar is met de implementatie van het gesimuleerde apparaat, ziet u dat de status van het apparaat in de portal voor apparaatbeheer wordt gewijzigd in **Wordt uitgevoerd**, zoals weergegeven in de volgende schermafbeelding. Tijdens de Stream Analytics-taak **DeviceInfo** worden gegevens over de status van het apparaat verzonden van het apparaat naar de portal voor apparaatbeheer.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_04.png)

Met behulp van de portal van de oplossing kunt u opdrachten, zoals **ChangeSetPointTemp**, naar het apparaat verzenden:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_05.png)

Wanneer het apparaat meldt dat het de opdracht heeft uitgevoerd, wordt de status gewijzigd in **Geslaagd**.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_06.png)

Op de portal van de oplossing kunt u naar apparaten zoeken met specifieke kenmerken zoals een modelnummer:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_07.png)

U kunt een apparaat uitschakelen en nadat het is uitgeschakeld kunt u het verwijderen:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_08.png)


## Volgende stappen

De volgende TechNet-blogberichten bieden aanvullende details over de vooraf geconfigureerde oplossing voor externe controle:

- [IoT Suite - Under The Hood - Remote Monitoring (IoT Suite - achter de schermen - externe controle) ](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices (IoT Suite - externe controle - live- en gesimuleerde apparaten toevoegen)](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)


<!--HONumber=Jun16_HO2-->


