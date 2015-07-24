<properties 
	pageTitle="Azure 通知中樞 - 診斷指導方針" 
	description="有關如何診斷 Azure 通知中樞常見問題的指導方針。" 
	services="notification-hubs" 
	documentationCenter="Mobile" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="wesmc"/>

#Azure 通知中樞 - 診斷指導方針

##概觀

最常從 Azure 通知中樞客戶口中聽到的其中一個問題是，如何得知從應用程式後端傳送的通知為什麼無法顯示於用戶端裝置上；這些通知在何處遭到捨棄，為什麼會遭到捨棄，該如何修正？在本文章中，我們將探討通知遭到捨棄及未抵達裝置的各種原因。我們也會探討用來分析及找出根本原因的方法。

首先，請務必了解 Azure 通知中樞如何將通知推送到裝置。![][0]

在典型的傳送通知流程中，系統會從**應用程式後端**將訊息傳送到 **Azure 通知中樞 (NH)**。接著，Azure 通知中樞會處理所有涉及已設定之標記和標記運算式的註冊來判斷「目標」(亦即所有需要接收推播通知的註冊)。這些註冊可以跨越任何或所有我們支援的平台，包括 iOS、Google、Windows、Windows Phone、Kindle 及用於中國 Android 的百度。建立目標後，NH 會將通知分配到多個註冊批次，然後再推送到裝置平台特有的**推播通知服務 (PNS)** (如 Apple 的 APNS、Google 的 GCM 等)。NH 會根據您在 [設定通知中樞] 頁面的 Azure 入口網站中設定的認證來驗證個別 PNS。隨後，PNS 會將通知轉送到各個**用戶端裝置**。以上是平台建議的推送通知傳遞方法。請注意，通知傳遞最終的旅程發生在平台 PNS 和裝置之間。因此我們有四個主要元件 - *用戶端*、*應用程式後端*、*Azure 通知中樞 (NH)* 和*推播通知服務 (PNS)*，而且其中任何一項可能會導致通知遭到捨棄。如需此架構的詳細資料，請參閱[通知中樞概觀]。

通知傳遞失敗可能會發生在初始測試/執行階段，其可能代表組態問題；也有可能發生在所有或部分通知遭到捨棄的生產環境中，其代表較深層的應用程式或訊息模式問題。在本節的後續內容中，我們會探討各種通知捨棄案例 (常見和罕見)，有些案例是顯而易見的，有些案例則不太明顯。

##Azure 通知中樞組態錯誤 

Azure 通知中樞需要在開發人員應用程式的內容中自行驗證，才能成功地將通知傳送到各個 PNS。若要讓 Azure 通知中樞自行驗證，開發人員需要向各平台 (Google、Apple、Windows 等) 建立開發人員帳戶，然後在取得認證 (需要在 Azure 入口網站的 [通知中樞組態] 區段中設定) 之處註冊應用程式。如果通知無法送出，第一個步驟應是確認於通知中樞內設定的認證是否正確，與在平台專屬開發人員帳戶下由應用程式建立的認證相符。[使用者入門教學課程]的逐步教學法將有助於您檢驗此程序。以下是一些常見的組態錯誤：

1. **一般**
 
	a) 確認通知中樞名稱與以下各項相同 (無錯別字)：

	- 從用戶端註冊之處 
	- 從後端傳送通知之處  
	- 設定 PNS 認證之處 
	- 在用戶端和後端上設定 SAS 認證的通知中樞。 
		
	b) 確認在用戶端和應用程式後端上使用正確的 SAS 組態字串。根據經驗法則，您必須在用戶端上使用 **DefaultListenSharedAccessSignature**，在應用程式後端上使用 **DefaultFullSharedAccessSignature** (賦予將通知傳送到 NH 的權限)。

2. **Apple Push Notification Service (APNS) 組態**
 
	您必須維護兩個不同的中樞，一個供生產環境之用，另一個供測試之用。這表示您需要將於沙箱環境中使用的憑證上載到某一個中樞，再將於生產環境中使用的憑證上載到另一個中樞。請勿嘗試將不同類型的憑證上載到同一個中樞，因為這樣會導致通知徹底失敗。如果您發現自己意外地將不同類型的憑證上載到同一個中樞，建議您刪除中樞，重新開始。如果您基於某些原因無法刪除中樞，至少必須刪除中樞內所有現有註冊。

