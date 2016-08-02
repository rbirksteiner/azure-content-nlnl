<properties
    pageTitle="Aan de slag met Notification Hubs die gebruikmaken van Baidu | Microsoft Azure"
    description="In deze zelfstudie leert u hoe u met Azure Notification Hubs pushmeldingen verzendt naar Android-apparaten die gebruikmaken van Baidu."
    services="notification-hubs"
    documentationCenter="android"
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="mobile-baidu"
    ms.workload="mobile"
    ms.date="05/05/2016"
    ms.author="wesmc"/>

# Aan de slag met Azure Notification Hubs die gebruikmaken van Baidu

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Overzicht

Baidu Cloud Push is een Chinese cloudservice waarmee u pushmeldingen naar mobiele apparaten kunt verzenden. Deze service is met name handig in China, omdat het leveren van pushmeldingen aan Android daar complex is vanwege de aanwezigheid van verschillende app stores en pushservices. Bovendien zijn er Android-apparaten die niet standaard verbonden zijn met GCM (Google Cloud Messaging).

##Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

+ Android-SDK (aannemende dat u Eclipse gebruikt), die u kunt downloaden van de <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android-site.</a>
+ [Android SDK voor Mobile Services]
+ [Android SDK Baidu Push]

>[AZURE.NOTE] U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F) voor meer informatie.


##Een Baidu-account maken

Voor het gebruik van Baidu moet u een Baidu-account hebben. Als u er al een hebt, meldt u zich aan bij de [Baidu Portal] en gaat u verder met de volgende stap. Als u nog geen account hebt, raadpleegt u de instructies hieronder voor het maken van een Baidu-account.  

1. Ga naar de [Baidu Portal] en klik op de koppeling **登录** (**Aanmelden**). Klik op **立即注册** om de registratie van het account te starten.

    ![][1]

2. Voer de vereiste gegevens in: telefoon, e-mailadres, wachtwoord en verificatiecode en klik vervolgens op **Registreren**.

    ![][2]

3. U ontvangt een e-mailbericht op het e-mailadres dat u hebt ingevoerd met daarin een koppeling om uw Baidu-account te activeren.

    ![][3]

4. Meld u aan bij uw e-mailaccount, open het e-mailbericht en klik op de activeringskoppeling om uw Baidu-account te activeren.

    ![][4]

Als uw Baidu-account is geactiveerd, meldt u zich aan bij de [Baidu Portal].

##Registreer u als een Baidu-ontwikkelaar.

1. Nadat u zich hebt aangemeld bij de [Baidu Portal], klikt u op **更多 >>** (**meer**).

    ![][5]

2. Schuif omlaag in het gedeelte **站长与开发者服务 (Webmaster- en Ontwikkelaarsservices)** en klik op **百度开放云平台** (**Baidu-opencloudplatform**).

    ![][6]

3. Klik op de volgende pagina op **开发者服务** (**Ontwikkelaarsservices**) in de rechterbovenhoek.

    ![][7]

4. Klik op de volgende pagina op **注册开发者** (**Geregistreerde ontwikkelaars**) in het menu in de rechterbovenhoek.

    ![][8]

5. Voer uw naam, een beschrijving en een mobiel telefoonnummer in voor het ontvangen van een sms-bericht voor verificatie en klik vervolgens op **送验证码** (**Verificatiecode verzenden**). Houd er rekening mee dat u voor een internationaal telefoonnummer de landcode tussen haakjes moet plaatsen. Voor een nummer in de Verenigde Staten moet u bijvoorbeeld de volgende indeling gebruiken: **(1)1234567890**.

    ![][9]

6. U ontvangt vervolgens een sms-bericht met een verificatiecode, zoals weergegeven in het volgende voorbeeld:

    ![][10]

7. Voer de verificatiecode uit het sms-bericht in **验证码** (**Bevestigingscode**) in.

8. Voltooi de registratie voor ontwikkelaars door de Baidu-overeenkomst te accepteren en te klikken op **提交** (**Verzenden**). Als de registratie is gelukt, verschijnt de volgende pagina:

    ![][11]

##Een Baidu-cloudpushproject maken

Als u een Baidu-cloudpushproject maakt, ontvangt u uw app-id, API-sleutel en een geheime sleutel.

1. Nadat u zich hebt aangemeld bij de [Baidu Portal], klikt u op **更多 >>** (**meer**).

    ![][5]

2. Schuif omlaag in het gedeelte **站长与开发者服务** (**Webmaster- en Ontwikkelaarsservices)** en klik op **百度开放云平台** (**Baidu-opencloudplatform**).

    ![][6]

3. Klik op de volgende pagina op **开发者服务** (**Ontwikkelaarsservices**) in de rechterbovenhoek.

    ![][7]

4. Klik op de volgende pagina op **云推送** (**Cloud Push**) in het gedeelte **云服务** (**Cloud Services**).

    ![][12]

5. Als u zich hebt geregistreerd als ontwikkelaar, ziet u **管理控制台** (**Beheerconsole**) in het bovenste menu. Klik op **开发者服务管理** (**Beheer van ontwikkelaarsservices**).

    ![][13]

