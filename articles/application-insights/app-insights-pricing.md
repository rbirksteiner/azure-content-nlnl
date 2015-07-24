<properties 
	pageTitle="管理 Application Insights 的定價和配額" 
	description="選擇您需要的價格方案" 
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
	ms.date="06/09/2015" 
	ms.author="awills"/>

# 管理 Application Insights 的定價和配額

*Application Insights 目前僅供預覽。*

[Visual Studio Application Insights][start] 的[定價][pricing]是根據每個應用程式的資料量而定。我們提供實質的免費層，讓您在該層次中使用大部分的功能，但會有一些限制。

每項 Application Insights 資源的收費方式採個別服務計價，並計入您 Azure 訂用帳戶的帳單。

[請參閱定價機制][pricing]。

## 檢視 Application Insights 資源的配額和價格方案

您可以在應用程式資源的 [設定] 中開啟 [配額 + 定價] 刀鋒視窗。

![依序選擇 [設定]、[配額 + 定價]。](./media/app-insights-pricing/01-pricing.png)

## 配額的運作方式？

* 您的應用程式每月最多可以將指定的遙測資料量上傳至 Application Insights。如需實際數字，請參閱[定價機制][pricing]。 
* 配額取決於您所選擇的定價層。
* 配額的計算是從每個月第一天的午夜起 (UTC)。
* 資料點圖表會顯示您這個月已使用多少配額。
* 配額是以資料點來進行測量。 不論您的程式碼，或其中一個標準遙測模組是否有明確呼叫，單一資料點都是其中一種追蹤方法的呼叫。您在診斷搜尋中看到的每個資料列都是資料點。度量的每個測量單位，例如效能計數器，都是資料點。 
* 工作階段資料不會計入配額。這包括使用者、工作階段、環境和裝置資料的計數。


## 超額部分

如果您的應用程式傳送的配額超過每月上限，您可以：

* 為額外的資料付費：如需詳細資料，請參閱[定價機制][pricing]。您可以預先選擇此選項。免費定價層中沒有此選項。
* 升級定價層。
* 不做任何動作。系統仍會繼續記錄工作階段資料，但診斷搜尋或計量瀏覽器中將不會顯示其他資料。

## 免費試用高階

當您第一次建立新的 Application Insights 資源時，會先從免費層開始。

您可以隨時切換成免費試用 30 天的高階版。如此便可體驗高階層的優點。30 天之後，如果您未明確選擇另一個層次，它會自動還原到您之前所在的任一層次。您可以在試用期間隨時選取想要的層次，但在 30 天的期限結束前，您仍可以免費試用。


## 我使用配額的方式？

[定價] 刀鋒視窗底部的圖表中，會依資料點類型分組，顯示您應用程式的資料點使用量。

![位於 [定價] 刀鋒視窗底部](./media/app-insights-pricing/03-allocation.png)

按一下圖表可取得詳細資料，或在圖表上拖曳，以取得某個時間範圍內的詳細資料。

## 檢視 Azure 訂用帳戶的帳單

Application Insights 費用會加到您的 Azure 帳單中。您可以在 Azure 入口網站中的 [帳務] 區段中，或在 [Azure 計費入口網站](https://account.windowsazure.com/Subscriptions)中，查看 Azure 帳單的詳細資料。

![選擇側邊功能表中的 [帳務]。](./media/app-insights-pricing/02-billing.png)




<!--Link references-->


[start]: app-insights-get-started.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=62-->