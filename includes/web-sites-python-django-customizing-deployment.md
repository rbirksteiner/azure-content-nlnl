Azure bepaalt dat uw toepassing Python gebruikt **als beide volgende voorwaarden waar zijn**:

- bestand requirements.txt in de hoofdmap
- elk bestand .py in de hoofdmap OF een runtime.txt die Python specificeert

Wanneer dit het geval is, wordt een implementatiescript gebruikt dat specifiek is voor Python en waarmee de standaard synchronisatie van bestanden wordt uitgevoerd, evenals extra Python-bewerkingen zoals:

- Automatisch beheer van virtuele omgeving
- Installatie van pakketten die zijn vermeld in requirements.txt met behulp van pip
- Maken van de juiste web.config op basis van de geselecteerde Python-versie
- Verzamelen van statische bestanden voor Django-toepassingen

U kunt bepaalde aspecten van de standaard implementatiestappen beheren zonder dat u het script hoeft aan te passen.

Als u alle stappen die specifiek zijn voor de implementatie van Python wilt overslaan, kunt u dit lege bestand maken:

    \.skipPythonDeployment

Als u het verzamelen van statische bestanden voor uw Django-toepassing wilt overslaan:

    \.skipDjango 

Voor meer controle over de implementatie kunt u het standaard implementatiescript overschrijven door de volgende bestanden te maken:

    \.deployment
    \deploy.cmd

U kunt de [Azure-opdrachtregelinterface][] gebruiken om de bestanden te maken.  Gebruik deze opdracht uit vanuit de projectmap:

    azure site deploymentscript --python

Als deze bestanden niet bestaan, maakt Azure een tijdelijk implementatiescript en voert dit script uit.  Het is identiek aan het script dat u met de bovenstaande opdracht maakt.

[Azure-opdrachtregelinterface]: http://azure.microsoft.com/downloads/



<!--HONumber=Jun16_HO2-->


