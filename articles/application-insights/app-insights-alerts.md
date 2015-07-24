<properties 
	pageTitle="在 Application Insights 中設定警示" 
	description="取得有關損毀、例外狀況、度量變更的電子郵件。" 
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
	ms.date="04/17/2015" 
	ms.author="awills"/>
 
# 在 Application Insights 中設定警示

[Visual Studio Application Insights][start] 會警示您應用程式中效能或使用量度量的變更。

Application Insights 會在[多種平台][platforms]上監視即時應用程式，協助您診斷效能問題，以及了解使用模式。

有兩種警示：
 
* **Web 測試**會在您的網站無法在網際網路上使用或回應速度很慢時通知您。[深入了解][availability]。
* **度量警示**會在任何度量超出某些期間的臨界值 (例如失敗計數、記憶體或頁面檢視) 的時候通知您。 

有 [Web 測試的個別頁面][availability]，因此我們將著重於度量警示。

## 度量警示

如果您尚未設定應用程式的 Application Insights，請[立即進行][start]。

若要在度量超出臨界值時取得電子郵件，請從 [計量瀏覽器] 開始，或是從概觀分頁上的 [警示] 規則磚開始。

![在 [警示規則] 分頁中，按一下 [新增警示]。將您的應用程式設定為要測量的資源，提供警示的名稱，然後選擇度量。](./media/app-insights-alerts/01-set-metric.png)

設定其他屬性之前的資源。如果您想要設定效能或使用度量的相關警示，請勿選擇 webtest 資源。

請小心注意系統要求您輸入臨界值時所使用的單位。

*我沒看到 [新增警示] 按鈕。* - 您使用的是組織帳戶嗎？ 如果您有這個應用程式資源的擁有者或參與者存取權，您可以設定警示。查看 [設定] -> [使用者]。[深入了解存取控制][roles]。

## 查看您的警示

警示會透過電子郵件傳送給您。

它們也會出現在作業事件記錄檔中：

![在 [概觀] 分頁靠近底部的位置，按一下 [過去一週的事件]](./media/app-insights-alerts/09-alerts.png)

*這些「事件」是否與遙測事件或自訂事件相關？*

* 否。這些作業事件只是於此應用程式資源發生之事件的記錄。 

## 可用性警示

您可以設定 Web 測試，從世界各個角落測試任何網站。[深入了解][availability]。

## 哪些是好的設定警示？

這取決於您的應用程式。開始時，最好不要設定太多度量。花點時間查看您的應用程式執行時的度量圖表，以了解正常運作時的情形。這會協助您找出改善其效能的方式。然後設定警示，在度量偏離正常區域時通知您。

熱門的警示包括：

* 如果您的應用程式是會顯示在公開的網際網路上的網站或 Web 服務，則 [Web 測試][availability]很重要。它們會告訴您您的網站是否當機，或者回應緩慢 - 即使這是電信業者的問題，而不是您的應用程式的問題。但是它們是綜合測試，因此不會測量使用者的實際經驗。
* [瀏覽器度量][client]，尤其是瀏覽器頁面載入時間，則適合用於 Web 應用程式。如果您的網頁有很多指令碼，您要留意瀏覽器例外狀況。若要取得這些度量和警示，您必須設定[網頁監視][client]。
* Web 應用程式伺服器端的伺服器回應時間和失敗要求。以及設定警示，注意這些度量，以查看高要求率時的差異是否不成比例：可能表示您的應用程式資源不足。



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

 

<!---HONumber=62-->