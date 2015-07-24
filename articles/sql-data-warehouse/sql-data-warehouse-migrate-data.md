<properties
   pageTitle="將您的資料移轉至 SQL 資料倉儲 | Microsoft Azure"
   description="將您的資料移轉至 Azure SQL 資料倉儲來開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/25/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# 移轉資料
移轉資料時的主要目標是填入 SQLDW 資料庫。此程序可以透過數種方式來達成。ADF 複製、SSIS 和 bcp 都可用來達成此目標。不過，隨著資料量增加，您應該考慮將資料移轉程序細分成步驟。這樣讓您有機會來最佳化每個步驟的效能和彈性，以確保順暢移轉資料。

本文首先討論 ADF 複製、SSIS 和 bcp 的簡單移轉案例。接著稍微深入討論如何將移轉最佳化。

## Azure Data Factory (ADF) 複製
[ADF 複製][]是 [Azure Data Factory][] 的一部分。您可以使用 ADF 複製將資料匯出到位於本機儲存體的一般檔案、匯出到到保留在 Azure blob 儲存體中的遠端一般檔案，或直接匯出到 SQL 資料倉儲。

如果資料是從一般檔案開始，則在開始將資料載入 SQL 資料倉儲之前，您首先要將資料傳輸到 Azure 儲存體 blob。一旦資料傳輸到 Azure blob 儲存體，您可以選擇再次使用 [ADF 複製][]，將資料推送至 SQL 資料倉儲。

PolyBase 還提供極高效能選項來載入資料。不過，這表示要使用兩種工具，而不止一種工具。如果您需要達到最佳效能，請使用 PolyBase。如果只想要使用單一工具 (且資料量不大)，則 ADF 就是答案。

> [AZURE.NOTE]PolyBase 規定資料檔案必須為 UTF-8。這是 ADF 複製的預設編碼，無可變更之處。這是提醒您不要變更 ADF 複製的預設行為。

請閱讀下文，內有一些很好的 [ADF 複製範例]。

## Integration Services ##
Integration Services (SSIS) 是一個功能強大且靈活的擷取轉換和載入 (ETL) 工具，支援複雜的工作流程、資料轉換，以及數個資料載入選項。使用 SSIS 來單純將資料傳輸至 Azure，或做為更廣泛移轉的一部分。

> [AZURE.NOTE]SSIS 可以匯出為 UTF-8，檔案中不需要有位元組順序標示。若要這樣設定，您必須先使用衍生的資料行元件，轉換資料流程中的字元資料來使用 65001 UTF-8 字碼頁。一旦轉換資料行之後，接著將資料寫入一般檔案目的地配接器，但要確定也已選取 65001 做為檔案的字碼頁。

SSIS 會連接到 SQL 資料倉儲，就像連接到 SQL Server 部署一樣。不過，您的連線必須使用 ADO.NET 連接管理員。您也應該格小心設定 "" 設定，以最大化輸送量。

> [AZURE.NOTE]不支援使用 OLEDB 連接到 Azure SQL 資料倉儲。

此外，封裝經常可能因為節流或網路問題而失敗。請將封裝設計成可以從失敗點繼續，而不必重做失敗之前已完成的工作。

如需詳細資訊，請參閱 [SSIS 文件][]。

## bcp
bcp 是專為一般檔案資料匯入和匯出所設計的命令列公用程式。資料匯出期間可能進行某些轉換。若要執行簡單的轉換，請使用查詢來選取和轉換資料。一旦匯出之後，一般檔案就可以直接載入到目標 SQL 資料倉儲資料庫。

> [AZURE.NOTE]通常建議將資料匯出時使用的轉換封裝在來源系統上的檢視表中。這可確保邏輯得以保留，且程序是可重複。

bcp 的優點包括：

- 簡單。bcp 命令十分容易建置和執行
- 可重新啟動的載入程序。一旦匯出，即可不限次數執行載入

bcp 的限制包括：

