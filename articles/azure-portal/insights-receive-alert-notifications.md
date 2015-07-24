<properties 
	pageTitle="接收警示通知" 
	description="在符合警示規則條件時收到通知。" 
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
	ms.date="04/25/2015" 
	ms.author="stepsic"/>

# 接收警示通知

您可以收到以您 Azure 服務的監視計量或事件為基礎的警示。

計量值的警示規則為，當特定計量的值超出所指派的閾值時，警示規則將會作用並可以傳送通知。事件的警示規則為，規則可以在*每個*事件或只在發生特定事件數目是傳送通知。

當您建立警示規則時，您可以選取選項傳送電子郵件通知給服務管理員、共同管理員或其他您可以指定的系統管理員。系統將會在規則作用及警示條件解決時，傳送電子郵件通知。

您可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 設定，並以程式設計方式取得警示規則的相關資訊。

## 建立警示規則

1. 在 [入口網站](https://portal.azure.com/)，按一下 [**瀏覽**]，然後按一下您有興趣監視的資源。

2. 在 [**作業**] 透鏡中按一下 [**警示規則**] 磚。

3. 按一下 [**新增警示**] 命令。![Add alert](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. 您可以為您的警示規則命名，並選擇將會顯示在電子郵件通知的描述。

5. 當您選取 [**計量**] 時，您將會為計量選擇條件和閾值標準。這是 Azure 用來監視和規劃警示活動的一段時間。![條件和閾值](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. 您也可以選擇 [**事件**]，以在特定事件發生時收到通知。![事件](./media/insights-receive-alert-notifications/Insights_Events.png)

7. 最後，您可以選擇傳送電子郵件通知給負責管理員。

按一下 [儲存] 之後，幾分鐘內您將收到所選的計量是否超出闕值的通知。

## 管理您的警示規則

當您建立警示規則之後時，您可以檢視警示閾值與前一天計量相比較的預覽。

![事件](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


您當然可以編輯此警示規則，而如果您想要暫時停止接收相關通知，可以 [**停用**] 或 [**啟用**] 規則。

## 後續步驟

* [監視服務計量](insights-how-to-customize-monitoring.md)以確保您的服務可用且可回應。
* [啟用監視和診斷](insights-how-to-use-diagnostics.md)來在您服務中收集詳細的高頻率計量。
* 使用 Application Insights [監視任何網頁的可用性和回應性](../app-insights-monitor-web-app-availability.md)，讓您可以找出您的頁面是否關閉。
* 如果您想要了解您的程式碼如何在雲端中執行，可以[監視應用程式效能](insights-perf-analytics.md)。
* [檢視事件和稽核記錄檔](insights-debugging-with-events.md)以了解在您服務內發生的所有內容。
* [追蹤服務健康狀況](insights-service-health.md) 可以找出 Azure 何時遭遇效能降低或服務中斷。
 

<!---HONumber=62-->