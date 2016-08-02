<properties
    pageTitle="Aan de slag met Azure Mobile Engagement voor Cordova/Phonegap"
    description="Informatie over het gebruik van Azure Mobile Engagement met analyses en pushmeldingen voor Cordova-/Phonegap-apps."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-phonegap"
    ms.devlang="js"
    ms.topic="hero-article" 
    ms.date="04/04/2016"
    ms.author="piyushjo" />

# Aan de slag met Azure Mobile Engagement voor Cordova/Phonegap

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In dit onderwerp leest u hoe u Azure Mobile Engagement gebruikt om inzicht te krijgen in het gebruik van uw apps en het verzenden van pushmeldingen aan gesegmenteerde gebruikers van een mobiele toepassing die is ontwikkeld met Cordova.

In deze zelfstudie maken we een lege Cordova-app met een Mac en integreren we vervolgens de Mobile Engagement SDK. Deze app verzamelt analytische basisgegevens en ontvangt pushmeldingen via Apple Push Notification System (APNS) voor iOS en Google Cloud Messaging (GCM) voor Android. We implementeren de app op een iOS- of Android-apparaat voor testdoeleinden. 

> [AZURE.NOTE] U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started) voor meer informatie.

Voor deze zelfstudie hebt u het volgende nodig:

+ XCode, dat u vanuit de Mac App Store kunt installeren (voor de iOS-implementatie)
+ [Android SDK & Emulator](http://developer.android.com/sdk/installing/index.html) (voor de Android-implementatie)
+ Pushmeldingscertificaat (.p12), dat u kunt verkrijgen via Apple Dev Center voor APNS
+ GCM-projectnummer, dat u kunt vinden in uw Google Developer-Console voor GCM
+ [Cordova-invoegtoepassing voor Mobile Engagement](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE] U vindt de broncode en het Leesmij-bestand voor de Cordova-invoegtoepassing in [Github](https://github.com/Azure/azure-mobile-engagement-cordova)

##<a id="setup-azme"></a>Mobile Engagement instellen voor uw Cordova-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Uw app verbinden met de back-end van Mobile Engagement

Deze zelfstudie toont een 'basisintegratie', de minimale set die vereist is voor het verzamelen van gegevens en verzenden van een pushmelding. 

We gaan een eenvoudige app maken met Cordova ter illustratie van de integratie.

###Nieuw Cordova-project maken

1. Start het *Terminal*-venster op uw Mac-computer en typ het volgende, waarmee een nieuw Cordova-project wordt gemaakt op basis van de standaardsjabloon. Zorg ervoor dat het publicatieprofiel waarmee u de iOS-app uiteindelijk zult implementeren 'com.mycompany.myapp' gebruikt als de App ID. 

        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova

2. Voer het volgende uit om uw project te configureren voor **iOS** en voer het uit in de iOS-simulator:

        $ cordova platform add ios 
        $ cordova run ios

3. Voer het volgende uit om uw project te configureren voor **Android** en voer het uit in de Android-emulator: Zorg ervoor dat in de instellingen van uw Android SDK Emulator Google API's (Google Inc.) wordt gebruikt als Target, met de CPU/ABI als Google API's ARM.  

        $ cordova platform add android
        $ cordova run android

4.  Voeg de invoegtoepassing Cordova Console toe. 

        $ cordova plugin add cordova-plugin-console 

###Uw app verbinden met de back-end van Mobile Engagement

1. Installeer de Cordova-invoegtoepassing voor Azure Mobile Engagement en geef de volgende waarden van variabelen op voor het configureren van de invoegtoepassing:

        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
            --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Android Reach Icon*: dit moet de naam van de resource zijn zonder een extensie of drawable voorvoegsel (voorbeeld: mynotificationicon), en het pictogrambestand moet worden gekopieerd naar uw Android-project (platforms/android/res/drawable)

*iOS Reach Icon*: dit moet de naam van de resource zijn met de extensie (bijvoorbeeld: mynotificationicon.png), en het pictogrambestand moet worden toegevoegd aan uw iOS-project met XCode (via het menu Add Files)

##<a id="monitor"></a>Realtime-bewaking inschakelen

1. Ga naar het Cordova-project, bewerk **www/js/index.js** en voeg de aanroep van Mobile Engagement toe om een nieuwe activiteit te declareren zodra de gebeurtenis *deviceReady* is ontvangen.

         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }

2. Voer de toepassing uit:
        
    - **Voor iOS**
    
        In het venster `Terminal` start u uw app in een nieuw exemplaar van de simulator door het volgende uit te voeren:

            cordova run ios

    - **Voor Android**
        
        In het venster `Terminal` start u uw app in een nieuw exemplaar van de emulator door het volgende uit te voeren:

            cordova run android

3. U ziet het volgende in de logboeken van de console:

        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

##<a id="monitor"></a>App verbinden met realtime-bewaking

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Pushmeldingen en in-app-berichten inschakelen

Met Mobile Engagement kunt u communiceren met uw gebruikers via pushmeldingen en in-app-berichten in de context van campagnes. Deze module heet REACH in de Mobile Engagement-portal.
In de volgende secties stelt u de app in om die te ontvangen.

###Pushreferenties configureren voor Mobile Engagement

Om Mobile Engagement pushmeldingen te laten verzenden namens u, moet u het toegang geven tot uw certificaat voor Apple iOS of de API-sleutel van GCM Server. 
    
1. Navigeer naar uw Mobile Engagement-portal. Zorg ervoor dat u zich bevindt in de app die we gebruiken voor dit project en klik vervolgens op de knop **Engage** onderaan:
    
    ![][1]
    
2. U gaat naar de instellingenpagina van de Engagement-portal. Klik op de sectie **Native pushbericht**:
    
    ![][2]

3. iOS-certificaat/API-sleutel GCM Server configureren

    **[iOS]**

    a. Selecteer uw .p12, upload het en typ uw wachtwoord:
    
    ![][3]

    **[Android]**

    a. Klik op het bewerkingspictogram vóór **API-sleutel** in de sectie GCM-instellingen. In het pop-upvenster dat wordt weergegeven, plakt u de GCM Server-sleutel. Klik vervolgens op **OK**. 
        
    ![][4]

###Pushmeldingen inschakelen in de Cordova-app

Bewerk **www/js/index.js** om de aanroep van Mobile Engagement voor het aanvragen van pushmeldingen toe te voegen en een handler te declareren:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                // on OpenUrl  
                function(_url) {   
                alert(_url);   
                });  
            Engagement.startActivity("myPage",{});  
        }

