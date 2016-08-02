<properties
    pageTitle="iOS-pushmeldingen met Notification Hubs voor Xamarin-apps | Microsoft Azure"
    description="In deze zelfstudie leert u hoe u met Azure Notification Hubs pushmeldingen verzendt naar een Xamarin iOS-toepassing."
    services="notification-hubs"
    keywords="ios push notifications,push messages,push notifications,push message"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="02/15/2016"
    ms.author="yuaxu"/>

# iOS-pushmeldingen met Notification Hubs voor Xamarin-apps

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Overzicht
> [AZURE.IMPORTANT] U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started) voor meer informatie.

In deze zelfstudie wordt gedemonstreerd hoe u met Azure Notification Hubs pushmeldingen verzendt naar een iOS-toepassing.
U maakt een lege Xamarin.iOS-app die pushmeldingen ontvangt met de [Apple Push Notification Service (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). Als u klaar bent, kunt u de Notification Hub gebruiken om pushmeldingen uit te zenden naar alle apparaten waarop uw app wordt uitgevoerd. De voltooide code is beschikbaar in het [NotificationHubs-app][GitHub]-voorbeeld.

In deze zelfstudie wordt een eenvoudig scenario voor het uitzenden van pushmeldingen met Notification Hubs beschreven.

##Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

+ [Xcode 6.0][Xcode installeren]
+ Een apparaat dat compatibel is met iOS 7.0 (of een hogere versie)
+ iOS Developer Program-lidmaatschap
+ [Xamarin Studio]

   > [AZURE.NOTE] Vanwege configuratievereisten voor iOS-pushmeldingen moet u de voorbeeldtoepassing op een fysiek iOS-apparaat (iPhone of iPad) implementeren en testen in plaats van in de simulator.

Het voltooien van deze zelfstudie is een vereiste voor alle andere Notification Hubs-zelfstudies voor Xamarin iOS-apps.


[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]


##Uw Notification Hub configureren

In deze sectie wordt u begeleid bij het maken van een nieuwe Notification Hub en het configureren van verificatie met APNs met het **.p12**-pushcertificaat dat u hebt gemaakt. Als u een Notification Hub wilt gebruiken die u al hebt gemaakt, kunt u doorgaan naar stap 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li>
<p>Omdat wij de APNs-verbinding willen configureren, opent u in Azure Portal de Notification Hub-instellingen en klikt u achtereenvolgens op <b>Notification Services</b> en het item <b>Apple (APNS)</b> in de lijst. Hierna klikt u op <b>Certificaat uploaden</b> en selecteert u het <b>.p12</b>-certificaat dat u eerder hebt geëxporteerd, evenals het wachtwoord voor het certificaat.</p>
<p>Zorg ervoor dat u de <b>Sandbox</b>-modus selecteert, omdat u pushberichten in een ontwikkelomgeving gaat verzenden. Gebruik de instelling <b>Productie</b> alleen als u pushmeldingen wilt verzenden naar gebruikers die uw app al in de winkel hebben aangeschaft.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-apns.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-sandbox.png)


De Notification Hub is nu geconfigureerd om te werken met APNs en u hebt de verbindingsreeksen om uw app te registreren en pushmeldingen te verzenden.


##Uw app verbinden met de Notification Hub

#### Een nieuw project maken

1. Maak in Xamarin Studio een nieuw iOS-project en selecteer de sjabloon **Unified API** > **Toepassing voor één weergave**.

    ![Xamarin Studio - Toepassingstype selecteren][31]

2. Voeg een verwijzing toe aan het Azure Messaging-onderdeel. Klik in de oplossingsweergave met de rechtermuisknop op de map **Onderdelen** voor uw project en kies **Meer onderdelen ophalen**. Zoek het onderdeel **Azure Messaging** en voeg het onderdeel toe aan het project.

3. Voeg in **AppDelegate.cs** de volgende instructie toe:

        using WindowsAzure.Messaging;

