<properties
    pageTitle="Aan de slag met vooraf geconfigureerde oplossingen | Microsoft Azure"
    description="Volg deze zelfstudie voor meer informatie over het implementeren van een vooraf geconfigureerde Azure IoT Suite-oplossing."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/25/2016"
     ms.author="dobett"/>

# Zelfstudie: Aan de slag met vooraf geconfigureerde oplossingen

## Inleiding

[Vooraf geconfigureerde oplossingen][lnk-preconfigured-solutions] voor Azure IoT-Suite combineren meerdere Azure IoT-services om totaaloplossingen te leveren die algemene IoT-bedrijfsscenario's implementeren.

In deze zelfstudie leert u hoe de vooraf geconfigureerde oplossing voor *externe controle* inricht. Hierbij maakt u kennis met de basisfuncties van de vooraf geconfigureerde oplossing voor externe controle.

Om deze zelfstudie te voltooien, hebt u een actief Azure-abonnement  nodig.

> [AZURE.NOTE]  Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure][lnk_free_trial] voor meer informatie.

## De vooraf geconfigureerde oplossing voor externe controle inrichten

1.  Meld u aan bij [azureiotsuite.com][lnk azureiotsuite] met de referenties van uw Azure-account en klik op **+** om een nieuwe oplossing te maken.

    > [AZURE.NOTE] Als er problemen zijn met de vereiste machtigingen voor het inrichten van een oplossing, leest u de richtlijnen in [Permissions on the azureiotsuite.com site][lnk-permissions] (Machtigingen op de site azureiotsuite.com).

2.  Klik op de tegel **Externe controle** op **Selecteren**.

3.  Voer een **oplossingsnaam** in voor uw vooraf geconfigureerde oplossing voor externe controle.

4.  Selecteer de **regio** die en het **abonnement** dat u wilt gebruiken voor het inrichten van de oplossing.

5.  Klik op **Oplossing maken** om het inrichtingsproces te starten. Doorgaans duurt het enkele minuten om dit proces uit te voeren.

## Wacht tot het inrichtingsproces is voltooid.

1. Klik op de tegel voor uw oplossing met de status **Inrichten**.
 
2. Tijdens de implementatie van Azure-services in uw Azure-abonnement verschijnen verschillende **inrichtingstatuswaarden**.

3. Nadat het inrichten is voltooid, verandert de status in **Gereed**.

4. Klik op de tegel, waarna de details van uw oplossing in het rechterdeelvenster worden weergegeven.

> [AZURE.NOTE] Als er problemen zijn met de implementatie van de vooraf geconfigureerde oplossing, leest u [Permissions on the azureiotsuite.com site][lnk-permissions] (Machtigingen op de site azureiotsuite.com) en de [veelgestelde vragen][lnk-faq]. Als de problemen zich blijven voordoen, maakt u een serviceticket op de [portal][lnk-portal].

