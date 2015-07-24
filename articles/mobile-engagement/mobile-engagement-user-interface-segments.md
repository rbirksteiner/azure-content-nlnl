<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 使用者分佈" 
   description="了解如何使用 Azure Mobile Engagement 建立和管理使用者的使用者分佈以分辨使用模式" 
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

# 如何建立和管理使用者的使用者分佈以分辨使用模式
UI 的 [使用者分佈] 區段可讓您根據不同的行為和分析，對您的使用者進行分佈研究，這些行為和分析可從應用程式取得，也可透過「使用者分佈 API」存取。使用者分佈會在建立之後的 24 小時先計算一次，然後根據最新的分析資訊，每隔 24 小時重新計算一次。計算使用者分佈之後，它會每天顯示「每天變化的歷程記錄」圖表。

### 另請參閱
- [API 文件 - 使用者分佈 API][Link 4]、[疑難排解指南 - 分析][Link 21]

## 建立使用者分佈
您可以根據 [分析] 區段中過去最多 60 天的特定期間及最多 10 個準則來建立使用者分佈。例如，您可以根據過去 10 天內在您的應用程式中檢視某些頁面或搜尋特定內容的人員，建立使用者分佈。您也可以在 [分析] 區段取得此資訊。因此，您可以使用這項資訊來建立使用者分佈，然後再針對這一類使用者設定推送通知，讓他們再回到應用程式。
 
> 注意：一旦計算使用者分佈之後，就無法進行編輯；只能加以複製或銷毀 (刪除)。使用者分佈可以在同一個應用程式 (具有相同的 AppID) 內複製，也可以複製到其他應用程式 (具有不同的 AppID)。
 
 ![segments1][35]

## 範例使用者分佈
 ![segments2][36]

使用者分佈可讓您對應用程式的使用者進行分佈研究。研究您的使用者分佈是很重要的行銷策略。Azure Mobile Engagement 可讓您取得歷史資料，並建立自訂的使用者分佈。這個功能強大的工具可讓您深入了解客戶的應用程式體驗。您可以輕鬆地分析您的使用者分佈，並利用這些使用者分佈做為推送目標。常見的使用案例是傳送推送通知，鼓勵使用者在市集中對應用程式評分。您可以建立一個使用者分佈，指定過去一個月每天使用您的應用程式且有絕佳使用者體驗的使用者，針對這些使用者而不是所有使用者傳送通知。若您傳送比較少、目標更明確的推送通知，您就會獲得更好的 ROI。
 
 ![segments3][37]

### 您可以根據主要 Azure Mobile Engagement 項目建立的使用者分佈：
- 事件：建立一週內發生兩次以上某個應用程式特定事件的使用者分佈。 
- 工作階段：建立上週使用 5 次以上應用程式的使用者分佈。
- 活動：建立上個月使用多於或少於 10 次某一頁面或內容的使用者分佈。
- 工作：建立一天完成兩次以上工作的使用者分佈。
- 當機：建立上週當機 10 次以上的所有使用者分佈。(您可以在推送此使用者分佈時包含道歉啟事或優待券！)
- 錯誤：建立過去 3 天發生 100 次以上錯誤的所有使用者分佈。
- 應用程式資訊：建立過去 25 天內發生自訂「應用程式資訊」的使用者分佈。
 
 ![segments4][38]

若要以最佳方式利用使用者分佈，您必須先在具有「應用程式資訊」標記之標記計畫的應用程式中完成 SDK 的自訂整合。然後，移至介面的首頁上，選取您想要的應用程式，然後按一下 [使用者分佈] 區段。

1. 選取 [使用者分佈] 區段。
2. 按一下 [新增使用者分佈] 按鈕，建立新的使用者分佈。

## 實際範例：根據「工作階段」資訊建立簡單的使用者分佈
建立上週至少使用 50 次您的應用程式的所有使用者分佈。從該處只尋找在應用程式中每個工作階段至少花費 30 秒的使用者。這會顯示在您的應用程式中獲得正面體驗的所有使用者。然後，建立的使用者分佈可用來推送通知給這些使用者，請他們在市集中對您的應用程式評分。
 
 ![segments5][39]

1. 您可以將使用者分佈命名，方便在 [使用者分佈] 清單中快速尋找。
2. 按一下 [建立] 按鈕。
 
 ![segments6][40]

選取 [工作階段]。
 
 ![segments7][41]

1. 選取 [上週] 的期間。
2. 按 [下一步]。
 
 ![segments8][42]

1. 在清單中選取相關的 [運算子]：=、≥、≤。
2. 輸入您想要的 [計數]。
3. 選取您想要的 [發生次數]。 
4. 按 [下一步]。此範例就設定為上週至少使用了 50 個工作階段的使用者。
 
 ![segments9][43]

如果要查看「工作階段」的分佈方式，您可以選擇每個工作階段的長度做為準則。

1. 從清單中選取 [運算子]。
2. 提供每個工作階段的 [長度]。
3. 按 [下一步]。此範例表示在根據發生區段計算而得的所有工作階段中，只選取在每個工作階段花費超過 30 秒的使用者。
 
 ![segments10][44]

將您的準則命名，以便在完整的漏斗圖中可以擷取準則，然後按一下 [完成]。
 
 ![segments11][45]

準則設定完成時，將會出現在使用者分佈漏斗圖中。因為使用者分佈是根據分析資料，所以使用者分佈也會每天計算一次。在此範例中，總共有 47.7% 的使用者符合準則。這些是有良好體驗的使用者，所以如果推送通知請他們在市集中對應用程式評分，他們應該會提供比較高的評分。

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