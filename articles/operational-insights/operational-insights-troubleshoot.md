<properties
   pageTitle="疑難排解 Operational Insights 問題"
   description="了解有關 Operational Insights 的問題疑難排解"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

#疑難排解 Operational Insights 問題

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

您可以使用下列各節中的資訊，幫助您疑難排解問題。如果您遇到的問題不在本文中，您可以嘗試 [Operational Insights 小組部落格](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx)。

## 診斷 Operational Insights 連線問題

由於 Microsoft Azure Operational Insights 依賴雲端來回移動的資料，因此連線問題可能有很大的影響。使用下列資訊來瞭解並解決連線問題。


**錯誤訊息：**已驗證網際網路連接，但無法建立至 Operational Insights 服務的連接。請稍後再試一次。

**可能的原因：**Operational Insights 服務正在進行維護。請等待 Operational Insights 維護完成。 - 您的網路已封鎖 Operational Insights。請連絡網路系統管理員並要求存取 Operational Insights，或使用另一部伺服器做為您的閘道。

**錯誤訊息：**無法建立網際網路連接。請檢查您的 Proxy 設定。

**可能的原因：**此伺服器未連線至網際網路。檢查網際網路連線狀態，並將伺服器連線到網際網路。 - Proxy 設定不正確。如需有關如何設定或變更 Proxy 設定的詳細資訊，請參閱[設定 Proxy 和防火牆設定](operational-insights-proxy-firewall.md)。 - Proxy 伺服器需要驗證。請參閱[設定 Proxy 和防火牆設定](operational-insights-proxy-firewall.md)以了解如何設定 Operations Manager 來使用 Proxy 伺服器。


## 疑難排解 SQL Server 探索

如果您執行 Microsoft SQL Server 2008 R2，且儘管部署 Operations Manager 代理程式，您仍看不到此伺服器的警示，則您可能遇到探索問題。

若要確認這是否為問題的來源，請檢查下列兩個問題：

- 在 Operations Manager 事件記錄檔中，您會看到事件識別碼 4001。這個事件表示有無效的類別。

- 在「SQL Server 組態管理員」中，當您檢視 SQL Server 服務時，您會看到錯誤訊息「遠端程序呼叫失敗 [0x0800706be]」。

如果兩個問題都發生，則您需要安裝 SQL Server 2008 R2 Service Pack 2。若要下載此 Server Pack，請參閱 Microsoft 下載中心的 [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310)。

安裝 Service Pack 之後，您應該會在 24 小時內看到伺服器的 Operational Insights 資料。

## 疑難排解代理程式或 Operations Manager 至 Operational Insights 的資料流程問題

下列程序集是用做指南，協助您疑難排解設定為向 Azure Operational Insights 報告資料之直接連接的代理程式或 Operations Manager 部署。

### 程序 1：驗證權限管理套件是否下載到您的 Operations Manager 環境
>[AZURE.NOTE]如果您只使用直接代理程式，您可以跳到下一個程序。

您會看到更多或更少 MP 是依據您從 OpInsights 入口網站啟用的解決方案 (先前稱為智慧套件)。在其名稱中搜尋關鍵字「Advisor」或「智慧」。您可以使用 OpsMgr PowerShell 檢查這些 MP：

```Powershell
Get-SCOMManagementPack | where {$_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version
Get-SCOMManagementPack | where {$_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version
```

>[AZURE.NOTE]如果您正在疑難排解容量解決方案，請檢查*有幾個*管理套件名稱包含「容量」：相同的 MP 配套中有兩個具有相同顯示名稱的管理套件 (但內部識別碼不同)；如果兩個的其中一個未匯入 (通常是因為缺少 VMM 相依性)，則不會匯入另一個 MP，且不會重試作業。

您應該會看到下列三個與「容量」相關的管理套件 - Microsoft System Center Advisor 容量智慧套件 - Microsoft System Center Advisor 容量智慧套件 - Microsoft System Center Advisor 容量儲存體資料

如果您只看到一或兩個，而不是全部三個，請將其移除並等候 5/10 分鐘，讓 Operations manager 下載和重新匯入它 – 在這段期間檢查事件日誌檔中的錯誤。

