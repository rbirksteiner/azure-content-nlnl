<properties
    pageTitle="Pushmeldingen verzenden naar iOS met Azure Notification Hubs | Microsoft Azure"
    description="In deze zelfstudie leert u hoe u met Azure Notification Hubs pushmeldingen verzendt naar een iOS-toepassing."
    services="notification-hubs"
    documentationCenter="ios"
    keywords="push notification,push notifications,ios push notifications"
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="03/14/2016"
    ms.author="wesmc"/>

# Pushmeldingen verzenden naar iOS met Azure Notification Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Overzicht

> [AZURE.NOTE] U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started) voor meer informatie.

In deze zelfstudie wordt gedemonstreerd hoe u met Azure Notification Hubs pushmeldingen verzendt naar een iOS-toepassing. U maakt een lege iOS-app die pushmeldingen ontvangt met de [Apple Push Notification Service (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). 

Als u klaar bent, kunt u de Notification Hub gebruiken om pushmeldingen uit te zenden naar alle apparaten waarop uw app wordt uitgevoerd.

## Voordat u begint

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

De volledige code voor deze zelfstudie vindt u [op GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

##Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

+ [iOS SDK Mobile Services versie 1.2.4]
+ Meest recente versie van [Xcode]
+ Een apparaat dat compatibel is met iOS 8 (of een hogere versie)
+ [Apple Developer Program](https://developer.apple.com/programs/)-lidmaatschap

   > [AZURE.NOTE] Vanwege configuratievereisten voor pushmeldingen moet u pushmeldingen op een fysiek iOS-apparaat (iPhone of iPad) implementeren en testen in plaats van in de iOS-simulator.

Het voltooien van deze zelfstudie is een vereiste voor alle andere Notification Hubs-zelfstudies voor iOS-apps.

[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##De Notification Hub voor iOS-pushmeldingen configureren

In deze sectie wordt u begeleid bij het maken van een nieuwe Notification Hub en het configureren van verificatie met APNs met het **.p12**-pushcertificaat dat u hebt gemaakt. Als u een Notification Hub wilt gebruiken die u al hebt gemaakt, kunt u doorgaan naar stap 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li>
<p>Klik op de knop <b>Notification Services</b> in de blade <b>Instellingen</b> en selecteer vervolgens <b>Apple (APNs)</b>. Klik op <b>Certificaat uploaden</b> en selecteer het <b>.p12</b>-bestand dat u eerder hebt geëxporteerd. Zorg ervoor dat u het juiste wachtwoord opgeeft.</p>
<p>Zorg ervoor dat u de modus <b>Sandbox</b> selecteert. Dit is de juiste ontwikkelingsmodus. Gebruik <b>Productie</b> alleen als u pushmeldingen wilt verzenden naar gebruikers die uw app in de winkel hebben aangeschaft.</p>
</li>
</ol>
&emsp;&emsp;![Configure APNS in Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;![APNs-certificering in Azure Portal configureren](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)



De Notification Hub is nu geconfigureerd om te werken met APNs en u hebt de verbindingsreeksen om uw app te registreren en pushmeldingen te verzenden.

##Uw iOS-app verbinden met Notification Hubs

1. Maak in Xcode een nieuw iOS-project en selecteer de sjabloon **Single View Application** (Toepassing met één weergave).

    ![Xcode - Toepassing voor één weergave][8]

2. Bij het instellen van de opties voor het nieuwe project moet u dezelfde **productnaam** en **organisatie-id** gebruiken als bij het instellen van de bundel-id op de Apple Developer portal.

    ![Xcode - Projectopties][11]

3. Klik onder **Targets** op de projectnaam, klik op het tabblad **Build Settings** (Instellingen opbouwen) en vouw **Code Signing Identity** (Identiteit voor ondertekening) uit. Stel vervolgens onder **Debug** (Fouten opsporen) uw identiteit voor ondertekening van programmacode in. Schakel **Levels** (Niveaus) van **Basic** (Basis) naar **All** (Alle) en stel **Provisioning Profile** (Profiel voor inrichting) in op het profiel voor inrichting dat u eerder hebt gemaakt.

    Als u het nieuwe profiel voor inrichting dat u hebt gemaakt in Xcode niet ziet, vernieuwt u de profielen voor uw identiteit voor ondertekening. Klik op **Xcode** in de menubalk, klik op **Preferences** (Voorkeuren), klik op het tabblad **Account** en klik op de knop **View Details** (Details weergeven), klik op uw identiteit voor ondertekening en klik vervolgens op de knop voor vernieuwen in de rechterbenedenhoek.

    ![Xcode - Profiel voor inrichting][9]

4. Download de [iOS SDK Mobile Services versie 1.2.4] en pak het bestand uit. Klik met de rechtermuisknop op uw project in Xcode en klik op de optie **Add Files to** (Bestanden toevoegen aan) om de map **WindowsAzureMessaging.framework** aan uw Xcode-project toe te voegen. Selecteer **Copy items if needed** (Items kopiëren indien nodig) en klik vervolgens op **Add**.

    >[AZURE.NOTE] Op dit moment biedt de Notification Hubs SDK geen ondersteuning voor bitcode op Xcode 7.  U moet **Enable Bitcode** (Bitcode inschakelen) instellen op **No** (Nee) in **Build Options** (Opties voor opbouwen) voor uw project.

    ![Azure SDK uitpakken][10]

5. Voeg een nieuw headerbestand toe aan uw project met de naam `HubInfo.h`. Dit bestand is bedoeld voor de constanten voor uw Notification Hub.  Voeg de volgende definities toe en vervang de tijdelijke aanduidingen voor tekenreeksen door uw *hubnaam* en de *DefaultListenSharedAccessSignature* die u eerder hebt genoteerd.

        #ifndef HubInfo_h
        #define HubInfo_h
        
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
        
        #endif /* HubInfo_h */

6. Open uw bestand `AppDelegate.h` en voeg de volgende instructies voor importeren toe:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
        
7. Voeg in uw `AppDelegate.m file` de volgende code toe in de `didFinishLaunchingWithOptions`-methode op basis van uw versie van iOS. Deze code registreert uw apparaatingang met APNs:

    Voor iOS 8:

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    Voor oudere iOS-versies:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


8. Voeg in hetzelfde bestand de volgende methoden toe. Deze code maakt verbinding met de Notification Hub met de verbindingsgegevens die u hebt opgegeven in HubInfo.h. Er wordt vervolgens een apparaattoken aan de Notification Hub toegekend, zodat de Notification Hub meldingen kan verzenden:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                    [self MessageBox:@"Registration Status" message:@"Registered"];
                }
            }];
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }


9. Voeg in hetzelfde bestand de volgende methode toe om een **UIAlert** weer te geven als de melding is ontvangen terwijl de app actief is:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

10. Ontwikkel en voer de app op uw apparaat uit om te controleren of er geen fouten zijn.

## Testpushmeldingen verzenden


U kunt testen of u meldingen in uw app ontvangt door pushmeldingen te verzenden via [Azure Portal]. Ga naar het gedeelte **Probleemoplossing** in de hubblade (en gebruik de optie *Test verzenden*).

![Azure Portal - Test verzenden][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## (Optioneel) Pushmeldingen vanuit de app verzenden

Als u pushmeldingen vanuit een app wilt verzenden, vindt u in deze sectie een voorbeeld van hoe u dit doet met de REST-interface.

1. Open `Main.storyboard` in Xcode en voeg de volgende gebruikersinterfaceonderdelen van de objectbibliotheek toe om in te stellen dat de gebruiker pushmeldingen in de app kan verzenden:

    - Een label zonder labeltekst. Dit wordt gebruikt om fouten tijdens het verzenden van meldingen te rapporteren. De eigenschap **Rules** (Regels) moet worden ingesteld op **0**, zodat de grootte naar de rechter- en linkermarges en de bovenkant van de weergave automatisch wordt beperkt.
    - Een tekstveld met **tijdelijke** tekst ingesteld op **Enter Notification Message** (Meldingstekst invoeren). Beperk het veld onder het label, zoals hieronder weergegeven. Stel de weergavebesturing in als de gemachtigde voor de aansluiting.
    - Een knop met de naam **Send Notification** (Melding verzenden) die direct onder het tekstveld en in horizontale richting is beperkt.

    De weergave ziet er als volgt uit:

    ![Xcode designer][32]


2. [Voeg aansluitingen](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) voor het label en het tekstveld in uw weergave toe en werk uw `interface`-definitie bij om `UITextFieldDelegate` en `NSXMLParserDelegate` te ondersteunen. Voeg de drie eigenschapdeclaraties die hieronder worden weergegeven toe om het aanroepen van de REST-API en het parseren van het antwoord te ondersteunen.

    Uw ViewController.h-bestand moet er als volgt uitzien:

        #import <UIKit/UIKit.h>

        @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
        {
            NSXMLParser *xmlParser;
        }

        // Make sure these outlets are connected to your UI by ctrl+dragging
        @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
        @property (weak, nonatomic) IBOutlet UILabel *sendResults;

        @property (copy, nonatomic) NSString *statusResult;
        @property (copy, nonatomic) NSString *currentElement;

        @end

3. Open `HubInfo.h` en voeg de volgende constanten toe, die worden gebruikt voor het verzenden van meldingen naar de hub. Vervang de tijdelijke aanduiding voor tekenreeksen door uw werkelijke *DefaultFullSharedAccessSignature*-verbindingsreeks.

        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"

4. Voeg de volgende instructies voor `#import` toe aan uw `ViewController.h`-bestand.

        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"

5. Voeg in `ViewController.m` de volgende code toe aan de implementatie van de interface. Deze code parseert uw *DefaultFullSharedAccessSignature*-verbindingsreeks. Zoals vermeld in de [REST-API-verwijzing](http://msdn.microsoft.com/library/azure/dn495627.aspx) wordt deze geparseerde informatie gebruikt om een SaS-token te genereren voor de aanvraagheader **Authorization** (Autorisatie).

        NSString *HubEndpoint;
        NSString *HubSasKeyName;
        NSString *HubSasKeyValue;

        -(void)ParseConnectionString
        {
            NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
            NSString *part;

            if ([parts count] != 3)
            {
                NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                    reason:@"Invalid full shared access connection string" userInfo:nil];

                @throw parseException;
            }

            for (part in parts)
            {
                if ([part hasPrefix:@"Endpoint"])
                {
                    HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
                }
                else if ([part hasPrefix:@"SharedAccessKeyName"])
                {
                    HubSasKeyName = [part substringFromIndex:20];
                }
                else if ([part hasPrefix:@"SharedAccessKey"])
                {
                    HubSasKeyValue = [part substringFromIndex:16];
                }
            }
        }

6. Werk in `ViewController.m` de `viewDidLoad`-methode bij voor het parseren van de verbindingsreeks zodra de weergave wordt geladen. Voeg ook de hulpprogrammamethoden die hieronder worden weergegeven toe aan de implementatie van de interface.  


        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self ParseConnectionString];
            [_notificationMessage setDelegate:self];
        }

        -(NSString *)CF_URLEncodedString:(NSString *)inputString
        {
           return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
                NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }





