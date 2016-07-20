Azure bepaalt de versie van Python die moet worden gebruikt voor de virtuele omgeving met de volgende prioriteit:

1. versie die is opgegeven in runtime.txt in de hoofdmap
1. versie die is opgegeven door de Python-instelling in de configuratie van de web-app (de blade **Instellingen** > **Toepassingsinstellingen** voor uw web-app in de Azure Portal)
1. python-2.7 is de standaardwaarde als geen van de bovenstaande zijn opgegeven

Geldige waarden voor de inhoud van 

    \runtime.txt

zijn:

- python-2.7
- python-3.4

Als de microversie (derde cijfer) is opgegeven, wordt dit genegeerd.



<!--HONumber=Jun16_HO2-->


