<properties 
	pageTitle="使用通知中樞傳送即時新聞 (Windows Universal)" 
	description="使用 Azure 通知中樞搭配註冊中的標籤，將重大新聞傳送至通用 Windows 應用程式。" 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>


<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="wesmc"/>

# 使用通知中心傳送即時新聞


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##概觀

本主題將說明如何使用 Azure 通知中樞，將即時新聞通知廣播至 Windows Store 或 Windows Phone 8.1 (非 Silverlight) 應用程式。如果您的目標是 Windows Phone 8.1 Silverlight，請參閱 [Windows Phone](notification-hubs-ios-send-breaking-news.md) 版本。完成時，您便能夠註冊您所感興趣的即時新聞類別，並僅接收這些類別的推播通知。此情況是許多應用程式的共同模式，這些應用程式必須將通知傳送給先前宣告對通知有興趣的使用者群組，例如，RSS 閱讀程式、供樂迷使用的應用程式等等。

在通知中心內建立註冊時，您可以透過包含一或多個 _tags_ 來啟用廣播案例。當標籤收到通知時，所有已註冊此標籤的裝置都會收到通知。由於標籤只是簡單的字串而已，您無需預先佈建標籤。如需標籤的詳細資訊，請參閱[通知中心指引]。

##必要條件

本主題會以您在[開始使用通知中心][get-started]中所建立的應用程式為基礎。開始本教學課程之前，您必須已完成[開始使用通知中心][get-started]。

##在應用程式中新增類別選項

第一個步驟是在您的現有主頁面上新增 UI 元素，以便使用者選取要註冊的類別。使用者所選取的類別會儲存在裝置上。啟動應用程式時，您的通知中心內會建立以所選取類別作為標籤的裝置註冊。

1. 開啟 MainPage.xaml 專案檔案，然後在 **Grid** 元素中複製下列程式碼：
			
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>


2. 以滑鼠右鍵按一下 [**共用**] 專案中，加入名為 **Notifications** 的新類別，將 **public** 修飾詞新增至類別定義，然後在新的程式碼檔案中新增下列 **using** 陳述式：

		using Windows.Networking.PushNotifications;
		using Microsoft.WindowsAzure.Messaging;
		using Windows.Storage;
		using System.Threading.Tasks;

3. 將下列程式碼複製到新的 **Notifications** 類別：

		private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            await SubscribeToCategories(categories);
        }

		public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                IEnumerable<string> categories = RetrieveCategories();
            }

            await hub.RegisterNativeAsync(channel.Uri, categories);
        }

    本類別會使用本機儲存體來儲存此裝置必須接收的新聞類別。它也包含註冊這些類別的方法。


4. 在 App.xaml.cs 專案檔案中，新增下列屬性至 **App** 類別：

		public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

	此屬性可用來建立並存取 **Notifications** 執行個體。

	在上述程式碼中，使用您的通知中樞名稱及先前取得的 *DefaultListenSharedAccessSignature* 連接字串，來取代 `<hub name>` 和 `<connection string with listen access>` 預留位置。

	> [AZURE.NOTE]因為隨用戶端應用程式散佈的憑證通常不安全，您應只將接聽存取權的金鑰隨用戶端應用程式散佈。您的應用程式可透過接聽存取權來註冊通知，但無法修改現有的註冊或無法傳送通知。在安全的後端服務中，會使用完整存取金鑰來傳送通知和變更現有的註冊。

5. 在 MainPage.xaml.cs 中新增下列程式碼行：

		using Windows.UI.Popups;

6. 在 MainPage.xaml.cs 專案檔案中新增下列方法：

		private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories));
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
	
	此方法會建立一份類別清單，並使用 **Notifications** 類別在本機儲存體中儲存清單，並在通知中心註冊對應標籤。變更類別時，系統會使用新類別重新建立註冊。

您的應用程式現在可以在裝置上的本機儲存體中儲存一組類別，並在使用者每次變更類別選項時在通知中心註冊。

##註冊通知

這些步驟會在啟動時，使用已儲存在本機儲存體中的類別在通知中心註冊。

> [AZURE.NOTE]由於 Windows 通知服務 (WNS) 所指派的通道 URI 可以隨時變更，您應經常註冊通知以避免通知失敗。此範例會在應用程式每次啟動時註冊通知。若是經常執行 (一天多次) 的應用程式，如果距離上次註冊的時間不到一天，則您可能可以略過註冊以保留頻寬。

1. 開啟 App.xaml.cs 檔案，並將 **async** 修飾詞新增至 **OnLaunched** 方法。

2. 在 **OnLaunched** 方法中，尋找現有的 **InitNotificationsAsync** 方法呼叫並以下列程式碼行取代：

		await notifications.SubscribeToCategories();

	這會確保應用程式每次啟動時都會從本機儲存體擷取類別，並要求這些類別的註冊。[開始使用通知中心] 教學課程的一部分是建立 **InitNotificationsAsync** 方法，但本主題中將不會用到。

3. 在 MainPage.xaml.cs 專案檔案中，在 *OnNavigatedTo* 方法中新增下列程式碼：

		var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;

	這會根據原先儲存的類別狀態更新主頁面。

現在已完成此應用程式，且可在裝置本機儲存體中儲存一組類別，以供每次使用者變更類別選項在通知中心註冊時使用。接著，我們會定義可將類別通知傳送至此應用程式的後端。

##從後端傳送通知

[AZURE.INCLUDE [notification-hubs-back-end](../../includes/notification-hubs-back-end.md)]

##執行應用程式並產生通知

1. 在 Visual Studio 中，按 F5 以編譯並啟動應用程式。

	![][1]

	請注意，應用程式 UI 提供一組切換，可讓您選擇要訂閱的類別。

2. 啟用一或多個類別切換，然後按一下 [訂閱]。

	應用程式會將選取的類別轉換成標籤，並在通知中心內為選取的標籤要求新裝置註冊。系統會傳回已註冊類別並顯示在對話方塊中。

	![][19]

4. 若要從後端傳送新通知，您可以使用下列其中一種方式：

	+ **主控台應用程式：**啟動主控台應用程式。

	+ **Java/PHP：**執行您的應用程式/指令碼。

	選取的類別通知會以快顯通知方式出現。

	![][14]

##後續步驟

在本教學課程中，我們了解到如何按類別廣播即時新聞。請考慮完成下列其中一個強調其他進階通知中心案例的教學課程：

+ [使用通知中樞廣播已當地語系化的即時新聞]

	了解如何擴充即時新聞應用程式，以啟用傳送已當地語系化的通知。

+ [使用通知中樞來通知使用者]

	了解如何推播通知給特定的經驗證使用者。在僅傳送通知給特定使用者的情況下，這是很好的解決方案。


<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[使用通知中樞廣播已當地語系化的即時新聞]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[使用通知中樞來通知使用者]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[通知中心指引]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591





 

<!---HONumber=July15_HO1-->