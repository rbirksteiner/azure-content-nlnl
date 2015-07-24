<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 觸達" 
   description="了解如何使用 Azure Mobile Engagement 透過推播通知觸及應用程式的使用者" 
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


# 如何透過推播通知觸及應用程式的使用者
UI 的 [觸達] 區段是推送活動管理工具，您可以在此建立/編輯/啟動/完成/監視推播通知活動，並取得推播通知活動的統計資料，您也可以透過「觸達 API」(和低階推送 API 的部份元素) 存取這些功能。請記住，無論您使用 API 或 UI，都必須先使用 SDK 針對每個平台將 Azure Mobile Engagement 和觸達整合到您的應用程式，才能使用觸達活動。

### 另請參閱
-  [API 文件 - 觸達 API][Link 4]、[API 文件 - 推送 API][Link 4]、[疑難排解指南 - 推送/觸達][Link 23]
-  [觸達 - 活動][Link 27]、[觸達 - 準則][Link 28]、[觸達 - 內容][Link 29]、[觸達 - 作法][Link 3]
 
## 推播通知的四種類型
1.    宣告：可讓您傳送廣告訊息給使用者，將他們重新導向至您應用程式內的另一個位置，或者將他們傳送至您應用程式外的網頁或市集。 
2.    輪詢：可讓您透過詢問使用者問題，從使用者收集資訊。
3.    資料推送：可讓您傳送二進位或 base64 資料檔案。資料推送中所包含的資訊會傳送至您的應用程式，以便調整您的使用者在應用程式中目前的體驗。您的應用程式必須能夠處理資料推送中的資料。
4.    磚 (僅限 Windows Phone)：可讓您使用 Microsoft 推播通知服務 (MPNS) 傳送包含 XML 資料的原生 Windows Push(從 SDK 版本 0.9.0 開始支援。磚的最後一個裝載不能超過 32 KB)。

### 另請參閱
-  [概念 - 詞彙][Link 6]

## 每個活動會顯示三種即時統計資料
1.    已推送：根據活動中指定的準則傳送的推送數目。 
2.    已回覆：從應用程式外部開啟通知或在應用程式中關閉通知來回應通知的使用者數目。 
3.    已採取動作：在通知中按下連結而被重新導向至應用程式新位置、市集或網頁瀏覽器的使用者數目。 

> 注意：您可以透過「觸達 API Stats」取得更詳細的活動統計資料。

### 另請參閱
-  [概念 - 詞彙][Link 6]、[API 文件 - 觸達 API - Stats][Link 4]


## 活動詳細資料
您可以停留在活動名稱上或按一下以開啟活動，來編輯、複製、刪除活動或啟動尚未啟動的活動。您可以停留在活動名稱上或按一下以開啟活動，來複製已經啟動的活動。不過，活動一旦啟動，您就無法變更。
 
![Reach1][18]

## 觸達意見反應
您可以從詳細資料切換至已啟動之開啟活動的統計檢視，並且將統計資料從簡單檢視切換到進階檢視，以檢視更詳細的資訊 (視您的權限而定)。您也可以使用來自先前活動的觸達意見反應資訊，做為新活動的目標設定準則。「觸達意見反應」統計資料也可以透過「觸達 API」的 "Stats" 收集。您也可以根據先前的活動，自訂推送活動的對象。


### 另請參閱 
-  [UI 文件 - 觸達 - 新增推送活動][27]、[API 文件 - 觸達 API - Stats][Link 4]

![Reach2][19]

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