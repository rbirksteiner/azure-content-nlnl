<properties
    pageTitle="Pushmeldingen verzenden naar Chrome-apps met Azure Notification Hubs | Microsoft Azure"
    description="In deze zelfstudie leert u hoe u met Azure Notification Hubs pushmeldingen verzendt naar een Chrome-app."
    services="notification-hubs"
    keywords="mobile push notifications,push notifications,push notification,chrome push notifications"
    documentationCenter=""
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-chrome"
    ms.devlang="JavaScript"
    ms.topic="hero-article"
    ms.date="03/21/2016"
    ms.author="wesmc"/>

# Pushmeldingen verzenden naar Chrome-apps met Azure Notification Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

In dit onderwerp wordt beschreven hoe u met Azure Notification Hubs pushmeldingen naar een Chrome-app kunt verzenden in de context van de Google Chrome-browser. In deze zelfstudie maken we een Chrome-app die pushmeldingen ontvangt via [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/). 

>[AZURE.NOTE] U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F) voor meer informatie.

De zelfstudie leidt u door deze eenvoudige stappen om pushmeldingen in te schakelen:

* [Google Cloud Messaging inschakelen](#register)
* [Uw Notification Hub configureren](#configure-hub)
* [Uw Chrome-app verbinden met de Notification Hub](#connect-app)
* [Een pushmelding naar uw Chrome-app verzenden](#send)
* [Extra functionaliteit en mogelijkheden](#next-steps)

>[AZURE.NOTE] Pushmeldingen in een Chrome-app zijn geen algemene meldingen in de browser. Ze zijn specifiek voor het uitbreidbaarheidsmodel van de browser (Zie [Overzicht van Chrome-apps] voor meer informatie). Chrome-apps kunnen niet alleen op de pc worden gebruikt, maar ook op mobiele apparaten (Android en iOS) via Apache Cordova. Zie [Chrome-apps op mobiele apparaten] voor meer informatie.

Het configureren van GCM en Azure Notification Hubs werkt op dezelfde manier als de configuratie voor Android, aangezien [Google Cloud Messaging voor Chrome] niet meer bestaat. Hetzelfde GCM ondersteunt nu Android-apparaten en Chrome-exemplaren.

##<a id="register"></a>Google Cloud Messaging inschakelen

1. Navigeer naar de [Google Cloud Console]-website, log in op uw Google-account en klik op vervolgens op de knop **Create Project** (Project maken). Geef een geschikte **Project Name** op en klik vervolgens op de knop **Create**.

    ![Google Cloud Console - Project maken][1]

2. Noteer het **Project Number** op de pagina **Projects** van het project dat u zojuist hebt gemaakt. U gebruikt deze als de **GCM afzender-id** in de Chrome-app om uzelf te registreren bij GCM.

    ![Google Cloud Console - Projectnummer][2]

3. Klik in het linkerdeelvenster op **API's & auth** en schuif omlaag. Klik op de wisselknop om **Google Cloud Messaging for Android** in te schakelen. U hoeft **Google Cloud Messaging for Chrome** niet in te schakelen.

    ![Google Cloud Console - Serversleutel][3]

4. Klik in het linkerdeelvenster op **Credentials** > **Create New Key** > **Server Key** > **Create**.

    ![Google Cloud Console - Referenties][4]

5. Noteer de waarde van de **API-sleutel** van de server. U configureert deze in de volgende stap in uw Notification Hub, zodat u puhmeldingen naar GCM kunt verzenden.

    ![Google Cloud Console - API-sleutel][5]

##<a id="configure-hub"></a>Uw Notification Hub configureren

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6.   Selecteer in de blade **Instellingen** **Notification Services** en vervolgens **Google (GCM)**. Voer de API-sleutel in en sla uw invoer op.

&emsp;&emsp;![Azure Notification Hubs - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

##<a id="connect-app"></a>Uw Chrome-app verbinden met de Notification Hub

De Notification Hub is nu geconfigureerd voor GCM en u hebt de verbindingsreeksen om uw app te registreren voor het ontvangen en verzenden van pushmeldingen. LK

###Een nieuwe Chrome-app maken

Het onderstaande voorbeeld is gebaseerd op het [Chrome App GCM-voorbeeld] waarin op de aanbevolen manier een Chrome-app wordt gemaakt. We zullen de stappen bespreken die specifiek betrekking hebben op Azure Notification Hubs. 

>[AZURE.NOTE] We raden u aan de bron voor deze Chrome-app te downloaden vanaf [Chrome App Notification Hub Sample].

De Chrome-app wordt gemaakt via JavaScript en u kunt uw favoriete tekstverwerkingsprogramma hiervoor gebruiken. Hieronder ziet u hoe deze Chrome-app eruit komt te zien.

![Google Chrome-app][15]

1. Maak een map en geef deze de naam `ChromePushApp`. De naam is natuurlijk willekeurig. Als u een andere naam gebruikt, moet u het pad in de vereiste codesegmenten vervangen.

2. Download [crypto-js library] in de map die u in de tweede stap hebt gemaakt. Deze bibliotheekmap bevat twee submappen: `components` en `rollups`.

3. Maak een `manifest.json`-bestand. Alle Chrome-apps worden ondersteund door een manifestbestand met de metagegevens van de app en alle machtigingen die worden verleend aan de app wanneer de gebruiker de app installeert.

        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }

    Let op het `permissions`-element, dat aangeeft dat deze Chrome-app pushmeldingen van GCM kan ontvangen. Ook moet hierin de Azure Notification Hubs URI worden opgegeven waarin de Chrome-app een REST-aanroep doet voor de registratie.
    Onze voorbeeldapp gebruikt ook een pictogrambestand `gcm_128.png`, dat zich bevindt op de bronlocatie die opnieuw wordt gebruikt van het originele GCM-voorbeeld. U kunt het vervangen door een andere afbeelding die voldoet aan de [vereisten voor een pictogram](https://developer.chrome.com/apps/manifest/icons).

4. Maak een bestand met de naam `background.js` met de volgende code:

        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }

        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.

          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);

          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }

        var registerWindowCreated = false;

        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {

            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }

        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);

        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);

    Dit is het bestand dat wordt weergegeven in de html van de Chrome-app (**register.html**). Hierin wordt ook de handler **messageReceived** voor binnenkomende pushberichten gedefinieerd.

5. Maak een bestand met de naam `register.html`. Hiermee definieert u de gebruikersinterface van de Chrome-app. 

   >[AZURE.NOTE] In dit voorbeeld werken we met **CryptoJS v3.1.2**. Als u een andere versie van de bibliotheek hebt gedownload, moet u de versie in het `src`-pad vervangen door de juiste versie.

        <html>

        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>

        <body>

        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>

        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

        <br/>

        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

        <br/>
        <br/>

        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

        </body>

        </html>

6. Maak een bestand met de naam `register.js` met de volgende code. Dit bestand definieert het script achter `register.html`. Chrome-apps ondersteunen geen inline-uitvoeringen. U moet dus een afzonderlijke back-up van het script maken voor uw gebruikersinterface.

        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }

        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }

          document.getElementById("console").innerHTML = currentStatus  + status;
        }

        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);

          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }

        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;

          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }

          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;

          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }

        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }

        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";

          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });

          originalUri = endpoint + hubName;
        }

        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration

          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;

          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);

          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }

        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";

          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();

          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };

          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }

          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");

          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }

    Het bovenstaande script heeft de volgende belangrijke parameters:
    - **Window.onload** definieert wat er gebeurt als op de twee knoppen in de gebruikersinterface wordt gedrukt. Met de ene knop wordt de registratie bij GCM uitgevoerd en de andere maakt gebruik van de registratie-id die na de registratie bij GCM van Azure Notification Hubs wordt geretourneerd.
    - **updateLog** is de functie waarmee we eenvoudige logboekmogelijkheden kunnen afhandelen.
    - **registerWithGCM** is de eerste handler voor de knop, waardoor `chrome.gcm.register` GCM wordt aangeroepen om het huidige exemplaar van de Chrome-app te registreren.
    - **registerCallback** is de callbackfunctie die wordt aangeroepen wanneer de aanroep van de GCM-registratie wordt geretourneerd.
    - **registerWithNH** is de tweede handler voor de knop, waardoor wordt geregistreerd bij Notification Hubs. Deze ontvangt `hubName` en `connectionString` (door de gebruiker opgegeven) en stelt de REST-API-aanroep samen voor registratie bij Notification Hubs.
    - **splitConnectionString** en **generateSaSToken** zijn hulpprogramma's voor het uitvoeren van de implementatie van JavaScript van een SaS-token, dat in alle REST-API-aanroepen moet worden gebruikt. Zie [Algemene begrippen](http://msdn.microsoft.com/library/dn495627.aspx) voor meer informatie.
    - **sendNHRegistrationRequest** is de functie die een HTTP REST-aanroep naar Azure Notification Hubs maakt.
    - **registrationPayload** definieert de XML-nettolading van de registratie. Zie [REST-API maken voor de registratie van NH] voor meer informatie. We werken de registratie-id hierin bij met de informatie die we van GCM ontvangen.
    - **client** is een exemplaar van **XMLHttpRequest** die we gebruiken voor het maken van de HTTP POST-aanvraag. Let op: de `Authorization`-header wordt bijgewerkt met `sasToken`. Als deze aanroep is voltooid, wordt dit exemplaar van de Chrome-app geregistreerd bij Azure Notification Hubs.


