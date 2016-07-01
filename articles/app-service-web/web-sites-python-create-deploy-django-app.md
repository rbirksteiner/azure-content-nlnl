<properties
    pageTitle="Web-apps maken met Django in Azure"
    description="Een zelfstudie waarin u leert hoe u een Python-web-app uitvoert in Azure App Service Web Apps."
    services="app-service\web"
    documentationCenter="python"
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article" 
    ms.date="02/19/2016"
    ms.author="huvalo"/>


# Web-apps maken met Django in Azure

In deze zelfstudie wordt beschreven hoe u Python uitvoert in [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Web Apps biedt beperkte gratis hosting en snelle implementatie. Bovendien kunt u Python gebruiken. Naarmate uw app groeit, kunt u overstappen op betaalde hosting én profiteren van integratie met alle overige Azure-services.

U maakt een toepassing op basis van het Django-webframework (zie andere versies van deze zelfstudie voor [Flask](web-sites-python-create-deploy-flask-app.md) en [Bottle](web-sites-python-create-deploy-bottle-app.md)). U maakt de web-app via Azure Marketplace, u configureert Git-implementatie en u kloont de opslagplaats lokaal. Vervolgens voert u de toepassing lokaal uit, brengt u wijzigingen aan, voert u deze door en pusht u ze naar Azure. In de zelfstudie ziet u hoe u dit doet in Windows of Mac/Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Als u aan de slag wilt met Azure App Service voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](http://go.microsoft.com/fwlink/?LinkId=523751). Hier kunt u direct een tijdelijke web-app maken in App Service. U hebt geen creditcard nodig en u gaat geen verplichtingen aan.


## Vereisten

- Windows, Mac of Linux
- Python 2.7 of 3.4
- setuptools, pip, virtualenv (alleen Python 2.7)
- Git
- [Python Tools for Visual Studio][] (PTVS) - Opmerking: dit is optioneel

**Opmerking**: TFS-publicatie wordt momenteel niet ondersteund voor Python-projecten.

### Windows

Als u Python 2.7 of 3.4 nog niet hebt geïnstalleerd (32-bits), wordt aangeraden om de [Azure SDK voor Python 2.7] of de [Azure SDK voor Python 3.4] te installeren via het webplatforminstallatieprogramma. Hiermee installeert u de 32-bits versie van Python, setuptools, PIP, virtualenv, enzovoort (32-bits Python wordt geïnstalleerd op de Azure-hostmachines). U kunt Python ook downloaden via [python.org].

Voor Git wordt [Git voor Windows] of [GitHub voor Windows] aangeraden. Als u Visual Studio gebruikt, kunt u de geïntegreerde Git-ondersteuning gebruiken.

Het wordt ook aangeraden om [Python Tools 2.2 for Visual Studio] te installeren. Dit is optioneel, maar als u [Visual Studio] hebt, met inbegrip van de gratis Visual Studio Community 2013 of Visual Studio Express 2013 for Web, beschikt u over een uitstekende Python IDE.

### Mac/Linux

Python en Git moeten al zijn geïnstalleerd, maar zorg ervoor dat u beschikt over Python 2.7 of 3.4.


## Web-apps maken via de portal

De eerste stap voor het maken van uw app bestaat uit het maken van een web-app via de [Azure Portal](https://portal.azure.com).

1. Meld u aan bij de Azure Portal en klik in de linkerbenedenhoek op de knop **NIEUW**.
3. Voer in het zoekvak 'python' in.
4. Selecteer in de zoekresultaten **Django** en klik vervolgens op **Maken**.
5. Configureer de nieuwe Django-app door er bijvoorbeeld een nieuw App Service-plan en een nieuwe resourcegroep voor te maken. Klik vervolgens op **Maken**.
6. Configureer Git-publicatie voor uw nieuwe web-app door de instructies te volgen in [Continue implementatie met Git in Azure App Service](web-sites-publish-source-control.md).

## Toepassingsoverzicht

### Inhoud van Git-opslagplaats

Hier volgt een overzicht van de bestanden in de eerste Git-opslagplaats. Deze gaat u in het volgende gedeelte klonen.

    \app\__init__.py
    \app\forms.py
    \app\models.py
    \app\tests.py
    \app\views.py
    \app\static\content\
    \app\static\fonts\
    \app\static\scripts\
    \app\templates\about.html
    \app\templates\contact.html
    \app\templates\index.html
    \app\templates\layout.html
    \app\templates\login.html
    \app\templates\loginpartial.html
    \DjangoWebProject\__init__.py
    \DjangoWebProject\settings.py
    \DjangoWebProject\urls.py
    \DjangoWebProject\wsgi.py

Belangrijkste bronnen voor de toepassing. Bestaat uit 3 pagina's (index, over, contact) met een modelindeling. Statische inhoud en scripts bevatten bootstrap, jquery, modernizr en respond.

    \manage.py

Lokaal beheer en ondersteuning voor de ontwikkelaarsserver. Gebruik deze om de toepassing lokaal uit te voeren, de database te synchroniseren, enzovoort.

    \db.sqlite3

Standaarddatabase. Bevat de benodigde tabellen voor het uitvoeren van de toepassing, maar bevat geen gebruikers (synchroniseer de database om een gebruiker te maken).

    \DjangoWebProject.pyproj
    \DjangoWebProject.sln

Projectbestanden voor gebruik met [Python Tools for Visual Studio].

    \ptvs_virtualenv_proxy.py

IIS-proxy voor virtuele omgevingen en ondersteuning voor externe PTVS-foutopsporing.

    \requirements.txt

Externe pakketten nodig voor deze toepassing. Door het implementatiescript wordt een PIP-installatie uitgevoerd voor de pakketten die in het bestand worden vermeld.

    \web.2.7.config
    \web.3.4.config

IIS-configuratiebestanden. Het implementatiescript maakt gebruik van de juiste web.x.y.config en kopieert deze als web.config.

### Optionele bestanden - implementatie aanpassen

[AZURE.INCLUDE [web-sites-python-django-customizing-deployment](../../includes/web-sites-python-django-customizing-deployment.md)]

### Optionele bestanden - Python-runtime

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### Aanvullende bestanden op server

Bepaalde bestanden bestaan wel op de server, maar worden niet toegevoegd aan de Git-opslagplaats. Deze worden gemaakt door het script voor implementatie.

    \web.config

IIS-configuratiebestand. Gemaakt op basis van web.x.y.config voor elke implementatie.

    \env\

Virtuele Python-omgeving. Wordt gemaakt tijdens de implementatie als er in de web-app nog geen compatibele virtuele omgeving bestaat. De pakketten die worden vermeld in requirements.txt, worden via PIP geïnstalleerd, maar PIP slaat de installatie over als de pakketten al zijn geïnstalleerd.

In de volgende drie gedeelten wordt beschreven hoe u verdergaat met de ontwikkeling van de web-app in drie verschillende omgevingen:

- Windows, met Python Tools for Visual Studio
- Windows, met opdrachtregel
- Mac/Linux, met opdrachtregel


## Ontwikkeling van web-apps - Windows - Python Tools for Visual Studio

### De opslagplaats klonen

Kloon eerst de opslagplaats via de URL die in de Azure Portal is opgegeven. Zie voor meer informatie [Continue implementatie met Git in Azure App Service](web-sites-publish-source-control.md).

Open het oplossingsbestand (.sln) dat is opgenomen in de hoofdmap van de opslagplaats.

![](./media/web-sites-python-create-deploy-django-app/ptvs-solution-django.png)

### Een virtuele omgeving maken

U maakt nu een virtuele omgeving voor lokale ontwikkeling. Klik met de rechtermuisknop op **Python-omgevingen** en selecteer **Virtuele omgeving toevoegen...**.

- Zorg ervoor dat de naam van de omgeving `env` is.

- Selecteer de basisinterpreter. Zorg ervoor dat u dezelfde versie van Python gebruikt als de versie die is geselecteerd voor uw web-app (in runtime.txt of op de blade **Toepassingsinstellingen** van uw web-app in de Azure Portal).

- Zorg ervoor dat de optie voor het downloaden en installeren van pakketten is ingeschakeld.

![](./media/web-sites-python-create-deploy-django-app/ptvs-add-virtual-env-27.png)

Klik op **Maken**. Hiermee maakt u de virtuele omgeving en worden alle afhankelijkheden uit requirements.txt geïnstalleerd.

### Een beheerder maken

Voor de database die deel uitmaakt van de toepassing, is geen beheerder opgegeven. Als u gebruik wilt maken van de aanmeldfunctionaliteit in de toepassing of van de Django-beheerinterface (als u besluit deze in te schakelen), moet u een beheerder maken.

Voer dit uit vanaf de opdrachtregel van de projectmap:

    env\scripts\python manage.py createsuperuser

Volg de aanwijzingen om de gebruikersnaam, het wachtwoord en meer in te stellen.

### Uitvoeren met behulp van de ontwikkelaarsserver

Druk op F5 om de foutopsporing te starten. Uw webbrowser wordt automatisch geopend op de pagina die lokaal wordt uitgevoerd.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

U kunt onderbrekingspunten instellen in de bronnen, de vensters Controle gebruiken, enzovoort. Zie de [Documentatie bij Python Tools for Visual Studio] voor meer informatie over de verschillende functies.

### Wijzigingen aanbrengen

U kunt nu experimenteren door wijzigingen aan te brengen aan de toepassingsbronnen en/of sjablonen.

Nadat u uw wijzigingen hebt getest, kunt u ze in de Git-opslagplaats vastleggen:

![](./media/web-sites-python-create-deploy-django-app/ptvs-commit-django.png)

### Meer pakketten installeren

Uw toepassing bevat mogelijk afhankelijkheden buiten Python en Django.

U kunt extra pakketten installeren via PIP. Als u een pakket wilt installeren, klikt u met de rechtermuisknop op de virtuele omgeving en selecteert u **Python-pakket installeren**.

Als u bijvoorbeeld de Azure SDK voor Python wilt installeren, waarmee u toegang krijgt tot Azure Storage, Service Bus en andere Azure-services, typt u `azure`:

![](./media/web-sites-python-create-deploy-django-app/ptvs-install-package-dialog.png)

Klik met de rechtermuisknop op de virtuele omgeving en selecteer **requirements.txt genereren** om requirements.txt bij te werken.

Voer vervolgens de wijzigingen aan requirements.txt door in de Git-opslagplaats.

### Implementeren in Azure

Als u een implementatie wilt activeren, klikt u op **Synchroniseren** of **Pushen**. Bij een synchronisatie vinden er push- en pullbewerkingen plaats.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-push.png)

De eerste implementatie duurt langer dan normaal omdat er een virtuele omgeving wordt gemaakt, er pakketten worden geïnstalleerd en meer.

De voortgang van de implementatie wordt niet weergegeven in Visual Studio. Als u de uitvoer wilt controleren, raadpleegt u het gedeelte [Probleemoplossing - Implementatie](#troubleshooting-deployment).

Blader naar de Azure-URL om uw wijzigingen te bekijken.


## Ontwikkeling van web-apps - Windows - opdrachtregel

### De opslagplaats klonen

Kloon eerst de opslagplaats via de URL die is opgegeven in de Azure Portal. Voeg daarna de Azure-opslagplaats extern toe. Zie voor meer informatie [Continue implementatie met Git in Azure App Service](web-sites-publish-source-control.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### Een virtuele omgeving maken

U maakt een nieuwe virtuele omgeving voor ontwikkelingsdoeleinden (voeg deze niet toe aan de opslagplaats). Virtuele omgevingen in Python kunnen niet worden verplaatst, dus elke ontwikkelaar die aan de toepassing werkt, maakt een eigen lokale versie.

Zorg ervoor dat u dezelfde versie van Python gebruikt die is geselecteerd voor uw web-app (in runtime.txt of op de blade Toepassingsinstellingen van uw web-app in de Azure Portal).

Voor Python 2.7:

    c:\python27\python.exe -m virtualenv env

Voor Python 3.4:

    c:\python34\python.exe -m venv env

Installeer alle externe pakketten die voor uw toepassing vereist zijn. U kunt het bestand requirements.txt in de hoofdmap van de opslagplaats gebruiken voor het installeren van de pakketten in de virtuele omgeving:

    env\scripts\pip install -r requirements.txt

### Een beheerder maken

Voor de database die deel uitmaakt van de toepassing, is geen beheerder opgegeven. Als u gebruik wilt maken van de aanmeldfunctionaliteit in de toepassing of van de Django-beheerinterface (als u besluit deze in te schakelen), moet u een beheerder maken.

Voer dit uit vanaf de opdrachtregel van de projectmap:

    env\scripts\python manage.py createsuperuser

Volg de aanwijzingen om de gebruikersnaam, het wachtwoord en meer in te stellen.

### Uitvoeren met behulp van de ontwikkelaarsserver

U kunt de toepassing op een ontwikkelaarsserver starten aan de hand van de volgende opdracht:

    env\scripts\python manage.py runserver

In de console worden de URL en de poort vermeld waarnaar de server luistert:

![](./media/web-sites-python-create-deploy-django-app/windows-run-local-django.png)

Open de betreffende URL in uw webbrowser.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

### Wijzigingen aanbrengen

U kunt nu experimenteren door wijzigingen aan te brengen aan de toepassingsbronnen en/of sjablonen.

Nadat u uw wijzigingen hebt getest, kunt u ze in de Git-opslagplaats vastleggen:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Meer pakketten installeren

Uw toepassing bevat mogelijk afhankelijkheden buiten Python en Django.

U kunt extra pakketten installeren via PIP. Als u bijvoorbeeld de Azure SDK voor Python wilt installeren, waarmee u toegang krijgt tot Azure Storage, Service Bus en andere Azure-services, typt u:

    env\scripts\pip install azure

Zorg ervoor dat u requirements.txt bijwerkt:

    env\scripts\pip freeze > requirements.txt

Voer de wijzigingen door:

    git add requirements.txt
    git commit -m "Added azure package"

### Implementeren in Azure

Als u een implementatie wilt starten, pusht u de wijzigingen naar Azure:

    git push azure master

U ziet de uitvoer van het script voor implementatie, inclusief de uitvoer van het maken van de virtuele omgeving, het installeren van pakketten, het maken van web.config en meer.

Blader naar de Azure-URL om uw wijzigingen te bekijken.


## Ontwikkeling van web-apps - Mac/Linux - opdrachtregel

### De opslagplaats klonen

Kloon eerst de opslagplaats via de URL die is opgegeven in de Azure Portal. Voeg daarna de Azure-opslagplaats extern toe. Zie voor meer informatie [Continue implementatie met Git in Azure App Service](web-sites-publish-source-control.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### Een virtuele omgeving maken

U maakt een nieuwe virtuele omgeving voor ontwikkelingsdoeleinden (voeg deze niet toe aan de opslagplaats). Virtuele omgevingen in Python kunnen niet worden verplaatst, dus elke ontwikkelaar die aan de toepassing werkt, maakt een eigen lokale versie.

Zorg ervoor dat u dezelfde versie van Python gebruikt die is geselecteerd voor uw web-app (in runtime.txt of op de blade Toepassingsinstellingen van uw web-app in de Azure Portal).

Voor Python 2.7:

    python -m virtualenv env

Voor Python 3.4:

    python -m venv env

of

    pyvenv env

Installeer alle externe pakketten die voor uw toepassing vereist zijn. U kunt het bestand requirements.txt in de hoofdmap van de opslagplaats gebruiken voor het installeren van de pakketten in de virtuele omgeving:

    env/bin/pip install -r requirements.txt

### Een beheerder maken

Voor de database die deel uitmaakt van de toepassing, is geen beheerder opgegeven. Als u gebruik wilt maken van de aanmeldfunctionaliteit in de toepassing of van de Django-beheerinterface (als u besluit deze in te schakelen), moet u een beheerder maken.

Voer dit uit vanaf de opdrachtregel van de projectmap:

    env/bin/python manage.py createsuperuser

Volg de aanwijzingen om de gebruikersnaam, het wachtwoord en meer in te stellen.

### Uitvoeren met behulp van de ontwikkelaarsserver

U kunt de toepassing op een ontwikkelaarsserver starten aan de hand van de volgende opdracht:

    env/bin/python manage.py runserver

In de console worden de URL en de poort vermeld waarnaar de server luistert:

![](./media/web-sites-python-create-deploy-django-app/mac-run-local-django.png)

Open de betreffende URL in uw webbrowser.

![](./media/web-sites-python-create-deploy-django-app/mac-browser-django.png)

### Wijzigingen aanbrengen

U kunt nu experimenteren door wijzigingen aan te brengen aan de toepassingsbronnen en/of sjablonen.

Nadat u uw wijzigingen hebt getest, kunt u ze in de Git-opslagplaats vastleggen:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Meer pakketten installeren

Uw toepassing bevat mogelijk afhankelijkheden buiten Python en Django.

U kunt extra pakketten installeren via PIP. Als u bijvoorbeeld de Azure SDK voor Python wilt installeren, waarmee u toegang krijgt tot Azure Storage, Service Bus en andere Azure-services, typt u:

    env/bin/pip install azure

Zorg ervoor dat u requirements.txt bijwerkt:

    env/bin/pip freeze > requirements.txt

Voer de wijzigingen door:

    git add requirements.txt
    git commit -m "Added azure package"

### Implementeren in Azure

Als u een implementatie wilt starten, pusht u de wijzigingen naar Azure:

    git push azure master

U ziet de uitvoer van het script voor implementatie, inclusief de uitvoer van het maken van de virtuele omgeving, het installeren van pakketten, het maken van web.config en meer.

Blader naar de Azure-URL om uw wijzigingen te bekijken.


## Probleemoplossing - Installatie van pakketten

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## Probleemoplossing - Virtuele omgeving

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Probleemoplossing - Statische bestanden

Voor Django wordt het concept van het verzamelen van statische bestanden gebruikt. Hierbij worden alle statische bestanden vanuit hun oorspronkelijke locatie gekopieerd naar één map. Voor deze toepassing worden ze gekopieerd naar `/static`.

Dit gebeurt omdat statische bestanden mogelijk afkomstig zijn van andere Django-apps. De statische bestanden van Django-beheerinterfaces bevinden zich bijvoorbeeld in een Django-bibliotheeksubmap in de virtuele omgeving. De statische bestanden die door deze toepassing worden gedefinieerd, bevinden zich in `/app/static`. Als u meer Django-apps gebruikt, staan er op meerdere plaatsen statische bestanden.

Wanneer de toepassing wordt uitgevoerd in de foutopsporingsmodus, gebruikt de toepassing de statische bestanden van de oorspronkelijke locatie.

Wanneer de toepassing wordt uitgevoerd in de releasemodus, maakt de toepassing **geen** gebruik van de statische bestanden. Het is de verantwoordelijkheid van de webserver om de bestanden te gebruiken. Voor deze toepassing gebruikt IIS de statische bestanden van `/static`.

De statische bestanden worden automatisch verzameld als onderdeel van het implementatiescript. Eerder verzamelde bestanden worden gewist. Dit betekent dat de verzameling plaatsvindt bij elke implementatie. Hierdoor duurt het implementatieproces langer, maar zijn verouderde bestanden niet beschikbaar. Dat voorkomt potentiële beveiligingsproblemen.

Als u het verzamelen van statische bestanden voor uw Django-toepassing wilt overslaan:

    \.skipDjango

U moet het verzamelen dan handmatig uitvoeren op uw lokale machine:

    env\scripts\python manage.py collectstatic

Verwijder de map `\static` uit `.gitignore` en voeg deze toe aan de Git-opslagplaats.


## Probleemoplossing - Instellingen

Diverse instellingen voor de toepassing kunnen worden gewijzigd in `DjangoWebProject/settings.py`.

Voor het gemak van ontwikkelaars is de foutopsporingsmodus ingeschakeld. Een handige bijkomstigheid is dat u bij lokale uitvoering afbeeldingen en andere statische inhoud kunt zien zonder dat er statische bestanden hoeven te worden verzameld.

De foutopsporingsmodus uitschakelen:

    DEBUG = False

Wanneer foutopsporing is uitgeschakeld, moet de waarde voor `ALLOWED_HOSTS` worden bijgewerkt met de naam van de Azure-host. Bijvoorbeeld:

    ALLOWED_HOSTS = (
        'pythonapp.azurewebsites.net',
    )

of als u deze wilt inschakelen:

    ALLOWED_HOSTS = (
        '*',
    )

In de praktijk wilt u mogelijk iets ingewikkelders doen om te schakelen tussen de foutopsporingsmodus en de releasemodus, en om de hostnaam op te halen.

U kunt de omgevingsvariabelen instellen via de pagina **CONFIGUREREN** van de Azure Portal. Ga daar naar het gedeelte **App-instellingen**.  Dit kan handig zijn om waarden in te stellen waarvan u wilt dat ze niet worden weergegeven in de bronnen (verbindingsreeksen, wachtwoorden, enzovoort) en die u anders wilt instellen voor Azure en uw lokale computer. In `settings.py` kunt u met `os.getenv` een query indienen voor de omgevingsvariabelen.


## Een database gebruiken

De database die is opgenomen in de toepassing, is een SQLite-database. Dit is een handige en nuttige standaarddatabase die u kunt gebruiken voor ontwikkeling, omdat er vrijwel geen configuratie is vereist. De database is opgeslagen in het bestand db.sqlite3 in de projectmap.

Azure biedt databaseservices die eenvoudig te gebruiken zijn vanuit een Django-toepassing. In de zelfstudies voor het gebruik van [SQL Database] en [MySQL] via een Django-toepassing ziet u de stappen die nodig zijn om de databaseservice te maken en om de database-instellingen te wijzigen in `DjangoWebProject/settings.py`. U ziet er ook welke bibliotheken er moeten worden geïnstalleerd.

Als u liever uw eigen databaseservers beheert, kunt u dat doen via virtuele Windows- of Linux-machines die in Azure worden uitgevoerd.


## Django-beheerinterface

Wanneer u begint met het bouwen van modellen, moet u de database vullen met gegevens. U kunt eenvoudig inhoud toevoegen en interactief bewerken door de Django-beheerinterface te gebruiken.

De code voor de beheerinterface is als opmerking opgenomen in de toepassingsbronnen. Omdat de code duidelijk is gemarkeerd, kunt u deze eenvoudig inschakelen (zoek naar 'admin').

Wanneer de code is ingeschakeld, synchroniseert u de database, voert u de toepassing uit en navigeert u naar `/admin`.


## Volgende stappen

Volg deze koppelingen voor meer informatie over Django en Python Tools for Visual Studio:

- [Documentatie bij Django]
- [Documentatie bij Python Tools for Visual Studio]

Voor informatie over het gebruik van SQL Database en MySQL:

- [Django en MySQL in Azure met Python Tools for Visual Studio]
- [Django en SQL Database in Azure met Python Tools for Visual Studio]

Raadpleeg het [Python Developer Center](/develop/python/) voor meer informatie.


## Wat is er gewijzigd
* Als u van Websites wilt overstappen op App Service, raadpleegt u de volgende handleiding: [Azure App Service en de invloed ervan op bestaande Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Django en MySQL in Azure met Python Tools for Visual Studio]: web-sites-python-ptvs-django-mysql.md
[Django en SQL Database in Azure met Python Tools for Visual Studio]: web-sites-python-ptvs-django-sql.md
[SQL Database]: web-sites-python-ptvs-django-sql.md
[MySQL]: web-sites-python-ptvs-django-mysql.md

<!--External Link references-->
[Azure SDK voor Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git voor Windows]: http://msysgit.github.io/
[GitHub voor Windows]: https://windows.github.com/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[Documentatie bij Python Tools for Visual Studio]: http://aka.ms/ptvsdocs
[Documentatie bij Django]: https://www.djangoproject.com/



<!--HONumber=Jun16_HO2-->


