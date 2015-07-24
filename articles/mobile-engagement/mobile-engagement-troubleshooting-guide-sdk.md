<properties 
   pageTitle="Azure Mobile Engagement 疑難排解指南 - SDK" 
   description="Azure Mobile Engagement 中 SDK 整合的疑難排解" 
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
   ms.date="06/18/2015"
   ms.author="piyushjo"/>

# SDK 整合問題的疑難排解指南

以下是您可能會遇到，有關 Azure Mobile Engagement 如何整合應用程式的問題。

## 因為您應用程式的另一個區域中發生失敗而發現的 SDK 問題

### 問題
- UI 資料收集失敗 (在分析、監視、分割或儀表板中)。
- 推送失敗 (推送在應用程式內或應用程式外無法運作，或在兩個情況下都無法運作)。
- 進階功能失敗 (追蹤、地理位置或平台特定的推送無法運作)。
- API 失敗 (API 經常以無訊息模式失敗，沒有錯誤訊息)。
- 服務失敗 (所有 Azure Mobile Engagement 都無法在您的應用程式上運作)。

### 原因

- 大部分需要解決的 Azure Mobile Engagement SDK 問題，都會因為您應用程式中發生失敗而發現 (例如 UI 資料收集失敗、推送失敗、進階功能失敗、API 失敗、應用程式當機或明顯的服務中斷)。  
- 如果 Azure Mobile Engagement 的某項特定功能從未在您的應用程式中運作過，表示您必須完成整合。 
- 如果 Azure Mobile Engagement 的某項特定功能曾運作並停止，表示您可能需要升級到 Azure Mobile Engagement SDK 的最新版本。請記住，Azure Mobile Engagement 支援的每個平台 (Android、iOS、Windows 及 Windows Phone) 皆有不同的 Azure Mobile Engagement SDK 適用版本。

#### SDK 整合

- Azure Mobile Engagement 未正確整合在 SDK 中 (分析)。
- Reach 未正確整合在 SDK 中 (應用程式內推送和應用程式外推送)。
- 憑證已過期或 PROD 與 DEV 不正確 (僅限 iOS)。
- GCM 或 ADM 未正確整合在 SDK 中 (僅限 Android - 服務特定推送)。
- 追蹤未正確整合在 SDK 中 (安裝存放區追蹤)。
- 延遲位置或 GPS 位置未正確整合在 SDK 中 (依地理位置設定目標)。


**另請參閱：**

- [SDK 文件 - 整合指南][Link 5] 
- [疑難排解指南 - 推送][Link 23]

#### SDK 升級

- 必須升級 SDK 來解決較舊版本 SDK 的問題 (經常與裝置作業系統的較新版本有關)。
- 解除安裝您裝置上所有的舊版應用程式，並重新安裝最新版本的應用程式，然後從 Azure Mobile Engagement UI 重新註冊您的裝置識別碼，以確認您的裝置是使用最新版本的應用程式。

**另請參閱：**

- [SDK 文件 - 版本資訊](http://go.microsoft.com/fwlink/?LinkId= 525554) 
- [SDK 文件 - 升級指南](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### SDK 其他問題

- 應用程式資訊清單 "AndroidManifest.xml" 中的錯誤可能會導致 Azure Mobile Engagement 無法運作 (僅限 Android)。
- SDK 整合與 API 使用方式的一個常見問題是將 SDK 金鑰和 API 金鑰混淆。

**另請參閱：**

- [概念 - 詞彙][Link 6]

## 進階編碼問題

### 問題
-  與 Azure Mobile Engagement 沒有直接相關的平台特定程式碼可能會造成在 iOS、Android 及 Windows Phone 上發生問題。

### 原因

- 許多 Azure Mobile Engagement 的進階編碼問題，都是因為沒有正確撰寫，且與 Azure Mobile Engagement 沒有直接相關的平台特定程式碼而產生。除了Azure Mobile Engagement 文件 (Android、iOS、Web、Windows 及 Windows Phone) 之外，您也必須參考您正在開發之平台的特定文件。
- 未正確設定「類別」會導致無法從通知連結到應用程式內或應用程式外的另一個位置 (僅限 Android )。 
- 在您的 iOS 程式碼中未將 "UIKit.framework" 設定為 "optional"，會在較舊的 iOS 裝置上顯示「錯誤：找不到符號」及/或當機 (僅限 iOS)。
- 過期的憑證或未正確使用憑證的 DEV 或 Prod 版本，會造成推送問題 (僅限 iOS)。
- 平台中有一些固有限制是 Azure Mobile Engagement 無法控制的 (例如系統中心針對 Android 和 iOS 中的應用程式外推送所採用的運作方式)。
- Azure Mobile Engagement 發佈了 Azure Mobile Engagement 針對 iOS 與 Android 所使用之內部套件的完整清單以供參考。請記住，Azure Mobile Engagement 的部分功能為平台特定功能 (Android、iOS、Web、Windows 及 Windows Phone)。

### 另請參閱

 - [疑難排解指南 - 推送][Link 23] 
 - [SDK 文件 - 版本資訊][Link 5]
 - [SDK 文件 - 升級指南][Link 5]

## 應用程式當機

### 問題
- 您的應用程式在使用者的裝置上當機。

### 原因

- 您可以在「分析 UI」或「分析 API」中檢視當機資訊。
- 您可以尋找測試裝置的裝置識別碼，並採取造成使用者使用應用程式時當機的相同動作，以協助識別您應用程式的當機原因。
- 升級為最新版本的 SDK 有時候可以解決造成應用程式當機的 Azure Mobile Engagement SDK 已知問題。因此在調查當機原因時，請務必查看平台的版本資訊。

### 另請參閱

- [SDK 文件 - 版本資訊][Link 5]
- [SDK 文件 - 升級指南][Link 5]

## 應用程式商店上傳失敗

### 問題
- 將您最新版本的應用程式上傳到 Apple、Google 或 Windows 應用程式市集的相關錯誤。

### 原因

- 應用程式商店有時候會封鎖已啟用特定功能的應用程式 (例如 Apple Store 會防止商店中的應用程式使用 IDFV，Google Play 商店則是會防止應用程式之間共享應用程式資訊)。 
- 如果您無法將應用程式上傳至商店，請務必查閱平台與 SDK 目前版本的相關版本資訊。

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
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
 

<!---HONumber=July15_HO1-->