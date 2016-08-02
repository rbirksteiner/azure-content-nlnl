<properties
 pageTitle="Leidraad voor voorspeld onderhoud | Microsoft Azure"
 description="Een leidraad voor de vooraf geconfigureerde Azure IoT-oplossing voor voorspeld onderhoud."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="05/16/2016"
 ms.author="araguila"/>

# Leidraad voor vooraf geconfigureerde oplossing voor voorspeld onderhoud

## Inleiding

De vooraf geconfigureerde IoT-Suite-oplossing voor voorspeld onderhoud is een totaaloplossing voor een bedrijfsscenario die het punt voorspelt waarop naar verwachting een fout optreedt. U kunt van deze vooraf geconfigureerde oplossing proactief gebruikmaken voor activiteiten zoals de optimalisering van onderhoud. De oplossing combineert belangrijke Azure IoT Suite-services, waaronder een [Azure Machine Learning][lnk_machine_learning]-werkruimte met experimenten voor het voorspellen van de resterende levensduur van een vliegtuigmotor op basis van een verzameling openbare voorbeeldgegevens. De oplossing biedt een volledige implementatie van het bedrijfsscenario als een beginpunt voor het plannen en implementeren van dit type IoT-oplossing om aan uw eigen specifieke zakelijke vereisten te voldoen.

## Logische architectuur

Het volgende diagram geeft een overzicht van de logische onderdelen van de vooraf geconfigureerde oplossing:

![][img-architecture]

De blauwe items zijn Azure-services die zijn ingericht op de locatie die u selecteert wanneer u de vooraf geconfigureerde oplossing inricht. U kunt de vooraf geconfigureerde oplossing inrichten in de regio's VS - oost, Noord-Europa of Oost-Azië.

Sommige resources zijn niet beschikbaar in de regio's waar u de vooraf geconfigureerde oplossing inricht. De oranje items in het diagram vertegenwoordigen de Azure-services die ingericht zijn in de dichtstbijzijnde beschikbare regio (Zuid-centraal VS, EU West of Zuidoost-Azië), afhankelijk van de geselecteerde regio.

Het groene item is een gesimuleerd apparaat dat een vliegtuigmotor vertegenwoordigt. Hieronder vindt u meer informatie over deze gesimuleerde apparaten.

De grijze items vertegenwoordigen onderdelen die mogelijkheden voor *apparaatbeheer* implementeren. In de huidige release van de vooraf geconfigureerde oplossing voor voorspeld onderhoud worden deze resources niet ingericht. Voor meer informatie over het beheer van apparaten leest u het artikel over de [vooraf geconfigureerde oplossing voor externe controle][lnk-remote-monitoring].

## Gesimuleerde apparaten

In de vooraf geconfigureerde oplossing vertegenwoordigt een gesimuleerd apparaat een vliegtuigmotor. De oplossing is ingericht met twee motoren die aan één vliegtuig zijn toegewezen. Elke motor verzendt 4 typen telemetrie: Sensor 9, Sensor 11, Sensor 14 en Sensor 15, die de benodigde gegevens leveren waarmee het Machine Learning-model de resterende levensduur voor die motor berekent. Elk gesimuleerd apparaat verzendt de volgende telemetrieberichten naar IoT Hub:

*Aantal maal gebruikt*. Eén cyclus vertegenwoordigt een uitgevoerde vlucht met een variabele lengte van 2 tot 10 uur waarbij tijdens de vlucht elk half uur telemetriegegevens worden vastgelegd.

*Telemetrie*. Er zijn 4 sensoren die motorkenmerken vertegenwoordigen. Deze sensoren worden doorgaans Sensor 9, Sensor 11 Sensor 14 en Sensor 15 genoemd. Deze 4 sensoren vertegenwoordigen voldoende telemetrie om bruikbare resultaten voor de resterende levensduur op te halen uit het Machine Learning-model. Dit model wordt gemaakt op basis van een openbare gegevensset die echte motorsensorgegevens bevat. Zie [Cortana Intelligence Gallery Predictive Maintenance Template][lnk-cortana-analytics] (Cortana Intelligence Gallery-sjabloon voor voorspeld onderhoud) voor meer informatie over hoe het model wordt gemaakt op basis van de oorspronkelijke gegevensset.

De gesimuleerde apparaten kunnen de volgende opdrachten verwerken die vanaf een IoT Hub worden verzonden:

| Opdracht | Beschrijving |
|---------|-------------|
| StartTelemetry | Bepaalt de status van de simulatie.<br/>Start het verzenden van telemetrie vanaf het apparaat     |
| StopTelemetry  | Bepaalt de status van de simulatie.<br/>Stopt het verzenden van telemetrie vanaf het apparaat |

IoT Hub biedt een bevestiging van apparaatopdrachten.

## Azure Stream Analytics-job

