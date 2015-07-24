<properties 
	pageTitle="將儲存資料傳輸至雲端的選項 | Azure" 
	description="提供將資料從非雲端來源的內部部署傳輸到 Microsoft Azure 以供進一步分析時，如何選擇最佳選項的指引。" 
	services="data-factory, hdinsight, machine-learning, storage, sql-database" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="01/07/2014" 
	ms.author="cgronlun"/>

# 將儲存資料傳輸至 Azure 雲端的選項

本文章提供將資料從內部部署或其他雲端來源傳輸到 Microsoft Azure 以供進一步分析時，如何選擇正確選項的指引。傳輸大量資料可能需要花費很長的時間，因此需要適當的安全性。

本文內容：

* [Azure Blob 儲存體的匯入/匯出服務](#blob)
* [AZCopy 公用程式](#azcopy-utility)
* [Azure PowerShell](#ps)
* [Azure Data Factory (預覽)](#data-factory)
* [Azure SQL Database 移轉工具](#tools)
* [Azure SQL 資料同步 (預覽)](#data-sync)
* [Azure 事件中樞](#event-hubs)
* [其他資料傳輸選項](#other)
* [選擇正確的資料傳輸選項](#choose)


## Azure Blob 儲存體的匯入/匯出服務

透過網路進行上傳或下載所費不貲或不可行時，您可以使用 Azure 匯入/匯出服務將大量檔案資料傳輸至 Azure Blob 儲存體，或從 Azure Blob 儲存體將檔案資料傳輸到他處。透過網路上傳或下載大型資料集需要花費很長的時間。例如，透過 T3 (44.7 Mbps) 傳輸 10 TB 需要 1 個月的時間。有了 Microsoft Azure 匯入/匯出服務，客戶能藉由送交硬碟來縮短資料上傳或下載的時間。包括運送在內，請預留幾天的時間。

若要將大量檔案資料移轉至 Blob 儲存體，您可以將含有該資料的一或多個硬碟送至 Azure 資料中心，而您的資料將會在此上傳至儲存體帳戶。同樣地，若要從 Blob 儲存體匯出資料，您可以將空的硬碟送至 Azure 資料中心，而您儲存體帳戶中的 Blob 資料將會在此複製到硬碟。然後再送回給您。在傳送含有資料的磁碟機之前，您要加密該磁碟機上的資料；當匯入/匯出服務匯出您的資料以便傳送給您時，此資料也會在送出前進行加密。

如需詳細資訊，請參閱「[使用 Microsoft Azure 匯入/匯出服務將資料移轉至 Blob 儲存體][import-export]」。


## AZCopy 公用程式

AzCopy 是個命令列公用程式，專為高效能上傳、下載，以及將資料複製到和複製出 Microsoft Azure Blob、檔案和資料表儲存體所設計。如果透過網路傳輸資料是可行的方法，此公用程式適合用於 Azure 儲存體和內部部署設施之間的一次性資料移動。請參閱「[開始使用 AzCopy 命令列公用程式][azcopy]」

> [AZURE.NOTE]Linux 使用者：[下載 ACP for Linux 和 AzCopy for Linux](http://www.paratools.com/acp) (英文)


## Azure PowerShell

Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。您可以使用 Azure PowerShell 將資料上傳至 Blob 儲存體，以供進階分析或 MapReduce 工作處理資料。

另請參閱：

* [使用 Azure PowerShell 將資料上傳至 Blob 儲存體][upload]
* [如何安裝和設定 Azure PowerShell][install]


## Azure Data Factory (預覽)

Azure Data Factory 是完全受管理的服務，它能將資料存放、處理及移動服務組成簡化、可擴充及可靠的資料生產管線。

開發人員可建置資料導向的工作流程，以聯結、彙總和轉換來自於內部部署 (透過資料管理閘道)、雲端和網際網路服務的半結構化、未結構化和結構化資料，以及透過簡單的 JSON 指令碼設定複雜的資料處理。結果資料可儲存在 Azure 儲存體或 Azure SQL Database 中，以供進一步分析之用。

具體來說，開發人員可以協調「[使用 Azure Data Factory 複製資料](data-factory-copy-activity.md)」 的＜[支援來源與接收](data-factory-copy-activity.md#SupportedSourcesAndSinks)＞一節中所述的多種來源和目的地之間的一般複製活動，該文還包含不同類型的資料存放區屬性、資料行對應、序列化格式和類型處理。

服務可以處理自動重新啟動失敗，並允許在將資料從某種格式移動到其他格式轉換格式。若要定義複製活動，請參閱「[開始使用 Data Factory][start]」。「[啟用管線以使用內部部署資料][pipelines]」中說明了資料存放區註冊和閘道器安裝經驗。

另請參閱：

* [Data Factory 服務簡介][intro]

## Azure SQL Database 移轉工具

能成功將 SQL Server 內部部署和非 SQL Server 資料庫移轉到 Azure SQL Database 的工具比比皆是。最適合案例的工具取決於待移轉之資料庫的類型、大小及複雜性：

* 您可以將資料庫匯出、將匯出檔案儲存在 Azure Blob 儲存體帳戶，然後匯入成新的 Azure SQL Database，藉此移轉結構描述和現有 Azure SQL Database 的資料。關於上述匯出作業，建立的檔案稱為 BACPAC 檔案。如需詳細資訊，請參閱「[做法：在 Azure SQL Database 中使用匯入和匯出服務][sql-import]」。
* 資料庫複製功能會在 Azure 中建立新資料庫，該資料庫是現有 Azure SQL Database 的交易一致複本。如需詳細資訊，請參閱「[在 Azure SQL Database 中複製資料庫][sql-copy]」。
* 需要複雜的資料轉換時，您可以使用 SQL Server Integration Services (SSIS)。SSIS 能用來將資料移入及移出 Azure SQL Database。如需詳細資訊，請參閱「[做法：使用 Integration Services 將資料庫移轉至 Azure SQL Database][integrate]」和「[適用於 Azure 和混合式資料移動的 SSIS][SSIS]」(英文)。
* SQL Server 匯入和匯出精靈是建立 SSIS 封裝以利移轉資料的便捷方法。設定來源和目的地之後，您可以指定基本資料轉換。您可以儲存、修改、執行這些封裝，以及將封裝排程為工作。如需詳細資訊，請參閱「[做法：使用匯入和匯出精靈將資料庫移轉至 Azure SQL Database][wizard]」。
* SQL Database 移轉精靈是有助於在內部部署 SQL Server 和 Azure SQL Database 之間，以及在多部 Azure SQL Database 伺服器之間移轉結構描述和資料的工具。這款工具也能分析追蹤檔案和指令碼，找出與 Azure SQL Database 之間是否有相容性問題。如需詳細資訊，請參閱「[做法：使用 SQL Database 移轉精靈][use-wizard]」。
* bcp 公用程式可用來將大量新資料列匯入 SQL Server 資料表，抑或是將資料表的資料匯出成資料檔案。如需詳細資訊，請參閱「[做法：使用 bcp 將資料庫移轉至 Azure SQL Database][bcp]」。

另請參閱：

* [將資料庫移轉至 Azure SQL Database][migrate]
 

## Azure SQL 資料同步 (預覽)

SQL 資料同步 (預覽) 可讓您在 Azure SQL Database 和裝載於 SQL Server 或 Azure SQL Database 中的資料庫之間建立及排程定期同步處理作業。

SQL 資料同步適合資料開發人員同步處理內部部署和雲端 Azure 資料庫之間的差異變更。請參閱「[SQL 資料同步][sync]」。

##	Azure 事件中心

Microsoft Azure 事件中心是事件攝入服務，它能提供大規模進入雲端的事件和遙測入口，並具備低延遲和高可靠性等特性。當搭配其他下游服務時，該服務特別適合用於應用程式檢測、使用者經驗或工作流程處理及物聯網案例。

開發人員可以撰寫程式碼以將資料傳送到事件中心、處理資料並儲存在 Azure Blob 或 Azure SQL Database 中，以供進一步處理。

開發人員也可以運用 Azure 資料流分析來輸出資料，它是一款完全受管理的服務，能透過串流資料提供可擴充的複雜事件處理。開發人員可以從 Azure 事件中心挑選資料流、指定要用來序列化連入事件的格式 (如 JSON、CSV 或 Avro 格式)，然後加入包括 Azure Blob 或 Azure SQL Database 的輸出位置。

請參閱：

* [事件中樞服務資訊](/services/event-hubs/) (英文)
* [事件中心概觀][overview]
* [Azure 串流分析簡介][stream] (英文)

## 其他資料傳輸選項

混合式連線提供簡單又方便的作法，讓您將 Azure 網站和 Azure 行動服務連接到內部部署資源。開發人員可以建立網站，以將資料從內部部署移至 Azure。請參閱「[混合式連線概觀][hybrid]」。

[虛擬網路](/services/virtual-network/)可讓您透過在 Azure 虛擬機器中執行的資料整合工具，安全地連接現場資料中心內的內部部署 SQL Server 資料庫。唯有相同虛擬網路中的虛擬機器和服務能彼此識別或連接。如有需要，您甚至可以透過您的網絡服務提供者或 Exchange 提供者建立連接到 Azure 的 [ExpressRoute](/services/expressroute/) 直接連接，並略過公用網際網路。

[Azure Marketplace](?../source=datamarket.md) 提供可將資料移動到 Azure 的合作夥伴解決方案，例如 Storm Managed File Transfer。

## 選擇正確的資料傳輸選項

### 決策樹狀圖

![協助您決定應選擇的雲端資料傳輸方案。][decision]

關於決策樹狀圖的附註：

* SQL 資料同步 (預覽) 會因資料變更觸發同步處理。
* Data Factory (預覽) 支援在 Azure 儲存體、Azure SQL Database 及內部部署 SQL Server 之間複製資料。 
* 除了使用 Data Factory 之外，您還可以擴充現有 SSIS 封裝，以按照排程將資料移至雲端。

### 移動以 GB 為單位的資料

<table border="1">
<tr>
<th>移動資料</th>
<th>一次性</th>
<th>排程</th>
<th>注意事項</th>
</tr>

<tr>
<td><p>檔案到 Azure 儲存體</p>
</td>
<td><ul>
<li><a href="/documentation/articles/storage-use-azcopy/">AzCopy</a></li>
<li><a href="http://www.paratools.com/acp" target="_blank">acp</a></li>
<li><a href="/documentation/articles/install-configure-powershell/">Azure PowerShell</a></li>
<li><a href="/documentation/articles/storage-import-export-service/">Azure 匯入/匯出</a></li>
</ul>
</td>
<td><p>N/A</p>
</td>
<td><p>當透過網路傳輸資料過於不切實際時，可以改用 Azure 匯入/匯出而避免使用 AzCopy、acp 及 Azure PowerShell。服務可能需要花費好幾天的時間；將磁碟運送到資料中心的時間另計。</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>SQL Server 當 Azure SQL Database</li>
<li>Azure SQL Database 到 SQL Server</li>
</ul>
</td>
<td><ul>
<li><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></li>
<li><a href="http://msdn.microsoft.com/library/azure/ee730904.aspx">SQL Database 移轉工具</a></li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></p>
</td>
<td><p>建議使用 Azure Data Factory (預覽) 而避免使用移轉工具，因為它提供格式轉換和異常情況工作自動復原。如果將 SQL Server 部署於內部，您需要資料管理閘道才能連接 SQL Server。</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>Azure 儲存體到 Azure SQL Database</li>
<li>Azure SQL Database 到 Azure 儲存體</li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></p>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></p>
</td>
<td><p>Azure Data Factory (預覽) 提供格式轉換和異常情況工作自動復原。如果將 SQL Server 部署於內部，您需要資料管理閘道才能連接 SQL Server。</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>SQL Server 到 SQL 資料同步</li>
<li>SQL 資料同步到 Azure SQL Database</li>
</ul>
</td>
<td><p>N/A</p>
</td>
<td><p><a href="http://msdn.microsoft.com/library/azure/hh456371.aspx">SQL 資料同步</a></p>
</td>
<td><p>SQL 資料同步 (預覽) 能依據定義要同步處理之資料庫、資料表及資料行的同步處理群組和同步處理排程來同步處理資料。</p>
</td>
</tr>

</table>



### 移動以 TB 為單位的資料

<table border="1">
<tr>
<th>移動資料</th>
<th>一次性</th>
<th>排程</th>
<th>注意事項</th>
</tr>

<tr>
<td><p>檔案到 Azure 儲存體</p>
</td>
<td><p><a href="/documentation/articles/storage-import-export-service/">Azure 匯入/匯出</a></p>
</td>
<td><p>N/A</p>
</td>
<td><p>服務可能需要花費好幾天的時間；將磁碟運送到資料中心的時間另計。</p>
</td>
</tr>

</table>
<br>


<!--Anchors-->
[Azure Import/Export service for Blob storage]: #blob
[AZCopy utility]: #azcopy-utility
[Azure PowerShell]: #ps
[Azure Data Factory (preview)]: #data-factory
[Azure SQL Database migration tools]: #tools
[Azure SQL Data Sync (preview)]: #data-sync
[Azure Event Hubs]: #event-hubs
[Other options for data transfer]: #other
[Choose the right data transfer option]: #choose

<!--Image references-->
[decision]: ./media/data-management-options-for-transferring-data/data-transfer-decision-tree.png


<!--Link references-->
[import-export]: ../storage-import-export-service.md
[azcopy]: ../storage-use-azcopy.md
[upload]: ../hdinsight-upload-data.md#powershell
[install]: ../install-configure-powershell.md
[start]: data-factory-get-started.md
[pipelines]: data-factory-use-onpremises-datasources.md
[copy]: data-factory-copy-activity.md
[intro]: data-factory-introduction.md
[sql-import]: http://msdn.microsoft.com/library/azure/hh335292.aspx
[sql-copy]: http://msdn.microsoft.com/library/azure/ff951624.aspx
[integrate]: http://msdn.microsoft.com/library/azure/jj156150.aspx
[SSIS]: http://msdn.microsoft.com/library/jj901708.aspx
[wizard]: http://msdn.microsoft.com/library/azure/jj156152.aspx
[use-wizard]: http://msdn.microsoft.com/library/azure/jj156144.aspx
[bcp]: http://msdn.microsoft.com/library/azure/jj156153.aspx
[migrate]: http://msdn.microsoft.com/library/azure/ee730904.aspx
[overview]: http://msdn.microsoft.com/library/dn836025.aspx
[stream]: ../stream-analytics-introduction.md
[sync]: http://msdn.microsoft.com/library/azure/hh456371.aspx
[hybrid]: ../integration-hybrid-connection-overview.md
 

<!---HONumber=62-->