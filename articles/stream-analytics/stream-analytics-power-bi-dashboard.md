<properties 
	pageTitle="串流分析上的 Power BI 儀表板 | Microsoft Azure" 
	description="使用即時串流 Power BI 儀表板來收集商業智慧及分析來自串流分析工作的大量資料。" 
	keywords="business intelligence tools,power bi,streaming data,power bi dashboard"	
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="05/12/2015" 
	ms.author="jeffstok"/>
	
# Azure 串流分析與 Power BI：用於串流資料即時分析的即時儀表板

Azure 串流分析可讓您使用其中一種業界頂尖智慧型工具 Microsoft Power BI。了解如何使用 Azure 串流分析來分析大量串流資料，以及取得即時 Power BI 儀表板中的深度資訊。

使用 [Microsoft Power BI](https://powerbi.com/) 來快速建置即時儀表板。[觀賞案例說明影片](https://www.youtube.com/watch?v=SGUpT-a99MA)。

在此文章中了解如何使用 Power BI 做為 Azure 串流分析工作的輸出，來建立您自己的自訂商業智慧型工具。

> [AZURE.NOTE]Power BI 輸出是 Azure 串流分析的預覽功能。

##必要條件

* 使用組織識別碼的 Microsoft Azure 帳戶 (限 Power BI 搭配組織識別碼。組織識別碼是公司或企業的電子郵件地址，例如 xyz@mycompany.com。xyz@hotmail.com 此類的個人電子郵件，則不是組織識別碼。您可以在[這裡](https://msdn.microsoft.com/subscriptions/dn531048.aspx)詳細了解組織識別碼，而且也可以從[這裡](http://go.microsoft.com/fwlink/?linkid=331007&clcid=0x409)下載常見問題集)。
* 串流分析工作從中使用串流資料的輸入。串流分析可接收來自 Azure 事件中樞或 Azure Blob 儲存體的輸入。  

##建立 Azure 串流分析工作

在 [Azure 入口網站][](https://manage.windowsazure.com) 中，依序按一下 [新增]、[資料服務]、[Stream Analytics]、[快速建立]。

指定下列值，然後按一下 [建立 Stream Analytics 工作]：

* **工作名稱** - 輸入工作名稱。例如 **DeviceTemperatures**。
* **區域** - 選取要執行此工作的區域。請考慮將工作和事件中樞放在相同的區域以確保更好的效能，以及在區域之間傳輸資料時無須付費。
* **儲存體帳戶** - 選擇您為在此區域內執行的所有 Stream Analytics 工作儲存監視資料時所要使用的儲存體帳戶。您可以選擇現有的儲存體帳戶，或建立新帳戶。

按一下左窗格中的 [Stream Analytics]，以列出 Stream Analytics 工作。

![graphic1][graphic1]

> [AZURE.TIP]新工作會以 [未啟動] 的狀態列出。請注意，頁面底部的 [啟動] 按鈕會停用。這是正常行為，因為您必須先設定工作輸入、輸出、查詢等項目，才能啟動工作。

##指定工作輸入

在本教學課程中，我們假設您搭配 JSON 序列化與 utf-8 編碼使用 EventHub 做為輸入。

* 按一下工作名稱。
* 按一下頁面頂端的 [輸入]，然後按一下 [新增輸入]。開啟的對話方塊會逐步引導您完成設定輸入。
*	選取 [資料流]，然後按一下右鍵。
*	選取 [事件中樞]，然後按一下右鍵。
*	在第三頁上輸入或選取下列值：
  *	**輸入別名** - 輸入此工作輸入的易記名稱。請注意，此名稱將用於後續的查詢。
  * **事件中樞** - 如果您建立的事件中樞與 Stream Analytics 工作位於相同的訂閱中，請選取事件中樞所在的命名空間。
*	如果事件中樞位於不同的訂閱中，請選取 [使用其他訂閱中的事件中樞]，並手動輸入 [服務匯流排命名空間]、[事件中樞名稱]、[事件中樞原則名稱]、[事件中樞原則索引鍵] 和 [事件中樞資料分割計數] 的資訊。

> [AZURE.NOTE]此範例會使用預設的 16 個資料分割數目。

* **事件中樞名稱** - 選取 Azure 事件中樞的名稱。
* **事件中樞原則名稱** - 為目前使用中的 eventhub 選取事件中樞原則。請確定此原則具有管理權限。
*	**事件中樞用戶群組** – 您可以保留空白，或指定事件中樞的用戶群組。請注意，事件中樞的每個用戶群組一次只能有 5 個讀取器。因此，請為您的工作選擇正確的用戶群組。如果您將欄位保留空白，它會使用預設的用戶群組。

*	按一下向右按鈕。
*	指定下列值：
  *	**事件序列化程式格式** - JSON
  *	**編碼** - UTF8
*	按一下核取按鈕以新增此來源，並確認 Stream Analytics 可成功連接到事件中樞。

##新增 Power BI 輸出

1.  按一下頁面頂端的 [輸出]，然後按一下 [新增輸出]。您會看到 Power BI 被列為輸出選項。

![graphic2][graphic2]

> [AZURE.NOTE]Power BI 輸出僅適用於使用組織識別碼的 Azure 帳戶。如果您的 Azure 帳戶不使用組織識別碼 (例如，即時識別碼/個人 Microsoft 帳戶)，就不會看到 Power BI 輸出選項。

2.  選取 [Power BI]，然後按一下右鍵。
3.  您會看到類似下列畫面：

![graphic3][graphic3]

4.  在這個步驟中，必須小心使用您用於串流分析工作的相同組織識別碼。目前，Power BI 輸出必須和串流分析工作使用相同的組織識別碼。如果 Power BI 帳戶已經使用相同的組織識別碼，請選取 [立即授權]。如果沒有，請在申請 Power BI 時，選擇 [立即註冊]，然後使用和 Azure 帳戶相同的組織識別碼。[下面的部落格文章為您逐步解說如何註冊 Power BI](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx)。
5.  接下來您會看到一個畫面，如下所示：

![graphic4][graphic4]

提供值，如下所示：

* **輸出別名** – 您可以填寫任何容易記住的輸出別名。如果您決定讓工作擁有多個輸出，填寫輸出別名會非常有幫助。在這個情況下，您必須參考此查詢中的輸出。比方說，我們要使用的輸出別名值 = "OutPbi"。
* **資料集名稱** - 提供一個 Power BI 輸出應該要有的資料集名稱。例如，"pbidemo"。
*	**資料表名稱** - 提供 Power BI 輸出資料集的資料表名稱。暫時稱之為 "pbidemo"。目前，串流分析工作的 Power BI 輸出中，一個資料集只能有一個資料表。

>	[AZURE.NOTE] You should not explicitly create this dataset and table in your Power BI account. They will be automatically created when you start your Stream Analytics job and the job starts pumping output into Power BI. If your job query doesn’t return any results, the dataset and table will not be created.

*	依序按一下 [確定]、[測試連線]，輸出組態就已經完成。

>	[AZURE.WARNING] Also be aware that if Power BI already had a dataset and table with the same name as the one you provided in this Stream Analytics job, the existing data will be overwritten.


##撰寫查詢

移至工作的 [查詢] 索引標籤。撰寫查詢，就是 Power BI 中要有的輸出。例如，可能是類似下列 SQL 查詢的內容：

    SELECT
    	MAX(hmdt) AS hmdt,
    	MAX(temp) AS temp,
    	System.TimeStamp AS time,
    	dspl
    INTO
        OutPBI
    FROM
    	Input TIMESTAMP BY time
    GROUP BY
    	TUMBLINGWINDOW(ss,1),
    	dspl

    
    
開始您的工作。驗證事件中樞是否正在接收事件，而且您的查詢會產生預期的結果。如果您的查詢輸出 0 個資料列，系統就不會自動建立 Power BI 資料集和資料表。

##在 Power BI 中建立儀表板

移至 [Powerbi.com](https://powerbi.com)，然後輸入組織識別碼登入如果串流分析工作查詢輸出任何結果，就會看到您的資料集已經建立完成：

![graphic5][graphic5]

如果想建立儀表板，請移至儀表板選項，然後建立新的儀表板。

![graphic6][graphic6]

在這個範例中，我們將它標籤為「示範儀表板 」。

現在按一下串流分析工作建立的資料集 (就是目前範例中的 pbidemo)。系統會開啟一個頁面並在這個資料集上方建立一張圖表。以下是您可以建立的報表範例：

選取 Σ 溫度和時間欄位。這兩個欄位會自動顯示在圖表中的 [值] 和 [軸]：

![graphic7][graphic7]

這樣一來，一張圖表就大功告成了，如下所示：

![graphic8][graphic8]

在 [值] 區段中，按一下溫度的下拉式清單，然後針對溫度選擇 [平均值]，接下來在圖表上，按一下 [視覺效果] 再選擇 [折線圖]：

![graphic9][graphic9]

您現在會看到一張折線圖，裡面列出各個時間的平均值。使用釘選選項，如下所示。您可以釘選先前建立的儀表板：

![graphic10][graphic10]

現在當您檢視儀表板以及這個釘選的報表時，會看到報表的數據不斷即時更新。試著變更事件中的資料 – 最高溫度或類似資料，您會看到儀表板即時反映最新的數據。

請注意，本教學課程示範如何為一個資料集建立一種圖表類型。Power BI 可協助您為組織建立其他客戶商業智慧型工具。如需其他 Power BI 儀表板範例，請觀賞[開始使用 Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) 視訊。

另一個深入了解如何利用 Power BI 建立儀表板的實用資源，可參考 [Power BI 預覽中的儀表板](http://support.powerbi.com/knowledgebase/articles/424868-dashboards-in-power-bi-preview)。

## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-tw/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
 

<!---HONumber=62-->