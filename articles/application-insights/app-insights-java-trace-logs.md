<properties 
	pageTitle="在 Application Insights 中探索 Java 追蹤記錄" 
	description="在 Application Insights 中搜尋 Log4J 或 Logback 追蹤" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="awills"/>

# 在 Application Insights 中探索 Java 追蹤記錄

如果您使用 Logback 或 Log4J (v1.2 或 v2.0) 進行追蹤，您可以將追蹤記錄自動傳送到 Application Insights，您可以在其中探索及搜尋記錄。

安裝 [Java 適用的 Application Insights SDK][java]，如果您還未完成的話。


## 將記錄程式庫加入至專案

*選擇適合您的專案的方式。*

#### 如果您使用 Maven...

如果您的專案已設定為使用 Maven 來建置，請將下列其中一個程式碼片段合併至 pom.xml 檔案。

然後重新整理專案相依性，以下載程式庫。

*Logback*

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[0.9,)</version>
       </dependency>
    </dependencies>

*Log4J v2.0*

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[0.9,)</version>
       </dependency>
    </dependencies>

*Log4J v1.2*

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[0.9,)</version>
       </dependency>
    </dependencies>

#### 如果您使用 Gradle...

如果您的專案已設定為使用 Gradle 來建置，請將下列其中一行加入至 build.gradle 檔案中的 `dependencies` 群組：

然後重新整理專案相依性，以下載程式庫。

**Logback**

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '0.9.+'

**Log4J v2.0**

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '0.9.+'

**Log4J v1.2**

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '0.9.+'

#### 否則...

下載並擷取適當的附加器，然後加入適當的程式庫至您的專案：


記錄器 | 下載 | 程式庫
----|----|----
Logback|[具有 Logback 附加器的 SDK](http://dl.msopentech.com/applicationinsights/javabin/logbackAppender.zip)|applicationinsights-logging-logback
Log4J v2.0|[具有 Log4J v2 附加器的 SDK](http://dl.msopentech.com/applicationinsights/javabin/log4j2Appender.zip)|applicationinsights-logging-log4j2 
Log4j v1.2|[具有 Log4J v1.2 附加器的 SDK](http://dl.msopentech.com/applicationinsights/javabin/log4j1_2Appender.zip)|applicationinsights-logging-log4j1_2 



## 將附加器加入至記錄架構

若要開始進行追蹤，請將相關的程式碼片段合併到 Log4J 或 Logback 組態檔案：

*Logback*

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>


*Log4J v2.0*

    
    <Appenders>
      <ApplicationInsightsAppender name="aiAppender" />
    </Appenders>
    <Loggers>
      <Root level="trace">
        <AppenderRef ref="aiAppender"/>
      </Root>
    </Loggers>


*Log4J v1.2*

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>

Application Insights 附加器可由任何設定的記錄器參考，而不一定是根記錄器 (如以上程式碼範例所示)。

## 在 Application Insights 入口網站中探索您的追蹤

既然已將專案設定為傳送追蹤至 Application Insights，您可以在 Application Insights 入口網站的 [[診斷搜尋][diagnostic]] 分頁中檢視及搜尋這些追蹤。

![在 Application Insights 入口網站中，開啟診斷搜尋](./media/app-insights-java-trace-logs/10-diagnostics.png)

## 後續步驟

[診斷搜尋][diagnostic]

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md

 

<!---HONumber=62-->