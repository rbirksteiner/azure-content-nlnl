<properties 
   pageTitle="Azure Mobile Engagement 使用者介面：觸達作法"
   description="Azure Mobile Engagement 的使用者介面概觀" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="05/12/2015"
   ms.author="piyushjo"/>

# 如何開始使用及管理推送通知以推送給使用者

一旦 SDK 完全整合至應用程式後，您便可以開始使用 UI 的 [Reach] 區段，將通知推送給應用程式使用者。

## 進行第一個推送通知活動
-    確認您的 Reach 已藉由 SDK 整合到應用程式。 
-    選取您的應用程式
 
![First1][1]

-    前往 [Reach] 區段，並按一下 [新公告]
 
![First2][2]

-    建立新的活動並將它命名
 
 ![First3][3]

-    選取如何應該傳送通知，做為僅限應用程式內
 
![First4][4]

-    建立您想要推送的訊息
 
![First5][5]

-    您可以撰寫通知標題 (選擇性)。
-    撰寫推送訊息內容。
-    您可以上傳影像。請注意，檔案的大小不能超過 32,768 個位元組。
-    您也可以選取進一步的選項，但是由於本教學課程的焦點，我們稍後會看到。

-    將內容類型選取為僅限通知
 
![First6][6]

-    建立推送活動，它會顯示在活動清單。
 
![First7][7]

## 測試推送通知活動
![Test1][8]

-    登記裝置。
-    按一下您想要推送的裝置的核取方塊。
-    按一下 [測試] 按鈕以傳送推送到裝置。
 
![Test2][9]

-    啟動活動
 
![Test3][10]

-    既然您已建立活動，就只需要啟動它，以便將通知推送給您的使用者。
 
## 傳送個人化的推送
-    這個範例會建立推送，其中自訂的退款程式碼已輸入到推送通知中。
 
![Personalize1][11]

個人化運作方式是取代應用程式資訊標記中的標記，因此，您必須確定使用者已先定義適當的應用程式資訊。在此範例中，目標使用者已定義名為 rebate_code 的應用程式資訊標記。如您所見，在推送通知內容上方包含標記 ${rebate_code}，這表示它將由應用程式資訊標記的實際內容所取代。

> 警告：如果未定義使用者的應用程式資訊標記，使用者將不會收到推送內容。

-    結果
 
![Personalize2][12]

### 您可以進一步個人化通知的文字
![Personalize3][13]

-    包括通知的標題，
-    與訊息的內容。
-    選擇公告的類型 (文字檢視或網頁檢視)
 
![Personalize4][14]

### 公告的內文也可以個人化：
-    動作 URL，如果您想要自訂登陸頁面的話
-    標題，
-    訊息內文。
 
 
## 區分您的推送通知 (在應用程式內外)
-    選擇您要推送的通知類型、選取您的應用程式、前往 [Reach] 區段、選取或建立推送活動，然後前往 [通知] 區段。
 
-    按一下您要的「傳遞模式」。
-    如果您希望通知發生在特定活動 (畫面)，便按一下 [限制活動] 核取方塊。

![Differentiate1][15]

### 「僅限應用程式外」傳遞模式
![Differentiate2][16]

「僅限應用程式外」傳遞模式會在應用程式關閉時提供推送通知。這是標準的推送通知。當您選取「僅限應用程式外」時，您必須已提供應用程式建置所在平台 (APN 或 GCM) 上的憑證。

