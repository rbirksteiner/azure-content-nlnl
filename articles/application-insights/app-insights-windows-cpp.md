<properties pageTitle="Application Insights for C++ apps" description="Analyze usage and performance of your C++ app with Application Insights." services="application-insights" documentationCenter="cpp" authors="crystk" manager="jakubo""/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="universal" 
    ms.devlang="na" 
    ms.topic="article" 
	ms.date="06/03/2015" 
    ms.author="crystk"/>

# C++ 應用程式的 Application Insights

Visual Studio Application Insights 可讓您監視行動應用程式的使用量、事件及當機。

## 需求

您需要：

* [Microsoft Azure](http://azure.com) 訂用帳戶。您使用 Microsoft 帳戶登入，可能是針對 Windows、XBox Live 或其他 Microsoft 雲端服務具備的帳戶。
* Visual Studio 2015 或更新版本。

## 建立 Application Insights 資源

在 [Azure 入口網站][portal] 中，建立新的 Application Insights 資源。選擇 [Windows Phone] 或 [Windows 市集] 選項。

![按一下 [新增]、[開發人員服務]、[Application Insights]](./media/app-insights-windows-cpp/01-universal.png)

開啟的刀鋒視窗是您要查看您的應用程式效能和使用量資料的位置。若要在下次登入 Azure 時回到此位置，您應該會在開始畫面上發現它的磚。或者按一下 [瀏覽] 以尋找它。

####  取得檢測金鑰的副本。

該金鑰識別資源，您很快就會將它安裝在 SDK 中，以將資源導向資料。

![按一下 [屬性]，選取金鑰，然後按下 CTRL+C](./media/app-insights-windows-cpp/02-props-asp.png)

## <a name="sdk"></a> 在應用程式中安裝 SDK


1. 在 Visual Studio 中，編輯桌面應用程式專案的 NuGet 封裝。

    ![以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 封裝]](./media/app-insights-windows-cpp/03-nuget.png)

2. 安裝 C++ 應用程式適用的 Application Insights SDK。

    ![選取 [**包括發行前版本**]，然後搜尋「Application Insights」](./media/app-insights-windows-cpp/04-nuget.png)

3. 在專案的版本與偵錯設定中：
  - 將 $(SolutionDir)packages\ApplicationInsights-CPP.1.0.0-Beta\src\inc 加入至 [專案屬性] -> [VC + + 目錄] -> [包含目錄]
  - 將$(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<PLATFORM TYPE>\release\AppInsights_Win10-UAP 加入至 [專案屬性] -> [VC + + 目錄] -> [資源庫目錄]

4. 從 $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<PLATFORM TYPE>\release\ApplicationInsights 中，以參考形式將 ApplicationInsights.winmd 加入至專案
5. 從 $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<PLATFORM TYPE>\release\AppInsights_Win10-UAP 加入 AppInsights_Win10-UAP.dll。移至 [屬性]，並將內容設為 [是]。這會將 dll 複製到組建目錄。


#### 將 SDK 更新為未來版本

當新的 [SDK 發行時](app-insights-release-notes-windows-cpp.md)：

* 在 NuGet 封裝管理員中，選取已安裝的 SDK，然後選擇 [動作：升級]。
* 使用新的版本號碼重複安裝步驟。

## 使用 SDK

初始化 SDK，並開始追蹤遙測。

1. 在 App.xaml.h 中： 
  - 新增：`ApplicationInsights::CX::SessionTracking^ m_session;`
2. 在 App.xaml.cpp 中：
  - 新增：`using namespace ApplicationInsights::CX;`

  - 在 App:App() 中
	
     `// this will do automatic session tracking and automatic page view collection` `m_session = ref new ApplicationInsights::CX::SessionTracking();`

  - 一旦建立根框架 (通常是在 App::OnLaunched 結尾)，請初始化 m_session：
	
    ```
    String^ iKey = L"<YOUR INSTRUMENTATION KEY>";
    m_session->Initialize(this, rootFrame, iKey);
	```

3. 若要在應用程式的其他位置使用追蹤，您可以宣告遙測用戶端的執行個體。


```

    using namespace ApplicationInsights::CX;
    TelemetryClient^ tc = ref new TelemetryClient(L"<YOUR INSTRUMENTATION KEY>");
	tc->TrackTrace(L"This is my first trace");
    tc->TrackEvent(L"I'M ON PAGE 1");
    tc->TrackMetric(L"Test Metric", 5.03);
```


## <a name="run"></a>執行專案

執行應用程式以產生遙測。您可以在開發電腦上以偵錯模式執行，或是發佈應用程式，讓使用者自行執行。

## 在 Application Insights 中檢視資料

返回 http://portal.azure.com 並且瀏覽至您的 Application Insights 資源。

按一下 [搜尋] 以開啟[診斷搜尋][diagnostic] - 這是前幾個事件顯示的位置。如果您看不到任何資料，請稍做等待後再按一下 [重新整理]。

![按一下 [診斷搜尋]](./media/app-insights-windows-cpp/21-search.png)

使用您的應用程式時，[概觀] 刀鋒視窗上會顯示資料。

![[概觀] 刀鋒視窗](./media/app-insights-windows-cpp/22-oview.png)

按一下任何圖表以查看詳細資料。例如，當機：

![按一下當機圖表](./media/app-insights-windows-cpp/23-crashes.png)


## <a name="usage"></a>後續步驟

[追蹤應用程式的使用量][track]

[使用 API 傳送自訂事件和度量][api]

[診斷搜尋][diagnostic]

[計量瀏覽器][metrics]

[疑難排解][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=62-->