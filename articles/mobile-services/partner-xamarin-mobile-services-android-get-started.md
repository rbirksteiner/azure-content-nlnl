<properties
    pageTitle="Aan de slag met Mobile Services voor Xamarin.Android | Microsoft Azure"
    writer="craigd"
    description="Informatie over het gebruik van Azure Mobile Services met uw Xamarin.Android-app."
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="02/10/2016"
    ms.author="donnam"/>

# <a name="getting-started"></a>Aan de slag met Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Voor de corresponderende versie van dit onderwerp voor Mobile Apps raadpleegt u [Een Xamarin.Android-app maken](../app-service-mobile/app-service-mobile-xamarin-android-get-started.md).

Deze zelfstudie laat zien hoe u met Azure Mobile Services een cloudgebaseerde back-endservice toevoegt aan een Xamarin.Android-app. In deze zelfstudie maakt u een nieuwe mobiele service en een eenvoudige *takenlijst*-app die app-gegevens opslaat in de nieuwe mobiele service.

Als u liever een video bekijkt, gebruik dan onderstaande clip. Deze toont dezelfde stappen als deze zelfstudie.

Video: 'Aan de slag met Xamarin en Azure Mobile Services' met Craig Dunn, ontwikkelaar voor Xamarin (duur: 10:05 min.)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Hieronder ziet u een schermafbeelding van de voltooide app:

![][0]

Voltooiing van deze zelfstudie vereist XCode en Xamarin Studio voor OS X of Visual Studio in Windows met een Mac in een netwerk. Volledige installatie-instructies vindt u in [Installatie voor Visual Studio en Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 

Het gedownloade Quick Start-project bevat het Azure Mobile Services-onderdeel voor Xamarin.Android. Hoewel de Mobile Services SDK overweg kan met Android 2.2 of hoger, is dit project gericht op Android 4.2 of een latere versie.

> [AZURE.IMPORTANT] U hebt een Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure. U krijgt dan maximaal 10 gratis mobiele services, die u ook na het einde van de proefperiode kunt blijven gebruiken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5) voor meer informatie.

## <a name="create-new-service"> </a>Een nieuwe mobiele service maken

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Een nieuwe app voor Xamarin.Android maken

Nadat u uw mobiele service hebt gemaakt, kunt u in de klassieke Azure Portal een eenvoudige Quick Start volgen om een nieuwe app te maken of een bestaande app zodanig te wijzigen dat deze verbinding maakt met uw mobiele service.

In deze sectie maakt u een nieuwe Xamarin.Android-app die is verbonden met uw mobiele service.

1.  Klik in de [klassieke Azure Portal] op **Mobile Services** en klik vervolgens op de mobiele service die u zojuist hebt gemaakt.

2. Klik op het tabblad Quick Start op **Xamarin.Android** onder **Platform kiezen** en vouw **Een nieuwe Android-app maken** uit.

    ![][6]

    U ziet nu de drie eenvoudige stappen om een Xamarin.Android-app te maken die is verbonden met uw mobiele service.

    ![][7]

3. Klik op **Takentabel maken** om een tabel te maken voor het opslaan van app-gegevens.

4. Klik onder **App downloaden en uitvoeren** op **Downloaden**.

    Hiermee downloadt u het project voor de _takenlijst_-voorbeeldtoepassing die is verbonden met uw mobiele service. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.

## Uw Android-app uitvoeren

De laatste fase van deze zelfstudie is het bouwen en uitvoeren van uw nieuwe app.

1. Blader naar de locatie waar u de gecomprimeerde projectbestanden hebt opgeslagen en vouw de bestanden op uw computer uit.

2. Klik in Xamarin Studio of Visual Studio op **Bestand** en vervolgens op **Openen**. Navigeer naar de niet-gecomprimeerde voorbeeldbestanden en selecteer **XamarinTodoQuickStart.Android.sln** om dit bestand te openen.

3. Druk op de knop **Uitvoeren** om het project te bouwen en de app te starten. U wordt gevraagd een emulator of een aangesloten USB-apparaat te selecteren.

    > [AZURE.NOTE] Als u het project in de Android-emulator wilt uitvoeren, moet u minimaal één virtueel Android-apparaat (AVD) definiëren. Gebruik AVD Manager om deze apparaten te maken en te beheren.

4. Typ een stukje zinvolle tekst in de app, zoals _Voltooi de zelfstudie_, en klik vervolgens op **Toevoegen**.

    ![][10]

    Er wordt nu een POST-aanvraag verzonden naar de nieuwe mobiele service die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de takentabel. De items die in de tabel zijn opgeslagen, worden geretourneerd door de mobiele service en de gegevens worden weergegeven in de lijst.

    > [AZURE.NOTE] U kunt de code die toegang heeft tot uw mobiele service, bekijken om gegevens op te vragen en in te voegen. Deze code vindt u in het C#-bestand ToDoActivity.cs.

6. Klik in de [klassieke Azure Portal] op het tabblad **Gegevens** en klik vervolgens op de tabel **Taken**.

    ![][11]

    U kunt nu door de gegevens bladeren die door de app in de tabel zijn ingevoegd.

    ![][12]

## <a name="next-steps"> </a>Volgende stappen
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
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Aan de slag met gegevens]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Aan de slag met het offline synchroniseren van gegevens]: mobile-services-xamarin-android-get-started-offline-data.md
[Aan de slag met verificatie]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Aan de slag met pushmeldingen]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[Android SDK voor Mobile Services]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure]: http://azure.microsoft.com/
[klassieke Azure Portal]: https://manage.windowsazure.com/




<!--HONumber=Jun16_HO2-->


