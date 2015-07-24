<properties
	pageTitle="開始使用 HDInsight 的 Hadoop 模擬器 |Microsoft Azure"
	description="使用安裝的模擬器搭配 MapReduce 教學課程和其他範例，深入了解 Hadoop 生態系統。HDInsight Emulator 的運作方式類似 Hadoop 沙箱。"
	keywords="emulator,hadoop ecosystem,hadoop sandbox,mapreduce tutorial"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="05/07/2015"
	ms.author="nitinme"/>

# 透過 HDInsight Emulator (Hadoop 沙箱) 開始使用 Hadoop 生態系統

本教學課程協助您開始使用 Microsoft HDInsight Emulator for Azure (先前稱為 HDInsight Server Developer Preview) 中的 Hadoop 叢集。HDInsight Emulator 提供 Hadoop 生態系統 Azure HDInsight 的相同元件。如需詳細資訊，包括部署的版本資訊，請參閱 [Azure HDInsight 提供 Hadoop 的什麼版本？](hdinsight-component-versioning.md) (英文)。

安裝模擬器後，請遵循字數統計的 MapReduce 教學課程，然後執行範例。

> [AZURE.NOTE]HDInsight Emulator 只包含 Hadoop 叢集。不含 HBase 或 Storm。


HDInsight Emulator 提供本機部署環境，與 Hadoop 沙箱極為相似。如果您熟悉 Hadoop，可以透過 Hadoop 分散式檔案系統 (HDFS) 開始使用 HDInsight Emulator。在 HDInsight 中，預設檔案系統是 Azure Blob 儲存體。所以您最終會使用 Azure Blob 儲存體來開發工作。若要搭配使用 Azure Blob 儲存體和 HDInsight Emulator，您必須變更模擬器的組態。

> [AZURE.NOTE]HDInsight Emulator 只會使用一個節點部署。


## 必要條件
開始進行本教學課程之前，您必須具備下列條件：

- HDInsight Emulator 需要 64 位元版本 Windows。必須滿足下列其中一項需求：

	- Windows 7 Service Pack 1
	- Windows Server 2008 R2 Service Pack 1
	- Windows 8
	- Windows Server 2012