3. **Google 雲端通訊 (GCM) 組態**

	a) 確認您在雲端專案下啟用的是 "Google Cloud Messaging for Android"。
	
	![][2]
	
	b) 確認您正在建立「伺服器金鑰」，同時取得 NH 用來驗證 GCM 的認證。
	
	![][3]
	
	c) 確認您已在用戶端上設定「專案 ID」，其為可從儀表板取得的全數值實體。
	
	![][1]

##應用程式問題

1) **標記/ 標記運算式**

如果您使用標記或標記運算式來區分對象，在傳送通知時，可能經常會找不到在傳送呼叫中指定之標記/標記運算式所代表的目標。在傳送通知時，建議您最好檢閱註冊以確認標記相符，並且驗證通知回條僅來自具有這些註冊的用戶端。例如，假設與 NH 相關的註冊均有 "Politics" 標記，如果您傳送具有 "Sports" 標記的通知，系統不會將該通知傳送到任何裝置。複雜的案例可能涉及只註冊 "Tag A" OR "Tag B" 的標記運算式，然而在傳送通知時，您設定的目標為 "Tag A && Tag B"。以下自我診斷秘訣小節提供檢閱註冊和註冊之標記的方法。

2) **範本問題**

如果您使用範本，請務必遵守[範本指引]所述的指導方針。

3) **無效的註冊**

假設通知傳送的適當目標搜尋起因於通知中樞設定正確且所有標記/標記運算式的用法正確，NH 會以平行方式引發數個處理批次，每個批次都會傳送訊息給一組註冊。

> [AZURE.NOTE]由於處理採平行方式進行，因此我們無法保證通知的傳遞順序。

現在我們已根據「最多一次」訊息傳遞模式將 Azure 通知中樞最佳化。這表示我們會嘗試刪除重複的項目，避免系統將通知多次傳遞給同一部裝置。為了確保功效，我們會先檢驗註冊並確認系統只會將一則訊息傳送給一個裝置識別項，然後再實際將訊息傳送給 PNS。隨著系統將各個批次傳送到 PNS，PSN 會接受及驗證註冊。它可能會在批次的一或多個註冊中偵測到錯誤，再將錯誤傳回 Azure NH 並停止處理，全然捨棄該批次。對於使用 TCP 串流通訊協定的 APNS 來說，這種情況尤其顯著。由於我們已針對最多一次傳遞模式最佳化，因此請注意，我們無法重試失敗的批次，因為我們無法得知 PNS 捨棄了整個批次或部分批次。儘管如此，PNS 會告知 Azure NH 引發失敗的註冊為何，所以我們可以根據意見反應將該註冊從資料庫移除。這意味著有可能是一個註冊批次或註冊批次的子集無法接收通知，然而由於我們已清除不當的註冊，因此當我們下次嘗試進行傳送時，成功傳送的機率很高。由於目標裝置的數量規模日益成長 (我們有些客戶傳送通知給數百萬部裝置)，捨棄零散的批次並不會對接收通知之裝置的整體百分比造成太大影響，然而如果您只是傳送少量通知但卻發生一些 PNS 錯誤，可能會發現所有或大部分的通知無法順利抵達。如果這樣的行為反覆出現，您就必須找出不當的註冊並予以刪除。您肯定要刪除所有手動形成的註冊，因為它們是造成通知捨棄最常見的原因。如果您有測試環境，還可以直接刪除所有註冊，因為當裝置上的應用程式開啟時，會再次嘗試向通知中樞重新註冊，如此可確保此後建立的註冊都是有效的註冊。

##PNS 問題

待各個 PNS 接收到通知訊息後，它們必須負責將通知傳遞給裝置。Azure 通知中樞與 PNS 的運作並不相關，也無法控制它們將通知傳遞給裝置的時間和成功與否。由於平台通知服務已臻完備，因此通知經常能在幾秒鐘之內從 PNS 抵達裝置。然而，如果 PNS 處於節流狀態，Azure 通知中樞會套用指數退回策略；如果 PNS 持續在 30 分鐘之內無法聯繫，我們將採取既定的原則讓這些訊息到期並永久捨棄。

