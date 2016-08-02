<properties
    pageTitle="Node.js-API-app in Azure App Service | Microsoft Azure"
    description="Informatie over het maken van een Node.js-RESTful-API en deze implementeren in een API-app in Azure App Service."
    services="app-service\api"
    documentationCenter="node"
    authors="bradygaster"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="node"
    ms.topic="get-started-article"
    ms.date="05/26/2016"
    ms.author="bradygaster"/>

# Een Node.js-RESTful-API maken en deze implementeren in een API-app in Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

In deze zelfstudie ziet u hoe u met behulp van [Git](http://git-scm.com) een eenvoudige [Node.js](http://nodejs.org)-API maakt en implementeert in een [API-app](app-service-api-apps-why-best-platform.md) in [Azure App Service](../app-service/app-service-value-prop-what-is.md). U kunt elk besturingssysteem gebruiken waarop Node.js kan worden uitgevoerd en u doet al het werk met opdrachtregelhulpprogramma's zoals cmd.exe of bash.

## Vereisten

1. Een Microsoft Azure-account ([open hier een gratis account](https://azure.microsoft.com/pricing/free-trial/))
1. [Node.js](http://nodejs.org) is geïnstalleerd (in dit voorbeeld wordt uitgegaan van Node.js-versie 4.2.2)
2. [Git](https://git-scm.com/) is geïnstalleerd
1. Een [GitHub](https://github.com/)-account

App Service ondersteunt vele manieren voor het implementeren van uw code in een API-app, maar in deze zelfstudie wordt de Git-methode gebruikt. Er wordt van uitgegaan dat u basiskennis hebt van het werken met Git. Zie voor meer informatie over andere implementatiemethoden [Een app implementeren in Azure App Service](../app-service-web/web-sites-deploy.md).

## De voorbeeldcode halen

1. Open een opdrachtregelinterface die Node.js- en Git-opdrachten kan uitvoeren.

1. Navigeer naar een map die u kunt gebruiken als lokale Git-opslagplaats en kloon de [GitHub-opslagplaats met de voorbeeldcode](https://github.com/Azure-Samples/app-service-api-node-contact-list).

        git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git

    De voorbeeld-API biedt twee eindpunten: een Get-aanvraag aan `/contacts` retourneert een lijst met namen en e-mailadressen in JSON-indeling. `/contacts/{id}` retourneert alleen de geselecteerde contactpersoon.

## Node.js-code ondersteunen (automatisch genereren) op basis van Swagger-metagegevens

[Swagger](http://swagger.io/) is een bestandsindeling voor metagegevens die een RESTful-API beschrijft. Azure App Service biedt [ingebouwde ondersteuning voor Swagger-metagegevens](app-service-api-metadata.md). In deze sectie van de zelfstudie wordt een API-ontwikkelingswerkstroom getoond waarin u eerst Swagger-metagegevens maakt en deze vervolgens gebruikt om automatisch servercode voor de API te genereren. 

>[AZURE.NOTE] U kunt deze sectie overslaan als u niet bent geïnteresseerd in hoe u op basis van een Swagger-metagegevensbestand Node.js-code genereert. Als u alleen voorbeeldcode wilt implementeren in een nieuwe API-app, gaat u rechtstreeks naar de sectie [Een API-app maken in Azure](#createapiapp).

### Swaggerize installeren en uitvoeren

1. Voer de volgende opdrachten uit voor het installeren van de NPM-modules **yo** en **generator-swaggerize**.

        npm install -g yo
        npm install -g generator-swaggerize

    Swaggerize is een hulpprogramma dat servercode genereert voor een API die wordt beschreven op basis van een Swagger-metagegevensbestand. Het Swagger-bestand dat u gaat gebruiken, heet *api.json* en bevindt zich in de map *start* van de opslagplaats die u hebt gekloond.

2. Navigeer naar de map *start* en voer vervolgens de opdracht `yo swaggerize` uit. Swaggerize stelt u een aantal vragen.  Bij **what to call this project** voert u 'contactlist' in, bij **path to swagger document** voert u 'api.json' in en bij **Express, Hapi, or Restify** voert u 'express' in.

        yo swaggerize

    ![Swaggerize-opdrachtregel](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
    **Opmerking**: als er in deze stap een fout optreedt, wordt in de volgende stap uitgelegd hoe u dit herstelt.

    Swaggerize maakt een toepassingsmap, genereert handlers en configuratiebestanden, en genereert vervolgens een **package.json**-bestand. De engine voor snelle weergave wordt gebruikt voor het genereren van de Swagger-Helppagina.  

3. Als de opdracht `swaggerize` mislukt met de fout 'unexpected token' of 'invalid escape sequence', lost u de oorzaak van de fout op door het gegenereerde *package.json*-bestand te bewerken. In de regel `regenerate` onder `scripts` wijzigt u de backslash die voorafgaat aan *api.json*, in een gewone slash, zodat de regel eruitziet als in het volgende voorbeeld:

        "regenerate": "yo swaggerize --only=handlers,models,tests --framework express --apiPath config/api.json"

1. Navigeer naar de map met de gegenereerde code (in dit geval de submap *ContactList*).

1. Voer `npm install` uit.
    
        npm install
        
2. Installeer de NPM-module **jsonpath**. 

        npm install --save jsonpath
        
    ![Jsonpath installeren](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. Installeer de NPM-module **swaggerize-ui**. 

        npm install --save swaggerize-ui
        
    ![Swaggerize-UI installeren](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

### De gegenereerde code aanpassen

1. Kopieer de map **lib** in de map **start** naar de map **ContactList** die door de gegenereerde code is gemaakt. 

1. Vervang de code in het bestand **handlers/contacts.js** door de volgende code. 

    Deze code gebruikt de JSON-gegevens die zijn opgeslagen in het bestand **lib/contacts.json** en worden geleverd door **lib/contactRepository.js**. De nieuwe contacts.js-code reageert op HTTP-aanvragen om alle contactpersonen op te vragen en deze als JSON-nettolading te retourneren. 

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. Vervang de code in het bestand **handlers/contacts/{id}.js** door de volgende code. 

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. Vervang de code in **server.js** door de volgende code. 

    De wijzigingen die in het server.js-bestand zijn aangebracht, worden aangegeven met opmerkingen, zodat u kunt zien wat er is veranderd. 

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth and final change
        });

### Testen met de API door deze lokaal uit te voeren

1. Activeer de server met behulp van het uitvoerbare bestand voor de Node.js-opdrachtregel. 

        node server.js

1. Wanneer u naar **http://localhost:8000/contacts** bladert, ziet u de JSON-uitvoer van de contactpersonenlijst. (Of u wordt gevraagd om deze te downloaden. Dit is afhankelijk van uw browser.) 

    ![De API voor alle contactpersonen aanroepen](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. Wanneer u naar **http://localhost:8000/contacts/2** bladert, ziet u de contactpersoon, vertegenwoordigd door de betreffende id-waarde.

    ![De API voor een specifieke contactpersoon aanroepen](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. De Swagger JSON-gegevens worden verwerkt door het **/swagger**-eindpunt:

    ![Swagger JSON voor contactpersonen](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. De gegevens voor de Swagger-UI worden aangeleverd via het **/docs**-eindpunt. U kunt in de Swagger-UI de uitgebreide HTML-clientfuncties gebruiken voor het testen van de API.

    ![Swagger-UI](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a> Een nieuwe API-app maken

In deze sectie gebruikt u de Azure Portal voor het maken van een nieuwe API-app in Azure. Deze API-app vertegenwoordigt de rekenresources die Azure biedt om uw code uit te voeren. In latere secties implementeert u uw code in de nieuwe API-app.

1. Blader naar de [Azure Portal](https://portal.azure.com/). 

1. Klik op **Nieuw > Web en mobiel > API-app**. 

    ![De nieuwe API-app in de portal](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

4. Voer bij **App-naam** een naam in die uniek is in het domein *azurewebsites.net*, bijvoorbeeld NodejsAPIApp plus een getal (om de naam uniek te maken). 

    Als de naam bijvoorbeeld `NodejsAPIApp` is, wordt de URL `nodejsapiapp.azurewebsites.net`.

    Als u een naam opgeeft die iemand anders al gebruikt, ziet u aan de rechterkant een rood uitroepteken.

6. Klik in de vervolgkeuzelijst **Resourcegroep** op **Nieuw** en voer vervolgens bij **Nieuwe naam resourcegroep** 'NodejsAPIAppGroup' of desgewenst een andere naam in. 

    Een [resourcegroep](../azure-portal/resource-group-portal.md) is een verzameling Azure-resources, zoals API-apps, databases en virtuele machines. Voor deze zelfstudie kunt u het beste een nieuwe resourcegroep maken. U kunt dan eenvoudig in één stap alle Azure-resources verwijderen die u tijdens de zelfstudie maakt.

4. Klik op **App Service-plan/Locatie** en vervolgens op **Nieuw**.

    ![Een App Service-plan maken](./media/app-service-api-nodejs-api-app/newappserviceplan.png)

    In de volgende stappen maakt u een App Service-plan voor de nieuwe resourcegroep. In een App Service-plan worden de rekenresources opgegeven op basis waarvan uw API-app wordt uitgevoerd. Als u bijvoorbeeld de gratis categorie hebt gekozen, wordt uw API-app uitgevoerd op basis van gedeelde virtuele machines. Bij sommige categorieën waar u voor betaalt, worden apps uitgevoerd via exclusieve virtuele machines. Zie [Overzicht van App Service-plannen](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) voor informatie over App Service-plannen

5. Voer op de blade **App Service-plan** 'NodejsAPIAppPlan' of desgewenst een andere naam in.

5. Kies in de vervolgkeuzelijst **Locatie** de locatie die het dichtst bij u ligt.

    Met deze instelling bepaalt u in welk Azure-datacentrum uw app wordt uitgevoerd. Voor deze zelfstudie kunt u een willekeurige regio selecteren. Dit maakt geen merkbaar verschil. Bij een productie-app moet de server echter zo dicht mogelijk bij de clients staan die de app gebruiken. Zo minimaliseert u de [latentie](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Klik op **Prijscategorie > Alles weergeven > F1 Gratis**.

    Voor deze zelfstudie levert de prijscategorie Gratis voldoende prestaties.

    ![Selecteer de prijscategorie Gratis](./media/app-service-api-nodejs-api-app/selectfreetier.png)

6. Klik op de blade **App Service-plan** op **OK**.

7. Klik op de blade **API-app** op **Maken**.

## De nieuwe API-app instellen voor Git-implementatie

U implementeert uw code in de API-app door doorvoeracties naar een Git-opslagplaats in Azure App Service te pushen. In deze sectie van de zelfstudie maakt u de referenties en de Git-opslagplaats in Azure die u voor de implementatie gaat gebruiken.  

1. Nadat uw API-app is gemaakt, klikt u op de startpagina van de portal op **App Services > {uw API-app}**. 

    In de portal worden de blades **API-app** en **Instellingen** weergegeven.

    ![De blades API app en Settings in de portal](media/app-service-api-nodejs-api-app/portalapiappblade.png)

1. Scrol op de blade **Instellingen** omlaag naar het gedeelte **Publiceren** en klik vervolgens op **Referenties voor implementatie**.
 
3. Voer op de blade **Implementatiereferenties instellen** een gebruikersnaam en een wachtwoord in. Klik vervolgens op **Opslaan**.

    U gebruikt deze referenties voor het publiceren van uw Node.js-code in uw API-app. 

    ![Implementatiereferenties](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. Klik op de blade **Instellingen** op **Implementatiebron > Bron kiezen > Lokale Git-opslagplaats**. Klik vervolgens op **OK**.

    ![Git-opslagplaats maken](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Nadat de Git-opslagplaats is gemaakt, worden op de blade de actieve implementaties weergegeven. Omdat de opslagplaats nieuw is, hebt u geen actieve implementaties in de lijst. 

    ![Geen actieve implementaties](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. Kopieer de URL van de Git-opslagplaats. Hiervoor gaat u naar de blade voor uw nieuwe API-app en bekijkt u hierop het gedeelte **Essentials**. In het gedeelte **Essentials** ziet u de **Git-kloon-URL**. Als u de muisaanwijzer boven deze URL houdt, wordt er aan de rechterzijde een pictogram weergegeven. Hiermee kunt u de URL naar het klembord kopiëren. Klik op dit pictogram om de URL te kopiëren.

    ![De Git-Url ophalen uit de portal](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **Opmerking**: u hebt de Git-kloon-URL in het volgende gedeelte nodig. Sla deze daarom ergens op.

Nu u een API-app hebt met een Git-opslagplaats als back-up, kunt u code in de opslagplaats pushen om de code in de API-app te implementeren. 

## De API-code implementeren in Azure

In deze sectie maakt u een lokale Git-opslagplaats met uw servercode voor de API. Vervolgens pusht u uw code vanuit die opslagplaats naar de opslagplaats in Azure die u eerder hebt gemaakt.

1. Kopieer de map `ContactList` naar een locatie die u kunt gebruiken voor een nieuwe lokale Git-opslagplaats. Als u het eerste deel van de zelfstudie hebt gedaan, kopieert u `ContactList` uit de map `start`. Zo niet, dan kopieert u `ContactList` uit de map `end`.

1. Navigeer naar de nieuwe map in uw opdrachtregelprogramma en voer de volgende opdracht uit voor het maken van een nieuwe lokale Git-opslagplaats. 

        git init

     ![Nieuwe lokale Git-opslagplaats](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. Voer de volgende opdracht uit om een externe Git voor de opslagplaats van uw API-app toe te voegen. 

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **Opmerking**: vervang de tekenreeks 'YOUR_GIT_CLONE_URL_HERE' door de Git-kloon-URL die u eerder hebt gekopieerd. 

1. Voer de volgende opdrachten uit om een doorvoer te maken die alle uw code bevat. 

        git add .
        git commit -m "initial revision"

    ![Uitvoer van GIT-doorvoer](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. Voer de opdracht uit om uw code naar Azure te pushen. Wanneer u wordt gevraagd om een wachtwoord, voert u het wachtwoord in dat u eerder in de Azure Portal hebt gemaakt.

        git push azure master

    Dit activeert een API-app-implementatie.  

1. Navigeer in uw browser terug naar de blade **Implementaties** voor de API-app. U ziet dan dat de implementatie plaatsvindt. 

    ![Implementatie vindt plaats](media/app-service-api-nodejs-api-app/deployment-happening.png)

    Tegelijkertijd laat de opdrachtregelinterface de status van uw implementatie zien terwijl deze plaatsvindt. 

    ![Implementatie van Node Js vindt plaats](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

    Nadat de implementatie is voltooid, wordt op de blade **Implementaties** aangegeven dat de implementatie van de codewijzigingen in uw API-app is gelukt. 

## Testen door de API in Azure uit te voeren
 
3. Kopieer de **URL** in het gedeelte **Essentials** van de blade van uw API-app. 

    ![Implementatie voltooid](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. Gebruik een REST-API-client, zoals Postman of Fiddler (of uw webbrowser), en geef de URL voor het aanroepen van uw contactpersonen-API op. Dit is het `/contacts`-eindpunt van de API-app. De URL wordt dan `https://{your API app name}.azurewebsites.net/contacts`

    Als u een GET-aanvraag verzendt naar dit eindpunt, krijgt u de JSON-uitvoer van uw API-app.

    ![Postman roept API aan](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

2. Ga in een browser naar het `/docs`-eindpunt om de Swagger-UI uit te proberen terwijl deze wordt uitgevoerd in Azure.

Nu u continue aanlevering hebt ingesteld, kunt u codewijzigingen aanbrengen en deze implementeren in Azure door simpelweg doorvoeracties naar uw Azure Git-opslagplaats te pushen.

## Volgende stappen

U hebt nu een API-app gemaakt en er Node.js-API-code in geïmplementeerd. In de volgende zelfstudie ziet u hoe u [API-apps met behulp van CORS kunt gebruiken vanuit JavaScript-clients](app-service-api-cors-consume-javascript.md).



<!--HONumber=Jun16_HO2-->


