<properties
    pageTitle="De Application Insights-SDK toevoegen voor het bewaken van uw ASP.NET-app | Microsoft Azure"
    description="Analyseer het gebruik, de beschikbaarheid en de prestaties van uw on-premises webtoepassing of Microsoft Azure-webtoepassing met Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/19/2016"
    ms.author="awills"/>


# De Application Insights-SDK toevoegen voor het bewaken van uw ASP.NET-app

*Application Insights verkeert momenteel in de preview-fase.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


Met Visual Studio Application Insights wordt uw live-toepassing bewaakt om u te helpen bij het [detecteren en onderzoeken van prestatieproblemen en -uitzonderingen][detect] en om te [ontdekken hoe uw app wordt gebruikt][knowUsers]. Application Insights kan met een groot aantal verschillende toepassingstypen worden gebruikt. De tool werkt voor apps die worden gehost op uw eigen on-premises IIS-servers, op virtuele Azure-machines en in Azure-webtoepassingen.



![Voorbeeld van grafieken met prestatiebewaking](./media/app-insights-asp-net-manual/10-perf.png)

*Zie ook:*

* [ASP.NET 5](app-insights-asp-net-five.md)
* [Apparaat-apps en Java-servers][platforms]

#### Voordat u begint

Voor veel toepassingstypen kan [Visual Studio Application Insights toevoegen aan uw app](#ide), bijna zonder dat u daar iets van merkt. Omdat u dit echter leest om meer inzicht te krijgen in wat er gebeurt, worden de stappen handmatig doorlopen.


U hebt de volgende zaken nodig:

* Een abonnement op [Microsoft Azure](http://azure.com). Als uw team of organisatie een Azure-abonnement heeft, kan de eigenaar u toevoegen met behulp van uw [Microsoft-account](http://live.com).
* Visual Studio 2013 of later.

## <a name="add"></a>Een Application Insights-resource maken

Meld u aan bij de [Azure Portal][portal] en maak een nieuwe Application Insights-resource. Kies ASP.NET als het toepassingstype.

![Klik op Nieuw > Application Insights](./media/app-insights-asp-net-manual/01-new-asp.png)

Een [resource][rolls] in Azure is een exemplaar van een service. In deze resource wordt de telemetrie van uw app geanalyseerd en aan u gepresenteerd.

Op basis van het gekozen toepassingstype wordt de standaardinhoud van de resourceblades bepaald, net als de eigenschappen die zichtbaar zijn in [Metrics Explorer][metrics].

#### De instrumentatiesleutel kopiëren

De sleutel geeft aan wat de resource is. U installeert de sleutel in het begin in de SDK om gegevens om te leiden naar de resource.

![Op Eigenschappen klikken, de sleutel selecteren en op Ctrl + C drukken](./media/app-insights-asp-net-manual/02-props-asp.png)

De stappen die u zojuist hebt gevolgd om een nieuwe resource te maken, vormen ook een goed startpunt om te beginnen met het bewaken van een toepassing. U kunt er nu gegevens naar verzenden.

## <a name="sdk"></a> De SDK installeren in uw toepassing

De installatie en configuratie van de Application Insights-SDK varieert, afhankelijk van het platform waarmee u werkt. Voor ASP.NET-apps is het eenvoudig.

1. Bewerk in Visual Studio de NuGet-pakketten van uw web-app-project.

    ![Klik met de rechtermuisknop op het project en selecteer NuGet-pakketten beheren](./media/app-insights-asp-net-manual/03-nuget.png)

2. Installeer de Application Insights-SDK voor web-apps.

    ![Naar Application Insights zoeken](./media/app-insights-asp-net-manual/04-ai-nuget.png)

3. Bewerk ApplicationInsights.config (toegevoegd tijdens de NuGet-installatie). Voeg dit vlak vóór de afsluitcode in:

    `<InstrumentationKey>` *de instrumentatiesleutel die u hebt gekopieerd* `</InstrumentationKey>`

    (U kunt ook [de sleutel instellen door code te schrijven][apikey] in uw app.)

#### Upgraden naar toekomstige SDK-versies

Van tijd tot tijd brengen we een nieuwe versie van de SDK uit.

Als u wilt upgraden naar een [nieuwe release van de SDK](app-insights-release-notes-dotnet.md), opent u NuGet-pakketbeheer opnieuw en filtert u op geïnstalleerde pakketten. Selecteer **Microsoft.ApplicationInsights.Web** en kies **Upgraden**.

Als u aanpassingen in ApplicationInsights.config hebt aangebracht, slaat u hiervan een kopie op voordat u de upgrade uitvoert. Voeg vervolgens uw wijzigingen samen in de nieuwe versie.


## <a name="run"></a> Uw project uitvoeren

Gebruik **F5** om uw toepassing te starten en uit te proberen. Open verschillende pagina’s om telemetrie te genereren.

In Visual Studio wordt bijgehouden hoeveel gebeurtenissen er zijn verzonden.

![](./media/app-insights-asp-net-manual/appinsights-09eventcount.png)

## <a name="monitor"></a> Uw telemetrie weergeven

Ga terug naar de [Azure Portal][portal] en blader naar de Application Insights-resource.


Zoek naar gegevens in de overzichtsgrafieken. Aanvankelijk ziet u slechts één of twee punten. Bijvoorbeeld:

![Klik verder voor meer gegevens](./media/app-insights-asp-net-manual/12-first-perf.png)

Klik in een grafiek voor gedetailleerdere metrische gegevens. [Meer informatie over metrische gegevens.][perf]

#### Zijn er geen gegevens?

* Gebruik de toepassing om verschillende pagina's te openen, zodat er telemetrie wordt gegenereerd.
* Open de tegel [Zoeken][diagnostics] om afzonderlijke gebeurtenissen te bekijken. Soms kan het even duren voordat de metrische gegevens van gebeurtenissen zijn opgehaald.
* Wacht een paar seconden en klik op **Vernieuwen**. De grafieken worden regelmatig vernieuwd, maar u kunt ze ook handmatig vernieuwen als u op bepaalde gegevens wacht.
* Zie [Probleemoplossing][qna].

## Uw app publiceren

Implementeer nu uw toepassing naar IIS of naar Azure en bekijk hoe de gegevens worden verzameld.

![Visual Studio gebruiken om uw app te publiceren](./media/app-insights-asp-net-manual/15-publish.png)

Wanneer u de foutopsporingsmodus gebruikt, wordt de telemetrie direct doorgegeven, zodat u binnen enkele seconden gegevens ziet verschijnen. Wanneer u uw app in de Release-configuratie implementeert, duurt het langer om gegevens te verzamelen.

#### Ziet u geen gegevens nadat u uw app naar uw server hebt gepubliceerd?

Open deze poorten voor uitgaand verkeer in de firewall van uw server:

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


#### Problemen op uw buildserver?

Raadpleeg dit artikel voor [Probleemoplossing](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [AZURE.NOTE] Als uw app veel telemetrie genereert (en u de ASP.NET-SDK-versie 2.0.0-beta3 of later gebruikt), beperkt de adaptieve steekproefmodule automatisch het volume dat naar de portal wordt verzonden door alleen een representatieve fractie van de gebeurtenissen te sturen. Gebeurtenissen die betrekking hebben op dezelfde aanvraag, worden echter als groep geselecteerd of gedeselecteerd, zodat u tussen gerelateerde gebeurtenissen kunt navigeren. 
> [Meer informatie over steekproeven](app-insights-sampling.md).


## Bijhouden van afhankelijkheid (en IIS-prestatiemeteritems) toevoegen

De SDK heeft hulp nodig om toegang te krijgen tot bepaalde gegevens. U hebt deze aanvullende stap met name nodig om aanroepen vanuit uw app naar databases, REST-API's en andere externe componenten automatisch te meten. Deze metrische afhankelijkheidsgegevens kunnen waardevol zijn bij het vaststellen van prestatieproblemen.

Als u een eigen IIS-server hebt, worden met deze stap ook prestatiemeteritems van het systeem weergegeven in [Metrics Explorer](app-insights-metrics-explorer.md).

#### Als uw app wordt uitgevoerd op uw IIS-server

Meld u met beheerdersrechten bij de server aan en installeer [Application Insights Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).

Mogelijk moet u [extra uitgaande poorten openen in uw firewall](app-insights-monitor-performance-live-website-now.md#troubleshooting).

Met deze stap wordt ook [rapportage van prestatiemeteritems](app-insights-web-monitor-performance.md#system-performance-counters) (zoals CPU, geheugen en netwerkbezetting) ingeschakeld.

#### Als uw app een Azure-web-app is

Voeg in het bedieningspaneel van uw Azure-web-app de extensie Application Insights toe.

![In uw web-app: Settings, Extensions, Add, Application Insights](./media/app-insights-asp-net-manual/05-extend.png)


#### Als het een Azure-cloudserviceproject betreft

[Voeg scripts toe aan web- en werkrollen](app-insights-cloudservices.md).



## Bewaking aan clientzijde toevoegen

U hebt de SDK geïnstalleerd waarmee telemetrische gegevens worden verzonden vanaf de server (back-end) van uw toepassing. U kunt nu bewaking aan clientzijde toevoegen. U beschikt dan over gegevens over gebruikers, sessies, paginaweergaven en eventuele uitzonderingen of crashes die in de browser optreden. Ook kunt u uw eigen code schrijven om bij te houden hoe uw gebruikers met uw app werken, tot op het niveau van muisklikken en toetsaanslagen.


Voeg aan elke pagina een JavaScript-fragment toe. Haal de code op uit uw Application Insights-resource:

![Open in uw web-app Snel starten en klik op Code ophalen voor het bewaken van mijn webpagina's](./media/app-insights-asp-net-manual/02-monitor-web-page.png)

U kunt zien dat de code de instrumentatiesleutel bevat die uw toepassingsresource identificeert.

[Meer informatie over het bijhouden van webpagina's.](app-insights-web-track-usage.md)


## Toepassingsversie bijhouden

Zorg ervoor dat `buildinfo.config` door het MSBuild-proces wordt gegenereerd. Voeg het volgende toe in uw .csproj-bestand:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Wanneer de buildgegevens beschikbaar zijn, voegt de Application Insights-webmodule automatisch **Toepassingsversie** als eigenschap toe aan elk telemetrie-item. Hierdoor kunt u filteren op versie bij het uitvoeren van [diagnostische zoekopdrachten][diagnostics] of bij het [verkennen van metrische gegevens][metrics]. 

Let er echter op dat het buildversienummer alleen wordt gegenereerd door MS Build en niet door de ontwikkelaarsbuild in Visual Studio.

## De installatie voltooien

Als u een volledig beeld wilt van uw toepassing, zijn er enkele aanvullende zaken die u kunt doen:

* [Webtests instellen][availability] om te controleren of de toepassing live en responsief blijft.
* [Logboektraceringen vastleggen][netlogs] van uw favoriete framework voor logboekregistratie
* [Aangepaste gebeurtenissen en metrische gegevens bijhouden][api] op de client, op de server of op beide, voor meer informatie over hoe uw toepassing wordt gebruikt.

## <a name="ide"></a> Geautomatiseerd

Aan het begin van dit artikel hebben we aangegeven dat u zou leren hoe u handmatig een Application Insights-resource maakt en hoe u daarna de SDK installeert. Het is handig om een goed inzicht te hebben in beide onderdelen van die procedure. Voor ASP.NET-apps (en vele andere typen apps) bestaat er echter een nog snellere, automatische manier.

Hiervoor hebt u [Visual Studio](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (2013, update 3 of hoger) en een account voor [Microsoft Azure](http://azure.com) nodig.

#### Als het een nieuw project is...

Wanneer u in Visual Studio een nieuw project maakt, controleert u of **Application Insights toevoegen** is geselecteerd.


![Een ASP.NET-project maken](./media/app-insights-asp-net-manual/appinsights-01-vsnewp1.png)

Visual Studio maakt een resource in Application Insights, voegt de SDK toe aan uw project en plaatst vervolgens de sleutel in het `.config`-bestand.

Als uw project webpagina's bevat, wordt ook de [JavaScript SDK][-client] aan de hoofdwebpagina toegevoegd.

#### ...of als het een bestaand project is

Klik in Solution Explorer met de rechtermuisknop op het project. Klik vervolgens op **Add Application Insights**.

![Add Application Insights kiezen](./media/app-insights-asp-net-manual/appinsights-03-addExisting.png)

Visual Studio maakt een resource in Application Insights, voegt de SDK toe aan uw project en plaatst vervolgens de sleutel in het `.config`-bestand.

In dit geval wordt de [JavaScript SDK][-client] niet toegevoegd aan uw webpagina’s. Het wordt aanbevolen om dat als volgende stap zelf te doen.

#### Instelopties

Als u dit voor het eerst doet, moet u zich aanmelden bij of registreren voor Microsoft Azure Preview. 

Als deze app deel uitmaakt van een grotere toepassing, is het mogelijk handig om **Instellingen configureren** te gebruiken om de app in dezelfde resourcegroep te plaatsen als de andere onderdelen.

*Ziet u de optie Application Insights niet? Controleer of u Visual Studio 2013 Update 3 of hoger gebruikt en of de Application Insights-hulpprogramma’s zijn ingeschakeld in Extensies en updates.*

#### Application Insights openen vanuit uw project

![Met de rechtermuisknop op het project klikken en de Azure Portal openen](./media/app-insights-asp-net-manual/appinsights-04-openPortal.png)




## <a name="video"></a>Video

> [AZURE.VIDEO getting-started-with-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[-client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostics]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[rolls]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md



<!--HONumber=Jun16_HO2-->


