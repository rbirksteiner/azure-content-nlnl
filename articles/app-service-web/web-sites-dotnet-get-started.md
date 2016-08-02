<properties
    pageTitle="Een ASP.NET-app in Azure App Service implementeren met Visual Studio | Microsoft Azure"
    description="Ontdek hoe u een ASP.NET-webproject implementeert in een nieuwe web-app in Azure App Service met behulp van Visual Studio."
    services="app-service\web"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="04/22/2016"
    ms.author="tdykstra"/>

# Een ASP.NET-web-app in Azure App Service implementeren met Visual Studio

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

## Overzicht

In deze zelfstudie ziet u hoe u een ASP.NET-web-app implementeert in een [web-app in Azure App Service](app-service-web-overview.md) met behulp van Visual Studio 2015.

Voor deze zelfstudie wordt ervan uitgegaan dat u een ASP.NET-ontwikkelaar bent die geen ervaring heeft met Azure. Wanneer u klaar bent, beschikt u over een eenvoudige web-app die wordt uitgevoerd in de cloud.

U leert het volgende:

* Hoe u een nieuwe App Service-web-app maakt terwijl u een nieuw webproject maakt in Visual Studio.
* Hoe u een webproject implementeert in een App Service-web-app met behulp van Visual Studio.

in het diagram ziet u wat u tijdens de zelfstudie gaat doen.

![Diagram over maken en implementeren met  Visual Studio](./media/web-sites-dotnet-get-started/Create_App.png)

