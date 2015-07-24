<properties 
	pageTitle="在 Java Web 應用程式中追蹤 HTTP 要求" 
	description="Application Insights 可讓您測量 Web Java Web 應用程式的效能" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="awills"/>
 
# 在 Java Web 應用程式中追蹤 HTTP 要求

如果執行 Java Web 應用程式，您可以檢視傳送至您的應用程式的 HTTP 要求相關資訊，例如要求的來源、失敗的要求和回應時間，全都在 Application Insights 入口網站中進行。

安裝 [Java 適用的 Application Insights SDK][java]，如果您還未完成的話。


## 將程式庫加入至專案

*選擇適合您的專案的方式。*

### 如果您使用 Maven...

如果您的專案已設定為使用 Maven 來建置，請將下列程式碼片段合併至 pom.xml 檔案。

然後重新整理專案相依性，以下載程式庫。

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <version>[0.9,)</version>
      </dependency>
    </dependencies>

### 如果您使用 Gradle...

如果您的專案已設定為使用 Gradle 來建置，請將下列程式碼片段合併至 build.gradle 檔案。

然後重新整理專案相依性，以下載程式庫。

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '0.9.+'
    }

## 將 Application Insights HTTP 篩選器加入至專案

在專案中找到並開啟 web.xml 檔案，並合併 web-app 節點下的下列程式碼片段，在其中您可以設定應用程式篩選器。

為獲得最準確的結果，應該在其他所有篩選器之前先對應此篩選器。

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

## 將 HTTP 模組加入至專案

在專案中找到並開啟 ApplicationInsights.xml 檔案，並合併 <TelemetryModules> 元素下的下列程式碼片段。

如果此檔案中沒有 <TelemetryModules> 元素，請在 <ApplicationInsights> 元素下加入一個。

    <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
    </TelemetryModules>

## 加入遙測初始設定式以用於事件相互關聯

利用事件相互關聯，您可以將 HTTP 要求與要求處理期間所傳送的所有遙測事件產生關聯，方法是使用附加到每一個遙測事件的作業識別碼屬性。這可讓您瀏覽 HTTP 要求以及透過它呼叫的所有事件，並且有助於診斷和疑難排解問題。

在專案中找到並開啟 ApplicationInsights.xml 檔案，並合併 <TelemetryInitializers> 元素下的下列程式碼片段。

如果此檔案中沒有 < TelemetryInitializers> 元素，請在 <ApplicationInsights> 元素下加入一個。

    <TelemetryInitializers>
     <Add  type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
     <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
     <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
     <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
     <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
    </TelemetryInitializers>


## 在 Application Insights 中檢視要求資訊

執行您的應用程式。

返回 Microsoft Azure 中的 Application Insights 資源。

[概觀] 分頁上會顯示 HTTP 要求資料。(如果沒有出現，請稍等片刻，然後按一下 [重新整理]。)

![](./media/app-insights-java-track-http-requests/5-results.png)
 

逐一點選任何圖表以查看更詳細的度量。

![](./media/app-insights-java-track-http-requests/6-barchart.png)


[深入了解度量。][metrics]

 

而檢視要求的屬性時，您可以查看與它關聯的遙測事件，例如要求和例外狀況。
 
![](./media/app-insights-java-track-http-requests/7-instance.png)




## 後續步驟

* [搜尋事件和記錄][diagnostic]以協助診斷問題。
* [擷取 Java、Log4J 或 Logback 追蹤][javalogs]



<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md

 

<!---HONumber=62-->