<properties
    pageTitle="Aan de slag met Node.js-web-apps in Azure App Service"
    description="Leer hoe u een Node.js-toepassing implementeert in een web-app in Azure App Service."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="cephalin"/>

# Aan de slag met Node.js-web-apps in Azure App Service

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

In deze zelfstudie ziet u hoe u vanaf een opdrachtregelomgeving, zoals cmd.exe of bash, een eenvoudige [Node.js][NODEJS]-toepassing maakt en implementeert in een [web-app] in [Azure App Service]. De instructies in deze zelfstudie kunnen worden uitgevoerd in elk besturingssysteem waarmee Node.js kan worden uitgevoerd.

<a name="prereq"></a>
## Vereisten

- **Node.js** ([Klik hier om te installeren][NODEJS])
- **Bower** ([Klik hier om te installeren][BOWER])
- **Yeoman** ([Klik hier om te installeren][YEOMAN])
- **Git** ([Klik hier om te installeren][GIT])
- **Azure CLI** ([Klik hier om te installeren][Azure CLI])
- Een Microsoft Azure-account. Als u geen account hebt, kunt u zich [aanmelden voor een gratis proefversie] of [uw voordelen als Visual Studio-abonnee activeren].

## Een eenvoudige Node.js-web-app maken en implementeren

1. Open de gewenste opdrachtregelterminal en installeer de [Express-generator voor Yeoman].

        npm install -g generator-express

