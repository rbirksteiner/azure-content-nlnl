<properties
    pageTitle="Aan de slag met Azure Mobile Engagement voor universele Windows-apps"
    description="Informatie over het gebruik van Azure Mobile Engagement met analyses en pushmeldingen voor universele Windows-apps."
    services="mobile-engagement"
    documentationCenter="windows"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/03/2016"
    ms.author="piyushjo" />

# Aan de slag met Azure Mobile Engagement voor universele Windows-apps

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In dit onderwerp leest u hoe u Azure Mobile Engagement gebruikt om inzicht te krijgen in het gebruik van uw apps, en om pushmeldingen te verzenden aan gesegmenteerde gebruikers van een universele Windows-toepassing.
Deze zelfstudie laat een eenvoudig broadcast-scenario met Mobile Engagement zien. In deze zelfstudie maakt u een lege universele Windows-app die basisgegevens verzamelt en pushmeldingen ontvangt via Windows Notification Service (WNS).

Voor deze zelfstudie hebt u het volgende nodig:

+ Visual Studio 2013
+ NuGet-pakket van [MicrosoftAzure.MobileEngagement]

> [AZURE.NOTE] U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-store-dotnet-get-started) voor meer informatie.

##<a id="setup-azme"></a>Mobile Engagement instellen voor uw universele Windows-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Uw app verbinden met de back-end van Mobile Engagement

Deze zelfstudie toont een ‘basisintegratie’, de minimale set die vereist is voor het verzamelen van gegevens en verzenden van een pushmelding. De volledige integratiedocumentatie is te vinden in de [Mobile Engagement universele Windows SDK-integratie](mobile-engagement-windows-store-sdk-overview.md).

We gaan een eenvoudige app maken met Visual Studio ter illustratie van de integratie.

###Een nieuw project voor een universele Windows-app maken

In de volgende stappen wordt ervan uitgegaan dat u Visual Studio 2015 gebruikt, hoewel de stappen hetzelfde zijn in eerdere versies van Visual Studio. 

1. Start Visual Studio en selecteer **New Project** in het scherm **Home**.

2. Selecteer in het pop-upvenster **Windows 8** -> **Universal** -> **Blank App (Universal Windows 8.1)**. Vul de velden **Name** en **Solution name** in voor de app en klik vervolgens op **OK**.

    ![][1]

U hebt nu een nieuwe universele Windows-app gemaakt waarin de Azure Mobile Engagement SDK is geïntegreerd.

###Uw app verbinden met de back-end van Mobile Engagement

1. Installeer het NuGet-pakket van [MicrosoftAzure.MobileEngagement] in het project. Als u ontwikkelt voor zowel Windows als Windows Phone, moet u dit doen voor beide projecten. Voor Windows 8.x en Windows Phone 8.1 plaatst hetzelfde NuGet-pakket de juiste platform-specifieke binaire bestanden in elk project.

2. Open **Package.appxmanifest** en zorg dat de volgende mogelijkheid is toegevoegd:

        Internet (Client)

    ![][2]

3. Plak nu de verbindingsreeks die u eerder hebt gekopieerd voor uw Mobile Engagement-app in het bestand `Resources\EngagementConfiguration.xml`, tussen de tags `<connectionString>` en `</connectionString>`:

    ![][3]

    >[AZURE.TIP] Als u een app maakt voor zowel Windows als Windows Phone, moet u nog steeds twee Mobile Engagement-toepassingen maken, een voor elk ondersteund platform. Dit zorgt ervoor dat uw doelgroep correct wordt gesegmenteerd en dat u de juiste meldingen naar ieder platform kunt sturen.

4. In het bestand `App.xaml.cs`:

    a. Voeg de instructie `using` toe:

            using Microsoft.Azure.Engagement;

    b. Voeg een specifieke methode toe voor de initialisatie en instelling van Engagement:

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    c. Initialiseer de SDK in de methode **OnLaunched**:

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    c. Voeg het volgende toe in de methode **OnActivated** en voeg de methode toe als die nog niet aanwezig is:

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

##<a id="monitor"></a>Realtime-bewaking inschakelen

U dient ten minste één scherm (activiteit) naar de back-end van Mobile Engagement te sturen om te beginnen met het verzenden van gegevens en ervoor te zorgen dat de gebruikers actief zijn.