Aan het einde van de zelfstudie staat het gedeelte [Probleemoplossing](#troubleshooting). Hierin vindt u ideeën over wat u moet doen als er iets niet werkt. In het gedeelte [Volgende stappen](#next-steps) staan koppelingen naar andere zelfstudies waarin dieper wordt ingegaan op het gebruik van Azure App Service.

Omdat dit een zelfstudie is om u op weg te helpen, wordt er slechts een eenvoudig webproject geïmplementeerd: een project zonder database waarvoor geen verificatie of autorisatie is vereist. Voor koppelingen naar onderwerpen over meer geavanceerde implementaties raadpleegt u [Een Azure-web-app implementeren](web-sites-deploy.md).

Naast de tijd die nodig is voor het installeren van de Azure SDK voor .NET, duurt het ongeveer 10-15 minuten om deze zelfstudie te voltooien.

## Vereisten

* Voor de zelfstudie wordt ervan uitgegaan dat u al eerder hebt gewerkt met ASP.NET MVC en Visual Studio. Als u een inleiding nodig hebt, raadpleegt u [Aan de slag met ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started).

* U hebt een Azure-account nodig. U kunt [een gratis Azure-account openen](/pricing/free-trial/?WT.mc_id=A261C142F) of [uw voordelen als Visual Studio-abonnee activeren](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

    Als u met Azure App Service aan de slag wilt voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](http://go.microsoft.com/fwlink/?LinkId=523751). Daar kunt u een tijdelijke en eenvoudige app maken in App Service. U hebt hiervoor geen creditcard nodig en bent nergens toe verplicht.

## <a name="setupdevenv"></a>De ontwikkelomgeving instellen

De zelfstudie is geschreven voor Visual Studio 2015 met de [Azure SDK voor .NET](../dotnet-sdk.md) 2.9 of hoger. 

* [Download de nieuwste Azure SDK voor Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). De SDK installeert Visual Studio 2015 als u daar nog niet over beschikt.

    >[AZURE.NOTE] Afhankelijk van hoeveel van de SDK-afhankelijkheden u al op uw computer hebt staan, kan het installeren van de SDK lang duren, van enkele minuten tot een half uur of meer.

Als u Visual Studio 2013 hebt en u liever dat programma gebruikt, kunt u [de nieuwste Azure SDK voor Visual Studio 2013 downloaden](http://go.microsoft.com/fwlink/?LinkID=324322). Sommige schermen zien er mogelijk anders uit dan wordt weergegeven in de afbeeldingen.

## Een nieuw webproject configureren

De volgende stap bestaat uit het maken van een webproject in Visual Studio en het maken van een web-app in Azure App Service. In dit gedeelte van de zelfstudie configureert u het nieuwe webproject. 

1. Open Visual Studio 2015.

2. Klik op **Bestand > Nieuw > Project**.

3. Klik in het dialoogvenster **Nieuw project** op **Visual C# > Web > ASP.NET-web-app**.

3. Zorg ervoor dat **.NET Framework 4.5.2** als doelframework is geselecteerd.

4.  [Azure Application Insights](../application-insights/app-insights-overview.md) bewaakt de beschikbaarheid, de prestaties en het gebruik van uw web-app. Het selectievakje **Application Insights toevoegen aan project** wordt standaard ingeschakeld wanneer u voor het eerst een webproject maakt na de installatie van Visual Studio. Schakel dit selectievakje uit als deze optie is geselecteerd, maar u Application Insights niet wilt uitproberen.

4. Noem de toepassing **MyExample** en klik vervolgens op **OK**.

    ![Het dialoogvenster Nieuw project](./media/web-sites-dotnet-get-started/GS13newprojdb.png)

5. Selecteer in het dialoogvenster **Nieuw ASP.NET-project** het **MVC**-sjabloon. Klik vervolgens op **Verificatie wijzigen**.

    Voor deze zelfstudie implementeert u een ASP.NET MVC-webproject. Als u meer informatie wilt over het implementeren van een ASP.NET-web-API-project, raadpleegt u het gedeelte [Volgende stappen](#next-steps). 

    ![Het dialoogvenster Nieuw ASP.NET-project](./media/web-sites-dotnet-get-started/GS13changeauth.png)

6. Klik in het dialoogvenster **Verificatie wijzigen** op **Geen verificatie** en vervolgens op **OK**.

    ![Geen verificatie](./media/web-sites-dotnet-get-started/GS13noauth.png)

    Deze zelfstudie is bedoeld om u op weg te helpen en daarom wordt er een eenvoudige app geïmplementeerd waarbij gebruikers zich niet hoeven aan te melden.

5. Zorg er in het gedeelte **Microsoft Azure** van het dialoogvenster **Nieuw ASP.NET-project** voor dat **Hosten in de cloud** is geselecteerd en dat **App Service** is geselecteerd in de vervolgkeuzelijst.

    ![Het dialoogvenster Nieuw ASP.NET-project](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)

    Met deze instellingen stelt u in dat Visual Studio een Azure-web-app maakt voor uw webproject.

6. Klik op **OK**

## Azure-resources configureren voor een nieuwe web-app

U kunt nu in Visual Studio aangeven welke Azure-resources er moeten worden gemaakt.

5. Klik in het dialoogvenster **App Service maken** op **Account toevoegen**. Meld u vervolgens bij Azure aan met de id en het wachtwoord van het account dat u gebruikt om uw Azure-abonnement te beheren.

    ![Aanmelden bij Azure](./media/web-sites-dotnet-get-started/configuresitesettings.png)

    Als u zich al eerder op dezelfde computer hebt aangemeld, wordt de knop **Account toevoegen** mogelijk niet weergegeven. In dat geval kunt u deze stap overslaan. Mogelijk moet u echter uw referenties opnieuw opgeven.
 
3. Voer een **Web-appnaam** in die uniek is in het domein *azurewebsites.net*. U kunt de app bijvoorbeeld MyExample noemen, met toegevoegde cijfers om de naam echt uniek te maken, zoals MyExample810. Als er een standaardnaam voor u wordt gemaakt, is deze uniek en kunt u die gebruiken.

    Als iemand anders de opgegeven naam al heeft gebruikt, ziet u rechts een rood uitroepteken in plaats van een groen vinkje. U moet dan een andere naam invoeren.

    De URL van uw toepassing is deze naam plus *.azurewebsites.net*. Als de naam bijvoorbeeld `MyExample810` is, is de URL `myexample810.azurewebsites.net`.

    U kunt voor een Azure-web-app ook een aangepast domein gebruiken. Zie voor meer informatie [Een aangepaste domeinnaam configureren in Azure App Service](web-sites-custom-domain-name.md).

6. Klik naast het vak **Resourcegroep** op de knop **Nieuw** en geef de naam MyExample op. Desgewenst kunt u ook een andere naam gebruiken. 

    ![Het dialoogvenster App Service maken](./media/web-sites-dotnet-get-started/rgcreate.png)

    Een resourcegroep is een verzameling Azure-resources, zoals web-apps, databases en virtuele machines. Bij een zelfstudie is het gewoonlijk het beste om een nieuwe resourcegroep te maken. U kunt dan eenvoudig in één stap alle Azure-resources verwijderen die u tijdens de zelfstudie maakt. Zie voor meer informatie [Overzicht van Azure Resource Manager](../resource-group-overview.md).

4. Klik naast de vervolgkeuzelijst **App Service-plan** op de knop **Nieuw**.

    ![Het dialoogvenster App Service maken](./media/web-sites-dotnet-get-started/createasplan.png)

    Het dialoogvenster **App Service-plan configureren** wordt weergegeven.

    ![Het dialoogvenster App Service configureren](./media/web-sites-dotnet-get-started/configasp.png)

    In de volgende stappen configureert u een App Service-plan voor de nieuwe resourcegroep. In een App Service-plan worden de rekenresources opgegeven op basis waarvan uw web-app wordt uitgevoerd. Als u bijvoorbeeld de gratis categorie hebt gekozen, wordt uw API-app uitgevoerd op basis van gedeelde virtuele machines. Bij sommige categorieën waar u voor betaalt, worden apps uitgevoerd via exclusieve virtuele machines. Zie voor meer informatie [Overzicht van App Service-plannen](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Geef in het dialoogvenster **App Service-plan configureren** de naam MyExamplePlan op. Desgewenst kunt u ook een andere naam gebruiken.

5. Kies in de vervolgkeuzelijst **Locatie** de locatie die het dichtst bij u ligt.

    Met deze instelling bepaalt u in welk Azure-datacentrum uw app wordt uitgevoerd. Voor deze zelfstudie kunt u een willekeurige regio selecteren. Dit maakt geen merkbaar verschil. Bij een productie-app moet de server echter zo dicht mogelijk bij de clients staan die de app gebruiken. Zo minimaliseert u de [latentie](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Klik in de vervolgkeuzelijst **Grootte** op **Gratis**.

    Voor deze zelfstudie levert de gratis prijscategorie voldoende prestaties.

6. Klik in het dialoogvenster **App Service-plan configureren** op **OK**.

7. Klik in het dialoogvenster **App Service maken** op **Maken**.

## Visual Studio maakt het project en de web-app

Visual Studio doet er meestal minder dan een minuut over om het webproject en de web-app te maken.  

In het venster **Solution Explorer** ziet u de bestanden en mappen van het nieuwe project.

![Solution Explorer](./media/web-sites-dotnet-get-started/solutionexplorer.png)

In het venster **Azure App Service-activiteit** ziet u dat de web-app is gemaakt.

![Web-app gemaakt in het venster Azure App Service-activiteit](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

In het venster **Cloud Explorer** kunt u Azure-resources bekijken en beheren, inclusief die van de nieuwe web-app die u zojuist hebt gemaakt.

![Web-app gemaakt in Cloud Explorer](./media/web-sites-dotnet-get-started/siteinse.png)
    
## Het webproject implementeren in de Azure-web-app

In dit gedeelte implementeert u het webproject in de web-app.

1. Klik in **Solution Explorer** met de rechtermuisknop op het project. Klik vervolgens op **Publiceren**.

    ![Publiceren selecteren in het Visual Studio-menu](./media/web-sites-dotnet-get-started/choosepublish.png)

    Na enkele seconden verschijnt de wizard **Webpublicatie**. De wizard wordt geopend. U ziet een *publicatieprofiel* met instellingen voor het implementeren van het webproject in de nieuwe web-app.

    Het publicatieprofiel bevat een gebruikersnaam en een wachtwoord voor implementatie.  Deze referenties zijn voor u gegenereerd. U hoeft ze niet in te voeren. Het wachtwoord is versleuteld in een verborgen, gebruikersspecifiek bestand in de map `Properties\PublishProfiles`.
 
8. Klik op het tabblad **Verbinding** van de wizard **Webpublicatie** op **Volgende**.

    ![Op Volgende klikken op het tabblad Verbinding van de wizard Webpublicatie](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)

    Vervolgens gaat u naar het tabblad **Instellingen**. Hier kunt u de buildconfiguratie wijzigen om een build voor foutopsporing te implementeren voor [foutopsporing op afstand](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). Het tabblad biedt ook diverse [opties voor het publiceren van bestanden](https://msdn.microsoft.com/library/dd465337.aspx#Anchor_2).

10. Klik op het tabblad **Instellingen** op **Volgende**.

    ![Tabblad Instellingen van de wizard Webpublicatie](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)

    Vervolgens gaat u naar het tabblad **Voorbeeld**. Hier hebt u de mogelijkheid om te bekijken welke bestanden van uw project worden gekopieerd naar de API-app. Wanneer u een project implementeert naar een API-app die u al eerder had geïmplementeerd, worden alleen de gewijzigde bestanden gekopieerd. Als u een overzicht wilt zien van wat er wordt gekopieerd, klikt u op de knop **Voorbeeldweergave starten**.

11. Klik op het tabblad **Voorbeeld** op **Publiceren**.

    ![Tabblad Voorbeeld van de wizard Webpublicatie](./media/web-sites-dotnet-get-started/GS13previewoutput.png)

    Wanneer u op **Publiceren** klikt, begint Visual Studio met het kopiëren van de bestanden naar de Azure-server. Dit kan een paar minuten duren.

    In de vensters **Uitvoer** en **Azure App Service-activiteit** staat welke implementatieacties zijn uitgevoerd. Ook vindt u er voltooiingsrapporten over de implementatie.

    ![Visual Studio-venster Uitvoer met een melding over het voltooien van de implementatie](./media/web-sites-dotnet-get-started/PublishOutput.png)

    Wanneer de implementatie is voltooid, wordt de standaardbrowser automatisch geopend. De URL van de geïmplementeerde web-app wordt geopend en de toepassing die u hebt gemaakt, wordt nu uitgevoerd in de cloud. Aan de URL in de adresbalk van de browser ziet u dat de web-app is geladen via internet.

    ![Web-app die wordt uitgevoerd in Azure](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

    > [AZURE.TIP] U kunt de werkbalk **Publicatie met één klik** inschakelen voor snelle implementatie. Klik op **Weergave > Werkbalken** en selecteer vervolgens **Webpublicatie met één klik**. U kunt de werkbalk gebruiken om een profiel te selecteren, op een knop klikken voor publicatie of op een knop klikken om de wizard **Webpublicatie** te openen.
    > ![Werkbalk Webpublicatie met één klik](./media/web-sites-dotnet-get-started/weboneclickpublish.png)

## Problemen oplossen

Als u een probleem ondervindt terwijl u deze zelfstudie doorloopt, controleert u of u de nieuwste versie van de Azure SDK voor .NET gebruikt. Dit kunt u het eenvoudigst doen door [de Azure SDK voor Visual Studio 2015 te downloaden](http://go.microsoft.com/fwlink/?linkid=518003). Als u de huidige versie hebt geïnstalleerd, ziet u in het webplatforminstallatieprogramma dat er geen installatie nodig is.

Als u een bedrijfsnetwerk gebruikt en u via een firewall probeert Azure App Service te implementeren, zorgt u ervoor dat de poorten 443 en 8172 zijn geopend voor Web Deploy. Als u deze poorten niet kunt openen, raadpleegt u het gedeelte Volgende stappen hieronder voor andere implementatie-opties.

Wanneer uw ASP.NET-web-app eenmaal wordt uitgevoerd in Azure App Service, doet u er verstandig aan u te verdiepen in de Visual Studio-functies die het oplossen van problemen vereenvoudigen. Voor meer informatie over logboekregistratie, foutopsporing op afstand en meer raadpleegt u [Probleemoplossing voor Azure-web-apps in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

## Volgende stappen

In deze zelfstudie hebt u gezien hoe u een eenvoudige web-app makt en hoe u deze implementeert in een Azure-web-app. Hier volgen enkele verwante onderwerpen en resources voor meer informatie over Azure App Service:

* Uw web-app bewaken en beheren in de [Azure Portal](https://portal.azure.com/). 

    Voor meer informatie raadpleegt u het [Overzicht van de Azure Portal](/services/management-portal/) en [Web-apps configureren in Azure App Service](web-sites-configure.md).

* Een bestaand webproject implementeren in een nieuwe web-app met Visual Studio

    Klik met de rechtermuisknop op het project in **Solution Explorer**. Klik vervolgens op **Publiceren**. Selecteer **Microsoft Azure App Service** als publicatiedoel en klik vervolgens op **Nieuw**. De dialoogvensters zijn hetzelfde als de dialoogvensters die u in deze zelfstudie hebt gezien.

* Een webproject implementeren via broncodebeheer

    Voor informatie over [automatisch implementeren](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) via een [broncodebeheersysteem](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) gaat u naar [Aan de slag met web-apps in Azure App Service](app-service-web-get-started.md) en [Een Azure-web-app implementeren](web-sites-deploy.md).

* Een ASP.NET-web-API implementeren in een API-app in Azure App Service

    U hebt al gezien hoe u een exemplaar van Azure App Service kunt maken die hoofdzakelijk is bedoeld voor het hosten van een website. App Service biedt ook functies voor het hosten van web-API's, zoals ondersteuning voor CORS en API-metagegevens voor het genereren van clientcode. U kunt de API-functies gebruiken in een web-app, maar als u vooral een API wilt hosten in een App Service-exemplaar, is een **API-app** een betere keuze. Voor meer informatie raadpleegt u [Aan de slag met API-apps en ASP.NET in Azure App Service](../app-service-api/app-service-api-dotnet-get-started.md). 

* Een aangepaste domeinnaam en SSL toevoegen

    Raadpleeg de volgende resources voor meer informatie over het gebruik van SSL en uw eigen domein (bijvoorbeeld www.contoso.com in plaats van contoso.azurewebsites.net):

    * [Een aangepaste domeinnaam configureren in Azure App Service](web-sites-custom-domain-name.md)
    * [HTTPS inschakelen voor een Azure-website](web-sites-configure-ssl-certificate.md)

* Als u deze niet meer nodig hebt, verwijdert u de resourcegroep die uw web-app bevat, evenals alle gerelateerde Azure-resources.

    Voor meer informatie over werken met resourcegroepen in de Azure Portal raadpleegt u [De Azure Portal gebruiken om Azure-resources te implementeren en te beheren](../resource-group-portal.md).   


<!--HONumber=Jun16_HO2-->


