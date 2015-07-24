<properties 
	pageTitle="使用指令碼動作在 Hadoop 叢集上安裝 Spark | Microsoft Azure" 
	description="了解如何使用 Spark 自訂 HDInsight 叢集。您將使用指令碼動作組態選項來使用指令碼安裝 Spark。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="nitinme"/>

# 在 HDInsight Hadoop 叢集上安裝和使用 Spark

您可以使用**指令碼動作**叢集自訂，在 Azure HDInsight 上 Hadoop 中的任何一種叢集上安裝 Spark。指令碼動作可讓您執行指令碼來自訂叢集，但只能在建立叢集時進行。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集][hdinsight-cluster-customize]。

在本主題中，您將學習如何使用指令碼動作來安裝 Spark。安裝 Spark 之後，您也將學習如何在 HDInsight 叢集上執行 Spark 查詢。


## <a name="whatis"></a>什麼是 Spark？

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。Spark 的記憶體內計算功能，使其成為機器學習和圖表計算中反覆演算法的絕佳選擇 。

Spark 也可用來執行傳統的磁碟型資料處理。Spark 以避免在中繼階段寫入磁碟的方式，改善傳統的 MapReduce 架構。此外，Spark 與 Hadoop 分散式檔案系統 (HDFS) 和 Azure Blob 儲存體相容，因此可以輕鬆地透過 Spark 來處理現有的資料。

本主題說明如何自訂 HDInsight 叢集以安裝 Spark。

## <a name="whatis"></a>可以安裝哪個版本的 Spark？

在本主題中，我們使用指令碼動作自訂指令碼在 HDInsight 叢集上安裝 Spark。根據您佈建的 HDInsight 叢集版本，此指令碼可安裝 Spark 1.2.0 或 Spark 1.0.2。

- 如果您在佈建 **HDInsight 3.2** 叢集時使用此指令碼，它會安裝 **Spark 1.2.0**。
- 如果您在佈建 **HDInsight 3.1** 叢集時使用此指令碼，它會安裝 **Spark 1.0.2**。 

您可以修改此指令碼或建立自有指令碼，以安裝其他版本的 Spark。


## <a name="install"></a>如何安裝 Spark？

您可以從唯讀的 Azure 儲存體 Blob 取得在 HDInsight 叢集上安裝 Spark 的範例指令碼，網址為 [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1)。本節提供有關如何在使用 Azure 入口網站佈建叢集時使用範例指令碼的指示。

> [AZURE.NOTE]範例指令碼只能與 HDInsight 3.1 和 3.2 叢集搭配使用。如需 HDInsight 叢集版本的詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。

