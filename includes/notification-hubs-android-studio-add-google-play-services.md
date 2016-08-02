1. Open Android SDK Manager door te klikken op het pictogram op de werkbalk van Android Studio of door in het menu te klikken op **Extra** -> **Android** -> **SDK Manager**. Zoek naar de doelversie van de Android SDK die in uw project wordt gebruikt, open deze door te klikken op **Pakketgegevens weergeven** en kies **Google API's**, als deze nog niet is geïnstalleerd.

2. Klik op het tabblad **SDK-hulpprogramma's** . Als u Google Play Service nog niet hebt geïnstalleerd, klikt u op **Google Play Services** zoals hieronder weergegeven. Klik vervolgens op **Toepassen** om met de installatie te beginnen. 
 
    Noteer het SDK-pad om het in een later stadium te kunnen gebruiken. 

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)


3. Open het bestand **build.gradle** in de app-map.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)

4. Voeg deze regel toe onder *Afhankelijkheden*: 

        compile 'com.google.android.gms:play-services-gcm:8.4.0'

5. Onder *defaultConfig* wijzigt u *minSdkVersion* in 9.
 
6. Klik op het pictogram **Project met Gradle-bestanden synchroniseren** in de werkbalk.

7. Open **AndroidManifest.xml** en voeg dit label toe aan het label *toepassing*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 







<!--HONumber=Jun16_HO2-->


