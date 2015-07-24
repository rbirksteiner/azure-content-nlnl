<properties 
	pageTitle="啟用監視和診斷" 
	description="了解如何在 Azure 設定資源的診斷。" 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="stepsic"/>

# 啟用監視和診斷

在 [Azure 入口網站](http://portal.azure.com)中，您可以為您的資源設定內容豐富且經常產生的監視與診斷資料。您也可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931932.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 設定，並以程式設計方式設定診斷。

在 Azure 中的診斷、監視和計量資料會被儲存到您所選擇的儲存體帳戶。這可讓您使用任何工具來讀取資料，從儲存體總管到 Power BI 再到協力廠商工具。

## 建立資源的時機

當您在 [Azure 入口網站](http://portal.azure.com)中首次建立服務時，大部分的服務可讓您啟用診斷功能。

1. 移至 [**新增**] 並選擇您感興趣的資源。 

2. 選取 [**選用組態**]。![[診斷] 刀鋒視窗](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. 選取 [**診斷**]，然後按一下 [**開啟**]。您將必須選擇您要儲存診斷的儲存體帳戶。將診斷傳送至儲存體帳戶時，您將需要支付儲存和交易的一般數據傳輸費用。

4. 按一下 [**確定**]，並建立資源。

## 變更現有資源的設定

如果您已經建立資源，而且您想要變更診斷設定 (例如，變更資料收集的層級)，您可以直接在 Azure 入口網站中執行此作業。

1. 移至資源，然後按一下 [**設定**] 命令。

2. 選取 [**診斷**]。

3. [**診斷**] 刀鋒視窗會有該資源的所有可能診斷和監視集合資料。在某些資源中，您也可以為資料選擇 [**保留**] 原則，以便將它從儲存體帳戶中清除。![儲存體診斷診斷](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. 選擇設定之後，請按一下 [**儲存**] 命令。如果您是第一次啟用此選項，則顯示監視資料可能需要一點時間。

### 虛擬機器的資料收集類別
在虛擬機器中，所有的計量與記錄都將以一分鐘的間隔來記錄，以便您隨時保有最新的機器相關資訊。

- **基本計量**：有關您的虛擬機器的健康情況計量，例如處理器與記憶體 
- **網路和 Web 計量**：有關您的網路連線與 Web 服務的計量
- **.NET 計量**：有關在您的虛擬機器上執行的 .NET 與 ASP.NET 應用程式計量
- **SQL 計量**：如果您執行的是 Microsoft SQL 服務，這會是其效能計量
- **Windows 事件應用程式記錄檔**：傳送至應用程式通道的 Windows 事件
- **Windows 事件系統記錄檔**：傳送至系統通道的 Windows 事件。此事件同時包含來自 [Microsoft 反惡意程式碼](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409) (英文) 的所有事件。 
- **Windows 事件安全性記錄檔**：傳送至安全性通道的 Windows 事件
- **診斷基礎結構記錄檔**：記錄有關診斷收集基礎結構的資訊
- **IIS 記錄檔**：有關 IIS 伺服器的記錄

請注意，目前不支援特定的 Linux 散發套件，而且虛擬機器上必須安裝客體代理程式。

## 後續步驟

* 每當發生操作事件或計量超過臨界值時，[接收警示通知](insights-receive-alert-notifications.md)。
* [監視服務計量](insights-how-to-customize-monitoring.md)以確保您的服務可用且可回應。
* [自動調整執行個體計數](insights-how-to-scale.md)以確保您的服務可根據需求進行調整。
* 如果您想要了解您的程式碼如何在雲端中執行，可以[監視應用程式效能](insights-perf-analytics.md)。
* [檢視事件和稽核記錄檔](insights-debugging-with-events.md)以了解在您服務內發生的所有內容。
* [追蹤服務健康狀況](insights-service-health.md)可以找出 Azure 何時遭遇效能降低或服務中斷。 
 

<!---HONumber=62-->