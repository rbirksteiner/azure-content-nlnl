<properties
    pageTitle="Aan de slag met API-apps en ASP.NET in App Service | Microsoft Azure"
    description="Ontdek hoe u met behulp van Visual Studio 2015 een ASP.NET API-app maakt, implementeert en gebruikt in Azure App Service."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="dotnet"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/27/2016"
    ms.author="tdykstra"/>

# Aan de slag met API-apps, ASP.NET en Swagger in Azure App Service

[AZURE.INCLUDE [selector](../../includes/app-service-api-get-started-selector.md)]

Dit is de eerste van een reeks zelfstudies die laten zien hoe u functies van Azure App Service kunt gebruiken die handig zijn voor het ontwikkelen en hosten van RESTful-API's.  In deze zelfstudie wordt ondersteuning voor API-metagegevens in Swagger-indeling besproken.

U leert het volgende:

* Het maken en implementeren van [API-apps](app-service-api-apps-why-best-platform.md) in Azure App Service met behulp van hulpprogramma's die zijn ingebouwd in Visual Studio 2015.
* Het automatiseren van API-detectie met behulp van het Swashbuckle NuGet-pakket om Swagger API-metagegevens dynamisch te genereren.
* Het gebruik van Swagger API-metagegevens voor het automatisch genereren van clientcode voor een API-app.

## Overzicht van voorbeeldtoepassing

In deze zelfstudie werkt u met een voorbeeld van een eenvoudige takenlijsttoepassing. De toepassing heeft een SPA-front-end (SPA staat voor 'Single-Page Application'), een ASP.NET-web-API als middelste laag en een ASP.NET-web-API als gegevenslaag.