De algehele mappenstructuur voor dit project moet er als volgt uitzien:     ![Google Chrome-app - mappenstructuur][21]

###Uw Chrome-app instellen en testen

1. Open uw Chrome-browser. Open **Chrome-extensies** en schakel **Modus voor ontwikkelaars** in.

    ![Google Chrome - Modus voor ontwikkelaars inschakelen][16]

2. Klik op **Uitgepakte extensie laden** en navigeer naar de map waarin u de bestanden hebt gemaakt. U kunt eventueel ook het **hulpprogramma Chrome Apps& Extensions Developer** gebruiken. Dit hulpprogramma is zelf een Chrome-app (geïnstalleerd via de Chrome Web Store) en biedt geavanceerde mogelijkheden voor foutopsporing tijdens de ontwikkeling van uw Chrome-app.

    ![Google Chrome - Uitgepakte extensie laden][17]

3. Als de Chrome-app foutloos is gemaakt, wordt uw app weergegeven.

    ![Google Chrome - Chrome-app weergeven][18]

4. Voer het **projectnummer** in dat u eerder hebt ontvangen van de **Google Cloud Console** als de afzender-id en klik op **Registreren bij GCM**. Het volgende bericht wordt weergegeven: **Registratie bij GCM is voltooid.**

    ![Google Chrome - Chrome-app aanpassen][19]

