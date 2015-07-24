<properties 
	pageTitle="如何以單一行動服務後端使用多個用戶端 | Azure 行動" 
	description="了解如何從鎖定不同行動平台的多個用戶端應用程式使用單一行動服務後端，包括 Windows 市集和 Windows Phone。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/>
<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# 從單一行動服務支援多重裝置平台
 
使用 Azure 行動服務來進行行動應用程式開發的最大好處之一，是能夠使用單一後端服務，在多重用戶端平台上支援您的應用程式。行動服務提供適用於所有主要裝置平台的原生用戶端程式庫，可讓您使用單一後端服務或跨平台開發人員工具來開發應用程式時，更加輕鬆。本主題討論使用單一行動服務後端時，讓您的應用程式在多重用戶端平台上執行所需的考量。

##<a id="push"></a>跨平台推播通知

行動服務會使用 Azure 通知中心，將推播通知傳送到所有主要裝置平台上的用戶端應用程式。通知中心提供一貫且統一基礎結構來建立和管理裝置註冊，以及傳送跨平台推播通知。通知中心支援使用下列平台特定通知服務來傳送推播通知：

+ 適用於 iOS 應用程式的 Apple 推播通知服務 (APNS)
+ 適用於 Android 應用程式的 Google 雲端通訊 (GCM) 服務
+ 適用於 Windows 市集、Windows Phone 8.1 市集和通用 Windows 應用程式的 Windows 通知服務 (WNS) 
+ 適用於 Windows Phone Silverlight 應用程式的 Microsoft 推播通知服務 (MPNS)

>[AZURE.NOTE]通知中心目前不支援使用 WNS 將推播通知傳送至 Windows Phone Silverlight 8.1 應用程式。您必須使用 MPNS 將通知傳送至 Silverlight 以及 Windows Phone 8.0 和 7.0 應用程式。

如需詳細資訊，請參閱 [Azure 通知中心]。

您可以使用平台特定行動服務用戶端程式庫中的註冊功能，或是使用行動服務 REST API 來建立用戶端註冊。通知中心支援兩種裝置註冊：

+ **原生註冊**<br/>原生註冊會依平台特定的推播通知服務而調整。將通知傳送至使用原生註冊來註冊的裝置時，您必須在行動服務中呼叫平台特定的 API。若要將通知傳送至多重平台上的裝置，則需要多重平台特定的呼叫。   
  
+ **範本註冊**<br/>通知中心也支援平台特定的範本註冊。藉由使用範本註冊，您可以使用單一 API 呼叫將通知傳送至執行於任何已註冊平台上的應用程式。如需詳細資訊，請參閱[傳送跨平台通知給使用者]。

>[AZURE.NOTE]嘗試將訊息傳送至未註冊任何裝置的原生裝置平台時，會發生錯誤。傳送範本通知時，不會發生此錯誤。

下列區段中的資料表會連結至用戶端特定的教學課程，為您示範如何從 .NET 和 JavaScript 後端行動服務來實作推播通知。

###.NET 後端

在 .NET 後端行動服務中，若要傳送通知，您要在從 [ApiServices.Push] 屬性取得的 [PushClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx) 物件上呼叫 [SendAsync](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx) 方法。所傳送的推播通知 (原生或範本)，取決於傳遞至 [SendAsync](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx) 方法的特定 [IPushMessage] 衍生物件，如下表所示：

|平台 |[APNS](mobile-services-dotnet-backend-ios-get-started-push.md)|[GCM](mobile-services-dotnet-backend-android-get-started-push.md) |[WNS](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-dotnet-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|原生|[ApplePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx) |[GooglePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx) |[WindowsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx) | [MpnsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx) |

下列程式碼會將推播通知從 .NET 後端服務傳送至所有 iOS 和 Windows 市集裝置註冊：

	// Define a push notification for APNS.
	ApplePushMessage apnsMessage = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));    

	// Define a push notification for WNS.
	WindowsPushMessage wnsMessage = new WindowsPushMessage();
    wnsMessage.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                         @"<toast><visual><binding template=""ToastText01"">" +
                         @"<text id=""1"">" + item.Text + @"</text>" +
                         @"</binding></visual></toast>";
    
	// Send push notifications to all registered iOS and Windows Store devices. 
    await Services.Push.SendAsync(apnsMessage);
	await Services.Push.SendAsync(wnsMessage);

如需如何將推播通知傳送至其他原生用戶端平台的範例，請按一下上表標頭中的平台連結。