![Diagram van API-apps-voorbeeldtoepassing](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

Hier volgt een schermafbeelding van de [AngularJS](https://angularjs.org/)-front-end.

![Voorbeeld van API-apps-takenlijsttoepassing](./media/app-service-api-dotnet-get-started/todospa.png)

De Visual Studio-oplossing omvat drie projecten:

![](./media/app-service-api-dotnet-get-started/projectsinse.png)

* **ToDoListAngular** - de front end: een AngularJS SPA die de middelste laag aanroept. 

* **ToDoListAPI** - de middelste laag: een ASP.NET Web API-project dat de gegevenslaag aanroept om CRUD-bewerkingen uit te voeren op taken.

* **ToDoListAPI** - de gegevenslaag: een ASP.NET Web API-project dat CRUD-bewerkingen uitvoert op taken. 

De architectuur met drie lagen is een van de vele architecturen die u kunt implementeren met behulp van API-apps en wordt hier alleen voor demonstratiedoeleinden gebruikt. Ter illustratie van de functies van API-apps is de code in elke laag zo eenvoudig mogelijk gehouden. De gegevenslaag maakt bijvoorbeeld voor persistentie gebruik van het geheugen van de server in plaats van een database.

Nadat u deze zelfstudie hebt voltooid, beschikt u over twee Web API-projecten die in de cloud worden uitgevoerd in App Service API-apps.

In de volgende zelfstudie in de reeks wordt de SPA-front-end in de cloud geïmplementeerd.

## Vereisten

* ASP.NET Web API: in de instructies in de zelfstudie wordt ervan uitgegaan dat u beschikt over basiskennis van het werken met ASP.NET [Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) in Visual Studio.

* Azure-account: u kunt [een gratis Azure-account openen](/pricing/free-trial/?WT.mc_id=A261C142F) of [uw voordelen als Visual Studio-abonnee activeren](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

    Als u met Azure App Service aan de slag wilt voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](http://go.microsoft.com/fwlink/?LinkId=523751). Daar kunt u direct een eenvoudige, tijdelijke app maken in App Service. U hebt hiervoor geen creditcard nodig en bent nergens toe verplicht.

* Visual Studio 2015 met de [Azure SDK voor .NET](http://go.microsoft.com/fwlink/?linkid=518003): de SDK installeert automatisch Visual Studio 2015 als u dit nog niet hebt.

    >[AZURE.NOTE] Afhankelijk van hoeveel van de SDK-afhankelijkheden u al op uw computer hebt staan, kan het installeren van de SDK lang duren, van enkele minuten tot een halfuur of meer.

## De voorbeeldtoepassing downloaden 

1. Download de opslagplaats [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list).

    U kunt klikken op de knop **ZIP downloaden** of de opslagplaats klonen op uw lokale computer. 

2. Open de ToDoList-oplossing in Visual Studio 2015 of 2013.

2. Maak de oplossing voor het herstellen van de NuGet-pakketten.

    Als u de toepassing in actie wilt zien voordat u deze implementeert, kunt u deze lokaal uitvoeren. Zorg ervoor dat alle drie de projecten opstartprojecten zijn. U moet Internet Explorer of Edge gebruiken omdat deze browsers cross-origin JavaScript-aanroepen toestaan naar `http://localhost`-URL's. 

## Swagger API-metagegevens en -gebruikersinterface gebruiken

Ondersteuning voor [Swagger](http://swagger.io/) 2.0 API-metagegevens is ingebouwd in Azure App Service. Elke API-app kan een URL-eindpunt opgeven dat metagegevens retourneert voor de API in Swagger JSON-indeling. De metagegevens die vanuit dat eindpunt worden geretourneerd, kunnen worden gebruikt voor het genereren van clientcode. 

Een ASP.NET Web API-project kan Swagger-metagegevens dynamisch genereren met behulp van het [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet-pakket. Het Swashbuckle NuGet-pakket is al geïnstalleerd in de ToDoListDataAPI- en ToDoListAPI-projecten die u hebt gedownload.

In deze sectie van de zelfstudie bekijkt u de gegenereerde Swagger 2.0-metagegevens en probeert u een testgebruikersinterface uit die hierop is gebaseerd.

2. Stel het project ToDoListDataAPI (**niet** het project ToDoListAPI) in als opstartproject. 
 
4. Druk op F5 of klik op **Fouten opsporen > Foutopsporing starten** om het project uit te voeren in de foutopsporingsmodus.

    De browser wordt geopend en u ziet de foutpagina HTTP 403.

12. Voeg in de adresbalk van uw browser `swagger/docs/v1` toe aan het einde van de regel en druk vervolgens op Enter. (De URL is `http://localhost:45914/swagger/docs/v1`.)

    Dit is de standaard-URL die wordt gebruikt om Swashbuckle Swagger 2.0 JSON-metagegevens te retourneren voor de API. 

    Als u Internet Explorer gebruikt, vraagt de browser u om een *v1.json*-bestand te downloaden.

    ![JSON-metagegevens downloaden in Internet Explorer](./media/app-service-api-dotnet-get-started/iev1json.png)

    Als u Chrome, Firefox of Edge gebruikt, wordt de JSON-code rechtstreeks in een browservenster weergegeven. Verschillende browsers verwerken JSON op een verschillende manier. Mogelijk ziet uw browservenster er daarom niet precies hetzelfde uit als in het voorbeeld.

    ![JSON-metagegevens in Chrome](./media/app-service-api-dotnet-get-started/chromev1json.png)

    Het volgende voorbeeld toont de eerste sectie van de Swagger-metagegevens voor de API, met de definitie voor de Get-methode. Deze metagegevens sturen de Swagger-gebruikersinterface aan die u in de volgende stappen nodig hebt. U gebruikt deze verderop in de zelfstudie voor het automatisch genereren van clientcode.

        {
          "swagger": "2.0",
          "info": {
            "version": "v1",
            "title": "ToDoListDataAPI"
          },
          "host": "localhost:45914",
          "schemes": [ "http" ],
          "paths": {
            "/api/ToDoList": {
              "get": {
                "tags": [ "ToDoList" ],
                "operationId": "ToDoList_GetByOwner",
                "consumes": [ ],
                "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
                "parameters": [
                  {
                    "name": "owner",
                    "in": "query",
                    "required": true,
                    "type": "string"
                  }
                ],
                "responses": {
                  "200": {
                    "description": "OK",
                    "schema": {
                      "type": "array",
                      "items": { "$ref": "#/definitions/ToDoItem" }
                    }
                  }
                },
                "deprecated": false
              },

1. Sluit de browser en stop de foutopsporing van Visual Studio.

3. Open in het project ToDoListDataAPI in **Solution Explorer** het bestand *App_Start\SwaggerConfig.cs*, blader naar de volgende code en verwijder het commentaarteken hierbij.

        /*
            })
        .EnableSwaggerUi(c =>
            {
        */

    Het bestand *SwaggerConfig.cs* wordt gemaakt wanneer u het pakket Swashbuckle in een project installeert. Het bestand biedt diverse manieren om Swashbuckle te configureren.

    Met de code waarbij u het commentaarteken hebt verwijderd, wordt de Swagger-gebruikersinterface ingeschakeld die u in de volgende stappen gebruikt. Wanneer u een Web API-project maakt met behulp van de API-app-projectsjabloon, wordt deze code als veiligheidsmaatregel standaard uitgecommentarieerd.

5. Voer het project opnieuw uit.

3. Voeg in de adresbalk van uw browser `swagger` toe aan het einde van de regel en druk vervolgens op Enter. (De URL is `http://localhost:45914/swagger`.)

4. Wanneer de pagina van de Swagger-gebruikersinterface wordt weergegeven, klikt u op **ToDoList** om na te gaan welke methoden er beschikbaar zijn.

    ![Beschikbare methoden in de Swagger-gebruikersinterface](./media/app-service-api-dotnet-get-started/methods.png)

5. Klik op de eerste knop **Get** in de lijst.

6. Geef in de sectie **Parameters** een sterretje op als de waarde van de `owner`-parameter en klik vervolgens op **Try it out**.

    Als u in latere zelfstudies verificatie toevoegt, biedt de middelste laag de werkelijke gebruikers-id voor de gegevenslaag. Zolang de toepassing wordt uitgevoerd zonder dat verificatie is ingeschakeld, hebben alle taken een sterretje als eigenaar-id.

    ![Try it out in de Swagger-gebruikersinterface](./media/app-service-api-dotnet-get-started/gettryitout1.png)

    De Swagger-gebruikersinterface roept deToDoList Get-methode aan en geeft de responscode en JSON-resultaten weer.

    ![Resultaten van Try it out in de Swagger-gebruikersinterface](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Klik op **Post** en vervolgens op het vak onder **Model Schema**.

    Als u op het modelschema klikt, worden er gegevens ingevuld in het invoervak waarin u de waarde van parameter voor de Post-methode kunt opgeven. (Als dit in Internet Explorer niet werkt, probeert u een andere browser of voert u in de volgende stap de waarde van de parameter handmatig in.)  

    ![Post voor Try it out in de Swagger-gebruikersinterface](./media/app-service-api-dotnet-get-started/post.png)

7. Wijzig de JSON-code in het invoervak van de `todo`-parameter, zodat deze lijkt op het volgende voorbeeld of vervang deze door uw eigen beschrijvende tekst:

        {
          "ID": 2,
          "Description": "buy the dog a toy",
          "Owner": "*"
        }

10. Klik op **Try it out**.

    De ToDoList-API retourneert een 204 HTTP-responscode waarmee wordt aangegeven dat de bewerking is geslaagd.

11. Klik op de eerste **Get**-knop en klik vervolgens in die sectie van de pagina op de knop **Try it out**.

    De respons van de Get-methode bevat nu de nieuwe taak. 

12. Optioneel: probeer ook de methoden Put, Delete en Get by ID.

14. Sluit de browser en stop de foutopsporing van Visual Studio.

Swashbuckle werkt met elk ASP.NET Web API-project. Als u het genereren van Swagger-metagegevens wilt toevoegen aan een bestaand project, hoeft u hiervoor alleen het Swashbuckle-pakket te installeren. 

**Opmerking:** de Swagger-metagegevens bevatten voor elke API-bewerking een unieke id. Standaard kan Swashbuckle dubbele Swagger-bewerkings-id's genereren voor uw Web API-controllermethoden. Dit gebeurt als uw domeincontroller overbelaste HTTP-methoden bevat, zoals `Get()` en `Get(id)`. Zie [Door Swashbuckle gegenereerde API-definities aanpassen](app-service-api-dotnet-swashbuckle-customize.md) voor meer informatie over het afhandelen van overbelasting. Als u in Visual Studio een Web API-project maakt met de Azure-API-app-sjabloon, wordt er aan het bestand *SwaggerConfig.cs* automatisch code toegevoegd die unieke bewerkings-id's geneert.  

## <a id="createapiapp"></a> Een API-app in Azure maken en er code in implementeren

In deze sectie gebruikt u de Azure-hulpprogramma's die in de wizard **Publish Web** van Visual Studio zijn geïntegreerd, voor het maken van een nieuwe API-app in Azure. Vervolgens implementeert u het project ToDoListDataAPI in de nieuwe API-app en roept u de API aan door de Swagger-gebruikersinterface uit te voeren.

1. Klik in **Solution Explorer** met de rechtermuisknop op het project ToDoListDataAPI. Klik vervolgens op **Publish**.

    ![Klikken op Publish in Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu.png)

3.  Klik in de stap **Profile** van de wizard **Publish Web** op **Microsoft Azure App Service**.

    ![Klikken op Azure App Service in de wizard Publish Web](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Meld u aan bij uw Azure-account als u dit nog niet hebt gedaan. Vernieuw uw referenties als deze zijn verlopen.

4. Kies in het dialoogvenster App Service bij **Subscription** het Azure-abonnement dat u wilt gebruiken en klik vervolgens op **New**.

    ![Klikken op New in het dialoogvenster App Service](./media/app-service-api-dotnet-get-started/clicknew.png)

    Het tabblad **Hosting** van het dialoogvenster **Create App Service** wordt weergegeven.

    Omdat u een Web API-project implementeert waarin Swashbuckle is geïnstalleerd, neemt Visual Studio aan dat u een API-app wilt maken. Dit wordt aangegeven door de titel **API App Name** en door het feit dat de vervolgkeuzelijst **Change Type** is ingesteld op **API App**.

    ![App-type in het dialoogvenster App Service](./media/app-service-api-dotnet-get-started/apptype.png)

4. Voer bij **API App Name** een naam in die uniek is in het domein *azurewebsites.net*. U kunt de standaardnaam accepteren die door Visual Studio wordt voorgesteld.

    Als u een naam opgeeft die iemand anders al gebruikt, ziet u aan de rechterkant een rood uitroepteken.

    De URL van de API-app wordt `{APi app name}.azurewebsites.net`.

6. Klik in de vervolgkeuzelijst **Resource Group** op **New** en voer vervolgens ToDoListGroup of desgewenst een andere naam in. 

    Een resourcegroep is een verzameling Azure-resources, zoals web-apps, databases, virtuele machines, enzovoort. Voor deze zelfstudie kunt u het beste een nieuwe resourcegroep maken. U kunt dan eenvoudig in één stap alle Azure-resources verwijderen die u tijdens de zelfstudie maakt.

    In dit vak kunt u een bestaande [resourcegroep](../azure-portal/resource-group-portal.md) selecteren of een nieuwe maken door een naam te typen die anders is dan alle bestaande resourcegroepen in uw abonnement.

4. Klik naast de vervolgkeuzelijst **App Service Plan** op de knop **New**.

    De schermafbeelding toont voorbeeldwaarden voor **API App Name**, **Subscription** en **Resource Group**. De door u gebruikte waarden zijn anders.

    ![Het dialoogvenster Create App Service](./media/app-service-api-dotnet-get-started/createas.png)

    In de volgende stappen maakt u een App Service-plan voor de nieuwe resourcegroep. In een App Service-plan worden de rekenresources opgegeven op basis waarvan uw API-app wordt uitgevoerd. Als u bijvoorbeeld de gratis categorie hebt gekozen, wordt uw API-app uitgevoerd op basis van gedeelde virtuele machines. Bij sommige categorieën waar u voor betaalt, worden apps uitgevoerd via exclusieve virtuele machines. Zie [Overzicht van App Service-plannen](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) voor informatie over App Service-plannen

5. Geef in het dialoogvenster **Configure App Service Plan** de naam ToDoListPlan op. Desgewenst kunt u ook een andere naam gebruiken.

5. Kies in de vervolgkeuzelijst **Location** de locatie die het dichtst bij u ligt.

    Met deze instelling bepaalt u in welk Azure-datacentrum uw app wordt uitgevoerd. Kies een locatie dicht bij u in de buurt om de [latentie](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090) te minimaliseren.

5. Klik in de vervolgkeuzelijst **Size** op **Free**.

    Voor deze zelfstudie levert de prijscategorie Free voldoende prestaties.

6. Klik in het dialoogvenster **Configure App Service Plan** op **OK**.

    ![Klikken op OK in het dialoogvenster Configure App Service Plan](./media/app-service-api-dotnet-get-started/configasp.png)

7. Klik in het dialoogvenster **Create App Service** op **Create**.

    ![Klikken op Create in het dialoogvenster Create App Service](./media/app-service-api-dotnet-get-started/clickcreate.png)

    Visual Studio maakt de API-app en een publicatieprofiel met alle vereiste instellingen voor de API-app. Vervolgens wordt de wizard **Publish Web** geopend. Deze wizard gebruikt u voor het implementeren van het project.

    De wizard **Publish Web** wordt geopend op het tabblad **Connection** (hieronder getoond). 

    Op het tabblad **Connection** verwijzen de instellingen bij **Server** en **Site name** naar uw API-app. **User name** en **Password** zijn implementatiereferenties die Azure voor u maakt. Na de implementatie opent Visual Studio een browservenster met de **doel-URL** (dat is de enige functie van **doel-URL**).  

8. Klik op **Next**. 

    ![Klikken op Next op het tabblad Connection van de wizard Publish Web](./media/app-service-api-dotnet-get-started/connnext.png)

    Het volgende tabblad is het tabblad **Settings** (hieronder getoond). Hier kunt u het buildconfiguratietabblad wijzigen om een build voor foutopsporing te implementeren voor [foutopsporing op afstand](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). Het tabblad biedt ook diverse **opties voor het publiceren van bestanden**:

    * Aanvullende bestanden op de bestemming verwijderen
    * Voorcompileren tijdens het publiceren
    * Bestanden uitsluiten van de map App_Data

    Voor deze zelfstudie hebt u geen van deze opties nodig. Zie [Procedure: een webproject implementeren met behulp van publicatie met één klik in Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx) voor gedetailleerde uitleg over deze opties.

14. Klik op **Next**.

    ![Klikken op Next op het tabblad Settings van de wizard Publish Web](./media/app-service-api-dotnet-get-started/settingsnext.png)

    Hierna volgt het tabblad **Preview** (hieronder getoond), waar u kunt bekijken welke bestanden van uw project er naar de API-app worden gekopieerd. Wanneer u een project implementeert in een API-app die u al eerder had geïmplementeerd, worden alleen de gewijzigde bestanden gekopieerd. Als u een overzicht wilt zien van wat er wordt gekopieerd, klikt u op de knop **Start Preview**.

15. Klik op **Publish**.

    ![Klikken op Publish op het tabblad Preview van de wizard Publish Web](./media/app-service-api-dotnet-get-started/clickpublish.png)

    Visual Studio implementeert het project ToDoListDataAPI in de nieuwe API-app. In het venster **Output** wordt bevestigd dat de implementatie is geslaagd. Ook wordt de URL van de API-app in een browservenster geopend, waarna er een pagina wordt weergegeven met de melding dat de nieuwe API-app is gemaakt.

    ![Bevestiging van geslaagde implementatie in het venster Output](./media/app-service-api-dotnet-get-started/deploymentoutput.png)

    ![Pagina met melding dat de nieuwe API-app is gemaakt](./media/app-service-api-dotnet-get-started/appcreated.png)

11. Voeg 'swagger' toe aan de URL in de adresbalk van de browser en druk op Enter. (De URL is `http://{apiappname}.azurewebsites.net/swagger`.)

    De browser geeft dezelfde Swagger-gebruikersinterface weer die u eerder hebt gezien, maar deze wordt nu uitgevoerd in de cloud. Probeer de Get-methode uit en u zult zien dat u bent teruggekeerd naar de twee standaardtaken. De wijzigingen die u eerder hebt doorgevoerd, zijn in het geheugen van de lokale computer opgeslagen.

12. Open de [Azure Portal](https://portal.azure.com/).

    De Azure Portal is een webinterface voor het beheer van Azure-bronnen zoals API-apps.
 
14. Klik op **Bladeren > App Services**.

    ![Bladeren door App Services](./media/app-service-api-dotnet-get-started/browseas.png)

15. Zoek uw nieuwe API-app op op de blade **App Services** en klik erop. (In de Azure Portal worden de vensters die aan de rechterkant worden geopend, *blades* genoemd.)

    ![De blade App Services](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

    Er worden twee blades geopend. De eerste blade bevat een overzicht van de API-app. De tweede bevat een lange lijst met instellingen die u kunt bekijken en wijzigen.

16. Ga op de blade **Instellingen** naar de sectie **API** en klik op **API-definitie**. 

    ![API-definitie op de blade Instellingen](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

    Op de blade **API-definitie** kunt u de URL opgeven die de Swagger 2.0-metagegevens in JSON-indeling retourneert. Wanneer Visual Studio de API-app maakt, wordt de URL van de API-definitie ingesteld op de standaardwaarde voor door Swashbuckle gegenereerde metagegevens die u eerder hebt gezien. Dit zijn de basis-URL van de API-app plus `/swagger/docs/v1`. 

    ![URL van de API-definitie](./media/app-service-api-dotnet-get-started/apidefurl.png)

    Wanneer u een API-app selecteert om er clientcode voor te genereren, worden de metagegevens door Visual Studio opgehaald via deze URL. 

## <a id="codegen"></a> Clientcode genereren voor de gegevenslaag

Een van de voordelen van de integratie van Swagger in Azure API-apps is het automatisch genereren van code. Gegenereerde clientklassen maken het gemakkelijker code te schrijven die een API-app aanroepen.

Het project ToDoListAPI heeft de gegenereerde clientcode al, maar in de volgende stappen gaat u deze verwijderen en opnieuw genereren om te leren hoe dit in zijn werk gaat.

1. Verwijder in Visual Studio **Solution Explorer** in het project ToDoListAPI de map *ToDoListDataAPI*. **Waarschuwing: verwijder alleen de map, niet het project ToDoListDataAPI.**

    ![Gegenereerde clientcode verwijderen](./media/app-service-api-dotnet-get-started/deletecodegen.png)

    Deze map is gemaakt met behulp van het proces voor het genereren van code dat u zo dadelijk gaat doorlopen.

2. Klik met de rechtermuisknop op het project ToDoListAPI en klik vervolgens op **Add > REST API Client**.

    ![REST-API-client toevoegen in Visual Studio](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Klik in het dialoogvenster **Add REST API Client** op **Swagger URL**. Klik vervolgens op **Select Azure Asset**.

    ![Azure-asset selecteren](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. Vouw in het dialoogvenster **App Service** de resourcegroep uit die u voor deze zelfstudie gebruikt en selecteer uw API-app. Klik vervolgens op **OK**.

    ![API-app selecteren voor het genereren van code](./media/app-service-api-dotnet-get-started/codegenselect.png)

    Als u terugkeert naar het dialoogvenster **Add REST API Client**, zult u zien dat in het tekstvak de URL-waarde van de API-definitie die u eerder in de portal zag, is ingevuld. 

    ![URL van de API-definitie](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

    >[AZURE.TIP] Behalve via het bladerdialoogvenster kunt u de metagegevens voor het genereren van code ook ophalen door de URL direct in te voeren. Als u clientcode wilt genereren voordat u Azure implementeert, kunt u bovendien ook het volgende doen: voer het Web API-project lokaal uit, ga naar de URL die het Swagger JSON-bestand aanlevert, sla het bestand op en selecteer de optie **Select an existing Swagger metadata file**.

9. Klik in het dialoogvenster **Add REST API Client** op **OK**.

    Visual Studio maakt een map met de naam van de API-app en genereert clientklassen.

    ![Codebestanden voor gegenereerde client](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. Open in het project ToDoListAPI *Controllers\ToDoListController.cs* om de code te zien die de API aanroept met behulp van de gegenereerde client. 

    Het volgende fragment toont hoe de code het clientobject instantieert en de Get-methode aanroept.

        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
            return client;
        }
        
        public async Task<IEnumerable<ToDoItem>> Get()
        {
            using (var client = NewDataAPIClient())
            {
                var results = await client.ToDoList.GetByOwnerAsync(owner);
                return results.Select(m => new ToDoItem
                {
                    Description = m.Description,
                    ID = (int)m.ID,
                    Owner = m.Owner
                });
            }
        }

    De constructorparameter haalt de eindpunt-URL uit de `toDoListDataAPIURL`-appinstelling. Deze waarde is in het bestand Web.config ingesteld op de lokale IIS Express URL van het API-project, zodat u de toepassing lokaal kunt uitvoeren. Als u de constructorparameter weglaat, wordt het standaardeindpunt de URL waaruit u de code hebt gegenereerd.

6. De clientklasse wordt gegenereerd met een andere naam op basis van de naam van de API-app; wijzig de code in *Controllers\ToDoListController.cs*, zodat de typenaam overeenkomt met wat er in uw project is gegenereerd. Als de naam van uw API-app bijvoorbeeld ToDoListDataAPI0121 is, zou u deze code wijzigen:

        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));

in deze:

        private static ToDoListDataAPI0121 NewDataAPIClient()
        {
            var client = new ToDoListDataAPI0121(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));


## Een API-app maken voor het hosten van de middelste laag

Eerder hebt u [de API-app voor de gegevenslaag gemaakt en er code in geïmplementeerd](#createapiapp).  Nu volgt u dezelfde procedure voor de API-app voor de middelste laag. 

1. Klik in **Solution Explorer** met de rechtermuisknop op het ToDoListAPI-project voor de middelste laag (niet op de ToDoListDataAPI voor de gegevenslaag) en klik vervolgens op **Publish**.

    ![Klikken op Publish in Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu2.png)

3.  Klik op het tabblad **Profile** van de wizard **Publish Web** op **Microsoft Azure App Service**.

5. Klik in het dialoogvenster **App Service** op **New**.

3. Accepteer op het tabblad **Hosting** van het dialoogvenster **Create App Service** de standaardnaam bij **API App Name** of voer een andere naam in die uniek is in het domein *azurewebsites.net*. 

5. Kies in **Subscription** het Azure-abonnement dat u momenteel gebruikt.

6. Kies in de vervolgkeuzelijst **Resource Group** dezelfde resourcegroep die u eerder hebt gemaakt.

4. Kies in de vervolgkeuzelijst **App Service Plan** hetzelfde abonnement dat u eerder hebt gemaakt. Deze waarde wordt standaard ingesteld. 

7. Klik op **Create**.

    Visual Studio maakt de API-app, maakt er een publicatieprofiel voor en geeft de stap **Connection** van de wizard **Publish Web** weer.

3.  Klik in de stap **Connection** van de wizard **Publish Web** op **Publish**.

    Visual Studio implementeert het project TToDoListAPI in de nieuwe API-app en opent een browservenster met de URL van de API-app. Er wordt een pagina weergegeven met de melding dat het maken is gelukt.

## De middelste laag configureren voor het aanroepen van de gegevenslaag

Als u de API-app voor de middelste laag nu aanroept, probeert deze de gegevenslaag aan te roepen via de localhost-URL die zich nog in het bestand Web.config bevindt. In deze sectie kunt u de API-app-URL voor de gegevenslaag invoeren in een omgevingsinstelling in de API-app voor de middelste laag. Wanneer de code in de API-app voor de middelste laag de URL-instelling van de gegevenslaag ophaalt, overschrijft de omgevingsinstelling de inhoud van het bestand Web.config. 
 
1. Ga naar de [Azure Portal](https://portal.azure.com/) en navigeer naar de blade **API-app** van de API-app die u als host voor het project TodoListAPI (middelste laag) hebt gemaakt.

2. Klik op de blade **Instellingen** van de API-app op **Toepassingsinstellingen**.
 
4. Ga op de blade **Toepassingsinstellingen** omlaag naar de sectie **App-instellingen** en voeg de volgende sleutel en waarde toe:

  	| **Sleutel** | toDoListDataAPIURL |
  	|---|---|
  	| **Waarde** | https://{de naam van uw API-app voor de gegevenslaag}.azurewebsites.net |
  	| **Voorbeeld** | https://todolistdataapi0121.azurewebsites.net |

4. Klik op **Opslaan**.

    ![Klikken op Opslaan in het gedeelte App-instellingen](./media/app-service-api-dotnet-get-started/asinportal.png)

    Wanneer de code wordt uitgevoerd in Azure, overschrijft deze waarde de localhost-URL die zich in het Web.config-bestand bevindt. 

## Testen

11. Open in een browservenster de URL van de nieuwe API-app voor de middelste laag die u zojuist hebt gemaakt voor ToDoListAPI. Klik hiertoe op de URL op de hoofdblade van de API-app in de portal.

13. Voeg 'swagger' toe aan de URL in de adresbalk van de browser en druk op Enter. (De URL is `http://{apiappname}.azurewebsites.net/swagger`.)

    In de browser wordt dezelfde Swagger-gebruikersinterface weergegeven die u eerder hebt gezien voor ToDoListDataAPI. Nu is `owner` echter geen verplicht veld voor de Get-bewerking, omdat de API-app voor de middelste laag die waarde voor u naar de API-app voor de gegevenslaag verzendt. (Wanneer u de zelfstudies over verificatie volgt, verzendt de middelste laag werkelijke gebruikers-id’s voor de `owner`-parameter. In deze zelfstudie gebruiken we een hard gecodeerd sterretje.)

12. Probeer de Get-methode en de andere methoden om te valideren dat de API-app voor de middelste laag de API-app voor de gegevenslaag op correcte wijze aanroept.

    ![Get-methode van de Swagger-gebruikersinterface](./media/app-service-api-dotnet-get-started/midtierget.png)

## Problemen oplossen

Als u tijdens het doorlopen van deze zelfstudie tegen problemen aanloopt, vindt u hier enkele ideeën voor probleemoplossing.

* Zorg ervoor dat u de nieuwste versie van de [Azure SDK voor .NET](http://go.microsoft.com/fwlink/?linkid=518003) gebruikt.

* Twee van de projectnamen lijken erg op elkaar (ToDoListAPI, ToDoListDataAPI). Als dingen er tijdens het werken met een project niet zo uitzien als ze worden beschreven in de instructies, controleer dan of u het juiste project hebt geopend.

* Gebruikt u een bedrijfsnetwerk en probeert u Azure App Service via een firewall te implementeren, zorg er dan voor dat de poorten 443 en 8172 zijn geopend voor Web Deploy. Als u deze poorten niet kunt openen, gebruik dan een andere implementatiemethode.  Zie [Een app implementeren in Azure App Service](../app-service-web/web-sites-deploy.md).

* Fouten van het type 'Routenamen moeten uniek zijn': mogelijk doen deze zich voor als u per ongeluk het verkeerde project in een API-app implementeert en vervolgens later het juiste project implementeert. U corrigeert dergelijke fouten door het juiste project opnieuw in de API-app te implementeren en op het tabblad **Settings** van de wizard **Publish Web** de optie **Remove additional files at destination** te selecteren.

Wanneer uw ASP.NET-API-app eenmaal wordt uitgevoerd in Azure App Service, doet u er verstandig aan u te verdiepen in de Visual Studio-functies die het oplossen van problemen vereenvoudigen. Voor meer informatie over logboekregistratie, foutopsporing op afstand en meer raadpleegt u [Problemen met Azure App Service-apps in Visual Studio oplossen](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## Volgende stappen

U hebt gezien hoe u bestaande Web API-projecten in API-apps kunt implementeren, clientcode kunt genereren voor API-apps en API-apps vanuit .NET-clients kunt gebruiken. In de volgende zelfstudie in deze reeks ziet u hoe u [CORS kunt toepassen om API-apps vanuit JavaScript-clients te gebruiken](app-service-api-cors-consume-javascript.md).
 
Zie de [Azure/AutoRest](https://github.com/azure/autorest)-opslagplaats op GitHub.com voor meer informatie over het genereren van clientcode. Open een [actie-item in de AutoRest-opslagplaats](https://github.com/azure/autorest/issues) voor hulp bij problemen met het gebruik van de gegenereerde client.

Als u geheel nieuwe API-app-projecten wilt maken, gebruikt u de **Azure-API-app**-sjabloon.

![API-app-sjabloon in Visual Studio](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

Gebruik van de **Azure-API-app**-projectsjabloon geeft hetzelfde resultaat als wanneer u de **lege** ASP.NET 4.5.2-sjabloon kiest, het selectievakje inschakelt om Web API-ondersteuning toe te voegen en vervolgens het Swashbuckle NuGet-pakket installeert. Daarnaast voegt de sjabloon Swashbuckle-configuratiecode toe die tot doel heeft om te voorkomen dat er dubbele Swagger-bewerkings-id's worden gemaakt. Nadat u een API-app-project hebt gemaakt, kunt u dit op dezelfde manier implementeren in een API-app als u in deze zelfstudie hebt gezien.



<!--HONumber=Jun16_HO2-->


