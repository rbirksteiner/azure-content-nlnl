# Azure en internet der dingen (IoT)

Welkom bij Microsoft Azure en het internet der dingen (IoT). In dit artikel maakt u kennis met een architectuur voor een IoT-oplossing waarin de algemene eigenschappen van een IoT-oplossing worden beschreven die u zou kunnen implementeren met Azure-services. IoT-oplossingen vereisen een beveiligde communicatie tussen mogelijk miljoenen apparaten in twee richtingen, evenals een back-end van de oplossing die bijvoorbeeld gebruikmaakt van geautomatiseerde, voorspellende analyses om inzichten bloot te leggen over de gebeurtenisstroom van het apparaat naar de cloud.

Azure IoT Hub is een essentiële bouwsteen bij het implementeren van de architectuur voor deze IoT-oplossing, en IoT Suite biedt volledige end-to-end-implementaties van deze architectuur voor specifieke IoT-scenario's. Zo kunt u met de *externe bewakingsoplossing* de status van apparaten, zoals snoep- en drankautomaten controleren, helpt *voorspeld onderhoud* u om te anticiperen op onderhoudsbehoeften van apparaten zoals pompen in verafgelegen gemalen, en wordt ongeplande uitvaltijd voorkomen.

## Architectuur voor een IoT-oplossing

Het volgende diagram toont een typische architectuur voor een IoT-oplossing. Houd er rekening mee dat dit diagram niet de namen van de specifieke Azure-services bevat, maar de belangrijkste elementen in een algemene architectuur voor een IoT-oplossing beschrijft. In deze architectuur verzamelen IoT-apparaten gegevens die ze naar een cloudgateway verzenden. De cloudgateway maakt de gegevens beschikbaar voor verwerking door andere back-endservices waarvan de gegevens bij andere Line-Of-Business-toepassingen of menselijke operators via een dashboard of een ander apparaat voor presentatie worden afgeleverd.

![Architectuur voor een IoT-oplossing][img-solution-architecture]

> [AZURE.NOTE] Zie [Microsoft Azure IoT Reference Architecture][lnk refarch] (Referentiearchitectuur voor Microsoft Azure IoT Ink-refarch) voor een gedetailleerdere beschrijving van de IoT-architectuur.

### Connectiviteit van apparaten

In deze architectuur voor een IoT-oplossing verzenden apparaten telemetriegegevens, zoals sensormetingen van een gemaal, naar een cloudeindpunt om daar te worden opgeslagen en verwerkt. In een scenario voor voorspeld onderhoud gebruikt de back-end de stroom van sensorgegevens mogelijk om vast te stellen of er aan een pomp onderhoud moet worden uitgevoerd. Apparaten kunnen ook opdrachten die van de cloud naar het apparaat zijn verstuurd, ontvangen en erop reageren door de berichten afkomstig van een cloudeindpunt te lezen. Zo kan in een scenario voor voorspeld onderhoud door de back-end van de oplossing opdrachten worden verzonden naar andere pompen in een gemaal, zodat de waterstromen net voordat de onderhoudswerkzaamheden van start gaan, kunnen worden omgeleid en de onderhoudstechnicus meteen aan de slag kan als hij aankomt.

Een van de grootste uitdagingen waarvoor IoT-projecten zich gesteld zien, is hoe apparaten op een betrouwbare en beveiligde manier kunnen worden verbonden met de back-end van de oplossing zodat het apparaat telemetriegegevens kan verzenden en opdrachten kan ophalen. Vergeleken met andere clients zoals browsers en mobiele apps hebben IoT-apparaten andere kenmerken. IoT-apparaten:

- Zijn vaak ingesloten systemen waarbij geen menselijke operator komt kijken.
- Kunnen worden geïmplementeerd op verafgelegen locaties, waar fysieke toegang bijzonder kostbaar is.
- Kunnen mogelijk alleen worden bereikt via de back-end van de oplossing. Er is geen andere manier om te communiceren met het apparaat.
- Hebben mogelijk een beperkt vermogen en een beperkt aantal verwerkingsbronnen.
- Hebben mogelijk een onstabiele, trage of dure netwerkverbinding.
- Vereisen mogelijk het gebruik van bedrijfseigen, aangepaste of branchespecifieke toepassingsprotocollen.
- Kunnen worden gemaakt met behulp van een groot aantal populaire hardware en softwareplatforms.