當您使用範本用戶端註冊，而不是原生用戶端註冊時，若要傳送相同通知，只需要透過單一呼叫至 [SendAsync]，提供 [TemplatePushMessage] 物件，如下所示：

	// Create a new template message and add the 'message' parameter.    
	var templatePayload = new TemplatePushMessage();
    templatePayload.Add("message", item.Text);

	// Send a push notification to all template registrations.
    await Services.Push.SendAsync(templatePayload); 
 
###JavaScript 後端

在 JavaScript 後端行動服務中，若要傳送通知，您要在從全域**推送物件**取得的平台特定物件上，呼叫 [send] 方法，如下表所示：

|平台 |[APNS](mobile-services-javascript-backend-ios-get-started-push.md)|[GCM](mobile-services-javascript-backend-android-get-started-push.md) |[WNS](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-javascript-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|原生|[apns 物件](http://msdn.microsoft.com/library/azure/jj839711.aspx) |[gcm 物件](http://msdn.microsoft.com/library/azure/dn126137.aspx) |[wns 物件](http://msdn.microsoft.com/library/azure/jj860484.aspx) | [mpns 物件](http://msdn.microsoft.com/library/azure/jj871025.aspx) |

下列程式碼會將推播通知傳送至所有 Android 和 Windows Phone 註冊：

	// Define a push notification for GCM.
	var gcmPayload = 
    '{"data":{"message" : item.text }}';

	// Define the payload for a Windows Phone toast notification.
	var mpnsPayload = '<?xml version="1.0" encoding="utf-8"?>' +
    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
    '</wp:Text2></wp:Toast></wp:Notification>';

	// Send push notifications to all registered Android and Windows Phone 8.0 devices. 
	push.mpns.send(null, mpnsPayload, 'toast', 22, {
            success: function(pushResponse) {
                // Push succeeds.
                },              
                error: function (pushResponse) {
                    // Push fails.
                    }
                });
    push.gcm.send(null, gcmPayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },              
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

如需如何將推播通知傳送至其他原生用戶端平台的範例，請按一下上表標頭中的平台連結。

當您使用範本用戶端註冊，而不是原生用戶端註冊時，若要傳送相同通知，只需要透過單一呼叫至全域**推送物件**上的 [send] 函數，提供範本訊息裝載，如下所示：

	// Create a new template message with the 'message' parameter.    
	var templatePayload = { "message": item.text };

	// Send a push notification to all template registrations.
    push.send(null, templatePayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },              
                error: function (pushResponse) {
                    // Push fails.
                    }
                }); 

##<a id="xplat-app-dev"></a>跨平台應用程式開發
若要開發適用於所有主要行動裝置平台的原生行動裝置應用程式，您 (或您的組織) 必須至少要有 Objective-C、Java 和 C# 或 JavaScript 程式設計語言的專業能力。跨這些相異平台開發時，為了顧及成本，有些開發人員會為其應用程式選擇完全以網頁瀏覽器為基底的使用經驗。不過，這種以網頁為基底的使用經驗無法存取大部分原生資源，因而無法提供使用者對其行動裝置期待的豐富經驗。

有跨平台工具可以在行動裝置上提供更豐富的原生使用經驗，但仍共用單一程式碼基底，最常見的就是 JavaScript。行動服務提供下列開發平台的快速入門教學課程，可讓您輕鬆建立及管理跨平台應用程式開發平台的後端服務：

