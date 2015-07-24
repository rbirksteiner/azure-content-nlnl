<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 分析" 
   description="了解如何使用 Azure Mobile Engagement 分析應用程式的歷史資料" 
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

# 如何分析應用程式的歷史資料
UI 的 [分析] 區段根據每 24 小時更新一次的歷史資料，提供您應用程式的相關彙總資訊。這些資訊會顯示在由折線圖/橫條圖/圓餅圖、方格和地圖所組成的各種儀表板上。您也可以將資料下載為 .csv 檔案。這些相同資訊大部分可從 UI 的 [監視] 區段即時取得，也可以從「分析 API」存取。＜概念＞中的＜詞彙＞具有＜分析和監視＞中術語和縮寫的定義，如下所示：「作用中使用者」、「新增使用者」、「保留使用者」、「工作階段」、「使用者路徑圖表」、「使用者地圖」、「追蹤 URL」、「趨勢」、「活動」、「事件」、「工作」、「錯誤」、「額外資訊」、「損毀」和「應用程式資訊」。

### 另請參閱
-  [概念 - 詞彙][Link 6]、[疑難排解指南 - 分析][Link 21]

## 標準和自訂分析
您只要將應用程式與 SDK 整合，Azure Mobile Engagement 就可針對您的應用程式提供一組基本、標準的可圖形化分析資訊。Azure Mobile Engagement 也能讓您收集有關使用者行為的其他自訂分析資訊。您可以從 [設定] 建立一個自訂「應用程式資訊標記」的標記計畫，Azure Mobile Engagement 就可以為您收集這類額外資料。

### 另請參閱
-  [UI 文件 - 設定][Link 20]
 
## 分析標頭
- 項目名稱：為要被計算的項目加上標籤
- 顯示說明：顯示有關此區段的內容資訊
- 版本：可讓您顯示每個應用程式版本的不同分析資訊，或是顯示所有版本的資訊。(注意：在 UI 中篩選您的分析資料將會顯示此類型的所有範例，無論應用程式版本為何。例如，依名稱篩選「當機」，會從應用程式第 1 版和第 2 版顯示。)
- 期間：過去 7 天、過去 30 天、所有時間、自訂
- 頻率：每小時、每天、每週、每個月
- 檢視：折線圖、方格、傳送至儀表板、下載 CSV 檔案
 
![Analytics1][10]

## 分析資料
- 儀表板：顯示有關新使用者和作用中使用者及其趨勢的一般資訊。
- 使用者：會依據其裝置識別碼來識別使用者：每個裝置的識別碼都是唯一的 (一位新使用者實際上是一個新裝置)。如果一位使用者在某個指定時間間隔內執行了第一個工作階段，就會在此時間間隔期間被視為新使用者。如果一位使用者在過去 7 天內至少執行了一個工作階段，則會被視為保留使用者。「作用中使用者」是在指定期間內至少建立了一個工作階段的使用者。您可以依據每個月、每週、每天或每小時的期間加以排序。所有圖表的外觀都很類似，但可讓您依據不同的功能 (例如您的應用程式版本) 進行篩選，然後依據一段時間排序。整合 SDK 時所蒐集到的標準資訊包括：作用中使用者、新使用者、工作階段數目、每個工作階段長度、國家 (地區) 的技術資訊、地區設定、位置、語言、電信業者、裝置、韌體、網路 (WIFI)、客戶使用的應用程式和 SDK 版本。這項資訊可以從 [監視] 區段即時檢視。 
- 依來源追蹤顯示因特定促銷活動而下載應用程式的新使用者數目。使用者是依據其裝置識別碼來識別：每個裝置的識別碼都是唯一的 (一位新使用者實際上是一個新裝置)。如果一位使用者在某個指定時間間隔內執行了第一個工作階段，就會在此時間間隔期間被視為新使用者。
- 依市集追蹤會顯示從特定市集下載應用程式的新使用者數目。使用者是依據其裝置識別碼來識別：每個裝置的識別碼都是唯一的 (一位新使用者實際上是一個新裝置)。如果一位使用者在某個指定時間間隔內執行了第一個工作階段，就會在此時間間隔期間被視為新使用者。

> 注意：期間是根據使用者裝置設定的日期而定，所以如果使用者的手機日期設定不正確，使用者可能會出現在錯誤的期間。

