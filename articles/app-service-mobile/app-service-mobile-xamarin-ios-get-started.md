<properties
    pageTitle="Aan de slag met Azure App Service Mobile Apps voor Xamarin iOS-apps | Microsoft Azure"
    description="Volg deze zelfstudie om aan de slag te gaan met Mobile Apps voor Xamarin.iOS-ontwikkeling"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="02/13/2016"
    ms.author="normesta"/>


#Een Xamarin.iOS-app maken

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Overzicht

Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een mobiele Xamarin.iOS-app met een back-end voor mobiele apps van Azure.  U maakt zowel een nieuwe back-end voor mobiele apps als een eenvoudige Xamarin.iOS-app voor _takenlijsten_ die app-gegevens opslaat in Azure.

Het volgen van deze zelfstudie is een vereiste voor alle andere Xamarin.iOS- zelfstudies over het gebruik van de Mobile Apps-functie in Azure App Service.

##Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure en maximaal 10 gratis mobiele apps krijgen die u ook na de proefperiode kunt blijven gebruiken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

* Visual Studio met Xamarin. Zie [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (Installeren en instellen voor Visual Studio en Xamarin) voor instructies. 

* Een Mac met Xcode v7.0 of hoger en waarop Xamarin Studio Community is geïnstalleerd. Zie [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (Installeren en instellen voor Visual Studio en Xamarin) en [Setup, install, and verifications for Mac users](https://msdn.microsoft.com/library/mt488770.aspx) (Instructies voor installatie, configuratie en verificatie voor Mac-gebruikers) (MSDN).

>[AZURE.NOTE] Als u met Azure App Service aan de slag wilt voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](https://tryappservice.azure.com/?appServiceName=mobile). Daar kunt u direct een tijdelijke en eenvoudige mobiele app maken in App Service: u hebt geen creditcard nodig en u doet geen toezeggingen.

## Een nieuwe back-end voor Azure Mobile Apps maken

Volg deze stappen voor het maken van een nieuwe back-end voor Mobile Apps.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Het serverproject configureren

U hebt nu een back-end voor Azure Mobile Apps ingericht, die kan worden gebruikt door uw mobiele clienttoepassingen. Nu gaat u een serverproject downloaden voor een eenvoudige back-end voor takenlijsten en deze publiceren naar Azure.

Volg onderstaande stappen voor het configureren van het serverproject voor het gebruik van de Node.js- of .NET-back-end.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]


## (Optioneel) Uw back-endproject lokaal testen

Als u hierboven een .NET-back-endconfiguratie hebt gekozen, kunt u eventueel de back-end lokaal testen.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]



## De Xamarin iOS-app downloaden en uitvoeren

1. Open de [Azure Portal] in een browservenster.

2. Klik op de blade Instellingen voor uw Mobile App op **Aan de slag** > **Xamarin.iOS**. Klik in stap 3 op  **Een nieuwe app maken** als deze optie nog niet is geselecteerd.  Klik vervolgens op de knop **Downloaden**.

    Er wordt een project gedownload dat een clienttoepassing bevat die is verbonden met uw mobiele app. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.

3. Pak het project uit dat u hebt gedownload en open het in Xamarin Studio (of Visual Studio).

    ![][9]

    ![][8]

4. Druk op F5 om het project te bouwen en de app te starten in de iPhone-emulator.

5. Typ in de app zinvolle tekst, zoals _Xamarin leren kennen_, en klik vervolgens op de knop **+**.

    ![][10]

    Hierdoor wordt een POST-aanvraag verzonden naar de nieuwe back-end voor mobiele apps die wordt gehost in Azure. Gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor mobiele apps en de gegevens worden weergegeven in de lijst.

>[AZURE.NOTE]U kunt de code die toegang geeft tot uw back-end voor mobiele apps, controleren om gegevens in het QSTodoService.cs C#-bestand op te vragen en in te voegen.

##Volgende stappen

* [Verificatie toevoegen aan uw app ](app-service-mobile-xamarin-ios-get-started-users.md)
  <br/>Informatie over het verifiëren van gebruikers van uw app met een id-provider.

* [Pushmeldingen toevoegen aan uw app](app-service-mobile-xamarin-ios-get-started-push.md)
  <br/>Ontdek hoe u een zeer eenvoudige pushmelding verzendt naar uw app.

<!-- Anchors. -->
[Aan de slag met back-ends voor mobiele apps]:#getting-started
[Een nieuwe back-end voor mobiele apps maken]:#create-new-service
[Volgende stappen]:#next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/



<!--HONumber=Jun16_HO2-->


