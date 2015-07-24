<properties 
   pageTitle="Azure Mobile Engagement 疑難排解指南 - API" 
   description="Azure Mobile Engagement 疑難排解指南 - API" 
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

# API 問題的疑難排解指南

以下是您可能遇到，有關系統管理員如何透過 API 與 Azure Mobile Engagement 互動的問題。

## 語法問題

### 問題
- 使用 API 的語法錯誤 (或非預期的行為)。

### 原因

- 語法問題：
    - 請務必檢查您正在使用之特定 API 的語法，以確認該選項可以使用。
    - API 使用方式的一個常見問題是將觸達 API 與推送 API 混淆 (大部分工作應該是使用觸達 API 來執行，而不是使用推送 API 來執行)。 
    - SDK 整合與 API 使用方式的另一個常見問題是將 SDK 金鑰和 API 金鑰混淆。
    - 連接到 API 的指令碼必須至少每 10 分鐘傳送一次資料，否則連線會逾時 (這在接聽資料的監視 API 指令碼中特別常見)。若要避免逾時，請讓您的指令碼每 10 分鐘傳送一個 XMPP ping，以保持伺服器工作階段運作。

### 另請參閱
 
- [API 文件][Link 4]
- [XMPP 通訊協定資訊](http://xmpp.org/extensions/xep-0199.html)
 
## 無法使用 API 執行 Azure Mobile Engagement UI 中可用的相同動作

### 問題
- 可從 Azure Mobile Engagement UI 運作的動作，無法從相關的 Azure Mobile Engagement API 運作。

### 原因

- 確認您可以從 Azure Mobile Engagement UI 執行相同的動作之後，即表示您已正確地將 Azure Mobile Engagement 的這項功能與 SDK 整合。

### 另請參閱
 
- [UI 文件][Link 1]
 
## 錯誤訊息

### 問題
- 使用 API 時在執行階段或記錄檔中顯示的錯誤碼。

### 原因

- 以下是供參考和初步疑難排解使用之一般 API 狀態碼號碼的複合清單：

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### 另請參閱

- [API 文件 - 適用於每個特定 API 的詳細錯誤][Link 4]
 
## 無訊息失敗

### 問題
- API 動作失敗，但執行階段或記錄檔中沒有顯示任何錯誤訊息。

### 原因

- 如果項目整合不正確，Azure Mobile Engagement UI 中的許多項目將會停用，但會從 API 以無訊息方式發生失敗，因此請記得從 UI 測試相同功能，以查看功能是否正常運作。
- Azure Mobile Engagement 以及許多您嘗試使用的 Azure Mobile Engagement 進階功能，都需要使用 SDK 以獨立步驟方式個別整合到您的應用程式中，您才能使用它們。

### 另請參閱

- [疑難排解指南 - SDK][Link 25]
 
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
 

<!---HONumber=July15_HO2-->