- **Azure PowerShell** (英文)。請參閱[安裝和使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。


##<a name="install"></a>安裝 HDInsight Emulator

可以透過 Microsoft Web Platform Installer 安裝 Microsoft HDInsight Emulator。

> [AZURE.NOTE]HDInsight Emulator 目前僅支援英文版作業系統。如果已安裝舊版的 Emulator，您必須從 [控制台/程式和功能] 中解除安裝下列兩個元件，再安裝最新版的 Emulator：<ul> <li>Microsoft HDInsight Emulator for Azure 或 HDInsight Developer Preview，視何者已安裝</li> <li>Hortonworks Data Platform</li> </ul>


**安裝 HDInsight Emulator**

1. 開啟 Internet Explorer，然後瀏覽到 [Microsoft HDInsight Emulator for Azure 安裝頁面][hdinsight-emulator-install]。
2. 按一下 [立即安裝]。
3. 頁面底端提示 HDINSIGHT.exe 安裝時，按一下 [執行]。
4. 按一下彈出的 [使用者帳戶控制] 視窗中出現的 [是] 按鈕完成安裝。[Web Platform Installer] 視窗隨即顯示。
6. 按一下頁面底部的 [安裝]。
7. 按一下 [我接受] 同意授權條款。
8. 確認 Web Platform Installer 顯示 [**已成功安裝下列產品**]，然後按一下 [**完成**]。
9. 按一下 [結束] 關閉 Web Platform Installer 視窗。

**驗證 HDInsight Emulator 安裝**

安裝程式應該已經在桌面上安裝 3 個圖示。3 個圖示的連結如下：

- **Hadoop 命令列** - 在 HDInsight Emulator 中執行 MapReduce、Pig 和 Hive 工作的 Hadoop 命令提示字元。

- **Hadoop NameNode 狀態** - NameNode 維持 HDFS 中所有檔案的樹狀目錄。它也會追蹤 Hadoop 叢集中保留的所有檔案相關的資料。用戶端能夠與 NameNode 進行通訊，以便了解所有檔案的資料節點儲存於何處。

- **Hadoop Yarn 狀態** - 對於叢集之中的節點配置 MapReduce 工作的工作追蹤器。

安裝程式應該也已經安裝多個本機服務。以下是 [服務] 視窗的螢幕擷取畫面：

![Hadoop 生態系統服務列在模擬器視窗中。][image-hdi-emulator-services]

依預設不會啟動 HDInsight Emulator 的相關服務。若要啟動服務，請從 Hadoop 命令列執行 C:\\hdp 底下的 **start_local_hdp_services.cmd** (預設位置)。若要在電腦重新啟動之後自動啟動服務，請執行 **set-onebox-autostart.cmd**。

對於安裝和執行 HDInsight Emulator 的已知問題，請參閱 [HDInsight Emulator 版本資訊](hdinsight-emulator-release-notes.md)。安裝記錄檔位於 **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**。

##<a name="vstools"></a>使用 Emulator 與 HDInsight Tools for Visual Studio

您可以使用適用於 Visual Studio 的 HDInsight 工具來連線到 HDInsight Emulator。如需如何在 Azure 上透過 HDInsight 叢集使用 Visual Studio 工具的詳細資訊，請參閱[開始使用 HDInsight Hadoop Tools for Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)。

### 安裝使用於 Emulator 的 HDInsight 工具

如需如何安裝 HDInsight Visual Studio 工具的指示，請參閱[這裡](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md#installation)。

### 連接到 HDInsight Emulator

1. 開啟 Visual Studio。
2. 從 [**檢視**] 功能表中，按一下 [**伺服器總管**] 以開啟 [伺服器總管] 視窗。
3. 展開 [**Azure**]，以滑鼠右鍵按一下 [**HDInsight**]，然後按一下 [**連接到 HDInsight Emulator**]。

	 ![Visual Studio 檢視：連接到功能表上的 HDInsight Emulator。](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.png)

4. 在 [連接到 HDInsight Emulator] 對話方塊中，確認 WebHCat、HiveServer2 及 WebHDFS 端點的值，然後按 [**下一步**]。如果您未對 Emulator 的預設組態進行任何變更，則預設填入的值應該會有作用。如果您已進行任何變更，請在對話方塊中更新值，然後按 [下一步]。

	![連接到具有設定的 HDInsight Emulator 對話方塊。](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.dialog.png)

5. 成功建立連線之後，請按一下 [**完成**]。現在您應該會在 [伺服器總管] 中看見 HDInsight Emulator。

	![顯示已連接 HDInsight 本機模擬器 (Hadoop 沙箱) 的伺服器總管。](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.vs.connected.png)

成功建立連線之後，您可以透過 Emulator 使用 HDInsight VS 工具，就像您透過 Azure HDInsight 叢集使用一般。如需如何透過 Azure HDInsight 叢集使用 VS 工具的指示，請參閱[使用 HDInsight Hadoop Tools for Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)。

## 疑難排解：連接 HDInsight 工具與 HDInsight Emulator

1. 連接到 HDInsight Emulator 時，即使對話方塊顯示 HiveServer2 已成功連接，您還是必須在 C:\\hdp\\hive-*version*\\conf\\hive-site.xml 的 Hive 組態檔中，手動將 **hive.security.authorization.enabled** 屬性設為 **false**，然後重新啟動本機 Emulator。HDInsight Tools for Visual Studio 只會在您預覽資料表的前 100 列的情況下，連接到 HiveServer2。如果您不打算使用此類查詢，可以保留 Hive 組態的現狀。

2. 如果您在執行 HDInsight Emulator 的電腦上使用動態 IP 配置 (DHCP)，可能需要更新 C:\\hdp\\hadoop-*version*\\etc\\hadoop\\core-site.xml，並且將 **hadoop.proxyuser.hadoop.hosts** 屬性的值變更為 (*)。如此可以讓 Hadoop 使用者從所有主機進行連接，以模擬您在 Visual Studio 中輸入的使用者。

		<property>
			<name>hadoop.proxyuser.hadoop.hosts</name>
			<value>*</value>
		</property>

3. 當 Visual Studio 嘗試連接到 WebHCat 服務時，您可能會收到錯誤 (「錯誤：找不到工作 job_XXXX_0001」)。若是如此，您必須重新啟動 WebHCat 服務，然後再試一次。若要重新啟動 WebHCat 服務，請啟動 [**服務**] MMC、以滑鼠右鍵按一下 [**Apache Hadoop Templeton**] (這是 WebHCat 服務的舊名稱)，然後按一下 [**重新啟動**]。

##<a name="runwordcount"></a>字數統計 MapReduce 教學課程

您現已在工作站上設定 HDInsight Emulator，請嘗試本 MapReduce 教學課程來測試安裝。您將先上傳一些資料檔到 HDFS，然後執行字數統計 MapReduce 工作，以統計這些檔案中特定單字出現的次數。

字數統計 MapReduce 程式已封裝在 *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar* 中。這個 jar 檔位於 *C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\hadoop\\mapreduce* 資料夾。

統計字數的 MapReduce 工作使用兩個引數：

- 輸入資料夾。您將會使用 *hdfs://localhost/user/HDIUser* 做為輸入資料夾。- 輸出資料夾。您將會使用 *hdfs://localhost/user/HDIUser/WordCount_Output* 做為輸出資料夾。輸出資料夾不可以是現有資料夾，否則 MapReduce 工作將失敗。如果您要第二次執行 MapReduce 工作，您必須指定不同的輸出資料夾，或刪除現有的輸出資料夾。
**執行字數統計 MapReduce 工作**

1. 在桌面上，按兩下 **Hadoop 命令列** 開啟 Hadoop 命令列視窗。目前資料夾應該是：

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	如果不是，請執行下列命令：

		cd %hadoop_home%

2. 執行下列 Hadoop 命令建立 HDFS 資料夾來儲存輸入及輸出檔案：

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser

3. 執行下列 Hadoop 命令將一些本機文字檔複製到 HDFS：

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /user/HDIUser

4. 執行下列命令列出 /user/HDIUser 資料夾中的檔案：

		hadoop fs -ls /user/HDIUser

	您應該會看見下列檔案：

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -ls /user/HDIUser
		Found 4 items
		-rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5. 執行下列命令執行字數統計 MapReduce 工作：

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. 執行下列命令，從輸出檔列出含有「windows」的字數：

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	輸出應該是：

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

如需 Hadoop 命令的詳細資訊，請參閱 [Hadoop 命令手冊][hadoop-commands-manual] (英文)。

##<a name="rungetstartedsamples"></a> 分析範例 Web 記錄資料

HDInsight Emulator 安裝提供一些範例，讓使用者開始學習 Windows 上以 Apache Hadoop 為基礎的服務。這些範例涵蓋處理巨量資料集時通常需要的一些工作。這些範例是以上述 MapReduce 教學課程為基礎，可協助您熟悉 MapReduce 程式設計模型及其生態系統。

範例資料是按照處理 IIS 全球資訊網協會 (W3C) 記錄資料來編排。資料產生工具可用來建立各種大小的資料集並匯入到 HDFS 或 Azure Blob 儲存體。(如需詳細資訊，請參閱[對於 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage.md)。)MapReduce、Pig 或 Hive 工作接著可以在 Azure PowerShell 指令碼產生的資料頁面上執行。Pig 和 Hive 指令碼是 MapReduce 上的抽象層，最後會編譯成 MapReduce 程式。您可以執行一連串工作，以觀察使用這些不同的技術所產生的效果，以及資料大小如何影響處理工作的執行。

### 本節內容

- [IIS W3C 記錄資料案例](#scenarios)
- [載入範例 W3C 記錄資料](#loaddata)
- [執行 Java MapReduce 工作](#javamapreduce)
- [執行 Hive 工作](#hive)
- [執行 Pig 工作](#pig)
- [重建範例](#rebuild)

###<a name="scenarios"></a>IIS W3C 記錄資料案例

W3C 案例會產生三種大小的 IIS W3C 記錄資料並匯入到 HDFS 或 Azure Blob 儲存體：1MB (小)、500MB (中) 及 2GB (大)。它提供三種工作類型，並且以 C#、Java、Pig 及 Hive 實作各個類型。

- **totalhits** - 計算指定頁面的要求總數。
- **avgtime** - 計算對於各頁的一項要求所進行的平均時間 (秒數)。
- **errors** - 計算各頁、每小時、對於狀態為 404 或 500 的要求出現的錯誤數。

這些範例及其文件不提供關鍵 Hadoop 技術的深入研究或完整實作。使用的叢集只有一個節點，因此，對於這個版本，無法觀察新增更多節點的效果。

###<a name="loaddata"></a>載入範例 W3C 記錄資料

產生資料並匯入到 HDFS 均透過 Azure PowerShell 指令碼 importdata.ps1 完成。

**匯入範例 W3C 記錄資料**

1. 從桌面開啟 Hadoop 命令列。
2. 切換至 **C:\\hdp\\GettingStarted** 目錄。
3. 執行下列命令產生資料並匯入到 HDFS：

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted

	如果要將資料改為載入 Azure Blob 儲存體，請參閱[連線至 Azure Blob 儲存體](#blobstorage)。

4. 從 Hadoop 命令列執行下列命令，在 HDFS 列出匯入的檔案：

		hadoop fs -ls -R /w3c

	輸出應該類似如下範例：

		C:\hdp\GettingStarted>hadoop fs -ls -R /w3c
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:41 /w3c/input/large
		-rw-r--r--   1 username hdfs  543683503 2014-09-08 15:41 /w3c/input/large/data_w3c_large.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input/medium
		-rw-r--r--   1 username hdfs  272435159 2014-09-08 15:40 /w3c/input/medium/data_w3c_medium.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:39 /w3c/input/small
		-rw-r--r--   1 username hdfs    1058423 2014-09-08 15:39 /w3c/input/small/data_w3c_small.txt

5. 如果您要驗證檔案內容，請執行下列命令，將其中一個資料檔顯示到主控台視窗：

		hadoop fs -cat /w3c/input/small/data_w3c_small.txt

現在您已經建立資料檔案並匯入到 HDFS。您可以開始執行不同的 Hadoop 工作。

###<a name="javamapreduce"></a>執行 Java MapReduce 工作

MapReduce 是 Hadoop 的基本運算引擎。依預設，它是以 Java 實作，但是另外也有利用 .NET 及 C# Hadoop 串流的範例。執行 MapReduce 工作的語法：

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

jar 檔及原始程式檔位在 C:\\Hadoop\\GettingStarted\\Java 資料夾。

**執行 MapReduce 工作計算網頁點擊數**

1. 開啟 Hadoop 命令列。
2. 切換至 **C:\\hdp\\GettingStarted** 目錄。
3. 執行下列命令在資料夾存在的情況下移除輸出目錄。如果輸出資料夾已經存在，MapReduce 工作將失敗。

		hadoop fs -rm -r /w3c/output

3. 執行以下命令：

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	下表說明命令的元素：<table border="1"> <tr><td>參數</td><td>附註</td></tr> <tr><td>w3c_scenarios.jar</td><td>此 jar 檔案位於 C:\\hdp\\GettingStarted\\Java 資料夾。</td></tr> <tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>類型可以由下列其中一個項目取代：<ul> <li>microsoft.hadoop.w3c.AverageTimeTaken</li> <li>microsoft.hadoop.w3c.ErrorsByPage</li> </ul></td></tr> <tr><td>/w3c/input/small/data_w3c_small.txt</td><td>輸入檔案可以由下列項目取代：<ul> <li>/w3c/input/medium/data_w3c_medium.txt</li> <li>/w3c/input/large/data_w3c_large.txt</li> </ul></td></tr> <tr><td>/w3c/output</td><td>這是輸出資料夾名稱。</td></tr> </table>

4. 執行下列命令顯示輸出檔：

		hadoop fs -cat /w3c/output/part-00000

	輸出應該類似：

		c:\Hadoop\GettingStarted>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126

	Default.aspx 頁面有 3360 次點擊，依此類推。換成上表中建議的值來重新嘗試執行命令，並觀察輸出如何隨著工作類型和資料大小而改變。

### <a name="hive"></a>執行 Hive 工作
具備高超結構化查詢語言 (SQL) 技術的分析師可能對 Hive 查詢引擎感到很熟悉。它提供類似 SQL 的介面和 HDFS 的關聯式資料模型。Hive 採用稱為 HiveQL 的語言，非常類似 SQL。Hive 在 Java 型 MapReduce 架構上提供一個抽象層，Hive 查詢在執行階段會編譯成 MapReduce。

**執行 Hive 工作**

1. 開啟 Hadoop 命令列。
2. 切換至 **C:\\hdp\\GettingStarted** 目錄。
3. 執行下列命令在資料夾存在的情況下移除 **/w3c/hive/input** 資料夾。如果資料夾存在，Hive 工作將失敗。

		hadoop fs -rmr /w3c/hive/input

4. 執行下列命令建立 **/w3c/hive/input** 資料夾，然後將資料檔複製到 /hive/input 資料夾：

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input

		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. 執行下列命令執行 **w3ccreate.hql** 指令碼檔案。該指令碼建立 Hive 表格，並且將資料載入到 Hive 表格：

	> [AZURE.NOTE]目前，您也可以使用 HDInsight Visual Studio 工具來執行 Hive 查詢。開啟 Visual Studio，建立新的專案，然後從 HDInsight 範本中選取 [**Hive 應用程式**]。開啟專案之後，將查詢新增為新的項目。查詢位於 **C:/hdp/GettingStarted/Hive/w3c**。查詢新增至專案之後，將 **${hiveconf:input}** 取代為 **/w3c/hive/input**，然後按 [**提交**]。

		C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

	輸出應該類似如下範例：

		Logging initialized using configuration in file:/C:/hdp/hive-0.13.0.2.1.3.0-1981	/conf/hive-log4j.properties
		OK
		Time taken: 1.137 seconds
		OK
		Time taken: 4.403 seconds
		Loading data to table default.w3c
		Moved: 'hdfs://HDINSIGHT02:8020/hive/warehouse/w3c' to trash at: hdfs://HDINSIGHT02:8020/user/<username>/.Trash/Current
		Table default.w3c stats: [numFiles=1, numRows=0, totalSize=1058423, rawDataSize=0]
		OK
		Time taken: 2.881 seconds

6. 執行下列命令來執行 **w3ctotalhitsbypage.hql** HiveQL 指令碼檔案：

	> [AZURE.NOTE]如同前面的說明，您也可以使用 HDInsight Visual Studio 工具執行此查詢。

        C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	下表說明命令的元素：<table border="1"> <tr><td>檔案</td><td>說明</td></tr> <tr><td>C:\\hdp\\hive-0.13.0.2.1.3.0-1981\\bin\\hive.cmd</td><td>Hive 命令指令碼。</td></tr> <tr><td>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3ctotalhitsbypage.hql</td><td>您可以使用下列其中一個項目取代 Hive 指令碼檔案：<ul> <li>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3caveragetimetaken.hql</li> <li>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3cerrorsbypage.hql</li> </ul> </td></tr>

	</table>

	w3ctotalhitsbypage.hql HiveQL 指令碼是：

		SELECT filtered.cs_uri_stem,COUNT(*)
		FROM (
		  SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
		) filtered
		GROUP BY (filtered.cs_uri_stem);

	輸出的結尾應該類似如下範例：

		MapReduce Total cumulative CPU time: 5 seconds 391 msec
		Ended Job = job_1410201800143_0008
		MapReduce Jobs Launched:
		Job 0: Map: 1  Reduce: 1   Cumulative CPU: 5.391 sec   HDFS Read: 1058638 HDFS Write: 53 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 391 msec
		OK
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126
		Time taken: 49.304 seconds, Fetched: 3 row(s)

請注意，在各個工作的第一個步驟中，將建立表格，並且從稍早建立的檔案將資料加入到表格中。您可以使用下列命令查看 HDFS 的 /Hive 節點下，瀏覽建立的檔案：

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>執行 Pig 工作

Pig 處理使用稱為 *Pig Latin* 的資料流程語言。Pig Latin 抽象提供的資料結構比 MapReduce 豐富，而且對於 Hadoop 執行的工作與 SQL 對於關聯式資料庫管理系統執行的工作相同。


**執行 pig 工作**

1. 開啟 Hadoop 命令列。
2. 將目錄切換至 **C:\\hdp\\GettingStarted** 資料夾。
3. 執行下列命令提交 Pig 工作：

		C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	下表顯示命令的元素：<table border="1"> <tr><td>檔案</td><td>說明</td></tr> <tr><td>C:\\hdp\\pig-0.12.1.2.1.3.0-1981\\bin\\pig.cmd</td><td>Pig 命令指令碼。</td></tr> <tr><td>C:\\hdp\\GettingStarted\\Pig\\w3c\\TotalHitsForPage.pig</td><td>您可以使用下列其中一個項目取代 Pig Latin 指令碼檔案：<ul> <li>C:\\hdp\\GettingStarted\\Pig\\w3c\\AverageTimeTaken.pig</li> <li>C:\\hdp\\GettingStarted\\Pig\\w3c\\ErrorsByPage.pig</li> </ul> </td></tr> <tr><td>/w3c/input/small/data_w3c_small.txt</td><td>您可以使用較大的檔案取代參數：

	<ul>
<li>/w3c/input/medium/data_w3c_medium.txt</li>
<li>/w3c/input/large/data_w3c_large.txt</li>
</ul></td></tr> </table>

	輸出應該類似如下範例：

		(/Info.aspx,1135)
		(/UserService,1126)
		(/Default.aspx,3380)

請注意，由於 Pig 指令碼編譯為 MapReduce 工作，而且可能編譯為多個這類工作，因此您可能看見多個 MapReduce 工作在處理 Pig 工作的過程中執行。

<!---
### <a name="rebuild"></a>Rebuild the samples
The samples currently contain all the required binaries, so building is not required. If you'd like to make changes to the Java or .NET samples, you can rebuild them by using either the Microsoft Build Engine (MSBuild) or the included Azure PowerShell script.


**To rebuild the samples**

1. Open a Hadoop command line.
2. Run the following command:

		powershell -F buildsamples.ps1
--->

##<a name="blobstorage"></a>連線至 Azure Blob 儲存體
HDInsight Emulator 使用 HDFS 做為預設檔案系統。不過，Azure HDInsight 使用 Azure Blob 儲存體做為預設檔案系統。可設定 HDInsight Emulator 來使用 Azure Blob 儲存體，代替本機儲存體。請依照下列指示，在 Azure 中建立儲存體空器，並將它連接到 HDInsight Emulator。

>[AZURE.NOTE]如需有關 HDInsight 如何使用 Azure Blob 儲存體的詳細資訊，請參閱[在 HDInsight 上使用 Azure Blob 儲存體](../hdinsight-use-blob-storage.md)。

開始依照下列指示執行之前，您必須先建立儲存體帳戶。如需相關指示，請參閱[如何建立儲存體帳戶](../storage-create-storage-account.md)。

**建立容器**

1. 登入 [Azure 入口網站][azure-management-portal]。
2. 按一下左邊的 [儲存]。儲存體帳戶的清單會顯示在您的訂閱底下。
3. 從清單中按一下要建立容器的儲存體帳戶。
4. 按一下頁面頂端的 [容器]。
5. 按一下頁面底部的 [新增]。
6. 輸入**名稱**，並選取 [存取]。您可以使用 3 個存取層級的任何一個。預設為 [私人]。
7. 按一下 [確定] 儲存變更。新容器現在列在入口網站上。

您存取 Azure 儲存體帳戶之前，必須將帳戶名稱和帳戶金鑰新增到組態檔。

**設定 Azure 儲存體帳戶的連線**

1. 在 [記事本] 中開啟 **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\etc\\hadoop\\core-site.xml**。
2. 在另一個 <property> 標籤旁邊新增下列 <property> 標籤：

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	您必須以符合儲存體帳戶資訊的值取代 <StorageAccountName> 及 <StorageAccountKey>。

3. 儲存變更。您不需要重新啟動 Hadoop 服務。

使用下列語法存取儲存體帳戶：

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

例如：

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a>執行 Azure PowerShell
HDInsight Emulator 也支援部分 Azure PowerShell Cmdlet。這些 Cmdlet 包括：

- HDInsight 工作定義 Cmdlet

	- New-AzureHDInsightSqoopJobDefinition
	- New-AzureHDInsightStreamingMapReduceJobDefinition
	- New-AzureHDInsightPigJobDefinition
	- New-AzureHDInsightHiveJobDefinition
	- New-AzureHDInsightMapReduceJobDefinition
- Start-AzureHDInsightJob
- Get-AzureHDInsightJob
- Wait-AzureHDInsightJob

以下是提交 Hadoop 工作的範例：

	$creds = Get-Credential (hadoop as username, password can be anything)
	$hdinsightJob = <JobDefinition>
	Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

您將在呼叫 Get-Credential 時看見提示。您必須使用 **hadoop** 做為使用者名稱。密碼可以是任何字串。叢集名稱一律是 **http://localhost:50111**。

如需提交 Hadoop 工作的詳細資訊，請參閱[以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)。如需適用於 HDInsight 之 Azure PowerShell Cmdlet 的詳細資訊，請參閱 [HDInsight Cmdlet 參考文件][hdinsight-powershell-reference]。


##<a name="remove"></a>移除 HDInsight Emulator
在您安裝 Emulator 的電腦上，開啟 [控制台]，並且在 [**程式**] 底下按一下 [**解除安裝程式**]。從已安裝程式的清單中，以滑鼠右鍵按一下 [**Microsoft HDInsight Emulator for Azure**]，然後按一下 [**解除安裝**]。


##<a name="nextsteps"></a> 後續步驟
在本 MapReduce 教學課程中，您安裝了 HDInsight Emulator (Hadoop 沙箱) 並執行一些 Hadoop 工作。若要深入了解，請參閱下列文章：

- [開始使用 Azure HDInsight](../hdinsight-get-started.md)
- [開發 HDInsight 的 Java MapReduce 程式](hdinsight-develop-deploy-java-mapreduce.md)
- [開發 HDInsight 的 C# Hadoop 串流 MapReduce 程式](hdinsight-hadoop-develop-deploy-streaming-jobs.md)
- [HDInsight Emulator 版本資訊](hdinsight-emulator-release-notes.md)
- [討論 HDInsight 的 MSDN 論壇](http://social.msdn.microsoft.com/Forums/hdinsight)



[azure-sdk]: http://azure.microsoft.com/downloads/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/library/ff961504.aspx

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: hdinsight-emulator-release-notes.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-develop-deploy-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-versions]: hdinsight-component-versioning.md

[Powershell-install-configure]: ../install-configure-powershell.md

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-hadoop-emulator-get-started/HDI.Emulator.Services.png
 

<!---HONumber=July15_HO2-->