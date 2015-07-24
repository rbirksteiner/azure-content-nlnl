<properties 
	pageTitle="疑難排解 Windows 裝置中的 Application Insights" 
	description="疑難排解指南以及問題和答案。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/27/2015" 
	ms.author="awills"/>
 
# Windows 裝置適用的 Application Insights 的疑難排解和問答集

[Windows 中的 Visual Studio Application Insights][windows] 疑問或問題？ 以下是一些秘訣。



## 沒有資料 

*我已成功加入 Application Insights 並執行我的應用程式，但在入口網站中從未看到資料。*

* 請稍等片刻，然後按一下 [重新整理]。重新整理目前不是自動的。
* 檢查您已在 ApplicationInsights.config 檔案中定義檢測金鑰，且該金鑰與 Application Insights 入口網站中的金鑰相同。若要查看金鑰，請按一下 [概觀] 刀鋒視窗上的 [Essentials]。
* 確定您的應用程式[要求連出網路存取](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx)。
* 模擬器或測試裝置與 Application Insights 入口網站之間有防火牆嗎？ 您可能必須開啟 TCP 連接埠 80 和 443，以允許連出流量送往 dc.services.visualstudio.com 和 f5.services.visualstudio.com。
* 在 Microsoft Azure 開始面板中，查看服務狀態對應。如果看到一些警示指示，請等待它們恢復 [正常]，然後關閉再重新開啟 Application Insights 應用程式分頁。


#### 我曾經看到資料，但是已停止

* 檢查[狀態部落格](http://blogs.msdn.com/b/applicationinsights-status/)。
* 您有達到資料點的每月配額嗎？ 開啟 [設定/配額和定價] 即可查看。若有達到配額，您可以升級您的方案，或付費取得額外容量。請參閱[定價配置](http://azure.microsoft.com/pricing/details/application-insights/)。


## 我要如何將 Application Insights 加入至通用應用程式？

手動將 NuGet 封裝加入至方案中的每個裝置專案。請參閱[開始使用 - 通用應用程式][universal]。

## 停用遙測

*如何停用遙測收集？*

在程式碼中：

    TelemetryConfiguration config = TelemetryConfiguration.Active;
    config.TrackingIsDisabled = true;

## 變更目標資源

*如何變更我的專案將資料傳送到哪一個 Application Insights 資源？*

在新的 Application Insights 概觀刀鋒視窗中，開啟 Essentials 並複製檢測金鑰。

將金鑰貼上到 `<InstrumentationKey>` 節點中的 ApplicationInsights.config 檔案。

或者，如果您想要在執行階段變更目標，請使用：

     var telemetry = new TelemetryClient();
     telemetry.Context.InstrumentationKey = newKey;
    
## 如何監視用戶端-伺服器應用程式？

作法有二：

* 針對用戶端和伺服器建立兩個 Application Insights 資源 (具有不同的檢測金鑰)。但是在相同的 Azure 資源群組中建立它們。這可輕鬆地在彼此之間切換。
* 使用一個 Application Insights 資源，並將其檢測金鑰同時放入用戶端和伺服器專案。如此便可以相互關聯兩個來源的度量和事件。

為了協助相互關聯用戶端和伺服器中的事件，請針對每個要求產生作業識別碼。在用戶端與伺服器之間傳輸，將它加入至兩端的遙測：

    telemetry.Context.OperationId = opid;


## Azure 開始畫面

*我正在查看 [Azure 入口網站](http://portal.azure.com)。地圖是否告知有關我的應用程式的相關資訊？*

否，它會顯示世界各地 Azure 伺服器的健全狀況。

*從 Azure 開始面板 (主畫面) 中，如何找到我應用程式的相關資料？*

假設您[已設定 Application Insights 的應用程式][windows]，並按一下 [瀏覽]，且選取 [Application Insights]，然後選取您為應用程式所建立的應用程式資源。日後若要更快速地從該處開始，您可以將資源釘選至開始面板。

## 資料保留 

*資料保留在入口網站多久的時間？ 是否安全？*

請參閱[資料保留和隱私權][data]。

## 後續步驟

*設定 Java 伺服器應用程式的 Application Insights。我還可以做什麼？*

* [監視網頁可用性][availability]
* [監視網頁使用狀況][usage]
* [追蹤使用狀況並診斷裝置應用程式中的問題][platforms]
* [撰寫程式碼以追蹤應用程式使用狀況][track]
* [擷取診斷記錄][javalogs]


## 取得說明

* [堆疊溢位](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-custom-events-metrics-api.md
[universal]: app-insights-windows-get-started.md#universal
[usage]: app-insights-web-track-usage.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=62-->