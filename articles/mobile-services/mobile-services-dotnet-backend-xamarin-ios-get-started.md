<properties
    pageTitle="Aan de slag met Mobile Services voor Xamarin iOS-apps | Microsoft Azure"
    description="Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Services voor Xamarin iOS-ontwikkeling"
    services="mobile-services"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor="mollybos"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="03/16/2016"
    ms.author="donnam"/>

# <a name="getting-started"> </a>Aan de slag met Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Voor de corresponderende versie van dit onderwerp voor Mobile Apps raadpleegt u [Een Xamarin.iOS-app maken](../app-service-mobile/app-service-mobile-xamarin-ios-get-started.md).

Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een Xamarin iOS-app met Azure Mobile Services. In deze zelfstudie maakt u een nieuwe mobiele service en een eenvoudige _takenlijst_-app die app-gegevens opslaat in de nieuwe mobiele service. De mobiele service die u gaat maken, gebruikt de ondersteunde .NET-talen met behulp van Visual Studio voor bedrijfslogica op de server en voor het beheren van de mobiele service. Voor het maken van een mobiele service waarmee u uw bedrijfslogica op de server in JavaScript kunt schrijven, zie de [JavaScript back-endversie] van dit onderwerp.

>[AZURE.NOTE]In dit onderwerp wordt beschreven hoe u een nieuw mobiele-serviceproject maakt met behulp van de klassieke Azure Portal. Met behulp van Visual Studio 2013 Update 2 kunt u ook een nieuw mobiele-serviceproject toevoegen aan een bestaande Visual Studio-oplossing. Zie voor meer informatie [Snelstartgids: een mobiele service (.NET-back-end) toevoegen](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

Hieronder ziet u een schermafbeelding van de voltooide app:

![][0]


Het voltooien van deze zelfstudie is een vereiste voor alle andere Mobile Services-zelfstudies voor Xamarin iOS-apps.

>[AZURE.NOTE]U hebt een Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure. U krijgt dan maximaal 10 gratis mobiele services, die u ook na het einde van de proefperiode kunt blijven gebruiken. Zie <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started" target="_blank">Gratis proefversie van Azure</a> voor meer informatie.<br />Voor deze zelfstudie is <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> vereist. Er is een gratis proefversie beschikbaar.

## Een nieuwe mobiele service maken

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Een nieuwe app voor Xamarin iOS maken

Nadat u uw mobiele service hebt gemaakt, kunt u in de klassieke Azure Portal een eenvoudige Quick Start volgen om een nieuwe app te maken of een bestaande app zodanig te wijzigen dat deze verbinding maakt met uw mobiele service.

In deze sectie downloadt u een nieuwe Xamarin iOS-app en een serviceproject voor uw mobiele service.

1. Installeer Visual Studio met Xamarin, als u dat nog niet hebt gedaan. Instructies hiervoor vindt u in [Installatie voor Visual Studio en Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). U kunt Xamarin Studio ook gebruiken op een Mac OS X-machine. Zie [Installatie en verificaties voor Mac-gebruikers](https://msdn.microsoft.com/library/mt488770.aspx).

2. Klik in de [klassieke Azure Portal] op **Mobile Services** en klik vervolgens op de mobiele service die u zojuist hebt gemaakt.

3. Klik op het tabblad Quick Start op **Xamarin** onder **Platform kiezen** en vouw **Een nieuwe Xamarin-app maken** uit.

    ![][6]

    U ziet nu de drie eenvoudige stappen om een Xamarin iOS-app te maken die is verbonden met uw mobiele service.

    ![][7]

4. Selecteer onder **Uw service downloaden en publiceren naar de cloud**, **iOS** en klik op **Downloaden**.

    Hiermee downloadt u een oplossing die projecten bevat voor zowel de mobiele service als voor de _takenlijst_-voorbeeldtoepassing die is verbonden met uw mobiele service. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.

5. Download uw publicatieprofiel, sla het gedownloade bestand op uw lokale computer op en noteer de opslaglocatie.

## De mobiele service testen

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Uw mobiele service publiceren

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## De Xamarin iOS-app uitvoeren

De laatste fase van deze zelfstudie is het bouwen en uitvoeren van uw nieuwe app.

1. Navigeer naar het client-project binnen de mobiele-serviceoplossing in Visual Studio of in Xamarin Studio.

    ![][8]

    ![][9]

2. Druk op de knop **Uitvoeren** om het clientproject te bouwen en de app te starten in de iPhone-emulator.

3. Typ in de app een stukje zinvolle tekst, zoals _Voltooi de zelfstudie_ en klik vervolgens op het plusteken (**+**).

    ![][10]

    Er wordt nu een POST-aanvraag verzonden naar de nieuwe mobiele service die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de takentabel. De items die in de tabel zijn opgeslagen, worden geretourneerd door de mobiele service en de gegevens worden weergegeven in de lijst.

>[AZURE.NOTE]U kunt de code die toegang heeft tot uw mobiele service bekijken om gegevens op te vragen en in te voegen in het C#-bestand QSTodoService.cs.


## Volgende stappen
Nu u de Quick Start hebt voltooid, kunt u nagaan hoe u in Mobile Services ook diverse andere belangrijke taken uitvoert:

* [Aan de slag met het offline synchroniseren van gegevens]
  <br/>Ontdek hoe de offline gegevenssynchronisatie in de snelstartgids wordt gebruikt om uw app responsief en robuust te maken.

* [Aan de slag met verificatie]
  <br/>Ontdek hoe u gebruikers van uw app verifieert met een id-provider.

* [Aan de slag met pushmeldingen]
  <br/>Ontdek hoe u een zeer eenvoudige pushmelding verzendt naar uw app.

* [Problemen met een Mobile Services .NET-back-end oplossen]
  <br/> Ontdek hoe u problemen met een Mobile Services .NET-back-end opspoort en oplost.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Aan de slag met Mobile Services]:#getting-started
[Een nieuwe mobiele service maken]:#create-new-service
[Volgende stappen]:#next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Aan de slag met het offline synchroniseren van gegevens]: mobile-services-xamarin-ios-get-started-offline-data.md
[Aan de slag met verificatie]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Aan de slag met pushmeldingen]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript en HTML]: mobile-services-win8-javascript/
[klassieke Azure Portal]: https://manage.windowsazure.com/
[JavaScript back-endversie]: mobile-services-ios-get-started.md
[Problemen met een Mobile Services .NET-back-end oplossen]: mobile-services-dotnet-backend-how-to-troubleshoot.md


<!--HONumber=Jun16_HO2-->