+ [**Appcelerator**](http://go.microsoft.com/fwlink/p/?LinkId=509987)<br/>Appcelerator 可讓您使用 JavaScript 來開發單一應用程式，編譯成能夠在所有行動裝置平台上執行的原生應用程式。如此可提供在 UI 中的豐富使用者經驗、對所有原生裝置資源的存取權，以及原生應用程式效能。如需詳細資訊，請參閱 [Appcelerator 教學課程][Appcelerator]。
 
+ [**PhoneGap**](https://go.microsoft.com/fwLink/p/?LinkID=390707)**/**[**Cordova**](http://cordova.apache.org/)<br/>PhoneGap (Apache Cordova 專案的散發套件) 是免費的開放原始碼架構，可讓您使用標準化的 Web API、HTML 和 JavaScript 來開發在 Android、iOS 和 Windows 裝置上執行的單一應用程式。PhoneGap 提供 Web 檢視形式的 UI，但能夠存取推播通知、加速計、相機、儲存體、地理位置和應用程式內部瀏覽器等裝置上的原生資源，因而增強了使用者經驗。如需詳細資訊，請參閱 [PhoneGap 快速入門教學課程][PhoneGap]。
	
	Visual Studio 現在也可以讓您使用 Visual Studio 的多重裝置混合式應用程式擴充功能 (發行前軟體) 來建置跨平台 Cordova 應用程式。如需詳細資訊，請參閱[藉由 HTML 和 JavaScript 開始使用多重裝置混合式應用程式](http://msdn.microsoft.com/library/dn771545.aspx) (英文)。

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>Sencha Touch 提供一組針對觸控螢幕最佳化的控制項，從單一 HTML 和 JavaScript 程式碼基底，在各種行動裝置上提供類原生經驗。Sencha Touch 可與 PhoneGap 或 Cordova 程式庫搭配使用，讓使用者能夠存取原生裝置資源。如需詳細資訊，請參閱 [Sencha Touch 快速入門教學課程][Sencha]。

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>Xamarin 可讓您建立 iOS 與 Android 裝置皆適用的完全原生應用程式，具有完全原生 UI，並可存取所有裝置資源。Xamarin 應用程式是以 C# 編碼，而不是以 Objective-C 和 Java 編碼。如此可讓 .NET 開發人員將應用程式發佈至 iOS 和 Android，以及從 Windows 專案共用程式碼。Xamarin 從 C# 程式碼提供 iOS 和 Android 裝置的完全原生使用者經驗。這可讓您在 iOS 和 Android 裝置上，從 Windows 應用程式重複使用部分行動服務程式碼。如需詳細資訊，請參閱下面的 [Xamarin 開發](#xamarin)。

	您可以使用 Xamarin Studio 或 Visual Studio 2013 建立 Xamarin 應用程式。如需詳細資訊，請參閱 [Visual Studio 中的跨平台開發](http://msdn.microsoft.com/library/dn771552.aspx) (英文)。


##<a id="shared-vs"></a>在 Visual Studio 專案中共用及重複使用程式碼

行動服務包含 .NET 用戶端程式庫，它是 .NET Framework 可攜式類別庫 (PCL)，可支援在所有 Windows 平台上進行開發。如需詳細資訊，請參閱[如何將 .NET 用戶端與行動服務搭配使用]。它可以讓您輕鬆地在多重 C# 專案中重複使用相同的行動服務程式碼，例如用於資料存取或驗證。

若要在專案之間共用及重複使用您的 C# 程式碼，有一個常用的方法，那就是實作 Model-View-ViewModel (MVVM) 模式，並跨多重平台共用組件。您可以在 Visual Studio 的可攜式類別庫專案中實作模型及檢視模型類別，然後建立針對不同平台自訂的檢視。舉例來說，跨平台共用的模型程式碼可從行動服務之類的來源，以平台無從驗證的方式擷取資料。MSDN 文件庫提供了<a href="http://msdn.microsoft.com/library/gg597391(v=vs.110)">概觀和範例</a>、<a href="http://msdn.microsoft.com/library/gg597392(v=vs.110)">API 差異</a>的討論、<a href="http://msdn.microsoft.com/library/hh563947(v=vs.110)">使用可攜式類別庫實作 MVVM 模式</a>的範例、其他<a href="http://msdn.microsoft.com/library/windowsphone/develop/jj714086(v=vs.105).aspx">慣用指引</a>，以及可攜式類別庫專案中<a href="http://msdn.microsoft.com/library/hh871422(v=vs.110)">管理資源</a>的相關資訊。

除了這個一般指導方針，Visual Studio 也有提供特定的機能，可讓您跨多重用戶端應用程式專案重複使用行動服務程式碼，以下各節將會加以討論。如需使用 Visual Studio 2013 來建置跨平台應用程式的一般詳細資訊，請參閱 [Visual Studio 中的跨平台開發](http://msdn.microsoft.com/library/dn771552.aspx) (英文)。

### 通用 Windows 應用程式

Visual Studio 2013 Update 2 新增對通用 Windows 應用程式專案的支援。通用應用程式解決方案包括 Windows 市集 8.1 和 Windows Phone 市集 8.1 應用程式專案，以及共用程式碼專案。在這種專案中，共用程式碼會被當作 Windows 市集和 Windows Phone 專案的一部分。如需詳細資訊，請參閱[建置適用於所有 Windows 裝置的通用 Windows 應用程式] (英文)。C#/XAML 和 JavaScript/HTML 都可以用來撰寫通用 Windows 應用程式。

依預設，[Azure 管理入口網站]中的 [行動服務] 快速入門索引標籤會產生通用 Windows 應用程式版的 TodoList 範例應用程式來協助您開始使用。您可以選擇下載 C#/XAML 或 JavaScript/HTML 版本的專案。如需詳細資訊，請參閱[開始使用行動服務](../mobile-services-windows-store-get-started.md)。

>[AZURE.NOTE]入口網站提供的 C# 版快速入門應用程式專案會共用 MainPage.xaml.cs 程式碼後置頁面，但不會使用檢視模型。如需將 TodoList 應用程式當作使用 MVVM 之 C# 版通用 Windows 應用程式專案的範例，請參閱[使用 MVVM 的 Azure 行動服務通用 Windows 應用程式] (英文)。

###<a id="xamarin"></a>Xamarin 開發

您可以運用您的 Visual Studio 和 C# 開發經驗，使用 Xamarin 以及 Visual Studio 或 Xamarin Studio 來開發適用於 iOS 和 Android 的應用程式。Xamarin 使用 .NET Framework 的跨平台實作，可讓您使用 C# 程式碼來開發 iOS 和 Android 應用程式。藉由使用 Xamarin，您可以運用 Windows 專案中，使用行動服務 .NET 用戶端程式庫來存取行動服務的現有程式碼。如需詳細資訊，請參閱 [Visual Studio 中的跨平台開發](http://msdn.microsoft.com/library/dn771552.aspx) (英文)。

若要開始建置使用行動服務的 Xamarin 應用程式，請參閱 Xamarin 快速入門教學課程 ([iOS](mobile-services-ios-get-started.md) / [Android](mobile-services-android-get-started.md))。


### Windows 市集和 Windows Phone Silverlight 應用程式

在 Windows Phone 8.1 中，您可以選擇使用舊有的 Silverlight 型 XAML 來開發應用程式，或使用 Windows Runtime 型 XAML，它可讓您開發通用 Windows 應用程式。如需 Windows Phone 8.1 Silverlight 應用程式和 Windows Phone 市集 8.1 應用程式的詳細資訊，請參閱 [Windows Phone 8 開發人員的下一步] (英文)。

行動服務 .NET 用戶端程式庫支援 Windows Phone 市集 8.1 和 Windows Phone Silverlight 8.1 應用程式。由於無法從相同的專案同時建置 Windows Runtime 和 Windows Phone Silverlight 應用程式，所以您應考慮上述 PCL 和 MVVM 之類的程式碼重複使用策略。

>[AZURE.NOTE]如果在 Windows Runtime 和 Windows Phone Silverlight 應用程式中，都要使用 Microsoft 帳戶來進行單一登入用戶端驗證，您必須先在 Windows 市集儀表板上註冊 Windows Runtime 應用程式。這是因為在您建立 Windows Phone 的 Live Connect 註冊後，您即無法建立 Windows 市集的註冊。如需如何執行此作業的詳細資訊，請閱讀**使用 Live Connect 單一登入驗證 Windows 市集應用程式** ([Windows 市集][SSO Windows Store]/[Windows Phone][SSO Windows Phone]) 主題。


<!-- URLs -->
[Azure 管理入口網站]: https://manage.windowsazure.com
[Azure 通知中心]: /develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows Store]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /develop/mobile/tutorials/single-sign-on-wp8/
[Tutorials and resources]: /develop/mobile/resources/
[Get started with Notification Hubs]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[傳送跨平台通知給使用者]: /manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Get started with push Windows dotnet]: /develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Get started with push Windows js]: /develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Get started with push Windows Phone]: /develop/mobile/tutorials/get-started-with-push-wp8/
[Get started with push iOS]: /develop/mobile/tutorials/get-started-with-push-ios/
[Get started with push Android]: /develop/mobile/tutorials/get-started-with-push-android/
[Dynamic schema]: http://msdn.microsoft.com/library/windowsazure/jj193175.aspx
[如何將 .NET 用戶端與行動服務搭配使用]: documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[send]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[TemplatePushMessage]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: mobile-services-javascript-backend-phonegap-get-started.md
[Sencha]: partner-sencha-mobile-services-get-started.md
[Appcelerator]: ../partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started.md
[SendAsync]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[ApiServices.Push]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[IPushMessage]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[Windows Phone 8 開發人員的下一步]: http://msdn.microsoft.com/library/windows/apps/dn655121(v=vs.105).aspx
[建置適用於所有 Windows 裝置的通用 Windows 應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[使用 MVVM 的 Azure 行動服務通用 Windows 應用程式]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de
 

<!---HONumber=July15_HO1-->