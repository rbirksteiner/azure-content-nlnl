<properties 
	pageTitle="使用 Azure 預覽入口網站監視和管理 Azure Data Factory" 
	description="了解如何使用 Azure Management Portal 監控和管理您建立的 Azure 資料處理站。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# 使用 Azure 預覽入口網站監視 Azure Data Factory
本文說明使用 Azure Preview 入口網站監視和管理 Azure Data Factory 的各種案例。

## <a name="AllDataFactories"></a> 檢視 Azure 訂用帳戶中的所有 Data Factory

- 登入 [Azure Preview 入口網站][azure-preview-portal]。
- 按一下左邊的 [瀏覽] 中樞，然後按一下 [Data Factory]。  

	![瀏覽中樞 -> Data Factory][image-data-factory-browse-datafactories]

	如果看不到 [Data Factory]，請按一下 [全部]，然後按一下 [瀏覽] 刀鋒視窗中的 [Data Factory]。

	![瀏覽中樞 -> 全部內容][image-data-factory-browse-everything]

- 您應該會在 [Data Factory] 刀鋒視窗中看到所有 Data Factory。

	![Data Factory 刀鋒視窗][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> 檢視 Data Factory 的詳細資料

若要檢視 Data Factory 的詳細資料，請執行下列其中一項：


- 按一下上方顯示的 [Data Factory] 刀鋒視窗中的 Data Factory。
- 按一下 [開始面板] 中的 Data Factory 連結。[開始面板] 是您在登入 Azure Preview 入口網站時看到的刀鋒視窗。如果在建立 Data Factory 時選取 [新增至開始面板] (預設選項)，則您應該會在 [開始面板] 上看到 Data Factory 連結，如下圖所示。在此範例中，[開始面板] 具有 **ADFTutorialDataFactory**、**ADFTutorialDataFactoryDF** 和 **LogProcessingFactory** 的 Data Factory 連結。


![[開始面板] 中的 Data factory][image-data-factory-datafactory-from-startboard]

無論如何，您都會看到所選取 Data Factory 的 [DATA FACTORY] 刀鋒視窗，如下圖所示。

 ![Data Factory 首頁][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> 檢視 Data Factory 的圖表檢視
在 Data Factory 的 [DATA FACTORY] 刀鋒視窗中，按一下 [圖表] 磚，即可查看 Data Factory 的圖表檢視。

![Data Factory 圖表檢視][image-data-factory-diagram-view]
 
### 在圖表檢視中開啟管線
在圖表檢視中，於管線上按一下滑鼠右鍵，然後按一下 [開啟管線]，即可檢視管線中的所有活動。您應該會看到管線中的活動，以及活動的輸入和輸出資料集。![開啟管線](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

按一下左上角階層連結中的 [Data Factory]，回到圖表檢視。圖表檢視會顯示所有管線。在此範例中，您只建立了一個管線。

## <a name="DataFactoryLinkedServices"></a> 檢視 Data Factory 中的連結服務
在 Data Factory 的 [DATA FACTORY] 刀鋒視窗中，按一下 [連結服務] 磚，即可查看清單中所有的連結服務。

![連結服務刀鋒視窗][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> 檢視連結服務的詳細資料
在 [連結服務] 刀鋒視窗中，按一下清單中的連結服務，即可查看其詳細資料。

![連結服務刀鋒視窗][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> 檢視 Data Factory 中的資料集 
在 Data Factory 的 [DATA FACTORY] 刀鋒視窗中，按一下 [資料集] 磚，即可查看 Data Factory 中的所有資料表。

![資料集刀鋒視窗][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> 檢視資料集的詳細資料
按一下 [資料集] 分頁上資料集清單中的資料集，即可查看資料集的詳細資料。請注意，資料表是具有結構描述的矩形資料集。它是目前唯一支援的資料集類型。

![資料表刀鋒視窗][image-data-factory-table]

在上方 [資料表] 刀鋒視窗中，[最近更新的配量] 和 [最近失敗的配量] 清單都依照 [上次更新時間] 排序。在下列情況中，配量的更新時間會變更。

-  您以手動方式更新配量的狀態，例如，使用 **Set-AzureDataFactorySliceStatus** (或) 在配量的 [配量] 刀鋒視窗上按一下 [執行]。
-  配量因為執行而變更狀態 (例如，開始執行、執行結束但失敗、執行結束且成功等)。
 
	
若要改成依配量開始/結束時間來檢視資料配量，請按一下 [資料配量 (依配量時間)] 磚。
 
![資料配量 (依配量時間)][DataSlicesBySliceTime]

按一下清單的標題或 **...** (省略符號)，查看更長的配量清單。

![資料表的所有配量][image-data-factory-all-slices]

在 [資料配量] 刀鋒視窗中，按一下 [篩選] 按鈕以查看 [篩選] 刀鋒視窗，此刀鋒視窗可讓您篩選配量，以查看您想要檢閱的特定配量。在配量為 [依更新時間排序] 的 [資料配量] 刀鋒視窗上按一下 [篩選] 時，您會看到與下列其中一個刀鋒視窗類似的刀鋒視窗。

![篩選刀鋒視窗][image-data-factory-filter-blade]

[篩選] 刀鋒視窗可讓您根據 [上次更新時間] 和 [配量狀態] 加以篩選。下表提供所有配量狀態和說明。
 
配量狀態 | 說明
------------ | ------------
PendingExecution | 資料處理尚未啟動。
InProgress | 資料處理進行中。
Ready | 資料處理已完成，而且資料配量就緒。
Failed | 產生配量的執行無法執行。
Skip | 略過配量處理。
Retry | 重試產生配量的執行。
Timed Out | 配量的資料處理逾時。
PendingValidation | 在開始處理之前，資料配量正在等候依照驗證原則進行驗證。
RetryValidation | 重試配量的驗證。
FailedValidation | 配量的驗證失敗。
LongRetry | 如果已在資料表 JSON 中指定 LongRetry，而且配量的一般重試失敗，則配量將會處於這個狀態。
ValidationInProgress | 正在執行配量的驗證 (根據 JSON 資料表中定義的原則) 。

在配量為 [依配量時間排序] 的 [資料配量] 刀鋒視窗上按一下 [篩選] 時，您會看到不同類型的 [篩選] 刀鋒視窗。

![篩選刀鋒視窗 2][image-data-factory-filter-blade-2]


啟動 [篩選] 刀鋒視窗時，[到] 欄位會自動設為最近的時間 (四捨五入) 來限制傳回的記錄數目。[從] 欄位也會一併自動設定。您可以按一下 [行事曆] 按鈕來變更 [從] 日期。變更 [從] 日期時，[到] 日期也會自動變更。

您可以按一下 [上一個]/[**下一個**] 按鈕，來檢視上一段期間/下一段期間的配量。[上一個] 和 [下一個] 按鈕的時間範圍是根據配量頻率和間隔所設定，如下表所示。

頻率 | 間隔值範圍 | 產生時間區塊
----------| -------------------- | --------------------
分鐘 | 1-4 | 6 小時
分鐘 | 5-29 | 1 天
分鐘 | 30-180 | 7 天
分鐘 | 180+ | 28 天 (大約，行事曆月份)
小時 | 1-3 | 7 天
小時 | 4-11 | 28 天 (大約，行事曆月份)
小時 | 12-72 | 182 天 (大約，6 個月)
小時 | 73+ | 1 年
天 | 1-6 | 1 年
天 | 7-20 | 5 年
天 | 21+ | 10 年
週 | 1-3 | 5 年
週 | 4+ | 10 年
月 | 任意 | 10 年
 
例如，如果您將 [頻率] 定義為 [小時]，並將 [間隔] 定義為 **2**，則按一下 [下一個]/[**上一個**] 按鈕時，會將時間範圍往其中一個方向移動「7 天」。不論您檢視的是所有配量、最近的配量或問題配量，此邏輯皆適用於 [篩選] 刀鋒視窗。




## <a name="DataFactorySlice"></a> 檢視配量的詳細資料
在 [資料表] 刀鋒視窗或 [資料配量] 刀鋒視窗上，按一下配量清單中的配量，即可查看該配量的詳細資料。

![資料配量][image-data-factory-dataslice]

如果配量不是 [就緒] 狀態，您可以在 [未就緒的上游配量] 清單中看到未就緒且阻礙目前配量執行的上游配量。

### <a name="DataFactoryActivtyRuns"></a> 檢視配量的所有活動執行
一個配量可有多次執行。例如，配量失敗時，服務可能會重試幾次。您也可以重新執行所有重試失敗的配量。您可以在底端清單中看到 [**資料配量**] 刀鋒視窗上的所有活動執行。

## <a name="DataFactoryActivtyRunDetails"></a> 檢視活動執行的詳細資料
在 [資料配量] 刀鋒視窗的執行清單中按一下活動執行，即可查看該活動執行的詳細資料。

![活動執行詳細資料][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> 作業透鏡：過去一週的事件
在 Data Factory 的 [DATA FACTORY] 刀鋒視窗 (或首頁) 中，按一下 [作業] 透鏡中的 [過去一週的事件]，即可查看過去一週的事件。這可協助您取得 Data Factory 在過去一週所執行之作業的高階檢視。它也可協助您對移動/處理資料時發生的任何錯誤進行疑難排解。

![Data Factory 事件][image-data-factory-events]


## 另請參閱

文章 | 說明
------ | ---------------
[使用 PowerShell 監視和管理 Azure Data Factory][monitor-manage-using-powershell] | 本文說明如何使用 Azure PowerShell Cmdlet 監視 Azure Data Factory。 


[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

[image-data-factory-filter-blade-2]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade2.png


[image-data-factory-browse-everything]: ./media/data-factory-monitor-manage-using-management-portal/BrowseEverything.png

[image-data-factory-browse-datafactories]: ./media/data-factory-monitor-manage-using-management-portal/BrowseDataFactories.png

[image-data-factory-datafactories-blade]: ./media/data-factory-monitor-manage-using-management-portal/DataFactories.png

[image-data-factory-datafactory-from-startboard]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryFromStartboard.png

[image-data-factory-datafactory-home-page]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryHomePage.png

[image-data-factory-diagram-view]: ./media/data-factory-monitor-manage-using-management-portal/DiagramView.png

[image-data-factory-linked-services]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServicesBlade.png

[image-data-factory-linked-service]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServiceBlade.png

[image-data-factory-datasets]: ./media/data-factory-monitor-manage-using-management-portal/Datasets.png

[image-data-factory-table]: ./media/data-factory-monitor-manage-using-management-portal/Table.png

[image-data-factory-all-slices]: ./media/data-factory-monitor-manage-using-management-portal/AllSlices.png

[image-data-factory-filter]: ./media/data-factory-monitor-manage-using-management-portal/Filter.png

[image-data-factory-dataslice]: ./media/data-factory-monitor-manage-using-management-portal/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-monitor-manage-using-management-portal/ActivityRunDetails.png

[image-data-factory-events]: ./media/data-factory-monitor-manage-using-management-portal/Events.png
[DataSlicesBySliceTime]: ./media/data-factory-monitor-manage-using-management-portal/DataSlicesBySliceTime.png

<!---HONumber=62-->