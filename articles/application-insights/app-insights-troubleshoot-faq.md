<properties 
	pageTitle="Application Insights 的疑難排解與問題" 
	description="Visual Studio 的 Application Insights 哪個部分不清楚或無法運作嗎？ 試試這裡。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="awills"/>
 
# 疑難排解與問題：ASP.NET 的 Application Insights

## 我是否可以使用 Application Insights 搭配...？

[請參閱「平台」][platforms]

## 它是免費的嗎？

* 是的，如果您選擇免費[定價層](app-insights-pricing.md)。您會獲得大部分的功能和大量資料配額。 
* 您必須提供信用卡資料以向 Microsoft Azure 註冊，但我們不會收取任何費用，除非您使用其他付費 Azure 服務，或明確地升級至付費層。
* 如果您的應用程式傳送的資料超過免費層的每月配額，它會停止記錄。如果發生這種情況，您可以選擇開始付費，或等到月底重設配額。
* 基本的使用情況和工作階段資料不受配額限制。
* 另外也有 30 天的免費試用，在這 30 天內您可以得到免費的高階功能。
* 每個應用程式資源都有個別的配額，其定價層的設定與其他任何資源無關。

#### 付費能得到什麼？

* 較大的[每月資料配額](http://azure.microsoft.com/pricing/details/application-insights/)。
* 能夠選擇針對超額部分付費，以繼續收集超過每月配額的資料。如果您的資料超過配額，我們將會以每 Mb 來收費。
* [連續匯出](app-insights-export-telemetry.md)

## 新增 SDK

#### <a name="q01"></a>我在 Visual Studio 中沒有看到任何選項可將 Application Insights 加入至我的專案

+ 請確定您有 [Visual Studio 2013 Update 3 或更新版本](http://go.microsoft.com/fwlink/?LinkId=397827)。這會隨 Application Insights 工具預先安裝。
+ 雖然這些工具不支援所有類型的應用程式，您仍然可將 Application Insights SDK 手動加入至您的專案。使用[此程序][windows]。 


#### <a name="q02"></a>我已建立新的 Web 專案，但加入 Application Insights 時失敗。

如果與 Application Insights 入口網站通訊失敗，或您的帳戶有問題，就可能發生此情形。

+ 請檢查您為正確的 Azure 帳戶提供登入認證。Microsoft Azure 認證 (在 [新增專案] 對話方塊中) 與 Visual Studio Online 認證 (在 Visual Studio 右上方) 可能不同。
+ 請稍候，再[將 Application Insights 加入至現有的專案][start]。
+ 移至 Microsoft Azure 帳戶設定來檢查限制。檢查您是否可以手動加入 Application Insights 應用程式。

#### <a name="emptykey"></a>我收到「檢測金鑰不能是空白」的錯誤

可能是您在安裝 Application Insights 或記錄配接器時發生問題。

在 [方案總管] 中，以滑鼠右鍵按一下 `ApplicationInsights.config`，然後選擇 [**更新 Application Insights**]。將會出現對話方塊邀請您登入 Azure，並建立 Application Insights 資源或重複使用現有的資源。


#### <a name="q14"></a>Application Insights 在我的專案中修改什麼？

詳細資料視專案類型而定。若是 Web 應用程式：


+ 在您的專案中加入這些檔案：

 + ApplicationInsights.config。
 + ai.js


+ 安裝這些 NuGet 套件：

 -  *Application Insights API* - 核心 API

 -  *Application Insights API for Web Applications* - 用來從伺服器傳送遙測

 -  *Application Insights API for JavaScript Applications* - 用來從用戶端傳送遙測

    套件包含這些組件：

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ 將項目插入至：

 - Web.config

 - packages.config

+ (僅限新專案 - 如果您[將 Application Insights 加入至現有的專案][start]，則必須手動執行。) 將程式碼片段插入至用戶端和伺服器程式碼中，以使用 Application Insights 資源識別碼將這些片段初始化。例如，在 MVC 應用程式中，程式碼會插入至主版頁面 Views/Shared/_Layout.cshtml

####<a name="NuGetBuild"></a> 我的組建伺服器收到「缺少 NuGet 封裝」的訊息，但在我的開發電腦上，一切組建均正常

請參閱 [NuGet 封裝還原](http://docs.nuget.org/Consume/Package-Restore) (英文) 和[自動封裝還原](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore) (英文)。

####<a name="FailUpdate"></a> 更新至 0.17 或以上版本的 NuGet 封裝後，當我嘗試建置時，我收到「電腦缺少專案參考 NuGet 封裝」的訊息。

更新至 0.17 或以上版本的 NuGet 封裝後，如果顯示上述錯誤，您需要編輯 proj 檔案並移除剩餘的 BCL 目標。

作法：

1. 在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [上傳專案]。
2. 再次以滑鼠右鍵按一下專案，並選擇 [*編輯 yourProject.csproj*] 
3. 移至專案檔案最下方，並移除類似以下所示的 BCL 目標：``` <Import Project="..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets" Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" />
	  
	  <Target Name="EnsureBclBuildImported" BeforeTargets="BeforeBuild" Condition="'$(BclBuildImported)' == ''">
	  
	    <Error Condition="!Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=317567." HelpKeyword="BCLBUILD2001" />
	    
	    <Error Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="The build restored NuGet packages. Build the project again to include these packages in the build. For more information, see http://go.microsoft.com/fwlink/?LinkID=317568." HelpKeyword="BCLBUILD2002" />
	    
	</Target> ```
4. 儲存檔案。
5. 以滑鼠右鍵按一下專案，然後選擇 [*重新載入 yourProject.csproj*]

## 如何從舊版 SDK 升級？

請參閱您的應用程式類型所適用之 SDK 的[版本資訊](app-insights-release-notes.md)。


## 沒有資料

#### <a name="q03"></a>我已成功加入 Application Insights 並執行我的應用程式，但在入口網站中從未看到資料。

+ 在 [概觀] 頁面中，按一下 [搜尋] 磚以開啟 [診斷搜尋]。這裡會先顯示資料。
+ 按一下 [重新整理] 按鈕。在目前版本中，刀鋒視窗的內容不會自動重新整理。
+ 在 Microsoft Azure 開始面板中，查看服務狀態對應。如果看到一些警示指示，請等待它們恢復 [正常]，然後關閉再重新開啟 Application Insights 應用程式刀鋒視窗。
+ 也請查閱[我們的狀態部落格](http://blogs.msdn.com/b/applicationinsights-status/archive/2015/04/14/data-latency-and-data-access-issue-with-data-storage-service-4-14-investigating.aspx)。
+ 在防火牆中，您可能必須開啟 TCP 連接埠 80 和 443，以允許連出流量送往 dc.services.visualstudio.com 和 f5.services.visualstudio.com。
+ 如果您必須使用 Proxy 在貴公司網路之外傳送內容，請設定 Web.config 中的 [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx)
+ Windows Server 2008：確定您已安裝以下更新：[KB2468871](https://support.microsoft.com/kb/2468871)、[KB2533523](https://support.microsoft.com/kb/2533523)、[KB2600217](https://support.microsoft.com/kb/2600217)。

#### <a name="q04"></a>我在網站的流量分析下沒有看到資料

+ 資料來自網頁中的指令碼。如果您將 Application Insights 加入至現有的 Web 專案，則[您必須手動加入指令碼][start]。
+ 確定 Internet Explorer 並非以相容性模式顯示您的網站。
+ 若要確認資料確實傳送至 dc.services.visualstudio.com，請使用瀏覽器的偵錯功能 (部分瀏覽器是使用 F12 鍵，然後選擇 [網路])。

#### <a name="q08"></a>我可以使用 Application Insights 來監視內部網路 Web 伺服器嗎？

是，如果您的伺服器可以傳送資料至公用網際網路，您可以監視健全狀況和使用量。

但如果您想對服務執行 Web 測試，則必須在連接埠 80 上從公用網際網路存取。

#### 我可以監視無法存取公用網際網路的內部網路 Web 伺服器嗎？

您必須安排可以將 https POST 呼叫轉送至 dc.services.visualstudio.com 的 Proxy

#### 我曾經看到資料，但是已停止

* 檢查[狀態部落格](http://blogs.msdn.com/b/applicationinsights-status/)。
* 您有達到資料點的每月配額嗎？ 開啟 [設定/配額和定價] 即可查看。若有達到配額，您可以升級您的方案，或付費取得額外容量。請參閱[定價機制](http://azure.microsoft.com/pricing/details/application-insights/)。



## 入口網站

#### <a name="q05"></a>我正在查看 Microsoft Azure 預覽開始面板。我如何在 Application Insights 中尋找我的資料？

下列其中一種方法：

* 選擇 [瀏覽]、[Application Insights]、您的專案名稱。如果您在其中沒有任何專案，則需要[在 Visual Studio 中將 Application Insights 加入至 Web 專案][start]。

* 在 Visual Studio [方案總管] 中，以滑鼠右鍵按一下 Web 專案，然後選擇 [Open Application Insights Portal]。


#### <a name="update"></a>如何變更我的專案將資料傳送到哪一個 Azure 資源？

在 [方案總管] 中，以滑鼠右鍵按一下 `ApplicationInsights.config`，然後選擇 [**更新 Application Insights**]。您可以將資料傳送至 Azure 中的現有資源或新資源。更新精靈會變更 ApplicationInsights.config 中的檢測機碼，這決定伺服器 SDK 將您的資料送往何處。除非您取消選取 [全部更新]，否則它也會變更機碼出現在您網頁中的位置。


#### <a name="q06"></a>在 Microsoft Azure 預覽主畫面上，該對應會顯示我的應用程式狀態嗎？

不會！ 它是顯示 Azure 服務的狀態。若要查看 Web 測試結果，請選擇 [瀏覽] > [Application Insights] > (您的應用程式)，然後查看 Web 測試結果。


#### <a name="q07"></a>當我使用 [將 Application Insights 加入至我的應用程式] 並開啟 Application Insights 入口網站時，實際畫面與螢幕擷取畫面完全不同。

您可能是使用[舊版的 Application Insights SDK](http://msdn.microsoft.com/library/dn793604.aspx)，這會連結至 Visual Studio Online 版本。

您所查看的說明頁面是關於[Application Insights for Microsoft Azure Preview][start]，這在 Visual Studio 2013 Update 3 中已開啟。

#### <a name="data"></a>資料會保留在入口網站多久？ 是否安全？

請參閱[資料保留和隱私權][data]。

## 記錄

#### <a name="post"></a>如何在診斷搜尋中查看 POST 資料？

我們不會自動記錄 POST 資料，但您可以使用 TrackTrace 呼叫：將資料放置到訊息參數中。相較於字串屬性的限制，此呼叫可容許較長的大小，不過您無法篩選。

## Security

#### 保留在入口網站上的資料安全嗎？ 會保留多久？

請參閱[資料保留和隱私權][data]。


## <a name="q17"></a>我是否已啟用 Application Insights 中的所有項目？

<table border="1">
<tr><th>您應該會看到</th><th>如何取得</th><th>取得原因</th></tr>
<tr><td>可用性圖表</td><td><a href="../app-insights-monitor-web-app-availability/">Web 測試</a></td><td>知道您的 Web 應用程式已啟動</td></tr>
<tr><td>伺服器應用程式效能：回應時間...
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">將 Application Insights 加入至專案</a><br/>或 <br/><a href="../app-insights-monitor-performance-live-website-now/">在伺服器上安裝 AI 狀態監視器</a></td><td>偵測效能問題</td></tr>
<tr><td>相依性遙測</td><td><a href="../app-insights-monitor-performance-live-website-now/">在伺服器上安裝 AI 狀態監視器</a></td><td>診斷資料庫或其他外部元件的問題</td></tr>
<tr><td>取得例外狀況的堆疊追蹤</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">在程式碼中插入 TrackException 呼叫</a> (但部分會自動報告)</td><td>偵測並診斷例外狀況</td></tr>
<tr><td>搜尋記錄追蹤</td><td><a href="../app-insights-search-diagnostic-logs/">加入記錄配接器</a></td><td>診斷例外狀況、效能問題</td></tr>
<tr><td>用戶端使用基本概念：頁面檢視、工作階段...</td><td><a href="../app-insights-start-monitoring-app-health-usage/#webclient">網頁中的 JavaScript 初始設定式</a></td><td>流量分析</td></tr>
<tr><td>用戶端自訂度量</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">追蹤網頁中的呼叫</a></td><td>增強使用者經驗</td></tr>
<tr><td>伺服器自訂度量</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">追蹤伺服器程式碼中的呼叫</a></td><td>商業智慧</td></tr>
</table>

如果您的 Web 服務在 Azure VM 中執行，您也可以從該處[取得診斷][azurediagnostic]。

## 自動化

您可以[撰寫 PowerShell 指令碼](app-insights-powershell-script-create-resource.md)來建立 Application Insights 資源。


<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-get-started.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=62-->