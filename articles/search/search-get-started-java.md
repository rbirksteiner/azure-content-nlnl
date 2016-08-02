<properties
    pageTitle="Aan de slag met Azure Search in Java | Microsoft Azure | Gehoste service voor zoeken in de cloud"
    description="Een gehoste cloudtoepassing voor zoeken in Azure bouwen met de programmeertaal Java."
    services="search"
    documentationCenter=""
    authors="EvanBoyle"
    manager="pablocas"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="03/08/2016"
    ms.author="evboyle"/>

# Aan de slag met Azure Search in Java
> [AZURE.SELECTOR]
- [Portal](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Informatie over het bouwen van een aangepaste Java-zoektoepassing die voor de zoekfunctie gebruikmaakt van Azure Search. In deze zelfstudie wordt de [Azure Search Service REST API](https://msdn.microsoft.com/library/dn798935.aspx) gebruikt om de objecten en bewerkingen in deze oefening te bouwen.

Als u dit voorbeeld wilt uitvoeren, moet u over de Azure Search-service beschikken. U kunt zich hiervoor aanmelden in [Azure Portal](https://portal.azure.com). Zie [Een Azure Search-service in de portal maken](search-create-service-portal.md) voor stapsgewijze instructies.

De volgende software is gebruik om deze sample te maken en te testen:

- [Eclipse IDE voor Java EE-ontwikkelaars](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Zorg ervoor dat u de EE-versie downloadt. Voor een van de verificatiestappen is een functie vereist die alleen in deze versie beschikbaar is.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## Over de gegevens

In deze voorbeeldtoepassing wordt gebruikgemaakt van gegevens van [United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), gefilterd op de staat Rhode Island om de grootte van de gegevensset te reduceren. We gebruiken deze gegevens om een zoektoepassing te bouwen die kenmerkende gebouwen, zoals ziekenhuizen of scholen, gebouwen zoals ziekenhuizen en scholen, maar ook geologische kenmerken, zoals stromen, meren en toppen, retourneert.

In deze toepassing zorgt het programma **SearchServlet.java** er met een [indexeerfunctie](https://msdn.microsoft.com/library/azure/dn798918.aspx) voor dat de index wordt gebouwd en geladen en dat de gefilterde USGS-gegevensset uit een openbare Azure SQL-database wordt opgehaald. De programmacode bevat de vooraf gedefinieerde referenties en gegevens voor verbinding met de onlinegegevensbron. Voor de toegang tot de gegevens hoeft u verder niets te configureren.

> [AZURE.NOTE] Er is een filter op de gegevensset toegepast om onder de limiet van 10.000 documenten voor de gratis prijscategorie te blijven. Als u de standaardcategorie gebruikt, is de limiet niet van toepassing en kunt u deze code aanpassen om een grotere gegevensset te gebruiken. Zie [Limieten en beperkingen](search-limits-quotas-capacity.md) voor meer informatie over de capaciteit voor elke prijscategorie.

## Over de programmabestanden

In de volgende lijst worden de bestanden beschreven die relevant zijn voor dit voorbeeld.

- Search.jsp: levert de gebruikersinterface
- SearchServlet.java: biedt methoden (vergelijkbaar met een controller in MVC)
- SearchServiceClient.java: verwerkt de HTTP-aanvragen
- SearchServiceHelper.java: een helperklasse die statische methoden biedt
- Document.Java: levert het gegevensmodel
- Config.Properties: stelt de URL van de zoekservice en de API-sleutel in
- Pom.XML: een Maven-afhankelijkheid

<a id="sub-2"></a>
## De servicenaam en API-sleutel van uw Azure Search-service zoeken

Voor alle REST API-aanroepen in Azure Search geldt dat u de service-URL en API-sleutel moet opgeven. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik in de snelbalk op de **Search-service** om alle Azure Search-services weer te geven die zijn ingericht voor uw abonnement.
3. Selecteer de service die u wilt gebruiken.
4. Op het servicedashboard worden tegels weergegeven voor essentiële informatie. Daarnaast wordt het sleutelpictogram voor toegang tot de beheersleutels weergegeven.

    ![][3]

5. Kopieer de service-URL en een beheersleutel. Deze hebt u later weer nodig wanneer u ze toevoegt aan het bestand **config.properties**.

## De voorbeeldbestanden downloaden

1. Ga naar [AzureSearchJavaDemo](http://go.microsoft.com/fwlink/p/?LinkId=530197) op Github.

2. Klik op **Download ZIP** (ZIP downloaden), sla het zip-bestand op naar de schijf en pak vervolgens alle bestanden in het zip-bestand uit. U kunt de bestanden eventueel uitpakken naar uw Java-werkruimte, zodat u het project later eenvoudig kunt terugvinden.

3. De voorbeeldbestanden hebben het kenmerk alleen -lezen Klik met de rechtermuisknop op de mapeigenschappen en verwijder het kenmerk alleen-lezen.

Alle volgende bestandswijzigingen en uitvoerinstructies worden uitgevoerd voor de bestanden in deze map.  

## Project importeren

1. Klik in Eclipse achtereenvolgens op **File** >  (Bestand)**Import** (Importeren) > **General** (Algemeen) > **Existing Projects into Workspace** (Bestaand project naar werkruimte).

    ![][4]

2. Blader in **Select root directory** (Hoofddirectory selecteren) naar de map met de voorbeeldbestanden. Selecteer de map die de map .project bevat. Het project moet worden weergegeven in de lijst **Projects** (Projecten) als een geselecteerd item.

    ![][12]

3. Klik op **Voltooien**.

4. Gebruik **Projectverkenner** om de bestanden weer te geven en te bewerken. Als Projectverkenner nog niet is geopend, klikt u op **Window** (Venster) > **Show View** (Weergaven tonen) > **Projectverkenner** of gebruik de snelkoppeling om Projectverkenner te openen.

## Configureer de service-URL en API-sleutel

1. Dubbelklik in **Projectverkenner** op **config.properties** om de configuratie-instellingen met de servernaam en API-sleutel te bewerken.

2. Raadpleeg de eerdere stappen in dit artikel, waarin u de service-URL en API-sleutel in [Azure Portal](https://portal.azure.com) hebt gevonden, om de waarden te verkrijgen die u nu moet opgeven in **config.properties**.

3. In **config.properties** vervangt u 'Api Key' door de API-sleutel voor uw service. Vervolgens wordt in hetzelfde bestand servicename (het eerste onderdeel van de URL http://servicename.search.windows.net) vervangen door de servicenaam.

    ![][5]

## Configureer de project-, build en runtime-omgeving

1. Ga in Eclipse naar de Projectverkenner en klik met de rechtermuisknop op het project > **Eigenschappen** > **Project-facetten**.

2. Selecteer **Dynamic Web Module** (Dynamische webmodule), **Java** en **JavaScript**.

    ![][6]

3. Klik op **Apply** (Toepassen).

4. Selecteer **Window** (Venster) > **Preferences** (Voorkeuren) > **Server** > **Runtime Environments** (Runtime-omgevingen) > **Add..** (Toevoegen).

5. Vouw Apache uit en selecteer de versie van de Apache Tomcat-server die u eerder hebt geïnstalleerd. Op ons systeem hebben we versie 8 geïnstalleerd.

    ![][7]

6. Geef op de volgende pagina de Tomcat-installatiedirectory op. Op een Windows-computer is dit waarschijnlijk C:\Program Files\Apache Software Foundation\Tomcat *versie*.

6. Klik op **Voltooien**.

7. Selecteer **Window** (Venster) > **Preferences** (Voorkeuren) > **Java** > **Installed JREs**(Geïnstalleerde JRE's) > **Add** (Toevoegen).

8. Selecteer in het venster **Add JRE** (JRE toevoegen) de optie **Standard VM** (Standaard-VM).

10. Klik op **Next** (Volgende).

11. Klik in JRE Definition (JRE-definitie), in JRE home (JRE-startpagina), op **Directory**.

12. Navigeer naar **Program Files** > **Java** en selecteer de JDK die u eerder hebt geïnstalleerd. Het is belangrijk dat JDK als de JRE wordt geselecteerd.

13. Kies bij Installed JREs (Geïnstalleerd JRE's) de optie **JDK**. Uw instellingen zijn vergelijkbaar met de volgende schermopname.

    ![][9]

14. Selecteer eventueel **Window** (Venster) > **Web Browser** (Webbrowser) > **Internet Explorer** om de toepassing te openen in een extern browservenster. Het gebruik van de externe browser biedt een betere gebruikservaring voor de webtoepassing.

    ![][8]

U hebt de configuratie nu voltooid. Vervolgens kunt u het project bouwen en uitvoeren.

## Het project bouwen

1. Klik in Projectverkenner met de rechtermuisknop op de projectnaam en kies **Uitvoeren als** > **Maven build...** (Maven-build) om het project te configureren.

    ![][10]

8. Ga naar Edit Configuration (Configuratie bewerken), typ bij Goals (Doelen) 'clean install' en klik vervolgens op **Run** (Uitvoeren).

Statusberichten worden uitgevoerd naar het consolevensters. Als het goed is wordt de tekst BUILD SUCCESS weergegeven. Hiermee wordt aangegeven dat het project zonder fouten is gebouwd.

## De app uitvoeren

In deze laatste stap voert u de toepassing uit in de runtime-omgeving van een lokale server.

Als u nog geen serverruntime-omgeving hebt opgegeven in Eclipse, moet u dat eerst doen.

1. Vouw in Projectverkenner **WebContent** uit.

5. Klik met de rechtermuisknop op **Search.jsp** > **Run As** (Uitvoeren als) > **Run on Server** (Uitvoeren op server). Selecteer de Apache Tomcat-server en klik vervolgens op **Uitvoeren**.

> [AZURE.TIP] Als u geen standaardwerkruimte hebt gebruikt om uw project op te slaan, moet u **Run Configuration** (Configuratie uitvoeren) wijzigen om naar de locatie van het project te wijzen, zodat er geen opstartfout voor de server optreedt. Klik in Projectverkenner met de rechtermuisknop op **Search.jsp** > **Uitvoeren als** > **Configuratie uitvoeren**. Selecteer de Apache Tomcat-server. Klik op **Argumenten**. Klik op **Werkruimte** of **Bestandssysteem** om de map in te stellen die het project bevat.

Wanneer u de toepassing uitvoert, ziet u een browservenster met een zoekvak waarin u termen kunt opgeven.

Wacht ongeveer een minuut voordat u op **Zoeken** klikt om de service de tijd te geven om de index te maken en te laden. Als er een HTTP 404-fout optreedt, moet u alleen iets langer wachten voordat u het opnieuw probeert.

## Zoeken in USGS-gegevens

De USGS-gegevensset bevat records die relevant zijn voor de staat Rhode Island. Als u op **Search** (Zoeken) klikt terwijl het zoekvak leeg is, worden de bovenste 50 vermeldingen weergegeven. Dit is de standaardinstelling.

Als u een zoekterm invoert, geeft u de zoekmachine iets om mee te werken. Voer een regionale naam in. 'Roger Williams' was de eerste gouverneur van Rhode Island. Er zijn verschillende parken, gebouwen en scholen naar hem vernoemd.

![][11]

U kunt ook de volgende termen proberen:

- Pawtucket
- Pembroke
- goose +cape

## Volgende stappen

Dit is de eerste Azure Search-zelfstudie op basis van Java en de USGS-gegevensset. In de loop van de tijd zal deze zelfstudie worden uitgebreid om aanvullende zoekfuncties te demonstreren die u mogelijk wilt gebruiken in uw aangepaste oplossingen.

Als u al enige ervaring met Azure Search hebt, kunt u dit voorbeeld gebruiken als springplank voor verdere experimenten, om de [zoekpagina](search-pagination-page-layout.md) uit te breiden of om [facetnavigatie](search-faceted-navigation.md) te implementeren. U kunt ook de pagina met zoekresultaten verbeteren door tellers toe te voegen document in batch te verwerken, zodat gebruikers door de resultaten kunnen bladeren.

Bent u niet bekend met Azure Search? Het is raadzaam andere zelfstudies te bekijken om inzicht te verwerven in wat u zoal kunt maken. Bezoek de [documentatiepagina](https://azure.microsoft.com/documentation/services/search/) voor meer resources. U kunt ook de koppelingen in de [lijst met video's en zelfstudies](search-video-demo-tutorial-list.md) volgen om meer informatie te bekijken.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png



<!--HONumber=Jun16_HO2-->