- bcp 只能使用列表式一般檔案。無法使用 xml 或 JSON 之類的檔案
- bcp 不支援匯出為 UTF-8。這可能造成無法在 bcp 匯出的資料上使用 PolyBase
- 資料轉換功能僅限於匯出階段且性質簡單
- 當透過網際網路載入資料時，bcp 的設計尚不夠健全。任何網路不穩定狀況都可能造成載入錯誤。
- bcp 依賴載入之前存在於目標資料庫中的結構描述

如需詳細資訊，請參閱[使用 bcp 將資料載入 SQL 資料倉儲][]。

## 最佳化資料移轉
SQLDW 資料移轉程序可以有效地分成三個獨立的步驟：

1. 匯出來源資料
2. 將資料傳輸至 Azure
3. 載入到目標 SQLDW 資料庫

每個步驟可以個別進行最佳化，建立健全、可重新啟動且富有彈性的移轉程序，讓每個步驟發揮最高的效能。

## 最佳化資料載入
稍微反過來看。載入資料最快的方式是透過 PolyBase。最佳化 PolyBase 載入程序對上述步驟設下必要條件，最好先了解這一點。如下：

1. 資料檔案的編碼
2. 資料檔案的格式
3. 資料檔案的位置

### 編碼
PolyBase 規定資料檔案必須為 UTF-8 編碼。這表示匯出資料時，資料必須符合這項需求。如果您的資料只包含基本 ASCII 字元 (不是擴充的 ASCII)，則這些會直接對應到 UTF-8 標準，您不需要太擔心編碼。不過，如果您的資料包含任何特殊字元，例如變音符號、腔調字或符號，或您的資料支援非拉丁語言，則您必須確保匯出檔案經過適當的 UTF-8 編碼。

> [AZURE.NOTE]bcp 不支援將資料匯出為 UTF-8。因此，最好的選擇是使用 Integration Services 或 ADF 複製來匯出資料。值得一提，資料檔案中不需要 UTF-8 位元組順序標記 (BOM)。

資料傳輸***之前***，任何使用 UFT-16 編碼的檔案都需要重新撰寫。

### 資料檔案的格式
PolyBase 規定要有固定的資料列結束字元 \n 或新行。您的資料檔必須符合此標準。字串或資料行結束字元沒有任何限制。

在 PolyBase 中，您必須將檔案中的每個資料行定義為外部資料表的一部分。請確定所有匯出的資料行都是必要，且型別符合必要的標準。

如需有關支援的資料類型的詳細資料，請回頭參閱 [移轉您的結構描述] 文章。

### 資料檔案的位置
SQL 資料倉儲只使用 PolyBase 從 Azure Blob 儲存體載入資料。因此，資料必須先傳輸到 blob 儲存體。

## 最佳化資料傳輸
資料移轉過程中，最慢的一部分是將資料傳輸到 Azure。不只網路頻寬可能是個問題，網路可靠性也可能嚴重阻礙進度。依預設，將資料移轉至 Azure 是透過網際網路，因此相當可能發生傳輸錯誤。不過，這些錯誤可能需要重新傳送整個或部分的資料。

所幸您有數個選項可以提升此程序的速度及恢復力：

### [ExpressRoute][]
您可以考慮使用 [ExpressRoute][] 加速傳輸。[ExpressRoute][] 為您提供確立的 Azure 私人連線，連接不經過公用網際網路。這絕不是必要的步驟。不過，從內部部署或共置設備推送資料到 Azure 時，將會改善輸送量。

使用 [ExpressRoute][] 的優點包括：

1. 更高的可靠性
2. 更快的網路速度
3. 更短的網路延遲
4. 更高的網路安全性

[ExpressRoute][] 有利於許多情況，而不只是移轉。

有興趣嗎？ 如需詳細資訊和定價，請瀏覽 [ExpressRoute 文件][]。

### Azure 匯入和匯出服務
Azure 匯入和匯出服務是針對大量 (GB++) 到巨量 (TB++) 的資料傳輸至 Azure 而設計的資料傳輸程序。它牽涉到將您的資料寫入磁碟及傳送至 Azure 資料中心。然後會代替您將磁碟內容載入 Azure 儲存體 Blob。

