<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 觸達活動" 
   description="了解如何使用 Azure Mobile Engagement 建立和管理推播通知活動。" 
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


# 如何建立和管理推播通知活動。
您可以使用 UI 的 [觸達] 區段，提供傳送推播通知所需的所有資訊，建立具有複雜公式的新推播活動。觸達活動的選項根據四種活動類型而有所不同：通知、投票、資料推送和磚 (僅 Windows Phone)。

### 選項適用範圍：
- 語言：全部 (通知、投票、資料推送、磚)
- 活動：全部 (通知、投票、資料推送、磚)
- 通知：通知、投票
- 內容：每種活動類型都不同
- 對象：全部 (通知、投票、資料推送、磚)
- 時間範圍：通知、投票、磚
- 測試：全部 (通知、投票、資料推送、磚)
 
![Reach-Campaign1][20]

## 語言
您可以使用 [語言] 下拉式功能表，將不同版本的推播傳送至設定使用不同語言的裝置。無論裝置設定使用的語言為何，所有裝置預設都會收到相同的推播。將裝置設定使用不同語言的使用者會收到預設語言版本的推播。大部分的推播活動選項都可讓您針對每個額外選取的其他語言，來指定不同的內容。
 
![Reach-Campaign2][21]

### 語言差異適用範圍：
- 語言：除了預設語言之外，還可以選取其他指定的的語言。
- 活動：所有語言都相同
- 通知：不只預設語言，可針對個別語言來指定
- 內容：不只預設語言，可針對個別語言來指定
- 對象：可根據個別的語言準則來篩選
- 時間範圍：所有語言都相同
- 測試：一次可以傳送給一種語言
 
### 支援的語言：
- 阿拉伯文 (ar) 
- 保加利亞文 (bg) 
- 卡達隆尼亞文 (ca) 
- 中文 (zh) 
- 克羅埃西亞文 (hr) 
- 捷克文 (cs) 
- 丹麥文 (da) 
- 荷蘭文 (nl) 
- 英文 (en) 
- 芬蘭文 (fi) 
- 法文 (fr) 
- 德文 (de) 
- 希臘文 (el) 
- 希伯來文 (he) 
- 印度文 (hi) 
- 匈牙利文 (hu) 
- 印尼文 (id) 
- 義大利文 (it) 
- 日文 (ja) 
- 韓文 (ko) 
- 拉脫維亞文 (lv) 
- 立陶宛文 (lt) 
- 馬來文 (macrolanguage) (ms) 
- 挪威文 (巴克摩) (nb) 
- 波蘭文 (pl) 
- 葡萄牙文 (pt) 
- 羅馬尼亞文 (ro) 
- 俄文 (ru) 
- 塞爾維亞文 (sr) 
- 斯洛伐克文 (sk) 
- 斯洛維尼亞文 (sl) 
- 西班牙文 (es) 
- 瑞典文 (sv) 
- 塔加拉族文 (tl) 
- 泰文 (th) 
- 土耳其文 (tr) 
- 烏克蘭文 (uk) 
- 越南文 (vi) 
 
## 活動
您可以使用 [活動] 區段設定活動的名稱和類別，如果您計劃忽略推播活動的對象區段，並改為透過觸達 API (以及使用低階推播 API 的部份元素) 傳送此活動，也可以這麼做。類別可以搭配自訂的通知範本一起使用，以便根據預先定義的設定控制應用程式內通知。您可以透過觸達 API 取得一份您現有的「類別」清單。

> 警告：如果您在觸達活動的 [活動] 區段中使用 [略過對象，推播將透過 API 傳送給使用者] 選項，活動將「不」會自動傳送，您必須以手動方式透過觸達 API 傳送活動。
 
![Reach-Campaign3][22]
 
### 選項適用範圍：
- 名稱：全部
- 類別：通知、投票
- 略過對象，推播將透過 API 傳送給使用者：全部
 
## 通知
您可以使用 [通知] 區段設定推播的基本設定，包括：推送的標題、訊息、應用程式內影像，或者它是否可關閉。許多通知設定都和您使用的裝置平台有關。您可以選取要在「應用程式內」或「應用程式外」或兩者傳送您的推播。(請留意，對於「應用程式外」的推播，使用者可以在裝置的作業系統層級「選擇加入」或「選擇退出」，Azure Mobile Engagement 無法覆寫這項設定。另請留意，觸達 API 可處理「應用程式內」和「應用程式外」推播。推播 API 也可用來處理「應用程式外」推播)。 推播可使用圖片或 HTML 內容加以自訂，包括連結到您應用程式之外，或連結到您應用程式內其他位置的深層連結 (需要 Android SDK 2.1.0 或更新版本的類別)。您可以變更圖示或 iOS 徽章，並傳送文字或 Web 內容 (有 html 內容和 URL 連結的快顯，可連結到應用程式內部或外部的其他位置)。您也可以使用推播讓 Android 裝置響鈴或震動。(請留意，您需要在 Android 資訊清單檔案中有正確的 SDK 權限，才能讓裝置響鈴或震動)。 Android 的「大型圖片」尺寸目前沒有任何業界標準，因為每個裝置使用不同的螢幕尺寸，不過 400x100 的圖片幾乎可在所有的螢幕尺寸上運作。

