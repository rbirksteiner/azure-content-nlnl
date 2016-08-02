<properties
    pageTitle="Pushmeldingen verzenden met Azure Notification Hubs op Windows Phone | Microsoft Azure"
    description="In deze zelfstudie leert u hoe u met Azure Notification Hubs pushmeldingen verzendt naar een Windows Phone 8 of Windows Phone 8.1 Silverlight-toepassing."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="push notification,push notification,windows phone push"
    authors="wesmc7777"
    manager="dwrede"
    editor="dwrede"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="03/10/2016"
    ms.author="wesmc"/>

# Pushmeldingen verzenden met Azure Notification Hubs op Windows Phone

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Overzicht

> [AZURE.NOTE] U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F) voor meer informatie.

In deze zelfstudie ziet u hoe u met Azure Notification Hubs pushmeldingen verzendt naar een Windows Phone 8 of Windows Phone 8.1 Silverlight-toepassing. Als u ontwikkelt voor Windows Phone 8.1 (zonder Silverlight), raadpleegt u de [universele Windows-versie](notification-hubs-windows-store-dotnet-get-started.md).
In deze zelfstudie maakt u een lege Windows Phone 8-app die pushmeldingen ontvangt via Microsoft Push Notification Service (MPNS). Als u klaar bent, kunt u de Notification Hub gebruiken om pushmeldingen uit te zenden naar alle apparaten waarop uw app wordt uitgevoerd.

> [AZURE.NOTE] De Notification Hubs Windows Phone SDK biedt geen ondersteuning voor het gebruik van Windows Push Notification Service (WNS) met Windows Phone 8.1 Silverlight-apps. Als u WNS (in plaats van MPNS) met Windows Phone 8.1 Silverlight-apps wilt gebruiken, raadpleegt u de [Notification Hubs - Windows Phone Silverlight tutorial] (Notification Hubs: zelfstudie voor Windows Phone Silverlight) waarin REST API's worden gebruikt.

In de zelfstudie wordt een eenvoudig scenario voor het uitzenden met Notification Hubs beschreven.

##Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

+ [Visual Studio 2012 Express voor Windows Phone] of een hogere versie.

Het voltooien van deze zelfstudie is een vereiste voor alle andere Notification Hubs-zelfstudies voor Windows Phone 8-apps.

##Een Notification Hub maken

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Klik op de sectie <b>Notification Services</b> (in <i>Instellingen</i>), klik op <b>Windows Phone (MPNS)</b> en schakel vervolgens het selectievakje <b>Niet-geverifieerde pushmeldingen inschakelen</b> in.</p>
</li>
</ol>

&emsp;&emsp;![Azure Portal - Niet-geverifieerde pushmeldingen inschakelen](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

De hub is nu gemaakt en geconfigureerd om niet-geverifieerde meldingen voor Windows Phone te verzenden.

> [AZURE.NOTE] In deze zelfstudie wordt MPNS in niet-geverifieerde modus gebruikt. Bij de niet-geverifieerde MPNS-modus gelden beperkingen voor meldingen die u naar elk kanaal kunt verzenden. Notification Hubs ondersteunt de [geverifieerde MPNS-modus](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) doordat u uw certificaat kunt uploaden.

##Uw app verbinden met de Notification Hub

1. Maak een nieuwe Windows Phone 8-toepassing in Visual Studio.

    ![Visual Studio - Nieuw project - Windows Phone-app][13]

    In Visual Studio 2013 Update 2 of hoger maakt u daarentegen een Windows Phone Silverlight-toepassing.

    ![Visual Studio - Nieuw project - Lege app - Windows Phone Silverlight][11]

2. Klik met de rechtermuisknop op de oplossing in Visual Studio en klik vervolgens op **NuGet-pakketten beheren**.

    Hierop wordt het dialoogvenster **NuGet-pakketten beheren** weergegeven.

3. Zoek `WindowsAzure.Messaging.Managed` en klik op **Installeren**. Accepteer vervolgens de gebruiksvoorwaarden.

    ![Visual Studio - NuGet Package Manager][20]

    Hiermee wordt een verwijzing gedownload, geïnstalleerd en toegevoegd aan de Azure Messaging-bibliotheek voor Windows met het <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet-pakket</a>.

4. Open het bestand App.xaml.cs en voeg de volgende `using`-instructies toe:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Voeg de volgende code toe aan de bovenkant van de **Application_Launching**-methode in App.xaml.cs:

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });

    >[AZURE.NOTE] De waarde **MyPushChannel** is een index die wordt gebruikt om een bestaand kanaal te zoeken in de collectie [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx). Als het kanaal niet wordt gevonden, maakt u een nieuwe vermelding met die naam.
    
    Zorg ervoor dat u de naam van uw hub en de verbindingsreeks met de naam **DefaultListenSharedAccessSignature** invoegt die u in de voorgaande sectie hebt verkregen.
    Met deze code wordt de kanaal-URI voor de app opgehaald uit MPNS en wordt vervolgens die kanaal-URI voor uw Notification Hub geregistreerd. Ook wordt hiermee gegarandeerd dat de kanaal-URI in uw Notification Hub wordt geregistreerd telkens wanneer de toepassing wordt gestart.

    >[AZURE.NOTE]In deze zelfstudie wordt een pop-upmelding naar het apparaat verzonden. Wanneer u een tegelmelding verzendt, moet u in plaats daarvan de **BindToShellTile**-methode voor het kanaal aanroepen. Voor de ondersteuning van zowel pop-up- als tegelmeldingen roept u **BindToShellTile** en **BindToShellToast** aan.