如果 PNS 嘗試傳遞通知但裝置已離線，PNS 會在一段有限的時間內儲存通知，並在可聯繫裝置時再行傳遞。PNS 只會針對每個應用程式儲存一則最近的通知。如果在裝置離線時傳送多則通知，每則新通知都會導致前一則通知遭到捨棄。這個只保留最新通知的行為在 APNS 中稱為聯合通知，在 GCM 中稱為摺疊 (使用摺疊索引鍵之故)。如果裝置長期處於離線狀態，針對該裝置儲存的所有通知都將遭到捨棄。資料來源 - [APNS 指引]和 [GCM 指引]

有了 Azure 通知中樞，您可以使用泛型 `SendNotification` API (如適用於 .NET SDK 的 – `SendNotificationAsync`) 透過 HTTP 標頭傳送聯合索引鍵，該 API 也會採用以原狀傳送到各 PNS 的 HTTP 標頭。

##自我診斷秘訣

在以下內容中，我們將檢視各種診斷及找出通知中樞問題之根本原因的途徑：

###驗證認證

1. **PNS 開發人員入口網站**

	使用我們的[使用者入門教學課程]於各個 PNS 開發人員入口網站 (APNS、GCM、WNS 等) 驗證認證。

2. **Azure 管理入口網站**

	移至 [設定] 索引標籤以檢閱認證，以及與從 PNS 開發人員入口網站取得的認證比對。

	![][4]

###驗證註冊

1. **Visual Studio**

	如果您使用 Visual Studio 來從事開發，可以連接 Microsoft Azure 來檢視及管理多種 Azure 服務，包括從伺服器總管檢視及管理通知中樞。這能為您的開發/測試環境帶來許多助益。

	![][9]

	您可檢視及管理中樞內已按照平台歸類的註冊、原生或範本註冊、任何標記、PNS 識別項、註冊 ID 及到期日。您也可以隨時編輯註冊；如果您想要編輯任何標記，這項功能便能派上用場。

	![][8]
 
	> [AZURE.NOTE]Visual Studio 的編輯註冊功能僅應用於註冊數量有限的開發/測試期間。如果您需要修正大量註冊，請考慮使用匯出/匯入註冊 (僅適用於 Standard 階層) 所述的[匯出/匯入註冊功能]。

2. **服務匯流排總管**

	有許多客戶使用[服務匯流排總管]所述的服務匯流排總管來檢視及管理通知中樞。其為可從 code.microsoft.com - [服務匯流排總管程式碼]取得的開放原始碼專案。

###驗證訊息通知

1. **Azure 入口網站**

	您可以前往 [偵錯] 索引標籤傳送測試通知給用戶端，這樣操作不需要任何運作中的服務後端即可完成。

	![][7]

2. **Visual Studio**

	Visual Studio 還能讓您輕鬆舒適的傳送測試通知：

	![][10]

	以下文章提供 Visual Studio 通知中樞 Azure 總管功能的詳細資料：
	
	- [VS 伺服器總管概觀]
	- [VS 伺服器總管部落格文章 - 1]
	- [VS 伺服器總管部落格文章 - 2]

###偵錯失敗的通知 / 檢閱通知結果

**EnableTestSend 屬性**

當您透過通知中樞傳送通知時，最初系統只會將其加入佇列並等候 NH 處理，找出通知的所有目標，最後 NH 才會將通知傳送給 PNS。這表示當您使用 REST API 或任何用戶端 SDK 時，成功傳回傳送呼叫只代表系統已將訊息順利地加入通知中樞的佇列。我們無從得知 NH 最終將訊息傳送給 PNS 時發生什麼事。如果通知未抵達用戶端裝置，有可能是當 NH 嘗試將訊息傳遞給 PNS 時發生錯誤 (如承載大小超出 PNS 允許的上限或在 NH 中設定的認證無效等)。為了深入了解 PNS 錯誤，我們推出名為 [EnableTestSend 功能]的屬性。當您從入口網站或 Visual Studio 用戶端傳送測試訊息時，該屬性會自動啟用，供您查看詳細的偵錯資訊。以 .NET SDK 為例，您可以透過近期推出的 API 使用該屬性，我們最終也會將該屬性加入所有用戶端 SDK 中。若要搭配使用該屬性與 REST 呼叫，只要在傳送呼叫結尾附加名為 "test" 的 QueryString 參數即可，例如：

	https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*範例 (.NET SDK)*
 