### 傳遞類型：
-    僅限應用程式外：當使用者不使用應用程式時，就會傳遞通知。
- 僅限應用程式外的通知需要來自 Apple 或 Google 的憑證 (APN 或 GCM 憑證)。
- 僅限應用程式內：通知只會在應用程式執行時顯示。
- 通知使用 Capptain 傳遞系統觸達使用者。您可以完全自訂推播的版面配置/顯示方式。
- 任何時間：無論應用程式是否執行，此選項可確保您傳送通知。

 
![Reach-Campaign4][23]

### 選項適用範圍：
- 通知：通知、投票
 
## 內容
您可以使用 [內容] 區段修改通知、投票、資料推送和磚 (僅限 Windows Phone) 的內容。推播活動的 [內容] 設定與活動類型有關。

### 另請參閱
- [UI 文件 - 觸達 - 推送內容][Link 29]
 
![Reach-Campaign5][24]

## 觀眾
您可以使用 [對象] 區段定義標準的項目清單，或者根據自訂的準則，對活動設定限制。限制對象的標準選項設定，可讓您只發送給新的或舊的使用者，或者只給原生的推播使用者。您也可以設定配額，限制接收推播的使用者數目。您可以手動編輯運算式，如何篩選您的活動以包含一或多個選取使用者的準則。您可以手動輸入對象運算式。這類運算式必須明確定義準則之間的關係。描述準則的識別項必須以大寫字母開頭，而且不能包含空格。準則之間的關係可以使用 and、or、not 等運算子，以及「(」、「)」 來描述。範例："Criterion1 or (Criterion1 and not Criterion2)"。

> 注意：如果活動的對象很多，尤其是當您嘗試同時啟動多個活動時，伺服器端的目標掃描可能會變慢。

- 可能的話，一次只啟動一個活動。
- 一次最多只啟動四個活動。
- 只推送給作用中使用者 (核取 [只吸引可使用原生推送觸達的使用者] 和 [只吸引作用中使用者] 方塊)，就只會掃描仍然安裝應用程式且使用它的使用者。一旦定義您的對象之後，就可以使用模擬按鈕找出會收到此推播的使用者數目。這會計算此對象可能選出的已知使用者數目 (根據使用者的隨機取樣估計)。請注意，已解除安裝應用程式的使用者也屬於這類對象，但無法觸達。

### 另請參閱
- [UI 文件 - 觸達 - 新增推播準則][Link 28]

![Reach-Campaign6][25]

### 編輯運算式
![Reach-Campaign7][26]
 
### 限制對象選項適用範圍：
- 只吸引部份使用者：全部 (通知、投票、資料推送、磚)
- 只吸引舊使用者：全部 (通知、投票、資料推送、磚)
- 只吸引新使用者：全部 (通知、投票、資料推送、磚)
- 只吸引閒置使用者：通知、投票、磚
- 只吸引作用中使用者：全部 (通知、投票、資料推送、磚)
- 只吸引可使用原生推送觸達的使用者：
 
## 時間範圍
您可以使用 [時間範圍] 區段設定推播傳送的時間，或者可以將時間範圍留空以便立即啟動活動。請留意，使用使用者的時區來啟動活動，對於亞洲的使用者可能會比您預期的時間還早一天，因此請在世界上所有的時區都符合活動的時間範圍時，才一次傳送小型批次的推播。使用使用者的時區也可能會造成活動延遲，因為必須先從使用者的電話取得時間才能啟動推送。

> 注意：沒有結束日期的活動可能會在本機快取推播，甚至在您手動完成活動之後，仍然繼續顯示推播。若要避免發生這個狀況，請為活動設定確切的結束時間。

### 另請參閱
- [觸達 - 做法 – 排程][Link 3] 
 
![Reach-Campaign8][27]

### 設定適用範圍：
- 時間範圍：通知、投票、磚
 
## 測試
儲存活動之前，您可以使用 [測試] 區段將此推播傳送到您自己的測試裝置。如果您為此活動設定任何自訂語言，可以使用每個語言測試推播。您可以從 [我的帳戶] 設定測試裝置。
> 注意：當您使用按鈕來「測試」推播時，不會記錄任何伺服器端的資料，只會記錄實際推播活動的資料。

### 另請參閱
- [UI 文件 - 我的帳戶][Link 14]
 
![Reach-Campaign9][28]

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