**Job: Telemetrie** verwerkt de inkomende telemetriestroom van het apparaat met behulp van twee instructies. De eerste selecteert alle telemetrie van de apparaten en verzendt deze gegevens naar de Blob Storage waar ze in de web-app worden weergegeven. De tweede instructie berekent de gemiddelde sensorwaarden gedurende een sliding window van twee minuten en verzendt deze gegevens via Event Hub naar een **gebeurtenisprocessor**.

## Gebeurtenisprocessor

De **gebeurtenisprocessor** neemt de gemiddelde sensorwaarden voor een voltooide cyclus en geeft deze waarden door aan een API die het getrainde Machine Learning-model beschikbaar maakt om de resterende levensduur van een motor te berekenen.

## Azure Machine Learning

Zie [Cortana Intelligence Gallery Predictive Maintenance Template][lnk-cortana-analytics] (Cortana Intelligence Gallery-sjabloon voor voorspeld onderhoud) voor meer informatie over hoe het model wordt gemaakt op basis van de oorspronkelijke gegevensset.

## De eerste stappen

In deze sectie maakt u kennis met de onderdelen van de oplossing, wordt het beoogde gebruiksscenario beschreven en vindt u voorbeelden.

### Dashboard voorspeld onderhoud

Deze pagina in de webtoepassing maakt gebruik van PowerBI JavaScript-besturingselementen (zie de [PowerBI-visuals repository][lnk-powerbi] (Power BI-opslagplaats voor visualisaties)) voor het visualiseren van:

- De uitvoergegevens van de Stream Analytics-jobs in Blob Storage.
- De resterende levensduur en aantal cycli per vliegtuigmotor.

### Het gedrag van de cloudoplossing observeren

U kunt uw ingerichte resources weergeven door eerst naar Azure Portal te navigeren en daarna naar de resourcegroep met de naam van de oplossing die u hebt gekozen.

![][img-resource-group]

Wanneer u de vooraf geconfigureerde oplossing inricht, krijgt u een e-mailbericht met een koppeling naar de Machine Learning-werkruimte. U kunt ook naar deze Machine Learning-werkruimte navigeren vanaf de [azureiotsuite.com][lnk-azureiotsuite]-pagina voor de ingerichte oplossing wanneer deze de status **Gereed** heeft.

![][img-machine-learning]

In de oplossingsportal kunt u zien dat het voorbeeld is ingericht met vier gesimuleerde apparaten die 2 vliegtuigen vertegenwoordigen, met 2 motoren per vliegtuig en 4 sensoren per motor. Wanneer u voor het eerst naar de oplossingsportal navigeert, wordt de simulatie gestopt.

![][img-simulation-stopped]

Klik op **Simulatie starten** om de simulatie te starten waarbij u de geschiedenis van de sensor, de resterende levensduur, het aantal cycli en de geschiedenis van de resterende levensduur in het dashboard ziet verschijnen.

![][img-simulation-running]

Wanneer de resterende levensduur minder dan 160 is (een willekeurige drempel gekozen ter illustratie), verschijnt in de oplossingsportal een waarschuwingssymbool naast de weergegeven resterende levensduur en wordt de vliegtuigmotor in de afbeelding geel gekleurd. Zoals u merkt, vertonen de waarden van de resterende levensduur een algemene neerwaartse trend, maar stijgen en dalen die waarden vaak. Dit wordt veroorzaakt door de verschillende lengten van  de cycli en de nauwkeurigheid van het model.

![][img-simulation-warning]

Het duurt ongeveer 35 minuten voordat de volledige simulatie 148 cycli heeft voltooid. Na ongeveer 5 minuten wordt voor het eerst de drempelwaarde van 160 voor de resterende levensduur bereikt. De drempelwaarde voor beide motoren wordt na ongeveer 8 minuten bereikt.

De simulatie wordt uitgevoerd voor de volledige gegevensset voor 148 cycli en bepaalt dan het eindresultaat voor de resterende levensduur en het aantal cycli.

U kunt de simulatie op elk punt stoppen, maar wanneer u op **Simulatie starten** klikt, wordt de simulatie opnieuw vanaf het begin van de gegevensset uitgevoerd.

## Volgende stappen

Nu dat u de vooraf geconfigureerde oplossing voor voorspeld onderhoud hebt uitgevoerd, wilt u deze mogelijk wijzigen. Zie [Guidance on customizing preconfigured solutions][lnk-customize] (Instructies voor het aanpassen van vooraf geconfigureerde oplossingen) voor meer informatie.

Het TechNet-blogbericht [IoT Suite - Under The Hood - Predictive Maintenance](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx) (IoT Suite - Achter de schermen - Voorspeld onderhoud) biedt aanvullende informatie over de vooraf geconfigureerde oplossing voor voorspeld onderhoud.

  
[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md



<!--HONumber=Jun16_HO2-->


