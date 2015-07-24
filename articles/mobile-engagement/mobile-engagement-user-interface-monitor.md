<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 監視" 
   description="了解如何使用 Azure Mobile Engagement 監視應用程式的即時資料" 
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
   ms.date="02/17/2015"
   ms.author="piyushjo"/>

# 如何監視應用程式的即時資料
UI 的 [監視] 區段提供即時分析資訊，並可讓您設定在 UI 之 [分析] 區段中過去可用的大多數相同功能達到臨界值時發出警示。＜概念＞中的＜詞彙＞具有＜分析和監視＞中術語和縮寫的定義，如下所示：「作用中使用者」、「新增使用者」、「保留使用者」、「工作階段」、「使用者路徑圖表」、「使用者地圖」、「追蹤 URL」、「趨勢」、「活動」、「事件」、「工作」、「錯誤」、「額外資訊」、「損毀」和「應用程式資訊」。

### 另請參閱
-  [概念 - 詞彙][Link 6]、[疑難排解指南 - 分析][Link 21]

## 監視 - 工作階段、工作、事件、錯誤和損毀
您可以看到工作階段中和特定螢幕上目前有多少位使用者，或是目前有多少位使用者執行特定動作。您可以檢視除以「工作階段」、「工作」、「事件」、「錯誤」和「損毀」的使用者活動。您可以查看目前的資訊，並顯示最後一個小時、一天或一週的資訊。您可以查看每種類別中的所有資訊，或依特定「工作階段」、「工作」、「事件」、「錯誤」和「損毀」中進行排序。即時監視適用於在事件 (例如推播行銷活動) 期間用來查看傳送推播通知後是否有立即作用的報升。
 
![Monitor1][14]

## 監視 - 事件 - 詳細資料疑難排解
從測試裝置產生應用程式中的事件以及在 [監視 - 事件 - 詳細資料] 中尋找它是一種最簡單的方法，可尋找測試裝置的裝置識別碼，以及確認 Azure Mobile Engagement 分析、監視和區段整合可以從您的應用程式運作。測試裝置的裝置識別碼之後，即可在 [我的帳戶 - 裝置] 中將它加入測試裝置。如果您無法產生事件，請確定已使用 SDK 將 Azure Mobile Engagement 正確地整合到 Android/iOS/Web/Windows/Windows Phone 應用程式。

### 另請參閱
-  [SDK 文件][Link 5]

![Monitor2][15]

## 監視 - 損毀 - 詳細資料疑難排解
您可以從 [監視 - 損毀 - 詳細資料] 檢閱應用程式的損毀資訊，以協助判斷您應用程式的損毀原因。您也應該在 Android/iOS/Web/Windows/Windows Phone 之每個 SDK 版本的版本資訊中，查閱每個 SDK 版本的已知問題。

### 另請參閱
-  [SDK 文件 - 版本資訊][Link 5]

![Monitor3][16]

## 監視 - 警示
您也可以指定透過電子郵件或立即訊息自動傳送給您之警示的條件 (支援任何 XMPP 相容服務，例如 Google 的 GTalk 或 Apple 的 iChat)。 警示是根據一個預先定義的偵測臨界值，而此臨界值大於 (>) 或小於 (<) 每秒、分鐘或小時特定數目的工作階段、工作、事件、錯誤或損毀。警示可以監視某指定類型的所有活動，或只監視特定工作、事件或錯誤活動。您也可以指定最小偵測率，這是分隔相同警示之兩個通知的最小時間量，藉此確定觸發警示時，每 X 分鐘不會收到 1 個以上的通知。
 
![Monitor4][17]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

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