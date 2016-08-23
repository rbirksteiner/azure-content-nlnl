<properties
    pageTitle="Application Insights voor JavaScript-web-apps | Microsoft Azure"
    description="Verzamel tellingen van het aantal paginaweergaven en sessies, webclientgegevens en gebruikspatronen. Detecteer uitzonderingen en prestatieproblemen in JavaScript-webpagina's."
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
    ms.date="05/18/2016"
    ms.author="awills"/>

# Application Insights voor webpagina’s


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Krijg inzicht in de prestaties en het gebruik van uw webpagina's of app. Wanneer u Visual Studio Application Insights toevoegt aan uw paginascript, krijgt u de beschikking over allerlei gegevens, zoals de tijden voor het laden van pagina’s en AJAX-aanroepen, tellingen en details van browseruitzonderingen en AJAX-fouten, evenals de aantallen gebruikers en sessies. Al deze gegevens kunnen worden gesegmenteerd op pagina, clientbesturingssysteem en browserversie, geografische locatie en andere dimensies. Ook kunt u waarschuwingen instellen voor foutaantallen of het langzaam laden van de pagina.

U kunt Application Insights voor elke webpagina gebruiken. Het enige wat u hiervoor hoeft te doen, is een klein stukje JavaScript toevoegen. Als de webservice [Java](app-insights-java-get-started.md) of [ASP.NET](app-insights-asp-net.md) is, kunt u telemetrie van uw server en clients integreren.

