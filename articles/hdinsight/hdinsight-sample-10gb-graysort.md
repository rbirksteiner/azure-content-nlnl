<properties
	pageTitle="10GB GraySort Hadoop MapReduce 範例 | Microsoft Azure"
	description="了解如何使用 Azure PowerShell 在 HDInsight 的 Hadoop 上，針對極大資料量 (通常至少 100 TB) 執行一般用途的 GraySort。"
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
	ms.date="03/30/2015"
	ms.author="bradsev"/>

# HDInsight 中的 10GB GraySort Hadoop MapReduce 範例

本範例主題說明如何使用 Azure PowerShell，在 Azure HDInsight 上執行一般用途的 GraySort Hadoop MapReduce 程式。GraySort 是一種效能評定排序，其度量為排序極大資料量時 (通常至少為 100TB) 所達成的排序速率 (TB/分鐘)。

> [AZURE.NOTE]此文件中的步驟需要以 Windows 為基礎的 HDInsight 叢集。如需在以 Linux 為基礎的叢集執行此範例和其他範例的資訊，請參閱[在 HDInsight 上執行 Hadoop 範例](hdinsight-hadoop-run-samples-linux.md)

本範例使用不太大的 10GB 資料，所以執行起來相對較快。本範例使用 Owen O'Malley 和 Arun Murthy 所開發的 MapReduce 應用程式，此應用程式於 2009 年的年度一般目的 (「耐力賽」) TB 排序效能評定中，以 0.578TB/分鐘 (173 分鐘內達到 100TB) 的速率獲勝。如需此效能評比和其他排序效能評比的詳細資訊，請參閱 [Sortbenchmark](http://sortbenchmark.org/) 網站。

本範例使用三組 MapReduce 程式：

1. **TeraGen** 是可用來產生排序資料列的 MapReduce 程式。
2. **TeraSort** 可取樣輸入資料並利用 MapReduce 將資料依全序排列。TeraSort 是 MapReduce 函數的標準排序，但自訂分割器除外，它使用 N-1 個樣本索引鍵的排序清單來定義每次歸納的索引鍵範圍。尤其是，會傳送使得 sample[i-1] <= key < sample[i] 的所有索引鍵給歸納 i。這保證歸納 i 的輸出全都小於歸納 i+1 的輸出。
3. **TeraValidate** 是一個驗證全域排序輸出的 MapReduce 程式。它會在輸出目錄中為每一個檔案建立一個對應，而每個對應可確保每一個索引鍵一定小於或等於前一個對應。對應函數也會產生每個檔案的第一個和最後一個索引鍵的記錄，而歸納函數可確保檔案 i 的第一個索引鍵大於檔案 i-1 的最後一個索引鍵。任何問題皆會回報為具錯誤索引鍵的歸納輸出。

這三個應用程式所使用的輸入和輸出格式會以正確格式讀取和寫入文字檔。歸納的輸出將複寫設為 1，而不是預設值 3，因為效能評定競賽不需要將輸出資料複寫至多個節點。


**您將了解：** * 如何使用 Azure PowerShell 在 Azure HDInsight 上執行一系列的 MapReduce 程式。* 以 Java 撰寫的 MapReduce 程式看起來如何。


