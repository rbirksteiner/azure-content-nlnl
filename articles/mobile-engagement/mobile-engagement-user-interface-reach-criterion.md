<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 觸達準則" 
   description="了解如何透過 Azure Mobile Engagement 使用目標準則傳送推播活動到選取的使用者子集。" 
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


# 如何使用目標準則傳送推播活動到選取的使用者子集。

在 Azure Mobile Engagement 中使用 [新增準則] 按鈕透過特定準則來找出目標對象，是一個非常有力的概念，這樣可協助您傳送相關的推播通知給會回應的客戶，而不是濫發垃圾訊息給每個人。您可以根據標準的準則限制對象，並且模擬推送來判斷有多少人會收到通知。

**另請參閱：**

- [UI 文件 - 觸達 - 新的推播活動][Link 27]

## 對象準則可以包括：
- **技術：**您可以根據 [分析] 和 [監視] 區段中也可看見的相同技術資訊來選取目標。**另請參閱：** [UI 文件 - 分析][Link 15]、[UI 文件 - 監視器][Link 16] - **位置：**使用「即時位置報告」與地理柵欄的應用程式，可以使用地理位置當作準則來從 GPS 位置選取對象。「延遲區域位置報告」呼叫也用來根據手機位置選擇目標對象 (「即時位置報告」和「延遲區域位置報告」必須從 SDK 啟動)。**另請參閱：** [SDK 文件 - iOS - 整合][Link 5]、[SDK 文件 - Android - 整合][Link 5]
- **觸達意見反應：** 您可以根據他們上一個透過觸達意見反應的通知、投票和資料推送的觸達意見反應來選擇目標對象。這讓您能在經過二或三次的觸達活動之後，更精確地選擇目標對象。藉由設定「不」傳送活動給之前已收到特定活動的使用者，它也可以篩選出已經接收類似內容通知的使用者。您甚至可以排除包含特定有效活動的使用者收到新的推播。**另請參閱：** [UI 文件 - 觸達 - 推播內容][Link 29]
- **安裝追蹤：**您可以追蹤根據使用者安裝您應用程式之位置所建立的資訊。**另請參閱：** [UI 文件 - 設定][Link 20]
- **使用者個人檔案：**您可以根據標準使用者，以及您建立的自訂應用程式資訊來選擇目標。 這包含目前登入的使用者，以及已回答您要求他們在應用程式中設定之特定問題的使用者，而不只是根據他們如何回應之前的活動。所有為您應用程式定義的應用程式資訊都顯示在這個清單。
- 區隔：您也可以根據依特定使用者行為 (包含多個準則) 建立的區隔來選擇目標。所有為您應用程式定義的區隔都顯示在這個清單。**另請參閱：**[UI 文件 -區隔][Link 18]
- **應用程式資訊**：您可以從 [設定] 建立自訂應用程式資訊標記，來追蹤使用者行為。**另請參閱：** [UI 文件 - 設定][Link 20]

## 範例： 
如果您只想要推送通知給已執行應用程式內購買動作的部份使用者使用者。

1. 移至您的應用程式設定頁面，選取 [應用程式資訊] 功能表，然後選取 [新增應用程式資訊]
2. 註冊一個稱為 "inAppPurchase" 的新布林值應用程式資訊
3. 讓您的應用程式將此應用程式資訊在使用者成功執行應用程式內購買 (使用 sendAppInfo("inAppPurchase", ...) 函式) 時設定為 "true"
4. 如果您不想從您的應用程式執行這項操作，可以從您的後端使用裝置 API 執行
5. 然後，您只需要建立通知，其準則是將對象限制在 "inAppPurchase" 設為 "true" 的使用者。
 
> 注意：如果目標選取是根據準則而不是應用程式資訊標記，Azure Mobile Engagement 必須先從使用者的裝置收集資訊，才能傳送推播，如此一來可能會導致延遲。複雜的推送設定選項 (例如更新徽章) 可能也會延遲推送。在 Azure Mobile Engagement 中，從推播 API 使用「一次性」的活動，絕對是最快速的推送方法。對於觸達活動只使用應用程式資訊標記做為推送準則 (從觸達 API 或 UI) 是第二種最快的方法，因為應用程式資訊標記儲存在伺服器端。使用其他目標選取準則來推播活動是最具彈性、但也是最慢的推送方法，因為 Azure Mobile Engagement 必須先查詢裝置才能傳送活動。
 
![Reach-Criterion1][29]

## 準則選項適用範圍：
- **技術**     
- 韌體名稱：韌體名稱
- 韌體版本：韌體版本
- 裝置型號：裝置型號
- 裝置製造商：裝置製造商
- 應用程式版本：應用程式版本
- 電信業者名稱：電信業者名稱、未定義
- 電信業者國家/地區：電信業者國家/地區、未定義
- 網路類型：網路類型
- 地區設定：地區設定
- 螢幕大小：螢幕大小
- **位置**      
- 上一個已知區域：國家/地區、區域、位置
- 即時地理柵欄：POI 清單 (名稱、動作)、圓形 POI (名稱、緯度、經度、以公尺為單位的半徑)
- **觸達意見反應**     
- 通知意見反應：通知、意見反應
- 投票意見反應：投票、意見反應
- 投票答案意見反應：投票答案意見反應、問題、選項
- 資料推送意見反應：資料推送、意見反應
- **安裝追蹤**     
- 市集：市集、未定義
- 來源：來源
- **使用者個人檔案**     
- 性別：男性或女性、未定義
- 生日：運算子、日期、未定義
- 選擇加入：true 或 false、未定義
- **應用程式資訊**      
- 字串：字串、未定義
- 日期：運算子、日期、未定義
- 整數：運算子、數字、未定義
- 布林值：true 或 false、未定義
- **區隔**    
- 區隔的名稱 (從下拉式清單)、排除 (不屬於此區隔的目標使用者)。

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 

<!---HONumber=July15_HO2-->