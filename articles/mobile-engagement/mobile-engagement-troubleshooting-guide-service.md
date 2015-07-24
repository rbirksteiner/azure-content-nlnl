<properties 
   pageTitle="Azure Mobile Engagement 疑難排解指南 - 服務" 
   description="Azure Mobile Engagement 疑難排解" 
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

# 服務問題的疑難排解指南

以下是您可能會遇到，有關 Azure Mobile Engagement 執行的問題。

## 服務中斷

### 問題
- 似乎是因為 Azure Mobile Engagement 服務中斷所造成的問題。

### 原因
- 似乎是因為 Azure Mobile Engagement 服務中斷所造成的問題，可能由數種不同原因所造成：
    - 原本顯示為 Azure Mobile Engagement 系統問題的隔離問題
    - 伺服器關閉所造成的已知問題 (不一定會顯示在伺服器狀態中)：
	- 排程延遲、目標錯誤、徽章更新問題、統計資料停止收集、推播停止運作、API 停止運作、無法建立新的應用程式或使用者、DNS 錯誤，以及 UI、API 或裝置上應用程式中的逾時錯誤。
    - 雲端相依性中斷 [Azure 服務狀態](http://status.azure.com/)
    - 推送通知服務 (PNS) 相依性中斷
    - 應用程式商店中斷

1) 若要測試是否為系統性問題，您可以從下列不同的位置測試相同的函數：
   
   - Azure Mobile Engagement 整合式應用程式
   - 測試裝置
   - 測試裝置作業系統版本
   - 活動
   - 系統管理員使用者帳戶
   - 瀏覽器 (IE、Firefox、Chrome 等)
   - 電腦

2) 若要測試問題是否只影響 UI 或 API：

   - 在 Azure Mobile Engagement UI 和 Azure Mobile Engagement API 中測試相同的函數。

3) 若要測試是否為您行動電話網路的問題：

   - 測試時透過 WIFI 和 3G 行動電話網路連線到網際網路。
   - 確認您的防火牆不會封鎖任何 Azure Mobile Engagement 的 IP 位址或連接埠。

4) 若要測試是否為您裝置的問題：

   - 測試您的裝置是否能透過另一個 Azure Mobile Engagement 整合式應用程式連接到 Azure Mobile Engagement。
   - 測試您是否可以從手機產生可在 Azure Mobile Engagement UI 中看到的事件、工作和當機情況。 
   - 測試是否能夠從 Azure Mobile Engagement UI，根據裝置識別碼傳送推播通知給您的裝置。 

5) 若要測試是否為您應用程式的問題：

   - 從模擬器 (而不是實體裝置)，安裝並測試您的應用程式：
   
6) 若要測試問題是否與使用者裝置的作業系統升級有關 (需要升級 SDK 才能解決)：

   - 在使用不同作業系統版本的不同裝置上測試應用程式。
   - 確認您使用最新版的 SDK。
 
## 連線和資訊不正確的問題

### 問題
- 登入 Azure Mobile Engagement UI 時發生問題。
- 使用 Azure Mobile Engagement API 時發生連線錯誤。
- 透過裝置 API 上傳應用程式資訊標記時發生問題。
- 從 Azure Mobile Engagement 下載記錄檔或匯出資料時發生問題。
- Azure Mobile Engagement UI 中顯示的資訊不正確。
- Azure Mobile Engagement 記錄檔中顯示的資訊不正確。

### 原因
* 確認您的使用者帳戶具備執行該作業所需的權限。
* 確認問題不是因為一部電腦或是因為您的區域網路而產生。
* 確認 Azure Mobile Engagement 服務沒有報告中斷。
* 確認您的應用程式資訊標記遵循以下所有規則：
	- 僅使用 UTF8 字元集 (不支援 ANSI 字元集)。
    - 使用逗號 "," 做為分隔字元 (您可以開啟服務要求，來要求將 .csv 分隔字元從逗號變更為另一個字元，例如分號 ";")。
    - 使用全部小寫的布林值 "true" 和 "false"。
    - 使用小於 35MB 檔案大小上限的檔案。
 

<!---HONumber=July15_HO1-->