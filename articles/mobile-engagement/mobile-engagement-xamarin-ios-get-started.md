<properties
    pageTitle="Aan de slag met Azure Mobile Engagement voor Xamarin.iOS"
    description="Informatie over het gebruik van Azure Mobile Engagement met analyses en pushmeldingen voor Xamarin.iOS-apps."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager=""
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="03/25/2016"
    ms.author="piyushjo" />

# Aan de slag met Azure Mobile Engagement voor Xamarin.iOS-apps

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In dit onderwerp leest u hoe u Azure Mobile Engagement gebruikt om inzicht te krijgen in het gebruik van uw apps, en om pushmeldingen te verzenden aan gesegmenteerde gebruikers van een Xamarin.iOS-toepassing.
In deze zelfstudie maakt u een lege Xamarin.iOS-app die basisgegevens verzamelt en pushmeldingen ontvangt via Apple Push Notification System (APNS).

Voor deze zelfstudie hebt u het volgende nodig:

+ [Xamarin Studio](http://xamarin.com/studio). U kunt ook Visual Studio gebruiken met Xamarin, maar deze zelfstudie gebruikt Xamarin Studio. Zie [Setup and Install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (Installatie voor Visual Studio en Xamarin) voor installatie-instructies. 
+ [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started) voor meer informatie.

##<a id="setup-azme"></a>Mobile Engagement instellen voor uw iOS-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Uw app verbinden met de back-end van Mobile Engagement

Deze zelfstudie toont een ‘basisintegratie’, de minimale set die vereist is voor het verzamelen van gegevens en verzenden van een pushmelding.

We gaan een eenvoudige app maken met Xamarin ter illustratie van de integratie.

###Een nieuw Xamarin.iOS-project maken

1. Start Xamarin Studio. Ga naar **File** -> **New** -> **Solution**. 

    ![][1]

2. Selecteer **Single View App**, controleer of de geselecteerde taal **C#** is en klik op **Next**.

    ![][2]

3. Vul de velden **App Name** en **Organization Identifier** in en klik op **Next**. 

    ![][3]

    > [AZURE.IMPORTANT] Zorg ervoor dat het publicatieprofiel dat u gaat gebruiken voor het implementeren van uw iOS-app gebruikmaakt van een app-id die exact overeenkomt met de bundel-id op deze locatie. 

4. Werk de velden **Project Name**, **Solution Name** en **Location** bij indien nodig en klik op **Create**.

    ![][4]
 
Xamarin Studio maakt de demo-app waarin we Mobile Engagement gaan integreren. 

###Uw app verbinden met de back-end van Mobile Engagement

1. Klik met de rechtermuisknop op de map **Packages** in het venster Solution en selecteer **Add Packages…**.

    ![][5]

2. Zoeken de **Microsoft Azure Mobile Engagement Xamarin SDK** en voeg die toe aan uw oplossing.  

    ![][6]
   
3. Open **MainActivity.cs** en voeg het volgende toe met de instructie:

        using Microsoft.Azure.Engagement.Xamarin;

4. In de methode **FinishedLaunching** voegt u het volgende toe om de verbinding met de back-end van Mobile Engagement te initialiseren. Zorg ervoor dat u uw **verbindingsreeks** toevoegt. In deze code wordt een dummy-**NotificationIcon** toegevoegd door de Mobile Engagement SDK die u waarschijnlijk zult willen vervangen. 

        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

##<a id="monitor"></a>Realtime-bewaking inschakelen

Om te beginnen met het verzenden van gegevens en ervoor te zorgen dat de gebruikers actief zijn, moet u ten minste één scherm naar de back-end van Mobile Engagement sturen.

1. Open **ViewController.cs** en voeg het volgende toe met de instructie:

        using Microsoft.Azure.Engagement.Xamarin;

2. Verander de klasse waarvan `ViewController` overneemt van `UIViewController` naar `EngagementViewController`. 

##<a id="monitor"></a>App verbinden met realtime-bewaking

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Pushmeldingen en in-app-berichten inschakelen

Met Mobile Engagement kunt u communiceren met uw gebruikers en ze bereiken met pushmeldingen en in-app-berichten in de context van campagnes. Deze module heet REACH in de Mobile Engagement-portal.
In de volgende secties stelt u de app in om die te ontvangen.

### Uw toepassingsgemachtigde wijzigen

1. Open **AppDelegate.cs** en voeg het volgende toe met de instructie:

        using System; 

2. Voeg binnen de methode `FinishedLaunching` het volgende toe voor de registratie van pushberichten na `EngagementAgent.init(...)`

        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }

3. Als laatste moet u de volgende methoden bijwerken of toevoegen:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }

4. Controleer in het bestand **Info.plist**van de oplossing of de **Bundle Identifier** overeenkomt met de **App ID** in uw inrichtingsprofiel in het Apple Dev Center. 

    ![][7]

5. In hetzelfde bestand **Info.plist** moeten **Enable Background Modes** en **Remote Notifications** zijn ingeschakeld. 

    ![][8]

6. Voer de app uit op het apparaat dat u hebt gekoppeld aan dit publicatieprofiel. 

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png



<!--HONumber=Jun16_HO2-->


