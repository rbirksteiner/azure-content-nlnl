<properties
    pageTitle="Aan de slag met Azure Mobile Engagement voor iOS in Swift"
    description="Informatie over het gebruik van Azure Mobile Engagement met analyses en pushmeldingen voor iOS-apps."
    services="mobile-engagement"
    documentationCenter="ios"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="hero-article"
    ms.date="05/03/2016"
    ms.author="piyushjo" />

# Aan de slag met Azure Mobile Engagement voor iOS-apps in Swift

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In dit onderwerp leest u hoe u Azure Mobile Engagement gebruikt om inzicht te krijgen in het gebruik van uw apps, en om pushmeldingen te verzenden aan gesegmenteerde gebruikers van een iOS-toepassing.
In deze zelfstudie maakt u een lege iOS-app die basisgegevens verzamelt en pushmeldingen ontvangt via Apple Push Notification System (APNS).

Voor deze zelfstudie hebt u het volgende nodig:

+ XCode 6 of XCode 7, die u vanuit de Mac App Store kunt installeren.
+ De [Mobile Engagement iOS SDK].
+ Pushmeldingcertificaat (.p12), dat u kunt verkrijgen via Apple Dev Center.

> [AZURE.NOTE] In deze zelfstudie wordt gebruikgemaakt van Swift versie 2.0. 

Het voltooien van deze zelfstudie is een vereiste voor alle andere Mobile Engagement-zelfstudies voor iOS-apps.

> [AZURE.NOTE] U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started) voor meer informatie.

##<a id="setup-azme"></a>Mobile Engagement instellen voor uw iOS-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Uw app verbinden met de back-end van Mobile Engagement

Deze zelfstudie toont een 'basisintegratie', de minimale set die vereist is voor het verzamelen van gegevens en verzenden van een pushmelding. De volledige integratiedocumentatie is te vinden in de [Mobile Engagement iOS SDK-integratie](mobile-engagement-ios-sdk-overview.md).

We gaan een eenvoudige app maken met XCode ter illustratie van de integratie:

###Een nieuw iOS-project maken

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###Uw app verbinden met de back-end van Mobile Engagement

1. Download de [Mobile Engagement iOS SDK].
2. Pak het .tar.gz-bestand uit in een map op uw computer.
3. Klik met de rechtermuisknop op het project en selecteer Add files to...

    ![][1]

4. Navigeer naar de map waar u de SDK hebt uitgepakt, selecteer de map `EngagementSDK` en druk op OK.

    ![][2]

5. Open het tabblad `Build Phases` en voeg in het menu `Link Binary With Libraries` de frameworks toe, zoals hieronder weergegeven. **Opmerking** U moet `CoreLocation, CFNetwork, CoreTelephony, and SystemConfiguration` opnemen:

    ![][3]

6. In **XCode 7** voegt u `libxml2.tbd` toe in plaats van `libxml2.dylib`.

7. Maak een bridging header om de Objective C-API's van de SDK te kunnen gebruiken. U doet dit door naar File > New > File > iOS > Source > Header File te gaan.

    ![][4]

8. Bewerk het bridging-headerbestand om de Objective-C-code van Mobile Engagement beschikbaar te maken voor uw Swift-code, en voeg de volgende import toe:

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEIdfaProvider.h"

9. Controleer onder Build Settings of de instellingen voor het bouwen van de Objective-C Bridging Header onder Swift Compiler - Code Generation een pad naar deze header bevatten. Dit is een voorbeeld van een pad: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (afhankelijk van het pad)**

    ![][6]

10. Ga terug naar de Azure-portal op de pagina *Verbindingsgegevens* van de app en kopieer de verbindingsreeks.

    ![][5]

11. Plak nu de verbindingsreeks in de gemachtigde `didFinishLaunchingWithOptions`.

        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }

##<a id="monitor"></a>Realtime-bewaking inschakelen

Om te beginnen met het verzenden van gegevens en ervoor te zorgen dat de gebruikers actief zijn, moet u ten minste één scherm (activiteit) naar de back-end van Mobile Engagement sturen.

1. Open het bestand **ViewController.swift** en vervang de basisklasse van **ViewController** met **EngagementViewController**:

    `class ViewController : EngagementViewController {`

##<a id="monitor"></a>App verbinden met realtime-bewaking

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Pushmeldingen en in-app-berichten inschakelen

Met Mobile Engagement kunt u communiceren met uw gebruikers en ze bereiken met pushmeldingen en in-app-berichten in de context van campagnes. Deze module heet REACH in de Mobile Engagement-portal.
In de volgende secties stelt u de app in om die te ontvangen.

### Ontvangen van achtergrond-pushmeldingen inschakelen voor de app

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### De Reach-bibliotheek toevoegen aan uw project

1. Klik met de rechtermuisknop op het project.
2. Selecteer `Add file to ...`
3. Navigeer naar de map waar u de SDK hebt uitgepakt.
4. Selecteer de map `EngagementReach`.
5. Klik op Add.
6. Bewerk het bridging-headerbestand om de Reach-headers van Mobile Engagement Objective-C  beschikbaar te maken, en voeg de volgende import toe:

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### Uw toepassingsgemachtigde wijzigen

1. Binnen de `didFinishLaunchingWithOptions` maakt u een Reach-module en geeft die door aan uw bestaande initialisatieregel voor Engagement:

        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
            let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

###Ontvangen van APNS-pushmeldingen inschakelen voor de app
1. Voeg de volgende regel toe aan de methode `didFinishLaunchingWithOptions`:

        /* Ask user to receive push notifications */
        if #available(iOS 8.0, *)
        {
           let settings = UIUserNotificationSettings(forTypes: [UIUserNotificationType.Alert, UIUserNotificationType.Badge, UIUserNotificationType.Sound], categories: nil)
           application.registerUserNotificationSettings(settings)
           application.registerForRemoteNotifications()
        }
        else
        {
           application.registerForRemoteNotificationTypes([UIRemoteNotificationType.Alert, UIRemoteNotificationType.Badge, UIRemoteNotificationType.Sound])
        }

2. Voeg de methode `didRegisterForRemoteNotificationsWithDeviceToken` als volgt toe:

        func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
        {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. Voeg de methode `didReceiveRemoteNotification:fetchCompletionHandler:` als volgt toe:

        func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void)
        {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png



<!--HONumber=Jun16_HO2-->


