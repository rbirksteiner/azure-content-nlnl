<properties
    pageTitle="Een WordPress-web-app maken in Azure App Service | Microsoft Azure"
    description="Ontdek hoe u via de Azure Portal een nieuwe Azure-web-app maakt voor een WordPress-blog."
    services="app-service\web"
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="hero-article"
    ms.date="04/08/2016"
    ms.author="robmcm"/>

# Een WordPress-web-app maken in Azure App Service

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

In deze zelfstudie gaat u een WordPress-blogsite implementeren vanuit Azure Marketplace.

Wanneer u de zelfstudie hebt voltooid, hebt u uw eigen WordPress-site in de cloud.

![WordPress-site](./media/web-sites-php-web-site-gallery/wpdashboard.png)

U leert het volgende:

* Een toepassingssjabloon zoeken in Azure Marketplace.
* In Azure App Service een web-app maken die op de sjabloon is gebaseerd.
* Azure App Service-instellingen configureren voor de nieuwe web-app en database.

Azure Marketplace maakt een groot aantal populaire web-apps beschikbaar die zijn ontwikkeld door Microsoft, door derden of via OOS-initiatieven (Open Source Software). De web-apps zijn gebouwd op een breed scala aan populaire frameworks, zoals [PHP](/develop/nodejs/) in dit WordPress-voorbeeld, [.NET](/develop/net/), [Node.js](/develop/nodejs/), [Java](/develop/java/) en [Python](/develop/python/), om er enkele te noemen. De enige software die u nodig hebt om vanuit Azure Marketplace een web-app te maken, is de browser die u gebruikt voor de [Azure Portal](https://portal.azure.com/). 

Op de WordPress-site die u in deze zelfstudie gaat implementeren, wordt voor de database MySQL gebruikt. Als u in plaats daarvan voor de database SQL Database wilt gebruiken, raadpleegt u [Project Nami](http://projectnami.org/). **Project Nami** is ook beschikbaar via de Marketplace.

> [AZURE.NOTE]
> U hebt een Microsoft Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u [uw voordelen als Visual Studio-abonnee activeren](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) of [zich aanmelden voor een gratis proefversie](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).
>
> Als u met Azure App Service aan de slag wilt voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](http://go.microsoft.com/fwlink/?LinkId=523751). Daar kunt u direct een eenvoudige, tijdelijke web-app maken in App Service. U hebt hiervoor geen creditcard nodig en bent nergens toe verplicht.

## WordPress selecteren en configureren voor Azure App Service

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

2. Klik op **Nieuw**.
    
    ![Nieuwe maken][5]
    
3. Zoek naar **WordPress** en klik op **WordPress**. Als u in plaats van MySQL SQL Database wilt gebruiken, zoekt u naar **Project Nami**.

    ![WordPress via een lijst][7]
    
5. Lees de beschrijving van de WordPress-app en klik op **Maken**.

    ![Maken](./media/web-sites-php-web-site-gallery/create.png)

4. Typ in het vak **Web-app** een naam voor de web-app.

    Deze naam moet uniek zijn in het domein azurewebsites.net, omdat de URL van de web-app {naam}.azurewebsites.net wordt. Als de ingevoerde naam niet uniek is, ziet u een rood uitroepteken in het tekstvak.

8. Als u meerdere abonnementen hebt, kiest u het abonnement dat u wilt gebruiken. 

5. Selecteer een **Resourcegroep** of maak een nieuwe.

    Zie [De Azure Portal gebruiken om Azure-resources te beheren](../resource-group-portal.md) voor meer informatie over resourcegroepen.

5. Selecteer een **App Service-plan/-locatie** of maak een nieuw(e).

    Zie [Overzicht van Azure App Service-plannen](../azure-web-sites-web-hosting-plans-in-depth-overview.md) voor meer informatie over App Service-plannen 

7. Klik op **Database** en geef op de blade **Nieuwe MySQL-database** de vereiste waarden op om uw MySQL-database te configureren.

    a. Voer een nieuwe naam in of laat de standaardnaam staan.

    b. Laat **Databasetype** ingesteld op **Gedeeld**.

    c. Kies de locatie die u ook voor de web-app hebt gekozen.

    d. Kies een prijscategorie. Mercury (gratis met minimale verbindingen en schijfruimte) is voldoende voor deze zelfstudie.

8. Klik op de blade **Nieuwe MySQL-database** op **OK**. 

8. Accepteer de juridische bepalingen op de blade **WordPress** en klik op **Maken**. 

    ![Web-app configureren](./media/web-sites-php-web-site-gallery/configure.png)

    De web-app wordt gewoonlijk in nog geen minuut door Azure App Service gemaakt. U kunt de voortgang bekijken door boven aan de portalpagina op het belpictogram te klikken.

    ![Voortgangsindicator](./media/web-sites-php-web-site-gallery/progress.png)

## De WordPress-web-app starten en beheren
    
7. Wanneer de web-app is gemaakt, navigeert u in de Azure Portal naar de resourcegroep waarin u de toepassing hebt gemaakt. Hier ziet u de web-app en de database.

    De extra resource met het pictogram van een gloeilampje is [Application Insights](/services/application-insights/), een resource die bewakingsservices voor uw web-app biedt.

1. Klik op de blade **Resourcegroep** op de regel van de web-app.

    ![Web-app configureren](./media/web-sites-php-web-site-gallery/resourcegroup.png)

2. Klik op de blade Web-apps op **Bladeren**.

    ![site-URL][browse]

3. Voer op de **Welkomstpagina** van WordPress de configuratiegegevens in die WordPress nodig heeft. Klik vervolgens op **WordPress installeren**.

    ![WordPress configureren](./media/web-sites-php-web-site-gallery/wpconfigure.png)

4. Meld u aan met de referenties die u op de **Welkomstpagina** hebt gemaakt.  

5. De dashboardpagina van uw site wordt geopend.    

    ![WordPress-site](./media/web-sites-php-web-site-gallery/wpdashboard.png)

## Volgende stappen

U hebt gezien hoe u een PHP-web-app uit de galerie maakt en implementeert. In het [PHP-ontwikkelaarscentrum](/develop/php/) vindt u meer informatie over het gebruik van PHP in Azure.

Voor meer informatie over werken met App Service-web-apps klikt u op de koppelingen aan de linkerkant van de pagina (in brede browservensters) of boven aan de pagina (in smalle browservensters). 

## Wat is er gewijzigd
* Als u van Websites wilt overstappen op App Service, raadpleegt u de volgende handleiding: [Azure App Service en de invloed ervan op bestaande Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714).

[5]: ./media/web-sites-php-web-site-gallery/startmarketplace.png
[7]: ./media/web-sites-php-web-site-gallery/search-web-app.png
[bladeren]: ./media/web-sites-php-web-site-gallery/browse-web.png



<!--HONumber=Jun16_HO2-->


