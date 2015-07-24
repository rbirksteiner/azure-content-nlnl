<properties 
	pageTitle="在 Eclipse 中利用 Java 開始使用 Application Insights" 
	description="使用 Eclipse 外掛程式來加入效能和使用量計數器監視到您使用 Application Insights 的 Java 網站" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/03/2015" 
	ms.author="awills"/>
 
# 在 Eclipse 中利用 Java 開始使用 Application Insights

Application Insights SDK 會透過 Java Web 應用程式傳送遙測，使得您可以分析使用量和效能。Application Insights 適用的 Eclipse 外掛程式會自動在您的專案中安裝 SDK，使得您可以取得內建的遙測，加上可以用來編寫自訂遙測的 API。


## 必要條件

目前外掛程式可用於 Eclipse 中的動態網站專案。([將 Application Insights 加入至其他類型的 Java 專案][java]。)

您需要：

* Oracle JRE 1.6 或更新版本
* [Microsoft Azure](http://azure.microsoft.com/) 訂用帳戶。(您可以從[免費試用](http://azure.microsoft.com/pricing/free-trial/)開始。)
* [Eclipse IDE for Java EE Developers](http://www.eclipse.org/downloads/)、Indigo 或更新版本。
* Windows 7 或更新版本，或 Windows Server 2008 或更新版本

## 在 Eclipse 上安裝 SDK (一次)

您只需在每一部機器上執行一次此動作。此步驟會安裝工具組，然後工具組會將 SDK 加入到每個動態網站專案。

1. 在 Eclipse 中，按一下 [說明]，然後按一下 [安裝新軟體]。

    ![[說明] -> [安裝新軟體]](./media/app-insights-java-eclipse/0-plugin.png)

2. SDK 位在 Azure 工具組的 http://dl.msopentech.com/eclipse。
3. 取消勾選 **[連絡所有更新網站...]**

    ![針對 Application Insights SDK，請清除 [連絡所有更新網站]](./media/app-insights-java-eclipse/1-plugin.png)

對每個 Java 專案遵循其餘的步驟。

## 取得 Application Insights 檢測金鑰

您的使用量和效能分析將顯示在 Azure Web 入口網站的 Azure 資源中。在此步驟中，您會為您的應用程式設定 Azure 資源。

1. 登入 [Microsoft Azure 入口網站](https://portal.azure.com)。(您將需要 [Azure 訂用帳戶](http://azure.microsoft.com/)。)
2. 建立新 Application Insights 資源

    ![按一下 + 並選擇 [Application Insights]](./media/app-insights-java-eclipse/01-create.png)
3. 將應用程式類型設定為 Java Web 應用程式。

    ![填寫名稱，選擇 [Java Web 應用程式]，然後按一下 [建立]](./media/app-insights-java-eclipse/02-create.png)
4. 尋找新資源的檢測金鑰。您將需要將此資訊貼上到 Eclipse 中的專案。

    ![在新資源概觀中，按一下 [屬性] 並複製檢測金鑰](./media/app-insights-java-eclipse/03-key.png)

## 將 SDK 加入至您的 Java 專案

1. 從 Web 專案的內容功能表將 Application Insights 加入。

    ![在新資源概觀中，按一下 [屬性] 並複製檢測金鑰](./media/app-insights-java-eclipse/4-addai.png)
2. 將您從 Azure 入口網站取得的檢測金鑰貼上。

    ![在新資源概觀中，按一下 [屬性] 並複製檢測金鑰](./media/app-insights-java-eclipse/5-config.png)


金鑰會隨著遙測的每個項目傳送，並告知 Application Insights 在您的資源中顯示它。

## 執行應用程式並查看度量

執行您的應用程式。

返回 Microsoft Azure 中的 Application Insights 資源。

[概觀] 分頁上會顯示 HTTP 要求資料。(如果沒有出現，請稍等片刻，然後按一下 [重新整理]。)

![伺服器回應、要求計數和失敗](./media/app-insights-java-eclipse/5-results.png)
 

逐一點選任何圖表以查看更詳細的度量。

![依名稱的要求計數](./media/app-insights-java-eclipse/6-barchart.png)


[深入了解度量。][metrics]

 

而檢視要求的屬性時，您可以查看與它關聯的遙測事件，例如要求和例外狀況。
 
![此要求的所有追蹤](./media/app-insights-java-eclipse/7-instance.png)


## 用戶端遙測

從 [概觀] 分頁上的 [快速入門] 磚，您可取得指令碼以加入至您的網頁。

頁面檢視、使用者和工作階段度量將出現在 [概觀] 分頁上：

![工作階段、使用者和頁面檢視](./media/app-insights-java-eclipse/appinsights-47usage-2.png)

[深入了解設定用戶端遙測。][usage]

## 可用性 Web 測試

Application Insights 可讓您定期測試網站，以檢查網站運作中且正常回應。在 [概觀] 分頁上逐一點選空白 Web 測試圖表，並提供您的公用 URL。

您將取得回應時間的圖表，以及若網站關閉還會取得電子郵件通知。

![Web 測試範例](./media/app-insights-java-eclipse/appinsights-10webtestresult.png)

[深入了解 Web 測試的可用性。][availability]

## 診斷記錄

如果您使用 Logback 或 Log4J (v1.2 或 v2.0) 進行追蹤，您可以將追蹤記錄自動傳送到 Application Insights，您可以在其中探索及搜尋記錄。

[深入了解診斷記錄][javalogs]

## 自訂遙測 

在 Java Web 應用程式中插入幾行程式碼，以了解使用者對它進行的動作或協助診斷問題。

您可以在網頁 JavaScript 和伺服器端 Java 中插入程式碼。

[貼上了解自訂遙測][track]



## 後續步驟

#### 偵測並診斷問題

* [加入 Web 用戶端遙測][usage]，從 Web 用戶端取得效能遙測。
* [設定 Web 測試][availability]，以確認應用程式處於線上狀態且能夠回應。
* [搜尋事件和記錄][diagnostic]以協助診斷問題。
* [擷取 Java、Log4J 或 Logback 追蹤][javalogs]

#### 追蹤流量

* [加入 Web 用戶端遙測][usage]，以監視頁面檢視和基本使用者度量。
* 在用戶端、伺服器或兩者，[追蹤自訂事件和度量][track]，以深入了解應用程式的使用情況。


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=62-->