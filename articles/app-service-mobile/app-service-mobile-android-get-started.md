<properties
    pageTitle="Een Android-app maken in Azure App Service Mobile Apps | Microsoft Azure"
    description="Volg deze zelfstudie om aan de slag te gaan met back-ends voor mobiele apps van Azure voor Android-ontwikkeling."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="05/03/2016"
    ms.author="yuaxu;ricksal"/>

#Een Android-app maken

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Overzicht

Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een mobiele Android-app met een back-end voor een mobiele apps van Azure.  U maakt zowel een nieuwe back-end voor een mobiele app als een eenvoudige Android-app voor _takenlijsten_ die app-gegevens opslaat in Azure.

Het volgen van deze zelfstudie is een vereiste voor alle andere Android-zelfstudies over het gebruik van de functie Mobile Apps in Azure App Service.

## Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* De [Android-hulpprogramma's voor ontwikkelaars](https://developer.android.com/sdk/index.html), waaronder Integrated Development Environment van Android Studio en het meest recente Android-platform.
* Azure Mobile Android SDK, waarnaar automatisch wordt verwezen als onderdeel van het snelstartproject dat u hebt gedownload.
* Een pc met [Visual Studio Community 2013] of hoger&mdash; is niet vereist voor een back-end voor Node.js.
* Een [actief Azure-account](https://azure.microsoft.com/pricing/free-trial/).

## Een nieuwe back-end voor mobiele apps van Azure maken

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Het serverproject configureren

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## De Android-app downloaden en uitvoeren

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure Portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203



<!--HONumber=Jun16_HO2-->


