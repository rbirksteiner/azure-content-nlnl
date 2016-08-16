<properties
    pageTitle="Analyse van Java-web-apps met Application Insights | Microsoft Azure"
    description="Bewaak prestaties en gebruik van uw Java-website met Application Insights. "
    services="application-insights"
    documentationCenter="java"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/12/2016"
    ms.author="awills"/>

# Aan de slag met Application Insights in een Java-webproject

*Application Insights verkeert momenteel in de preview-fase.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

[Application Insights](https://azure.microsoft.com/services/application-insights/) is een uitbreidbare analyseservice die u helpt de prestaties en het gebruik van uw live-toepassing te begrijpen. Gebruik Application Insights om [prestatieproblemen en uitzonderingen te detecteren en te onderzoeken](app-insights-detect-triage-diagnose.md), en om [code te schrijven][api] om bij te houden wat gebruikers met uw app doen.

![voorbeeldgegevens](./media/app-insights-java-get-started/5-results.png)

Application Insights biedt ondersteuning voor Java-apps die op Linux, Unix of Windows worden uitgevoerd.

U hebt het volgende nodig:

* Oracle JRE 1.6 of hoger, of Zulu JRE 1.6 of hoger
* Een abonnement op [Microsoft Azure](https://azure.microsoft.com/). (U zou kunnen beginnen met de [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).)

*Als u een web-app hebt die al is gepubliceerd, kunt u de alternatieve procedure volgen om [de SDK tijdens runtime toe te voegen in de webserver](app-insights-java-live.md). Met deze alternatieve procedure hoeft u de code niet helemaal aan te passen, maar u hebt niet de optie om code te schrijven voor het bijhouden van gebruikersactiviteit.*


## 1. Een Application Insights-instrumentatiesleutel ophalen

1. Meld u aan bij de [Microsoft Azure Portal](https://portal.azure.com).
2. Maak een nieuwe Application Insights-resource.

    ![Op + klikken en Application Insights kiezen](./media/app-insights-java-get-started/01-create.png)
3. Stel het toepassingstype in op Java-webtoepassing.

    ![Een naam invoeren, Java-web-app kiezen en op Maken klikken](./media/app-insights-java-get-started/02-create.png)
4. Zoek de instrumentatiesleutel van de nieuwe resource. U moet deze zo dadelijk in de code van uw project plakken.

    ![Op Eigenschappen klikken in het overzicht van de nieuwe resource en de instrumentatiesleutel kopiëren](./media/app-insights-java-get-started/03-key.png)

## 2. De Application Insights-SDK voor Java toevoegen aan uw project

*Kies de juiste methode voor uw project.*

#### Als u Eclipse gebruikt om een Maven- of Dynamic Web-project te maken...

Gebruik de [invoegtoepassing Application Insights-SDK voor Java][eclipse].

#### Als u Maven gebruikt...

Als uw project al is ingesteld om voor de build Maven te gebruiken, voegt u de volgende code in uw pom.xml-bestand in.

Vervolgens vernieuwt u de projectafhankelijkheden om de binaire bestanden te downloaden.

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[1.0,)</version>
      </dependency>
    </dependencies>


* *Validatiefouten in build of controlesom?* Probeer een specifieke versie te gebruiken, bijvoorbeeld: `<version>1.0.n</version>`. U vindt de nieuwste versie in de [SDK-releaseopmerkingen](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) of in onze [Maven-artefacten](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Moet u bijwerken naar een nieuwe SDK?* Vernieuw de afhankelijkheden van uw project.

#### Als u Gradle gebruikt...

Als uw project al is ingesteld om voor de build Gradle te gebruiken, voegt u de volgende code in uw build.gradle-bestand in.

Vervolgens vernieuwt u de projectafhankelijkheden om de binaire bestanden te downloaden.

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '1.+'
      // or applicationinsights-core for bare API
    }

* *Validatiefouten in build of controlesom? Probeer een specifieke versie te gebruiken, bijvoorbeeld: * `version:'1.0.n'`. *U vindt de nieuwste versie in de [SDK-releaseopmerkingen](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).*
* *Bijwerken naar een nieuwe SDK*
 * Vernieuw de afhankelijkheden van uw project.

#### Of...

Voeg de SDK handmatig toe:

1. Download de [Application Insights-SDK voor Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.html).
2. Pak het zip-bestand uit en voeg de binaire bestanden toe aan uw project.

### Vragen...

* *Wat is de relatie tussen de `-core`- en `-web`-onderdelen in het zip-bestand?*

 * `applicationinsights-core` geeft u de bare-API. U hebt dit onderdeel altijd nodig.
 * `applicationinsights-web` geeft u metrische gegevens die het aantal HTTP-aanvragen en -reactietijden bijhouden. U kunt dit onderdeel weglaten als u deze telemetrie niet automatisch wilt verzamelen. Bijvoorbeeld omdat u deze zelf wilt schrijven.

* *De SDK bijwerken wanneer we wijzigingen publiceren*
 * Download de meest recente [Application Insights-SDK voor Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.zip) en vervang de oude versie.
 * De wijzigingen worden beschreven in de [SDK-releaseopmerkingen](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).



## 3. Een Application Insights-.xml-bestand toevoegen

Voeg ApplicationInsights.xml toe aan de resourcesmap in uw project of plaats het in het implementatieklassepad van uw project. Kopieer de volgende XML-code naar het bestand.

Vervang de instrumentatiesleutel die u in de Azure Portal hebt verkregen.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* De instrumentatiesleutel wordt samen met alle telemetrie-items verzonden en instrueert Application Insights om deze in de resource weer te geven.
* Het onderdeel voor de HTTP-aanvraag is optioneel. Het verzendt automatisch telemetrie over aanvragen en reactietijden naar de portal.
* Correlatie tussen gebeurtenissen is een aanvulling op het onderdeel voor de HTTP-aanvraag. Deze aanvulling wijst een id toe aan elke aanvraag die door de server wordt ontvangen en voegt deze als de eigenschap 'Operation.Id' toe aan elk telemetrie-item. Op deze manier kunt u correlaties zichtbaar maken tussen de telemetrie die aan elke aanvraag is gekoppeld. Dit doet u door een filter in te stellen in [Diagnostische gegevens doorzoeken][diagnostics].

## 4. Een HTTP-filter toevoegen

De laatste configuratiestap stelt het onderdeel voor de HTTP-aanvraag in staat elke webaanvraag vast te leggen. (Niet vereist als u alleen de bare-API wilt.)

Zoek en open het web.xml-bestand in uw project en voeg de volgende code samen onder het web-app-knooppunt, waar de toepassingsfilters zijn geconfigureerd.

Voor de nauwkeurigste resultaten moet het filter vóór alle andere filters worden toegewezen.

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

#### Als u MVC 3.1 of hoger gebruikt

Bewerk deze elementen zodanig dat het Application Insights-pakket is opgenomen:

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>

#### Als u Struts 2 gebruikt

Voeg dit item toe aan het Struts-configuratiebestand (meestal struts.xml of struts-default.xml):

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

(Als u interceptors hebt gedefinieerd in een standaardstack, kan de interceptor gewoon worden toegevoegd aan die stack.)



## 5. Uw toepassing uitvoeren

Voer uw app uit in de foutopsporingsmodus op uw ontwikkelcomputer of publiceer de app op uw server.

## 6. Uw telemetrie in Application Insights weergeven


Ga terug naar uw Application Insights-resource in de [Microsoft Azure Portal](https://portal.azure.com).

Gegevens van HTTP-aanvragen worden weergegeven op de overzichtsblade. (Als dit niet het geval is, wacht u een paar seconden en klikt u vervolgens op Vernieuwen.)

![voorbeeldgegevens](./media/app-insights-java-get-started/5-results.png)

[Meer informatie over metrische gegevens.][metrics]

Klik in een grafiek voor gedetailleerdere cumulatieve metrische gegevens.

![](./media/app-insights-java-get-started/6-barchart.png)

> Application Insights gaat uit van de volgende indeling van HTTP-aanvragen voor MVC-toepassingen: `VERB controller/action`. `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` en `GET Home/Product/sdf96vws` worden bijvoorbeeld gegroepeerd in `GET Home/Product`. Op deze manier kunnen er zinvolle sets aanvragen worden samengesteld, zoals het aantal aanvragen en de gemiddelde runtime voor aanvragen.


### Gegevens van exemplaren 

Klik op een specifiek aanvraagtype om de afzonderlijke exemplaren weer te geven. 

In Application Insights worden twee soorten gegevens weergegeven: cumulatieve gegevens (opgeslagen en weergegeven als gemiddelden, aantallen en sommen) en gegevens van exemplaren (afzonderlijke rapporten over HTTP-aanvragen, uitzonderingen, paginaweergaven of aangepaste gebeurtenissen).

Wanneer u de eigenschappen van een aanvraag bekijkt, ziet u de bijbehorende telemetrische gebeurtenissen, zoals aanvragen en uitzonderingen.

![](./media/app-insights-java-get-started/7-instance.png)


### Analyse: krachtige querytaal

Naarmate u meer gegevens verzamelt, kunt u query's uitvoeren voor zowel het samenvoegen van gegevens als het zoeken naar afzonderlijke exemplaren. [Analyse]() is een krachtig hulpprogramma om inzicht te krijgen in prestaties en gebruik, en om diagnoses uit te voeren.

![Voorbeeld van het hulpprogramma Analyse](./media/app-insights-java-get-started/025.png)


## 5. Uw app installeren op de server

Publiceer nu uw app op de server, geef de app vrij voor gebruik en bekijk de telemetrische gegevens die in de portal binnenkomen.

* Controleer of de firewall het verzenden van telemetrie door uw app naar deze poorten toestaat:

 * dc.services.visualstudio.com:443
 * dc.services.visualstudio.com:80
 * f5.services.visualstudio.com:443
 * f5.services.visualstudio.com:80


* Installeer op Windows-servers:

 * [Microsoft Visual C++ Redistributable](http://www.microsoft.com/download/details.aspx?id=40784)

    (Hiermee kunt u prestatiemeteritems instellen.)

## Uitzonderingen en mislukte aanvragen

Onverwerkte uitzonderingen worden automatisch verzameld:

![Schuif naar beneden en klik op de tegel Fouten](./media/app-insights-java-get-started/21-exceptions.png)

Voor het verzamelen van gegevens over andere uitzonderingen hebt u twee opties:

* [Aanroepen naar trackException() invoegen in uw code][apiexceptions]. 
* [De Java-agent installeren op uw server](app-insights-java-agent.md). U specificeert de methoden die u wilt bekijken.


## Methodeaanroepen en externe afhankelijkheden bewaken

[Installeer de Java-agent](app-insights-java-agent.md) om gespecificeerde interne methoden en oproepen via JDBC vast te leggen, inclusief timinggegevens.


## Prestatiemeteritems

Klik op de tegel **Servers**. U ziet nu een groot aantal prestatiemeteritems.


![](./media/app-insights-java-get-started/11-perf-counters.png)

### Het verzamelen van prestatiemeteritems aanpassen

Als u het verzamelen van de standaardset prestatiemeteritems wilt uitschakelen, voegt u de volgende code toe onder het hoofdknooppunt van het ApplicationInsights.xml-bestand:

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### Verzamelen van aanvullende prestatiemeteritems

U kunt opgeven dat er aanvullende prestatiemeteritems moeten worden verzameld.

#### JMX-tellers (weergegeven door de virtuele Java-machine)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*   `displayName` - De naam die wordt weergegeven in de Application Insights-portal.
*   `objectName` - De JMX-objectnaam.
*   `attribute` - Het kenmerk van de JMX-objectnaam dat moet worden opgehaald
*   `type` (optioneel) - Het type kenmerk van het JMX-object:
 *  Standaard: een eenvoudig type, zoals int of long.
 *  `composite`: de gegevens van de prestatiemeteritems hebben de indeling 'Attribute.Data'
 *  `tabular`: de gegevens van de prestatiemeteritems hebben de vorm van een rij in een tabel



#### Windows-prestatiemeteritems

Elk [Windows-prestatiemeteritem](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) maakt deel uit van een categorie (net zoals een veld deel uitmaakt van een klasse). Categorieën kunnen globaal zijn, maar ook genummerde of benoemde exemplaren hebben.

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*   displayName: de naam die wordt weergegeven in de Application Insights-portal.
*   categorynaam: de prestatiemeteritemcategorie (prestatie-object) waaraan dit prestatiemeteritem is gekoppeld.
*   counterName: de naam van het prestatiemeteritem.
*   instanceName: de naam van het exemplaar van de prestatiemeteritemcategorie, of een lege tekenreeks ("") als de categorie slechts één exemplaar bevat. Als de categorienaam Process is en het prestatiemeteritem dat u wilt verzamelen, afkomstig is uit het huidige JVM-proces waarop uw app wordt uitgevoerd, specificeert u `"__SELF__"`.

De prestatiemeteritems zijn zichtbaar als aangepaste metrische gegevens in [Metrics Explorer][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)


### Unix-prestatiemeteritems

* [Installeer collectd met de Application Insights-invoegtoepassing](app-insights-java-collectd.md) om een scala aan systeem- en netwerkgegevens op te halen.

## Gebruikers- en sessiegegevens ophalen

U verzendt nu telemetrie vanaf de webserver. Wilt u echt een volledig inzicht in uw toepassing, dan kunt u nog meer bewakingsopties toevoegen:

* [Voeg telemetrie toe aan uw webpagina's][usage] voor het bewaken van paginaweergaven en metrische gegevens over gebruikers.
* [Webtests instellen][availability] om te controleren of de toepassing live en responsief blijft.

## Logboektraceringen vastleggen

U kunt Application Insights gebruiken om logboeken op te delen vanuit Log4J, Logback en andere frameworks voor logboekregistratie. U kunt de logboeken correleren met HTTP-aanvragen en andere telemetrie. [Hier leest u hoe u dat doet][javalogs].

## Uw eigen telemetrie verzenden

Nu u de SDK hebt geïnstalleerd, kunt u de API gebruiken voor het verzenden van uw eigen telemetrie.

* [Houd aangepaste gebeurtenissen en metrische gegevens bij][api] voor meer informatie over wat gebruikers met uw toepassing doen.
* [Zoek naar gebeurtenissen en logboeken][diagnostics] om u te helpen bij het analyseren van problemen.


## Webtests voor beschikbaarheid

Application Insights kan uw website regelmatig testen om te controleren of deze actief is en goed reageert. Als u deze functie wilt [instellen][availability], schuift u omlaag en klikt u op Beschikbaarheid.

![Omlaag schuiven, op Beschikbaarheid klikken en vervolgens op Webtest toevoegen klikken](./media/app-insights-java-get-started/31-config-web-test.png)

Er worden grafieken weergegeven met reactietijden en u ontvangt e-mailmeldingen als uw site uitvalt.

![Voorbeeld van een webtest](./media/app-insights-java-get-started/appinsights-10webtestresult.png)

[Meer informatie over de webtests voor beschikbaarheid][availability]. 






## Vragen? Problemen?

[Problemen met Java oplossen](app-insights-java-troubleshoot.md)

## Volgende stappen

Raadpleeg het [Java Developer Center](/develop/java/) voor meer informatie.

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostics]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md



<!--HONumber=Jun16_HO2-->