假設您使用 .NET SDK 來傳送原生快顯通知：

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
 
`result.State` 只會在執行結束時陳述 `Enqueued`，不提供任何有關推播的歷程資訊。現在您可以使用 `EnableTestSend` 布林值屬性並將 `NotificationHubClient` 初始化，如此便能取得在傳送通知時遭遇之 PNS 錯誤的詳細狀態。此處的傳送呼叫必須在 NH 將通知傳遞給 PNS 來判斷結果後才會傳回，因此需要花費額外的時間才能傳回。
 
	bool enableTestSend = true;
	NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
	
	var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
	Console.WriteLine(outcome.State);
	
	foreach (RegistrationResult result in outcome.Results)
	{
	    Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
	}

*範例輸出*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong
 
此訊息指出在通知中樞內設定的認證無效或中樞內的註冊有問題，建議的途徑是刪除該註冊並讓用戶端重新建立，隨後再傳送訊息。
 
> [AZURE.NOTE]請注意，該屬性需在嚴密節流的情況下使用，其僅適用於註冊集有限的開發/測試環境。我們只會傳送偵錯通知給 10 部裝置。我們的處理中偵錯傳送次數也有每分鐘 10 次的限制。

###檢閱遙測 

1. **使用 Azure 入口網站**

	Azure 入口網站能讓您取得通知中樞上所有活動的簡要概觀。
	
	a) 在 [儀表板] 索引標籤中，您可以利用彙總檢視查看各平台的註冊、通知及錯誤。
	
	![][5]
	
	b) 您也可以從 [監視] 索引標籤加入其他多樣化的平台專用度量，以便深入了解當 NH 嘗試將通知傳送給 PNS 時傳回的任何 PNS 特有錯誤。
	
	![][6]
	
	c) 您應從檢閱 [**傳入訊息**]、[**註冊作業**]、[**成功的通知**] 開始，然後再移至各平台的索引標籤來檢閱 PNS 特有錯誤。
	
	d) 如果通知中樞的驗證設定錯誤，您會看見 PNS 驗證錯誤。這是提醒您檢查 PNS 認證的徵兆。

2) **程式設計存取**

以下文章提供更多詳細資料：

- [程式設計遙測存取]
- [透過 API 存取遙測範例] 

> [AZURE.NOTE]諸如**匯出/匯入註冊**、**透過 API 存取遙測**等數種遙測相關功能僅適用於 Standard 階層。如果您使用 Free 或 Basic 階層但嘗試使用以上功能，系統會顯示與此效果相關的例外狀況訊息 (使用 SDK 時) 和 HTTP 403 (禁止) (直接從 REST API 使用時)。請務必透過 Azure 管理入口網站移至 Standard 階層。

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png
 
<!-- LINKS -->
[通知中樞概觀]: notification-hubs-overview.md
[使用者入門教學課程]: notification-hubs-windows-store-dotnet-get-started.md
[範本指引]: https://msdn.microsoft.com/library/dn530748.aspx
[APNS 指引]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[GCM 指引]: http://developer.android.com/google/gcm/adv.html
[匯出/匯入註冊功能]: http://msdn.microsoft.com/library/dn790624.aspx
[服務匯流排總管]: http://msdn.microsoft.com/library/dn530751.aspx
[服務匯流排總管程式碼]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[VS 伺服器總管概觀]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[VS 伺服器總管部落格文章 - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[VS 伺服器總管部落格文章 - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend 功能]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[程式設計遙測存取]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[透過 API 存取遙測範例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

 

<!---HONumber=July15_HO2-->