4. Declareer een exemplaar van **SBNotificationHub**:

        private SBNotificationHub Hub { get; set; }

5. Maak een klasse **Constants.cs** met de volgende variabelen:

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. Werk in **AppDelegate.cs** het item **FinishedLaunching()** zo bij dat het overeenkomt met het volgende:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }

            return true;
        }

7. Overschrijf de **RegisteredForRemoteNotifications()**-methode in **AppDelegate.cs**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. Overschrijf de **ReceivedRemoteNotification()**-methode in **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Maak de volgende **ProcessNotification()**-methode in **AppDelegate.cs**:

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE] U kunt ervoor kiezen **FailedToRegisterForRemoteNotifications()** te overschrijven om situaties zoals het ontbreken van een netwerkverbinding af te handelen. Dit is vooral belangrijk wanneer de gebruiker de toepassing in de offlinemodus start (bijvoorbeeld in een vliegtuig) en u scenario's voor het verzenden van pushberichten wilt afhandelen die specifiek zijn voor uw app.


10. Voer de app uit op uw apparaat.


## Pushmeldingen verzenden


U kunt het ontvangen van pushmeldingen in uw app testen door in [Azure Portal] via de mogelijkheid **Test verzenden** in de werkset **Probleemoplossing** direct op de pagina van de Notification Hub meldingen te verzenden (zie onderstaand scherm).

![](./media/notification-hubs-ios-get-started/notification-hubs-test-send.png)

Pushmeldingen worden gewoonlijk met een compatibele bibliotheek verzonden via een back-endservice zoals Mobile Services of ASP.NET. U kunt de REST API ook direct gebruiken om pushmeldingen te verzenden als er geen bibliotheek beschikbaar is voor uw scenario. 

In deze zelfstudie houden we het eenvoudig en wordt alleen gedemonstreerd hoe u uw clientapp test door meldingen te verzenden met de .NET SDK voor Notification Hubs in een consoletoepassing in plaats van een back-endservice. U kunt het beste de zelfstudie [Notification Hubs gebruiken om pushmeldingen naar gebruikers te verzenden](notification-hubs-aspnet-backend-ios-notify-users.md) doornemen voor informatie over het verzenden van meldingen vanuit een ASP.NET-back-end. Voor het verzenden van meldingen kunt u echter de volgende methoden gebruiken:

