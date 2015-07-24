<properties 
	pageTitle="通知中樞已當地語系化的即時新聞教學課程" 
	description="了解如何使用 Azure 服務匯流排通知中樞傳送本地化重大新聞通知。" 
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
	ms.date="04/27/2015" 
	ms.author="wesmc"/>

# 使用通知中心傳送當地語系化的即時新聞

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows 市集 C#" class="current">Windows 市集 C#</a><a href="/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iiOS">iOS</a>
</div>

##概觀

本主題將說明如何使用 Azure 通知中心的**範本**功能，廣播已由語言和裝置當地語系化的即時新聞通知。在本教學課程中，首先您會執行在[使用通知中心傳送即時新聞]中建立的 Windows 市集應用程式。完成之後，您將可註冊您感興趣的類別、指定您要接收哪種語言的通知，並以該語言針對選取的類別接收推播通知。


此案例分成兩部分：

- Windows 市集應用程式允許用戶端裝置指定語言，以及訂閱不同的即時新聞類別； 

- 後端使用 Azure 通知中心的**標籤**和**範本**功能廣播通知。



##必要條件

您必須已完成[使用通知中心傳送即時新聞]教學課程，並具有可用的程式碼，因為此教學課程是直接根據該程式碼而建置的。

您也需要 Visual Studio 2012。


##範本概念

在[使用通知中心傳送即時新聞]，您建置了使用**標籤**來訂閱不同即時新聞類別之通知的應用程式。但有許多應用程式是以多個市場為目標的，因此需要當地語系化。這表示通知本身的內容必須進行當地語系化，並傳遞至正確的裝置集。在此主題中，我們將說明如何使用通知中心的**範本**功能，輕鬆地傳遞已當地語系化的即時新聞通知。

注意：傳送當地語系化通知的方法之一，是為每個標籤建立多個版本。例如，若要支援英文、法文和中文，我們將必須為世界新聞建立三個不同的標籤："world_en"、"world_fr" 和 "world_ch"。接著，我們必須將當地語系化版本的世界新聞分別傳送至這三個標籤。在此主題中我們會使用範本，以避免使用過多的標籤和傳送過多訊息。

以較高的層級而言，範本可用來指定特定裝置接收通知的方式。範本可參照您的應用程式後端所傳送的訊息中包含的屬性，藉以指定確切的裝載格式。在此處的範例中，我們將傳送地區設定無從驗證、且包含所有支援語言的訊息：

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

接著，我們將確保裝置會為參照正確屬性的範本進行註冊。例如，要接收簡易快顯通知訊息的 Windows 市集應用程式，將會為下列範本註冊：

	<toast>
	  <visual>
	    <binding template="ToastText01">
	      <text id="1">$(News_English)</text>
	    </binding>
	  </visual>
	</toast>



範本的功能非常強大，您可以在[通知中心指引]一文中了解詳情。[Windows 市集的通知中心作法]則提供了範本運算式語言的參考。


##應用程式使用者介面

現在，我們將修改您在[使用通知中心傳送即時新聞]主題中建立的即時新聞應用程式，以使用範本傳送當地語系化的即時新聞。


若要調整用戶端應用程式使其能夠接收當地語系化的訊息，您必須以範本註冊取代您的*原生*註冊 (也就是指定範本的註冊)。


在您的 Windows 市集應用程式中：

變更 MainPage.xaml 以加入地區設定下拉式方塊：

	<Grid Margin="120, 58, 120, 80"  
			Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="Button_Click" />
    </Grid>

##建置 Windows 市集用戶端應用程式

1. 在您的 Notifications 類別中，將地區設定參數新增至 *StoreCategoriesAndSubscribe* 和 *SubscribeToCateories* 方法。

		public async Task StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            await SubscribeToCategories(locale, categories);
        }

        public async Task SubscribeToCategories(string locale, IEnumerable<string> categories)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            var template = String.Format(@"<toast><visual><binding template=""ToastText01""><text id=""1"">$(News_{0})</text></binding></visual></toast>", locale);

            await hub.RegisterTemplateAsync(channel.Uri, template, "newsTemplate", categories);
        }

	請注意，我們不會呼叫 *RegisterNativeAsync* 方法，而是會呼叫 *RegisterTemplateAsync*：我們將會註冊讓範本依循地區設定的特定通知格式。我們也為範本提供名稱 ("newsTemplate")，因為我們可能會想註冊多個範本 (例如，一個供快顯通知使用，一個供磚使用)，而且我們必須為其命名，才能加以更新或刪除。

	請注意，如果有裝置使用相同的標籤註冊多個範本，一個以該標籤為目標的傳入訊息將會使多個通知傳遞至裝置 (每個範本各一個)。此行為在相同的邏輯訊息必須產生多個視覺化通知時將有所幫助，例如，在一個 Windows 市集應用程式中同時顯示徽章和快顯通知。

2. 新增下列方法，以擷取已儲存的地區設定：

		public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. 在您的 MainPage.xaml.cs 中，擷取地區設定下拉式方塊的現行值，並將其提供給 Notifications 類別的呼叫，以更新您的按鈕點選處理常式，如下所示：

		 var locale = (string)Locale.SelectedItem;
            
         var categories = new HashSet<string>();
         if (WorldToggle.IsOn) categories.Add("World");
         if (PoliticsToggle.IsOn) categories.Add("Politics");
         if (BusinessToggle.IsOn) categories.Add("Business");
         if (TechnologyToggle.IsOn) categories.Add("Technology");
         if (ScienceToggle.IsOn) categories.Add("Science");
         if (SportsToggle.IsOn) categories.Add("Sports");

         await ((App)Application.Current).Notifications.StoreCategoriesAndSubscribe(locale, categories);

         var dialog = new MessageDialog(String .Format("Locale: {0}; Subscribed to: {1}", locale, string.Join(",", categories)));
         dialog.Commands.Add(new UICommand("OK"));
         await dialog.ShowAsync();

4. 最後，在您的 App.xaml.cs 檔案中，確實在 *OnLaunched* 方法中更新您對通知 singleton 的呼叫：

		Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());


##從後端傳送已當地語系化的通知

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]





## 後續步驟

如需有關使用範本的詳細資訊，請參閱[使用通知中樞來通知使用者：ASP.NET]、[使用通知中樞來通知使用者：行動服務]，還可參閱[通知中樞指引]。[Windows 市集的通知中心作法]則提供了範本運算式語言的參考。

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

<!-- Images. -->





















<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[使用通知中樞來通知使用者：ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[使用通知中樞來通知使用者：行動服務]: /manage/services/notification-hubs/notify-users
[使用通知中心傳送即時新聞]: /manage/services/notification-hubs/breaking-news-dotnet

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知中心指引]: http://msdn.microsoft.com/library/jj927170.aspx
[通知中樞指引]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Windows 市集的通知中心作法]: http://msdn.microsoft.com/library/jj927172.aspx
 

<!---HONumber=July15_HO1-->