6. Klik op de volgende pagina **创建工程** (**Project maken**).

    ![][14]

7. Voer een toepassingsnaam in en klik op **创建** (**Maken**).

    ![][15]

8. Als u een Baidu-cloudpushproject hebt gemaakt, ziet u een pagina met de **App-id**, de **API-sleutel** en een **geheime sleutel**. Noteer de API-sleutel en de geheime sleutel. U hebt deze later nodig.

    ![][16]

9. Configureer het project voor pushmeldingen door te klikken op **云推送** (**Cloud Push**) in het linkerdeelvenster.

    ![][31]

10. Klik op de volgende pagina op de knop **推送设置** (**Pushinstellingen**).

    ![][32]  

11. Voeg op de configuratiepagina de pakketnaam toe die u wilt gebruiken in uw Android-project in het veld **应用包名** (**Toepassingspakket**) en klik vervolgens op **保存设置** (**Opslaan**).  

    ![][33]

U ziet het bericht **保存成功！** (**Opgeslagen!**).

##Uw Notification Hub configureren

1. Meld u aan bij de [klassieke Azure-portal] en klik op **+NIEUW** onder aan het scherm.

2. Klik achtereenvolgens op **App Services**, **Service Bus**, **Notification Hub** en **Snelle invoer**.

3. Geef een naam op voor uw **Notification Hub**, selecteer de **regio** en de **naamruimte** waar deze Notification Hub wordt gemaakt en klik vervolgens op **Een nieuwe Notification Hub maken**.  

    ![][17]

4. Klik op de naamruimte waarin u de Notification Hub hebt gemaakt en klik vervolgens op **Notification Hubs** bovenin.

    ![][18]

5. Selecteer de Notification Hub die u hebt gemaakt en klik vervolgens op **Configureren** in het bovenste menu.

    ![][19]

6. Schuif omlaag naar het gedeelte **Baidu-instellingen voor meldingen** en voer voor uw Baidu-cloudpushproject de API-sleutel en de geheime sleutel in die u eerder van de Baidu-console hebt ontvangen. Klik op **Opslaan**.

    ![][20]

7. Klik op het tabblad **Dashboard** in de rechterbovenhoek van de Notification Hub en klik vervolgens op **Verbindingsreeks weergeven**.

    ![][21]

8. Noteer de **DefaultListenSharedAccessSignature** en **DefaultFullSharedAccessSignature** in het venster **Toegang tot de verbindingsgegevens**.

    ![][22]

##Uw app verbinden met de Notification Hub

1. Maak in Eclipse ADT een nieuw Android-project (**Bestand** > **Nieuw** > **Android-toepassingsproject**).

    ![][23]

2. Voer een **toepassingsnaam** in en zorg ervoor dat de **minimale vereiste SDK**-versie is ingesteld op **API 16: Android 4.1**.

    ![][24]

3. Klik op **Volgende** en doorloop de wizard totdat het venster **Activiteit maken** wordt weergegeven. Zorg ervoor dat **Lege activiteit** is geselecteerd en selecteer vervolgens **Voltooien** om een nieuwe Android-toepassing te maken.

    ![][25]

4. Zorg ervoor dat het **doel van de projectbuild** juist is ingesteld.

    ![][26]

5. Download het bestand notification-hubs-0.4.jar van het tabblad **Bestanden** van de [Notification-Hubs-Android-SDK op Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Voeg het bestand toe aan de map **bibliotheken** van uw Eclipse-project en vernieuw de map *bibliotheken*.

6. Download de [Android SDK Baidu Push] en pak deze uit. Open de map **bibliotheken** en kopieer het **pushservice x.y.z** jar-bestand en de **armeabi** & **mips**-mappen in de map **bibliotheken** van uw Android-toepassing.

7. Open het bestand **AndroidManifest.xml** van uw Android-project en voeg de machtigingen toe die voor de SDK Baidu zijn vereist.

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Voeg de eigenschap **android:name** toe aan uw **toepassings**element in **AndroidManifest.xml**, waarbij u *yourprojectname* (bijvoorbeeld **com.example.BaiduTest**) vervangt. Zorg ervoor dat deze naam overeenkomt met de naam die u in de Baidu-console hebt geconfigureerd.

        <application android:name="yourprojectname.DemoApplication"

9. Voeg de volgende configuratie toe aan het toepassingselement na het activiteitelement **.MainActivity**, waarbij u *yourprojectname* (bijvoorbeeld **com.example.BaiduTest**) vervangt.

        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>

        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Voeg een nieuwe klasse met de naam **ConfigurationSettings.java** toe aan het project.

    ![][28]

    ![][29]

10. Voeg de volgende code toe aan de klasse:

        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }

    Stel de waarde van **API_KEY** in op de waarde die u eerder hebt opgehaald uit het Baidu-cloudproject, geef voor **NotificationHubName** de naam op van de Notification Hub van de klassieke Azure-portal en geef voor **NotificationHubConnectionString** DefaultListenSharedAccessSignature op van de klassieke Azure-portal.

