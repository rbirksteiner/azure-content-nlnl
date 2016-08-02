Sommige pakketten kunnen niet worden geïnstalleerd met pip wanneer ze worden uitgevoerd op Azure.  Dit kan eenvoudig komen doordat het pakket niet beschikbaar is op de Python Package Index.  Mogelijk is een compiler vereist (een compiler is niet beschikbaar op de computer waarop de web-app in de Azure App Service wordt uitgevoerd).

In deze sectie zullen we kijken naar manieren om dit probleem op te lossen.

### Wheels aanvragen

Als de pakketinstallatie een compiler vereist, dient u contact op te nemen met de eigenaar van het pakket om te vragen of wheels beschikbaar gesteld kunnen worden voor het pakket.

Met de recente beschikbaarheid van [Microsoft Visual C++ Compiler voor Python 2.7][] is het nu eenvoudiger geworden om pakketten met systeemeigen code voor Python 2.7 te bouwen.

### Wheels bouwen (vereist Windows)

Opmerking: wanneer u deze optie gebruikt, dient u ervoor te zorgen dat u het pakket compileert met behulp van een Python-omgeving die overeenkomt met het platform of de architectuur of versie die wordt gebruikt op de web-app in Azure App Service (Windows/32-bits/2.7 of 3.4).

Als het pakket niet wordt geïnstalleerd omdat het een compiler vereist, kunt u de compiler installeren op uw lokale computer en een wheel bouwen voor het pakket dat u vervolgens kunt opnemen in de opslagplaats.

Gebruikers van Mac-/ Linux: als u geen toegang hebt tot een Windows-computer, raadpleegt u [Een virtuele machine met Windows maken][] voor het maken van een VM op Azure.  U kunt deze gebruiken om de wheels te bouwen, deze toevoegen aan de opslagplaats en de VM desgewenst verwijderen. 

Voor Python 2.7 kunt u [Microsoft Visual C++ Compiler voor Python 2.7][] installeren.

Voor Python 3.4 kunt u [Microsoft Visual C++ 2010 Express][] installeren.

Om wheels te bouwen, hebt u het wheel-pakket nodig:

    env\scripts\pip install wheel

U gebruikt `pip wheel` voor het compileren van een afhankelijkheid:

    env\scripts\pip wheel azure==0.8.4

Hierdoor wordt een .whl-bestand gemaakt in de map \wheelhouse.  Voeg de map \wheelhouse en de wheel-bestanden toe aan uw opslagplaats.

Voeg de optie `--find-links` bovenaan toe in requirements.txt. Hierdoor krijgt pip de opdracht te zoeken naar een exacte overeenkomst in de lokale map voordat hij naar de Python Package Index gaat.

    --find-links wheelhouse
    azure==0.8.4

Als u alle afhankelijkheden wilt opnemen in de map \wheelhouse en de Python Package Index helemaal niet wilt gebruiken, kunt u pip dwingen de Package Index te negeren door `--no-index` bovenaan toe te voegen in requirements.txt.

    --no-index

### Installatie aanpassen

U kunt het implementatiescript aanpassen om een pakket te installeren in de virtuele omgeving met behulp van een alternatief installatieprogramma zoals easy\_install.  Zie deploy.cmd voor een voorbeeld met opmerkingen.  Zorg ervoor dat dergelijke pakketten niet zijn opgenomen in requirements.txt om te voorkomen dat pip ze installeert.

Voeg dit toe aan het implementatiescript:

    env\scripts\easy_install somepackage

U kunt easy\_install mogelijk ook gebruiken om te installeren vanuit een exe-installatieprogramma (sommige zijn zip-compatibel en worden dus ondersteund door easy\_install).  Voeg het installatieprogramma toe aan uw opslagplaats en roep easy\_install aan door het pad door te geven aan het uitvoerbare bestand.

Voeg dit toe aan het implementatiescript:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### De virtuele omgeving opnemen in de opslagplaats (vereist Windows)

Opmerking: wanneer u deze optie gebruikt, dient u ervoor te zorgen dat u een virtuele omgeving gebruikt die overeenkomst met het platform of de architectuur of versie die wordt gebruikt op de web-app in Azure App Service (Windows/32-bits/2.7 of 3.4).

Als u de virtuele omgeving opneemt in de opslagplaats, kunt u voorkomen u dat het implementatiescript op Azure beheeractiviteiten uitvoert bij de virtuele omgeving door een leeg bestand te maken:

    .skipPythonDeployment

Het is raadzaam dat u de bestaande virtuele omgeving op de app verwijdert, om te voorkomen dat er bestanden overblijven uit de periode dat de virtuele omgeving automatisch werd beheerd.


[Een virtuele machine met Windows maken]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ Compiler voor Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949



<!--HONumber=Jun16_HO2-->