2. `CD` in een werkmap en genereer een express-app met de volgende syntaxis:

        yo express
        
    Kies de volgende opties wanneer dit wordt gevraagd:  

    `? Would you like to create a new directory for your project?` **Ja**  
    `? Enter directory name` **{appnaam}**  
    `? Select a version to install:` **MVC**  
    `? Select a view engine to use:` **Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):` **Geen**  
    `? Select a database to use:` **Geen**  
    `? Select a build tool to use:` **Grunt**

3. `CD` in de hoofdmap van de nieuwe app en start de app om te controleren of deze in uw ontwikkelingsomgeving wordt uitgevoerd:

        npm start

    Navigeer in uw browser naar <http://localhost:3000> om te controleren of de startpagina van Express wordt geopend. Wanneer u hebt geverifieerd dat de app correct wordt uitgevoerd, gebruikt u `Ctrl-C` om deze te stoppen.
    
1. Meld u als volgt aan bij Azure (hiervoor hebt u [Azure CLI](#prereq) nodig):

        azure login

    Volg de aanwijzing om de aanmelding in een browser voort te zetten met het Microsoft-account waarmee u uw Azure-abonnement hebt afgesloten.

2. Controleer of u zich nog steeds in de hoofdmap van de app bevindt. Maak vervolgens met de volgende opdracht de resource voor de App Service-app in Azure met een unieke app-naam, zoals: http://{appnaam}.azurewebsites.net

        azure site create --git {appname}

    Volg de aanwijzing om een Azure-regio voor de implementatie te selecteren. Als u nog niet eerder Git-/FTP-implementatiereferenties voor uw Azure-abonnement hebt ingesteld, wordt u gevraagd om deze te maken.

3. Open het bestand ./config/config.js in de hoofdmap van de toepassing en wijzig de productiepoort in `process.env.port`. Uw JSON-productieobject moet eruitzien als in het volgende voorbeeld. Als deze map of dit bestandspad niet voorkomt in de hoofdmap van uw toepassing, maakt u een map met de naam **config** in de hoofdmap van de toepassing en kopieert u de volgende JSON naar de map.

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    Hiermee kunt uw Node.js-app laten reageren op webserviceaanvragen bij de standaardpoort waarnaar iisnode luistert.
    
4. Sla de wijzigingen op en gebruik git om de app te implementeren in Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master

    De Express-generator bevat al een bestand .gitignore. `git push` gebruikt dus geen bandbreedte voor het uploaden van de map node_modules/.

5. Ten slotte gaat u als volgt te werk om de live Azure-app in de browser te starten:

        azure site browse

    U ziet nu hoe uw node.js-web-app live in Azure App Service wordt uitgevoerd.
    
    ![Example of browsing to the deployed application.][deployed-express-app]

## De Node.js-web-app bijwerken

Als u updates wilt aanbrengen in een Node.js-web-app die wordt uitgevoerd in App Service, voert u `git add`, `git commit` en `git push` uit, net zoals toen u de web-app de eerste keer implementeerde.
     
## Hoe uw Node.js-web-app wordt geïmplementeerd door App Service

Azure App Service gebruikt [iisnode] om Node.js-apps uit te voeren. De engines Azure CLI en Kudu (Git-implementatie) werken samen voor een gestroomlijnde ervaring bij het ontwikkelen en implementeren van Node.js-apps vanaf de opdrachtregel. 

- `azure site create --git` herkent het algemene Node.js-patroon van server.js of app.js en maakt een iisnode.yml in de hoofdmap. Met dit bestand kunt u iisnode aanpassen.
- Op `git push azure master` worden de volgende implementatietaken door Kudu geautomatiseerd:

    - Voer `npm install --production` uit als package.json in de hoofdmap van de opslagplaats staat.
    - Genereer een Web.config voor iisnode die naar uw startscript in package.json wijst (bijvoorbeeld server.js of app.js).
    - Pas Web.config aan om de app gereed te maken voor foutopsporing met Node-Inspector.
    
## Een Node.js-framework gebruiken

Als u voor de ontwikkeling van apps een populair Node.js-framework gebruikt, zoals [Sails.js][SAILSJS] of [MEAN.js][MEANJS], kunt u de apps implementeren in App Service. Maar populaire Node.js-frameworks hebben zo hun eigenaardigheden. Bovendien worden hun pakketafhankelijkheden voortdurend bijgewerkt. Via App Service hebt u echter inzage in de logboeken stdout en stderr, zodat u precies weet wat er met uw app gebeurt en dienovereenkomstig wijzigingen kunt aanbrengen. Zie [Logboeken stdout en stderr ophalen uit iisnode](#iisnodelog) voor meer informatie.

In de volgende zelfstudies leert u hoe u in App Service met een specifiek framework werkt:

- [Een Sails.js-web-app implementeren in Azure App Service]
- [In Azure App Service een Node.js-chattoepassing maken met Socket.IO]
- [io.js gebruiken met Web Apps van Azure App Service]

## Een specifieke Node.js-engine gebruiken

In een reguliere werkstroom kunt u App Service instrueren om een bepaalde Node.js-engine te gebruiken, net zoals in package.json.
Bijvoorbeeld:

    "engines": {
        "node": "5.5.0"
    }, 

De implementatie-engine Kudu bepaalt aan de hand van de volgende volgorde welke Node.js-engine er wordt gebruikt:

- Kijk eerst in iisnode.yml of `nodeProcessCommandLine` is opgegeven. Zo ja, maak hier dan gebruik van.
- Bekijk vervolgens package.json om na te gaan of `"node": "..."` is opgegeven in het `engines`-object. Zo ja, maak hier dan gebruik van.
- Kies een standaardversie van Node.js.

<a name="iisnodelog"></a>
## De logboeken stdout en stderr ophalen uit iisnode

Voer de volgende stappen uit om iisnode-logboeken te lezen.

> [AZURE.NOTE] Wanneer u deze stappen hebt voltooid, is het mogelijk dat de logboekbestanden pas worden gegenereerd wanneer er een fout optreedt.

1. Open het bestand iisnode.yml dat door de Azure CLI wordt verstrekt.

2. Stel de volgende twee parameters in: 

        loggingEnabled: true
        logDirectory: iisnode
    
    Met deze twee parameters wordt aan iisnode in App Service doorgegeven dat de uitvoer van stdout en stderror moet worden geplaatst in de map D:\home\site\wwwroot\**iisnode**.

3. Sla de wijzigingen op en push ze met de volgende Git-opdrachten naar Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
   Iisnode is nu geconfigureerd. In de volgende stappen ziet u hoe u deze logboeken opent.
     
4. Ga in uw browser naar de Kudu-console voor foutopsporing. Deze bevindt zich op:

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    Dit is een andere URL dan die van de web-app, omdat '*.scm.*' is toegevoegd aan de DNS-naam. Als u deze aanvulling op de URL weglaat, krijgt u een 404-fout.

5. Navigeer naar D:\home\site\wwwroot\iisnode

    ![Navigeren naar de locatie van de iisnode-logboekbestanden][iislog-kudu-console-find]

6. Klik op het pictogram **Bewerken** voor het logboek dat u wilt lezen. U kunt desgewenst ook op **Downloaden** of **Verwijderen** klikken.

    ![Een iisnode-logboekbestand openen][iislog-kudu-console-open]

    Nu kunt u het logboek bekijken om u te helpen bij het opsporen van fouten in uw App Service-implementatie.
    
    ![Een iisnode-logboekbestand bestuderen][iislog-kudu-console-read]

## Fouten in een app opsporen met Node-Inspector

Als u Node-Inspector gebruikt om fouten in een Node.js-app op te sporen, kunt u dit hulpprogramma gebruiken voor live App Service-apps. Node-Inspector wordt vooraf geïnstalleerd, samen met de installatie van iisnode voor App Service. Als u de implementatie uitvoert via Git, bevat de automatisch gegenereerde Web.config van Kudu bovendien al de configuratie die u nodig hebt om Node-Inspector in te schakelen.

Volg deze stappen om Node-Inspector in te schakelen:

1. Open iisnode.yml in de hoofdmap van uw opslagplaats en geef de volgende parameters op: 

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Sla de wijzigingen op en push ze met de volgende Git-opdrachten naar Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. Navigeer nu naar het startbestand van uw app (dat is opgegeven in het startscript in uw package.json) met /debug toegevoegd aan de URL. Bijvoorbeeld:

        http://{appname}.azurewebsites.net/server.js/debug
    
    Of
    
        http://{appname}.azurewebsites.net/app.js/debug

## Meer bronnen

- [Een Node.js-versie opgeven in een Azure-toepassing](../nodejs-specify-node-version-azure-apps.md)
- [Fouten opsporen in een Node.js web-app in Azure App Service](web-sites-nodejs-debug.md)
- [Node.js-modules gebruiken met Azure-toepassingen](../nodejs-use-node-modules-azure-apps.md)
- [Web-apps van Azure App Service: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Node.js Developer Center](/develop/nodejs/)
- [Aan de slag met web-apps in Azure App Service](app-service-web-get-started.md)
- [De geheimen van de Kudu-console voor foutopsporing]

<!-- URL List -->

[Azure CLI]: ../xplat-cli-install.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[uw voordelen als Visual Studio-abonnee activeren]: http://go.microsoft.com/fwlink/?LinkId=623901
[BOWER]: http://bower.io/
[In Azure App Service een Node.js-chattoepassing maken met Socket.IO]: ./web-sites-nodejs-chat-app-socketio.md
[Een Sails.js-web-app implementeren in Azure App Service]: ./app-service-web-nodejs-sails.md
[De geheimen van de Kudu-console voor foutopsporing]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Express-generator voor Yeoman]: https://github.com/petecoop/generator-express
[GIT]: http://www.git-scm.com/downloads
[io.js gebruiken met Web Apps van Azure App Service]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[NODEJS]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[aanmelden voor een gratis proefversie]: http://go.microsoft.com/fwlink/?LinkId=623901
[web-app]: ./app-service-web-overview.md
[YEOMAN]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-zoeken]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-openen]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-lezen]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png



<!--HONumber=Jun16_HO2-->


