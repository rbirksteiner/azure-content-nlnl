<properties
    pageTitle="Aan de slag met Azure Mobile Services voor Android-apps (back-end JavaScript)"
    description="Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Services voor Android-ontwikkeling (back-end JavaScript)."
    services="mobile-services"
    documentationCenter="android"
    authors="RickSaling"
    manager="reikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="04/08/2016"
    ms.author="ricksal"/>

# Aan de slag met Mobile Services voor Android (back-end JavaScript)

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Raadpleeg voor de equivalente mobiele apps-versie van dit onderwerp raadpleegt u [Een Android-app maken in mobiele apps van Azure](../app-service-mobile/app-service-mobile-android-get-started.md).

Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een Android-app met Azure Mobile Services. In deze zelfstudie maakt u een nieuwe mobiele service en een eenvoudige **takenlijst**-app die appgegevens opslaat in de nieuwe mobiele service.

> [AZURE.VIDEO mobile-get-started-android]

Hieronder staat een schermafbeelding van de voltooide app:

![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

## Vereisten

Voltooiing van deze zelfstudie vereist de [Android-hulpprogramma's voor ontwikkelaars](https://developer.android.com/sdk/index.html), waaronder de Integrated Development Environment van Android Studio en het meest recente Android-platform. Android 4.2 of een latere versie is vereist.

Het gedownloade Quick Start-project bevat de Azure Mobile Services SDK voor Android.

> [AZURE.IMPORTANT] U hebt een Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28) voor meer informatie.


## Een nieuwe mobiele service maken

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Een nieuwe app voor Android maken

Als u uw mobiele service hebt gemaakt, kunt u een eenvoudige Quick Start in de klassieke Azure Portal volgen om een nieuwe app te maken of een bestaande app wijzigen om verbinding te maken met uw mobiele service.

In deze sectie maakt u een nieuwe Android-app die is verbonden met uw mobiele service.

1.  Klik in de klassieke Azure Portal op **Mobile Services** en klik vervolgens op de mobiele service die u zojuist hebt gemaakt.

2. Klik in het tabblad Quick Start op **Android** onder **Platform kiezen** en vouw **Een nieuwe Android-app maken** uit.

    ![](./media/mobile-services-android-get-started/mobile-portal-quickstart-android1.png)

    U ziet nu de drie eenvoudige stappen om een Android-app te maken die is verbonden met uw mobiele service.

    ![](./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png)

3. Als u dit nog niet hebt gedaan, kunt u de [Android-hulpprogramma's voor ontwikkelaars](https://go.microsoft.com/fwLink/p/?LinkID=280125) downloaden en installeren op uw lokale computer of virtuele machine.

4. Klik op **Takentabel maken** om een tabel te maken voor het opslaan van app-gegevens.


5. Download nu uw app door op de knop **Downloaden** te klikken.

## Voer uw Android-app uit

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]


## <a name="next-steps"> </a>Volgende stappen
Nu u de Quick Start hebt voltooid, kunt u ontdekken hoe u aanvullende belangrijke taken uitvoert in Mobile Services:

* [Aan de slag met gegevens]
  <br/>Ontdek hoe u gegevens opslaat en gegevensquery‘s maakt met behulp van Mobile Services.

* [Aan de slag met authenticatie]
  <br/>Ontdek hoe u gebruikers van uw app authenticeert met een id-provider.

* [Aan de slag met pushmeldingen]
  <br/>Ontdek hoe u een zeer eenvoudige pushmelding verzendt naar uw app.


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]


<!-- URLs. -->
[Aan de slag (Eclipse)]: mobile-services-android-get-started-ec.md
[Aan de slag met gegevens]: mobile-services-android-get-started-data.md
[Aan de slag met authenticatie]: mobile-services-android-get-started-users.md
[Aan de slag met pushmeldingen]: mobile-services-javascript-backend-android-get-started-push.md
[Android SDK Mobile Services]: https://go.microsoft.com/fwLink/p/?LinkID=266533




<!--HONumber=Jun16_HO2-->