### 另請參閱
-  [Apple Push Notification Service – 憑證](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9)、Google Cloud Messaging – 憑證 (http://developer.android.com/google/gcm/index.html) 

### 「僅限應用程式內」傳遞模式
![Differentiate3][17]

「僅限應用程式內」傳遞模式會在應用程式執行時提供推送通知。針對此通知，您不需要經歷 APNS 和 GCM 系統。您可以使用應用程式內傳遞系統來觸達使用者。您完全可以自訂通知，並決定通知將會出現在哪個活動 (畫面)。

### 「隨時」傳遞模式
您可以選擇「隨時」傳遞模式，確保您不論應用程式是否正在執行都可觸達使用者。當您選取「隨時」時，您必須已提供應用程式建置所在平台 (APN 或 GCM) 上的憑證。
 
## 排定推送活動
### 計畫開始活動
![Shedule1][18]

時間是 3 月 21 日，您要進行公告，且計畫是在 3 月 22 日午夜。您不需要待在介面前就可以推送！ 您可以事先規劃傳送通知的確切時間。取消勾選 [無] 核取方塊，並選取開始時間：選擇您要開始推送活動的日期和時間。

### 計畫結束活動
![Shedule2][19]

您想要在 3 月 25 日下午 3 點停止活動，但您知道您到時候不會在場。您不需要待在介面前就可以推送！ 您可以事先規劃停止活動的確切時間。按一下 [無] 核取方塊或選取結束時間：選擇您要結束推送活動的日期和時間。

### 手動結束活動
![Shedule3][20]

根據預設，已選取 [無] 核取方塊。這表示只要在 [Reach] 區段啟動活動它便會開始，在 [Reach] 區段停止它便會結束。
 
> 注意：建立時若未設定活動的結束日期，推送內容會儲存在本機裝置上，並在下一次應用程式開啟時顯示，即使活動已手動結束也會顯示。

## 使用文字檢視增強推送通知
### 什麼是文字檢視？
![TextView1][21]

文字檢視是具有文字內容的快顯視窗。使用者按下推送通知之後，就會出現這個快顯視窗。文字檢視可讓您呈現更多的內容給使用者。這也是呈現動作呼叫的機會，例如跳至您的應用程式頁面、重新導向至市集、開啟網頁、傳送電子郵件、開始地理當地語系化的搜尋等等...

### 範例：文字檢視
-    在 [Reach] 區段中建立推送通知活動，並為您的活動指定名稱。
 
![TextView2][22]

-    撰寫將出現在通知的訊息。
-    將 [公告內容類型] 選取為 [文字]
 
![TextView3][23]

> 注意：當您推送文字檢視時，它一律是先有通知。

- 定義文字 (選取文字公告內容之後，子區段會出現，可讓您定義您想要顯示的文字)。
 
![TextView4][24]

-    撰寫將出現在訊息頂端的標題。
-    撰寫文字檢視的主要內容。
-    撰寫將出現在動作按鈕的內容 (動作按鈕可讓應用程式做出特定的動作，例如開啟應用程式頁面、重新導向至應用程式市集，或您可以提供的任何類型來源)。
-    撰寫將出現在結束按鈕的內容 (藉由按一下結束按鈕，文字檢視將會消失)。
 
-    建立推送通知活動，它會顯示在活動清單。
 
![TextView5][25]

-    啟動推送通知活動，將文字檢視傳送給使用者。
 
![TextView6][26]

-    結果
 
![TextView7][27]

-    使用者會收到通知並按一下它。
-    文字檢視顯示為快顯視窗，讓使用者能夠與它互動。

## 使用網頁檢視增強推送通知
### 什麼是網頁檢視？
![WebView1][28]

網頁檢視是具有網頁內容的快顯視窗。使用者按下推送通知時，就會出現這個快顯視窗。網頁檢視可讓您與使用者有更多互動。這也是呈現動作呼叫的機會，例如重新導向至應用程式市集、開啟網頁、傳送電子郵件、開始地理當地語系化的搜尋等等...

### 範例：網頁檢視
-    在 [Reach] 區段中建立推送活動，並為您的活動指定名稱。
 
![WebView2][29]

-    撰寫將出現在通知的訊息。
-    將 [公告內容類型] 選取為 [網頁]
 
![WebView3][30]

### 關於公告類型：
- 僅通知：這是簡單的標準通知。表示如果使用者按一下通知，不會出現任何其他檢視，但是會發生與它關聯的動作。
- 文字通知：這是一種吸引使用者查看文字檢視的通知。
- Web 通知：這是一種吸引使用者查看 Web 檢視的通知。選取「網頁公告」內容。

> 注意：當您推送網頁檢視時，其一律會先有通知。

- 定義網頁內容 (選取網頁公告內容之後，子區段會出現，可讓您定義您想要顯示的網頁檢視內容)。

 
![WebView4][31]

-    撰寫將出現在訊息頂端的標題 (選擇性)。
-    在這裡撰寫您的 HTML 程式碼。
-    按一下 [原始碼編輯模式] 按鈕以切換版本，並查看其外觀。
-    撰寫將出現在動作按鈕的內容 (動作按鈕可讓應用程式做出特定的動作，例如開啟應用程式頁面、重新導向至市集，或您可以提供的任何類型來源)。
-    撰寫將出現在結束按鈕的內容 (藉由按一下結束按鈕，網頁檢視將會消失)。
 
-    結果
 
![WebView5][32]

-    使用者會收到通知，並按一下它。
-    文字檢視會顯示為快顯視窗，讓使用者能夠與它互動。

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 

<!---HONumber=July15_HO2-->