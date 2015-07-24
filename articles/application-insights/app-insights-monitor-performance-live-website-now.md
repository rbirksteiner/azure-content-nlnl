<properties 
	pageTitle="在執行中的網站上診斷效能問題" 
	description="監視網站的效能而不重新部署網站。使用獨立或帶有 Application Insights 的 SDK，取得相依性遙測資料。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="awills"/>
 

# 安裝 Application Insights 狀態監視器以監視網站效能

*Application Insights 目前僅供預覽。*

Visual Studio Application Insights 的狀態監視器可讓您在任何 IIS 伺服器的 Web 應用程式中診斷例外狀況與效能問題。只要將它安裝在您的 IIS Web 伺服器上，它就會檢測在伺服器中找到的 ASP.NET Web 應用程式，並將資料傳送至 Application Insights 入口網站，供您搜尋和分析。您可以將它安裝在您具有系統管理員存取權的任何實體或虛擬伺服器上。

![範例圖表](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

下列三種方法均可讓您將 Application Insights 套用至 IIS Web 應用程式：

* **建置階段：**[加入 Application Insights SDK][greenbrown] 至您的 Web 應用程式程式碼。這會提供您：
 * 標準診斷和使用狀況遙測的範圍。 
 * 如果您想要自行撰寫遙測來追蹤使用狀況或診斷問題，可以使用 [Application Insights API][api]。
* **執行階段：**使用狀態監視器檢測伺服器上的 Web 應用程式。
 * 監視執行中的 Web 應用程式：不需要重建或重新發佈。
 * 標準診斷和使用狀況遙測的範圍。
 * 相依性診斷：找出應用程式使用其他元件 (例如資料庫、REST API 或其他服務) 時的錯誤或效能不佳原因。
 * 對任何遙測問題進行疑難排解。
* **兩者：**將 SDK 編譯至 Web 應用程式程式碼，並在您的 Web 伺服器上執行狀態監視器。集合兩者之優點：
 * 標準診斷和使用狀況遙測。
 * 相依性診斷。
 * 使用 API 撰寫自訂遙測。
 * 對任何 SDK 和遙測問題進行疑難排解。



> [AZURE.TIP]您的應用程式是 [Azure App Service Web 應用程式](../app-service-web/websites-learning-map.md)嗎？ 在 Azure 的應用程式控制台中[加入 Application Insights SDK][greenbrown]，然後[加入 Application Insights 延伸模組](../insights-perf-analytics.md)。


## 在 IIS Web 伺服器上安裝 Application Insights 狀態監視器

1. 您需要 [Microsoft Azure](http://azure.com) 訂用帳戶。 

1. 在 IIS Web 伺服器中，以系統管理員認證登入。
2. 下載並執行[狀態監視器安裝程式](http://go.microsoft.com/fwlink/?LinkId=506648)。

4. 在安裝精靈中，登入 Microsoft Azure。

    ![使用 Microsoft 帳戶認證登入 Azure](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

5. 挑選您想要監視的已安裝 Web 應用程式或網站，然後設定您在 Application Insights 入口網站中查看結果時想要使用的資源。

    ![選擇應用程式和資源。](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    一般來說，您需要選擇設定新的資源和[資源群組][roles]。

    如果您已經為網站設定 [Web 測試][availability]或 [Web 用戶端監視][client]，就可能會使用現有的資源。

6. 重新啟動 IIS。

    ![選擇對話方塊頂端的 [重新啟動]。](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    您的 Web 服務將會中斷一小段時間。

6. 請注意，ApplicationInsights.config 已插入至您想要監視的 Web 應用程式。

    ![在 Web 應用程式的程式碼檔案旁找到 .config 檔案。](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   web.config 也有一些變動。

### 稍後再 (重新) 設定嗎？

完成精靈之後，您隨時都可以重新設定代理程式。如果已安裝代理程式，但初始設定有一些問題，則您也可以這樣做。

![按一下工作列上的 [Application Insights] 圖示](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## 檢視效能遙測資料

登入 [Azure Preview 入口網站](http://portal.azure.com)、瀏覽 Application Insights，然後開啟您建立的資源。

![依序選擇 [瀏覽]、[Application Insights]，然後選取您的應用程式](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

開啟 [效能] 刀鋒視窗，即可查看相依性和其他資料。

![效能](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

逐一點選任一圖表以查看更詳細的資料。


![](./media/app-insights-monitor-performance-live-website-now/appinsights-038-dependencies.png)

#### 相依項目

標示為 HTTP、SQL、AZUREBLOB 的圖表會顯示回應時間和相依性呼叫計數：也就是您應用程式使用的外部服務。



#### 效能計數器

按一下任一效能計數器圖表以變更其顯示內容。或者，您可以加入新的圖表。
 
#### 例外狀況

![逐一點選伺服器例外狀況圖表](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

您可以鑽研過去七天的特定例外狀況，並取得堆疊追蹤和內容資料。


### 沒有遙測資料？

  * 使用您的網站來產生一些資料。
  * 等候幾分鐘讓資料抵達，然後按一下 [重新整理]。
  * 開啟 [診斷搜尋] ([搜尋] 磚) 以查看個別事件。彙總資料在圖表中出現之前，事件通常會顯示在 [診斷搜尋] 中。
  * 開啟狀態監視器，然後選取左窗格中的應用程式。檢查 [設定通知] 區段中是否有任何關於此應用程式的診斷訊息：
  
  ![](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * 請確定伺服器防火牆允許連出流量從連接埠 443 送往 dc.services.visualstudio.com。 
  * 如果您在伺服器上看到有關「權限不足」的訊息：
  * 請在 IIS 管理員中選取應用程式集區，開啟 [進階設定]，並記下 [處理序模型] 節點下的身分識別。
  * 在電腦的管理控制台中，將此身分識別加入至效能監試器使用者群組。
  * 請參閱[疑難排解][qna]。

## 系統需求

支援伺服器上 Application Insights 狀態監視器的作業系統：

- Windows Server 2008
- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

(含最新版 SP 和 .NET Framework 4.0 和 4.5)

在用戶端 Windows 7、8 和 8.1 上，一樣需含有 .NET Framework 4.0 和 4.5

IIS 支援：IIS 7、7.5、8、8.5 (需要有 IIS)

## <a name="next"></a>接續步驟

* [建立 Web 測試][availability]，確定您的網站保持即時狀態。
* [搜尋事件和記錄][diagnostic]以協助診斷問題。
* [加入 Web 用戶端遙測][usage]，以查看網頁程式碼中的例外狀況，並讓您插入追蹤呼叫。
* [將 Application Insights SDK 加入至您的 Web 服務程式碼][greenbrown]，便可以將追蹤和記錄呼叫插入至伺服器程式碼中。

## 影片

#### 效能監視

[AZURE.VIDEO app-insights-performance-monitoring]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=62-->