6. Vouw in Solution Explorer **Eigenschappen** uit, open het bestand `WMAppManifest.xml`, klik op het tabblad **Mogelijkheden** en zorg ervoor dat de mogelijkheid **ID_CAP_PUSH_NOTIFICATION** is ingeschakeld.

    ![Visual Studio - Windows Phone-app-mogelijkheden][14]

    Dit zorgt ervoor dat uw app pushmeldingen kan ontvangen. Als u dit niet hebt gedaan, kunnen er geen pushmeldingen naar de app worden verzonden.

7. Druk op de toets `F5` om de app uit te voeren.

    Er wordt een registratiebericht in de app weergegeven.

8. Sluit de app.  

   >[AZURE.NOTE] Als u een pop-upmelding wilt ontvangen, moet de toepassing niet worden uitgevoerd op de voorgrond.

##Pushmeldingen verzenden vanuit uw back-end

U kunt pushmeldingen verzenden via Notification Hubs vanuit elke back-end via de openbare <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST-interface</a>. In deze zelfstudie verzendt u pushmeldingen met een .NET-consoletoepassing. 

Zie [Azure Notification Hubs Notify Users with .NET backend](./notification-hubs-aspnet-backend-windows-dotnet-notify-users.md) (Met Azure Notification Hubs meldingen naar gebruikers verzenden via een .NET-back-end) voor een voorbeeld van hoe u pushmeldingen verzendt vanuit een ASP.NET WebAPI-back-end die is geïntegreerd met Notification Hubs.  

Voor een voorbeeld van hoe u pushmeldingen verzendt met [REST API's](https://msdn.microsoft.com/library/azure/dn223264.aspx) raadpleegt u [How to use Notification Hubs from Java](./notification-hubs-java-backend-how-to.md) (Notification Hubs gebruiken vanuit Java) en [How to use Notification Hubs from PHP](./notification-hubs-php-backend-how-to.md) (Notification Hubs gebruiken vanuit PHP).

1. Klik met de rechtermuisknop op de oplossing, selecteer **Toevoegen** en **Nieuw project**. Klik vervolgens onder **Visual C#** op **Windows** en **Consoletoepassing** en klik op **OK**.

    ![Visual Studio - Nieuw project - Consoletoepassing][6]

    Hiermee voegt u een nieuwe Visual C#-consoletoepassing toe aan de oplossing. U kunt dit ook in een afzonderlijke oplossing doen.

4. Klik achtereenvolgens op **Extra**, **Library Package Manager** en **Package Manager-console**.

    Hiermee wordt Package Manager Console weergegeven.

5.  Stel in het venster **Package Manager-console** het **standaardproject** in op uw nieuwe consoletoepassingsproject en voer vervolgens in het consolevenster de volgende opdracht uit:

        Install-Package Microsoft.Azure.NotificationHubs

    Hiermee wordt een verwijzing toegevoegd aan de Azure Notification Hubs SDK met het <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet-pakket</a>.

6. Open het bestand `Program.cs` en voeg de volgende `using`-instructie toe:

        using Microsoft.Azure.NotificationHubs;

6. Voeg in de klasse `Program` de volgende methode toe:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    Vervang de tijdelijke aanduiding `<hub name>` door de naam van de Notification Hub die wordt weergegeven in de portal. Vervang ook de tijdelijke aanduiding voor de verbindingsreeks door de verbindingsreeks genaamd **DefaultFullSharedAccessSignature** die u hebt verkregen in de sectie Uw Notification Hub configureren.

    >[AZURE.NOTE]Zorg ervoor dat u de verbindingsreeks met het toegangsrecht **Full** (Volledig) gebruikt, dus niet **Listen** (Luisteren). Met een verbindingsreeks met het toegangsrecht Luisteren kunnen geen pushmeldingen worden verzonden.

4. Voeg de volgende regel toe in de `Main`-methode:

         SendNotificationAsync();
         Console.ReadLine();

5. Zorg ervoor dat uw Windows Phone-emulator wordt uitgevoerd en dat uw app is gesloten. Stel vervolgens het consoletoepassingsproject in als standaardopstartproject en druk op de toets `F5` om de app uit te voeren.

    U ontvangt een pop-uppushmelding. Als u op de banner van de pop-up klikt, wordt de app geladen.

U vindt alle mogelijke nettoladingen in de onderwerpen [pop-upcatalogus] en [tegelcatalogus] op MSDN.

##Volgende stappen

In dit eenvoudige voorbeeld hebt u pushmeldingen uitgezonden naar al uw Windows Phone 8-apparaten. 

Als u zich op specifieke gebruikers wilt richten, raadpleegt u de zelfstudie [Notification Hubs gebruiken om pushmeldingen naar gebruikers te verzenden]. 

Als u gebruikers wilt indelen op belangengroepen, raadpleegt u [Notification Hubs gebruiken om belangrijk nieuws te verzenden]. 

Zie [Richtlijnen voor Notification Hubs] voor meer informatie over het gebruik van Notification Hubs.



<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express voor Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Richtlijnen voor Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Geverifieerde MPNS-modus]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Notification Hubs gebruiken om pushmeldingen naar gebruikers te verzenden]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Notification Hubs gebruiken om belangrijk nieuws te verzenden]: notification-hubs-windows-phone-send-breaking-news.md
[pop-upcatalogus]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[tegelcatalogus]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hubs - Windows Phone Silverlight-zelfstudie]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp




<!--HONumber=Jun16_HO2-->


