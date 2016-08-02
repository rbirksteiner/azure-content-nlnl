
###Manifestbestand bijwerken om meldingen in te schakelen

Kopieer de onderstaande resources voor in-app-meldingen naar uw Manifest.xml tussen de labels `<application>` en `</application>`.

        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
        </activity>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
        </receiver>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
        </receiver>

###Een pictogram voor meldingen opgeven

Plak het volgende XML-fragment in het bestand Manifest.xml tussen de labels `<application>` en `</application>`.

        <meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Zo definieert u welk pictogram er bij systeemmeldingen en in in-app-meldingen wordt weergegeven. Het is optioneel voor in-app-meldingen maar verplicht voor systeemmeldingen. In Android worden systeemmeldingen met ongeldige pictogrammen geweigerd.

Controleer of u een pictogram gebruikt dat in een van de **drawable**-mappen (zoals ``engagement_close.png``) voorkomt. **mipmap**-map wordt niet ondersteund.

>[AZURE.NOTE] Gebruik niet het pictogram van het **startprogramma voor toepassingen**. Dit heeft een andere resolutie en bevindt zich doorgaans in de mipmap-mappen, die niet worden ondersteund.

Voor echte apps kunt u een pictogram gebruiken dat geschikt is voor meldingen volgens de [Android-ontwerprichtlijnen](http://developer.android.com/design/patterns/notifications.html).

>[AZURE.TIP] Om er zeker van te zijn dat de juiste resoluties voor pictogrammen worden gebruikt, kunt u [deze voorbeelden](https://www.google.com/design/icons) bekijken.
Schuif omlaag naar de sectie **Melding**, klik op een pictogram en klik vervolgens op `PNGS` om de drawable-set voor het pictogram te downloaden. U kunt zien welke drawable-mappen met welke resolutie moeten worden gebruikt voor elke versie van het pictogram.

###Ontvangen van GCM-pushmeldingen inschakelen voor de app

1. Plak het volgende in uw Manifest.xml tussen de labels `<application>` en `</application>` nadat u het `project number` hebt vervangen dat u hebt opgehaald uit de Google Play-console. De \n is opzettelijk dus zorg ervoor dat het projectnummer hiermee eindigt.

        <meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Plak de onderstaande code in het bestand Manifest.xml tussen de labels `<application>` en `</application>`. Vervang de pakketnaam <Your package name>.

        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
        android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<Your package name>" />
            </intent-filter>
        </receiver>

3. Voeg de laatste reeks machtigingen die zijn gemarkeerd vóór het label `<application>` toe. Vervang `<Your package name>` door de werkelijke pakketnaam van uw toepassing.

        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
        <permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />







<!--HONumber=Jun16_HO2-->


