<properties
    pageTitle="De ontwikkeling van een Python Flask-webtoepassing met DocumentDB | Microsoft Azure"
    description="Bekijk een databasezelfstudie over het gebruik van DocumentDB voor het opslaan van en de toegang tot gegevens uit een Python Flask-webtoepassing die wordt gehost in Azure. Oplossingen voor het ontwikkelen van toepassingen zoeken." 
    keywords="Application development, database tutorial, python flask, python web application, python web development, documentdb, azure, Microsoft azure"
    services="documentdb"
    documentationCenter="python"
    authors="aliuy"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="04/18/2016"
    ms.author="andrl"/>

# De ontwikkeling van een Python Flask-webtoepassing met DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

In deze zelfstudie wordt uitgelegd hoe u Azure DocumentDB gebruikt voor het opslaan van en de toegang tot gegevens uit een Python-webtoepassing die wordt gehost in Azure. Verondersteld wordt dat u enige ervaring hebt met het gebruik van Python en Azure Websites.

In deze zelfstudie komen de volgende onderwerpen aan bod:

1. Een DocumentDB-account maken en inrichten.
2. Een Python MVC-toepassing maken.
3. Verbinding maken met en gebruiken van Azure DocumentDB via uw webtoepassing.
4. De webtoepassing implementeren naar Azure Websites.

In deze zelfstudie bouwt u een eenvoudige stemtoepassing waarmee u kunt stemmen.

![Schermopname van de takenlijstwebtoepassing die is gemaakt met deze databasezelfstudie](./media/documentdb-python-application/image1.png)


## Vereisten voor de databasezelfstudie

Voordat u de instructies in dit artikel uitvoert, moet het volgende zijn geïnstalleerd:

