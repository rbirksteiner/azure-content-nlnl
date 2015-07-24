

在您的後端應用程式中，您現在必須切換成傳送範本通知 (而非原生裝載)。此動作將會簡化後端程式碼，因為您將無需為不同平台傳送多個裝載。

傳送範本通知時，您只需提供一組屬性，在我們的案例中，我們將傳送一組包含已當地語系化版本的目前新聞屬性，例如：

	{
		"News_English": "World News in English!",
    	"News_French": "World News in French!",
    	"News_Mandarin": "World News in Mandarin!"
	}


本節將以兩種不同方式示範傳送通知的方式：

- 使用主控台應用程式
- 使用行動服務指令碼

後端可廣播至任何支援的裝置，因此隨附的程式碼會廣播至 Windows 市集和 iOS 裝置。



## 使用 C# 主控台應用程式傳送通知 ##

我們將透過傳送單一範本通知，來簡單修改您的 *SendNotificationAsync* 方法。

	var hub = NotificationHubClient.CreateClientFromConnectionString("<connection string>", "<hub name>");
    var notification = new Dictionary<string, string>() {
							{"News_English", "World News in English!"},
                            {"News_French", "World News in French!"},
                            {"News_Mandarin", "World News in Mandarin!"}};
    await hub.SendTemplateNotificationAsync(notification, "World");

請注意，此簡單呼叫會將已當地語系化的正確新聞片段傳送到您的**所有**裝置 (不論平台為何)，因為您的通知中心會建立並傳遞正確的原生裝載給訂用特定標籤的所有裝置。

### 行動服務

在您的行動服務排程器中，使用下列內容覆寫您的指令碼：

	var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
			"News_English": "World News in English!",
			"News_French": "World News in French!",
			"News_Mandarin", "World News in Mandarin!"
	}
	notificationHubService.send('World', notification, function(error) {
		if (!error) {
			console.warn("Notification successful");
		}
	});
	
請注意，此案例不需要針對不同地區設定和平台傳送多次通知。

<!---HONumber=July15_HO2-->