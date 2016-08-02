
<properties
    pageTitle="Aan de slag met Azure Mobile Services voor Android-apps"
    description="Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Services voor Android-ontwikkeling."
    services="mobile-services"
    documentationCenter="android"
    authors="RickSaling"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="get-started-article"
    ms.date="03/05/2016"
    ms.author="ricksal"/>


# <a name="getting-started"> </a>Aan de slag met Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Voor de equivalente Mobile Apps-versie van dit onderwerp raadpleegt u [Een Android-app maken in Azure Mobile Apps](../app-service-mobile/app-service-mobile-android-get-started.md).

Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een Android-app met Azure Mobile Services. In deze zelfstudie maakt u een nieuwe mobiele service en een eenvoudige _takenlijst_-app die app-gegevens opslaat in de nieuwe mobiele service. De mobiele service die u gaat maken, gebruikt de ondersteunde .NET-talen met behulp van Visual Studio voor bedrijfslogica op de server en voor het beheren van de mobiele service. Voor het maken van een mobiele service waarmee u uw bedrijfslogica op de server in JavaScript kunt schrijven, zie de [JavaScript back-endversie](mobile-services-android-get-started.md) van dit onderwerp.

Hieronder ziet u een schermafbeelding van de voltooide app:

![](./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png)

Voltooiing van deze zelfstudie vereist de [Android-hulpprogramma's voor ontwikkelaars][Android Studio], waaronder de Integrated Development Environment van Android Studio, en het meest recente Android-platform. Android 4.2 of een latere versie is vereist.

Het gedownloade Quick Start-project bevat de Mobile Services SDK voor Android.

> [AZURE.IMPORTANT] U hebt een Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure. U krijgt dan maximaal 10 gratis mobiele services, die u ook na het einde van de proefperiode kunt blijven gebruiken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28) voor meer informatie.


## <a name="create-new-service"> </a>Een nieuwe mobiele service maken

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## De mobiele service op de lokale computer downloaden

Nadat u de mobiele service hebt gemaakt, downloadt u uw persoonlijke mobiele-serviceproject dat u op de lokale computer of virtuele machine kunt uitvoeren.

1. Klik op de mobiele service die u zojuist hebt gemaakt en klik vervolgens op het tabblad Quick Start op **Android** onder **Platform kiezen** en vouw **Een nieuwe Android-app maken** uit.

    ![][1]

2. Download en installeer [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) of een latere versie, als u dat nog niet hebt gedaan.

3. In stap 2 klikt u op **Downloaden** onder **Uw service downloaden en publiceren naar de cloud**.

    Hierdoor wordt het Visual Studio-project gedownload dat uw mobiele service implementeert. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.

## De mobiele service testen

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Uw mobiele service publiceren

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Een nieuwe app voor Android maken

In deze sectie maakt u een nieuwe Android-app die is verbonden met uw mobiele service.

1. Klik in de [klassieke Azure Portal] op **Mobile Services** en klik vervolgens op de mobiele service die u zojuist hebt gemaakt.

2. Klik op het tabblad Quick Start op **Android** onder **Platform kiezen** en vouw **Een nieuwe Android-app maken** uit.

    ![][2]

3. Als u dit nog niet hebt gedaan, kunt u de [Android-hulpprogramma's voor ontwikkelaars][Android SDK] downloaden en installeren op uw lokale computer of virtuele machine.

4. Klik onder **Uw app downloaden en uitvoeren** op **Downloaden**.

    Hiermee downloadt u het project voor de _takenlijst_-voorbeeldtoepassing die is verbonden met uw mobiele service. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.

## Uw Android-app uitvoeren

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]

## <a name="next-steps"> </a>Volgende stappen
Nu u de Quick Start hebt voltooid, kunt u nagaan hoe u in Mobile Services ook diverse andere belangrijke taken uitvoert:

* [Pushmeldingen toevoegen aan uw app]
  <br/>Ontdek hoe u een zeer eenvoudige pushmelding verzendt naar uw app.

* [Verificatie toevoegen aan uw app]
  <br/>Ontdek hoe u toegang tot uw back-endgegevens beperkt tot specifieke geregistreerde gebruikers van uw app.

* [Problemen met een Mobile Services .NET-back-end oplossen]
  <br/> Ontdek hoe u problemen met een Mobile Services .NET-back-end opspoort en oplost.

<!-- Anchors. -->
[Aan de slag met Mobile Services]:#getting-started
[Een nieuwe mobiele service maken]:#create-new-service
[Het mobiele service-exemplaar definiëren]:#define-mobile-service-instance
[Volgende stappen]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs-AS.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android-AS.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Aan de slag (Eclipse)]: mobile-services-dotnet-backend-android-get-started-ec.md
[Pushmeldingen toevoegen aan uw app]: mobile-services-dotnet-backend-android-get-started-push.md
[Verificatie toevoegen aan uw app]: mobile-services-dotnet-backend-android-get-started-auth.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Android SDK voor Mobile Services]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Problemen met een Mobile Services .NET-back-end oplossen]: mobile-services-dotnet-backend-how-to-troubleshoot.md

[klassieke Azure Portal]: https://manage.windowsazure.com/



<!--HONumber=Jun16_HO2-->


