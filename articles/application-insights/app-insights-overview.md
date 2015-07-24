<properties 
	pageTitle="什麼是 Application Insights？" 
	description="追蹤即時 Web 或裝置應用程式的使用情况和效能。偵測、分級和診斷問題。持續監視並改善您的使用者的成功。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="awills"/>
 
# 什麼是 Application Insights？

Visual Studio Application Insights 可讓您追蹤即時 Web 或裝置應用程式的效能和使用情形。

* [偵測、分級及診斷][detect]效能問題，並且在大部分的使用者感知之前加以修正。
 *  效能變更或當機的警示。
 *  度量，用以協助診斷效能問題，例如回應時間、CPU 使用量、相依性追蹤。
 *  Web 應用程式的可用性測試。
 *  當機和例外狀況報告和警示
 *  強大的診斷記錄搜尋 (包括從您最愛的記錄架構的記錄追蹤)。
* [持續改進應用程式][knowUsers]，方法是了解使用者如何使用它。 
 * 頁面檢視計數、新的和返回的使用者以及其他核心使用量分析
 * 追蹤您自己的事件，以評估使用模式和各項功能的成功。

## 運作方式

您在應用程式中安裝小型 SDK，並且在 Application Insights 入口網站設定帳戶。SDK 會監視您的應用程式，並將遙測資料傳送至入口網站。入口網站會顯示統計圖表，並提供強大的搜尋工具以協助您診斷任何問題。

![您的應用程式中的 Application Insights SDK 會將遙測傳送到 Azure 入口網站中的 Application Insights 資源。](./media/app-insights-overview/01-scheme.png)

SDK 有數個模組收集遙測，例如，計算使用者、工作階段和效能。您也可以撰寫自己的自訂程式碼，將遙測資料傳送至入口網站。自訂遙測在追蹤使用者劇本時特別有用：您可以計算事件，例如按鈕點擊、達成特定目標或使用者錯誤。

對於 ASP.NET 伺服器與 Azure Web 應用程式，您也可以安裝[狀態監視器][redfield]，有兩種用法。它可讓您：

* 監視 Web 應用程式，而不需要重新建立或重新安裝它。
* 追蹤對相依模組的呼叫。

## 它可以處理何種應用程式？

目前有下列項目適用的 SDK：

* Web 應用程式
 * Azure 或您的 IIS 伺服器上的 [ASP.NET][greenbrown]
 * JRE 上的 [Java][java] 
 * [網頁][client]：HTML+JavaScript
* 裝置應用程式
 * [Windows][windows]
 * [iOS][ios]
 * [Android][android]
 * Cordova
 * [其他平台][platforms]


## 要如何使用它？

* [偵測、分級和診斷問題][detect]
* [分析應用程式的使用量][knowUsers]


## 使用時需要哪些項目？

* Microsoft Azure 訂用帳戶。Application Insights 是 Azure 的其中一個雲端服務，其中也包括網站、資料庫、VM 等等。(但是您可以使用 Application Insights 來監視任何應用程式 - 您的應用程式不需要在 Azure 中執行。) 

 * 您的組織可能擁有帳戶。


## 如何開始使用？

從左側的 [開始使用] 功能表中選擇您的平台。

在所有情況下，基本程序是：

1. 在 [Azure][portal] 中建立 Application Insights 資源 (並且取得其檢測金鑰)。
2. 使用適當的 SDK 檢測您的應用程式 (並且以檢測金鑰加以設定)。
3. 在偵錯模式下或即時執行應用程式。
4. 在 [Azure][portal] 中查看您的資源的結果。

在某些情況下，外掛程式適用於為您執行前兩個步驟的 IDE (例如 Visual Studio 或 Eclipse)。但是，您一律可以手動進行程序。

如果您的應用程式是網站或服務，有一些基本程序以外的選擇性新增和變化：

* 將 SDK 加入伺服器端應用程式和用戶端[裝置][windows]或[網頁][client]。遙測資料會在入口網站合併，讓您可以相互關聯兩個端點的事件。
* 設定 Web 測試，從世界各個角落監視網站的可用性。
* 檢測已經存留的伺服器端應用程式，而不需要重新建立或重新部署它。這適用於 [IIS 伺服器][redfield]和 [Azure Web 應用程式][azure]。
* 監視您的應用程式對其他元件的相依性呼叫，例如資料庫或透過 REST API。適用於 [IIS 伺服器][redfield]和 [Azure Web 應用程式][azure]。


<!--Link references-->

[android]: app-insights-android.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[ios]: app-insights-ios.md
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=62-->