7. Voeg in `ViewController.m` de volgende code toe aan de implementatie van de interface om het SaS-autorisatietoken te genereren dat beschikbaar wordt gesteld in de koptekst **Authorization** (Autorisatie), zoals vermeld de [REST-API-verwijzing](http://msdn.microsoft.com/library/azure/dn495627.aspx).

        -(NSString*) generateSasToken:(NSString*)uri
        {
            NSString *targetUri;
            NSString* utf8LowercasedUri = NULL;
            NSString *signature = NULL;
            NSString *token = NULL;

            @try
            {
                // Add expiration
                uri = [uri lowercaseString];
                utf8LowercasedUri = [self CF_URLEncodedString:uri];
                targetUri = [utf8LowercasedUri lowercaseString];
                NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60;
                UInt64 expires = trunc(expiresOnDate);
                NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

                // Get an hmac_sha1 Mac instance and initialize with the signing key
                const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
                const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
                unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
                CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
                NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
                signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

                // Construct authorization token string
                token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                    signature, expires, HubSasKeyName, targetUri];
            }
            @catch (NSException *exception)
            {
                [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
            }
            @finally
            {
                if (utf8LowercasedUri != NULL)
                    CFRelease((CFStringRef)utf8LowercasedUri);
                if (signature != NULL)
                CFRelease((CFStringRef)signature);
            }

            return token;
        }


8. Houd CTRL ingedrukt en sleep vanaf de knop **Send Notification** (Melding verzenden) om `ViewController.m` een actie met de naam **SendNotificationMessage** voor de **Touch Down**-gebeurtenis toe te voegen. Werk de methode bij met de volgende code om de melding via de REST-API te verzenden.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";
            [self SendNotificationRESTAPI];
        }

        - (void)SendNotificationRESTAPI
        {
            NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

            // Apple Notification format of the notification message
            NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                                self.notificationMessage.text];

            // Construct the message's REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                                HUBNAME, API_VERSION]];

            // Generate the token to be used in the authorization header
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the APNs notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            // Signify Apple notification format
            [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];
            [dataTask resume];
        }


