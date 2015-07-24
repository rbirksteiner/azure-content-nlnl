<properties 
	pageTitle="使用 DocumentDB 和 HDInsight 執行 Hadoop 工作 | Azure" 
	description="了解如何使用 DocumentDB 和 Azure HDInsight 來執行簡單的 Hive、Pig 和 MapReduce 工作。"
	services="documentdb" 
	authors="andrewhoh" 
	manager="jhubbard" 
	editor="mimig"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="anhoh"/>

#<a name="DocumentDB-HDInsight"></a>使用 DocumentDB 和 HDInsight 執行 Hadoop 工作

本教學課程會示範如何在 Azure HDInsight 上，使用 DocumentDB 的 Hadoop 連接器來執行 [Apache Hive][apache-hive]、[Apache Pig][apache-pig] 和 [Apache Hadoop][apache-hadoop] MapReduce 工作。DocumentDB 的 Hadoop 連接器可讓 DocumentDB 同時做為 Hive、Pig 和 MapReduce 工作的來源與接收。本教學課程將使用 DocumentDB 同時作為 Hadoop 工作的資料來源和目的地。 

完成本教學課程後, 您將能夠回答下列問題：

- 如何使用 Hive、Pig 或 MapReduce 工作從 DocumentDB 載入資料？
- 如何使用 Hive、Pig 或 MapReduce 工作將資料儲存在 DocumentDB 中？

我們建議使用者從觀看下列影片開始入門，在影片中我們會使用 DocumentDB 和 HDInsight 執行 Hive 工作。

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

接著回到本文，您將在本文中找到有關如何在 DocumentDB 資料上執行分析工作的完整詳細資訊。

> [AZURE.TIP] 本教學課程假設您先前已有使用 Apache Hadoop、Hive 和/或 Pig 的經驗。如果您不熟悉 Apache Hadoop、Hive 和 Pig，建議您造訪 [Apache Hadoop 文件][apache-hadoop-doc]。本教學課程也假設您先前已有使用 DocumentDB 的經驗，且擁有 DocumentDB 帳戶。如果您不熟悉 DocumentDB 或沒有 DocumentDB 帳戶，請造訪我們的[開始使用][getting-started]頁面。

是否沒有時間完成本教學課程，而只想取得 Hive、Pig 和 MapReduce 的完整範例 PowerShell 指令碼？沒問題，您可以在[這裡][documentdb-hdinsight-samples]取得。下載項目也包含這些範例的 hql、pig 和 java 檔案。

## <a name="Prerequisites"></a>必要條件
在依照本教學課程中的指示進行之前，請先確定您已備妥下列項目：

- DocumentDB 帳戶、資料庫和包含文件的集合。如需詳細資訊，請參閱 [開始使用 DocumentDB][getting-started]。
- 輸送量。從 HDInsight 讀取和寫入將會計入您容量單位 (CU) 的配置要求單位。如需詳細資訊，請參閱 [佈建輸送量、要求單位和資料庫作業][documentdb-manage-throughput]。
- 每個輸出集合額內額外預存程序的容量。預存程序可用來傳送產生的文件。如需詳細資訊，請參閱 [集合及佈建的輸送量][documentdb-manage-document-storage]。
- 由 Hive、Pig 或 MapReduce 工作產生的文件容量。如需詳細資訊，請參閱 [管理 DocumentDB 容量和效能][documentdb-manage-collections]。
- [*選擇性*] 額外集合的容量。如需詳細資訊，請參閱 [佈建的文件儲存體和索引額外負荷][documentdb-manage-document-storage]。
	
> [AZURE.WARNING] 為避免在任何工作期間建立新的集合，您可以將結果列印至 stdout、將輸出儲存到您的 WASB 容器，或指定現有的集合。如果是指定現有的集合，則會在集合內部建立新的文件，且只有當  *ids* 發生衝突時，現有的文件才會受到影響。**連接器會自動覆寫發生識別碼衝突的現有文件**。將更新插入選項設定為 false 便可關閉這項功能。如果更新插入為 false，但發生衝突，則 Hadoop 工作將會失敗。回報識別碼衝突錯誤。

## <a name="CreateStorage"></a>步驟 1：建立 Azure 儲存體帳戶