匯入匯出程序的高階觀點如下：

1. 設定要接收資料的 Azure Blob 儲存體容器
2. 將資料匯出到本機儲存體
2. 使用 [Azure 匯入/匯出工具] 將資料複製到 3.5 英吋 SATA II/III 硬碟機
3. 使用 Azure 匯入和匯出服務，並提供 [Azure 匯入/匯出工具] 所產生的日誌檔案，建立匯入工作
4. 將磁碟機寄送到指定的 Azure 資料中心
5. 您的資料傳輸至 Azure Blob 儲存體容器
6. 使用 PolyBase 將資料載入 SQLDW

### [AZCopy][] 公用程式
[AZCopy][] 公用程式是將資料傳輸至 Azure 儲存體 Blob 的一個極佳工具。它是針對少量 (MB++) 到非常大量 (GB++) 資料傳輸而設計。[AZCopy] 也設計成將資料傳輸到 Azure 時提供絕佳彈性的輸送量，因此是資料傳輸步驟的理想選擇。一旦傳輸之後，您就可以使用 PolyBase 將資料載入 SQL 資料倉儲。您也可以使用「執行程序」工作，將 AZCopy 納入 SSIS 封裝。

若要使用 AZCopy，您必須先下載並安裝它。有[生產版本][]和[預覽版本][]可用。

若要從檔案系統上傳檔案，您需要如下所示的命令：

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

高階程序摘要如下：

1. 設定要接收資料的 Azure 儲存體 blob 容器
2. 將資料匯出到本機儲存體
3. AZCopy 您在 Azure Blob 儲存體容器中的資料
4. 使用 PolyBase 將資料載入 SQL 資料倉儲

有完整的文件：[AZCopy][]。

## 最佳化資料匯出
除了確保匯出符合 PolyBase 所規定的需求，您也可以設法最佳化資料匯出，以進一步改善程序。

> [AZURE.NOTE]因為 PolyBase 要求資料必須是 UTF-8 格式，您不太可能使用 bcp 來執行資料匯出。bcp 不支援將資料檔案輸出為 UTF-8。SSIS 或 ADF 複製比較適合執行這類的資料匯出。

### 資料壓縮
PolyBase 可以讀取 gzip 壓縮的資料。如果您可以將資料壓縮成 gzip 檔案，就能將網路上推送的資料量減到最少。

### 多個檔案
將大型資料表分割成數個檔案，不僅有助於改善匯出速度，也有助於重新開始傳輸，以及資料進入 Azure blob 儲存體之後的整體管理能力。PolyBase 的眾多優點之一是它會讀取資料夾內的所有檔案，並視為一個資料表來處理。因此，最好將每個資料表的檔案隔離到它自己的資料夾。

PolyBase 也支援一項稱為「遞迴資料夾周遊」的功能。您可以使用這項功能來進一步加強組織您匯出的資料，以改善資料管理。

若要深入了解使用 PolyBase 載入資料，請參閱[使用 PolyBase 將資料載入 SQL 資料倉儲][]。


## 後續步驟
如需有關移轉的詳細資訊，請參閱[將您的解決方案移轉至 SQL 資料倉儲][]。如需更多開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[AZCopy]: storage/storage-use-azcopy.md
[ADF 複製]: storage/data-factory-copy-activity.md
[ADF 複製範例]: data-factory/data-factory-copy-activity-examples.md
[開發概觀]: sql-data-warehouse-develop-overview.md
[將您的解決方案移轉至 SQL 資料倉儲]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[使用 bcp 將資料載入 SQL 資料倉儲]: sql-data-warehouse-load-with-bcp.md
[使用 PolyBase 將資料載入 SQL 資料倉儲]: sql-data-warehouse-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute 文件]: http://azure.microsoft.com/documentation/services/expressroute/

[生產版本]: http://aka.ms/downloadazcopy/
[預覽版本]: http://aka.ms/downloadazcopypr/
[SSIS 文件]: https://msdn.microsoft.com/zh-tw/library/ms141026.aspx

<!---HONumber=July15_HO1-->