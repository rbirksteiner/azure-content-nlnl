<properties
    pageTitle="De Application Insights-SDK toevoegen om uw Node.js-app te bewaken | Microsoft Azure"
    description="Analyseer het gebruik, de beschikbaarheid en de prestaties van uw on-premises webtoepassing of Microsoft Azure-webtoepassing met Application Insights."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/25/2016"
    ms.author="awills"/>


# De Application Insights-SDK toevoegen om uw Node.js-app te bewaken

*Application Insights verkeert momenteel in de preview-fase.*

Met [Visual Studio Application Insights](app-insights-overview.md) wordt uw live-toepassing bewaakt om u te helpen bij het [detecteren en onderzoeken van prestatieproblemen en -uitzonderingen](app-insights-detect-triage-diagnose.md) en om te [ontdekken hoe uw app wordt gebruikt](app-insights-overview-usage.md). De tool werkt voor apps die worden gehost op uw eigen on-premises IIS-servers, op virtuele Azure-machines en in Azure-webtoepassingen.



Met de SDK kunt u automatisch de snelheid en reacties van binnenkomende HTTP-aanvragen verzamelen, net als de prestatiemeteritems (CPU, geheugen, RPS) en onverwerkte uitzonderingen. Bovendien kunt u aangepaste aanroepen toevoegen om de afhankelijkheden, meetgegevens en andere gebeurtenissen bij te houden.

![Voorbeeld van grafieken met prestatiebewaking](./media/app-insights-asp-net-manual/10-perf.png)


#### Voordat u begint

U hebt de volgende zaken nodig:

* Visual Studio 2013 of later. Later is beter.
* Een abonnement op [Microsoft Azure](http://azure.com). Als uw team of organisatie een Azure-abonnement heeft, kan de eigenaar u toevoegen met behulp van uw [Microsoft-account](http://live.com).

## <a name="add"></a>Een Application Insights-resource maken

Meld u aan bij de [Azure Portal][portal] en maak een nieuwe Application Insights-resource. Een [resource][rolls] in Azure is een exemplaar van een service. In deze resource wordt de telemetrie van uw app geanalyseerd en aan u gepresenteerd.

![Klik op Nieuw > Application Insights](./media/app-insights-asp-net-manual/01-new-asp.png)

Kies Overige als het toepassingstype. Op basis van het gekozen toepassingstype wordt de standaardinhoud van de resourceblades bepaald, net als de eigenschappen die zichtbaar zijn in [Metrics Explorer][metrics].

#### De instrumentatiesleutel kopiëren

De sleutel geeft aan wat de resource is. U installeert de sleutel in het begin in de SDK om gegevens om te leiden naar de resource.

![Op Eigenschappen klikken, de sleutel selecteren en op Ctrl + C drukken](./media/app-insights-asp-net-manual/02-props-asp.png)


## <a name="sdk"></a> De SDK installeren in uw toepassing

```
npm install applicationinsights
```

## Gebruik

Hiermee schakelt u de bewaking van aanvragen, het bijhouden van onverwerkte uitzonderingen en de systeemprestatiebewaking (CPU/geheugen/RPS) in.

```javascript

import appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>").start();
```

De instrumentatiesleutel kan ook worden ingesteld in de omgevingsvariabele APPINSIGHTS_INSTRUMENTATIONKEY. Als u dit doet, is er geen argument vereist bij het aanroepen van `appInsights.setup()` of `appInsights.getClient()`.

U kunt de SDK uitproberen zonder telemetrie te verzenden. Stel de instrumentatiesleutel hiertoe in op een niet-lege tekenreeks.


## <a name="run"></a> Uw project uitvoeren

Start uw toepassing en probeer deze uit. Open verschillende pagina’s om telemetrie te genereren.


## <a name="monitor"></a> Uw telemetrie weergeven

Ga naar de [Azure Portal](https://portal.azure.com) en blader naar uw Application Insights-resource.


Zoek naar gegevens op de pagina Overzicht. Aanvankelijk ziet u slechts één of twee punten. Bijvoorbeeld:

![Klik verder voor meer gegevens](./media/app-insights-asp-net-manual/12-first-perf.png)

Klik in een grafiek voor gedetailleerdere metrische gegevens. [Meer informatie over metrische gegevens.][perf]

#### Zijn er geen gegevens?

* Gebruik de toepassing om verschillende pagina's te openen, zodat er telemetrie wordt gegenereerd.
* Open de tegel [Zoeken](app-insights-diagnostic-search.md) om afzonderlijke gebeurtenissen te bekijken. Soms kan het even duren voordat de metrische gegevens van gebeurtenissen zijn opgehaald.
* Wacht een paar seconden en klik op **Vernieuwen**. De grafieken worden regelmatig vernieuwd, maar u kunt ze ook handmatig vernieuwen als u op bepaalde gegevens wacht.
* Zie [Probleemoplossing][qna].

## Uw app publiceren

Implementeer nu uw toepassing naar IIS of naar Azure en bekijk hoe de gegevens worden verzameld.


#### Ziet u geen gegevens nadat u uw app naar uw server hebt gepubliceerd?

Open deze poorten voor uitgaand verkeer in de firewall van uw server:

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


#### Problemen op uw buildserver?

Raadpleeg dit artikel voor [Probleemoplossing](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).



## Aangepast gebruik 

### Automatisch verzamelen uitschakelen

```javascript
import appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    // no telemetry will be sent until .start() is called
    .start();
```

### Aangepaste bewaking

```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

client.trackEvent("custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");
```

[Meer informatie over de telemetrie-API](app-insights-api-custom-events-metrics.md).

### Meerdere instrumentatiesleutels gebruiken

```javascript
import appInsights = require("applicationinsights");

// configure auto-collection with one instrumentation key
appInsights.setup("<instrumentation_key>").start();

// get a client for another instrumentation key
var otherClient = appInsights.getClient("<other_instrumentation_key>");
otherClient.trackEvent("custom event");
```

## Voorbeelden

### Afhankelijkheid bijhouden

```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

var startTime = Date.now();
// execute dependency call
var endTime = Date.now();

var elapsedTime = endTime - startTime;
var success = true;
client.trackDependency("dependency name", "command name", elapsedTime, success);
```



### Handmatig alle GET-aanvragen bijhouden

```javascript
var http = require("http");
var appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false) // disable auto-collection of requests for this example
    .start();

// assign common properties to all telemetry sent from the default client
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};

// track a system startup event
appInsights.client.trackEvent("server start");

// create server
var port = process.env.port || 1337
var server = http.createServer(function (req, res) {
    // track all "GET" requests
    if(req.method === "GET") {
        appInsights.client.trackRequest(req, res);
    }

    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello World\n");
}).listen(port);

// track startup time of the server as a custom metric
var start = +new Date;
server.on("listening", () => {
    var end = +new Date;
    var duration = end - start;
    appInsights.client.trackMetric("StartupTime", duration);
});
```




<!--Link references-->

[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[rolls]: app-insights-resources-roles-access-control.md



<!--HONumber=Jun16_HO2-->


