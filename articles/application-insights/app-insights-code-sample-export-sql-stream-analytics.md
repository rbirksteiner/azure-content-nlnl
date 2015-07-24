<properties 
	pageTitle="逐步解說：將遙測資料從 Application Insights 匯出至 SQL Database" 
	description="使用連續匯出功能，在 Application Insights 中自行撰寫遙測資料分析的程式碼。" 
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
	ms.date="06/13/2015" 
	ms.author="awills"/>
 
# 逐步解說：使用串流分析從 Application Insights 匯出至 SQL

本文將說明如何使用[連續匯出][export]和 [Azure 串流分析](http://azure.microsoft.com/services/stream-analytics/)，將您的遙測資料從 [Visual Studio Application Insights][start] 移入 Azure SQL Database。

連續匯出會以 JSON 格式將遙測資料移入 Azure 儲存體。我們將使用 Azure 串流分析來剖析 JSON 物件，並在資料庫資料表中建立資料列。

(一般來說，「連續匯出」是對應用程式傳送至 Application Insights 的遙測資料自行進行分析的方式。您可以調整這個程式碼範例，以使用匯出的遙測資料執行其他作業，例如彙總資料，以及將資料發佈至 PowerBI 視覺效果套件)。

我們先假設您已經有想要監視的應用程式。


在此範例中，我們使用頁面檢視資料，但相同的模式可以很輕易地延伸到其他資料類型，例如自訂事件和例外狀況。


## 加入 Application Insights SDK

若要監視您的應用程式，請將 [Application Insights SDK 加入][start]應用程式。不同的平台、IDE 和語言有不同的 SDK 和協助程式工具。您可以監視數種類型的網頁、Java 或 ASP.NET Web 伺服器和行動裝置。所有 SDK 均會將遙測資料傳送至 [Application Insights 入口網站][portal]，您可以在該入口網站中使用我們功能強大的分析與診斷工具，並將資料匯出至儲存體。

開始進行之前：

1. 取得 [Microsoft Azure 帳戶](http://azure.microsoft.com/pricing/)。
2. 在 [Azure 入口網站][portal]中，為您的應用程式加入新的 Application Insights 資源：

    ![依序選擇 [新增]、[開發人員服務]、[Application Insights]，然後選擇應用程式類型](./media/app-insights-code-sample-export-sql-stream-analytics/010-new-asp.png)


    (您的應用程式類型和訂用帳戶可能會和這裡的不同)。
3. 開啟 [快速入門] 來了解如何針對應用程式的類型設定 SDK。

    ![選擇 [快速入門]，然後依照指示進行](./media/app-insights-code-sample-export-sql-stream-analytics/020-quick.png)

    如果沒有列出您的應用程式類型，請查看[使用者入門][start]頁面。

4. 在此範例中，我們正在監視 Web 應用程式，因此可以使用 Visual Studio 中的 Azure Tools 安裝 SDK。告訴 SDK 您的 Application Insights 資源名稱：

    ![在 Visual Studio [方案總管] 中，以滑鼠右鍵按一下專案，然後選擇 [加入 Application Insights]。在 [傳送遙測資料至] 選擇建立新資源，或使用現有資源。](./media/app-insights-code-sample-export-sql-stream-analytics/appinsights-d012-addbrown.png)

5. 發行應用程式，並觀察出現在 Application Insights 資源中的遙測資料。


## 在 Azure 中建立儲存體

連續匯出一律會將資料輸出至 Azure 儲存體帳戶，因此您必須先建立儲存體。

1. 在 [Azure 入口網站][portal]的訂用帳戶中建立儲存體帳戶。

    ![在 Azure 入口網站中，依序選擇 [新增]、[資料]、[儲存體]](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)

2. 建立容器

    ![在新的儲存體中，依序選取 [容器]、[新增]](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)

3. 複製儲存體存取金鑰

    稍後您會需要使用它來設定串流分析服務的輸入。

    ![在儲存體中，依序開啟 [設定]、[金鑰]，然後複製主要存取金鑰](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## 啟動對 Azure 儲存體的連續匯出

1. 在 Azure 入口網站中，瀏覽至您為應用程式建立的 Application Insights 資源。

    ![依序選擇 [瀏覽]、[Application Insights]、您的應用程式](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)

2. 建立連續匯出。

    ![依序選擇 [設定]、[連續匯出]、[新增]](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)


    選取您稍早建立的儲存體帳戶：

    ![設定匯出目的地](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)
    
    設定您想要查看的事件類型：

    ![選擇事件類型](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)

現在請休息一下，讓其他人使用您的應用程式一段時間。遙測資料會送過來，而您會在[計量瀏覽器][metrics]中看到統計圖表，並在[診斷搜尋][diagnostic]中看到個別事件。

此外，資料會匯出至儲存體，您可以在其中檢查內容。例如，在 Visual Studio 中的儲存體瀏覽器：


![在 Visual Studio 中，依序開啟 [Server Browser]、[Azure]、[儲存體]](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)

事件會以 JSON 格式寫入至 Blob 檔案。每個檔案可能會包含一或多個事件。因此我們想要讀取事件資料，並篩選出需要的欄位。我們可以利用資料執行各種作業，但我們現在打算使用串流分析，將資料移至 SQL Database。這麼做可讓您輕鬆執行許多有趣的查詢工作。

## 建立 Azure SQL Database

再次從 [Azure 入口網站][portal]中的訂用帳戶開始，建立要在其中寫入資料的資料庫 (以及新的伺服器，若已經有新伺服器則不必建立)。

![[新增]、[資料]、[SQL]](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)


確定資料庫伺服器允許存取 Azure 服務：


![[瀏覽]、[伺服器]、您的伺服器、[設定]、[防火牆]、[允許存取 Azure]](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## 在 Azure SQL Database 中建立資料表

使用您慣用的管理工具，連接到上一節所建立的資料庫。在本逐步解說中，我們將使用 [SQL Server 管理工具](https://msdn.microsoft.com/ms174173.aspx) (SSMS)。

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

建立新的查詢，然後執行下列 T-SQL：

```SQL

CREATE TABLE [dbo].[PageViewsTable](
	[pageName] [nvarchar](max) NOT NULL,
	[viewCount] [int] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlDataPort] [int] NULL,
	[urlDataprotocol] [nvarchar](50) NULL,
	[urlDataHost] [nvarchar](50) NULL,
	[urlDataBase] [nvarchar](50) NULL,
	[urlDataHashTag] [nvarchar](max) NULL,
	[eventTime] [datetime] NOT NULL,
	[isSynthetic] [nvarchar](50) NULL,
	[deviceId] [nvarchar](50) NULL,
	[deviceType] [nvarchar](50) NULL,
	[os] [nvarchar](50) NULL,
	[osVersion] [nvarchar](50) NULL,
	[locale] [nvarchar](50) NULL,
	[userAgent] [nvarchar](max) NULL,
	[browser] [nvarchar](50) NULL,
	[browserVersion] [nvarchar](50) NULL,
	[screenResolution] [nvarchar](50) NULL,
	[sessionId] [nvarchar](max) NULL,
	[sessionIsFirst] [nvarchar](50) NULL,
	[clientIp] [nvarchar](50) NULL,
	[continent] [nvarchar](50) NULL,
	[country] [nvarchar](50) NULL,
	[province] [nvarchar](50) NULL,
	[city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
	[eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

## 建立 Azure 串流分析執行個體

在[傳統 Azure 入口網站](https://manage.windowsazure.com/)中，選取 Azure 串流分析服務，然後建立新的串流分析工作：


![](./media/app-insights-code-sample-export-sql-stream-analytics/37-create-stream-analytics.png)



![](./media/app-insights-code-sample-export-sql-stream-analytics/38-create-stream-analytics-form.png)

建立新工作後，請展開其詳細資料：

![](./media/app-insights-code-sample-export-sql-stream-analytics/41-sa-job.png)

#### 設定 Blob 位置

將此設定為從您的連續匯出 Blob 接收輸入：

![](./media/app-insights-code-sample-export-sql-stream-analytics/42-sa-wizard1.png)

現在您需要儲存體帳戶的主要存取金鑰 (您已在稍早記下此金鑰)。請將此金鑰設為儲存體帳戶金鑰。

![](./media/app-insights-code-sample-export-sql-stream-analytics/46-sa-wizard2.png)

#### 設定路徑前置詞模式 

![](./media/app-insights-code-sample-export-sql-stream-analytics/47-sa-wizard3.png)

請務必將 [日期格式] 設為 [YYYY-MM-DD] (含連接號)。

[路徑前置詞模式] 會指定串流分析在儲存體中尋找輸入檔案的方式。您必須將它設定為可對應「連續匯出」儲存資料的方式。請設定如下：

    webapplication27_100000000-0000-0000-0000-000000000000/PageViews/{date}/{time}

在此範例中：

* `webapplication27` 是 Application Insights 資源的名稱。 
* `1000...` 是 Application Insights 資源的檢測金鑰。 
* `PageViews` 是我們想要分析的資料類型。可用的類型取決於您在「連續匯出」中設定的篩選。請檢查匯出的資料，以查看其他可用的類型。
* `/{date}/{time}` 是要依字面意思寫入資訊的格式。

若要取得 Application Insights 資源的名稱和 iKey，請在資源的概觀頁面中開啟 Essentials，或開啟 [設定]。

#### 完成初始設定

確認序列化格式：

![確認並關閉精靈](./media/app-insights-code-sample-export-sql-stream-analytics/48-sa-wizard4.png)

關閉精靈，並等候設定完成。

## 設定查詢

開啟 [查詢] 區段：

![在串流分析中選取 [查詢]](./media/app-insights-code-sample-export-sql-stream-analytics/51-query.png)

將預設查詢替換為以下內容：

```SQL

    SELECT flat.ArrayValue.name as pageName
	, flat.ArrayValue.count as viewCount
	, flat.ArrayValue.url as url
	, flat.ArrayValue.urlData.port as urlDataPort
	, flat.ArrayValue.urlData.protocol as urlDataprotocol
	, flat.ArrayValue.urlData.host as urlDataHost
	, flat.ArrayValue.urlData.base as urlDataBase
	, flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOuput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

請注意，前幾個屬性是頁面檢視資料的特定屬性。其他遙測資料類型的匯出會有不同的屬性。

## 設定資料庫的輸出

選取 SQL 做為輸出。

![在串流分析中選取 [輸出]](./media/app-insights-code-sample-export-sql-stream-analytics/53-store.png)

指定 SQL Database。


![填入資料庫的詳細資料](./media/app-insights-code-sample-export-sql-stream-analytics/55-output.png)

關閉精靈，然後等待輸出設定完成的通知。

## 開始處理

從動作列啟動工作：

![在串流分析中按一下 [開始]](./media/app-insights-code-sample-export-sql-stream-analytics/61-start.png)

您可以選擇是否要處理從現在開始的資料，或是從較早的資料開始處理。如果您的「連續匯出」已經執行了一段時間，則後者會是很好用的選項。


![在串流分析中按一下 [開始]](./media/app-insights-code-sample-export-sql-stream-analytics/63-start.png)

經過數分鐘後，即可返回 SQL Server 管理工具，觀看資料的流入。例如，使用如下查詢：

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## 相關文章

* [使用背景工作角色匯出至 SQL](app-insights-code-sample-export-telemetry-sql-database.md)
* [Application Insights 中的連續匯出](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=62-->