11. Voeg een nieuwe klasse toe met de naam **DemoApplication.java** en voeg de volgende code toe:

        import com.baidu.frontia.FrontiaApplication;

        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }

12. Voeg nog een nieuwe klasse toe met de naam **MyPushMessageReceiver.java** en voeg de code onderaan toe. Dit is de klasse die verantwoordelijk is voor de pushmeldingen die worden ontvangen van de Baidu-push-server.

        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();

            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;

                try {
                 if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }

                registerWithNotificationHubs();
            }

            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }

            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }

            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }

            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }

13. Open **MainActivity.java** en voeg het volgende toe aan de methode **onCreate**:

            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

14. Open bovenin de volgende importinstructies:

            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

##Pushmeldingen naar uw app verzenden


U kunt de ontvangst van meldingen in uw app testen door meldingen in de klassieke Azure-portal te verzenden via het foutopsporingstabblad op de Notification Hub (zie onderstaande scherm).

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-debug.png)

Pushmeldingen worden gewoonlijk in een back-endservice zoals Mobile Services of ASP.NET verzonden met een compatibele bibliotheek. U kunt de REST API ook direct gebruiken om meldingsberichten te verzenden als er geen bibliotheek beschikbaar is voor uw back-end.

In deze zelfstudie houden we het eenvoudig en wordt alleen gedemonstreerd hoe u uw clientapp test door meldingen te verzenden met de .NET SDK voor Notification Hubs in een consoletoepassing in plaats van een back-endservice. U kunt het beste de zelfstudie [Notification Hubs gebruiken om pushmeldingen naar gebruikers te verzenden](notification-hubs-aspnet-backend-windows-dotnet-notify-users.md) doornemen voor informatie over het verzenden van meldingen vanuit een ASP.NET-back-end. Voor het verzenden van meldingen kunt u echter de volgende methoden gebruiken:

* **REST-interface**: u kunt meldingen op elk back-endplatform ondersteunen met de [REST-interface](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Microsoft Azure Notification Hubs .NET SDK**: in NuGet Package Manager voor Visual Studio voert u [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) uit.

* **Node.js**: [Notification Hubs gebruiken vanuit Node.js](notification-hubs-nodejs-how-to-use-notification-hubs.md).

* **Azure Mobile Services**: zie [Pushmeldingen naar uw Mobile Services-app verzenden](../mobile-services/mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md) voor een voorbeeld van hoe u meldingen verzendt vanuit een back-end van Azure Mobile Services die is geïntegreerd met Notification Hubs.

* **Java/PHP**: zie 'Notification Hubs gebruiken vanuit Java/PHP' voor een voorbeeld van hoe u meldingen verzendt met de REST API's ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

##(Optioneel) Meldingen verzenden vanuit een .NET-console-app

In dit gedeelte behandelen we hoe u een melding vanuit een .NET-console-app kunt verzenden.

1. Maak een nieuwe Visual C#-consoletoepassing:

    ![][30]

2. Stel in het venster Package Manager-console het **standaardproject** in op uw nieuwe consoletoepassingsproject en voer vervolgens in het consolevenster de volgende opdracht uit:

        Install-Package Microsoft.Azure.NotificationHubs

    Hiermee wordt een verwijzing toegevoegd aan de Azure Notification Hubs SDK met het <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet-pakket</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Open het bestand **Program.cs** en voeg de volgende gebruiksinstructie toe:

        using Microsoft.Azure.NotificationHubs;

4. Voeg in uw klasse `Program` de volgende methode toe en vervang *DefaultFullSharedAccessSignatureSASConnectionString* en *NotificationHubName* door de waarden die u hebt.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }

5. Voeg de volgende regels in de **Main**-methode toe:

         SendNotificationAsync();
         Console.ReadLine();

##Uw app testen

Als u uw app met een telefoon wilt testen, sluit u de telefoon met een USB-kabel op uw computer aan. Uw app wordt naar de gekoppelde telefoon geladen.

Om deze app met een emulator te testen, klikt u in de bovenste werkbalk van Eclipse op **Uitvoeren** en selecteert u vervolgens uw app. Hiermee start u de emulator en laadt en opent u de app.

De app haalt de gebruikers-id en de channelId op uit de Baidu Push Notification Service en registreert zich bij de Notification Hub.

U kunt vanaf het foutopsporingstabblad van de klassieke Azure-portal een testmelding verzenden. Als u de .Net-console-toepassing hebt gebouwd voor Visual Studio, drukt u in Visual Studio op F5 om de toepassing te starten. De toepassing verstuurt een melding. Deze verschijnt in het bovenste gedeelte voor meldingen op uw apparaat of emulator.


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Android SDK voor Mobile Services]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Android SDK Baidu Push]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[klassieke Azure-portal]: https://manage.windowsazure.com/
[Baidu Portal]: http://www.baidu.com/



<!--HONumber=Jun16_HO2-->


