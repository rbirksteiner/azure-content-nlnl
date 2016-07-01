<properties
    pageTitle="Aan de slag met Azure Mobile Engagement voor Windows Phone Silverlight-apps"
    description="Informatie over het gebruik van Azure Mobile Engagement met analyses en pushmeldingen voor Windows Phone Silverlight-apps."
    services="mobile-engagement"
    documentationCenter="windows"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/03/2016"
    ms.author="piyushjo" />

# Aan de slag met Azure Mobile Engagement voor Windows Phone Silverlight-apps

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In dit onderwerp leest u hoe u Azure Mobile Engagement gebruikt om inzicht te krijgen in het gebruik van uw apps, en om pushmeldingen te verzenden aan gesegmenteerde gebruikers van een Windows Phone Silverlight-toepassing.
Deze zelfstudie laat een eenvoudig broadcast-scenario met Mobile Engagement zien. In deze zelfstudie maakt u een lege Windows Phone Silverlight-app die basisgegevens verzamelt en pushmeldingen ontvangt via Microsoft Push Notification Service (MPNS).

> [AZURE.NOTE] Als u ontwikkelt voor Windows Phone 8.1 (zonder Silverlight), raadpleegt u de [universele Windows-zelfstudie](mobile-engagement-windows-store-dotnet-get-started.md).

Voor deze zelfstudie hebt u het volgende nodig:

+ Visual Studio 2013
+ NuGet-pakket van [MicrosoftAzure.MobileEngagement]

> [AZURE.NOTE] U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started) voor meer informatie.

##<a id="setup-azme"></a>Mobile Engagement instellen voor uw Windows Phone-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Uw app verbinden met de back-end van Mobile Engagement

Deze zelfstudie toont een ‘basisintegratie’, de minimale set die vereist is voor het verzamelen van gegevens en verzenden van een pushmelding. De volledige integratiedocumentatie is te vinden in de [Mobile Engagement Windows Phone SDK-integratie](mobile-engagement-windows-phone-sdk-overview.md).

We gaan een eenvoudige app maken met Visual Studio ter illustratie van de integratie.

###Een nieuw Windows Phone Silverlight-project maken

In de volgende stappen wordt ervan uitgegaan dat u Visual Studio 2015 gebruikt, hoewel de stappen hetzelfde zijn in eerdere versies van Visual Studio. 

1. Start Visual Studio en selecteer **New Project** in het scherm **Home**.

2. Selecteer in het pop-upvenster **Windows 8** -> **Windows Phone** -> **Blank App (Windows Phone Silverlight)**. Vul de velden **Name** en **Solution name** in voor de app en klik vervolgens op **OK**.

    ![][1]

3. U kunt **Windows Phone 8.0** of **Windows Phone 8.1** als doel kiezen.

U hebt nu een nieuwe Windows Phone Silverlight-app gemaakt waarin de Azure Mobile Engagement SDK is geïntegreerd.

###Uw app verbinden met de back-end van Mobile Engagement

1. Installeer het NuGet-pakket van [MicrosoftAzure.MobileEngagement] in het project.

2. Open `WMAppManifest.xml` (onder de map Eigenschappen) en zorg ervoor dat het volgende is gedeclareerd (voeg items toe als dat niet het geval is) in de tag `<Capabilities />`:

        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. Plak nu de verbindingsreeks die u eerder hebt gekopieerd voor uw Mobile Engagement-app in het bestand `Resources\EngagementConfiguration.xml`, tussen de tags `<connectionString>` en `</connectionString>`:

    ![][3]

4. In het bestand `App.xaml.cs`:

    a. Voeg de instructie `using` toe:

            using Microsoft.Azure.Engagement;

    b. Initialiseer de SDK in de methode `Application_Launching`:

            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }

    c. Voeg het volgende toe in de `Application_Activated`:

            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

##<a id="monitor"></a>Realtime-bewaking inschakelen

U dient ten minste één scherm (activiteit) naar de back-end van Mobile Engagement te sturen om te beginnen met het verzenden van gegevens en ervoor te zorgen dat de gebruikers actief zijn.

1. Voeg in MainPage.xaml.cs de instructie `using` toe:

        using Microsoft.Azure.Engagement;

2. Vervang de basisklasse van **MainPage**, die zich voor **PhoneApplicationPage** bevindt, door **EngagementPage**.

        class MainPage : EngagementPage 
    
3. In uw bestand `MainPage.xml`:

    a. Toevoegen aan de naamruimtedeclaraties:

         xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

    b. Vervang `phone:PhoneApplicationPage` in de XML-tagnaam met `engagement:EngagementPage`.

##<a id="monitor"></a>App verbinden met realtime-bewaking

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Pushmeldingen en in-app-berichten inschakelen

Met Mobile Engagement kunt u communiceren met uw gebruikers en ze bereiken met pushmeldingen en in-app-berichten in de context van campagnes. Deze module heet REACH in de Mobile Engagement-portal.
In de volgende secties stelt u de app in om die te ontvangen.

###Ontvangen van MPNS-pushmeldingen inschakelen voor de app

Nieuwe mogelijkheden toevoegen aan uw bestand `WMAppManifest.xml`:

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###Initialiseer de REACH-SDK.

1. Roep in `App.xaml.cs` `EngagementReach.Instance.Init();` aan in de functie **Application_Launching**, meteen na de initialisatie van de agent:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

2. Roep in `App.xaml.cs` `EngagementReach.Instance.OnActivated(e);` aan in de functie **Application_Activated**, meteen na de initialisatie van de agent:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

U bent nu klaar. Nu controleren we of u hebt deze basisintegratie juist hebt uitgevoerd.

##<a id="send"></a>Een melding verzenden naar uw app

[AZURE.INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Er moet nu een melding op uw apparaat worden weergegeven, als een in-app-melding indien de app is geopend, of anders als een toast-melding zoals in dit voorbeeld: 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK-documentatie]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png



<!--HONumber=Jun16_HO2-->


