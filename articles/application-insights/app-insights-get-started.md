<properties
    pageTitle="Aan de slag met Visual Studio Application Insights | Microsoft Azure"
    description="Analyseer met Visual Studio Application Insights gebruik, beschikbaarheid en prestaties van uw on-premises webtoepassing of Microsoft Azure-webtoepassing."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="03/31/2016"
    ms.author="awills"/>

# Aan de slag met Visual Studio Application Insights

*Application Insights verkeert momenteel in de preview-fase.*

Detecteer problemen en los ze op, en breng voortdurend verbeteringen aan in uw toepassingen. Analyseer snel problemen in uw live-toepassing. Krijg inzicht in wat gebruikers met uw toepassing doen.

De configuratie is heel eenvoudig en u ziet binnen enkele minuten resultaten.

Momenteel bieden we ondersteuning voor iOS-, Android- en Windows-apps, J2EE- en ASP.NET-webtoepassingen en WCF-services. Web-apps kunnen worden uitgevoerd op Azure of op uw eigen on-premises servers. Onze JavaScript-SDK kan worden uitgevoerd op elke webpagina.

[Bekijk het introductiefilmpje](https://www.youtube.com/watch?v=fX2NtGrh-Y0).

## Aan de slag

Begin met een combinatie (in willekeurige volgorde) van de toegangspunten die in het volgende diagram worden weergegeven. Volg het pad dat aansluit bij uw behoeften.

Application Insights werkt door aan uw app een SDK toe te voegen die telemetrie verzendt naar de [Azure Portal](https://portal.azure.com). Er zijn verschillende SDK's voor de verschillende combinaties van ondersteunde platforms, talen en IDE’s.

U hebt een account in [Microsoft Azure](http://azure.com) nodig. Mogelijk hebt u al toegang tot een groepsaccount via uw organisatie. U kunt ook een account voor betalen per gebruik maken. Omdat Application Insights een gratis laag heeft, betaalt u pas wanneer uw app populair wordt. Bekijk de [pagina met prijzen](https://azure.microsoft.com/pricing/details/application-insights/).

Wat u wilt | Wat u moet doen | Wat u krijgt
---|---|---
 <a href="app-insights-asp-net.md">![ASP.NET](./media/app-insights-get-started/appinsights-gs-i-01-perf.png)</a> | <a href="app-insights-asp-net.md">Application Insights-SDK toevoegen aan uw webproject</a> <br/> ![hiermee krijgt u](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-asp-net.md">![Bewaking van prestaties en gebruik](./media/app-insights-get-started/appinsights-gs-r-01-perf.png)</a>
<a href="app-insights-monitor-performance-live-website-now.md">![ASP.NET-website al actief](./media/app-insights-get-started/appinsights-gs-i-04-red2.png)</a><br/><a href="app-insights-monitor-performance-live-website-now.md">![Bewaking van afhankelijkheid en prestaties](./media/app-insights-get-started/appinsights-gs-i-03-red.png)</a>|<a href="app-insights-monitor-performance-live-website-now.md">Status Monitor installeren op uw IIS-server</a> <br/> ![hiermee krijgt u](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-monitor-performance-live-website-now.md">![Bewaking van ASP.NET-afhankelijkheid](./media/app-insights-get-started/appinsights-gs-r-03-red.png)</a>
<a href="insights-perf-analytics.md">![Azure-web-app of virtuele machine](./media/app-insights-get-started/appinsights-gs-i-10-azure.png)</a>|<a href="insights-perf-analytics.md">Inzicht in uw Azure-web-app of virtuele machine mogelijk maken</a> <br/> ![hiermee krijgt u](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="insights-perf-analytics.md">![Bewaking van afhankelijkheid en prestaties](./media/app-insights-get-started/appinsights-gs-r-03-red.png)</a>
<a href="app-insights-java-get-started.md">![Java](./media/app-insights-get-started/appinsights-gs-i-11-java.png)</a>|<a href="app-insights-java-get-started.md">De SDK toevoegen aan uw Java-project</a><br/>![hiermee krijgt u](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-java-get-started.md">![Bewaking van prestaties en gebruik](./media/app-insights-get-started/appinsights-gs-r-10-java.png)</a>
<a href="app-insights-web-track-usage.md">![JavaScript](./media/app-insights-get-started/appinsights-gs-i-02-usage.png)</a>|<a href="app-insights-web-track-usage.md">Het Application Insights-script invoegen in uw webpagina's</a><br/>![hiermee krijgt u](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-web-track-usage.md">![browserprestaties en paginaweergaven](./media/app-insights-get-started/appinsights-gs-r-02-usage.png)</a>
<a href="app-insights-monitor-web-app-availability.md">![Beschikbaarheid](./media/app-insights-get-started/appinsights-gs-i-05-avail.png)</a>|<a href="app-insights-monitor-web-app-availability.md">Webtests maken</a><br/>![hiermee krijgt u](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-monitor-web-app-availability.md">![Beschikbaarheid](./media/app-insights-get-started/appinsights-gs-r-05-avail.png)</a>
<a href="app-insights-platforms.md">![iOS-, Android- en Windows-apparaten](./media/app-insights-get-started/appinsights-gs-i-07-device.png)</a>|<a href="http://hockeyapp.net">HockeyApp gebruiken</a><br/>![hiermee krijgt u](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="http://hockeyapp.net">![Gegevens over crashes en gebruik](./media/app-insights-get-started/appinsights-gs-r-06-device.png)</a>

## Ondersteuning en feedback


* Vragen en problemen:
 * [Probleemoplossing][qna]
 * [MSDN-forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
 * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* Fouten:
 * [Verbinding maken](https://connect.microsoft.com/VisualStudio/Feedback/LoadSubmitFeedbackForm?FormID=6076)
* Suggesties:
 * [UserVoice](https://visualstudio.uservoice.com/forums/357324)
* Codevoorbeelden
 * [Codevoorbeelden](app-insights-code-samples.md)



## <a name="video"></a>Video's


> [AZURE.VIDEO 218]

> [AZURE.VIDEO usage-monitoring-application-insights]

> [AZURE.VIDEO performance-monitoring-application-insights]



<!--Link references-->

[qna]: app-insights-troubleshoot-faq.md



<!--HONumber=Jun16_HO2-->