Naast de bovenstaande vereisten moet een IoT-oplossing schaalbaar, veilig en betrouwbaar zijn. De resulterende reeks connectiviteitsvereisten zijn moeilijk te implementeren en kost veel tijd als er gebruik wordt gemaakt van conventionele technologieën zoals webcontainers en berichtenbrokers. De SDK's van Azure IoT-Hub en IoT Device maken het gemakkelijker om oplossingen te implementeren die aan deze vereisten voldoen.

Een apparaat kan rechtstreeks communiceren met een eindpunt van een cloudgateway of als het apparaat geen van de communicatieprotocollen kan gebruiken die door de cloudgateway wordt ondersteund, kan het verbinding maken via een tussenliggende gateway, zoals de [IoT-Hub-protocolgateway][lnk-protocolgateway], die protocollen vertaalt. Bijvoorbeeld van CIP (Common Industrial Protocol) naar AMQPS.

### Gegevensverwerking en -analyse

In de cloud vindt het grootste gedeelte van de gegevensverwerking plaats in de back-end van de IoT-oplossing, in het bijzonder waar het gaat om het filteren en samenvoegen van telemetriegegevens, en om deze naar andere services door te sturen. De back-end van de IoT-oplossing:

- Ontvangt van uw apparaten op schaal de telemetriegegevens en bepaalt hoe die gegevens moeten worden verwerkt en opgeslagen. 
- Kan het voor u mogelijk maken om opdrachten te verzenden van de cloud naar een specifiek apparaat.
- Biedt mogelijkheden voor het registreren van uw apparaten waardoor u apparaten kunt inrichten en kunt beheren welke apparaten er verbinding mogen maken met uw infrastructuur.
- Maakt het voor u mogelijk om de status van uw apparaten bij te houden en hun activiteiten te volgen.

In het scenario voor voorspeld onderhoud slaat de back-end van de oplossing historische telemetriegegevens op die vervolgens worden gebruikt om patronen te identificeren en telemetriegegevens te analyseren zodra deze binnenkomen. Op die manier kunnen de patronen worden herkend die aangeven dat onderhoud van een specifieke pomp noodzakelijk is.

IoT-oplossingen kunnen automatische feedbacklussen omvatten. Zo kan een analysemodule in de back-end op grond van de telemetriegegevens bijvoorbeeld zien dat de temperatuur van een specifiek apparaat hoger is dan de normale bedrijfstemperatuurniveaus, en vervolgens een opdracht naar het apparaat verzenden met de instructie om corrigerende actie te ondernemen.

### Presentatie en bedrijfsconnectiviteit

De laag voor presentatie en bedrijfsconnectiviteit stelt eindgebruikers in staat te communiceren met de IoT-oplossing en de apparaten. Gebruikers kunnen met behulp hiervan de gegevens bekijken en analyseren die van hun apparaten zijn verzameld. Deze weergaven kunnen de vorm hebben van dashboards of BI-rapporten die zowel historische gegevens als gegevens in bijna realtime kunnen weergeven. Een operator kan bijvoorbeeld de status van een bepaald gemaal controleren en zien of het systeem eventueel waarschuwingen heeft gegeven. Deze laag maakt ook integratie mogelijk van de back-end van de IoT-oplossing met bestaande line-of-business-toepassingen zodat deze in bedrijfsprocessen of werkstromen kunnen worden ingevoegd. De oplossing voor voorspeld onderhoud kan bijvoorbeeld worden geïntegreerd met een werkroostersysteem dat op het moment dat de oplossing een pomp identificeert waaraan onderhoud moet worden uitgevoerd, een technicus boekt die naar het gemaal met de desbetreffende pomp moet gaan.

![Dashboard van de IoT-oplossing][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocolgateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf



<!--HONumber=Jun16_HO2-->