Zijn er voor uw oplossing bepaalde details niet vermeld, die u wel verwacht had te zien? Geef ons suggesties voor functies op [User Voice](https://feedback.azure.com/forums/321918-azure-iot).

## Dashboard van oplossing voor externe controle weergeven

Vanaf het dashboard van de oplossing kunt u de geïmplementeerde oplossing beheren. U kunt er bijvoorbeeld telemetrie weergeven, apparaten toevoegen en regels configureren.

1.  Wanneer het inrichten is voltooid en voor de tegel voor uw vooraf geconfigureerde oplossing de status **Gereed** wordt weergegeven, klikt u op **Starten** om uw oplossingsportal voor externe controle te openen in een nieuw tabblad.

    ![][img-launch-solution]

2.  Standaard ziet u in de oplossingsportal het *dashboard van de oplossing*. In het menu links kunt u andere weergaven selecteren.

    ![][img-dashboard]

Het dashboard bevat de volgende informatie:

- De kaart toont de locatie van elk apparaat dat met de oplossing is verbonden. Wanneer u de oplossing voor het eerst uitvoert, zijn er vier gesimuleerde apparaten. De gesimuleerde apparaten worden geïmplementeerd als Azure WebJobs en de oplossing gebruikt de API van Bing Kaarten om informatie op de kaart te tekenen.
- Het deelvenster **Telemetriegeschiedenis** tekent de vochtigheids- en temperatuurtelemetrie van een geselecteerd apparaat in bijna realtime en geeft statistische gegevens weer, zoals de maximale, minimale en gemiddelde vochtigheid.
- Het deelvenster **Geschiedenis van waarschuwingen** toont recente waarschuwingsgebeurtenissen wanneer voor een telemetriewaarde een drempelwaarde wordt overschreden. Naast de voorbeelden die met de vooraf geconfigureerde oplossing zijn gemaakt, kunt u ook uw eigen alarmen definiëren.

## Lijst met apparaten van de oplossing weergeven

De lijst met apparaten bevat alle geregistreerde apparaten in de oplossing. U kunt metagegevens van apparaten weergeven en bewerken, apparaten toevoegen of verwijderen en opdrachten naar apparaten verzenden.

1.  Klik links in het menu op **Apparaten** om de *lijst met apparaten* voor deze oplossing weer te geven.

    ![][img-devicelist]

2.  De lijst met apparaten geeft aan dat bij het inrichtingsproces vier gesimuleerde apparaten zijn gemaakt.

3.  Klik in de lijst op een apparaat om de details ervan weer te geven.

    ![][img-devicedetails]

Het deelvenster **Apparaatdetails** bevat drie secties:

- In de sectie **Acties** vindt u de acties die u voor het apparaat kunt uitvoeren. Als u het apparaat uitschakelt, kan dit geen telemetrie meer verzenden of opdrachten ontvangen. Als u een apparaat uitschakelt, kunt u het later opnieuw inschakelen. U kunt een regel toevoegen die is gekoppeld aan het apparaat dat een waarschuwing activeert wanneer voor een telemetriewaarde de drempelwaarde wordt overschreden. U kunt ook een opdracht verzenden naar een apparaat. Wanneer een apparaat voor het eerst verbinding maakt, geeft het aan de oplossing door op welke opdrachten deze kan reageren.
- In de sectie **Apparaateigenschappen** vindt u de metagegevens van apparaten. Sommige van deze metagegevens zijn afkomstig van het apparaat zelf (zoals de fabrikant). Enkele ervan worden gegenereerd door de oplossing (zoals de aanmaaktijd). U kunt de metagegevens van apparaten hier bewerken.
- In de sectie **Verificatiesleutels** vindt u de sleutels die het apparaat kan gebruiken om met de oplossing te verifiëren.

## Een opdracht naar een apparaat verzenden

Het deelvenster Apparaatdetails bevat alle opdrachten die een specifiek apparaat ondersteunt en biedt u de mogelijkheid om opdrachten naar een apparaat te verzenden. Wanneer een apparaat voor het eerst wordt gestart, stuurt het naar de oplossing informatie over de opdrachten die het apparaat ondersteunt.

1.  Klik in het deelvenster Apparaatdetails op **Opdrachten** voor het geselecteerde apparaat.

    ![][img-devicecommands]

2.  Selecteer **PingDevice** in de lijst met opdrachten.

3.  Klik op **Opdracht verzenden**.

4.  U ziet de status van de opdracht in de opdrachtgeschiedenis.

    ![][img-pingcommand]

De oplossing houdt de status van elke opdracht bij die met de oplossing wordt verzonden. In eerste instantie is het resultaat **In behandeling**. Wanneer het apparaat meldt dat het de opdracht heeft uitgevoerd, wordt het resultaat ingesteld op **Geslaagd**.

## Een nieuw gesimuleerd apparaat toevoegen

1.  Ga terug naar de lijst met apparaten.

2.  Klik linksonder op **+ Een apparaat toevoegen** om een nieuw apparaat toe te voegen.

    ![][img-adddevice]

3.  Klik op **Nieuwe toevoegen** op de tegel **Gesimuleerd apparaat**.

    ![][img-addnew]
    
    U kunt niet alleen een nieuw gesimuleerd apparaat maken, maar ook een fysiek apparaat toevoegen als u ervoor kiest om een **aangepast apparaat** te maken. Zie [Connect your device to the IoT Suite remote monitoring preconfigured solution][lnk-connecting-devices] (Uw apparaat aansluiten op de vooraf geconfigureerde IoT Suite-oplossing voor externe controle) voor meer informatie hierover.

4.  Selecteer **Laat mij mijn eigen apparaat-id definiëren** en voer de unieke id van een apparaatnaam in, zoals **mijnapparaat_01**.

5.  Klik op **Maken**.

    ![][img-definedevice]

6. Klik in stap 3 van **Een gesimuleerd apparaat toevoegen** op **Gereed** om terug te keren naar de lijst met apparaten.

7. In de lijst met apparaten kunt u zien dat uw apparaat **wordt uitgevoerd**.

    ![][img-runningnew]

8. U kunt op het dashboard ook de gesimuleerde telemetrie van het nieuwe apparaat bekijken:

    ![][img-runningnew-2]

## De metagegevens van een apparaat bewerken

1.  Ga terug naar de lijst met apparaten.

2.  Selecteer het nieuwe apparaat in de **lijst met apparaten** en klik daarna op **Bewerken** om de **apparaateigenschappen** te bewerken:

    ![][img-editdevice]

3. Schuif omlaag en wijzig de waarden voor breedtegraad en lengtegraad. Klik daarna op **Wijzigingen aan het apparaatregister opslaan**.

    ![][img-editdevice2]

4. Ga terug naar het dashboard. De locatie van het apparaat op de kaart is gewijzigd:

    ![][img-editdevice3]

## Een regel voor het nieuwe apparaat toevoegen

Er zijn geen regels voor het nieuwe apparaat dat u zojuist hebt toegevoegd. In deze sectie voegt u een regel toe die een waarschuwing activeert wanneer de door het nieuwe apparaat gemelde temperatuur 47 graden overschrijdt. Voordat u begint, kunt u al zien dat de telemetriegeschiedenis voor het nieuwe apparaat op het dashboard aantoont dat de temperatuur van het apparaat nooit meer dan 45 graden bedraagt.

1.  Ga terug naar de lijst met apparaten.

2.  Selecteer het nieuwe apparaat in de **lijst met apparaten** en klik daarna op **Regel toevoegen** om een nieuwe regel voor het apparaat toe te voegen.

3. Maak een regel die **Temperatuur** als gegevensveld gebruikt en **AlarmTemp** gebruikt als de uitvoer wanneer de temperatuur 47 graden overschrijdt:

    ![][img-adddevicerule]

4. Klik op **Regels opslaan en weergeven** om uw wijzigingen op te slaan.

5.  Klik op **Opdrachten** in het deelvenster Apparaatdetails voor het nieuwe apparaat.

    ![][img-adddevicerule2]

6.  Selecteer **ChangeSetPointTemp** in de opdrachtlijst en stel **SetPointTemp** in op 45. Klik daarna op **Opdracht verzenden**:

    ![][img-adddevicerule3]

7.  Ga terug naar het dashboard van de oplossing. Na korte tijd verschijnt in het deelvenster **Geschiedenis van waarschuwingen** een nieuwe vermelding wanneer de door het nieuwe apparaat gemelde temperatuur 47 graden overschrijdt:

    ![][img-adddevicerule4]

8. Op de pagina **Regels** van het dashboard kunt u al uw regels bekijken en bewerken:

    ![][img-rules]

9. Op de pagina **Acties** van het dashboard kunt u alle acties die worden uitgevoerd in reactie op een regel bekijken en bewerken:

    ![][img-actions]

> [AZURE.NOTE] Het is mogelijk om acties te definiëren die een e-mailbericht of sms kunnen verzenden in antwoord op een regel of die met een Line-Of-Business-systeem kunnen worden geïntegreerd via een [logische app][lnk-logic-apps].

## Achter de schermen

Wanneer u een vooraf geconfigureerde oplossing implementeert, maakt het implementatieproces meerdere resources in het door u geselecteerde Azure-abonnement. U kunt deze resources weergeven in Azure [Portal][lnk-portal]. Het implementatieproces maakt een **resourcegroep** met een naam die is gebaseerd op de naam die u voor uw vooraf geconfigureerde oplossing hebt gekozen:

![][img-portal]

U kunt de instellingen van elke resource weergeven door deze te selecteren in de lijst met resources in de resourcegroep. In de bovenstaande schermafbeelding ziet u de instellingen voor de IoT-hub die in de vooraf geconfigureerde oplossing worden gebruikt.

U kunt ook de broncode voor de vooraf geconfigureerde oplossing weergeven. De broncode van de vooraf geconfigureerde oplossing voor externe controle bevindt zich in [azure-iot-remote-monitoring][lnk-rmgithub]:

- De map **DeviceAdministration** bevat de broncode voor het dashboard.
- De map **Simulator** bevat de broncode voor het gesimuleerde apparaat.
- De map **EventProcessor** bevat de broncode voor het back-endproces dat de binnenkomende telemetrie verwerkt.

Als u klaar bent, kunt u de vooraf geconfigureerde oplossing verwijderen uit uw Azure-abonnement op de site [azureiotsuite.com][lnk azureiotsuite]. Zo kunt u eenvoudig alle resources verwijderen die zijn ingericht bij het maken van de vooraf geconfigureerde oplossing.

> [AZURE.NOTE] Om ervoor te zorgen dat u alles met betrekking tot de vooraf geconfigureerde oplossing verwijdert, verwijdert u niet gewoon de resourcegroep uit de portal, maar verwijdert u de oplossing uit [azureiotsuite.com][lnk azureiotsuite].

## Volgende stappen

Nu dat u een werkende vooraf geconfigureerde oplossing hebt gemaakt, kunt u verder gaan met de volgende leidraden:

-   [Guidance on customizing preconfigured solutions][lnk-customize] (Instructies voor het aanpassen van vooraf geconfigureerde oplossingen)
-   [Overzicht van de vooraf geconfigureerde oplossing voor voorspeld onderhoud][lnk-predictive]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md
[lnk-connecting-devices]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-faq]: iot-suite-faq.md



<!--HONumber=Jun16_HO2-->


