<properties 
	pageTitle="使用通知中樞向使用者傳送跨平台通知 (ASP.NET)" description="了解如何使用通知中樞範本，在單一要求中傳送以所有平台為目標的跨平台通知。" 
	services="notification-hubs" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="wesmc"/>

# 使用通知中心向使用者傳送跨平台通知


在上一堂教學課程[使用通知中心來通知使用者]中，您已了解如何將通知推播至所有由特定經驗證使用者所註冊的裝置。在該教學課程中，需要用多個要求來傳送通知給每個支援的用戶端平台。通知中心可支援範本，讓您指定特定裝置接收通知的方式。這使得傳送跨平台通知變得更簡單。本主題示範如何運用範本，在單一要求中傳送以所有平台為目標的跨平台通知。如需這些範本的詳細資訊，請參閱 [Azure 通知中心概觀][Templates]。

> [AZURE.NOTE]通知中心可以讓裝置註冊多個具有相同標籤的範本。在此情況下，當傳入的訊息符合該標籤時，就會有多個通知傳遞至裝置 (每個通知各用於一個範本)。如此一來，您就能讓相同訊息顯示在多個視覺通知中，例如以徽章形式和 Windows 市集應用程式中的快顯通知形式。

完成下列步驟，可使用範本傳送跨平台通知：

1. 在 Visual Studio 的 [方案總管] 中展開 [控制器] 資料夾，然後開啟 RegisterController.cs 檔案。 

2. 在 **Post** 方法中找出建立新註冊的程式碼區塊，並將 `switch` 內容取代為下列程式碼：

		switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version="1.0" encoding="utf-8"?>" +
                    "<wp:Notification xmlns:wp="WPNotification">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{"aps":{"alert":"$(message)"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{"data":{"msg":"$(message)"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
	
	這段程式碼會呼叫平台特有方法來建立範本註冊，而非原生註冊。不需要修改現有註冊，因為範本註冊源自原生註冊。

3. 在 [通知] 控制器中，將 **sendNotification** 方法取代為下列程式碼：

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);   

            return Request.CreateResponse(HttpStatusCode.OK);
        }

	這段程式碼會同時將通知傳送至所有平台，完全不需要指定原生裝載。通知中心將以所提供的 _tag_ 值 (指定於註冊的範本中) 建置並傳遞正確的裝載到每個裝置。

4. 重新發佈您的 WebApi 後端專案。

5. 重新執行用戶端應用程式，然後驗證註冊已成功。

6. (選用) 將此用戶端應用程式部署到第二個裝置，然後執行此應用程式。

	請留意到，每個裝置上都會顯示通知。

## 後續步驟

您已完成本教學課程，現在可參閱下列主題進一步了解通知中心和範本：

+ **[使用通知中樞傳送即時新聞]** <br/>示範另一個使用範本的案例 

+  **[Azure 通知中心概觀][Templates]**<br/>概觀主題包含範本的詳細資訊。

+  **[Windows 市集的通知中心作法]**<br/> (英文) 包含範本運算式語言參考。



<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Management Portal]: https://manage.windowsazure.com/
[使用通知中樞傳送即時新聞]: notification-hubs-windows-store-dotnet-send-breaking-news.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[使用通知中心來通知使用者]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Windows 市集的通知中心作法]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
 

<!---HONumber=July15_HO1-->