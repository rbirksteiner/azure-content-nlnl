<properties
    pageTitle="Aan de slag met Azure Notification Hubs voor Kindle-apps | Microsoft Azure"
    description="In deze zelfstudie leert u hoe u met Azure Notification Hubs pushmeldingen verstuurt naar een Kindle-toepassing."
    services="notification-hubs"
    documentationCenter=""
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-kindle"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="02/29/2016"
    ms.author="wesmc"/>

# Aan de slag met Azure Notification Hubs voor Kindle-apps

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Overzicht

In deze zelfstudie ziet u hoe u met Azure Notification Hubs pushmeldingen verstuurt naar een Kindle-toepassing.
U maakt een lege Kindle-app die pushmeldingen ontvangt via Amazon Device Messaging (ADM).

##Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

+ Download de Android-SDK (aannemende dat u Eclipse gebruikt) van de <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android-site</a>.
+ Volg de stappen in <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">Uw ontwikkelingsomgeving instellen</a> om uw ontwikkelingsomgeving voor Kindle in te stellen.

##Een nieuwe app toevoegen aan de portal voor ontwikkelaars

1. Maak een app in de [Amazon-portal voor ontwikkelaars].

    ![][0]

2. Kopieer de **toepassingssleutel**.

    ![][1]

3. Klik in de portal op de naam van uw app en klik vervolgens op het tabblad **Device Messaging**.

    ![][2]

4. Klik op **Een nieuw beveiligingsprofiel maken** en maak vervolgens een nieuw beveiligingsprofiel (bijvoorbeeld **TestAdm-beveiligingsprofiel**). Klik vervolgens op **Opslaan**.

    ![][3]

5. Klik op **Beveiligingsprofielen** om het beveiligingsprofiel weer te geven dat u zojuist hebt gemaakt. Kopieer de waarden voor **Client-id** en **Clientgeheim** voor later gebruik.

    ![][4]

## Maak een API-sleutel.

1. Open een opdrachtprompt met beheerdersbevoegdheden.
2. Navigeer naar de map Android SDK.
3. Voer de volgende opdracht in:

        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

    ![][5]

4.  Voor het **KeyStore**-wachtwoord typt u **android**.

5.  Kopieer de **MD5**-vingerafdruk.
6.  Klik in de portal voor ontwikkelaars op het tabblad **Berichten**, klik op **Android/Kindle** en voer de naam in van het pakket voor uw app (bijvoorbeeld **com.sample.notificationhubtest**) en de **MD5**-waarde en klik vervolgens op **API-sleutel genereren**.

## Referenties aan de hub toevoegen

Voeg in de portal het clientgeheim en de client-id in op het tabblad **Configureren** van uw Notification Hub.

## Uw toepassing instellen

> [AZURE.NOTE] Wanneer u een toepassing maakt, gebruikt u ten minste API-niveau 17.

De ADM-bibliotheken aan uw Eclipse-project toevoegen:

1. Als u de ADM-bibliotheek wilt verkrijgen, [downloadt u de SDK]. Pak het SDK-zipbestand uit.
2. Klik in Eclipse met de rechtermuisknop op het project en klik vervolgens op **Eigenschappen**. Selecteer **JavaBuild-pad** aan de linkerkant en selecteer vervolgens het tabblad **Bibliotheken** boven aan het venster. Klik op **Externe jar toevoegen** en selecteer het bestand `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` in de map waaruit u de Amazon SDK hebt opgehaald.
3. Download de NotificationHubs Android-SDK (koppeling).
4. Pak het pakket uit en sleep het bestand `notification-hubs-sdk.jar` in de `libs` map in Eclipse.

Uw app-manifest bewerken voor ondersteuning van ADM:

1. De Amazon-naamruimte in het basismanifestelement toevoegen:


        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. Voeg machtigingen toe als het eerste element onder het manifestelement. Vervang **[NAAM VAN UW PAKKET]**door de naam het pakket dat u hebt gebruikt om uw app te maken.

        <permission
         android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
         android:protectionLevel="signature" />

        <uses-permission android:name="android.permission.INTERNET"/>

        <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

        <!-- This permission allows your app access to receive push notifications
        from ADM. -->
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

        <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Voeg het volgende element in als het eerste onderliggende item van het toepassingselement. Vervang **[NAAM VAN UW SERVICE]** door de ADM-berichtenhandler die u maakt in het volgende gedeelte (inclusief het pakket) en wijzig **[NAAM VAN UW PAKKET]** in de pakketnaam waarmee u uw app hebt gemaakt.

        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />

        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />

            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
          <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
          <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

          <!-- Replace the name in the category tag with your app's package name. -->
          <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>

## De ADM-berichtenhandler maken

1. Maak een nieuwe klasse die eigenschappen overneemt van `com.amazon.device.messaging.ADMMessageHandlerBase` en geef deze de naam `MyADMMessageHandler`, zoals wordt weergegeven in de volgende afbeelding:

    ![][6]

2. Voeg de volgende `import` instructies toe:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub

3. Voeg de volgende code toe aan de klasse die u hebt gemaakt. Vervang de hubnaam en de verbindingsreeks (luisteren):

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }

        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
            }

            public static class Receiver extends ADMMessageReceiver
            {
                public Receiver()
                {
                    super(MyADMMessageHandler.class);
                }
            }

            private void sendNotification(String msg) {
                Context ctx = getApplicationContext();

             mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                .setSmallIcon(R.mipmap.ic_launcher)
                .setContentTitle("Notification Hub Demo")
                .setStyle(new NotificationCompat.BigTextStyle()
                         .bigText(msg))
                .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

4. Voeg de volgende code toe aan de methode `OnMessage()`:

        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);

5. Voeg de volgende code toe aan de methode `OnRegistered`:

            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }

6.  Voeg de volgende code toe aan de methode `OnUnregistered`:

            try {
                getNotificationHub(getApplicationContext()).unregister();
            } catch (Exception e) {
                Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
            }

7. Voeg in de methode `MainActivity` de volgende importinstructie toe:

        import com.amazon.device.messaging.ADM;

8. Voeg de volgende code toe aan het einde van de methode `OnCreate`:

        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                       MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }

## Uw API-sleutel aan uw app toevoegen

1. Maak in Eclipse een nieuw bestand met de naam **api_key.txt** in de map van uw project.
2. Open het bestand en kopieer de API-sleutel die u in de Amazon-portal voor ontwikkelaars hebt gegenereerd.

## De app uitvoeren

1. Start de emulator.
2. Veeg in de emulator vanaf de bovenkant en klik op **Instellingen**. Klik vervolgens op **Mijn account** en meld u aan met een geldig Amazon-account.
3. Voer vervolgens de app uit in Eclipse.

> [AZURE.NOTE] Als er een probleem optreedt, controleert u het tijdstip van de emulator (of het apparaat). De tijdswaarde moet juist zijn. Voer de volgende opdracht uit de map Android SDK platform-hulpprogramma's uit als u de tijd van de Kindle-emulator wilt wijzigen:

        adb shell  date -s "yyyymmdd.hhmmss"

## Een bericht verzenden

Een bericht verzenden met .NET:

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[Amazon-portal voor ontwikkelaars]: https://developer.amazon.com/home.html
[downloadt u de SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png



<!--HONumber=Jun16_HO2-->