### 程序 2：驗證是否將適當的解決方案下載到直接代理程式
>[AZURE.NOTE]如果您只使用 Operations Manager，您可以忽略此程序。

在直接代理程式中，應該會看到 **C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\Health Service State\\Management Packs** 下正在快取的解決方案集合原則


### 程序 3：驗證是否正在將資料傳送至 Advisor 服務 (或在上一次嘗試)
根據您使用直接連接的代理程式或 Operations Manager，您可以在直接代理程式電腦或 Operations Manager 管理伺服器上執行下列程序：

1. - 開啟「效能監視器」
2. - 選取 [Health Service Management Groups]
3. - 加入開頭為 ‘HTTP’ 的所有計數器。

如果設定正確，您應該會在上傳事件和其他資料項目時看到這些計數器的活動 (根據入口網站中的解決方案，及設定的記錄檔收集原則)。這些計數器不一定要持續「忙碌」 - 如果您看到少許或看不到活動，則可能是您沒有登入許多解決方案，或有非常輕量級的收集原則。

### 程序 4：檢查管理伺服器或直接代理程式事件記錄檔中的錯誤
最後一個步驟是，如果上述所有動作都失敗，但您仍然看不到服務所接收的資料，請檢查**事件檢視器**中是否有任何錯誤。

開啟 [**事件檢視器**] –> [**應用程式及服務**] –> [**Operations Manager**] 並透過事件來源進行篩選：[**Advisor**]、[**健全狀況服務模組**]、[**HealthService**] 和 [**服務連接器**] (只有最後一個適用於直接代理程式)。

其中大部分事件在任一 Operations Manager 和直接代理程式中皆類似，疑難排解步驟也類似。Operations Manager 和直接代理程式之間的唯一差異是註冊程序 (在 Operations Manager 中為 GUI，在直接代理程式中為工作區識別碼/金鑰組合)，但在初始註冊之後會交換及使用憑證，且服務通訊的一切都相同。

因此，其中許多事件會套用至這兩種報告基礎結構。以下是您可能會看到的常見清單。

