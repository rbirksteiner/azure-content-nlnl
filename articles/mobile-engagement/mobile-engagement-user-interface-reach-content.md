<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 觸達內容" 
   description="了解如何在 Azure Mobile Engagement 管理不同類型推播通知活動的指定內容" 
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

# 如何管理不同類型推播通知活動的指定內容
 
您可以使用新觸達活動的 [內容] 區段修改通知、投票、資料推送和磚 (僅限 Windows Phone) 的內容。推播活動的內容設定與活動類型有關。
 
### 內容類型：
- 通知
- 投票
- 資料推送
- 磚 (僅限 Windows Phone)
 
## 通知的內容
 ![Reach-Content1][30]

### 選擇通知的類型：
-    僅通知：這是簡單的標準通知。表示如果使用者按一下通知，不會出現任何其他檢視，但是會發生與它關聯的動作。
-    文字通知：這是一種吸引使用者查看文字檢視的通知。
-    Web 通知：這是一種吸引使用者查看 Web 檢視的通知。

### 另請參閱
- [觸達 - 作法 - 通知][Link 3] 

### 關於 Web 檢視通知：
您在此處提供的每個 "{deviceid}" 字樣 (在 HTML 程式碼或 JavaScript 程式碼中) 將會自動取代為顯示通知的裝置識別碼。這個簡單的方法可以擷取您後端系統裝載的外部 Web 服務中的 Azure Mobile Engagement 裝置識別碼。如果您想要建立全螢幕的 Web 檢視 (不含我們提供的預設 [動作] 和 [離開] 按鈕)，您可以從 Web 檢視通知的 JavaScript 程式碼中使用下列函式：

-    執行通知動作：ReachContent.actionContent()
-    離開通知：ReachContent.exitContent()
 
### 選擇您的動作：

### 關於動作 URL：
可由目標裝置的作業系統解譯的任何 URL 都可以用來做為動作 URL。應用程式可能支援的任何專用 URL (例如讓使用者跳至特定畫面) 也可用來做為動作 URL。每個 {deviceid} 字樣的項目都會自動取代為執行動作的裝置識別碼。透過您後端系統裝載的外部 Web 服務，這可用來輕鬆擷取 Azure Mobile Engagement 裝置識別碼。

- **Android + iOS 動作**
    - 開啟網頁
    - http://[web-site-domain\] 
    - 範例：http://www.azure.com
    - 傳送電子郵件
    - mailto:[收件者]?subject=[主旨]&body=[訊息] 
    - 範例：mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - 傳送簡訊
    - sms:[電話號碼] 
    - 範例：sms:2125551212
    - 撥電話號碼
    - tel:[電話號碼] 
    - 範例：tel:2125551212
- **僅限 Android 的動作**
    - 下載 Play 商店上的應用程式
    - market://details?id=[app 套件] 
    - 範例：market://details?id=com.microsoft.office.word
    - 開始地理當地化的搜尋
    - geo:0,0?q=[搜尋查詢] 
    - 範例：geo:0,0?q=starbucks,paris
- **僅限 iOS 的動作**
    - 下載 App Store 上的應用程式
    - http://itunes.apple.com/[country]/app/[app name]/id[app id]?mt=8 
    - 範例：http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Windows 動作
    - 開啟網頁
    - http://[web-site-domain\] 
    - 範例：http://www.azure.com
    - 傳送電子郵件
    - mailto:[收件者]?subject=[主旨]&body=[訊息] 
    - 範例：mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - 傳送簡訊 (需要 Skype 市集應用程式)
    - sms:[電話號碼] 
    - 範例：sms:2125551212
    - 撥電話號碼 (需要 Skype 市集應用程式)
    - tel:[電話號碼] 
    - 範例：tel:2125551212
    - 下載 Play 商店上的應用程式
    - ms-windows-store:PDP?PFN=[應用程式套件識別碼] 
    - 範例：ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - 開始 bingmaps 搜尋
    - bingmaps:?q=[搜尋查詢] 
    - 範例：bingmaps:?q=starbucks,paris
    - 使用自訂配置
    - [自訂配置]://[自訂配置參數] 
    - 範例：myCustomProtocol://myCustomParams
    - 使用套件資料 (需要副檔名讀取的市集應用程式)
    - [資料夾][資料].[副檔名] 
    - 範例：myfolderdata.txt
 
### 建置追蹤 URL：
-    請參閱 <UI Documentation> 的＜設定＞一節，了解建置可讓使用者下載其他應用程式之追蹤 URL 的相關指示。
 
### 定義您的通知文字：
填寫通知的標題、內容和按鈕文字。您可以根據使用者如何回應此活動的觸達意見反應，找出未來的活動對象。選取目標對象可以根據此活動是否已推送、回覆、採取動作或離開的意見反應。

### 另請參閱
- [UI 文件 - 觸達 - 新增推播準則][Link 28]

## 投票的內容
![Reach-Content2][31] 填寫通知的標題、描述和按鈕文字。然後，新增問題和問題的答案選項。您可以根據使用者如何回應此活動的觸達意見反應，找出未來的活動對象。選取目標對象時可以根據此活動是否已推送、回覆、採取動作或離開。選取目標對象時也可以根據投票答案的意見反應，將其中的問題和答案選項做為準則。

### 另請參閱
- [UI 文件 - 觸達 - 新增推播準則][Link 28]
 
## 資料推送的內容
![Reach-Content3][32]

### 選擇您的資料類型：
- 文字
- 二進位資料
- Base64 資料

### 定義資料的內容
- 如果您選取推送文字資料，請複製文字並貼到 [內容] 方塊。
- 如果您選取推送二進位或 base64 資料，請使用 [上傳您的檔案] 按鈕上傳您的檔案。
- 您可以根據使用者如何回應此活動的觸達意見反應，找出未來的活動對象。選取目標對象時可以根據此活動是否已推送、回覆、採取動作或離開。

### 另請參閱
- [UI 文件 - 觸達 - 新增推播準則][Link 28]

## 磚的內容 (僅限 Windows Phone)
![Reach-Content4][33]

### 定義磚的內容
磚承載是在 Windows Phone 裝置上應用程式磚中顯示的文字。磚推送是適用於 Windows Phone 之原生推送的 Microsoft 推播通知服務 (MPNS) 版本。磚推送類型是唯一沒有回應的推播類型，因此無法依據磚推播活動的結果建置未來活動的對象。

### 另請參閱
- [API 文件 - 觸達 API - 原生推送][Link 4]

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