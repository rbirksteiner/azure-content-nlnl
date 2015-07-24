<properties 
	pageTitle="在 Application Insights 中診斷相依性問題" 
	description="尋找失敗和相依性所造成的遲緩效能" 
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
	ms.date="04/16/2015" 
	ms.author="awills"/>
 
# 在 Application Insights 中診斷相依性問題


「相依性」是由應用程式呼叫的外部元件。這通常是使用 HTTP 呼叫的服務，或資料庫，或檔案系統。在 Visual Studio Application Insights 中，您很容易看到應用程式等待相依性所用的時間，以及相依性呼叫失敗的頻率。

## 可以使用的地方

預設的相依性監視目前適用於：

* 在 IIS 伺服器或在 Azure 上執行的 ASP.NET Web 應用程式和服務

若為其他類型，例如 Java Web 應用程式或裝置應用程式，則您可以使用 TrackDependency API 撰寫自己的監視。

預設的相依性監視目前會回報這些相依性類型的呼叫：

* SQL DATABASE
* ASP.NET Web 和 WCF 服務
* 本機或遠端 HTTP 呼叫
* Azure DocumentDb、資料表、Blob 儲存體和佇列

同樣地，您可以撰寫自己的 SDK 呼叫來監視其他相依性。

## 設定相依性監視

為了取得相依性監視，您必須：

* 在 IIS 伺服器上使用[狀態監視器](app-insights-monitor-performance-live-website-now.md)，並使用它來啟用監視
* 將 [Application Insights 延伸模組](../insights-perf-analytics.md)加入至您的 Azure Web 應用程式或 VM

(若是 Azure VM，您可以從 Azure 控制台安裝延伸模組，或是像在任何電腦上一樣安裝狀態監視器)。

您可以對已部署的 Web 應用程式執行上述步驟。若要取得標準相依性監視，就不必將 Application Insights 加入至您的來源專案。

## 診斷相依性效能問題

若要評估伺服器上的要求效能：

![在 Application Insights 的應用程式 [概觀] 頁面中，按一下 [效能] 磚](./media/app-insights-dependencies/01-performance.png)

向下捲動以查看要求方格：

![含有平均和計數的要求清單](./media/app-insights-dependencies/02-reqs.png)

最上方的要求花了很長的時間。來看看我們是否可以查明時間花費在何處。

按一下該列，以查看個別的要求事件：


![要求發生次數的清單](./media/app-insights-dependencies/03-instances.png)

按一下任一個長時間執行的執行個體，來進一步檢查。

> [AZURE.NOTE]稍微向下捲動，以選擇執行個體。管線中的延遲可能表示最上方的執行個體資料不完整。

向下捲動至與此要求相關的遠端相依性呼叫：

![尋找遠端相依性的呼叫，識別不尋常的持續時間](./media/app-insights-dependencies/04-dependencies.png)

看起來此要求的大部分時間似乎都花費在呼叫本機服務上。

選取該列，以取得詳細資訊：


![點選該遠端相依性來找出問題原因](./media/app-insights-dependencies/05-detail.png)

詳細資料含有足以診斷問題的資訊。



## 失敗

如果有失敗的要求，請按一下圖表。

![按一下失敗要求的圖表](./media/app-insights-dependencies/06-fail.png)

點選要求類型和要求執行個體，以尋找失敗的遠端相依性呼叫。


![按一下要求類型，按一下執行個體以取得同一個執行個體的不同檢視，按一下執行個體以取得例外狀況的詳細資料。](./media/app-insights-dependencies/07-faildetail.png)


<!--Link references-->

<!---HONumber=62-->