<properties 
	pageTitle="Azure 資料處理站-常見問題集" 
	description="關於 Azure Data Factory 的常見問題。" 
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
	ms.date="06/16/2015" 
	ms.author="spelluru"/>

# Azure 資料處理站-常見問題集

## 一般問題

### 問：什麼是 Azure Data Factory？

資料處理站是完全受管理的服務，讓開發人員將資料儲存體、搬移和處理服務撰寫到高度可用的容錯資料管線。資料處理站可在內部部署和雲端資料儲存體上作業。管線是資料輸入、處理活動和資料輸出的集合，並以簡單的 JSON 指令碼定義，以及透過 PowerShell 命令啟動。一旦啟動後，資料處理站會使用代表使用者的自動叢集管理選項，來協調及排定管線在 HDInsight (Hadoop) 上的執行。資料處理站也會提供視覺化的管理和透過 Azure 預覽入口網站的監視經驗，利用一個儀表板中具有多項操作和服務的健康資訊，來監視所有的管線。
 
### 問：Data Factory 可解決何種客戶難題？

Azure 資料處理站藉由完全受管理之服務的控制和監視功能，可在與非結構化資料並存的傳統關聯式儲存體中，平衡且靈活運用不同資料儲存體、處理和搬移服務。

### 問：Data Factory 的目標對象是誰？


- 資料開發人員：負責建置 Hadoop 與其他系統之間的整合服務：
	- 必須掌握及整合不斷變動和成長的資料環境
	- 必須為昂貴、難以維護，且不是高度可用或容錯的資訊產品撰寫自訂程式碼

- IT 專業人員：想要將更多不同資料併入其 IT 基礎結構內：
	- 必須觀察組織內所有資料以衍生出豐富的業務深入見解
	- 必須跨越內部部署和雲端來管理計算資源和儲存體資源，以平衡成本和規模
	- 必須快速加入多個來源和處理動作以處理新的商務需求，同時維護所有計算資產和儲存體資產的可見度

###  問：哪裡可以找到 Azure Data Factory 的定價詳細資料？

請參閱 [Data Factory 定價詳細資料頁面][adf-pricing-details]以了解 Azure Data Factory 的定價詳細資料。

### 問：如何開始使用 Azure Data Factory？

- 如需 Azure Data Factory 的概觀，請參閱 [Azure Data Factory 簡介][adf-introduction]。
- 如需快速教學課程，請參閱[開始使用 Azure Data Factory][adfgetstarted]。
- 如需完整文件，請參閱 [Azure Data Factory 文件][adf-documentation-landingpage]。

  
### 問：客戶如何存取 Data Factory？

客戶可透過 [Azure Preview 入口網站][azure-preview-portal]存取 Data Factory。

### 問：什麼是 Data Factory 的區域可用性？

在公開預覽版中，資料處理站只可在美國西部使用。資料處理站所使用的計算服務和儲存體服務可以在其他地區使用。
 
### 問：Data Factory/管線/活動/資料集的數目有什麼限制？ 


- 訂用帳戶中的 Data Factory 數目：50
- Data Factory 中的管線數目：100
- 管線中的活動數目：10
- Data Factory 中的資料集數目：100

### 問：Azure Data Factory 服務的撰寫/開發人員經驗為何？

您可以使用下列其中一項來撰寫/建立資料處理站：