* **REST-interface**: u kunt pushmeldingen op elk back-endplatform ondersteunen met de [REST-interface](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Microsoft Azure Notification Hubs .NET SDK**: in NuGet Package Manager voor Visual Studio voert u [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) uit.

* **Node.js**: [Notification Hubs gebruiken vanuit Node.js](notification-hubs-nodejs-how-to-use-notification-hubs.md).

* **Azure Mobile Services**: zie 'Aan de slag met pushmeldingen in Mobile Services' voor een voorbeeld van hoe u pushmeldingen verzendt vanuit een back-end van Azure Mobile Servicesdie is geïntegreerd met Notification Hubs ([.NET-back-end](../mobile-services/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md) | [JavaScript-back-end](../mobile-services/mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md)).

* **Java/PHP**: zie 'Notification Hubs gebruiken vanuit Java/PHP' voor een voorbeeld van hoe u pushmeldingen verzendt met de REST API's ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).


####(Optioneel) Pushmeldingen verzenden vanuit een .NET-console-app

In deze sectie worden pushmeldingen verzonden met een eenvoudige .NET-console-app. Voor dit voorbeeld wordt overgeschakeld naar een Windows-ontwikkelomgeving waarin Visual Studio al is geïnstalleerd.

1. Maak in Visual Studio een nieuwe Visual C#-consoletoepassing:

    ![Visual Studio - Een nieuwe consoletoepassing maken][213]

2. Klik in Visual Studio achtereenvolgens op **Extra**, **NuGet Package Manager** en **Package Manager-console**.

    De Package Manager-console moet vastgezet aan de onderkant van de Visual Studio-werkruimte worden weergegeven.

3. Stel in het venster Package Manager-console het **standaardproject** in op uw nieuwe consoletoepassingsproject en voer vervolgens in het consolevenster de volgende opdracht uit:

        Install-Package Microsoft.Azure.NotificationHubs

    Hiermee wordt een verwijzing toegevoegd aan de Azure Notification Hubs SDK met het <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet-pakket</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. Open het bestand `Program.cs` en voeg de volgende `using`-instructie toe, waarbij u ervoor zorgt dat we Azure-klassen en -functies binnen uw hoofdklasse kunnen gebruiken:

        using Microsoft.Azure.NotificationHubs;

3. Voeg in de klasse `Program` de volgende methode toe (vergeet niet om de **verbindingsreeks** en **hubnaam** te vervangen):

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Voeg de volgende regels in de `Main`-methode toe:

         SendNotificationAsync();
         Console.ReadLine();

5. Druk op de toets F5 om de app uit te voeren. Binnen enkele seconden wordt een pushmelding op uw apparaat weergegeven. Zorg ervoor dat u via Wi-Fi of een mobiele verbinding voor dataverkeer een actieve internetverbinding op het apparaat hebt.

U kunt alle mogelijke nettoladingen vinden in de [Local and Push Notification Programming Guide] van Apple.

####(Optioneel) Meldingen verzenden vanuit een mobiele service

In deze sectie worden pushmeldingen verzonden met een mobiele service via een knooppuntscript.

Als u een melding wilt verzenden met een mobiele service, volgt u [Aan de slag met Mobile Services] en gaat u daarna als volgt te werk:

1. Meld u aan bij de [klassieke Azure-portal] en selecteer uw mobiele service.

2. Selecteer het tabblad **Scheduler** aan de bovenkant.

    ![Klassieke Azure-portal - Scheduler][215]

3. Maak een nieuwe geplande taak, voeg een naam in en selecteer **Op aanvraag**.

    ![Klassieke Azure-portal - Nieuwe taak maken][216]

4. Wanneer de taak is gemaakt, klikt u op de taaknaam. Klik vervolgens op het tabblad **Script** op de bovenste balk.

5. Voeg het volgende script in de plannerfunctie in. Zorg ervoor dat de tijdelijke aanduidingen worden vervangen door de Notification Hub-naam en de verbindingsreeks voor *DefaultFullSharedAccessSignature* die u eerder hebt verkregen. Klik op **Opslaan**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );


6. Klik op **Eenmaal uitvoeren** op de balk onderaan. U ontvangt een waarschuwing op uw apparaat.

##Volgende stappen

In dit eenvoudige voorbeeld hebt u pushmeldingen uitgezonden naar al uw iOS-apparaten. Als u zich op specifieke gebruikers wilt richten, raadpleegt u de zelfstudie [Notification Hubs gebruiken om pushmeldingen naar gebruikers te verzenden]. Als u gebruikers wilt indelen op belangengroepen, raadpleegt u [Notification Hubs gebruiken om belangrijk nieuws te verzenden]. Lees meer over het gebruik van Notification Hubs in [Richtlijnen voor Notification Hubs] en in [Notification Hubs-procedure voor iOS].


<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[iOS SDK voor Mobile Services]: http://go.microsoft.com/fwLink/?LinkID=266533
[Een app-pagina verzenden]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mijn toepassingen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK voor Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Aan de slag met Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[klassieke Azure-portal]: https://manage.windowsazure.com/
[Richtlijnen voor Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs-procedure voor iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Xcode installeren]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Notification Hubs gebruiken om pushmeldingen naar gebruikers te verzenden]: /manage/services/notification-hubs/notify-users-aspnet
[Notification Hubs gebruiken om belangrijk nieuws te verzenden]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services-onderdeel]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin Studio]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
[Azure Portal]: https://portal.azure.com



<!--HONumber=Jun16_HO2-->


