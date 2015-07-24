<properties 
    pageTitle="iOS 應用程式的 Application Insights" 
    description="使用 Application Insights 分析您 iOS 應用程式的使用情況和效能。" 
    services="application-insights" 
    documentationCenter="ios"
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

# iOS 應用程式的 Application Insights

Visual Studio Application Insights 可讓您監視行動應用程式的使用量、事件及當機。

## 需求

您需要：

* [Microsoft Azure](http://azure.com) 訂用帳戶。您使用 Microsoft 帳戶登入，可能是針對 Windows、XBox Live 或其他 Microsoft 雲端服務具備的帳戶。
* Xcode 6 或更新版本。
* SDK 會在具有 iOS 6.0 或更新版本的裝置上執行。

## 建立 Application Insights 資源

在 [Azure 入口網站][portal] 中，建立新的 Application Insights 資源。挑選 iOS 選項。

![按一下 [新增]、[開發人員服務]、[Application Insights]](./media/app-insights-ios/11-new.png)

開啟的刀鋒視窗是您要查看您的應用程式效能和使用量資料的位置。若要在下次登入 Azure 時回到此位置，您應該會在開始畫面上發現它的磚。或者按一下 [瀏覽] 以尋找它。

## 下載 Application Insights for Mac

(如果您尚未執行這項操作。)

1. 下載 [Application Insights for Mac](http://go.microsoft.com/fwlink/?LinkID=533209)

2. 解壓縮 zip 檔案

3. 按一下應用程式圖示以啟動 Application Insights for Mac

## <a name="signin"></a>登入 Azure

1. 按一下 [登入]

2. 使用您的 Azure 帳戶進行登入

## 在應用程式中安裝 SDK

1. 按一下 [整合] 以啟動 SDK 整合

2. 從清單中選取您的 Xcode 專案，或按一下 [開啟其他] 以尋找您的專案，然後按一下 [整合]

3. 選擇 Application Insights SDK 的資料夾，然後按一下 [安裝]

4. 將顯示的執行指令碼新增至組建階段

    [新增執行指令碼階段](http://hockeyapp.net/help/runscriptbuildphase/)

5. 將遺漏的架構新增至您的 Xcode 專案

6. 將 Application Insights 架構拖曳到 Xcode 專案，然後按 [下一步]

7. 針對您的目標，選取 [將 SDK 整合至目標]

8. 按一下 [建立新元件]，以在 Application Insights 入口網站中建立您的應用程式

9. 選取您的訂用帳戶、資源群組，並輸入元件名稱。在大部分情況下，這應該符合您的應用程式名稱。以 [建立資源] 按鈕進行確認。

10. 請確定已選取正確的元件，然後按 [下一步]

11. 如精靈中所示修改您的原始程式碼，然後按一下 [完成]

12. 在 iOS 模擬器中以 [建置和執行] 啟動您的應用程式

## 插入遙測呼叫

一旦呼叫 `[MSAIApplicationInsights start]`，SDK 就會開始追蹤工作階段、頁面檢視及任何未處理的例外狀況或當機。

您可以新增其他事件，如下所示：

    // Send an event with custom properties and measuremnts data
    [MSAITelemetryManager trackEventWithName:@"Hello World event!"
                                  properties:@{@"Test property 1":@"Some value",
                                             @"Test property 2":@"Some other value"}
                                 measurements:@{@"Test measurement 1":@(4.8),
                                             @"Test measurement 2":@(15.16),
                                             @"Test measurement 3":@(23.42)}];

    // Send a message
    [MSAITelemetryManager trackTraceWithMessage:@"Test message"];

    // Manually send pageviews (note: this will also be done automatically)
    [MSAITelemetryManager trackPageView:@"MyViewController"
                               duration:300
                             properties:@{@"Test measurement 1":@(4.8)}];

    // Send custom metrics
    [MSAITelemetryManager trackMetricWithName:@"Test metric" 
                                        value:42.2];

## 在 Application Insights 中檢視資料

返回 http://portal.azure.com 並且瀏覽至您的 Application Insights 資源。

按一下 [搜尋] 以開啟[診斷搜尋][diagnostic] - 這是前幾個事件顯示的位置。如果您看不到任何資料，請稍做等待後再按一下 [重新整理]。

![按一下 [診斷搜尋]](./media/app-insights-ios/21-search.png)

使用您的應用程式時，[概觀] 刀鋒視窗上會顯示資料。

![[概觀] 刀鋒視窗](./media/app-insights-ios/22-oview.png)

按一下任何圖表以查看詳細資料。例如，當機：

![按一下當機圖表](./media/app-insights-ios/23-crashes.png)
## <a name="usage"></a>後續步驟

[追蹤應用程式的使用量][track]

[診斷搜尋][diagnostic]

[計量瀏覽器][metrics]

[疑難排解][qna]


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=62-->