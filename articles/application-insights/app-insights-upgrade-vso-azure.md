<properties 
	pageTitle="從 Application Insights 的舊 Visual Studio Online 版本升級" 
	description="升級現有專案"
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
	ms.date="06/19/2015" 
	ms.author="awills"/>
 
# 從 Application Insights 的舊 Visual Studio Online 版本升級

本文件僅適用仍在使用屬於 Visual Studio Online 一部分的舊版 Application Insights 專案的您。該版本將自然進入關閉階段，而我們鼓勵您升級為新版本，它屬於 Microsoft Azure 內的服務。

## 我擁有的是什麼版本？

如果您使用 Visual Studio 2013 Update 3 或更新版本將 Application Insights 加入專案，它最可能使用新 Azure 版本。

開啟 ApplicationInsights.config。如果它有節點 `ActiveProfile` 和 `Profiles`，則是舊版，因此您應該升級。

或查看 Visual Studio 方案總管中的專案，在 [參考] 下選取 Microsoft.ApplicationInsights。在 [屬性] 視窗中尋找 [版本]。如果少於 0.12，則您應該升級。

## 如果您有 Visual Studio 專案...

1. 在 Visual Studio 2013 Update 3 或更新版本中開啟專案。
2. 刪除 ApplicationInsights.config 
3. 從專案移除 Application Insights NuGet 封裝。若要執行此動作，請在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [管理 NuGet 封裝]。

    ![](./media/app-insights-upgrade-vso-azure/nuget.png)
4. 刪除資料夾 AppInsightsAgent 和它所包含的檔案。

    ![](./media/app-insights-upgrade-vso-azure/folder.png)

5. 移除 ServiceDefinition.csdef 和 ServiceConfiguration.csfg 中的所有 Microsoft.AppInsights 設定名稱和值

    ![](./media/app-insights-upgrade-vso-azure/csdef.png)
4. SDK：以滑鼠右鍵按一下專案，然後選擇 [[加入 Application Insights][greenbrown]]。如此會將 SDK 加入到您的專案，同時在 Azure 中建立新 Application Insights 資源。
5. 記錄：如果您的程式碼包含對舊 API 的呼叫(例如 LogEvent())，在嘗試建置方案時將會發現它們。請將它們更新以[使用新 API][track]。
6. 網頁：如果您的專案包含網頁，請取代 <head> 區段中的指令碼。一般來說，主版頁面中只有一份，例如 Views\Shared_Layout.cshtml。[從 Azure 的 Application Insights 資源中的 [快速入門] 分頁取得新指令碼][usage]。如果您的網頁主體中包括遙測呼叫 (例如 logEvent 或 logPage)，請[將它們更新為使用新 API][api]。
7. 伺服器監視：如果您的應用程式是執行於 IIS 的服務，請從伺服器解除安裝 Microsoft Monitoring Agent，然後[安裝 Application Insights 狀態監視器][redfield]。
8. Web 測試：如果您使用 Web 可用性測試，請[在新入口網站上重新建立測試][availability]，以及其警示。
9. 警示：在 Azure 入口網站中設定[度量的警示][alerts]。


## 如果您具有 Java Web 服務...

1. 在您的伺服器機器中，透過從 Web 服務啟動檔中移除 APM 代理程式的參考來停用舊的代理程式。在 TomCat 伺服器上，編輯 Catalina.bat。在 JBoss 伺服器上，編輯 Run.bat。 
2. 重新啟動 Web 服務。
3. 在 Microsoft Azure 入口網站中，[加入新的 Application Insights 資源][java]。在您的開發機器中，將 [Java SDK][java] 加入至您的 Web 專案。
4. 取代網頁 <head> 區段中的指令碼。(伺服器端 Include 中可能只有一份。) [從 Azure 的新 Application Insights 資源中的 [快速入門] 分頁取得新指令碼][usage]。如果您的網頁主體中包括遙測呼叫 (例如 logEvent 或 logPage)，請[將它們更新為使用新 API][track]。
8. Web 測試：如果您使用 Web 可用性測試，請[在新入口網站上重新建立測試][availability]，以及其警示。
9. 警示：在 Azure 入口網站中設定[度量的警示][alerts]。



<!--Link references-->

[alerts]: app-insights-alerts.md
[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=62-->