<properties 
	pageTitle="Application Insights 的版本資訊" 
	description="最新的更新。" 
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
	ms.date="06/18/2015" 
	ms.author="awills"/>
 
# Java 適用的 Application Insights SDK 的版本資訊

[Application Insights SDK for Java](app-insights-java-get-started.md) 傳送有關您作用中的應用程式遙測到 [Application Insights](http://azure.microsoft.com/services/application-insights/)，您可以在此分析它的使用情況與效能。

#### 若要在應用程式中安裝 SDK

請參閱[開始使用 SDK for Java](app-insights-java-get-started.md)。

#### 若要升級至最新的 SDK 

升級之後，必須將您對 ApplicationInsights.xml 所做的任何自訂合併回來。請取得一份複本來與新的檔案比較。

如果您使用 Maven 或 Gradle

1. 如果您已在 pom.xml 或 build.gradle 中指定特定的版本號碼，請更新版本號碼。
2. 請重新整理專案的相依項目。

否則

* 請下載最新版的 [Azure Libraries for Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html) 取代舊版本。 
 
比較舊的和新的 ApplicationInsights.xml。您看到的變更許多是因為我們新增與移除了模組。恢復您所做的任何自訂。

## 0.9.6 版
- 讓 Java SDK 與 Servlet 2.5 版與 HttpClient pre-4.3 版相容
- 新增支援 Java EE 攔截器
- 從 Logback 附加器移除多餘的相依性

## 0.9.5 版  

- 修正自訂事件因 Cookie 剖析錯誤而沒有與使用者/工作階段相互關聯的問題。  
- 改善解析 ApplicationInsights.xml 組態檔位置的邏輯。
- 移除工作階段和使用者的追蹤 (僅能由用戶端 SDK 完成)。

## 0.9.4 版

- 支援從 32 位元 Windows 電腦收集效能計數器。
- 支援使用新的 ```trackDependency``` 方法 API 手動追蹤相依性。
- 能夠藉由將 ```SyntheticSource``` 屬性加入至報告項目，將遙測項目標記為綜合。
 

<!---HONumber=62-->