> [AZURE.IMPORTANT] 如果您「**已經**」具有 Azure 儲存體帳戶，且願意在該帳戶內建立新的 Blob 容器，則您可以跳到 [步驟 2：建立自訂的 HDInsight 叢集](#ProvisionHDInsight)。

Azure HDInsight 會使用 Azure Blob 儲存體來儲存資料。其稱為  *WASB* 或  *Azure Storage - Blob*。WASB 是 Microsoft 在 Azure Blob 儲存體上的 HDFS 實作。如需詳細資訊，請參閱 [搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。

佈建 HDInsight 叢集時，您可以指定 Azure 儲存體帳戶。將該帳戶的特定 Blob 儲存體容器指定為預設檔案系統，如同在 HDFS 中一般。依預設，系統會在與您指定儲存體帳戶的相同資料中心內佈建 HDInsight 叢集。

**建立 Azure 儲存體帳戶**

1. 登入 [Azure 管理入口網站][azure-management-portal]。
	
	> [AZURE.NOTE] Azure 管理入口網站目前支援 Azure HDInsight，雖然 Azure DocumentDB 僅存在於 Azure 預覽入口網站中。

2. 按一下左下角的 [**新增**]，指向 [**資料服務**] 及 [**儲存體**]，然後按一下 [**快速建立**]。
	![Azure portal where you can use Quick Create to set up a new storage account.][image-storageaccount-quickcreate]

3. 輸入 [**URL**]，並依序選取 [**位置**] 和 [**複寫**] 值，然後按一下 [**建立儲存體帳戶**]。不支援同質群組。 
	
	您將在儲存體清單中看見新的儲存體帳戶。

	> [AZURE.IMPORTANT] 為了達到最佳效能，請確定您的儲存體帳戶、HDInsight 叢集及 DocumentDB 帳戶位於相同的 Azure 區域。支援這三種服務的 Azure 區域如下：**東亞**]、[**東南亞**]、[**北歐**]、[**西歐**]、[**美國東部**] 和 [**美國西部**]。

4. 等候新儲存體帳戶的 [**狀態**] 變更為 [**線上**]。

## <a name="ProvisionHDInsight"></a>步驟 2：建立自訂的 HDInsight 叢集
本教學課程會使用 Azure 管理入口網站的指令碼動作，來自訂您的 HDInsight 叢集。在本教學課程中，我們將使用 Azure 管理入口網站來建立自訂的叢集。如需有關如何使用 PowerShell 指令程式或 HDInsight .NET SDK 的指示，請參閱[使用指令碼動作來自訂 HDInsight 叢集][hdinsight-custom-provision]一文。

1. 登入 [Azure 管理入口網站][azure-management-portal]。您可能已經在先前步驟中登入。

2. 按一下頁面底部的 [**+ 新增**]，然後依序按一下 [**資料服務**]、[**HDInsight**] 和 [**自訂建立**]。

3. 在 [**叢集詳細資料**] 頁面上，輸入或選擇下列值：

	![Provide Hadoop HDInsight initial cluster details][image-customprovision-page1]

	<table border='1'>
		<tr><th>屬性</th><th>值</th></tr>
		<tr><td>叢集名稱</td><td>為叢集命名。<br/>
			DNS 名稱的開頭與結尾都必須是英數字元，且可包含連字號。<br/>
			欄位必須是介於 3 到 63 個字元的字串。</td></tr>
		<tr><td>訂閱名稱</td>
			<td>如果您有多個 Azure 訂閱，請選取<strong>步驟 1</strong> 中儲存體帳戶的對應訂閱。 </td></tr>
		<tr><td>叢集類型</td>
			<td>在叢集類型中，請選取 [<strong>Hadoop</strong>]。</td></tr>
		<tr><td>作業系統</td>
			<td>在作業系統中，請選取 [<strong>Windows Server 2012 R2 Datacenter</strong>]。</td></tr>
		<tr><td>HDInsight 版本</td>
			<td>選擇版本。 </br>選取 [<Strong>HDInsight 3.1 版</strong>]。</td></tr>
		</table>

	<p>輸入或選取如資料表中所示的值，然後按一下向右箭頭。</p>

4. 在 [**設定叢集**] 頁面上，輸入或選取下列值：

	<table border="1">
	<tr><th>名稱</th><th>值</th></tr>
	<tr><td>資料節點</td><td>您要部署的資料節點數。 </br>記下 HDInsight 的資料節點會與效能和定價相關聯。</td></tr>
	<tr><td>區域/虛擬網路</td><td>選擇與您新建立<strong>儲存體帳戶</strong>和 <strong>DocumentDB 帳戶</strong>相同的區域。 </br> 使用 HDInsight 時，儲存體帳戶必須位於相同的區域中。後續進行設定時，您只能選擇此處指定之相同區域中的儲存體帳戶。</td></tr>
	</table>
	
    按一下向右箭頭。

5. 在 [**Configure Cluster User**] 頁面上，提供下列值：

    <table border='1'>
		<tr><th>屬性</th><th>值</th></tr>
		<tr><td>使用者名稱</td>
			<td>指定 HDInsight 叢集使用者名稱。</td></tr>
		<tr><td>密碼/確認密碼</td>
			<td>指定 HDInsight 叢集使用者密碼。</td></tr>
	</table>
	
    按一下向右箭頭。
    
6. 在 [**儲存體帳戶**] 頁面上，提供下列值：

	![Provide storage account for Hadoop HDInsight cluster][image-customprovision-page4]

	<table border='1'>
		<tr><th>屬性</th><th>值</th></tr>
		<tr><td>儲存體帳戶</td>
			<td>指定將作為 HDInsight 叢集之預設檔案系統的 Azure 儲存體帳戶。您可以從三個選項中擇一使用：使用現有儲存體、建立新儲存體，或使用其他訂閱的儲存體</br></br>
			請選取 [<strong>使用現有儲存體</strong>]。
			</td>
			</td></tr>
		<tr><td>帳戶名稱</td>
			<td>
			在 [<strong>帳戶名稱</strong>] 中，選取在<strong>步驟 1</strong> 中所建立的帳戶。下拉式清單只會針對位於您選擇要佈建叢集之相同資料中心的相同 Azure 訂閱，列出其下的儲存體帳戶。
			</td></tr>
		<tr><td>預設容器</td>
			<td>指定儲存體帳戶上的預設容器作為 HDInsight 叢集的預設檔案系統使用。如果您在 [<strong>儲存體帳戶</strong>] 欄位中選擇了 [<strong>使用現有儲存體</strong>]，且該帳戶中沒有現有的容器，則系統便會依預設建立容器，容器名稱會與叢集名稱相同。如果已有使用叢集名稱的容器存在，則會為容器名稱加上序號。
        </td></tr>
		<tr><td>其他儲存體帳戶</td>
			<td>HDInsight 支援多個儲存體帳戶。叢集可使用的其他儲存體帳戶並沒有數量上的限制。但如果您使用管理入口網站建立叢集，則會因為 UI 的限制而限定為七個帳戶。您所指定的每個其他儲存體帳戶都會在精靈上另外新增一個 [儲存體帳戶] 頁面，您可在此指定帳戶資訊。</td></tr>
	</table>

	按一下向右箭頭。

7. 若要在建立叢集時自訂您的叢集，請在 [**指令碼動作**] 頁面上，按一下 [**加入指令碼動作**] 以提供有關您將執行的 PowerShell 指令碼詳細資料。PowerShell 指令碼會在叢集建立期間，將 DocumentDB Hadoop 連接器安裝到您的 HDInsight 叢集。
	
	![Configure Script Action to customize an HDInsight cluster][image-customprovision-page5]

	<table border='1'>
		<tr><th>屬性</th><th>值</th></tr>
		<tr><td>名稱</td>
			<td>指定指令碼動作的名稱。</td></tr>
		<tr><td>指令碼 URI</td>
			<td>對自訂叢集所叫用的指令碼指定 URI。</br></br>
			請輸入： </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v02.ps1</strong>。</td></tr>
		<tr><td>節點類型</td>
			<td>指定執行自訂指定碼的節點。您可以選擇 [<b>所有節點</b>]、[<b>僅限前端節點</b>] 或 [<b>僅限背景工作節點</b>]。</br></br>
			請選取 [<strong>所有節點</strong>]。</td></tr>
		<tr><td>參數</td>
			<td>如果指令碼要求，請指定參數。</br></br>
			<strong>不需要參數</strong>。</td></tr>
	</table>

	按一下核取記號以完成建立叢集。

## <a name="InstallCmdlets"></a>步驟 3：安裝並設定 Azure PowerShell

1. 安裝 Azure PowerShell。您可以在[這裡][powershell-install-configure]找到指示。

	> [AZURE.NOTE] 或者，您可以使用 HDInsight 的線上 Hive 編輯器 (僅限 Hive 查詢)。若要這樣做，請登入 [Azure 管理入口網站][azure-management-portal]，按一下左側窗格中的 **HDInsight** 以檢視您的 HDInsight 叢集清單。按一下您要執行 Hive 查詢的叢集，然後按一下 [**查詢主控台**]。

2. 開啟 Azure PowerShell 整合式指令碼環境：
	- 在執行 Windows 8 或 Windows Server 2012 或更新版本的電腦上，您可以使用內建搜尋。在 [開始] 畫面中，輸入 **powershell ise**，並按一下 **Enter**。 
	- 在執行比 Windows 8 或 Windows Server 2012 更舊版本的電腦上，使用 [開始] 功能表。在 [開始] 功能表中，在搜尋方塊中輸入**命令提示字元**，然後按一下結果清單中的 [**命令提示字元**]。在命令提示字元中，輸入 **powershell_ise**，並按一下 **Enter**。

3. 新增您的 Azure 帳戶。
	1. 在主控台窗格中，輸入 **Add-AzureAccount**，並按一下  **Enter**。 
	2. 輸入與您 Azure 訂閱相關聯的電子郵件地址，並按一下 [**繼續**]。 
	3. 輸入您 Azure 訂閱的密碼。 
	4. 按一下 [**登入**]。

4. 下圖將指出 Azure PowerShell 指令碼環境的重要部分。 

	![Diagram for Azure PowerShell][azure-powershell-diagram]

## <a name="RunHive"></a>步驟 4：使用 DocumentDB 和 HDInsight 執行 Hive 工作

> [AZURE.IMPORTANT] 以 < > 表示的所有變數都必須使用組態設定進行填寫。

1. 在 [PowerShell 指令碼] 窗格中設定下列變數。

		# Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide the HDInsight cluster name where you want to run the Hive job.
		$clusterName = "<HDInsightClusterName>"

2. 
	<p>首先我們要建構查詢字串。我們將撰寫執行下列動作的 Hive 查詢：接受所有文件從 DocumentDB 集合系統產生的時間戳記 (_ts) 和唯一識別碼 (_rid) ，並計算所有文件 (以分鐘為單位)，然後將結果存回新的 DocumentDB 集合。 </p>

    <p>首先，我們要在 DocumentDB 集合中建立 Hive 資料表。將下列程式碼片段加入 [PowerShell 指令碼] 窗格中 # 1 的程式碼片段<strong>後面</strong>。請確定包含選擇性的 DocumentDB.query 參數，將文件整理成只有 _ts 和 _rid。 </p>

    > [AZURE.NOTE] **命名 DocumentDB.inputCollections 未發生錯誤。**沒錯，我們允許加入多個集合做為輸入： </br>
    '*DocumentDB.inputCollections*' = '*<DocumentDB 輸入集合名稱1>*,*<DocumentDB 輸入集合名稱 2>*' </br> 不使用空格，只使用單一逗點分隔的集合名稱。

		# Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
		$queryStringPart1 = "drop table DocumentDB_timestamps; "  + 
                            "create external table DocumentDB_timestamps(id string, ts INT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " + 
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "
 
3.  接下來，我們將建立輸出集合的 Hive 資料表。輸出文件屬性包括月、日、小時、分鐘和總發生次數。

	> [AZURE.NOTE] **再度，命名 DocumentDB.outputCollections 未發生錯誤。**沒錯，我們允許加入多個集合做為輸出： </br>
    '*DocumentDB.outputCollections*' = '*<DocumentDB 輸出集合名稱 1>*,*<DocumentDB 輸出集合名稱 2>*' </br> 不使用空格，只使用單一逗點分隔的集合名稱。 </br></br>
    文件將會是跨多個集合的分散式循環配置資源。第一批文件會儲存在一個集合中，然後第二批文件會儲存在下一個集合中，以此類推。

		# Create a Hive table for the output data to DocumentDB.
	    $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " + 
                              "tblproperties ( " + 
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. 最後，我們可以依照月、日、小時和分鐘計算文件，並將結果插回輸出 Hive 資料表中。

		# GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. 新增下列指令碼片段，從先前的查詢建立 Hive 工作定義。

		# Create a Hive job definition.
		$queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	您也可以使用 -File 參數來指定 HDFS 上的 HiveQL 指令碼檔案。

6. 新增下列指令碼片段來儲存開始時間，並提交 Hive 工作。

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. 新增下列指令碼來等待 Hive 工作完成：

		# Wait for the Hive job to complete.
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. 新增下列項目來列印標準輸出及開始和結束時間。

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **執行**新的指令碼！**按一下**綠色的執行按鈕。

10. 查看結果。登入 [Azure 預覽入口網站][azure-preview-portal]。 
	1. 按一下左側面板上的 [<strong>瀏覽</strong>]。 </br>
	2. 按一下 [瀏覽] 面板右上方的 [<strong>所有項目</strong>]。 </br>
	3. 尋找並按一下 [<strong>DocumentDB 帳戶</strong>]。 </br>
	4. 接下來，依序尋找您的 [<strong>DocumentDB 帳戶</strong>]、[<strong>DocumentDB 資料庫</strong>] 和與 Hive 查詢中指定輸出集合相關聯的 [<strong>DocumentDB 集合</strong>]。</br>
	5. 最後，按一下 [<strong>開發人員工具</strong>] 底下的 <strong>Document Explorer</strong>。</br></p>

	您將會看到 Hive 查詢的結果。

	![Hive query results][image-hive-query-results]

## <a name="RunPig"></a>步驟 5：使用 DocumentDB 和 HDInsight 執行 Pig 工作

> [AZURE.IMPORTANT] 以 < > 表示的所有變數都必須使用組態設定進行填寫。

1. 在 [PowerShell 指令碼] 窗格中設定下列變數。

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>首先我們要建構查詢字串。我們將撰寫執行下列動作的 Pig 查詢：接受所有文件從 DocumentDB 集合系統產生的時間戳記 (_ts) 和唯一識別碼 (_rid) ，並計算所有文件 (以分鐘為單位)，然後將結果存回新的 DocumentDB 集合。</p>
    <p>首先，將文件從 DocumentDB 載入 HDInsight。將下列程式碼片段加入 [PowerShell 指令碼] 窗格中 # 1 的程式碼片段<strong>後面</strong>。請務必將 DocumentDB 查詢加入選擇性的 DocumentDB 查詢參數，以將文件整理成只有 _ts 和 _rid。</p>

    > [AZURE.NOTE] 沒錯，我們允許在一筆輸入中加入多個集合： </br>
    '*<DocumentDB 輸入集合名稱 1>*,*<DocumentDB 輸入集合名稱 2>*'</br> 不使用空格，只使用單一逗點分隔的集合名稱。 </b>

	文件將會是跨多個集合的分散式循環配置資源。第一批文件會儲存在一個集合中，然後第二批文件會儲存在下一個集合中，以此類推。

		# Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  接下來，我們將依照月、日、小時、分鐘和總發生次數來計算文件。

		# GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. 最後，將結果存回新的輸出集合。

    > [AZURE.NOTE] 沒錯，我們允許在一筆輸出中加入多個集合： </br>
    '<DocumentDB 輸出集合名稱 1>,<DocumentDB 輸出集合名稱 2>'</br> 不使用空格，只使用單一逗點分隔的集合名稱。</br>
    文件將會是跨多個集合的分散式循環配置資源。第一批文件會儲存在一個集合中，然後第二批文件會儲存在下一個集合中，以此類推。

		# Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. 新增下列指令碼片段，從先前的查詢建立 Pig 工作定義。

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

	您也可以使用 -File 參數，在 HDFS 上指定一個 Pig 指令碼檔案。

6. 新增下列指令碼片段來儲存開始時間，並提交 Pig 工作。

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. 新增下列指令碼來等待 Pig 工作完成：

		# Wait for the Pig job to complete.
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. 新增下列項目來列印標準輸出及開始和結束時間。

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
		
9. **執行**新的指令碼！**按一下**綠色的執行按鈕。

10. 查看結果。登入 [Azure 預覽入口網站][azure-preview-portal]。 
	1. 按一下左側面板上的 [<strong>瀏覽</strong>]。 </br>
	2. 按一下 [瀏覽] 面板右上方的 [<strong>所有項目</strong>]。 </br>
	3. 尋找並按一下 [<strong>DocumentDB 帳戶</strong>]。 </br>
	4. 接下來，依序尋找您的 [<strong>DocumentDB 帳戶</strong>]、[<strong>DocumentDB 資料庫</strong>] 和與 Pig 查詢中指定輸出集合相關聯的 [<strong>DocumentDB 集合</strong>]。</br>
	5. 最後，按一下 [<strong>開發人員工具</strong>] 底下的 <strong>Document Explorer</strong>。</br></p>

	您將會看到 Pig 查詢的結果。

	![Pig query results][image-pig-query-results]

## <a name="RunMapReduce"></a>步驟 6：使用 DocumentDB 和 HDInsight 執行 MapReduce 工作

1. 在 [PowerShell 指令碼] 窗格中設定下列變數。
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
		
2. 我們將執行 MapReduce 工作，針對您 DocumentDB 集合中每一個文件屬性的發生次數進行計算。在上述程式碼片段**之後**加入此指令碼程式碼片段。

		# Define the MapReduce job.
		$TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

	> [AZURE.NOTE] TallyProperties-v01.jar 隨附於 DocumentDB Hadoop 連接器的自訂安裝。

3. 新增下列命令來提交 MapReduce 工作。

		# Save the start time and submit the job.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	除了 MapReduce 工作定義，您也提供想要用來執行 MapReduce 工作的 HDInsight 叢集名稱，以及認證。Start-AzureHDInsightJob 是非同步呼叫。若要檢查工作是否完成，請使用 *Wait-AzureHDInsightJob*指令程式。

4. 新增下列命令來檢查 MapReduce 工作執行時有無任何錯誤。	
	
		# Get the job output and print the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green 

5. **執行**新的指令碼！**按一下**綠色的執行按鈕。

6. 查看結果。登入 [Azure 預覽入口網站][azure-preview-portal]。 
	1. 按一下左側面板上的 [<strong>瀏覽</strong>]。
	2. 按一下 [瀏覽] 面板右上方的 [<strong>所有項目</strong>]。
	3. 尋找並按一下 [<strong>DocumentDB 帳戶</strong>]。
	4. 接下來，依序尋找您的 [<strong>DocumentDB 帳戶</strong>]、[<strong>DocumentDB 資料庫</strong>] 和與 MapReduce 工作中指定輸出集合相關聯的 [<strong>DocumentDB 集合</strong>]。
	5. 最後，按一下 [<strong>開發人員工具</strong>] 底下的 <strong>Document Explorer</strong>。

	您將會看到 MapReduce 工作的結果。

	![MapReduce query results][image-mapreduce-query-results]

## <a name="NextSteps"></a>後續步驟

恭喜！您剛剛使用 Azure DocumentDB 和 HDInsight 執行您的第一個 Hive、Pig 和 MapReduce 工作。 

我們已開放 Hadoop 連接器的原始碼。如果您有興趣，您可以在 [GitHub][documentdb-github] 上發表意見。

若要深入了解，請參閱下列文章：

- [使用 Documentdb 開發 Java 應用程式][documentdb-java-application]
- [在 HDInsight 上開發 Hadoop 的 Java MapReduce 程式][hdinsight-develop-deploy-java-mapreduce]
- [開始在 HDInsight 中使用 Hadoop 搭配 Hive 以分析行動電話使用][hdinsight-get-started]
- [搭配 HDInsight 使用 MapReduce][hdinsight-use-mapreduce]
- [搭配 HDInsight 使用 Hive][hdinsight-use-hive]
- [搭配 HDInsight 使用 Pig][hdinsight-use-pig]
- [使用指令碼動作來自訂 HDInsight 叢集][hdinsight-hadoop-customize-cluster] 

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-management-portal]: https://manage.windowsazure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png
[azure-preview-portal]: https://portal.azure.com/

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-manage-collections]: documentdb-manage.md#Collections
[documentdb-manage-document-storage]: documentdb-manage.md#IndexOverhead
[documentdb-manage-throughput]: documentdb-manage.md#ProvThroughput

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight-get-started.md 
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-customprovision-page4]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page4.png
[image-customprovision-page5]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page5.png
[image-storageaccount-quickcreate]: ./media/documentdb-run-hadoop-with-hdinsight/storagequickcreate.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../install-configure-powershell.md

<!--HONumber=49--> 