9. Voeg in `ViewController.m` de volgende gemachtigdenmethode toe om het toetsenbord te sluiten voor het tekstveld. Houd CTRL ingedrukt en sleep vanuit het tekstveld naar het pictogram voor weergavebesturing in de interface-ontwerper om de weergavebesturing instellen als de gemachtigde voor de aansluiting.

        //===[ Implement UITextFieldDelegate methods ]===

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }


10. Voeg in `ViewController.m` de volgende gemachtigdenmethoden toe om het antwoord via `NSXMLParser` te kunnen parseren.

        //===[ Implement NSXMLParserDelegate methods ]===

        -(void)parserDidStartDocument:(NSXMLParser *)parser
        {
            self.statusResult = @"";
        }

        -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
            namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
            attributes:(NSDictionary *)attributeDict
        {
            NSString * element = [elementName lowercaseString];
            NSLog(@"*** New element parsed : %@ ***",element);

            if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
            {
                self.currentElement = element;
            }
        }

        -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
        {
            self.statusResult = [self.statusResult stringByAppendingString:
                [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
        }

        -(void)parserDidEndDocument:(NSXMLParser *)parser
        {
            // Set the status label text on the UI thread
            dispatch_async(dispatch_get_main_queue(),
            ^{
                [self.sendResults setText:self.statusResult];
            });
        }



11. Bouw het project en controleer of er geen fouten zijn.


> [AZURE.NOTE] Als u in Xcode7 een opbouwfout ontdekt met betrekking tot ondersteuning voor bitcode, moet u **Build Settings** > **Enable Bitcode (ENABLE_BITCODE)** (Instellingen voor opbouwen - Bitcode inschakelen) instellen op **No** (Nee) in Xcode. Op dit moment biedt de Notification Hubs SDK geen ondersteuning voor bitcode. 

U vindt alle mogelijke nettoladingen van meldingen in de [Local and Push Notification Programming Guide] van Apple.


##Controleren of een app pushmeldingen kan ontvangen

Als u pushmeldingen op iOS wilt testen, moet u de app implementeren op een fysiek iOS-apparaat. U kunt geen Apple pushmeldingen verzenden via de iOS-simulator.

1. Voer de app uit en controleer of de registratie is gelukt en druk vervolgens op **OK**.

    ![De registratie van pushmeldingen in iOS-apps testen][33]

2. U kunt een pushmelding verzenden via [Azure Portal], zoals hierboven beschreven. Als u code voor het verzenden van pushmeldingen in de app hebt toegevoegd, gaat u naar het tekstvak om een meldingsbericht in te voeren. Druk op de knop **Verzenden** op het toetsenbord of op de knop **Melding verzenden** in de weergave om het meldingsbericht te verzenden.

    ![Het verzenden van pushmeldingen in iOS-apps testen][34]

3. De pushmelding wordt verzonden naar alle apparaten die zijn geregistreerd voor het ontvangen van meldingen van de specifieke Notification Hub.

    ![Het ontvangen van pushmeldingen in iOS-apps testen][35]


##Volgende stappen

In dit eenvoudige voorbeeld hebt u pushmeldingen uitgezonden naar al uw geregistreerde iOS-apparaten. U kunt het beste de zelfstudie [Azure Notification Hubs - Een melding naar iOS-gebruikers verzenden met .NET back-end] doornemen. Hierin leert u hoe u een back-end kunt maken om pushmeldingen te verzenden met tags. 

Zie de zelfstudie [Notification Hubs gebruiken om belangrijk nieuws te verzenden] als u gebruikers wilt indelen op belangengroep. 

Zie [Richtlijnen voor Notification Hubs] voor algemene informatie over Notification Hubs.



<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[iOS SDK Mobile Services versie 1.2.4]: http://aka.ms/kymw2g
[iOS SDK voor Mobile Services]: http://go.microsoft.com/fwLink/?LinkID=266533
[Een app-pagina verzenden]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mijn toepassingen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK voor Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Aan de slag met Mobile Services]: /develop/mobile/tutorials/get-started-ios
[klassieke Azure-portal]: https://manage.windowsazure.com/
[Richtlijnen voor Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Aan de slag met pushmeldingen in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs - Een melding naar iOS-gebruikers verzenden met .NET back-end]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs gebruiken om belangrijk nieuws te verzenden]: notification-hubs-ios-send-breaking-news.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure Portal]: https://portal.azure.com


<!--HONumber=Jun16_HO2-->


