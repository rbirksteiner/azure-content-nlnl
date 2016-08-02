Het implementatiescript maakt geen virtuele omgeving op Azure als wordt gedetecteerd dat er al een compatibele virtuele omgeving bestaat.  Dit kan de implementatie aanzienlijk versnellen.  Pakketten die al zijn geïnstalleerd, worden door pip overgeslagen.

In bepaalde situaties wilt u die virtuele omgeving mogelijk geforceerd verwijderen.  Bijvoorbeeld als u besluit dat u een virtuele omgeving wilt opnemen als onderdeel van uw opslagplaats.  Een ander voorbeeld kan zijn als u bepaalde pakketten kwijt wilt, of als u wijzigingen in requirements.txt wilt testen.

Er zijn een aantal opties voor het beheren van de bestaande virtuele omgeving op Azure:

### Optie 1: FTP gebruiken

Maak verbinding met de server met een FTP-client, waarna u de map env kunt verwijderen.  Houd er rekening mee dat sommige FTP-clients (zoals webbrowsers) alleen-lezen kunnen zijn. Bij dergelijke FTP-clients kunt u geen mappen verwijderen. Zorg er daarom voor dat u een FTP-client gebruikt die wel die mogelijkheid biedt.  De FTP-hostnaam en de gebruiker worden weergegeven in de blade van uw web-app op de [Azure Portal](https://portal.azure.com).

### Optie 2: runtime wisselen

Bij dit alternatief wordt gebruikgemaakt van het feit dat het implementatiescript de map env verwijdert wanneer deze niet overeenkomt met de gewenste versie van Python.  Hierdoor wordt de bestaande omgeving effectief verwijderd en een nieuwe omgeving gemaakt.

1. Schakel over naar een andere versie van Python (via runtime.txt of de blade **Toepassingsinstellingen** in de Azure Portal)
1. Git push enkele wijzigingen (negeer pip-installatiefouten indien aanwezig)
1. Ga terug naar de oorspronkelijke versie van Python
1. Git push sommige wijzigingen opnieuw

### Optie 3: implementatiescript aanpassen

Als u het implementatiescript hebt aangepast, kunt u de code in deploy.cmd wijzigen om deze te dwingen de map env te verwijderen.



<!--HONumber=Jun16_HO2-->


