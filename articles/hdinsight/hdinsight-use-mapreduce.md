<properties
   pageTitle="MapReduce 與 HDInsight 上的 Hadoop | Microsoft Azure"
   description="了解如何在 HDInsight 叢集中的 Hadoop 上執行 MapReduce 工作。您將以 Java MapReduce 工作的方式執行基本字數統計作業實作。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

# 在 HDInsight 上的 Hadoop 中使用 MapReduce

[AZURE.INCLUDE [MapReduce 選取器](../../includes/hdinsight-selector-use-mapreduce.md)]

在本文中，您將了解如何在 HDInsight 叢集中的 Hadoop 上執行 MapReduce 工作。我們會以 Java MapReduce 工作的方式執行基本字數統計作業實作。

##<a id="whatis"></a>什麼是 MapReduce？

Hadoop MapReduce 是一種可撰寫工作來處理大量資料的軟體架構。輸入資料會分割成幾個獨立區塊，然後在叢集中跨多個節點平行處理。MapReduce 工作由兩項功能組成：

* **對應程式**：取用輸入資料、分析 (通常使用篩選及排序作業)，以及發出 Tuple (機碼值組)
* **減壓器**：取用對應程式發出的 Tuple 並執行摘要作業，從對應程式資料建立較小的組合結果

下圖說明了基本字數統計 MapReduce 工作範例：

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

此工作的輸出是分析的文字中每個單字出現的次數統計。

* 對應程式從輸入的文字中取得每一行當作一個輸入，然後將其打散成單字。對應程式會在單字出現時發出機碼/值組，並在該字之後加上 1。輸出會先經過排序再傳送至減壓器。

* 接著，減壓器會加總每個單字的個別計數並發出單一機碼/值組，其中包含該單字和尾隨在後的出現次數總和。

MapReduce 可在各種語言中實作。Java 是最常見的實作，基於示範目的用於此文件中。

### Hadoop 資料流

以 Java 及 Java Virtual Machine 為基礎的語言或架構 (例如，Scalding 或 Cascading) 可以 MapReduce 工作的形式直接執行 (類似於 Java 應用程式)。其他語言 (例如 C#、Python 或獨立可執行檔) 必須使用 Hadoop 資料流。

Hadoop 資料流會透過 STDIN 與 STDOUT 與對應程式和減壓器通訊 (對應程式與減壓器會從 STDIN 一次讀取一行資料，然後將輸出寫入 STDOUT。對應程式和減壓器所讀取或發出的每行資料，必須為機碼/值組格式，並以索引標籤字元分隔：

    [key]/t[value]

如需詳細資訊，請參閱[Hadoop 資料流](http://hadoop.apache.org/docs/r1.2.1/streaming.html) (英文)。

如需搭配 HDInsight 使用 Hadoop 資料流的範例，請參閱下列內容：

* [開發 C# Hadoop 資料流程式](hdinsight-hadoop-develop-deploy-streaming-jobs.md)

* [開發 Python MapReduce 工作](hdinsight-hadoop-streaming-python.md)

##<a id="data"></a>關於範例資料

在此範例中，您將會使用 The Notebooks of Leonardo Da Vinci 當作範例資料，這會以文字文件的形式在 HDInsight 叢集中提供。

範例資料會儲存在 Azure Blob 儲存體中，供 HDInsight 做為 Hadoop 叢集的預設檔案系統使用。HDInsight 可使用 **wasb** 首碼存取儲存在 Blob 儲存體中的檔案。例如，若要存取 sample.log 檔案，您應使用下列語法：

	wasb:///example/data/gutenberg/davinci.txt

由於 Azure Blob 儲存體是 HDInsight 的預設儲存體，因此您也可以使用 **/example/data/gutenberg/davinci.txt** 存取檔案。

> [AZURE.NOTE]在上一個語法中，**wasb:///** 是用來存取 HDInsight 叢集的預設儲存容器所儲存的檔案。如果您在佈建叢集時指定其他儲存體帳戶，並想要存取這些帳戶上儲存的檔案，您可以指定容器名稱和儲存體帳戶位址來存取資料。例如：**wasb://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**。

##<a id="job"></a>關於範例 MapReduce

用於此範例中的 MapReduce 工作位在 **wasb://example/jars/hadoop-mapreduce-examples.jar**，隨附於HDInsight 叢集。這包含您依據 **davinci.txt** 執行的字數統計範例。

> [AZURE.NOTE]在 HDInsight 2.1 版叢集上的檔案位置是 **wasb:///example/jars/hadoop-examples.jar**。

如需參考，以下是字數統計 MapReduce 工作的 Java 程式碼：

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

如需自行撰寫 MapReduce 工作的指示，請參閱[開發 HDInsight 的 Java MapReduce 程式](hdinsight-develop-deploy-java-mapreduce.md)。

##<a id="run"></a>執行 MapReduce

HDInsight 可以使用各種方法執行 HiveQL 工作。請使用下表決定適合您的方法，然後跟著連結逐項閱讀介紹。

| **使用此方法**... | **...執行此工作** | ...搭配此**叢集作業系統** | ...從此**用戶端作業系統** |
|:-------------------------------------------------------------------|:--------------------------------------------------------|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md) | 透過 **SSH** 使用 Hadoop 命令 | Linux | Linux、Unix、Mac OS X 或 Windows |
| [Curl](hdinsight-hadoop-use-mapreduce-curl.md) | 使用 **REST** 遠端提交工作。 | Linux 或 Windows | Linux、Unix、Mac OS X 或 Windows |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) | 使用 **Windows PowerShell** 遠端提交工作。 | Linux 或 Windows | Windows |
| [遠端桌面](hdinsight-hadoop-use-mapreduce-remote-desktop) | 透過**遠端桌面**使用 Hadoop 命令 | Windows | Windows |

##<a id="nextsteps"></a>接續步驟

雖然 MapReduce 提供強大的診斷功能，但要靈活運用還是頗具挑戰性。有數個以 Java 為基礎的架構可更輕鬆地定義 MapReduce 應用程式以及技術 (例如，Pig 和 Hive，這兩者提供更輕鬆的方式在 HDInsight 中使用資料)。若要深入了解，請參閱下列文章：

* [開發 HDInsight 的 Java MapReduce 程式](hdinsight-develop-deploy-java-mapreduce.md)

* [開發 HDInsight 的 Python 資料流 MapReduce 程式](hdinsight-hadoop-streaming-python.md)

* [開發 HDInsight 的 C# Hadoop 資料流 MapReduce 程式][hdinsight-develop-streaming]

* [使用 HDInsight 上的 Apache Hadoop 開發 Scalding MapReduce 工作](hdinsight-hadoop-mapreduce-scalding.md)

* [搭配 HDInsight 使用 Hivet][hdinsight-use-hive]

* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]

* [執行 HDInsight 範例][hdinsight-samples]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif

<!---HONumber=July15_HO2-->