5. Voer de **Naam van de Notification Hub** en de **DefaultListenSharedAccessSignature** in die u eerder hebt ontvangen van de portal en klik op **Registreren bij Azure Notification Hub**. Als het goed is, wordt het volgende bericht weergegeven: **Registratie van Notification Hub gelukt!**, evenals de details van het registratie-antwoord dat de registratie-id van Azure Notification Hubs bevat.

    ![Google Chrome - Details voor de Notification Hub opgeven][20]  

##<a name="send"></a>Een melding naar uw Chrome-app verzenden

Voor testdoeleinden sturen we Chrome-pushmeldingen met een .NET-consoletoepassing. 

>[AZURE.NOTE] U kunt pushmeldingen verzenden via Notification Hubs vanuit elke back-end via de openbare <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST-interface</a>. Bekijk onze [portal met documentatie](https://azure.microsoft.com/documentation/services/notification-hubs/) voor meer platformonafhankelijke voorbeelden.

1. Klik in Visual Studio in het menu **File** (Bestand) op **New** (Nieuw) en vervolgens **Project**. Klik onder **Visual C#** op **Windows** en **Console Application** en klik vervolgens op **OK**.  Hiermee maakt u een nieuw consoletoepassingsproject.

2. Klik in het menu **Tools** op **Library Package Manager** en **Package Manager Console**. Hiermee wordt Package Manager Console weergegeven.

3. Voer de volgende opdracht uit in het consolevenster:

        Install-Package Microsoft.Azure.NotificationHubs

    Hiermee voegt u een verwijzing in naar de Azure Service Bus-SDK met het <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-pakket</a>.

4. Open `Program.cs` en voeg de volgende `using` instructie toe:

        using Microsoft.Azure.NotificationHubs;

5. Voeg in de klasse `Program` de volgende methode toe:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

    Vervang de tijdelijke aanduiding `<hub name>` door de naam van de Notification Hub die wordt weergegeven in de [portal](https://portal.azure.com) in de blade Notification Hub. Vervang ook de tijdelijke aanduiding voor de verbindingsreeks door de verbindingsreeks genaamd `DefaultFullSharedAccessSignature` die u hebt verkregen in de sectie voor het configureren van uw Notification Hub.

    >[AZURE.NOTE] Zorg ervoor dat u de verbindingsreeks met het toegangsrecht **Full** (Volledig) gebruikt, dus niet **Listen** (Luisteren). Met de verbindingsreeks met het toegangsrecht **Listen** kunnen geen pushmeldingen worden verzonden.

5. Voeg de volgende aanroepen toe aan de methode `Main`:

         SendNotificationAsync();
         Console.ReadLine();
         
6. Zorg ervoor dat Chrome wordt uitgevoerd en start de consoletoepassing.

7. De volgende meldingspop-up wordt op uw bureaublad weergegeven.

    ![Google Chrome - Melding][13]

8. U kunt ook al uw meldingen zien in het venster Chrome-meldingen in de taakbalk (in Windows) als Chrome wordt uitgevoerd.

    ![Google Chrome - Lijst met meldingen][14]

>[AZURE.NOTE] Hiervoor hoeft u de Chrome-app niet te starten of te openen in de browser (de Chrome-browser zelf moet wel worden uitgevoerd). U kunt ook een geconsolideerde weergave van al uw meldingen bekijken in het venster Chrome-berichten.

## <a name="next-steps"> </a>Volgende stappen

Zie [Overzicht van Notification Hubs] voor meer informatie.

Zie de zelfstudie [Azure Notification Hubs Gebruikers waarschuwen] om specifieke gebruikers te bereiken. 

Zie [Azure Notification Hubs voor belangrijk nieuws] als u gebruikers wilt indelen op belangengroep.

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Chrome App Notification Hub Sample]: http://google.com
[Google Cloud Console]: http://cloud.google.com/console
[klassieke Azure-portal]: https://manage.windowsazure.com/
[Overzicht van Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Overzicht van Chrome-apps]: https://developer.chrome.com/apps/about_apps
[Chrome App GCM-voorbeeld]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installeerbare Webapps]: https://developers.google.com/chrome/apps/docs/
[Chrome-apps op mobiele apparaten]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[REST-API maken voor de registratie van NH]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[crypto-js library]: http://code.google.com/p/crypto-js/
[GCM met Chrome-apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging voor Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs Gebruikers waarschuwen]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Azure Notification Hubs voor belangrijk nieuws]: notification-hubs-windows-store-dotnet-send-breaking-news.md



<!--HONumber=Jun16_HO2-->