- 保留：如果一位使用者在某個指定時間間隔內執行了第一個工作階段，就會在此時間間隔期間被視為保留使用者。您可以將保留使用者 (和新使用者) 的時間間隔計算變更為時數、天數、週數或月數。使用者保留分析會以同群使用者為基礎。「同群使用者」是在指定期間內偵測到的所有新使用者集合 (亦即在此期間執行他們第一個工作階段的使用者集合)。我們使用 1 天、2 天、4 天、7 天或 1 個月的同群使用者。指定每隔 1 天、2 天，4 天、7 天或 1 個月的同群使用者，Azure Mobile Engagement 會計算屬於該同群使用者且仍然作用中的所有使用者集合 (亦即，在期間內至少執行一個工作階段的使用者集合)。此類使用者集合稱為同群使用者版本(Azure Mobile Engagement 會向您顯示有多少使用者仍在使用您的應用程式，但是只有特定平台市集，例如 GooglePlay、iTunes、Windows 市集等，才能告訴您有多少使用者解除安裝您的應用程式)。 
- 工作階段：由一位使用者使用一個應用程式。工作階段是從使用者執行的活動序列所產生 (一個活動通常使用一個應用程式畫面，但這會根據 SDK 整合至應用程式的方式而有所不同)。使用者一次只能執行一個活動：當使用者開始他的第一個活動時，工作階段就開始，當他完成他的最後一個活動時，工作階段就停止。如果使用者停留超過幾秒鐘的時間而沒有執行任何活動，他的活動序列會分成兩個不同的工作階段。
- 活動：應用程式中的每個畫面名稱，以及使用者在每個畫面所花費的時間。活動是一種自訂的分析選項，會對應到您為自己的應用程式所設定的「應用程式資訊」標記：
- 使用者路徑：顯示使用者如何瀏覽您的應用程式活動 (畫面)。您可以移動滑桿調整詳細資料的層級。藍色的節點代表您的應用程式活動。節點大小和使用者花費在節點的時間呈正比。白色的節點代表工作階段開始和停止。紅色的節點代表當機。連結代表在應用程式活動之間 (或活動和當機之間) 轉換。按一下節點或連結可顯示工具提示，其中包含資料的詳細資訊：在特定畫面中所花費的時間、轉換計數，以及從來源活動轉換為目的地活動的百分比(A ---60% ---> B 表示在活動 A 的使用者移至活動 B 花費 60% 的時間)。 如果想要更仔細研究，可以重新安排圖表；每次您進行變更時，都會儲存圖表的位置。您可以顯示或隱藏當機，來加強圖表。
- 事件：使用者在應用程式中所採取的特定動作。事件的分佈會以每個工作階段每位使用者的事件計數顯示。一個事件表示一個立即動作，例如按一下按鈕或接收通知(事件的意義取決於 SDK 與應用程式整合的方式)。 事件可能在工作階段或作業期間發生，也可以獨立存在。
- 工作：與事件類似，只不過工作的重點在於動作的長度。例如，工作可以告訴您載入內容或呼叫 Web 服務要花費多長時間的相關技術資訊。工作也可以顯示使用者填寫表單、建立帳戶或購買東西時所花費的時間。工作代表某項工作的持續時間，例如，下載工作的持續時間或橫幅顯示在畫面上的時間(工作的意義取決於 SDK 與應用程式整合的方式)。 工作通常與工作階段範圍之外所執行的背景工作相關 (亦即，不含任何使用者活動)。
- 技術：您可以追蹤應用程式中有關使用者裝置的技術資訊，例如地區設定、電信業者、網路、裝置、韌體、使用者裝置的螢幕大小，以及您的應用程式版本和應用程式中使用的 SDK 版本。
- 錯誤：位於應用程式內部，且不會造成應用程式當機的技術錯誤相關資訊。錯誤表示立即的問題，例如網路錯誤或不正確的操作(事件的意義取決於 SDK 與應用程式整合的方式)。 錯誤可能在工作階段或工作期間發生，也可以獨立存在。
- 當機：造成應用程式當機的錯誤相關資訊。當機是應用程式停止執行其預期的功能，且必須停止的非預期狀況。當機通常是應用程式中的錯誤 (bug) 所造成。
 
![Analytics2][11]

## 存取保留概觀
![Analytics3][12]

保留概觀會在中間分成幾張卡片，每一張顯示特定保留期間的概觀。範例顯示的是為期 2 天的保留期間。其他卡片則顯示 4 天和 7 天的保留期間。

## 了解保留概觀卡片
![Analytics4][13]

### 每張卡片由 3 個主要部分組成：
1. 1：同群使用者和考慮的期間
2. 2-4：目前期間的保留
3. 5：歷程記錄的走勢圖

### 以下是每個項目的詳細資訊：
1.    同群使用者和期間：此標題提供同群使用者的類型。此處的「2 天期間」表示我們要查看這 2 天的使用者行為、到達超過 2 天期間的使用者數目，以及使用者在接下來的 2 天是否連線。上述範例考慮 11 月 21 日和 22 日之間的使用者活動。
2.    此處提供 11 月 19 日和 20 日到達的使用者到 11 月 21 日和 22 日的保留率。我們在 21 日和 22 日之間有 1 位作用中使用者，在 19 日和 20 日之間有 3 位新使用者。
3.    此視覺化指標提供的資訊與上面的圖表相同(1/3 的圓形來自 33% 的數字)。 色彩可提供其他資訊：綠色表示此數字與之前的計算值相比是增加的。黃色表示穩定，紅色表示減少。
4.    這表示用來計算的值。
5.    這是保留值的歷程記錄走勢圖。它可讓您查看過去的值，充分了解這些數值演進的方式。

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