- **Azure Preview 入口網站**。Azure 預覽入口網站中的 Data Factory 刀鋒視窗提供豐富的使用者介面，讓您建立資料處理站和連結的服務。**Data Factory 編輯器**也是入口網站的一部分，讓您透過指定成品的 JSON 定義，輕鬆建立連結服務、資料表、資料集和管線。如需編輯器的概觀，請參閱 [Data Factory 編輯器][data-factory-editor]，如需使用入口網站/編輯器來建立和部署 Data Factory 的範例，請參閱[開始使用 Data Factory][datafactory-getstarted]。   
- **Azure PowerShell** (英文)。如果您是 PowerShell 使用者，而且想要使用 PowerShell，而不是入口網站 UI，則可以使用 Azure PowerShell 隨附的 Azure Data Factory Cmdlet 來建立和部署資料處理站。請參閱[使用 Azure PowerShell 建立和監視 Azure Data Factory][create-data-factory-using-powershell] 以取得簡單範例；以及[教學課程：使用 Data Factory 移動和處理記錄檔][adf-tutorial]，以取得使用 PowerShell Cmdlet 建立和部署 Data Factory 的進階範例。如需 Data Factory Cmdlet 的完整文件，請參閱 MSDN Library 上的 [Data Factory Cmdlet 參考][adf-powershell-reference]內容。  
- **.NET 類別庫**。您可以使用 Data Factory .NET SDK，透過程式設計方式建立資料處理站。如需使用 .NET SDK 建立 Data Factory 的逐步解說，請參閱[使用 .NET SDK 建立、監視和管理 Data Factory][create-factory-using-dotnet-sdk]。如需 Data Factory .NET SDK 的完整文件，請參閱 [Data Factory 類別庫參考][msdn-class-library-reference]。  
- **REST API**。您也可以使用 Azure Data Factory 服務所公開的 REST API 來建立和部署資料處理站。如需 Data Factory REST API 的完整文件，請參閱 [Data Factory REST API 參考][msdn-rest-api-reference]。 

### 問：我是否可以重新命名資料處理？
否。和其他 Azure 資源一樣，您無法變更 Azure Data Factory 的名稱。

## 活動 - 常見問題集
### 問：支援哪些資料來源和活動？

- **支援的資料來源：** 
	- Azure 儲存體 (Blob 與資料表)
	- Azure SQL
	- Azure DocumentDB
	- 內部部署 SQL Server
	- 內部部署 Oracle 
	- 內部部署檔案系統
	- 內部部署 MySQL
	- 內部部署 DB2
	- 內部部署 Teradata
	- 內部部署 Sybase
	- 內部部署 PostgreSQL。  
- **支援的活動**： 
	- 複製活動 (內部部署至雲端，以及雲端至內部部署)
	- HDInsight 活動 (Pig、Hive、MapReduce、Hadoop 資料流處理轉換)
	- Azure Machine Learning 批次評分活動
	- Azure SQL 預存程序活動
	- 自訂 .NET 活動。

### 何時執行活動？
輸出資料表中的**可用性**組態設定決定何時執行活動。活動會在開始執行之前，檢查是否滿足所有輸入資料相依性 (即 [就緒] 狀態)。

## 複製活動 - 常見問題集
### 問：複製活動支援哪些區域？

複製活動支援將資料複製至下列區域：美國東部、美國東部 2、美國西部、美國中部、美國中北部、美國中南部、北歐、西歐和東南亞。

使用上述其中一個區域來路由資料，也可支援將資料複製到其他區域。複製作業會根據用於資料路由的地區計量付費。

複製目的地的區域 | 用於路由的區域
-------------------------- | -----------------------
東亞 | 東南亞
日本東部 | 美國西部
日本西部 | 美國西部
巴西南部 | 美國東部 2

### 如何複製到多個輸出資料表？
管線中可以有多個輸出資料表，如下列範例所示：

	"outputs":  [ 
		{ "name": “outputtable1” }, 
		{ "name": “outputtable2” }  
	],
 
### 最好是一個管線有多個活動，還是每個活動都有不同的管線？ 
管線依例應該有配套的相關活動。邏輯上，如果管線外部的任何其他活動都未使用連接活動的資料表，則可以將活動保留在一個管線中。如此一來，您就不需要鏈結管線作用期間，使其彼此一致。此外，更新管線時，也會更適當地保留管線內部資料表中的資料完整性。管線更新基本上會停止、移除並重新建立管線內的所有活動。從撰寫觀點來看，可能也較容易看出管線的某個 JSON 檔案中相關活動內的資料流程。

