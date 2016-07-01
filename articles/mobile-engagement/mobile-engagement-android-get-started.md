<properties
    pageTitle="Aan de slag met Azure Mobile Engagement"
    description="Informatie over het gebruik van Azure Mobile Engagement met analyses en pushmeldingen voor Android-apps."
    services="mobile-engagement"
    documentationCenter="android"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="05/12/2016"
    ms.author="piyushjo;ricksal" />

# Aan de slag met Azure Mobile Engagement voor Android-apps

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In dit onderwerp leest u hoe u Azure Mobile Engagement gebruikt om inzicht te krijgen in het gebruik van uw apps, en om pushmeldingen te verzenden aan gesegmenteerde gebruikers van een Android-toepassing.
Deze zelfstudie laat een eenvoudig broadcast-scenario met Mobile Engagement zien. U maakt een lege Android-app die basisgegevens verzamelt en pushmeldingen ontvangt via Google Cloud Messaging (GCM).

## Vereisten

Voltooiing van deze zelfstudie vereist de [Android-hulpprogramma's voor ontwikkelaars](https://developer.android.com/sdk/index.html), waaronder de Integrated Development Environment van Android Studio en het meest recente Android-platform.

Ook is [Mobile Engagement Android SDK](https://aka.ms/vq9mfn) vereist.

> [AZURE.IMPORTANT] U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started) voor meer informatie.

## Mobile Engagement instellen voor uw Android-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

## Uw app verbinden met de back-end van Mobile Engagement

Deze zelfstudie toont een 'basisintegratie', de minimale set die vereist is voor het verzamelen van gegevens en verzenden van een pushmelding. De volledige integratiedocumentatie is te vinden in de [Mobile Engagement Android SDK-integratie](mobile-engagement-android-sdk-overview.md)

We gaan een eenvoudige app maken met Android Studio ter illustratie van de integratie.

### Een nieuw Android-project maken

1. Start **Android Studio** en selecteer **Start a new Android Studio project** in het pop-upvenster.

    ![][1]

2. Geef een naam voor de app en het bedrijfsdomein op. Maak een notitie van wat u invult, want u hebt de informatie later nog nodig. Klik op **Next**.

    ![][2]

3. Selecteer de doel-vormfactor en het API-niveau en klik op **Next**.

    >[AZURE.NOTE] Mobile Engagement vereist minimaal API-niveau 10 (Android 2.3.3).

    ![][3]

4. Selecteer hier **Blank Activity**, dit wordt het enige scherm voor deze app. Klik vervolgens op **Next**.

    ![][4]

5. Laat verder alle standaardinstellingen zoals ze zijn en klik op **Finish**.

    ![][5]

Android Studio maakt nu de demo-app waarin we Mobile Engagement gaan integreren.

### De SDK-bibliotheek opnemen in uw project

1. Download [Mobile Engagement Android SDK].
2. Pak het bestand uit in een map op uw computer.
3. Zoek de JAR-bibliotheek voor de huidige versie van deze SDK en kopieer die naar het Klembord.

      ![][6]

4. Navigeer naar de sectie **Project** (1) en plak de JAR in de map libs (2).

      ![][7]

5. Synchroniseer uw project om de bibliotheek te laden.

      ![][8]

### Uw app verbinden met de back-end van Mobile Engagement via de verbindingsreeks

1. Kopieer de volgende regels code in het maken van de activiteit (dit moet worden uitgevoerd op één plek in de toepassing, meestal de belangrijkste activiteit). Open voor deze voorbeeld-app de MainActivity onder src -> main -> java en voeg het volgende toe:

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Zet de verwijzingen om door op Alt+Enter te drukken of de volgende importinstructies toe te voegen:

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;

3. Ga terug naar de klassieke Azure-portal op de pagina **Verbindingsgegevens** van de app en kopieer de **verbindingsreeks**.

      ![][9]

4. Plak deze in de parameter `setConnectionString` en vervang het voorbeeld zoals hieronder weergegeven:

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### Machtigingen en een servicedeclaratie toevoegen

1. Voeg machtigingen toe aan het bestand Manifest.xml van uw project direct vóór of na de tag `<application>`:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Voeg het volgende toe tussen de tags `<application>` en `</application>` om de agent-service te declareren:

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

3. In de code die u zojuist hebt geplakt, vervangt u `"<Your application name>"` in het label. Dit wordt weergegeven in het menu **Instellingen**, waar gebruikers de services kunnen zien die worden uitgevoerd op het apparaat. U kunt bijvoorbeeld het woord 'Service' aan dat label toevoegen.

### Een scherm naar Mobile Engagement verzenden

Om te beginnen met het verzenden van gegevens en ervoor te zorgen dat de gebruikers actief zijn, moet u ten minste één scherm (activiteit) naar de back-end van Mobile Engagement sturen.

Ga naar **MainActivity.java** en voeg het volgende toe ter vervanging van de basisklasse van **MainActivity** met **EngagementActivity**:

    public class MainActivity extends EngagementActivity {

> [AZURE.NOTE] Als de basisklasse niet *Activity* is, raadpleegt u [Advanced Android Reporting](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes) (Geavanceerde Android-rapportages) voor het overnemen van verschillende klassen.


U moet de volgende regel voor dit eenvoudige voorbeeldscenario uitcommentariëren (uitsluiten):

    // setSupportActionBar(toolbar);

Als u dit wilt behouden, raadpleeg dan [Advanced Android Reporting](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes)  (Geavanceerde Android-rapportages).

## App verbinden met realtime-bewaking

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## Pushmeldingen en in-app-berichten inschakelen

Met Mobile Engagement kunt u communiceren met uw gebruikers en ze bereiken met pushmeldingen en in-app-berichten in de context van campagnes. Deze module heet REACH in de Mobile Engagement-portal.
In de volgende secties stelt u de app in om die te ontvangen.

### SDK-bronnen naar uw project kopiëren

1. Ga terug naar de gedownloade SDK-inhoud en kopieer de map **res**.

    ![][10]

2. Ga terug naar Android Studio, selecteer de map **main** van uw projectbestanden en plak de gekopieerde map om de resources toe te voegen aan het project.

    ![][11]

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## Volgende stappen

Ga naar [Android SDK](mobile-engagement-android-sdk-overview.md) voor uitgebreide informatie over de SDK-integratie.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png



<!--HONumber=Jun16_HO2-->