U hebt een abonnement op [Microsoft Azure](https://azure.com) nodig. Als uw team een organisatie-abonnement heeft, vraagt u de eigenaar om uw Microsoft-account hieraan toe te voegen. Omdat er een gratis prijscategorie is, kosten ontwikkeling en kleinschalig gebruik u niets.


## Application Insights instellen voor uw webpagina

Mogelijk hebt u dit al gedaan. Als uw app een nieuw ASP.NET-project is en u ervoor hebt gekozen Application Insights toe te voegen in het dialoogvenster New Project in Visual Studio, is het script al toegevoegd en bent u klaar.

Anders gaat u als volgt te werk om een codefragment aan uw webpagina's toe te voegen.

### Een Application Insights-resource openen

In de Application Insights-resource worden gegevens over de prestaties en het gebruik van uw pagina weergegeven. 

Meld u aan bij de [Azure Portal](https://portal.azure.com).

Als u bewaking voor de serverkant van uw app hebt ingesteld, hebt u al een resource:

![Kies Bladeren, Ontwikkelaarsservices, Application Insights.](./media/app-insights-javascript/01-find.png)

Als u nog geen resource hebt, maakt u er een:

![Kies Nieuw, Ontwikkelaarsservices, Application Insights.](./media/app-insights-javascript/01-create.png)


*Heb u op dit moment vragen?* [Meer over het maken van een resource](app-insights-create-new-resource.md).


### Het SDK-script toevoegen aan uw app of webpagina's

Haal het script voor webpagina's op in Snel starten:

![Kies op de overzichtsblade van uw app de optie Snel starten, Code ophalen voor het bewaken van mijn webpagina’s. Kopieer het script.](./media/app-insights-javascript/02-monitor-web-page.png)

Voeg het script in vlak vóór de `<head>`-tag op elke pagina die u wilt volgen. Als uw website een basispagina heeft, kunt u daar het script plaatsen. Bijvoorbeeld:

* In een ASP.NET-MVC-project plaatst u het in `View\Shared\_Layout.cshtml`
* Op een SharePoint-site opent u in het Configuratiescherm [Site-instellingen / basispagina](app-insights-sharepoint.md).

Het script bevat de instrumentatiesleutel die de gegevens naar uw Application Insights-resource stuurt. 

([Nadere uitleg over het script.](http://apmtips.com/blog/2015/03/18/javascript-snippet-explained/))

*(Als u een bekend webpaginaframework gebruikt, is het een goed idee om op zoek te gaan naar een geschikte Application Insights-adapter. Er is bijvoorbeeld [een AngularJS-module](http://ngmodules.org/modules/angular-appinsights).)*


## Gedetailleerde configuratie

U kunt diverse [parameters](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) instellen. In de meeste gevallen is dat echter niet nodig. U kunt bijvoorbeeld het aantal gemelde AJAX-aanroepen per paginaweergave uitschakelen of beperken (om verkeer te beperken), of u kunt de foutopsporingsmodus zo instellen dat telemetrie sneller wordt verzameld door deze niet in een batch op te nemen.

Voor het instellen van deze parameters zoekt u deze regel in het codefragment en voegt u hierna meer items toe, gescheiden door komma's:

    })({
      instrumentationKey: "..."
      // Insert here
    });

De [beschikbare parameters](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) zijn:

    // Send telemetry immediately without batching.
    // Remember to remove this when no longer required, as it
    // can affect browser performance.
    enableDebug: true,

    // Don't log browser exceptions.
    disableExceptionTracking: true,

    // Don't log ajax calls.
    disableAjaxTracking: boolean,

    // Limit number of Ajax calls logged, to reduce traffic.
    maxAjaxCallsPerView: 10, // default is 500

    // Time page load up to execution of first trackPageView().
    overridePageViewDuration: boolean,

    // Set these dynamically for an authenticated user.
    appUserId: string,
    accountId: string,



## <a name="run"></a>Uw app uitvoeren

Voer uw web-app uit, gebruik deze een tijdje om telemetrie te genereren en wacht een paar seconden. U kunt de app zelf op uw ontwikkelcomputer uitvoeren met behulp van de **F5**-toets, maar u kunt de app ook publiceren en door gebruikers laten uitproberen.

Als u de telemetrie wilt controleren die door een web-app naar Application Insights wordt verzonden, gebruikt u de foutopsporingsprogramma's van de browser (in veel browsers opent u deze met **F12**). Gegevens worden verzonden naar dc.services.visualstudio.com.

## Gegevens over uw browserprestaties verkennen

Open de blade Browsers om cumulatieve prestatiegegevens weer te geven van de browsers van uw gebruikers.

![Open de resource van uw app in portal.azure.com en klik op Instellingen, Browser](./media/app-insights-javascript/03.png)


*Zijn er nog geen gegevens? Klik boven aan de pagina op **Vernieuwen**. Ziet u nog steeds niets? Raadpleeg [Probleemoplossing](app-insights-troubleshoot-faq.md).*

De blade Browsers is een [Metrics Explorer-blade](app-insights-metrics-explorer.md) met vooraf ingestelde filters en grafiekselecties. U kunt het tijdbereik, de filters en configuratie van de grafiek bewerken, en het resultaat als favoriet opslaan. Klik op **Standaardwaarden herstellen** om de oorspronkelijke bladeconfiguratie terug te zetten.

## Laadprestaties van de pagina

Bovenaan staat een grafieksegment met laadtijden van pagina’s. De totale hoogte van de grafiek geeft de gemiddelde tijd aan die nodig is voor het laden en weergeven van pagina's van uw app in de browsers van uw gebruikers. De tijd wordt gemeten vanaf het moment dat de browser de eerste HTTP-aanvraag verzendt tot het moment dat alle synchrone gebeurtenissen tijdens het laden zijn verwerkt, met inbegrip van indeling en uitgevoerde scripts. Asynchrone taken, zoals het laden van de webonderdelen van AJAX-aanroepen, zijn niet opgenomen.

De grafiek segmenteert de totale laadtijd van de pagina in de [standaardtijdsinstellingen gedefinieerd door W3C](http://www.w3.org/TR/navigation-timing/#processing-model). 

![](./media/app-insights-javascript/08-client-split.png)

De *netwerkverbindingstijd* is vaak lager dan u verwacht, omdat het een gemiddelde betreft op basis van alle aanvragen van de browser aan de server. Veel afzonderlijke aanvragen hebben een verbindingstijd van 0, omdat er al een actieve verbinding met de server bestaat.

### Worden pagina’s traag geladen?

Het traag laden van pagina is voor uw gebruikers een belangrijke bron van ergernis. Als de grafiek aangeeft dat pagina’s traag worden geladen, is het eenvoudig om wat diagnostisch onderzoek uit te voeren.

De grafiek toont de gemiddelde paginalaadtijd in uw app. Als u wilt weten of het probleem zich beperkt tot bepaalde pagina's, kijkt u een stukje lager op de blade. Daar ziet u een raster gesegmenteerd op pagina-URL:

![](./media/app-insights-javascript/09-page-perf.png)

Let op het aantal paginaweergaven en de standaarddeviatie. Als het aantal pagina's zeer laag is, hebben gebruikers er niet veel last van. Een hoge standaarddeviatie (vergelijkbaar met het gemiddelde) geeft aan dat er grote verschillen bestaan tussen de afzonderlijke metingen.

**Zoom in op een URL en een paginaweergave.** Klik op een paginanaam om een blade weer te geven met browsergrafieken die zijn gefilterd op die ene URL. Klik vervolgens op een exemplaar van een paginaweergave.

![](./media/app-insights-javascript/35.png)

Klik op `...` voor een volledige lijst met eigenschappen voor de betreffende gebeurtenis of controleer de AJAX-aanroepen en gerelateerde gebeurtenissen. Trage AJAX-aanroepen zijn van invloed op de algemene laadtijd van pagina’s als beide synchroon gebeuren. Gerelateerde gebeurtenissen omvatten serveraanvragen voor dezelfde URL (als u op uw webserver Application Insights hebt ingesteld).


**Paginaprestaties over langere tijd.** Wijzig op de blade Browsers het raster met paginalaadtijden in een lijndiagram om na te gaan of er op bepaalde tijden pieken waren:

![Op de kop van het raster klikken en een nieuw grafiektype selecteren](./media/app-insights-javascript/10-page-perf-area.png)

**Segmenteer op andere dimensies.** Mogelijk laden uw pagina's trager in een bepaalde browser, in een bepaald clientbesturingssysteem of op bepaalde gebruikerslocaties. Voeg een nieuwe grafiek toe en experimenteer met de dimensie **Groeperen op**.

![](./media/app-insights-javascript/21.png)


## AJAX-prestaties

Zorg ervoor dat eventuele AJAX-aanroepen op uw webpagina's goed worden uitgevoerd. Ze worden vaak gebruikt voor het asynchroon vullen van onderdelen van uw pagina. Hoewel de algemene pagina waarschijnlijk onmiddellijk laadt, kunnen uw gebruikers zich gaan ergeren doordat ze steeds moeten wachten op ladende paginaonderdelen.

AJAX-oproepen vanuit uw webpagina worden op de blade Browsers weergegeven als afhankelijkheden.

In het bovenste gedeelte van de blade ziet u samenvattingsgrafieken:

![](./media/app-insights-javascript/31.png)

en verder naar beneden gedetailleerde rasters:

![](./media/app-insights-javascript/33.png)

Klik op een rij voor specifieke informatie.


> [AZURE.NOTE] Als u het filter Browsers op de blade verwijdert, worden zowel de server als de AJAX-afhankelijkheden opgenomen in deze grafieken. Klik op Standaardwaarden herstellen om het filter opnieuw te configureren.

**Als u wilt inzoomen op mislukte AJAX-aanroepen**, bladert u omlaag naar het raster met afhankelijkheidsfouten en klikt u vervolgens op een rij om specifieke exemplaren weer te geven.

![](./media/app-insights-javascript/37.png)

Klik op `...` voor de volledige telemetrie voor een AJAX-aanroep.

### Zijn er geen AJAX-aanroepen gemeld?

AJAX-aanroepen zijn HTTP-aanroepen vanuit het script van de webpagina. Als u constateert dat deze niet worden gerapporteerd, gaat u na of het codefragment niet de [parameter](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableAjaxTracking` of `maxAjaxCallsPerView` instelt.

## Browseruitzonderingen

Op de blade Browsers ziet u een grafiek met samenvattingen van uitzonderingen en lager op de blade een raster met typen uitzonderingen.

![](./media/app-insights-javascript/39.png)

Als u constateert dat er geen browseruitzonderingen worden gerapporteerd, gaat u na of het codefragment niet de [parameter](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableExceptionTracking` instelt.

## De afzonderlijke paginaweergavegebeurtenissen inspecteren

Meestal wordt telemetrie van paginaweergaven geanalyseerd door Application Insights en ziet u alleen cumulatieve rapporten, het gemiddelde van alle gebruikers. Maar voor foutopsporing kunt u ook zoeken op afzonderlijke paginaweergavegebeurtenissen.

Stel op de blade Diagnostische gegevens doorzoeken de optie Filters in op Paginaweergave.

![](./media/app-insights-javascript/12-search-pages.png)

Selecteer een gebeurtenis om deze gedetailleerder te bekijken. Klik op de pagina met details op '...' om nog meer details weer te geven.

> [AZURE.NOTE] Als u [Zoeken](app-insights-diagnostic-search.md) gebruikt, zorg er dan voor dat u zoekt op volledige woorden. 'About' vindt u bijvoorbeeld niet met 'Abou' of met 'bout', maar wel met 'Abou*'. U kunt een zoekterm niet laten beginnen met een jokerteken. Met '*bou' zoekt u tevergeefs naar 'About'.

> [Meer informatie over diagnostische gegevens doorzoeken](app-insights-diagnostic-search.md)

### Eigenschappen van paginaweergaven

* **Duur van paginaweergave** 

 * Standaard is dit de tijd voor het laden van de pagina, van clientverzoek tot het moment dat de pagina volledig is laden (inclusief hulpbestanden, maar uitgezonderd asynchrone taken zoals AJAX-aanroepen). 
 * Als u `overridePageViewDuration` instelt in de [paginaconfiguratie](#detailed-configuration), is de duur van de paginaweergave het interval tussen de clientaanvraag en het uitvoeren van de eerste `trackPageView`. Als u trackPageView na de initialisatie van het script hebt verplaatst vanaf de normale positie, wordt er een andere waarde weergegeven.
 * Als `overridePageViewDuration` is ingesteld en er een duurargument is opgegeven in de `trackPageView()`-aanroep, wordt in plaats daarvan de waarde van het argument gebruikt. 


## Aangepast telling van het aantal paginaweergaven

Standaard wordt er een paginaweergave geteld telkens wanneer er in de clientbrowser een nieuwe pagina wordt geladen.  Maar desgewenst kunt u ook aanvullende paginaweergaven tellen. Op een pagina kan bijvoorbeeld de inhoud worden weergegeven in tabbladen en u wilt een paginaweergave tellen wanneer de gebruiker naar een ander tabblad gaat. Of JavaScript-code op de pagina laadt nieuwe inhoud zonder de browser-URL te wijzigen.

Ga als volgt te werk om op het betreffende punt in uw clientcode een JavaScript-aanroep in te voegen:

    appInsights.trackPageView(myPageName);

De naam van de pagina mag dezelfde tekens als een URL bevatten, maar alles na '#' of '?' wordt genegeerd.



## Gebruik bijhouden


Wilt u weten wat gebruikers met uw app doen?

* [Meer informatie over het bijhouden van gebruik](app-insights-web-track-usage.md)
* [Meer informatie over de API voor aangepaste gebeurtenissen en metrische gegevens](app-insights-api-custom-events-metrics.md).


#### <a name="video"></a> Video: Bijhouden van gebruik

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> Volgende stappen

* [Bijhouden van gebruik](app-insights-web-track-usage.md)
* [Aangepaste gebeurtenissen en metrische gegevens](app-insights-api-custom-events-metrics.md)
* [Bouwen-meten-leren](app-insights-overview-usage.md)





<!--HONumber=Jun16_HO2-->


