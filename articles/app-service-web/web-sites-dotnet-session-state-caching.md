<properties 
    pageTitle="Sessiestatus met Azure Redis-cache in Azure App Service" 
    description="Informatie over het gebruik van de Azure Cache Service voor de ondersteuning van ASP.NET-sessiestatus-caching." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="Rick-Anderson" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="get-started-article" 
    ms.date="02/27/2016" 
    ms.author="riande"/>


# Sessiestatus met Azure Redis-cache in Azure App Service


In dit onderwerp wordt uitgelegd hoe u de Azure Redis Cache Service gebruikt voor sessiestatus.

Als uw ASP.NET-web-app sessiestatus gebruikt, moet u een externe sessiestatus-provider configureren (de Redis Cache Service of een sessiestatus-provider voor SQL Server). Als u sessiestatus gebruikt maar geen externe provider, wordt uw gebruik van de web-app beperkt tot één exemplaar. De Redis Cache Service is het snelst en eenvoudigst om in te schakelen.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

##<a id="createcache"></a>De cache maken
Volg [deze instructies](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) om de cache te maken.

##<a id="configureproject"></a>Het NuGet-pakket van RedisSessionStateProvider toevoegen aan uw web-app
Installeer het NuGet-pakket van `RedisSessionStateProvider`.  Gebruik de volgende opdracht om te installeren vanuit de Package Manager Console (**Tools** > **NuGet Package Manager** > **Package Manager Console**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Om te installeren vanuit **Tools** > **NuGet Package Manager** > **NuGet-pakketten beheren voor oplossing**, zoekt u naar `RedisSessionStateProvider`.

Zie voor meer informatie de [NuGet RedisSessionStateProvider-pagina](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) en [De cacheclient configureren](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

##<a id="configurewebconfig"></a>Het bestand Web.Config wijzigen
Naast het maken van assembly-verwijzingen voor Cache, voegt het NuGet-pakket stub-vermeldingen toe aan het *web.config*-bestand. 

1. Open de *web.config* en zoek het element **sessionState**.

1. Voer de waarden in voor `host`, `accessKey`, `port` (de SSL-poort moet 6380 zijn), en stel `SSL` in op `true`. U vindt deze waarden op de blade [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715) voor uw cache-exemplaar. Zie voor meer informatie [Verbinding maken met de cache](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Houd er rekening mee dat de niet-SSL-poort standaard is uitgeschakeld voor nieuwe caches. Zie voor meer informatie over het inschakelen van de niet-SSL-poort het gedeelte [Toegangspoorten](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) in het onderwerp [Een cache configureren in Azure Redis-cache](https://msdn.microsoft.com/library/azure/dn793612.aspx). De volgende tekst toont de wijzigingen in het *web.config*-bestand, en specifiek de wijzigingen aan *poort*, *host*, accessKey* en *ssl*.

          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;


##<a id="usesessionobject"></a> Het sessieobject in code gebruiken
De laatste stap is te beginnen het sessieobject te gebruiken in uw ASP.NET-code. U voegt objecten toe aan sessiestatus met de **Session.Add**-methode. Deze methode maakt gebruik van sleutelwaardeparen voor het opslaan van items in de cache van de statussessie.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

De volgende code haalt deze waarde op uit de sessiestatus.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue; 

U kunt ook de Redis-cache gebruiken om cacheobjecten in uw web-app te cachen. Zie voor meer informatie [MVC film app met Azure Redis-Cache in 15 minuten](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Zie voor meer informatie over het gebruik van ASP.NET-sessiestatus [Overzicht van ASP.NET-sessiestatus][].

>[AZURE.NOTE] Als u aan de slag wilt met Azure App Service voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](http://go.microsoft.com/fwlink/?LinkId=523751). Hier kunt u direct een tijdelijke web-app maken in App Service. U hebt geen creditcard nodig en u gaat geen verplichtingen aan.

## Wat is er gewijzigd
* Als u van Websites wilt overstappen op App Service, raadpleegt u de volgende handleiding: [Azure App Service en de invloed ervan op bestaande Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)

  *Door [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [de meest recente geïnstalleerd]: http://www.windowsazure.com/downloads/?sdk=net  
  [Overzicht van ASP.NET-sessiestatus]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 



<!--HONumber=Jun16_HO2-->


