<properties
    pageTitle="Een Cordova-app maken in Azure App Service Mobile Apps| Microsoft Azure"
    description="Volg deze zelfstudie om aan de slag te gaan met back-ends voor mobiele apps van Azure voor Apache Cordova-ontwikkeling."
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""
    tags=""
    keywords="cordova,javascript,mobile,client" />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="05/02/2016"
    ms.author="glenga"/>

#Een Apache Cordova-app maken

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Overzicht

Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een mobiele Apache Cordova-app met een back-end voor mobiele apps van Azure.  U maakt zowel een nieuwe back-end voor een mobiele app als een eenvoudige Apache Cordova-app voor _takenlijsten_ die app-gegevens opslaat in Azure.

Het volgen van deze zelfstudie is een vereiste voor alle andere Apache Cordova-zelfstudies over het gebruik van de functie Mobile Apps in Azure App Service.

## Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een pc met [Visual Studio Community 2015] of hoger.
* [Visual Studio Tools for Apache Cordova].
* Een [actief Azure-account](https://azure.microsoft.com/pricing/free-trial/).

U kunt Visual Studio ook omzeilen en de Apache Cordova-opdrachtregel rechtstreeks gebruiken.  Dit is handig bij het voltooien van de zelfstudie op een Mac-computer.  Het compileren van Apache Cordova-clienttoepassingen via de opdrachtregel wordt niet behandeld in deze zelfstudie.

## Een nieuwe back-end voor mobiele apps van Azure maken

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Het serverproject configureren

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## De Apache Cordova-app downloaden en uitvoeren

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## Volgende stappen

Nu u deze zelfstudie hebt voltooid, gaat u verder met een van de volgende zelfstudies:

* [Verificatie toevoegen] aan uw Apache Cordova-app.
* [Pushmeldingen toevoegen] aan uw Apache Cordova-app.

Meer informatie over belangrijke concepten met Azure App Service.

* [Verificatie]
* [Pushmeldingen]

Informatie over het gebruik van de SDK's.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- Images. -->

<!-- URLs -->
[Azure Portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Verificatie toevoegen]: app-service-mobile-cordova-get-started-users.md
[Pushmeldingen toevoegen]: app-service-mobile-cordova-get-started-push.md
[Verificatie]: app-service-mobile-auth.md
[Pushmeldingen]: ../notification-hubs/notification-hubs-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md




<!--HONumber=Jun16_HO2-->


