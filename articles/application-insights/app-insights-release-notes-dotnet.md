<properties 
	pageTitle="Application Insights 的版本資訊" 
	description="最新的更新。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="sergkanz"/>
 
# .NET 適用的 Application Insights SDK 的版本資訊

[Application Insights SDK for .NET](app-insights-start-monitoring-app-health-usage.md) 傳送有關您作用中的應用程式遙測到 [Application Insights](http://azure.microsoft.com/services/application-insights/)，您可以在此分析它的使用情況與效能。


#### 若要在應用程式中安裝 SDK

See [Get started with Application Insights for .NET](app-insights-start-monitoring-app-health-usage.md).

#### 若要升級至最新的 SDK 

* 升級之後，必須將您對 ApplicationInsights.config 所做的任何自訂合併回來。如果您不確定是否自訂過，請建立新專案，將 Application Insights 新增到其中，然後以新專案中的 .config 檔案與您自己的比較。請記下任何的差異。
* 在 [方案總管] 中，以滑鼠右鍵按一下您的專案並選擇 [管理 NuGet 封裝]。
* 設定篩選器來顯示已安裝的封裝。 
* 選取 **Microsoft.ApplicationInsights.Web** 然後選擇 [升級]。(這也會升級所有相依的封裝。)
* 與舊的複本比較 ApplicationInsights.config。您看到的變更大部分是因為我們移除了部分模組，並讓其他的模組可參數化。恢復您對舊檔案做的任何自訂。
* 重建您的方案。

## 0.17 版
- 已移除 Framework 4.5 的應用程式對 EventSource NuGet 的相依性。
- 將不會在伺服器端產生匿名使用者和工作階段 Cookie 。不再支援遙測模組 ```WebSessionTrackingTelemetryModule``` 和 ```WebUserTrackingTelemetryModule```，並且已經從 ApplicationInsights.config 檔案中移除。接受來自 JavaScript SDK 的 Cookie。
- 高負載案例的持續性通道最佳化會用於 Web SDK。修正「死亡螺旋」問題。死亡螺旋這種狀況是，當遙測項目計數中的尖峰，遠超過端點上的節流限制時，將會導致在特定的時間後重試，並且會於再次重試時遭到節流。
- 針對生產最佳化開發人員模式。若意外地離開，開發人員模式不會像之前般嘗試輸出額外的資訊導致大量負荷。
- 根據預設，當應用程式於偵錯工具下才會啟用開發人員模式。您可以使用 ```ITelemetryChannel``` 介面的 ```DeveloperMode``` 屬性覆寫它。

## 0.16 版 

2015-04-28 預覽

- SDK 現在支援 DNX 目標平台，以監視 [.NET 核心架構](http://www.dotnetfoundation.org/NETCore5) 應用程式。
- ```TelemetryClient``` 的執行個體不再快取檢測金鑰。現在如果未明確在 ```TelemetryClient``` 中設定檢測金鑰，則 ```InstrumentationKey``` 會傳回 Null。它可以在您於已收集一些遙測之後設定 ```TelemetryConfiguration.Active.InstrumentationKey``` 時修正問題，像是相依性收集器的遙測模組、Web 要求資料收集和效能計數器收集器會使用新的檢測金鑰。

## 0.15 版

- 新屬性 ```Operation.SyntheticSource``` 現在可用於 ```TelemetryContext```。現在您可以將您的遙測項目標示為「非真實使用者流量」，並指定如何產生此流量。做為範例，藉由設定這個屬性您可以區分來自您的測試自動化的流量和來自負載測試的流量。
- 通道邏輯已移至稱為 Microsoft.ApplicationInsights.PersistenceChannel 的個別 NuGet。預設通道現在稱為 InMemoryChannel
- 新方法 ```TelemetryClient.Flush``` 讓使用者能夠以同步方式從緩衝區排清遙測項目

## 0.13 版

沒有可用於較舊版本的版本資訊。

 

<!---HONumber=62-->