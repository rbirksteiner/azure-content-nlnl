<properties
    pageTitle="CORS-ondersteuning in App Service | Microsoft Azure"
    description="Informatie over het gebruik van CORS-ondersteuning in Azure App Service."
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
    ms.topic="get-started-article"
    ms.date="03/31/2016"
    ms.author="tdykstra"/>

# Een API-app van JavaScript gebruiken met CORS

App Service biedt ingebouwde ondersteuning voor [CORS (Cross Origin Resource Sharing)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing), waarmee JavaScript-clients aanroepen tussen domeinen kunnen maken naar API's die worden gehost in API-apps. Met App Service kunt u CORS-toegang tot uw API configureren zonder dat u in de API code hoeft te schrijven.

Dit artikel bestaat uit twee gedeelten:

* Eerst wordt in de sectie [CORS configureren](#corsconfig) in grote lijnen uitgelegd hoe u CORS configureert voor een API-app, web-app of mobiele app. Deze informatie heeft ook betrekking op alle frameworks die door App Service worden ondersteund, waaronder .NET, Node.js en Java. 

* Vanaf de sectie [Vervolg van de zelfstudie Aan de slag met .NET](#tutorialstart) is het artikel een zelfstudie. Hierin leert u hoe CORS-ondersteuning wordt toegepast, door voort te bouwen op wat u hebt gedaan in [de eerste zelfstudie Aan de slag met API-apps](app-service-api-dotnet-get-started.md). 

## <a id="corsconfig"></a> CORS configureren in Azure App Service

U kunt CORS configureren in de Azure Portal of met behulp van de [Azure Resource Manager](../resource-group-overview.md)-hulpprogramma's.

#### CORS configureren in de Azure Portal

8. Ga in een browser naar de [Azure Portal](https://portal.azure.com/).

2. Klik op **App Services** en vervolgens op de naam van de API-app.

    ![De API-app in de portal selecteren](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. Ga op de blade **Instellingen**, die rechts naast de blade **API-app** wordt geopend, naar de sectie **API** en klik vervolgens op **CORS**.

    ![CORS selecteren op de blade Instellingen](./media/app-service-api-cors-consume-javascript/clicksettings.png)

11. Voer in het tekstvak de URL of URL's in waarvan JavaScript-aanroepen afkomstig mogen zijn.


    Als u uw JavaScript-toepassing bijvoorbeeld hebt geïmplementeerd in een web-app met de naam todolistangular, voert u 'https://todolistangular.azurewebsites.net' in. Als alternatief kunt u een sterretje (*) invoeren als u wilt opgeven dat alle domeinen worden geaccepteerd.


13. Klik op **Opslaan**.

    ![Op Opslaan klikken](./media/app-service-api-cors-consume-javascript/corsinportal.png)

    Nadat u op **Opslaan** hebt geklikt, accepteert de API-app JavaScript-aanroepen vanuit de opgegeven URL's.

#### CORS configureren met de Azure Resource Manager-hulpprogramma's

U kunt CORS ook configureren voor een API-app met behulp van [Azure Resource Manager-sjablonen](../resource-group-authoring-templates.md) in opdrachtregelhulpprogramma's zoals [Azure PowerShell](../powershell-install-configure.md) en de [Azure CLI](../xplat-cli-install.md). 

Voor een voorbeeld van een Azure Resource Manager-sjabloon die de CORS-eigenschap instelt, opent u het [bestand azuredeploy.json in de opslagplaats voor de voorbeeldtoepassing uit deze zelfstudie](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Ga naar het gedeelte van de sjabloon die lijkt op het volgende voorbeeld:

        "cors": {
            "allowedOrigins": [
                "todolistangular.azurewebsites.net"
            ]
        }

## <a id="tutorialstart"></a> Vervolg van de zelfstudie Aan de slag met .NET

Als u de reeks Aan de slag met Node.js of Java voor API-apps volgt, hebt u de Aan de slag-reeks nu voltooid. Ga naar de sectie [Volgende stappen](#next-steps) voor suggesties voor meer informatiebronnen over API-apps.

De rest van dit artikel is een vervolg van de reeks Aan de slag met .NET. Er wordt van uitgegaan dat u [de eerste zelfstudie](app-service-api-dotnet-get-started.md) met succes hebt voltooid.

## Het project ToDoListAngular implementeren in een nieuwe web-app

In [de eerste zelfstudie](app-service-api-dotnet-get-started.md) hebt u een API-app voor de middelste laag en een API-app voor de gegevenslaag gemaakt. In deze zelfstudie maakt u een SPA-web-app (SPA staat voor 'Single-Page Application') die de API-app voor de middelste laag aanroept. Voor de SPA moet u CORS inschakelen in de API-app voor de middelste laag. 

In de [voorbeeldtoepassing ToDoList](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) is het project ToDoListAngular een eenvoudige AngularJS-client die het ToDoListAPI Web API-project voor de middelste laag aanroept. De JavaScript-code in het bestand *app/scripts/todoListSvc.js* roept de API aan met behulp van de AngularJS HTTP-provider. 

        angular.module('todoApp')
        .factory('todoListSvc', ['$http', function ($http) {
            var apiEndpoint = "http://localhost:46439";
        
            $http.defaults.useXDomain = true;
            delete $http.defaults.headers.common['X-Requested-With']; 
        
            return {
                getItems : function(){
                    return $http.get(apiEndpoint + '/api/TodoList');
                },

                /* Get by ID, Put, and Delete methods not shown */

                postItem : function(item){
                    return $http.post(apiEndpoint + '/api/TodoList', item);
                }
            };
        }]);

### Een nieuwe web-app maken voor het project ToDoListAngular

De procedure voor het maken van een nieuwe App Service-web-app en het vervolgens implementeren hiervan in een project is vergelijkbaar met wat u hebt gezien voor [het maken en implementeren van een API-app in de eerste zelfstudie van deze reeks](app-service-api-dotnet-get-started.md#createapiapp). Het enige verschil is dat het type app **Web-app** is in plaats van **API-app**.  Zie voor schermafbeeldingen van de dialoogvensters 

1. Klik in **Solution Explorer** met de rechtermuisknop op het project ToDoListAngular. Klik vervolgens op **Publish**.

3.  Klik op het tabblad **Profile** van de wizard **Publish Web** op **Microsoft Azure App Service**.

5. Klik in het dialoogvenster **App Service** op **New**.

3. Voer op het tabblad **Hosting** van het dialoogvenster **Create App Service** in het veld **Web App Name** een naam in die uniek is in het domein *azurewebsites.net*. 

5. Kies in **Subscription** het Azure-abonnement waarmee u wilt werken.

6. Kies in de vervolgkeuzelijst **Resource Group** dezelfde resourcegroep die u eerder hebt gemaakt.

4. Kies in de vervolgkeuzelijst **App Service Plan** hetzelfde abonnement dat u eerder hebt gemaakt. 

7. Klik op **Create**.

    Visual Studio maakt de web-app, maakt er een publicatieprofiel voor en geeft de stap **Connection** van de wizard **Publish Web** weer.

    Klik nog niet op **Publish**. In de volgende sectie configureert u de nieuwe web-app om de API-app voor de middelste laag aan te roepen die wordt uitgevoerd in de App Service. 

### De URL voor de middelste laag instellen in de instellingen voor web-apps

1. Ga naar de [Azure Portal](https://portal.azure.com/) en navigeer vervolgens naar de blade **Web-app** voor de web-app die u hebt gemaakt als host voor het (front-end-)project TodoListAngular.

2. Klik op **Instellingen > Toepassingsinstellingen**.

3. Geef in de sectie **App-instellingen** de volgende sleutel en waarde op:

  	|Sleutel|Waarde|Voorbeeld
  	|---|---|---|
  	|toDoListAPIURL|https://{de naam van uw API-app voor de middelste laag}.azurewebsites.net|https://todolistapi0121.azurewebsites.net|

4. Klik op **Opslaan**.

    Wanneer de code in Azure wordt uitgevoerd, overschrijft deze waarde de localhost-URL die zich in het *Web.config*-bestand bevindt. 

    De code die de instellingswaarde krijgt, bevindt zich in *index.cshtml*:

        <script type="text/javascript">
            var apiEndpoint = "@System.Configuration.ConfigurationManager.AppSettings["toDoListAPIURL"]";
        </script>
        <script src="app/scripts/todoListSvc.js"></script>

    De code in *todoListSvc.js* maakt gebruik van de instelling:

        return {
            getItems : function(){
                return $http.get(apiEndpoint + '/api/TodoList');
            },
            getItem : function(id){
                return $http.get(apiEndpoint + '/api/TodoList/' + id);
            },
            postItem : function(item){
                return $http.post(apiEndpoint + '/api/TodoList', item);
            },
            putItem : function(item){
                return $http.put(apiEndpoint + '/api/TodoList/', item);
            },
            deleteItem : function(id){
                return $http({
                    method: 'DELETE',
                    url: apiEndpoint + '/api/TodoList/' + id
                });
            }
        };

### Het webproject ToDoListAngular implementeren in een nieuwe web-app

*  Klik in Visual Studio in de stap **Connection** van de wizard **Publish Web** op **Publish**.

    Visual Studio implementeert het project ToDoListAngular in de nieuwe web-app en opent een browservenster met de URL van de web-app. 

### De toepassing testen zonder dat CORS is ingeschakeld 

2. Open in uw browser het consolevenster van de ontwikkelhulpprogramma’s.

3. Klik in het browservenster waarin de AngularJS-gebruikersinterface wordt weergegeven, op de koppeling **To Do List**.

    De JavaScript-code probeert de API-app voor de middelste laag aan te roepen, maar de aanroep mislukt omdat de front-end in een ander domein wordt uitgevoerd dan de back-end. In het consolevenster van de ontwikkelhulpprogramma’s van de browser wordt een cross-origin-foutbericht weergegeven.

    ![Cross-origin-foutbericht](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## CORS configureren voor de API-app voor de middelste laag

In deze sectie configureert u de CORS-instelling in Azure voor ToDoListAPI, de API-app voor de middelste laag. Met deze instelling kan de API-app voor de middelste laag JavaScript-aanroepen ontvangen van de web-app die u hebt gemaakt voor het project ToDoListAngular.

8. Ga in een browser naar de [Azure Portal](https://portal.azure.com/).

2. Klik op **App Services** en vervolgens op de API-app ToDoListAPI (middelste laag).

    ![De API-app in de portal selecteren](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. Ga op de blade **Instellingen**, die rechts naast de blade **API-app** wordt geopend, naar de sectie **API** en klik vervolgens op **CORS**.

    ![CORS selecteren in de portal](./media/app-service-api-cors-consume-javascript/clicksettings.png)

12. Voer in het tekstvak de URL voor de web-app ToDoListAngular (front-end) in. Hebt u het project ToDoListAngular bijvoorbeeld geïmplementeerd voor een web-app met de naam todolistangular0121, dan staat u aanroepen toe van de URL `https://todolistangular0121.azurewebsites.net`.

    Als alternatief kunt u een sterretje (*) invoeren als u wilt opgeven dat alle domeinen worden geaccepteerd.

13. Klik op **Opslaan**.

    ![Op Opslaan klikken](./media/app-service-api-cors-consume-javascript/corsinportal.png)

    Nadat u op **Opslaan** hebt geklikt, accepteert de API-app JavaScript-aanroepen vanuit de opgegeven URL. Op deze schermafbeelding accepteert de API-app ToDoListAPI0223 JavaScript-clientaanroepen van de web-app ToDoListAngular.

### De toepassing testen met CORS ingeschakeld

* Open een browservenster met de HTTPS-URL van de web-app. 

    De toepassing staat u nu toe taken te bekijken, toe te voegen, te bewerken en te verwijderen. 

    ![Takenlijstpagina van voorbeeld-app](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## App Service CORS versus Web API CORS

In een Web API-project kunt u het [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet-pakket installeren om in de code op te geven vanuit welke domeinen uw API JavaScript-aanroepen accepteert.
 
Ondersteuning voor Web API CORS biedt meer flexibiliteit dan ondersteuning voor App Service CORS. Zo kunt u in de code voor verschillende actiemethoden verschillende toegestane bronnen opgeven. Voor App Service CORS kunt u voor alle methoden van uw API-app slechts één set toegestane bronnen opgeven.

> [AZURE.NOTE] Gebruik Web API CORS en App Service CORS niet samen in één API-app. Doet u dit toch, dan krijgt App Service CORS voorrang en heeft Web API CORS geen effect. Als u bijvoorbeeld in App Service één brondomein inschakelt en in uw Web API-code alle brondomeinen, accepteert uw Azure API-app alleen aanroepen van het domein dat u in Azure hebt opgegeven.

### CORS inschakelen in de web API-code

De volgende stappen geven een overzicht van het proces voor het inschakelen van ondersteuning voor Web API CORS. Zie [Cross-origin-aanvragen inschakelen in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) voor meer informatie.

1. Installeer in een Web API-project het [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet-pakket.

1. Voeg een `config.EnableCors()`coderegel in de **Register**-methode van de **WebApiConfig**-klasse toe, zoals in het volgende voorbeeld. 

        public static class WebApiConfig
        {
            public static void Register(HttpConfiguration config)
            {
                // Web API configuration and services
                
                // The following line enables you to control CORS by using Web API code
                config.EnableCors();
    
                // Web API routes
                config.MapHttpAttributeRoutes();
    
                config.Routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { id = RouteParameter.Optional }
                );
            }
        }

1. Voeg in uw Web API-controller een `using`-instructie voor de `System.Web.Http.Cors`-naamruimte toe en voeg het `EnableCors`-kenmerk toe aan de controllerklasse of aan afzonderlijke actiemethoden. In het volgende voorbeeld geldt CORS-ondersteuning voor de gehele controller.

        namespace ToDoListAPI.Controllers 
        {
            [HttpOperationExceptionFilterAttribute]
            [EnableCors(origins:"https://todolistangular0121.azurewebsites.net", headers:"accept,content-type,origin,x-my-header", methods: "get,post")]
            public class ToDoListController : ApiController
 
## Azure API Management gebruiken met API-apps

Als u Azure API Management met een API-app gebruikt, configureert u CORS in API Management in plaats van in de API-app. Zie de volgende bronnen voor meer informatie:

* [Overzicht van Azure API Management (video: CORS begint bij 12:10)](https://azure.microsoft.com/documentation/videos/azure-api-management-overview/)
* [API Management-beleid voor meerdere domeinen](https://msdn.microsoft.com/library/azure/dn894084.aspx#CORS)
 
## Problemen oplossen

Als u een probleem ervaart tijdens het doorlopen van deze zelfstudie, vindt hier u enkele ideeën voor probleemoplossing.

* Zorg ervoor dat u de nieuwste versie van de [Azure SDK voor .NET voor Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003) gebruikt.

* Zorg ervoor dat u `https` hebt ingevoerd in de CORS-instelling en dat u `https` gebruikt om de front-end web-app uit te voeren.

* Zorg ervoor dat u de CORS-instelling hebt ingevoerd in de API voor de middelste laag en niet in de front-end web-app.

* Als u CORS zowel in de toepassingscode als in Azure App Service configureert, houd er dan rekening mee dat de App Service CORS-instelling overschrijft wat u in de toepassingscode doet. 

Zie [Problemen met Azure App Service-apps in Visual Studio oplossen](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md) voor meer informatie over de functies van Visual Studio die probleemoplossing vereenvoudigen.

## Volgende stappen 

In dit artikel hebt u gezien hoe u App Service CORS-ondersteuning kunt inschakelen, zodat client-JavaScript-code een API kan aanroepen in een ander domein. Lees voor meer informatie over API-apps de [Kennismaking met verificatie in App Service](../app-service/app-service-authentication-overview.md) en ga vervolgens naar de zelfstudie [Gebruikersverificatie voor API-apps](app-service-api-dotnet-user-principal-auth.md).



<!--HONumber=Jun16_HO2-->


