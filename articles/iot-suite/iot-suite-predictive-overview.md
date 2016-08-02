<properties
 pageTitle="Voorspeld onderhoud vooraf geconfigureerde oplossing | Microsoft Azure"
 description="Een beschrijving van de vooraf geconfigureerde oplossing van Azure IoT voor voorspeld onderhoud."
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
 ms.date="05/24/2016"
 ms.author="stevehob"/>

# Overzicht van de vooraf geconfigureerde oplossing voor voorspeld onderhoud

De vooraf geconfigureerde oplossing voor *voorspeld onderhoud* is een van de [vooraf geconfigureerde oplossingen][lnk_preconfigured_solutions] die is uitgebracht als onderdeel van [Microsoft Azure IoT Suite][lnk_iot_suite]. Deze oplossing integreert het verzamelen van telemetriegegevens in realtime met een voorspellend model dat is gemaakt met behulp van [Azure Machine Learning][lnk_machine_learning].


Met Azure IoT Suite zijn bedrijven snel in staat om eenvoudig verbinding te maken met assets en deze te controleren, en om gegevens in realtime te analyseren. De vooraf geconfigureerde oplossing voor voorspeld onderhoud neemt de gegevens en gebruikt uitgebreide dashboards en visualisaties om bedrijven nieuwe bedrijfsinformatie te bieden waardoor deze efficiënter kunnen werken en meer inkomsten kunnen genereren.

## Het scenario

Fabrikam is een regionale luchtvaartmaatschappij die zich toelegt op het leveren van een uitstekende klantervaring tegen concurrerende prijzen. Een oorzaak van vertragingen zijn onderhoudsproblemen, waarbij het onderhoud van vliegtuigmotoren een bijzondere uitdaging vormt. Dat er problemen met motoren ontstaan tijdens de vlucht moet koste wat kost worden voorkomen. Fabrikam inspecteert om die reden regelmatig de motoren en houdt zich aan een strikte planning voor het plegen van onderhoud. Vliegtuigmotoren slijten echter niet allemaal even snel. Soms wordt er onnodig onderhoud uitgevoerd op motoren. En wat belangrijker is, er doen zich soms problemen voor die ervoor zorgen dat een vliegtuig aan de grond moet blijven totdat het onderhoud is uitgevoerd. Dit heeft kostbare vertragingen tot gevolg, in het bijzonder als een vliegtuig zich op een locatie bevindt waar de juiste technici of onderdelen niet beschikbaar zijn.

De motoren van de vliegtuigen van Fabrikam zijn uitgerust met sensoren die de toestand van de motor tijdens de vlucht in de gaten houden. Fabrikam gebruikt Azure IoT Suite voor het verzamelen van de sensorgegevens die zijn verzameld tijdens de vlucht. Na jarenlang operationele gegevens van engines te hebben verzameld, hebben de gegevensanalisten van Fabrikam een model samengesteld waarmee ze de nog resterende bruikbare levensduur van een vliegtuigmotor kunnen voorspellen. Ze hebben een correlatie ontdekt tussen de gegevens van vier van de motorsensoren en de slijtage van de motor die uiteindelijk tot problemen leidt. Terwijl Fabrikam doorgaat met het uitvoeren van regelmatige inspecties om de veiligheid te garanderen, kan het bedrijf nu de modellen gebruiken om na elke vlucht de resterende bruikbare levensduur van elke motor te berekenen met behulp van de telemetriegegevens die tijdens de vlucht over de motor zijn verzameld. Fabrikam is nu in staat om toekomstige probleempunten te voorspellen en om van tevoren onderhoud en reparatiewerkzaamheden te plannen.

> [AZURE.NOTE] Het model van de oplossing maakt gebruik van de reële slijtagegegevens van de motor.

Door het punt te voorspellen waarop onderhoud vereist is, kan Fabrikam zijn activiteiten zo optimaliseren dat de kosten worden verminderd. Onderhoudscoördinatoren plannen samen met planners het onderhoud op een moment dat een vliegtuig op een bepaalde locatie stopt, en ze ruimen voldoende tijd in dat het vliegtuig buiten gebruik is zonder dat het tijdschema in de war wordt gestuurd. Fabrikam kan technici overeenkomstig inplannen en zorgt ervoor dat het onderhoud aan het vliegtuig efficiënt wordt uitgevoerd zonder dat er vertragingen ontstaan. Magazijnbeheerders ontvangen de onderhoudsplannen zodat zij hun bestelproces en hun voorraad met reserveonderdelen kunnen optimaliseren. Door dit alles is Fabrikam in staat om de tijd die vliegtuigen aan de grond staan te minimaliseren, de operationele kosten te verlagen en kan het bedrijf de veiligheid van de passagiers en bemanning garanderen.

Als u beter wilt begrijpen welke mogelijkheden [Azure IoT Suite][lnk_iot_suite] biedt die klanten nodig hebben om het potentieel van voorspeld onderhoud te realiseren, bekijk dan deze [infographic][lnk_infographic].

## Hoe de oplossing voor voorspeld onderhoud is gebouwd

De oplossing gebruikt een bestaand Azure Machine Learning-model dat als sjabloon beschikbaar is om te laten zien hoe deze mogelijkheden werken op basis van telemetriegegevens die zijn verzameld via IoT Suite-services. Microsoft heeft een [regressiemodel][lnk_regression_model] van een vliegtuigmotor en de volledige sjabloon, data<sup>\[1\]</sup>, gepubliceerd evenals richtlijnen met stappen voor het gebruik van het model.

De vooraf geconfigureerde oplossing voor voorspeld onderhoud Azure IoT maakt gebruik van het regressiemodel dat is gemaakt op basis van deze sjabloon. Het is geïmplementeerd in uw Azure-abonnement en is toegankelijk met behulp van een automatisch gegenereerde API. De oplossing omvat een subset met de testgegevens voor 4 (van in totaal 100) motoren en de 4 (van in totaal 21) gegevensstromen van sensoren die een accuraat resultaat opleveren van het getrainde model.

*\[1\] A. Saxena en K. Goebel (2008). 'Turbofan Engine Degradation Simulation Data Set', NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## Volgende stappen

Meer informatie hoe Azure IoT werkt in scenario's voor voorspeld onderhoud [Capture value from the Internet of Things][lnk_capture_value] (Waarde toevoegen via het internet der dingen).

Neem een [walkthrough][lnk-predictive-walkthrough] door over een vooraf geconfigureerde oplossing voor voorspeld onderhoud.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/en-us/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF



<!--HONumber=Jun16_HO2-->