- Een actief Azure-account. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
- [Visual Studio 2013](http://www.visualstudio.com/) of hoger, of [Visual Studio Express]() (de gratis versie). De instructies in deze zelfstudie zijn specifiek geschreven voor Visual Studio 2015. 
- Python Tools for Visual Studio van [GitHub](http://microsoft.github.io/PTVS/). In deze zelfstudie wordt gebruikgemaakt van Python Tools for VS 2015. 
- Versie 2.4 of hoger van de Azure Python SDK voor Visual Studio is beschikbaar via [azure.com](https://azure.microsoft.com/downloads/). Wij hebben de Microsoft Azure SDK voor Python 2.7 gebruikt
- Python 2.7 van [python.org][2]. Wij hebben Python 2.7.11 gebruikt. 

> [AZURE.IMPORTANT] Als u Python 2.7 voor het eerst installeert, moet u in het scherm Customize Python 2.7.11 (Python 2.7.11 aanpassen) de optie **Add python.exe to Path** (Python.exe aan het pad toevoegen) selecteren.
> 
>    ![Schermopname van het scherm Customize Python 2.7.11 (Python 2.7.11 aanpassen), waarin u Add python.exe to Path (Python.exe aan het pad toevoegen) moet selecteren](./media/documentdb-python-application/image2.png)

- Microsoft Visual C++ Compiler voor Python 2.7 uit het [Microsoft Downloadcentrum][3].

## Stap 1: een DocumentDB-databaseaccount maken

Begin met het maken van een DocumentDB-account. Als u al een account hebt, kunt u doorgaan met [Stap 2: een nieuwe Python Flask-toepassing maken](#step-2:-create-a-new-python-flask-web-application).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

<br/>
U kunt nu zien hoe u een compleet nieuwe Python Flask-toepassing maakt.

## Stap 2: een nieuwe Python Flask-webtoepassing maken

1. Wijs in het menu **Bestand** van Visual Studio de optie **Nieuw** aan en klik vervolgens op **Project**.

    Het dialoogvenster **Nieuw project** wordt weergegeven.

2. Vouw in het linkerdeelvenster achtereenvolgens **Sjablonen** en **Python** uit en klik vervolgens op **Web**. 

3. Selecteer in het middelste deelvenster de optie **Flask Web Project** (Flask-webproject), typ in het vak **Naam** de tekst **tutorial** en klik vervolgens op **OK**. Houd er rekening mee dat Python-pakketnamen uitsluitend kleine letters mogen bevatten, zoals beschreven in de [Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/#package-and-module-names) (Stijlgids voor Python-code).

    Als u nog niet bekend bent met Python Flask, dit is een ontwikkelframework voor webtoepassingen waarmee u sneller webtoepassingen in Python kunt maken.

    ![Schermopname van het venster Nieuw project in Visual Studio met aan de linkerkant Python gemarkeerd, in het midden het geselecteerde Python Flask-webproject en de naam tutorial in het naamvak](./media/documentdb-python-application/image9.png)

4. Klik in het venster **Python Tools for Visual Studio** (Python-hulpprogramma's voor Visual Studio) op **Install into a virtual environment** (Installeren in een virtuele omgeving). 

    ![Schermopname van de databasezelfstudie - Venster met Python Tools for Visual Studio](./media/documentdb-python-application/image10.png)

5. In het venster **Add Virtual Environment** (Virtuele omgeving toevoegen) kunt u de standaardinstellingen accepteren en Python 2.7 als basisomgeving gebruiken, aangezien PyDocumentDB momenteel geen ondersteuning biedt voor Python 3.x. Klik vervolgens op **Create** (Maken). Hiermee stelt u de vereiste virtuele Python-omgeving voor uw project in.

    ![Schermopname van de databasezelfstudie - Venster met Python Tools for Visual Studio](./media/documentdb-python-application/image10_A.png)

    Wanneer de omgeving is geïnstalleerd, verschijnt de volgende tekst in het uitvoervenster: `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.`.

## Stap 3: de Python Flask-webtoepassing wijzigen

### De Python Flask-pakketten aan uw project toevoegen

Zodra het project is ingesteld, moet u de vereiste Flask-pakketten toevoegen aan uw project, inclusief pydocumentdb, het Python-pakket voor DocumentDB.

1. Open het bestand **requirements.txt** in Solution Explorer en vervang de inhoud door het volgende:

        flask==0.9
        flask-mail==0.7.6
        sqlalchemy==0.7.9
        flask-sqlalchemy==0.16
        sqlalchemy-migrate==0.7.2
        flask-whooshalchemy==0.55a
        flask-wtf==0.8.4
        pytz==2013b
        flask-babel==0.8
        flup
        pydocumentdb>=1.0.0

2. Sla het bestand **requirements.txt** op. 
3. Klik in Solution Explorer met de rechtermuisknop op **env** en klik op **Install from requirements.txt** (Installeren vanuit requirements.txt).

    ![Schermopname waarin env (Python 2.7) is geselecteerd en de optie Install from requirements.txt (Installeren vanuit requirements.txt) in de lijst is gemarkeerd](./media/documentdb-python-application/image11.png)

    Zodra de installatie is voltooid, wordt in het uitvoervenster het volgende weergegeven:

        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2

    > [AZURE.NOTE] In uitzonderlijke gevallen wordt er een fout in het uitvoervenster weergegeven. Als dit gebeurt, controleert u of de fout verband houdt met de opschoonbewerking. Soms mislukt de opschoonbewerking maar wordt de installatie wel gewoon voltooid (schuif naar boven in het uitvoervenster om dit te controleren). U kunt uw installatie controleren door [de virtuele omgeving te verifiëren](#verify-the-virtual-environment). Als de installatie is mislukt maar de controle is geslaagd is, kunt u zonder problemen doorgaan.

### De virtuele omgeving verifiëren

Zorg ervoor dat alles juist is geïnstalleerd.

1. Bouw de oplossing op door op **Ctrl**+**Shift**+**B** te drukken.
2. Zodra de opbouwbewerking is voltooid, start u de website door op **F5** te drukken. Hiermee wordt de Flask-ontwikkelaarsserver en uw webbrowser gestart. De volgende pagina wordt weergegeven:

    ![Het lege Python Flask-webontwikkelingsproject in een browser](./media/documentdb-python-application/image12.png)

3. Stop de foutopsporing voor de website door in Visual Studio op **Shift**+**F5** te drukken.

### Database-, verzamelings- en documentdefinities maken

U kunt nu de stemtoepassing maken door nieuwe bestanden toe te voegen en andere bestanden bij te werken.

1. Klik in Solution Explorer met de rechtermuisknop op het project **tutorial** en klik vervolgens op **Toevoegen** en **Nieuw item**. Selecteer **het lege Python-bestand** en noem het bestand **forms.py**.  
2. Kopieer de volgende code naar het bestand forms.py en sla het bestand vervolgens op:

```python
from flask.ext.wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### De vereiste imports toevoegen aan views.py

1. Vouw in Solution Explorer de map **tutorial** uit en open het bestand **views.py**. 
2. Voeg de volgende importinstructies boven aan het bestand **views.py** toe en sla het bestand vervolgens op. Hiermee worden de PythonSDK van DocumentDB en de Flask-pakketten geïmporteerd.

    ```python
    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    ```


### De database, de verzameling en het document maken

- Voeg de volgende code toe aan het eind van het bestand **views.py**. Hiermee wordt de database gemaakt die door het formulier wordt gebruikt. Verwijder de bestaande code in **views.py** niet. U kunt de code gewoon aan het eind toevoegen.

```python
@app.route('/create')
def create():
    """Renders the contact page."""
    client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

    # Attempt to delete the database.  This allows this to be used to recreate as well as create
    try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
        client.DeleteDatabase(db['_self'])
    except:
        pass

    # Create database
    db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })

    # Create collection
    collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION }, { 'offerType': 'S1' })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config.DOCUMENTDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config.DOCUMENTDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```

> [AZURE.TIP] De methode **CreateCollection** maakt voor de derde parameter gebruik van de optionele parameter **RequestOptions**. Deze kan worden gebruikt om het aanbiedingstype (OfferType) voor de verzameling op te geven. Als er geen waarde voor OfferType is opgegeven, wordt het standaard-OfferType gebruikt om de verzameling te maken. Zie [Prestatieniveaus in DocumentDB](documentdb-performance-levels.md) voor meer informatie over DocumentDB-aanbiedingstypen (OfferType).


### De database, de verzameling en het document lezen en het formulier verzenden

- Voeg de volgende code toe aan het eind van het bestand **views.py**. Deze code wordt gebruikt om het formulier in te stellen en de database, de verzameling en het documenten te lezen. Verwijder de bestaande code in **views.py** niet. U kunt de code gewoon aan het eind toevoegen.

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))

        # Take the data from the deploy_preference and increment our database
        doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
        replaced_document = client.ReplaceDocument(doc['_self'], doc)

        # Create a model to pass to results.html
        class VoteObject:
            choices = dict()
            total_votes = 0

        vote_object = VoteObject()
        vote_object.choices = {
            "Web Site" : doc['Web Site'],
            "Cloud Service" : doc['Cloud Service'],
            "Virtual Machine" : doc['Virtual Machine']
        }
        vote_object.total_votes = sum(vote_object.choices.values())

        return render_template(
            'results.html', 
            year=datetime.now().year, 
            vote_object = vote_object)

    else :
        return render_template(
            'vote.html', 
            title = 'Vote',
            year=datetime.now().year,
            form = form)
```


### De HTML-bestanden maken

1. Klik in de map **tutorial** in Solution Explorer met de rechtermuisknop op de map **templates** en klik vervolgens op **Toevoegen** en **Nieuw item**. 
2. Selecteer **HTML-pagina** en typ in het naamvak **create.html**. 
3. Herhaal de eerste twee stappen om twee extra HTML-bestanden te maken: results.html en vote.html.
4. Voeg in het bestand **create.html** de volgende code toe aan het `<body>`-element. Er wordt een bericht weergegeven dat er een nieuwe database, een nieuwe verzameling en een nieuw document is gemaakt.

    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

5. Voeg in het bestand **results.html** de volgende code toe aan het `<body`-element. De resultaten van de poll worden weergegeven.

    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
        <br />
        
    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
            <div class="col-sm-5">
                <div class="progress">
                    <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                                {{vote_object.choices[choice]}}
                </div>
            </div>
            </div>
    </div>
    {% endfor %}
    
    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}
    ```

6. Voeg in het bestand **vote.html** de volgende code toe aan het `<body`-element. De poll wordt weergegeven en de stemmen worden geaccepteerd. Bij het registreren van de stemmen wordt de besturing overgegeven aan het bestand views.py, waar de uitgebrachte stem wordt herkend en het document dienovereenkomstig wordt toegevoegd.

    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
            {{form.deploy_preference}}
            <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}
    ```

7. In de map **templates** vervangt u de inhoud van het bestand **index.html** door de volgende inhoud. Deze pagina fungeert als de landingspagina voor uw toepassing.
    
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + DocumentDB Voting Application.</h2>
    <h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### Een configuratiebestand toevoegen en \_\_init\_\_.py wijzigen

1. Klik in Solution Explorer met de rechtermuisknop op het project **tutorial** en klik op **Toevoegen** en **Nieuw item**, selecteer **het lege Python-bestand** en noem het bestand **config.py**. Dit configuratiebestand is nodig voor de formulieren in Flask. U kunt dit bestand ook gebruiken om een geheime sleutel te verstrekken. Deze sleutel is echter niet nodig voor deze zelfstudie.

2. Voeg de volgende code toe aan het bestand config.py. In de volgende stap moet u de waarden wijzigen voor **DOCUMENTDB\_HOST** en **DOCUMENTDB\_KEY**.

    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    
    DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    DOCUMENTDB_DATABASE = 'voting database'
    DOCUMENTDB_COLLECTION = 'voting collection'
    DOCUMENTDB_DOCUMENT = 'voting document'
    ```

3. Navigeer in [Azure Portal](https://portal.azure.com/) naar de blade **Sleutels** door op **Bladeren** en **DocumentDB-accounts** te klikken. Dubbelklik op de naam van het account dat u wilt gebruiken en klik vervolgens op de knop **Sleutels** in het gebied **Essentials**. Kopieer op de blade **Sleutels** de **URI**-waarde en plak deze in het bestand **config.py** als waarde voor de eigenschap **DOCUMENTDB\_HOST**. 
4. Terug in Azure Portal kopieert u op de blade **Sleutels** de waarde van de **primaire sleutel** of de **secundaire sleutel** en plakt u deze in het bestand **config.py** als waarde voor de eigenschap **DOCUMENTDB\_KEY**.
5. Voeg de volgende regel toe aan het bestand **\_\_init\_\_.py**. 

        app.config.from_object('config')

    De inhoud van het bestand is nu:

    ```python
    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views
    ```

6. Zodra alle bestanden zijn toegevoegd, ziet Solution Explorer er als volgt uit:

    ![Schermopname van het Solution Explorer-venster in Visual Studio](./media/documentdb-python-application/image15.png)


## Stap 4: de webtoepassing lokaal uitvoeren

1. Bouw de oplossing op door op **Ctrl**+**Shift**+**B** te drukken.
2. Zodra de opbouwbewerking is voltooid, start u de website door op **F5** te drukken. Uw scherm ziet er nu als volgt uit:

    ![Schermopname van de Python + DocumentDB-stemtoepassing in een webbrowser](./media/documentdb-python-application/image16.png)

3. Klik op **Create/Clear the Voting Database** (De stemdatabase maken/wissen) om de database te genereren.

    ![Schermopname van de Create Page (Pagina maken) maken van de webtoepassing – ontwikkelingsgegevens](./media/documentdb-python-application/image17.png)

4. Klik vervolgens op **Vote** (Stemmen) en selecteer uw optie.

    ![Schermopname van de webtoepassing met een vraag waarvoor kan worden gestemd](./media/documentdb-python-application/image18.png)

5. Voor elke stem die u uitbrengt, wordt de desbetreffende teller verhoogd.

    ![Schermopname van de pagina met stemresultaten](./media/documentdb-python-application/image19.png)

6. Stop de foutopsporing voor het project door op Shift + F5 te drukken.

## Stap 5: de webtoepassing implementeren naar Azure Websites

Zodra de volledige toepassing correct werkt met DocumentDB, kunt u de toepassing implementeren naar Azure Websites.

1. Klik in Solution Explorer met de rechtermuisknop op het project (zorg ervoor dat de toepassing niet meer lokaal wordt uitgevoerd) en selecteer **Publiceren**.  

    ![Schermopname van de geselecteerde zelfstudie in Solution Explorer met de optie Publiceren gemarkeerd](./media/documentdb-python-application/image20.png)

2. Selecteer in het venster **Publish Web** (Publiceren naar het web) de optie **Microsoft Azure Web Apps** (Web-apps van Microsoft Azure) en klik op **Next** (Volgende).

    ![Schermopname van het venster Publish Web (Publiceren naar het web) met Microsoft Azure Web Apps (Web-apps van Microsoft Azure) gemarkeerd](./media/documentdb-python-application/image21.png)

3. Klik in het venster **Microsoft Azure Web Apps** (Web-apps van Microsoft Azure) op **New** (Nieuw).

    ![Schermopname van het venster Microsoft Azure Web Apps (Web-apps van Microsoft Azure)](./media/documentdb-python-application/select-existing-website.png)

4. Geef in het venster **Create site on Microsoft Azure** (Site op Microsoft Azure maken) de **naam voor de web-app**, een **App Service-plan **, een **resourcegroep** en **regio** in de desbetreffende velden op en klik op **Create** (Maken).

    ![Schermopname van het venster Create site on Microsoft Azure (Site op Microsoft Azure maken)](./media/documentdb-python-application/create-site-on-microsoft-azure.png)

5. Klik in het venster **Publish Web** (Publiceren naar het web) op **Publish** (Publiceren).

    ![Schermopname van het venster Create site on Microsoft Azure (Site op Microsoft Azure maken)](./media/documentdb-python-application/publish-web.png)

3. Over een paar seconden zal Visual Studio de publicatie van uw webtoepassing voltooien en een browser starten waarin u kunt zien hoe uw werk in Azure wordt uitgevoerd.

## Problemen oplossen

Als dit de eerste Python-app is die u op uw computer uitvoert, moet u ervoor zorgen dat de variabele PATH de volgende mappen (of gelijkwaardige installatielocaties) bevat:

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

Als er een foutbericht wordt weergegeven op de stempagina en u het project een andere naam dan **tutorial** hebt gegeven, moet u ervoor zorgen dat **\_\_init\_\_.py** naar de juiste projectnaam verwijst in de regel: `import tutorial.view`.

## Volgende stappen

Gefeliciteerd. U hebt zojuist uw eerste Python-webtoepassing met Azure DocumentDB gemaakt en gepubliceerd naar Azure Websites.

Dit onderwerp wordt regelmatig bijgewerkt en verbeterd op basis van uw feedback.  Zodra u de zelfstudie hebt voltooid, verzoeken we u de stemknoppen boven of onder aan de pagina te gebruiken en ons feedback te geven met betrekking tot de verbeteringen die u graag zou willen zien. Als u graag rechtstreeks contact wilt opnemen, voegt u uw e-mailadres ook toe aan uw reactie.

Als u de functionaliteit van uw webtoepassing wilt uitbreiden, bekijkt u de beschikbare API's in de [DocumentDB Python SDK](documentdb-sdk-python.md).

Zie [Python-ontwikkelaarscentrum](https://azure.microsoft.com/develop/python/) voor meer informatie over Azure, Visual Studio en Python. 

Zie [The Flask Mega-Tutorial, Part I: Hello, World!](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world) (Engelstalig) voor aanvullende zelfstudies over Python Flask. 

  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [2]: https://www.python.org/downloads/windows/
  [3]: https://www.microsoft.com/download/details.aspx?id=44266
  [Webplatforminstallatieprogramma voor Windows]: http://www.microsoft.com/web/downloads/platform.aspx
  [Azure Portal]: http://portal.azure.com



<!--HONumber=Jun16_HO2-->


