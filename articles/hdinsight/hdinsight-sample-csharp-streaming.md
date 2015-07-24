<properties
	pageTitle="C# 串流字數統計 Hadoop 範例 | Microsoft Azure"
	description="如何以 C# 撰寫使用 Hadoop 串流介面的 MapReduce 程式，以及如何使用 PowerShell Cmdlet 在 HDInsight 上執行該程式。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/30/2014" 
	ms.author="bradsev"/>

# 在 HDInsight 上 Hadoop 中的 C# 串流字數統計 MapReduce 範例

Hadoop 提供 MapReduce 一個串流 API，可讓您以 Java 以外的語言撰寫 map 和 reduce 函數。本教學課程說明如何以 C# 撰寫使用 Hadoop 串流介面的 MapReduce 程式，以及如何使用 Azure PowerShell Cmdlet 在 Azure HDInsight 上執行該程式。

> [AZURE.NOTE]本教學課程的步驟只適用於 Windows HDInsight 叢集。如需 Linux HDInsight 叢集的串流範例，請參閱[開發適用於 HDInsight 的 Python 串流程式](hdinsight-hadoop-streaming-python.md)。

在範例中，mapper 和 reducer 是從 [stdin][stdin-stdout-stderr] 讀取輸入 (循行) 並將輸出發出到 [stdout][stdin-stdout-stderr] 的可執行檔。程式會計算內容中的所有文字。

在已為 **mappers** 指定可執行檔的情況下，當 mapper 初始化時，每個 mapper 工作都會將可執行檔啟動成為個別的處理程序。當 mapper 工作執行時，它會將其輸入傳換成行，並將這些行饋送至處理程序的 [stdin][stdin-stdout-stderr]。

同時，mapper 會收集來自處理程序 stdout 的行導向輸出。它會將每一行轉換成索引鍵/值組，其會做為 mapper 的輸出來收集。根據預設，從一行的前置詞一直到第一個定位字元即是索引鍵，行的其餘部分 (不包含定位字元) 則為值。如果行中沒有定位字元，則整行都會被視為索引鍵，而值則為 null。

在已為 **reducers** 指定可執行檔的情況下，當 reducer 初始化時，每個 reducer 工作都會將可執行檔啟動成為個別的處理程序。在執行 reducer 工作時，它會將其輸入索引鍵/值組傳換成行，並將這些行饋送至處理程序的 [stdin][stdin-stdout-stderr]。

同時，reducer 會收集來自處理程序 [stdout][stdin-stdout-stderr] 的行導向輸出。它會將每一行轉換成索引鍵/值組，其會做為 reducer 的輸出來收集。根據預設，從一行的前置詞一直到第一個定位字元即是索引鍵，行的其餘部分 (不包含定位字元) 則為值。

如需有關 Hadoop 串流介面的詳細資訊，請參閱 [Hadoop 串流][hadoop-streaming]。

**在本教學課程中，您將了解如何：**

* 在 HDInsight 上使用 Azure PowerShell 執行 C# 串流程式來分析檔案中包含的資料。
* 撰寫使用 Hadoop 串流介面的 C# 程式碼。


**必要條件**：

開始之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **HDInsight 叢集**。如需各種建立此類叢集方式的相關指示，請參閱[佈建 HDInsight 叢集](hdinsight-provision-clusters.md)。
- **具有 Azure PowerShell 的工作站**。請參閱[安裝和使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。



## <a id="run-sample"></a>使用 Azure PowerShell 執行範例

**執行 MapReduce 工作**

1.	開啟 **Azure PowerShell**。如需有關開啟 Azure PowerShell 主控台視窗的指示，請參閱[安裝和設定 Azure PowerShell][powershell-install-configure]。

3. 在下列命令中設定 2 個變數，然後執行這些命令：

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


2. 執行下列命令來定義 MapReduce 工作：

		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe"

	參數指定了 mapper 和 reducer 函數，以及輸入檔和輸出檔。

5. 執行下列命令以執行 MapReduce 工作、等待工作完成，然後列印標準錯誤訊息：

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError

6. 執行下列命令以顯示字數的結果：

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $_.Primary } $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	請注意，MapReduce 工作的輸出檔是固定不變的。因此，如果您重新執行此範例，則需要變更輸出檔的名稱。


## <a id="java-code"></a>Hadoop 串流的 C# 程式碼


MapReduce 程式使用 cat.exe 應用程式做為對應介面以將文字串流至主控台，並使用 wc.exe 應用程式做為縮減介面以計算從文件串流的字數。mapper 和 reducer 都會從標準輸入資料流 (stdin) 逐行讀取字元並寫入至標準輸出資料流 (stdout)。



	// The source code for the cat.exe (Mapper).

	using System;
	using System.IO;

	namespace cat
	{
	    class cat
	    {
	        static void Main(string[] args)
	        {
	            if (args.Length > 0)
	            {
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            string line;
	            while ((line = Console.ReadLine()) != null)
	            {
	                Console.WriteLine(line);
	            }
	        }
	    }
	}



cat.cs 檔案中的 mapper 程式碼會使用 [StreamReader][streamreader] 物件將連入資料流的字元讀取至主控台，再由主控台以靜態 [Console.Writeline][console-writeline] 方法將資料流寫入至標準輸出資料流。


	// The source code for wc.exe (Reducer) is:

	using System;
	using System.IO;
	using System.Linq;

	namespace wc
	{
	    class wc
	    {
	        static void Main(string[] args)
	        {
	            string line;
	            var count = 0;

	            if (args.Length > 0){
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            while ((line = Console.ReadLine()) != null) {
	                count += line.Count(cr => (cr == ' ' || cr == '\n'));
	            }
	            Console.WriteLine(count);
	        }
	    }
	}


wc.cs 檔案中的 reducer 程式碼會使用 [StreamReader][streamreader] 物件，從已經由 cat.exe mapper 輸出的標準輸入資料流讀取字元。當它使用 [Console.Writeline][console-writeline] 方法讀取字元時，它會藉由計算空格和每個字尾端的行尾字元來計算字數。然後將總計使用 [Console.Writeline][console-writeline] 方法寫入標準輸出資料流。


## <a id="summary"></a>摘要

在本教學課程中，您已看到如何使用 Hadoop 串流在 HDInsight 部署 MapReduce 工作。

## <a id="next-steps"></a>接續步驟


如需執行其他範例及提供如何以 Azure PowerShell 在 Azure HDInsight 上使用 Pig、Hive 和 MapReduce 工作之指示的教學課程，請參閱下列主題：

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [範例：Pi 估算器][hdinsight-sample-pi-estimator]
* [範例：字數統計][hdinsight-sample-wordcount]
* [範例：10GB GraySort][hdinsight-sample-10gb-graysort]
* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]
* [搭配 HDInsight 使用 Hivet][hdinsight-use-hive]
* [Azure HDInsight SDK 文件][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/library/3x292kth(v=vs.110).aspx

[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
 

<!---HONumber=July15_HO2-->