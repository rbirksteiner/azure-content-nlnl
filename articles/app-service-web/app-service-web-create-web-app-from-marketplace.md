<properties
    pageTitle="Een web-app maken vanuit Azure Marketplace | Microsoft Azure"
    description="Lees hoe u vanuit Azure Marketplace een nieuwe WordPress-web-app maakt met behulp van de Azure Portal."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/10/2016"
    ms.author="robmcm"/>

<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

# Een web-app maken vanuit Azure Marketplace

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure Marketplace maakt een groot aantal populaire web-apps beschikbaar die zijn ontwikkeld door Microsoft, door derden of via OOS-initiatieven (Open Source Software). Bijvoorbeeld WordPress, Umbraco CMS, Drupal, enzovoort. Deze web-apps zijn gebouwd op een breed scala aan populaire frameworks, zoals [PHP] in dit WordPress-voorbeeld, [.NET], [Node.js], [Java] en [Python], om er enkele te noemen. De enige software die u nodig hebt om vanuit Azure Marketplace een web-app te maken, is de browser die u gebruikt voor de [Azure Portal].

In deze zelfstudie leert u het volgende:

* Een toepassingssjabloon zoeken in Azure Marketplace.
* In Azure App Service een web-app maken die op de sjabloon is gebaseerd.
* Azure App Service-instellingen configureren voor de nieuwe web-app en database.

In deze zelfstudie gaat u een WordPress-blogsite implementeren vanuit Azure Marketplace. Wanneer u de stappen in deze zelfstudie hebt uitgevoerd, hebt u uw eigen WordPress-site in werking in de cloud.

![Voorbeeld van dashboard voor WordPress-web-app][WordPressDashboard]

Op de WordPress-site die u in deze zelfstudie gaat implementeren, wordt voor de database MySQL gebruikt. Als u in plaats daarvan SQL Database voor de database wilt gebruiken, raadpleeg dan [Project Nami], dat eveneens beschikbaar is via Azure Marketplace.

> [AZURE.NOTE]
> U hebt een Microsoft Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u [uw voordelen als Visual Studio-abonnee activeren][activeren] of u [aanmelden voor een gratis proefversie][gratis proefversie].
>
> Als u met Azure App Service aan de slag wilt voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen]. Daar kunt u direct een tijdelijke en eenvoudige web-app maken in App Service. U hebt hiervoor geen creditcard nodig en u bent nergens toe verplicht.

## WordPress selecteren en configureren voor Azure App Service

1. Meld u aan bij de [Azure Portal].

1. Klik op **Nieuw**.
    
    ![Een nieuwe Azure-resource maken][MarketplaceStart]
    
1. Zoek naar **WordPress** en klik op **WordPress**. (Als u in plaats van MySQL SQL Database wilt gebruiken, zoekt u naar **Project Nami**.)

    ![WordPress zoeken in de Marketplace][MarketplaceSearch]
    
1. Lees de beschrijving van de WordPress-app en klik op **Maken**.

    ![Een WordPress-web-app maken][MarketplaceCreate]

1. De blade Instellingen van WordPress wordt weergegeven. Hiermee gaat u de volgende stappen uitvoeren:

    ![Instellingen voor de WordPress-web-app configureren][ConfigStart]

1. Typ in het vak **Web-app** een naam voor de web-app.

    Deze naam moet uniek zijn in het domein azurewebsites.net, omdat de URL van de web-app *{naam}*.azurewebsites.net wordt. Als de ingevoerde naam niet uniek is, ziet u een rood uitroepteken in het tekstvak.

    ![De naam van de WordPress-web-app configureren][ConfigAppName]

1. Als u meerdere abonnementen hebt, kiest u het abonnement dat u wilt gebruiken. 

    ![Het abonnement voor de web-app configureren][ConfigSubscription]

1. Selecteer een **Resourcegroep** of maak een nieuwe.

    Zie [De Azure Portal gebruiken om Azure-resources te beheren][ResourceGroups] voor meer informatie over resourcegroepen.

    ![De resourcegroep voor de web-app configureren][ConfigResourceGroup]

1. Selecteer een **App Service-plan/-locatie** of maak een nieuw(e).

    Zie [Overzicht van Azure App Service-plannen][AzureAppServicePlans] voor meer informatie over App Service-plannen. 

    ![Het serviceplan voor de web-app configureren][ConfigServicePlan]

1. Klik op **Database** en geef op de blade **Nieuwe MySQL-database** de vereiste waarden op om uw MySQL-database te configureren.

    a. Voer een nieuwe naam in of laat de standaardnaam staan.

    b. Laat **Databasetype** ingesteld op **Gedeeld**.

    c. Kies de locatie die u ook voor de web-app hebt gekozen.

    d. Kies een prijscategorie. **Mercury**: is gratis met minimale verbindingen en schijfruimte. Dit is voldoende voor deze zelfstudie.

    e. Accepteer de juridische bepalingen op de blade **Nieuwe MySQL-database** en klik op **OK**. 

    ![De database-instellingen voor de web-app configureren][ConfigDatabase]

1. Accepteer de juridische bepalingen op de blade **WordPress** en klik op **Maken**. 

    ![De instellingen voor de web-app voltooien en op OK klikken][ConfigFinished]

    De web-app wordt gewoonlijk in nog geen minuut door Azure App Service gemaakt. U kunt de voortgang bekijken door boven aan de portalpagina op het belpictogram te klikken.

    ![Voortgangsindicator][ConfigProgress]

## De WordPress-web-app starten en beheren
    
1. Wanneer de web-app is gemaakt, navigeert u in de Azure Portal naar de resourcegroep waarin u de toepassing hebt gemaakt. Hier ziet u de web-app en de database.

    De extra resource met het pictogram van een gloeilampje is [Application Insights][ApplicationInsights], een resource die bewakingsservices voor uw web-app biedt.

1. Klik op de blade **Resourcegroep** op de regel van de web-app.

    ![Uw WordPress-web-app selecteren][WordPressSelect]

1. Klik op de blade Web-apps op **Bladeren**.

    ![Naar de WordPress-web-app bladeren][WordPressBrowse]

1. Als u wordt gevraagd om de taal voor de WordPress-blog te selecteren, selecteert u de gewenste taal en klikt u op **Doorgaan**.

    ![De taal voor de WordPress-web-app configureren][WordPressLanguage]

1. Voer op de **Welkomstpagina** van WordPress de configuratiegegevens in die WordPress nodig heeft. Klik vervolgens op **WordPress installeren**.

    ![De instellingen voor de WordPress-web-app configureren][WordPressConfigure]

1. Meld u aan met de referenties die u op de **Welkomstpagina** hebt gemaakt.  

1. De dashboardpagina van de site wordt geopend met de informatie die u hebt opgegeven.    

    ![Het WordPress-dashboard weergeven][WordPressDashboard]

## Volgende stappen

In deze zelfstudie hebt u gezien hoe u een voorbeeld-web-app kunt maken en implementeren vanuit Azure Marketplace.

Voor meer informatie over werken met App Service-web-apps klikt u op de koppelingen aan de linkerkant van de pagina (in brede browservensters) of boven aan de pagina (in smalle browservensters).

Voor meer informatie over het ontwikkelen van WordPress-web-apps op Azure raadpleegt u [WordPress ontwikkelen op Azure App Service][WordPressOnAzure]. 

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activeren]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[gratis proefversie]: https://azure.microsoft.com/pricing/free-trial/
[App Service uitproberen]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../azure-portal/resource-group-portal.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Azure Portal]: https://portal.azure.com/
[Project Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png



<!--HONumber=Jun16_HO2-->


