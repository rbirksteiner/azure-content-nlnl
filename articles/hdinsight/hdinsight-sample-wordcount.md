<properties
	pageTitle="HDInsight 中的 Hadoop MapReduce 字數統計範例 | Microsoft Azure"
	description="在 HDInsight 的 Hadoop 叢集上執行 MapReduce 字數統計範例。以 Java 撰寫的程式將計算文字在文字檔中出現的次數。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2015" 
	ms.author="bradsev"/>

#在 HDInsight 的 Hadoop 叢集上執行以 Java 撰寫的 MapReduce 字數統計範例

本教學課程說明如何在 HDInsight 的 Hadoop 叢集上執行 MapReduce 字數統計範例。程式是以 Java 撰寫。它可統計文字檔中出現的字數，然後輸出內含每個文字及其出現頻率配對的新文字檔。本範例中所分析的文字檔，是 The Notebooks of Leonardo Da Vinci 的古騰堡計劃 (Project Gutenberg) 電子書版本。

> [AZURE.NOTE]本文件中的步驟需要 Windows 用戶端。如需利用 Linux 架構的 HDInsight 叢集從 Linux、OS X 或 Unix 用戶端使用字數統計範例的步驟，請參閱[搭配使用 MapReduce 與 HDInsight 上的 Hadoop 和 SSH](hdinsight-hadoop-use-mapreduce-ssh.md) 或[利用 Curl 搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-hadoop-use-mapreduce-curl.md)。

**您將了解：**

* 如何使用 Azure PowerShell 在 HDInsight 叢集上執行 MapReduce 程式。
* 如何以 Java 撰寫 MapReduce 程式。


**必要條件**：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **HDInsight 叢集**。如需各種建立此類叢集方式的相關指示，請參閱[開始使用 Azure HDInsight][hdinsight-get-started] 或[佈建 HDInsight 叢集](hdinsight-provision-clusters.md)

- **具有 Azure PowerShell 的工作站**。請參閱[安裝和使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。



## <a id="run-sample"></a>使用 Azure PowerShell 執行範例</h2>

**提交 MapReduce 工作**

1.	開啟 **Azure PowerShell** 主控台。如需指示，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。

3. 在下列命令中設定 2 個變數，然後執行這些命令：

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

5. 執行下列命令以建立 MapReduce 工作定義：

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	> [AZURE.NOTE]*hadoop-examples.jar* 隨附於 HDInsight 2.1 版叢集。檔案已在 HDInsight 3.0 版叢集上重新命名為 *hadoop-mapreduce.jar*。

	HDInsight 叢集附有 hadoop-mapreduce-examples.jar 檔案。MapReduce 工作有兩個引數。第一個是來源檔案名稱，第二個是輸出檔案路徑。HDInsight 叢集附有來源檔案，輸出檔案路徑則是在執行階段中建立。

6. 執行下列命令提交 MapReduce 工作：

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	除了 MapReduce 工作定義，您也提供想要用來執行 MapReduce 工作的 HDInsight 叢集名稱。

8. 執行下列命令來檢查 MapReduce 工作執行時有無錯誤：

		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

**擷取 MapReduce 工作的結果**

1. 開啟 **Azure PowerShell** 主控台。
2. 在下列命令中設定 3 個變數，然後執行這些命令：

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

	Azure 儲存體帳戶是您在教學課程稍早建立的帳戶。儲存體帳戶用來主控當做預設 HDInsight 叢集檔案系統的 Blob。Azure Blob 儲存體容器名稱通常與 HDInsight 叢集同名，除非您在佈建叢集時指定不同的名稱。

3. 執行下列命令來建立 Azure 儲存體內容物件：

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	在您有多個訂用帳戶，而且預設訂用帳戶不是要使用的訂用帳戶時，可使用 **Select-AzureSubscription** 設定目前的訂用帳戶。

4. 執行下列命令將 Blob 的 MapReduce 工作輸出下載至工作站：

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	*/example/data/WordCountOutput* 資料夾是您執行 MapReduce 工作時指定的輸出資料夾。*part-r-00000* 是 MapReduce 工作輸出的預設檔案名稱。檔案會以相同資料夾結構下載至本機資料夾。例如，在下列螢幕擷取畫面中，目前的資料夾是 C 根資料夾。檔案將下載到 *C:\\example\\data\\WordCountOutput* 資料夾。

5. 執行下列命令來列印 MapReduce 工作輸出檔案：

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	MapReduce 工作會產生一個名為 *part-r-00000* 的檔案，內有文字和字數。指令碼使用 **findstr** 命令列出包含 *"there"* 的所有文字。

WordCount 指令碼的輸出應會在命令視窗中顯示如下：

![來自 HDInsight 中 Hadoop MapReduce 字數統計範例的 PowerShell 文字頻率結果。][image-hdi-sample-wordcount-output]

請注意，MapReduce 工作的輸出檔是固定不變的。因此，如果您重新執行此範例，則需要變更輸出檔的名稱。

## <a id="java-code"></a>適用於 WordCount MapReduce 程式的 Java 程式碼</h2>



	package org.apache.hadoop.examples;
	import java.io.IOException;
	import java.util.StringTokenizer;
	import org.apache.hadoop.conf.Configuration;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.IntWritable;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapreduce.Job;
	import org.apache.hadoop.mapreduce.Mapper;
	import org.apache.hadoop.mapreduce.Reducer;
	import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
	import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
	import org.apache.hadoop.util.GenericOptionsParser;

	public class WordCount {

  	public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      	}
      }
  	}

  	public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
      }
  	}

  	public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
      System.err.println("Usage: wordcount <in> <out>");
      System.exit(2);
    	}
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  	}
  	}



在此教學課程中，您已了解如何透過 HDInsight 使用 Azure PowerShell 執行 MapReduce 程式，以計算文字檔中的文字出現次數。

## <a id="next-steps"></a>後續步驟</h2>

如需執行其他範例，及提供以 Azure PowerShell 在 Azure HDInsight 上使用 Pig、Hive 和 MapReduce 工作之指示的教學課程，請參閱：

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [範例：10GB GraySort][hdinsight-sample-10gb-graysort]
* [範例：Pi 估算器][hdinsight-sample-pi-estimator]
* [範例：C# 串流][hdinsight-sample-cs-streaming]
* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]
* [搭配 HDInsight 使用 Hivet][hdinsight-use-hive]
* [Azure HDInsight SDK 文件][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png
 

<!---HONumber=July15_HO2-->