###De app uitvoeren

**[iOS]**

1. We gebruiken XCode voor het bouwen en implementeren van de app op het apparaat om pushmeldingen te testen, omdat iOS alleen pushmeldingen naar een daadwerkelijk apparaat toestaat. Ga naar de locatie waar uw Cordova-project is gemaakt en navigeer naar de locatie **...\platforms\ios**. Open het systeemeigen .xcodeproj-bestand in XCode. 
    
2. Bouw en implementeer de Cordova-app op het iOS-apparaat met het account dat beschikt over het inrichtingsprofiel met het certificaat dat u zojuist hebt geüpload en de app-id die overeenkomt met de id die u hebt opgegeven bij het maken van de Cordova-app. Raadpleeg de *Bundle identifier* in het bestand **Resources\*-info.plist** in XCode om te zorgen dat ze overeenkomen. 

3. U krijgt de standaard iOS-pop-up te zien op uw apparaat, waarin wordt gemeld dat de app verzoekt om machtigingen voor het verzenden van meldingen Geef toestemming. 

**[Android]**

U kunt eenvoudigweg de emulator gebruiken om de Android-app uit te voeren, omdat GCM-meldingen door de Android-emulator worden ondersteund. 

    cordova run android

##<a id="send"></a>Een melding verzenden naar uw app

We gaan nu een eenvoudige pushmeldingcampagne maken waarbij een pushmelding wordt verzonden naar uw app op het apparaat:

1. Navigeer naar het tabblad **Reach** in uw Mobile Engagement-portal.

2. Klik op **Nieuwe aankondiging** om uw pushcampagne te maken.

    ![][6]

3. Informatie invoeren voor het maken van uw campagne **[Android]**
    
    - Geef uw campagne een **naam**. 
    - Selecteer als **Bezorgingstype**  *Systeemmelding* *Eenvoudig*.
    - Selecteer als **Leveringstijd** *Elk tijdstip*.
    - Geef een **titel** op voor de melding die als eerste zal worden verzonden.
    - Geef een **bericht** op als de berichttekst van de melding. 

    ![][11]

4. Informatie invoeren voor het maken van uw campagne **[iOS]**

    - Geef uw campagne een **naam**. 
    - Selecteer als **Leveringstijd** *Alleen buiten app*.
    - Geef een **titel** op voor de melding die als eerste zal worden verzonden.
    - Geef een **bericht** op als de berichttekst van de melding. 
 
    ![][12]

5. Blader naar beneden en selecteer in het gedeelte inhoud **Alleen melding**.

    ![][8]

6. [Optioneel] U kunt ook een actie-URL opgeven. Zorg ervoor dat die gebruikmaakt van een URL-schema dat wordt opgegeven bij het configureren van de variabele **AZME\_REDIRECT\_URL** van de invoegtoepassing, bijvoorbeeld *myapp://test*.  

7. U hebt nu een campagne gemaakt die zo eenvoudig mogelijk is. Blader nogmaals naar beneden en klik op de knop **Maken** om uw campagne op te slaan.

8. Ten slotte moet u uw campagne **activeren**.
    
    ![][10]

9. U ziet nu een pushmelding op het apparaat of de emulator als onderdeel van deze campagne. 

##<a id="next-steps"></a>Volgende stappen
[Overzicht van alle methoden beschikbaar met Cordova Mobile Engagement SDK](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png




<!--HONumber=Jun16_HO2-->