**必要條件：**

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **HDInsight 叢集**。如需各種建立此類叢集方式的相關指示，請參閱[佈建 HDInsight 叢集](hdinsight-provision-clusters.md)。
- **具有 Azure PowerShell 的工作站**。請參閱[安裝和使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。



##使用 Azure PowerShell 執行範例

範例需要三項工作，各對應至簡介中描述的其中一個 MapReduce 程式：

1. 執行 **TeraGen** MapReduce 工作來產生要排序的資料。
2. 執行 **TeraSort** MapReduce 工作來排序資料。
3. 執行 **TeraValidate** MapReduce 工作來確認資料排序正確。


**執行 TeraGen 程式**

1. 開啟 Azure PowerShell。如需有關開啟 Azure PowerShell 主控台視窗的指示，請參閱[安裝和設定 Azure PowerShell][powershell-install-configure]。
2. 在下列命令中設定 2 個變數，然後執行這些命令：

		# Provide the Azure subscription name and the HDInsight cluster name
		$subscriptionName = "myAzureSubscriptionName"
		$clusterName = "myClusterName"

4. 執行下列命令以建立 MapReduce 工作定義：

		# Create a MapReduce job definition for the TeraGen MapReduce program
		$teragen = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-mapreduce-examples.jar" -ClassName "teragen" -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"

	*"-Dmapred.map.tasks=50"* 引數指定要建立來執行工作的 50 個對應。*100000000* 引數指定要產生的資料量。最後一個引數 */example/data/10GB-sort-input* 指定要儲存結果的輸出目錄 (並指定包含下列排序階段輸入的目錄)。

5. 執行下列命令來提交工作、等待工作完成，然後印出標準錯誤：

		# Run the TeraGen MapReduce job
		# Wait for the job to finish
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$teragen | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError


**執行 TeraSort 程式**

1. 開啟 Azure PowerShell。
2. 在下列命令中設定 2 個變數，然後執行這些命令：

		# Provide the Azure subscription name and the HDInsight cluster name
		$subscriptionName = "myAzureSubscriptionName"
		$clusterName = "myClusterName"

3. 執行下列命令來定義 MapReduce 工作：

		# Create a MapReduce job definition for the TeraSort MapReduce program
		$terasort = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-mapreduce-examples.jar" -ClassName "terasort" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"

	*"-Dmapred.map.tasks=50"* 引數指定要建立來執行工作的 50 個對應。*100000000* 引數指定要產生的資料量。最後兩個引數指定輸入和輸出目錄。

4. 執行下列命令來提交工作、等待工作完成，然後印出標準錯誤：

		# Run the TeraSort MapReduce job
		# Wait for the job to finish
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$terasort | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError


**執行 TeraValidate 程式**

1. 開啟 Azure PowerShell。
2. 在下列命令中設定 2 個變數，然後執行這些命令：

		# Provide the Azure subscription name and the HDInsight cluster name
		$subscriptionName = "myAzureSubscriptionName"
		$clusterName = "myClusterName"

3. 執行下列命令來定義 MapReduce 工作：

		#	Create a MapReduce job definition for the TeraValidate MapReduce program
		$teravalidate = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-mapreduce-examples.jar" -ClassName "teravalidate" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"

	*"-Dmapred.map.tasks=50"* 引數指定要建立來執行工作的 50 個對應。*"-Dmapred.reduce.tasks=25"* 引數指定將建立 25 個歸納工作來執行工作。最後兩個引數指定輸入和輸出目錄。


4. 執行下列命令來提交 MapReduce 工作、等待工作完成，然後印出標準錯誤：

		# Run the TeraSort MapReduce job
		# Wait for the job to finish
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$teravalidate | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError


##TeraSort MapReduce 程式的 Java 程式碼

本節顯示 TeraSort MapReduce 程式的程式碼以進行檢查。


	/**
	 * Licensed to the Apache Software Foundation (ASF) under one
	 * or more contributor license agreements.  See the NOTICE file
	 * distributed with this work for additional information
	 * regarding copyright ownership.  The ASF licenses this file
	 * to you under the Apache License, Version 2.0 (the
	 * "License"); you may not use this file except in compliance
	 * with the License.  You may obtain a copy of the License at
	 *
	 *     http://www.apache.org/licenses/LICENSE-2.0
	 *
	 * Unless required by applicable law or agreed to in writing, software
	 * distributed under the License is distributed on an "AS IS" BASIS,
	 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
	 * See the License for the specific language governing permissions and
	 * limitations under the License.
	 */

	package org.apache.hadoop.examples.terasort;

	import java.io.IOException;
	import java.io.PrintStream;
	import java.net.URI;
	import java.util.ArrayList;
	import java.util.List;

	import org.apache.commons.logging.Log;
	import org.apache.commons.logging.LogFactory;
	import org.apache.hadoop.conf.Configured;
	import org.apache.hadoop.filecache.DistributedCache;
	import org.apache.hadoop.fs.FileSystem;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.NullWritable;
	import org.apache.hadoop.io.SequenceFile;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapred.FileOutputFormat;
	import org.apache.hadoop.mapred.JobClient;
	import org.apache.hadoop.mapred.JobConf;
	import org.apache.hadoop.mapred.Partitioner;
	import org.apache.hadoop.util.Tool;
	import org.apache.hadoop.util.ToolRunner;

	/**
	 * Generates the sampled split points, launches the job,
	 * and waits for it to finish.
	 * <p>
	 * To run the program:
	 * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
	 */

	public class TeraSort extends Configured implements Tool {
	  private static final Log LOG = LogFactory.getLog(TeraSort.class);

	  /**
	   * A partitioner that splits text keys into roughly equal
	   * partitions in a global sorted order.
	   */

	  static class TotalOrderPartitioner implements Partitioner<Text,Text>{
	    private TrieNode trie;
	    private Text[] splitPoints;

	    /**
	     * A generic trie node
	     */
	    static abstract class TrieNode {
	      private int level;
	      TrieNode(int level) {
	        this.level = level;
	      }
	      abstract int findPartition(Text key);
	      abstract void print(PrintStream strm) throws IOException;
	      int getLevel() {
	        return level;
	      }
	    }

	    /**
	     * An inner trie node that contains 256 children based on the next
	     * character.
	     */
	    static class InnerTrieNode extends TrieNode {
	      private TrieNode[] child = new TrieNode[256];

	      InnerTrieNode(int level) {
	        super(level);
	      }
	      int findPartition(Text key) {
	        int level = getLevel();
	        if (key.getLength() <= level) {
	          return child[0].findPartition(key);
	        }
	        return child[key.getBytes()[level]].findPartition(key);
	      }
	      void setChild(int idx, TrieNode child) {
	        this.child[idx] = child;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int ch=0; ch < 255; ++ch) {
	          for(int i = 0; i < 2*getLevel(); ++i) {
	            strm.print(' ');
	          }
	          strm.print(ch);
	          strm.println(" ->");
	          if (child[ch] != null) {
	            child[ch].print(strm);
	          }
	        }
	      }
	    }

	    /**
	     * A leaf trie node that does string compares to figure out where the given
	     * key belongs between lower..upper.
	     */
	    static class LeafTrieNode extends TrieNode {
	      int lower;
	      int upper;
	      Text[] splitPoints;
	      LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
	        super(level);
	        this.splitPoints = splitPoints;
	        this.lower = lower;
	        this.upper = upper;
	      }
	      int findPartition(Text key) {
	        for(int i=lower; i<upper; ++i) {
	          if (splitPoints[i].compareTo(key) >= 0) {
	            return i;
	          }
	        }
	        return upper;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int i = 0; i < 2*getLevel(); ++i) {
	          strm.print(' ');
	        }
	        strm.print(lower);
	        strm.print(", ");
	        strm.println(upper);
	      }
	    }


	    /**
	     * Read the cut points from the given sequence file.
	     * @param fs the file system
	     * @param p the path to read
	     * @param job the job config
	     * @return the strings to split the partitions on
	     * @throws IOException
	     */
	    private static Text[] readPartitions(FileSystem fs, Path p,
	                                         JobConf job) throws IOException {
	      SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
	      List<Text> parts = new ArrayList<Text>();
	      Text key = new Text();
	      NullWritable value = NullWritable.get();
	      while (reader.next(key, value)) {
	        parts.add(key);
	        key = new Text();
	      }
	      reader.close();
	      return parts.toArray(new Text[parts.size()]);  
	    }

	    /**
	     * Given a sorted set of cut points, build a trie that will find the correct
	     * partition quickly.
	     * @param splits the list of cut points
	     * @param lower the lower bound of partitions 0..numPartitions-1
	     * @param upper the upper bound of partitions 0..numPartitions-1
	     * @param prefix the prefix that we have already checked against
	     * @param maxDepth the maximum depth we will build a trie for
	     * @return the trie node that will divide the splits correctly
	     */
	    private static TrieNode buildTrie(Text[] splits, int lower, int upper,
	                                      Text prefix, int maxDepth) {
	      int depth = prefix.getLength();
	      if (depth >= maxDepth || lower == upper) {
	        return new LeafTrieNode(depth, splits, lower, upper);
	      }
	      InnerTrieNode result = new InnerTrieNode(depth);
	      Text trial = new Text(prefix);
	      // append an extra byte on to the prefix
	      trial.append(new byte[1], 0, 1);
	      int currentBound = lower;
	      for(int ch = 0; ch < 255; ++ch) {
	        trial.getBytes()[depth] = (byte) (ch + 1);
	        lower = currentBound;
	        while (currentBound < upper) {
	          if (splits[currentBound].compareTo(trial) >= 0) {
	            break;
	          }
	          currentBound += 1;
	        }
	        trial.getBytes()[depth] = (byte) ch;
	        result.child[ch] = buildTrie(splits, lower, currentBound, trial,
	                                     maxDepth);
	      }
	      // pick up the rest
	      trial.getBytes()[depth] = 127;
	      result.child[255] = buildTrie(splits, currentBound, upper, trial,
	                                    maxDepth);
	      return result;
	    }

	    public void configure(JobConf job) {
	      try {
	        FileSystem fs = FileSystem.getLocal(job);
	        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
	        splitPoints = readPartitions(fs, partFile, job);
	        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
	      } catch (IOException ie) {
	        throw new IllegalArgumentException("can't read paritions file", ie);
	      }
	    }

	    public TotalOrderPartitioner() {
	    }

	    public int getPartition(Text key, Text value, int numPartitions) {
	      return trie.findPartition(key);
	    }

	  }

	  public int run(String[] args) throws Exception {
	    LOG.info("starting");
	    JobConf job = (JobConf) getConf();
	    Path inputDir = new Path(args[0]);
	    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
	    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
	    URI partitionUri = new URI(partitionFile.toString() +
	                               "#" + TeraInputFormat.PARTITION_FILENAME);
	    TeraInputFormat.setInputPaths(job, new Path(args[0]));
	    FileOutputFormat.setOutputPath(job, new Path(args[1]));
	    job.setJobName("TeraSort");
	    job.setJarByClass(TeraSort.class);
	    job.setOutputKeyClass(Text.class);
	    job.setOutputValueClass(Text.class);
	    job.setInputFormat(TeraInputFormat.class);
	    job.setOutputFormat(TeraOutputFormat.class);
	    job.setPartitionerClass(TotalOrderPartitioner.class);
	    TeraInputFormat.writePartitionFile(job, partitionFile);
	    DistributedCache.addCacheFile(partitionUri, job);
	    DistributedCache.createSymlink(job);
	    job.setInt("dfs.replication", 1);
	    TeraOutputFormat.setFinalSync(job, true);
	    JobClient.runJob(job);
	    LOG.info("done");
	    return 0;
	  }

	  /**
	   * @param args
	   */

	  public static void main(String[] args) throws Exception {
	    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
	    System.exit(res);
	  }

	}


##摘要

本範例示範如何使用 Azure HDInsight 來執行一系列的 MapReduce 工作，其中，一個工作的資料輸出會變成系列中下一個工作的輸入。

##後續步驟

如需指引您執行其他範例及提供在 Azure HDInsight 上以 Azure PowerShell 使用 Pig、Hive 和 MapReduce 工作之相關指示的教學課程，請參閱下列主題：

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [範例：Pi 估算器][hdinsight-sample-pi-estimator]
* [範例：字數統計][hdinsight-sample-wordcount]
* [範例：C# 串流][hdinsight-sample-csharp-streaming]
* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]
* [搭配 HDInsight 使用 Hivet][hdinsight-use-hive]
* [Azure HDInsight SDK 文件][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx


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