1. 依照[使用自訂選項佈建叢集](hdinsight-provision-clusters.md#portal)所述，使用**自訂建立**選項開始佈建叢集。根據下列原則挑選叢集版本：

	- 如果您想要安裝 **Spark 1.2.0**，請佈建 HDInsight 3.2 叢集。
	- 如果您想要安裝 **Spark 1.0.2**，請佈建 HDInsight 3.1 叢集。


2. 在精靈的 [**指令碼動作**] 頁面上，按一下 [**加入指令碼動作**] 以提供有關指令碼動作的詳細資料，如下所示：

	![使用指令碼動作以自訂叢集](./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "使用指令碼動作以自訂叢集")
	
	<table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>名稱</td>
		<td>指定指令碼動作的名稱。例如，<b>安裝 Spark</b>。</td></tr>
	<tr><td>指令碼 URI</td>
		<td>指定為了自訂叢集所叫用之指令碼的統一資源識別項 (URI)。例如，<i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
	<tr><td>節點類型</td>
		<td>指定執行自訂指令碼的節點。您可以選擇 [<b>所有節點</b>]、[<b>僅限前端節點</b>] 或 [<b>僅限背景工作節點</b>]。
	<tr><td>參數</td>
		<td>如果指令碼要求，請指定參數。用來安裝 Spark 的指令碼不需要任何參數，因此可以讓此處空白。</td></tr>
</table>您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。加入指令碼之後，請按一下核取記號以開始佈建叢集。

您也可以使用 Azure PowerShell 或 HDInsight .NET SDK，在 HDInsight 上使用指令碼安裝 Spark。本主題稍後會提供這些程序的指示。

## <a name="usespark"></a>如何在 HDInsight 中使用 Spark？
Spark 提供以 Scala、Python 及 Java 撰寫的 API。您也可以使用互動式 Spark 殼層來執行 Spark 查詢。本節說明如何透過不同方法來使用 Spark：

- [使用 Spark 殼層來執行互動式查詢](#sparkshell)
- [使用 Spark 殼層來執行 Spark SQL 查詢](#sparksql) 
- [使用獨立 Scala 程式](#standalone)

###<a name="sparkshell"></a>使用 Spark 殼層來執行互動式查詢
請執行下列步驟以從互動式 Spark 殼層執行 Spark 查詢。在本節中，我們將對 HDInsight 叢集上預設提供的範例資料檔案 (/example/data/gutenberg/davinci.txt) 執行 Spark 查詢。

1. 從 Azure 入口網站，針對您所建立且已安裝 Spark 的叢集啟用遠端桌面，然後遠端登入到叢集。如需指示，請參閱<a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">使用 RDP 連線到 HDInsight 叢集</a>。

2. 在遠端桌面通訊協定 (RDP) 工作階段中，從桌面開啟 Hadoop 命令列 (從桌面捷徑)，然後瀏覽至 Spark 的安裝位置；例如 **C:\\apps\\dist\\spark-1.2.0**。


3. 執行下列命令以啟動 Spark 殼層：

		 .\bin\spark-shell --master yarn

	在命令完成執行之後，您應該會看到 Scala 提示：

		 scala>

5. 出現 Scala 提示時，輸入下方所示的 Spark 查詢。此查詢會計算 davinci.txt 檔案中每個單字的出現次數，該檔案位於與叢集關聯之 Azure Blob 儲存體上的 /example/data/gutenberg/ 位置中。

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. 輸出應該如以下所示：

	![在 HDInsight 叢集中執行 Scala 互動式殼層所得到的輸出](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)
		

7. 輸入 :q 以結束 Scala 提示。

		:q

###<a name="sparksql"></a>使用 Spark 殼層來執行 Spark SQL 查詢

Spark SQL 可讓您使用 Spark 來執行以結構化查詢語言 (SQL)、HiveQL 或 Scala 表示的關聯式查詢。在本節中，我們要來看看如何使用 Spark 對範例 Hive 資料表執行 Hive 查詢。本節所用的 Hive 資料表 (稱為 **hivesampletable**) 依預設可在佈建叢集時取得。

>[AZURE.NOTE]下面的範例是針對 **Spark 1.2.0** 所建立，若您在佈建 HDInsight 3.2 叢集時執行指令碼動作，便會安裝 Spark 1.2.0。

1. 從 Azure 入口網站，針對您所建立且已安裝 Spark 的叢集啟用遠端桌面，然後遠端登入到叢集。如需指示，請參閱<a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">使用 RDP 連線到 HDInsight 叢集</a>。

2. 在 RDP 工作階段中，從桌面開啟 Hadoop 命令列 (從桌面捷徑)，然後瀏覽至 Spark 的安裝位置；例如 **C:\\apps\\dist\\spark-1.2.0**。


3. 執行下列命令以啟動 Spark 殼層：

		 .\bin\spark-shell --master yarn

	在命令完成執行之後，您應該會看到 Scala 提示：

		 scala>

4. 在 Scala 提示設定 Hive 內容。必須執行此動作，才能透過 Spark 使用 Hive 查詢。

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	請注意，**sc** 是您啟動 Spark 殼層時所設定的預設 Spark 內容。

5. 使用 Hive 內容執行 Hive 查詢，並將輸出列印到主控台。查詢會擷取特定型號裝置的資料，並將所擷取的記錄數限制在 20 個內。

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. 您應該會看到如下的輸出：

	![在 HDInsight 叢集中執行 Spark SQL 所得到的輸出](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. 輸入 :q 以結束 Scala 提示。

		:q

### <a name="standalone"></a>使用獨立 Scala 程式

在本節中，我們將撰寫一個 Scala 應用程式，用來計算 HDInsight 叢集上預設提供的範例資料檔案 (/example/data/gutenberg/davinci.txt) 中含有字母 'a' 和 'b' 的行數。若要撰寫獨立 Scala 程式並與已安裝 Spark 的自訂叢集搭配使用，您必須執行下列步驟：

- 撰寫 Scala 程式
- 建置 Scala 程式以取得 .jar 檔案
- 在叢集上執行工作

#### 撰寫 Scala 程式
在本節中，您將撰寫一個 Scala 應用程式，用來計算範例資料檔案中含有 'a' 和 'b' 的行數。

1. 開啟文字編輯器，並貼上下列程式碼：


		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//Location of the sample data file on Azure Blob storage
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

2. 以 **SimpleApp.scala** 的名稱儲存檔案。

#### 建置 Scala 程式
在本節中，您將使用<a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">簡單建置工具</a> (或稱 sbt) 來建置 Scala 程式。sbt 需要 Java 1.6 或更新版本，因此請先確定您已安裝正確版本的 Java，再繼續進行本節。

1. 從 http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html 安裝 sbt。
2. 建立一個稱為 **SimpleScalaApp** 的資料夾，並在此資料夾中建立一個稱為 **simple.sbt** 的檔案。這是包含 Scala 版本、程式庫相依性等相關資訊的組態檔。將下列程式碼貼到 simple.sbt 檔案中並加以儲存：


		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



	>[AZURE.NOTE]請確定在檔案中保留那些空白行。

	
3. 在 **SimpleScalaApp** 資料夾下，建立目錄結構 **\\src\\main\\scala**，然後將您稍早建立的 Scala 程式 (**SimpleApp.scala**) 貼到 \\src\\main\\scala 資料夾下。
4. 開啟命令提示字元，瀏覽至 SimpleScalaApp 目錄，並輸入下列命令：


		sbt package


	在編譯應用程式之後，您會看到 **simpleapp_2.10-1.0.jar** 檔案建立在根 SimpleScalaApp 資料夾內的 **\\target\\scala-2.10** 目錄下。


#### 在叢集上執行工作
在本節中，您將從遠端連線至已安裝 Spark 的叢集，然後複製 SimpleScalaApp 專案的目標資料夾。接著，您將使用 **spark-submit** 命令在叢集上提交工作。

1. 從遠端連線至已安裝 Spark 的叢集。從您撰寫並建置 SimpleApp.scala 程式的電腦上複製 **SimpleScalaApp\\target** 資料夾，並將它貼到叢集上的位置。
2. 在 RDP 工作階段中，從桌面開啟 Hadoop 命令列，然後瀏覽至您貼上 **target** 資料夾的位置。
3. 輸入下列命令來執行 SimpleApp.scala 程式：


		C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. 當程式執行完成時，輸出會顯示在主控台上。


		Lines with a: 21374, Lines with b: 11430

## <a name="usingPS"></a>使用 Azure PowerShell 在 HDInsight Hadoop 叢集上安裝 Spark

本節中，我們使用 **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** Cmdlet，使用指令碼動作叫用指令碼以自訂叢集。在繼續之前，請確認您已安裝和設定 Azure PowerShell。如需設定工作站以執行適用於 HDInsight 的 Azure Powershell Cmdlet 的相關資訊，請參閱[安裝和設定 Azure PowerShell][powershell-install-configure]。

執行下列步驟：

1. 開啟 Azure PowerShell 視窗，並宣告下列變數：

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.2"
	
2. 指定設定值，例如要使用的叢集中節點和預設儲存體。

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. 使用 **Add-AzureHDInsightScriptAction** Cmdlet 將指令碼動作加入叢集組態。稍後在建立叢集時，將會執行指令碼動作。

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

	**Add-AzureHDInsightScriptAction** Cmdlet 可接受下列參數：

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">參數</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">定義</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">設定</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">要在其中新增指令碼動作資訊的設定物件。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名稱</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指令碼動作的名稱。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指定執行自訂指定碼的節點。有效值為 HeadNode (在前端節點上安裝) 或 DataNode (在所有資料節點上安裝)。您可以使用其中一個或兩個值。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指定所執行之指令碼的 URI。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">參數</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指令碼所需的參數。本主題中使用的範例指令碼不需要任何參數，因此您在上述程式碼片段中看不到此參數。
</td></tr>
</table>
	
4. 最後，開始佈建已安裝 Spark 的自訂叢集。
	
		# Start provisioning a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

出現提示時，請輸入叢集的認證。建立叢集可能需要幾分鐘的時間。


## <a name="usingSDK"></a>使用 .NET SDK 在 HDInsight Hadoop 叢集上安裝 Spark

HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您能夠輕鬆地從 .NET Framework 應用程式使用 HDInsight。本節說明如何使用來自 SDK 的「指令碼動作」來佈建已安裝 Spark 的叢集。必須執行下列程序：

- 安裝 HDInsight .NET SDK
- 建立自我簽署憑證
- 建立主控台應用程式
- 執行應用程式


**安裝 HDInsight .NET SDK**

您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 安裝最新發佈的 SDK 組建。下一個程序會顯示相關指示。

**建立自我簽署憑證**

建立自我簽署憑證，將它安裝到工作站，然後上傳至 Azure 訂用帳戶。如需指示，請參閱[建立自我簽署憑證](http://go.microsoft.com/fwlink/?LinkId=511138)。


**建立 Visual Studio 應用程式**

1. 開啟 Visual Studio 2013。

2. 從 [檔案] 功能表中，按一下 [新增]，再按 [專案]。

3. 在 [**新增專案**] 中，輸入或選取下列值：
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">屬性</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">值</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">類別</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">範本/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">範本</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">主控台應用程式</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名稱</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSparkCluster</td></tr>
</table>

4. 按一下 [確定] 以建立專案。

5. 在 [工具] 功能表中按一下 [Nuget 套件管理員]，然後按一下 [Package Manager Console]。

6. 在主控台中執行下列命令，以安裝套件：

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	此命令會從目前的 Visual Studio 專案新增 .NET 程式庫及其參考。

7. 在 [方案總管] 中，按兩下 **Program.cs** 加以開啟。

8. 在檔案頂端新增下列 using 陳述式：

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. 在 Main() 函數中，複製及貼上下列程式碼，並提供變數的值：
		
        var clusterName = args[0];

        // Provide values for the variables
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Provide the certificate thumbprint to retrieve the certificate from the certificate store 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsight client object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // Provide the cluster information
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.2"
        };        

10. 將下列程式碼附加至 Main() 函數中，以使用 [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) 類別叫用用來安裝 Spark 的自訂指令碼。

		// Add the script action to install Spark
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Spark", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Spark on
          new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), // Location of the script to install Spark.
		  null //Because the script used does not require any parameters
        ));

11. 最後，建立叢集。

		client.CreateCluster(clusterInfo);

11. 儲存對應用程式所做的變更，然後建置方案。

**執行應用程式**

開啟 Azure PowerShell 主控台、瀏覽至您儲存 Visual Studio 專案的位置、瀏覽至專案內的 \\bin\\debug 目錄，然後執行下列命令：

	.\CreateSparkCluster <cluster-name>

請提供叢集名稱，然後按 ENTER 以佈建已安裝 Spark 的叢集。


## 另請參閱##
- [在 HDInsight 叢集上安裝 R][hdinsight-install-r] 說明如何使用叢集自訂，以在 HDinsight Hadoop 叢集上安裝和使用 R。R 是一個用於統計計算的開放原始碼語言和環境。它提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。它也提供廣泛的圖形功能。
- [在 HDInsight 叢集上安裝 Giraph](hdinsight-hadoop-giraph-install.md)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Giraph。Giraph 可讓您利用 Hadoop 執行圖形處理，且可以搭配 Azure HDInsight 一起使用。
- [在 HDInsight 叢集上安裝 Solr](hdinsight-hadoop-solr-install.md)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Solr。Solr 可讓您對儲存的資料執行功能強大的搜尋作業。





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: ../install-configure-powershell.md
 

<!---HONumber=July15_HO2-->