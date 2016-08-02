<properties
    pageTitle="Aan de slag met Azure Mobile Services voor iOS-apps | Back-end JavaScript"
    description="Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Services voor iOS-ontwikkeling."
    services="mobile-services"
    documentationCenter="ios"
    authors="krisragh"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="03/09/2016"
    ms.author="krisragh"/>

# <a name="getting-started"> </a>Aan de slag met Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Raadpleeg voor de equivalente Mobile Apps-versie van dit onderwerp raadpleegt u [Een iOS-app maken in Azure Mobile Apps](../app-service-mobile/app-service-mobile-ios-get-started.md).

Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een iOS-app met Azure Mobile Services.

In deze zelfstudie maakt u een nieuwe mobiele service en een eenvoudige _takenlijst_-app die app-gegevens opslaat in de nieuwe mobiele service. De mobiele service die u maakt, gebruikt JavaScript voor bedrijfslogica op de server. Zie voor informatie over het maken van een mobiele service met bedrijfslogica in .NET op de server de [.NET-back-endversie] van dit onderwerp.

> [AZURE.NOTE] U hebt een Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure en [gratis mobiele services krijgen die u ook na het einde van de proefperiode kunt blijven gebruiken](https://azure.microsoft.com/pricing/details/mobile-services/). Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20) voor meer informatie.

## <a name="create-new-service"> </a>Een nieuwe mobiele service maken

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Een nieuwe iOS-app maken

U kunt een eenvoudig Quick Start volgen in de klassieke Azure Portal voor het maken van een nieuwe app die is verbonden met uw mobiele service:

1. Klik in de [klassieke Azure Portal] op **Mobile Services** en klik vervolgens op de mobiele service die u zojuist hebt gemaakt.

2. Klik op het tabblad Quick Start op **iOS** onder **Een platform kiezen** en vouw **Een nieuwe iOS-app maken** uit. U ziet nu de stappen om een iOS-app te maken die is verbonden met uw mobiele service.

3. Klik op **Takentabel maken** om een tabel te maken voor het opslaan van app-gegevens.

4. Klik onder **Uw app downloaden en uitvoeren** op **Downloaden**. Hiermee downloadt u het project voor de _takenlijst_-voorbeeldtoepassing die is verbonden met uw mobiele service, samen met de Mobile Services iOS SDK. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.

## Uw nieuwe iOS-app uitvoeren

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

<ol start="4">
<li><p>Klik in de [klassieke Azure Portal] op het tabblad **Gegevens** en klik vervolgens op de tabel **Taken**. U kunt nu door de gegevens bladeren die door de app in de tabel zijn ingevoegd.<p></li></ol></p>

## <a name="next-steps"> </a>Volgende stappen
Ontdek hoe u aanvullende belangrijke taken uitvoert in Mobile Services:

* [Aan de slag met het offline synchroniseren van gegevens]
    <br/>Ontdek hoe u offline gegevenssynchronisatie kunt gebruiken om uw app responsief en robuust te maken.

* [Verificatie toevoegen aan een bestaande app]
    <br/>Ontdek hoe u gebruikers van uw app verifieert met een id-provider.

* [Pushmeldingen toevoegen aan een bestaande app]
    <br/>Ontdek hoe u een zeer eenvoudige pushmelding verzendt naar uw app.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]


<!-- Anchors. -->
[Aan de slag met Mobile Services]:#getting-started
[Een nieuwe mobiele service maken]:#create-new-service
[Het mobiele service-exemplaar definiëren]:#define-mobile-service-instance
[Volgende stappen]:#next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Aan de slag met het offline synchroniseren van gegevens]: mobile-services-ios-get-started-offline-data.md
[Verificatie toevoegen aan een bestaande app]: mobile-services-dotnet-backend-ios-get-started-users.md
[Pushmeldingen toevoegen aan een bestaande app]: mobile-services-dotnet-backend-ios-get-started-push.md


[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[klassieke Azure Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET-back-endversie]: mobile-services-dotnet-backend-ios-get-started.md



<!--HONumber=Jun16_HO2-->