### 事件來源為「健全狀況服務模組」
##### 事件識別碼 2138
這表示您的 Proxy 需要驗證。請遵循步驟以[設定 Proxy 伺服器](https://msdn.microsoft.com/library/azure/dn884643.aspx)

##### EventID 2137
Operations Manager 無法讀取驗證憑證。重新執行 Advisor 註冊精靈會修正憑證/RunAs 帳戶

##### EventID 2132
表示**未獲授權**。可能是服務的憑證和/或註冊發生問題；嘗試重新執行註冊精靈將修復憑證和 RunAs 帳戶。此外，確認已將 Proxy 設定為允許排除。如需詳細資訊，請參閱[設定 Proxy 伺服器](https://msdn.microsoft.com/library/azure/dn884643.aspx)

##### EventID 2129
這是因為 SSL 交涉失敗而失敗的連線。確認您的系統是設定為使用 TLS 而非 SSL。此伺服器上可能有一些與 Chiper 有關的異常 SSL 設定，可能位於 Internet Explorer 的 **進階** 選項，或 Windows 登錄的金鑰底下

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL

##### EventID 2127
傳送資料失敗並收到錯誤碼。如果只是短暫發生一次，它可能只是小異常。請持續監控以瞭解它的發生頻率。如果相同情況經常出現 (每隔 10 分鐘或時間相當長)，則它可能是真正的問題 – 請檢查您的網路組態、上述的 Proxy 設定，及重新執行註冊精靈。但如果只是偶發性發生此問題 (也就是每天好幾次)，則所有情況應該沒問題，因為資料會排入佇列並重新傳輸。如果是它發生的頻率，則可能只是網路逾時。

有些 HTTP 錯誤碼有一些特殊的意義，例如：

- 當 MMA 直接代理程式或管理伺服器第一次嘗試將資料傳送給我們的服務時，它會取得 500 錯誤 (包含內部 404 錯誤碼) – 404 代表找不到；這表示我們將為您使用的這個新工作區的存放區還沒準備好，也就是正在佈建。但在下次重試時即可備妥，且流量會開始運作 (在正常情況下)。
- 403 可能表示發生權限/認證問題等等。

##### EventID 2128
DNS 名稱解析失敗。您的伺服器無法解析會將資料傳送到的網際網路位址。原因可能在於您電腦的 DNS 解析程式設定、不正確的 Proxy 設定，或您的提供者 (暫時) 發生 DNS 問題。如先前的事件一般，視問題是不斷發生或「偶爾」，這可能表示真正有問題 – 或也許不是。

##### EventID 2130
逾時。如先前的事件一般，視問題是不斷發生或偶爾，這可能表示真正有問題 – 或也許不是。

### 事件來源為 'HealthService'
##### EventID 4511
無法載入模組 "System.PublishDataToEndPoint" – 找不到檔案。類型 "System.PublishDataToEndPoint" (CLSID "{D407D659-65E4-4476-BF40-924E56841465}") 的模組初始化失敗並出現錯誤碼「系統找不到指定的檔案」。此錯誤表示您的電腦有不包含所需模組的舊 DLL。修正方法是將管理伺服器更新為最新的更新彙總套件。

##### EventID 4502
模組已損毀。如果您在擁有 **CollectInstanceSpace** 或 **CollectTypeSpace** 等名稱的工作流程中看到這種情況，可能表示伺服器正在傳送某些組態資料但遇到問題。根據發生的頻率發生 (持續或「偶爾」)，這也許是問題。如果每小時發生多次，則肯定是問題。如果這項作業每天只有一次或兩次失敗，它可以很好地復原。根據此模組實際的失敗方式 (會有更詳細的說明)，這可能是內部部署問題 (也就是收集至資料庫) 或傳送至雲端的問題。請確認您的網路和 Proxy 設定，最壞情況下請嘗試重新啟動 HealthService。

##### EventID 4501
模組「System.PublishDataToEndPoint」毀損。類型 "System.PublishDataToEndPoint" 的模組報告了錯誤 87L，其在針對執行個體 "Operations Manager Management Group" 執行的規則 "Microsoft.SystemCenter.CollectAlertChangeDataToCloud" 過程中執行，在管理群組 "SCOMTEST" 中包含 id:"{6B1D1BE8-EBB4-B425-08DC-2385C5930B04}"。藉助此精確的工作流程、模組和錯誤，您應該*不會*再看到這種情況，它以往是[錯誤*，現在已修正*](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale)。如果再次出現，請透過您慣用的 Microsoft 支援通道回報。


### 事件來源為「服務連接器」
##### EventID 4002
服務傳回 HTTP 狀態碼 403 以回應查詢。請聯絡服務系統管理員以檢查服務的健全狀況。稍後將重試查詢。您可以在代理程式的初始註冊階段取得 403，您會看到像 https://<YourWorkspaceID>.oms.opinsights.azure.com/ AgentService.svc/AgentTopologyRequest 的URL，錯誤碼 403 表示「禁止」 – 這通常是錯誤複製的工作區識別或金鑰，或者時鐘未在電腦上同步處理。請嘗試同步處理可靠的時間來源，並在 [控制台] 小程式中為 Microsoft Monitoring Agent 使用連線能力檢查，確認您擁有正確的工作區識別碼和金鑰。


### 程序 5：尋找代理程式以傳送他們的資料，並在入口網站中索引它
登入 OpInsights 入口網站，從 [概觀] 頁面瀏覽至小磚 **伺服器和使用方式**；這會顯示管理群組 (及其代理程式) 和直接代理程式是否將資料報告到記錄搜尋中。磚上的代理程式數目是衍生自資料 – 如果機器有 2 週不報告，它們將關閉雷達圖。

向下鑽研可讓您記錄搜尋並顯示每部電腦的前一個索引資料的時間戳記。您可以從該處探索資料的內容。根據設定的資料集合數量和解決方案，資料上傳排程和速度可能會改變。

這個頁面也提供了計量資訊 (這不會使用記錄搜尋索引而是使用計費系統，它會每隔幾小時重新整理)，其與傳送至服務的資料量有關，並且由解決方案細分。

<!---HONumber=July15_HO2-->