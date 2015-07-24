<properties 
	pageTitle="監視任何網站的可用性和回應性" 
	description="在 Application Insights 中設定 Web 測試。如果網站無法使用或回應緩慢，將收到警示。" 
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
	ms.date="04/21/2015" 
	ms.author="awills"/>
 
# 監視任何網站的可用性和回應性

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

開發 Web 應用程式之後，您可以設定 Web 測試來監視其可用性和回應性。Application Insights 會定期傳送來自全球各地的 Web 要求，如果應用程式回應太慢或完全沒有回應，則會警告您。

![Web 測試範例](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

您可以為公用網際網路可存取的任何 HTTP 端點設定 Web 測試。

Web 測試可分為兩種：

* [URL Ping 測試](#set-up-a-url-ping-test)：您可以在 Azure 入口網站中建立的簡單測試。
* [多步驟 Web 測試](#multi-step-web-tests)：您可以在 Visual Studio Ultimate 或 Visual Studio Enterprise 中建立並上傳至入口網站的測試。

這是 Azure Web 應用程式嗎？ 只是[在 Web 應用程式刀鋒視窗中建立 Web 測試][azure-availability]。



## 設定 URL Ping 測試

### <a name="create"></a>1.建立新的資源？

如果您已經為這個應用程式[設定 Application Insights 資源][start]，而且想要在相同位置中查看可用性資料，請略過此步驟。

註冊 [Microsoft Azure](http://azure.com)，移至 [Azure 入口網站](https://portal.azure.com)，然後建立新的 Application Insights 資源。

![New > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

新資源的 [概觀] 刀鋒視窗隨即開啟。若要在 [Azure 入口網站](https://portal.azure.com)中隨時找到此資源，請按一下 [瀏覽]。

### <a name="setup"></a>2.建立 Web 測試

在您的 Application Insights 資源中，尋找 [可用性] 磚。按一下以開啟應用程式的 [Web 測試] 刀鋒視窗，然後新增 Web 測試。

![Fill at least the URL of your website](./media/app-insights-monitor-web-app-availability/13-availability.png)

- **URL** 必須可在公用網際網路上顯示。它可以包含查詢字串 - 例如，您可以訓練一下您的資料庫。如果 URL 解析為重新導向，我們將會跟隨它，最多 10 個重新導向。

- 如果已選取 [啟用重試]，則當測試失敗時，就會在短時間內進行重試。只有在連續三次重試失敗後，才會回報失敗。後續測試則會以一般間隔執行。重試會暫時停止，直到下次成功為止。此規則可個別套用在每個測試位置。

- **測試位置**是我們的伺服器將 Web 要求傳送至您的 URL 的位置。請選擇多個位置，以便區分網站問題與網路問題。您最多可以選取 16 個位置。

- **成功準則**：

    **HTTP 狀態碼**：通常是 200。

    **內容比對**：字串，例如「歡迎！」 我們將測試它出現在每個回應中。必須是單純字串，不含萬用字元。別忘了，如果頁面內容變更，則可能需要更新。


- 依預設，如果持續 15 分鐘重複失敗，就會傳送**警示**給您。但您可以將它變更為更敏感或更不敏感，也可以變更通知的電子郵件地址。

#### 測試更多 URL

您可以隨意為更多 URL 加入測試。例如，除了測試首頁，您也可以測試搜尋的 URL 來確定資料庫在執行中。


### <a name="monitor"></a>3.檢視可用性報告

1-2 分鐘後，在 [可用性] 刀鋒視窗上按一下 [重新整理](它不會自動重新整理)。

![Summary results on the home blade](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

[可用性] 刀鋒視窗中的圖表會結合此應用程式的所有 Web 測試結果。

#### 網頁元件

在測試中會要求影像、樣式表、指令碼及其他靜態元件。

記錄的回應時間是所有元件完成載入所花費的時間。

如果有任何元件無法載入，測試會標示為失敗。

## <a name="failures"></a>如果您看到失敗...

在 [Web 測試] 刀鋒視窗中，向下捲動並按一下您看到的失敗測試。

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/15-webTestList.png)

這會顯示該測試的結果。

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/16-1test.png)

若是從數個位置執行測試，請挑選其中一個結果小於 100% 的位置。

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/17-availViewDetails.png)


向下捲動至 [失敗的測試] 並挑選結果。

按一下結果以在入口網站中評估，並查看失敗的原因。

![Webtest run result](./media/app-insights-monitor-web-app-availability/18-availDetails.png)


或者，您可以下載結果檔案，然後在 Visual Studio 中檢查。


看起來正常，但回報為失敗？ 請檢查所有映像、指令碼、樣式表和頁面載入的任何其他檔案。If any of them fails, the test will be reported as failed, even if the main html page loads OK.



## 多重步驟 Web 測試

您可以監視涉及一連串 URL 的案例。例如，如果您正在監視銷售網站，您可以測試將項目加入購物車正確運作。

若要建立多重步驟測試，您可以使用 Visual Studio 來記錄案例，然後將記錄結果上傳至 Application Insights。Application Insights 將會不時地重新執行案例，並確認回應。

#### 1.記錄案例

使用 Visual Studio Ultimate 來記錄 Web 工作階段。

1. 建立 Web 效能測試專案。

    ![在 Visual Studio 中，從「Web 效能」和「負載測試」範本建立新的專案。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

2. 開啟 .webtest 檔案，並開始記錄。

    ![開啟 .webtest 檔案，然後按一下 [記錄]。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)

3. 執行您想要在測試中模擬的使用者動作：開啟網站、將產品加入購物車等等。然後停止測試。

    ![在 Internet Explorer 中執行 Web 測試錄製器。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    不要讓案例太長。以 100 個步驟和 2 分鐘為限。

4. 在 Visual Studio 中執行測試，以確定可以運作。

    Web 測試執行器會開啟網頁瀏覽器，並重複您已記錄的動作。請確定運作如您所預期。

    ![在 Visual Studio 中，開啟 .webtest 檔案，並按一下 [執行]。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)
 

(請勿在 Web 測試程式碼中插入迴圈。)

#### 2.將 Web 測試上傳至 Application Insights

1. 在 Application Insights 入口網站中，建立新的 Web 測試。

    ![在 [Web 測試] 刀鋒視窗中，選擇 [加入]。](./media/app-insights-monitor-web-app-availability/16-another-test.png)

2. 選取多重步驟測試，並上傳 .webtest 檔案。

    ![選取 [多步驟 Web 測試]。](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

就像在單一 url 測試中一樣，以相同的方式檢視您的測試結果和任何失敗。

失敗的常見原因是在測試執行太長。不可執行超過兩分鐘。

別忘了，頁面的所有資源必須正確載入，測試才能成功 (包括指令碼、樣式表、映像等等)。


### 將時間和隨機數字插入多重步驟測試中

假設您要測試的工具會從外部來源取得與時間相關的資料 (例如股票)。當您記錄 Web 測試時，您必須使用特定的時間，但您將它們設為測試的參數：StartTime 和 EndTime。

![具有參數的 Web 測試。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

當您執行測試時，希望 EndTime 永遠為目前時間，而 StartTime 為 15 分鐘前。

Web 測試外掛程式提供這種作法。

1. 針對您想要的每個變數參數值，各加入一個 Web 測試外掛程式。在 Web 測試工具列中，選擇 [加入 Web 測試外掛程式]。

    ![選擇 [加入 Web 測試外掛程式]，然後選取類型。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    在此範例中，我們將使用兩個日期時間外掛程式執行個體。一個執行個體設定為 "15 minutes ago"，另一個則設定為 "now"。

2. 開啟每個外掛程式的屬性。為屬性命名，然後將它設為使用目前時間。對其中一個，設定 [加入分鐘] = -15。

    ![設定 [名稱]、[使用目前時間] 和 [加入分鐘]。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)

3. 在 Web 測試參數中，使用 {{plug-in name}} 來參考外掛程式名稱。

    ![在測試參數中，使用 {{plug-in name}}。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

現在將您的測試上傳至入口網站。在每次執行測試時，它會使用動態值。

## <a name="edit"></a> 編輯或停用測試

開啟個別測試來編輯或停用。

![Edit or disable a web test](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

當您在服務上進行維護時，您可能會想要停用 Web 測試。

## <a name="video"></a>影片

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>接續步驟

[搜尋診斷記錄][diagnostic]

[疑難排解][qna]




<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-get-started.md

 

<!---HONumber=62-->