<properties 
    pageTitle="Inleiding tot API Apps | Microsoft Azure" 
    description="Lees hoe Azure App Service u helpt bij het ontwikkelen, hosten en gebruiken van RESTful-API's." 
    services="app-service\api" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/03/2016" 
    ms.author="tdykstra"/>

# Overzicht van API Apps

API-apps in Azure App Service bevatten functies waarmee API's eenvoudiger kunnen worden ontwikkeld, gehost en gebruikt in de cloud en on-premises. Met API-apps beschikt u over hoogwaardige beveiliging, eenvoudig toegangsbeheer, hybride verbindingen, automatisch genereren van SDK's en naadloze integratie met [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md).

[De Azure App Service](../app-service/app-service-value-prop-what-is.md) is een volledig beheerd platform voor web- en integratiescenario's en mobiele scenario's. API Apps zijn een van de vier typen apps die worden aangeboden door [Azure App Service](../app-service/app-service-value-prop-what-is.md).

![App-typen in Azure App Service](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## Waarom API Apps gebruiken?

Hierna ziet u een aantal belangrijke voordelen van API Apps:

- **Laat uw bestaande API ongewijzigd**: u hoeft de code in de bestaande API's niet te wijzigen om gebruik te kunnen maken van API Apps, u hoeft de code alleen te implementeren naar een API-app. Voor de API kan elke taal of elk framework worden gebruikt dat wordt ondersteund door App Service, waaronder ASP.NET en C#, Java, PHP, Node.js en Python.

- **Eenvoudig gebruik**: door de geïntegreerde ondersteuning voor [Swagger API-metagegevens](http://swagger.io/) kunnen uw API's eenvoudig worden gebruikt door diverse clients.  Genereer clientcode automatisch voor uw API's in diverse talen, waaronder C#, Java, en Javascript. Configureer [CORS](app-service-api-cors-consume-javascript.md) eenvoudig, zonder uw code te wijzigen. Zie [API Apps-metagegevens van App Service voor het detecteren en genereren van code](app-service-api-metadata.md) en [Een API-app van JavaScript gebruiken met CORS](app-service-api-cors-consume-javascript.md) voor meer informatie. 

- **Eenvoudig toegangsbeheer**: beveilig een API-app tegen niet-geverifieerde toegang zonder uw code te wijzigen. Met ingebouwde verificatieservices worden API's beveiligd tegen toegang door andere services of door clients waarmee gebruikers worden aangegeven. Ondersteunde identiteitsproviders zijn onder andere Azure Active Directory, Facebook, Twitter, Google en Microsoft-account. Clients kunnen Active Directory Authentication Library (ADAL) of de Mobile Apps SDK gebruiken. Zie [Verificatie en autorisatie voor API Apps in Azure App Service](app-service-api-authentication.md) voor meer informatie.

- **Visual Studio-integratie**: specifieke hulpprogramma's in Visual Studio stroomlijnen het maken, implementeren, gebruiken en beheren van apps en het opsporen van fouten. Zie [Aankondiging van Azure SDK 2.8.1 voor .NET](/blog/announcing-azure-sdk-2-8-1-for-net/) voor meer informatie.

- **Integratie met Logic Apps**: API-apps die u maakt, kunnen worden gebruikt door [Logic Apps van App Service](../app-service-logic/app-service-logic-what-are-logic-apps.md).  Zie [Uw aangepaste API gebruiken die op App Service wordt gehost met logische apps](../app-service-logic/app-service-logic-custom-hosted-api.md) en [Nieuwe previewschemaversie 2015-08-01](../app-service-logic/app-service-logic-schema-2015-08-01.md) voor meer informatie.

Daarnaast kunt u met een API-app gebruikmaken van functies die worden aangeboden door [Web Apps](../app-service-web/app-service-web-overview.md) en [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md). Het omgekeerde geldt ook: als u een web-app of mobiele app gebruikt om een API te hosten, kan deze app gebruikmaken van functies van API Apps, zoals Swagger-metagegevens voor het genereren van clientcode en CORS voor browsertoegang in meerdere domeinen. Het enige verschil tussen de drie app-typen (API, web, mobiel) is de naam en het pictogram van deze apps in de Azure-portal.

## Wat is het verschil tussen API Apps en Azure API Management?

API Apps en [Azure API Management](../api-management/api-management-key-concepts.md) zijn aanvullende services:

* API Management is bedoeld voor het beheer van API's. U gebruikt een API Management-front-end voor een API om het gebruik te controleren en te beperken, invoer en uitvoer te manipuleren, verschillende API's samen te voegen in één eindpunt enzovoort. De API's die worden beheerd, kunnen overal worden gehost.
* API Apps is bedoeld om API's te hosten. De service bevat functies waarmee u eenvoudiger API's kunt ontwikkelen en gebruiken, maar deze bevat niet de functies voor controleren, beperken, bewerken en samenvoegen van API Management. Als u geen API Management-functies nodig hebt, kunt u API's hosten in API-apps zonder hiervoor API Management te gebruiken.

Hier ziet u een diagram waarin API Management wordt gebruikt voor API's die in API-apps en op andere locaties worden gehost.

![Azure API Management en API Apps](./media/app-service-api-apps-why-best-platform/apia-apim.png)

Sommige voorzieningen van API Management en API Apps hebben soortgelijke functies.  Met beide kan bijvoorbeeld CORS-ondersteuning worden geautomatiseerd. Wanneer u de twee services samen gebruikt, gebruikt u API Management voor CORS omdat deze fungeert als front-end voor de API-apps. 

## Aan de slag

Zie de zelfstudie voor het gewenste framework om aan de slag te gaan met API Apps door voorbeeldcode te implementeren in een app:

* [ASP.NET](app-service-api-dotnet-get-started.md) 
* [Node.js](app-service-api-nodejs-api-app.md) 
* [Java](app-service-api-java-api-app.md) 

Begin een thread in het [API Apps-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps) als u vragen hebt over API-apps. 



<!--HONumber=Jun16_HO2-->


