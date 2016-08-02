<properties
    pageTitle="Aan de slag met Mobile Services voor Xamarin iOS-apps | Microsoft Azure"
    description="Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Services voor Xamarin iOS-ontwikkeling."
    services="mobile-services"
    documentationCenter="xamarin"
    authors="conceptdev"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="03/16/2016"
    ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"> </a>Aan de slag met Mobile Services
[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Voor de corresponderende versie van dit onderwerp voor Mobile Apps raadpleegt u [Een Xamarin.iOS-app maken](../app-service-mobile/app-service-mobile-xamarin-ios-get-started.md).

Deze zelfstudie laat zien hoe u met Azure Mobile Services een cloudgebaseerde back-endservice toevoegt aan een Xamarin.iOS-app. In deze zelfstudie maakt u een nieuwe mobiele service en een eenvoudige *takenlijst*-app die app-gegevens opslaat in de nieuwe mobiele service.

Als u liever een video bekijkt, gebruik dan onderstaande clip. Deze toont dezelfde stappen als deze zelfstudie.

Video: 'Aan de slag met Xamarin en Azure Mobile Services' met Craig Dunn, ontwikkelaar voor Xamarin (duur: 10:05 min.)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Hieronder ziet u een schermafbeelding van de voltooide app:

![][0]

Voltooiing van deze zelfstudie vereist XCode en Xamarin Studio voor OS X of Visual Studio in Windows met een Mac in een netwerk. Volledige installatie-instructies vindt u in [Installatie voor Visual Studio en Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 

> [AZURE.IMPORTANT] U hebt een Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure. U krijgt dan maximaal 10 gratis mobiele services, die u ook na het einde van de proefperiode kunt blijven gebruiken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

## <a name="create-new-service"> </a>Een nieuwe mobiele service maken

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Een nieuwe app voor Xamarin.iOS maken

Nadat u uw mobiele service hebt gemaakt, kunt u in de klassieke Azure Portal een eenvoudige Quick Start volgen om een nieuwe app te maken of een bestaande app zodanig te wijzigen dat deze verbinding maakt met uw mobiele service.

In deze sectie maakt u een nieuwe Xamarin.iOS-app die is verbonden met uw mobiele service.

1.  Klik in de [klassieke Azure Portal] op **Mobile Services** en klik vervolgens op de mobiele service die u zojuist hebt gemaakt.

2. Klik op het tabblad Quick Start op **Xamarin.iOS** onder **Platform kiezen** en vouw **Een nieuwe app voor Xamarin.iOS maken** uit.

    ![][6]

    U ziet nu de drie eenvoudige stappen om een Xamarin.iOS-app te maken die is verbonden met uw mobiele service.

    ![][7]

3. Download en installeer Xcode (we raden u aan de nieuwste versie, Xcode 6.0 of hoger, te gebruiken) en [Xamarin Studio], als u dit nog niet hebt gedaan.

4. Klik op **Takentabel maken** om een tabel te maken voor het opslaan van app-gegevens.

5. Klik onder **App downloaden en uitvoeren** op **Downloaden**.

    Hiermee downloadt u het project voor de _takenlijst_-voorbeeldtoepassing die is verbonden met uw mobiele service en verwijst naar het Azure Mobile Service-onderdeel voor Xamarin.iOS. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.

## Uw nieuwe app voor Xamarin.iOS uitvoeren

De laatste fase van deze zelfstudie is het bouwen en uitvoeren van uw nieuwe app.

1. Blader naar de locatie waar u de gecomprimeerde projectbestanden hebt opgeslagen, vouw de bestanden op uw computer uit en open het oplossingsbestand **XamarinTodoQuickStart.iOS.sln** met Xamarin Studio of Visual Studio.

    ![][8]

    ![][9]

2. Druk op de knop **Uitvoeren** om het project te bouwen en de app te starten in de iPhone-emulator. Dit is de standaardoptie voor dit project.

3. Typ in de app een stukje zinvolle tekst, zoals _Voltooi de zelfstudie_ en klik vervolgens op het plusteken (**+**).

    ![][10]

    Er wordt nu een POST-aanvraag verzonden naar de nieuwe mobiele service die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de takentabel. De items die in de tabel zijn opgeslagen, worden geretourneerd door de mobiele service en de gegevens worden weergegeven in de lijst.

    > [AZURE.NOTE] U kunt de code die toegang heeft tot uw mobiele service, bekijken om gegevens op te vragen en in te voegen. Deze code vindt u in het C#-bestand TodoService.cs.

4. Klik in de [klassieke Azure Portal] op het tabblad **Gegevens** en klik vervolgens op de tabel **Taken**.

    ![][11]

    U kunt nu door de gegevens bladeren die door de app in de tabel zijn ingevoegd.

    ![][12]


## Volgende stappen
Nu u de Quick Start hebt voltooid, kunt u nagaan hoe u in Mobile Services ook diverse andere belangrijke taken uitvoert:

* [Aan de slag met het offline synchroniseren van gegevens] Ontdek hoe in de Quick Start de offline gegevenssynchronisatie wordt gebruikt om uw app responsief en robuust te maken.

* [Aan de slag met verificatie] Ontdek hoe u gebruikers van uw app verifieert met een id-provider.

* [Aan de slag met pushmeldingen] Ontdek hoe u een zeer eenvoudige pushmelding verzendt naar uw app.




[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Aan de slag met Mobile Services]:#getting-started
[Een nieuwe mobiele service maken]:#create-new-service
[Het mobiele service-exemplaar definiëren]:#define-mobile-service-instance
[Volgende stappen]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Aan de slag met het offline synchroniseren van gegevens]: mobile-services-xamarin-ios-get-started-offline-data.md
[Aan de slag met verificatie]: partner-xamarin-mobile-services-ios-get-started-users.md
[Aan de slag met pushmeldingen]: partner-xamarin-mobile-services-ios-get-started-push.md

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[klassieke Azure Portal]: https://manage.windowsazure.com/



<!--HONumber=Jun16_HO2-->