1.  Voeg in **MainPage.xaml.cs** de volgende instructie `using` toe:

        using Microsoft.Azure.Engagement.Overlay;

2. Verander de basisklasse van **MainPage** van **Page** in **EngagementPageOverlay**:

        class MainPage : EngagementPageOverlay

3. In het bestand `MainPage.xaml`:

    a. Toevoegen aan de naamruimtedeclaraties:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    b. Vervang de **Page** in de XML-tagnaam met **engagement:EngagementPageOverlay**.
    
> [AZURE.IMPORTANT] Als uw pagina de methode `OnNavigatedTo` overschrijft, roept u `base.OnNavigatedTo(e)` aan. Anders wordt de activiteit niet gerapporteerd (de `EngagementPage` roept `StartActivity` aan binnen de methode `OnNavigatedTo`). Dit is vooral belangrijk in een Windows Phone-project waarbij de standaardsjabloon een methode `OnNavigatedTo` heeft. 

##<a id="monitor"></a>App verbinden met realtime-bewaking

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Pushmeldingen en in-app-berichten inschakelen

Met Mobile Engagement kunt u communiceren met uw gebruikers en ze bereiken met pushmeldingen en in-app-berichten in de context van campagnes. Deze module heet REACH in de Mobile Engagement-portal.
In de volgende secties stelt u de app in om die te ontvangen.

###Ontvangen van WNS-pushmeldingen inschakelen voor de app

1. In het bestand `Package.appxmanifest` stelt u op het tabblad **Application** onder **Notifications** de optie **Toast capable:** in op **Yes**.

    ![][5]

###Initialiseer de REACH-SDK.

Roep in `App.xaml.cs` **EngagementReach.Instance.Init(e);** aan in de functie **InitEngagement**, direct na initialisatie van de agent:

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

Nu bent u klaar voor het verzenden van een toast-melding. Nu controleren we of u hebt deze basisintegratie juist hebt uitgevoerd.

###Mobile Engagement toegang verlenen om meldingen te verzenden

1. Open het [ontwikkelaarscentrum voor Windows Store] in uw webbrowser, meld u aan en maak een account indien nodig.
2. Klik op **Dashboard** in de rechterbovenhoek hoek en klik vervolgens op **Create a new app** in het menu aan de linkerkant. 

    ![][9]

2. Maak uw app door de naam ervan te reserveren. 

    ![][10]

3. Zodra de app is gemaakt, gaat u naar **Services -> Push notifications** in het menu links.

    ![][11]

4. Klik in de sectie Push notifications op de koppeling **Live Services site**. 

    ![][12]

5. U gaat naar het gedeelte Push credentials. Zorg dat u zich in de sectie **App Settings** bevindt en kopieer uw **Package SID** en **Client secret**.

    ![][13]

6. Navigeer naar de **Instellingen** van uw Mobile Engagement-portal en klik op de sectie **Native pushbericht** aan de linkerkant. Klik vervolgens op de knop **Bewerken** om uw **Beveiligings-id van pakket (SID)** en uw **Geheime sleutel** in te voeren, zoals hieronder weergegeven:

    ![][6]

8. Controleer ten slotte of u de app in Visual Studio hebt gekoppeld aan de app die u in de App store hebt gemaakt. U moet klikken op **Associate App with Store** in Visual Studio om dit te doen.

    ![][7]

##<a id="send"></a>Een melding verzenden naar uw app

[AZURE.INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Als de toepassing wordt uitgevoerd, wordt een melding in de app weergegeven. Als de app is gesloten, wordt een toast-melding weergegeven. Als u wel een in-app-melding ziet maar geen toast-melding terwijl u de app uitvoert in de foutopsporingsmodus in Visual Studio, gebruikt u **Lifecycle events -> Suspend** op de werkbalk om ervoor te zorgen dat de app daadwerkelijk wordt onderbroken. Als u alleen op de knop Start hebt geklikt bij het opsporen van fouten in de toepassing in Visual Studio, wordt de app niet altijd onderbroken; daardoor wordt er geen toast-melding weergegeven, maar wel een in-app-melding.  

![][8]

<!-- URLs. -->
[Mobile Engagement universele Windows SDK-documentatie]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[ontwikkelaarscentrum voor Windows Store]: https://dev.windows.com
[Universele Windows-apps - Overlay-integratie]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png





<!--HONumber=Jun16_HO2-->