## HDInsight 活動 - 常見問題集

### 問：HDInsight 支援哪些區域？

請參閱下列文章中的＜各地區上市情況＞一節：或 [HDInsight 定價詳細資料][hdinsight-supported-regions]。

### 問：隨選 HDInsight 叢集使用哪一個區域？

隨選 HDInsight 叢集會建立在存有您指定用來使用叢集之儲存體的位置。

### 問：如何讓其他儲存體帳戶與 HDInsight 叢集產生關聯？

如果使用專屬 HDInsight 叢集 (BYOC - 自備叢集 (Bring Your Own Cluster))，請參閱下列主題：

- [搭配使用 HDInsight 叢集與替代儲存體帳戶和中繼存放區][hdinsight-alternate-storage]
- [搭配使用其他儲存體帳戶與 HDInsight Hive][hdinsight-alternate-storage-2]

如果您使用 Data Factory 服務所建立的隨選叢集，則需要指定 HDInsight 連結服務的其他儲存體帳戶，讓 Data Factory 服務代表您註冊它們。在隨選連結服務的 JSON 定義中，請使用 **additionalLinkedServiceNames** 屬性指定替代的儲存體帳戶，如下列 JSON 片段所示：
 
	{
	    "name": "MyHDInsightOnDemandLinkedService",
	    "properties":
	    {
	        "type": "HDInsightOnDemandLinkedService",
	        "clusterSize": 1,
	        "timeToLive": "00:01:00",
	        "linkedServiceName": "LinkedService-SampleData",
	        "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
	    }
	} 

在上述範例中，otherLinkedServiceName1 和 otherLinkedServiceName2 連結服務的定義，包含 HDInsight 叢集存取替代儲存體帳戶所需的認證。

## 預存程序活動 - 常見問題集
### 預存程序活動支援哪些資料來源？
預存程序活動目前只支援 Azure SQL Database。

## 配量 - 常見問題集

### 如何重新執行配量？
您可以利用下列方式之一來重新執行配量：

- 針對入口網站中的配量，在 [**資料配量**] 刀鋒視窗的命令列中按一下 [**執行**]。 
- 在配量的狀態設為 **PendingExecution** 的情況下，執行 **Set-AzureDataFactorySliceStatus** Cmdlet。   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

如需 Cmdlet 的詳細資料，請參閱 [Set-AzureDataFactorySliceStatus][set-azure-datafactory-slice-status]。

### 處理配量需要多久的時間？
1. 在您 Data Factory 的 **Data Factory** 刀鋒視窗中，按一下 [**資料集**] 磚。
2. 在 [**資料集**] 刀鋒視窗中，按一下特定資料集。
3. 從 [**資料表**] 刀鋒視窗的 [**最近配量**] 清單中，選取您感興趣的配量。
4. 從 [**資料配量**] 刀鋒視窗的 [**活動執行**] 清單中，按一下活動執行。 
5. 在 [**活動執行詳細資料**] 刀鋒視窗中，按一下 [**屬性**] 磚。 
6. 您應該會看到 [持續時間] 欄位與值。這是處理配量所需的時間。   

### 如何停止執行中配量？
如果需要停止執行管線，可以使用 [Suspend-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx) Cmdlet。目前，擱置管線並不會停止正在進行的配量執行。一旦進行中的執行完成，就不會再挑選任何額外的配量。

如果真的想要立即停止所有執行作業，唯一的方法就是刪除管線，然後再重新建立。如果您選擇刪除管線，則「不」需要刪除管線所使用的資料表和連結服務。



[image-rerun-slice]: ./media/data-factory-faq/rerun-slice.png

[adfgetstarted]: data-factory-get-started.md
[adf-introduction]: data-factory-introduction.md
[adf-troubleshoot]: data-factory-troubleshoot.md
[data-factory-editor]: data-factory-editor.md
[datafactory-getstarted]: data-factory-get-started.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/azure/dn835095.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 

<!---HONumber=62-->