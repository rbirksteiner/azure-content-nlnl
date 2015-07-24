<properties 
	pageTitle="使用 Azure 應用程式服務將推播通知新增至 Windows 通用應用程式" 
	description="了解如何使用 Azure 應用程式服務將推播通知傳送至 Windows 通用應用程式。" 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="yuaxu"/>

# 將推播通知新增至 Windows 市集應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

本主題說明如何從 .NET 後端使用 Azure 應用程式服務將推播通知傳送至 Windows 通用應用程式。完成時，您將會在插入記錄時，從 .NET 後端將推播通知傳送至所有已註冊的 Windows 通用應用程式。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [針對推播通知註冊應用程式](#register)
2. [設定](#configure)
3. [更新服務以傳送推播通知](#update-service)
4. [將推播通知新增至應用程式](#add-push)
5. [在應用程式中測試推播通知](#test)

本教學課程以應用程式服務行動應用程式快速入門為基礎。在開始本教學課程之前，您必須先完成 [開始使用 App Service mobile apps]。

若要完成此教學課程，您需要下列項目：

* 有效的 [Microsoft 市集帳戶](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>。

##<a id="register"></a>針對推播通知註冊應用程式

若要使用 Azure 應用程式服務將推播通知傳送至 Windows 通用應用程式，您必須將應用程式提交到 Windows 市集。接著，您必須設定行動應用程式推播通知服務認證，才能與 WNS 整合。

1. 如果您尚未註冊您的應用程式，請瀏覽至 Windows 市集應用程式之開發人員中心的<a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">提交應用程式頁面</a>，並使用您的 Microsoft 帳戶登入，然後按一下 [**應用程式名稱**]。

    ![][0]

2. 在 **[應用程式名稱]** 中為您的應用程式輸入名稱，然後依序按一下 **[保留應用程式名稱]** 和 **[儲存]**。

    ![][1]

    This creates a new Windows Store registration for your app.

4. 在 [方案總管] 中，以滑鼠右鍵按一下 Windows 市集應用程式專案，然後依序按一下 [市集] 和 [將應用程式與市集建立關聯...]。

    ![][3]

    隨即顯示 [將您的應用程式與 Windows 市集建立關聯] 精靈。

5. 在此精靈中，按一下 [登入]，然後使用您的 Microsoft 帳戶登入。

6. 按一下您在步驟 2 中註冊的應用程式，按 [下一步]，然後按一下 [關聯]。

    ![][4]

    這會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。

7. (選用) 針對 Windows Phone 市集應用程式專案重複步驟 4-6。

7. 回到新應用程式的 Windows 開發人員中心頁面，按一下 **[服務]**。

    ![][5]

8. 在 [服務] 頁面中，按一下 [Microsoft Azure 行動服務] 下的 [Live 服務網站]。

    ![][17]

9. 記下 [應用程式設定] 索引標籤中的 [用戶端密碼] 和 [封裝安全性識別碼 (SID)] 的值。

    ![][6]

    > [AZURE.NOTE]**安全性提示**：用戶端密碼和封裝 SID 是重要的安全性認證。請勿與任何人共用這些值，或與您的應用程式一起散發密碼。

##<a id="configure"></a>設定行動應用程式以傳送推播要求

1. 登入 [Azure 預覽入口網站]，依序選取 [**瀏覽**]、[**行動應用程式**]，然後按一下您的應用程式。按一下 [推播通知] 服務。

2. 在 Windows 通知服務中，輸入您的 [**用戶端密碼**] 和 [**封裝安全性識別碼 (SID)**]，並加以儲存。

應用程式服務行動應用程式現在已設定成可與 WNS 搭配運作。

<!-- URLs. -->
[Azure 預覽入口網站]: https://portal.azure.com/

##<a id="update-service"></a>更新服務以傳送推播通知

現在應用程式已經啟用了推播通知，您必須更新應用程式後端以傳送推播通知。

1. 在 Visual Studio 中，以滑鼠右鍵按一下方案，然後按一下 [管理 NuGet 封裝]。

2. 搜尋 **Microsoft.Azure.NotificationHubs**，然後為方案中的所有專案按一下 [**安裝**]。

3. 在 Visual Studio 的 [方案總管] 中，展開行動後端專案中的 **Controllers** 資料夾。開啟 TodoItemController.cs。在檔案頂端新增下列 `using` 陳述式：

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;

4. 將下列程式碼片段新增至 **InsertAsync** 呼叫後面的 `PostTodoItem` 方法：

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // windows payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + item.Text + @"</text></binding></visual></toast>";

        await Hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

    此程式碼會指示與此行動應用程式相關聯的通知中心，在 todo 項目插入後傳送推播通知。


## <a name="publish-the-service"></a>將行動後端發佈至 Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##<a id="update-service"></a>將推播通知新增至應用程式

1. 在 Visual Studio 中，以滑鼠右鍵按一下方案，然後按一下 [管理 NuGet 封裝]。 

    此時會顯示 [管理 NuGet 封裝] 對話方塊。

2. 在應用程式服務行動應用程式用戶端 SDK 中搜尋受管理的項目，然後按一下 [**安裝**]，選取方案中的所有專案，並接受使用條款。

    這會下載、安裝所有專案中的參考，並將其新增至適用於 Windows 的 Azure Mobile Push 程式庫。

3. 開啟 **App.xaml.cs** 專案檔案，並新增下列 `using` 陳述式：

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.MobileServices;

    在通用專案中，此檔案位於 `<project_name>.Shared` 資料夾中。

4. 在相同檔案中，將下列 **InitNotificationsAsync** 方法定義新增至 [**應用程式**] 類別：
    
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            
            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    此程式碼會從 WNS 中擷取應用程式的 ChannelURI，然後向您的應用程式服務行動應用程式註冊該 ChannelURI。
    
5. 在 **App.xaml.cs** 中的 **OnLaunched** 事件處理常式頂端，將下列呼叫新增至新 **InitNotificationsAsync** 方法：

        InitNotificationsAsync();

    這樣可保證在每次啟動應用程式時都會註冊存留期較短的 ChannelURI。

6. 在 [方案總管] 中，按兩下 Windows 市集應用程式的 **Package.appxmanifest**，並將 [**通知**] 中的 [**支援快顯通知**] 設為 [**是**]：

    從 [檔案] 功能表中，按一下 [全部儲存]。

7. (選用) 針對 Windows Phone 市集應用程式專案重複上一個步驟。

8. 按 **F5** 鍵以執行應用程式。

您的應用程式現在已能夠接收快顯通知。

##<a id="test"></a>在應用程式中測試推播通知

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-windows-universal-test-push-preview](../../includes/app-service-mobile-dotnet-backend-windows-universal-test-push-preview.md)]

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-submit-win8-app.png
[1]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/notification-hub-associate-win8-app.png
[4]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-select-app-name.png
[5]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit-app.png
[6]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[17]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit2-app.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582

<!---HONumber=62-->