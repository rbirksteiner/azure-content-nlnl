<properties
	pageTitle="Operational Insights 安全性"
	description="深入了解 Operational Insights 如何保護您的隱私權和保護您的資料安全。"
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="dev-center-name"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/07/2015"
	ms.author="banders"/>

# Operational Insights 安全性

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft 致力於保護您的隱私權和保護資料安全，同時提供軟體和服務，幫助您管理組織的 IT 基礎結構。我們了解當您將資料委託給他人管理時，將需要嚴格的安全性。Microsoft 從撰寫程式碼到運作服務均遵守嚴格的規範與安全性指導方針。

保全和保護資料是在 Microsoft 的第一要務。如有任何問題、建議或關於下列任一項資訊的問題 (包括我們的安全性原則)，請與我們連絡：[Azure 支援選項](http://azure.microsoft.com/support/options/)。

本文說明 Microsoft Azure Operational Insights 中的資料收集、處理和保全方式。您可以使用任一代理程式直接連接到 Web 服務，或可使用 System Center Operations Manager 收集 Operational Insights 服務的作業資料。收集的資料是透過網際網路傳送至裝載於 Microsoft Azure 的 Operational Insights 服務。

Operational Insights 服務會使用下列方法安全地管理您的資料：

**資料隔離：**客戶資料以邏輯方式分開儲存整個作業的見解服務每個元件上。每個組織加上標記的所有資料。這項標記作業在整個資料生命週期持續發生，它會強制執行服務的每個層級。

每個客戶都有專用的 Azure Blob 可裝載長期資料。Blob 是以每個客戶唯一的金鑰加密，而且每 90 天會變更一次。

**資料保留：**每個智慧組件的彙總度量會儲存在 Microsoft Azure 代管的 SQL Database。這項資料會儲存 390 天。已編制索引的搜尋資料在清理前平均會儲存 10 天。如果提前達到每個資料類型 2 千萬筆記錄的上限，則 Operational Insights 不到 10 天就會先清理資料。如果由 10 天內未達到資料限制，操作 Insights 等待達到限制時要清理它。

**實體安全性：**Operational Insights 服務是由 Microsoft 人員操縱，所有活動都有記錄並且可供稽核。Operational Insights 服務是完全在 Azure 中執行，並符合 Azure 通用工程準則。您可以在 [Microsoft Azure 安全性概觀](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf)的第 18 頁上檢視 Azure 資產之實體安全性的詳細資料。

**規範與認證：**在 Operational Insights 服務正式推出前，Operational Insights 軟體開發和服務小組會積極地與 Microsoft 法律和規範小組及其他產業合作夥伴合作取得各種不同的認證 (包括 ISO)。

我們目前符合下列安全性標準：

- Windows 通用工程準則
- Microsoft 高可信度電腦運算認證


## 資料流安全性
下圖顯示貴公司的資訊流，以及當移至 Operational Insights 服務時如何受到保護，最後由您在 Operational Insights 中看到。圖表後面詳述每個步驟的詳細資訊。

![Operational Insights 資料收集和安全性的影像](./media/operational-insights-security/security.png)

### 1.註冊 Operational Insights 並收集資料

如果您的組織要將資料傳送給 Operational Insights 服務，您必須在直接連接到 Web 服務時設定 Microsoft Monitoring 代理程式，或使用 Operations Manager 中 Operations 主控台的組態精靈。使用者 (可能是您、其他個別使用者或一群人) 必須建立一或多個 Operational Insights 帳戶，並且使用下列其中一個帳戶註冊每個直接連接的代理程式或其 Operations Manager 環境：


- [組織識別碼](../sign-up-organization.md)

- [Microsoft 帳戶 - Outlook、Office Live、MSN](../sign-up-organization.md)

Operational Insights 帳戶是資料收集、彙總、分析以及呈現的位置。Operational Insights 帳戶主要是做為資料分割資料，每個 Operational Insights 帳戶都是唯一的。例如，您可能想要使用一個 Operational Insights 帳戶管理實際執行資料，使用另一個帳戶管理測試資料。帳戶也會協助系統管理員控制資料的使用者存取。每個 Operational Insights 帳戶可以有多個與其關聯的帳戶，每個使用者帳戶可以有多個 Operational Insights 帳戶。

當組態精靈完成時，每個 Operations Manager 管理群組就會建立與 Operational Insights 服務的連接。然後，您會使用 [新增電腦精靈] 來選擇允許管理群組中的哪些電腦將資料傳送至服務。

兩種類型的代理程式都會收集 Operational Insights 的資料。所收集的資料類型取決於使用的解決方案類型。解決方案是預先定義的檢視、記錄搜尋查詢、資料收集規則，以及處理邏輯的組合。只有 Operational Insights 系統管理員可以使用 Operational Insights 來匯入解決方案。在匯入解決方案之後，便會移到 Operations Manager 管理伺服器 (如果使用的話)，然後移至您所選擇的 Operations Manager 代理程式。之後，代理程式會收集資料。

下表列出 Operational Insights 中可用的解決方案與它們所收集的資料類型。

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>解決方案</b></td>
		<td><b>資料類型</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>組態評估</td>
		<td>組態資料、中繼資料及狀態資料</td>
    </tr>
    <tr align="left" valign="top">
		<td>容量規劃</td>
		<td>效能資料、中繼資料及狀態資料</td>
    </tr>
	<tr align="left" valign="top">
		<td>反惡意程式碼</td>
		<td>組態資料、中繼資料及狀態資料</td>
    </tr>
	    <tr align="left" valign="top">
		<td>系統更新評估</td>
		<td>中繼資料和狀態資料</td>
    </tr>
    <tr align="left" valign="top">
		<td>記錄檔管理</td>
		<td>使用者定義事件記錄檔</td>
    </tr>
    <tr align="left" valign="top">
		<td>變更追蹤</td>
		<td>軟體清查和 Windows 服務中繼資料</td>
    </tr>
    <tr align="left" valign="top">
		<td>SQL 和 Active Directory 評估</td>
		<td>WMI 資料、登錄資料、效能資料和 SQL Server 動態管理檢視結果</td>
    </tr>
    </tbody>
    </table>


下表顯示資料類型範例：

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>資料類型</b></td>
		<td><b>欄位</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>警示</td>
		<td>警示名稱、警示描述、BaseManagedEntityId、問題識別碼、IsMonitorAlert、RuleId、ResolutionState、優先順序、嚴重性、分類、擁有者、ResolvedBy、TimeRaised、TimeAdded、LastModified、LastModifiedBy、LastModifiedExceptRepeatCount、TimeResolved、TimeResolutionStateLastModified、TimeResolutionStateLastModifiedInDB、RepeatCount</td>
    </tr>
    <tr align="left" valign="top">
		<td>組態</td>
		<td>CustomerID、AgentID、EntityID、ManagedTypeID、ManagedTypePropertyID、CurrentValue、ChangeDate</td>
    </tr>
	<tr align="left" valign="top">
		<td>事件</td>
		<td>EventId、EventOriginalID、BaseManagedEntityInternalId、RuleId、PublisherId、PublisherName、FullNumber、Number、Category、ChannelLevel、LoggingComputer、EventData、EventParameters、TimeGenerated、TimeAdded<p><b>附註：</b>當您使用自訂欄位將事件記錄至 Windows 事件記錄檔時，Operational Insights 會收集它們。</td>
    </tr>
	    <tr align="left" valign="top">
		<td>中繼資料</td>
		<td>BaseManagedEntityId、ObjectStatus、OrganizationalUnit、ActiveDirectoryObjectSid、PhysicalProcessors、NetworkName、IPAddress、ForestDNSName、NetbiosComputerName、VirtualMachineName、LastInventoryDate、HostServerNameIsVirtualMachine、IP 位址、NetbiosDomainName、LogicalProcessors、DNSName、DisplayName、DomainDnsName、ActiveDirectorySite、PrincipalName、OffsetInMinuteFromGreenwichTime</td>
    </tr>
    <tr align="left" valign="top">
		<td>效能</td>
		<td>ObjectName、CounterName、PerfmonInstanceName、PerformanceDataId、PerformanceSourceInternalID、SampleValue、TimeSampled、TimeAdded</td>
    </tr>
    <tr align="left" valign="top">
		<td>狀況</td>
		<td>StateChangeEventId、StateId、NewHealthState、OldHealthState、Context、TimeGenerated、TimeAdded、StateId2、BaseManagedEntityId、MonitorId、HealthState、LastModified、LastGreenAlertGenerated、DatabaseTimeModified</td>
    </tr>
    </tbody>
    </table>


### 2.從代理程式傳送資料

使用直接連接到 Web 服務的代理程式，您以金鑰進行註冊，並且使用連接埠 443 保護代理程式和 Operational Insights 服務之間建立之連線的安全。

使用 Operations Manager，您以 Operational Insights 服務進行註冊，並且使用連接埠 443 保護 Operations Manager 管理伺服器與 Operational Insights 服務之間建立之 HTTPS 連線的安全。如果 Operations Manager 因為任何原因而無法與服務通訊，收集的資料會儲存在暫時快取，且管理伺服器在 2 個小時內每 8 分鐘會嘗試重新傳送資料。收集的資料會壓縮，並且傳送給 Operational Insights 服務，略過內部部署資料庫，因此不會對它們產生任何負載。傳送收集的資料之後，會從快取中移除。

### 3.Operational Insights 服務會收到資料並且處理

Operational Insights 服務會確保內送資料是來自信任的來源，方法是驗證憑證和資料完整性。未處理的原始資料會儲存為 [Microsoft Azure 儲存體](http://azure.microsoft.com/documentation/services/storage/)中的 Blob。每個 Operational Insights 使用者都有專屬的 Azure Blob，只能由該使用者存取。所儲存的資料類型取決於匯入和用來收集資料的解決方案類型。

Operational Insights 服務會處理原始資料，彙總資料會儲存在 SQL Database。Operational Insights 服務和 SQL Database 之間的通訊依賴 SQL Database 驗證。

### 4.使用 Operational Insights 存取資料

您可以使用您先前設定的帳戶登入 Operational Insights。Operational Insights 和 Operational Insights 服務之間的所有流量是透過安全的 HTTPS 通道傳送的。
 

<!---HONumber=July15_HO1-->