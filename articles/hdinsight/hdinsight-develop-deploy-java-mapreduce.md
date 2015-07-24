<properties
	pageTitle="開發 Hadoop 的 Java MapReduce 程式 | Microsoft Azure"
	description="了解如何在 HDInsight 模擬器上開發 Java MapReduce 程式，以及如何部署至 HDInsight。"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="nitinme"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="04/01/2015"
	ms.author="nitinme"/>

# 在 HDInsight 上開發 Hadoop 的 Java MapReduce 程式

[AZURE.INCLUDE [Pig 選取器](../../includes/hdinsight-maven-mapreduce-selector.md)]

本教學課程引導您完成一項端對端案例，在 Java 中使用 Apache Maven 來開發字數統計 Hadoop MapReduce 工作。本教學課程也說明如何在 HDInsight Emulator for Azure 上測試應用程式，然後在以 Windows 為基礎的 HDInsight 叢集上部署並執行。

##<a name="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須完成下列工作：

- 安裝 HDInsight Emulator。如需指示，請參閱＜[開始使用 HDInsight Emulator][hdinsight-emulator]＞。確定所有必要服務都在執行中。在已安裝 HDInsight Emulator 的電腦上，從桌面捷徑啟動 Hadoop 命令列，導覽至 **C:\\hdp**，然後執行命令 **start_local_hdp_services.cmd**。
- 在模擬器電腦上安裝 Azure PowerShell。如需指示，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。
- 在模擬器電腦上安裝 Java 平台 JDK 7 或更高版本。模擬器電腦上已有此版本。
- 安裝和設定 [Apache Maven](http://maven.apache.org/)。
- 取得 Azure 訂用帳戶。如需指示，請參閱＜[購買選項][azure-purchase-options]＞、＜[成員優惠][azure-member-offers]＞或＜[免費試用][azure-free-trial]＞。


##<a name="develop"></a>使用 Apache Maven 以 Java 建立 MapReduce 程式

建立字數統計 MapReduce 應用程式。這是一個簡單的應用程式，可計算給定輸入集中每個字的出現次數。本節中，我們將執行下列工作：

1. 使用 Apache Maven 建立專案
2. 更新專案物件模型 (POM)
3. 建立字數統計 MapReduce 應用程式
4. 建置和封裝應用程式

**使用 Maven 建立專案**

1. 建立目錄 **C:\\Tutorials\\WordCountJava**。2. 從開發環境的命令列中，將目錄切換至您建立的位置。
3. 使用隨 Maven 一起安裝的 __mvn__ 命令來產生專案的結構。

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	這會在目前的目錄中建立新目錄，名稱由 __artifactID__ 參數指定 (此範例中為 **wordcountjava**)。 此目錄包含下列項目：

	* __pom.xml__ - [專案物件模型 (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)，包含用來建置專案的資訊和組態詳細資料。

	* __src__ - 含有 __main\\java\\org\\apache\\hadoop\\examples__ 目錄的目錄，您將在此撰寫應用程式。
3. 刪除 __src\\test\\java\\org\\apache\\hadoop\\examples\\apptest.java__ 檔案，因為此範例中不會用到。

**更新 POM**

1. 編輯 __pom.xml__ 檔案，在 `<dependencies>` 區段內新增下列程式碼：

		<dependency>
		  <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
        </dependency>
    	<dependency>
      	  <groupId>org.apache.hadoop</groupId>
      	  <artifactId>hadoop-mapreduce-client-common</artifactId>
      	  <version>2.5.1</version>
    	</dependency>
    	<dependency>
      	  <groupId>org.apache.hadoop</groupId>
      	  <artifactId>hadoop-common</artifactId>
      	  <version>2.5.1</version>
    	</dependency>

	這向 Maven 表示專案需要特定版本 (列於 <version> 內) 的程式庫 (列於 <artifactId> 內)。編譯時，將會從預設 Maven 儲存機制下載此版本。您可以使用 [Maven 儲存機制搜尋](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) (英文) 檢視詳細資訊。

2. 將下列程式碼新增至 __pom.xml__ 檔案。這必須在檔案中的 `<project>...</project>` 標籤內；例如，在 `</dependencies>` 和 `</project>` 之間。

		<build>
  		  <plugins>
    		<plugin>
      		  <groupId>org.apache.maven.plugins</groupId>
      		  <artifactId>maven-shade-plugin</artifactId>
      		  <version>2.3</version>
      		  <configuration>
        		<transformers>
          		  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		          </transformer>
        		</transformers>
      		  </configuration>
      		  <executions>
        		<execution>
          		  <phase>package</phase>
          			<goals>
            		  <goal>shade</goal>
          			</goals>
        	    </execution>
      		  </executions>
      	    </plugin>
  		  </plugins>
	    </build>

	這會設定 [Maven Shade 外掛程式](http://maven.apache.org/plugins/maven-shade-plugin/)，用來防止以 Maven 所建立的 JAR 中發生授權重複。使用此項目的理由在於，重複的授權檔會導致 HDInsight 叢集在執行階段發生錯誤。使用 Maven Shade 外掛程式與 `ApacheLicenseResourceTransformer` 實作可防止此錯誤。

	Maven Shade 外掛程式也會產生 uberjar (或稱為 fatjar)，內含應用程式需要的所有相依項目。

3. 儲存 __pom.xml__ 檔案。

**建立字數統計應用程式**

1. 移至 __wordcountjava\\src\\main\\java\\org\\apache\\hadoop\\examples__ 目錄，將 __app.java__ 檔案重新命名為 __WordCount.java__。
2. 開啟記事本。
2. 將下列程式複製並貼到記事本中：

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

	請注意，封裝名稱是 **org.apache.hadoop.examples**，類別名稱是 **WordCount**。提交 MapReduce 工作時會用到這些名稱。

3. 儲存檔案。

**建置和封裝應用程式**

1. 開啟命令提示字元，切換至 __wordcountjava__ 目錄。

2. 使用下列命令來建置含有應用程式的 JAR 檔案：

		mvn clean package

	這會清除任何先前的組建成品、下載任何尚未安裝的相依項目，然後建置並封裝應用程式。

3. 命令完成時，__wordcountjava\\target__ 目錄將包含一個名為 __wordcountjava-1.0-SNAPSHOT.jar__ 的檔案。

	> [AZURE.NOTE]__wordcountjava-1.0-SNAPSHOT.jar__ 檔案是 uberjar。


##<a name="test"></a>在模擬器上測試程式

在 HDInsight Emulator 上測試 MapReduce 工作時需要執行下列程序：

1. 將資料檔案上傳至模擬器上的 Hadoop 分散式檔案系統 (HDFS)
2. 建立本機使用者群組
3. 執行字數統計 MapReduce 工作
4. 擷取工作結果

依預設，HDInsight Emulator 使用 HDFS 做為檔案系統。您也可以選擇設定 HDInsight 模擬器來使用 Azure Blob 儲存體。如需詳細資料，請參閱＜[開始使用 HDInsight Emulator][hdinsight-emulator-wasb]＞。

本教學課程中，您將使用 HDFS **copyFromLocal** 命令將資料檔案上傳至 HDFS。下一節說明如何使用 Azure PowerShell 將檔案上傳至 Azure Blob 儲存體。如需有關將檔案上傳至 Azure Blob 儲存體的其他方法，請參閱＜[將資料上傳到 HDInsight][hdinsight-upload-data]＞。

本教學課程使用下列 HDFS 資料夾結構：

<table border="1">
<tr><td>資料夾</td><td>注意</td></tr>
<tr><td>/WordCount</td><td>字數統計專案的根資料夾。</td></tr>
<tr><td>/WordCount/Apps</td><td>對應器和歸納器可執行檔的資料夾。</td></tr>
<tr><td>/WordCount/Input</td><td>MapReduce 來源檔案資料夾。</td></tr>
<tr><td>/WordCount/Output</td><td>MapReduce 輸出檔案資料夾。</td></tr>
<tr><td>/WordCount/MRStatusOutput</td><td>工作輸出資料夾。</td></tr>
</table>

本教學課程使用 %hadoop_home% 目錄中的 .txt 檔案做為資料檔案。

> [AZURE.NOTE]Hadoop HDFS 命令區分大小寫。

**將資料檔案複製到模擬器 HDFS**

1. 從桌面開啟 Hadoop 命令列。Hadoop 命令是由模擬器安裝程式來安裝。
2. 從 Hadoop 命令列視窗中，執行下列命令來建立輸入檔案的目錄：

		hadoop fs -mkdir /WordCount
		hadoop fs -mkdir /WordCount/Input

	這裡使用的路徑是相對路徑。此命令相當於：

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3. 執行下列命令將一些文字檔案複製到 HDFS 上的輸入資料夾：

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /WordCount/Input

	MapReduce 工作將計算這些檔案中的字數。

4. 使用下列命令來列出並驗證已上傳的檔案：

		hadoop fs -ls /WordCount/Input

**建立本機使用者群組**

為了在叢集上成功執行 MapReduce 工作，您必須建立名為 hdfs 的使用者群組。在此群組中，您也必須加入稱為 hadoop 的使用者，和您用來登入模擬器的本機使用者。從提高權限的命令提示字元中，使用下列命令：

		# Add a user group called hdfs
		net localgroup hdfs /add

		# Adds a user called hadoop to the group
		net localgroup hdfs hadoop /add

		# Adds the local user to the group
		net localgroup hdfs <username> /add

**使用 Hadoop 命令列執行 MapReduce 工作**

1. 從桌面開啟 Hadoop 命令列。
2. 執行下列命令從 HDFS 中刪除 /WordCount/Output 資料夾結構。/WordCount/Output 是字數統計 MapReduce 工作的輸出資料夾。如果此資料夾已存在，MapReduce 工作會失敗。如果是第二次執行工作，則這是必要的步驟。

		hadoop fs -rm - r /WordCount/Output

2. 執行以下命令：

		hadoop jar C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

	如果工作順利完成，應該會得到類似下列螢幕擷取畫面的輸出：

	![HDI.EMulator.WordCount.Run][image-emulator-wordcount-run]

	從螢幕擷取畫面中，您可以看到對應和歸納都已 100% 完成。其中也列出工作識別碼。從桌面開啟 [**Hadoop MapReduce 狀態**] 捷徑並尋找相同的工作識別碼，可擷取同樣的報告。

另一個執行 MapReduce 工作的方法是使用 Azure PowerShell。如需指示，請參閱＜[開始使用 HDInsight Emulator][hdinsight-emulator]＞。

**從 HDFS 顯示輸出**

1. 開啟 Hadoop 命令列。
2. 執行下列命令來顯示輸出：

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-r-00000

	您可以在命令尾端附加 "|more" 來取得頁面檢視。或者，使用 **findstr** 命令來尋找字串模式：

		hadoop fs -cat /WordCount/Output/part-r-00000 | findstr "there"

現在您已開發字數統計 MapReduce 工作，並於模擬器上成功測試。下一步是在 Azure HDInsight 上部署並執行。



##<a id="upload"></a>將資料和應用程式上傳至 Azure Blob 儲存體
Azure HDInsight 使用 Azure Blob 儲存體來儲存資料。佈建 HDInsight 叢集時，Azure Blob 儲存體容器用來儲存系統檔案。您可以使用此預設容器或不同的容器 (在相同的 Azure 儲存體帳戶上，或與叢集相同的資料中心上的不同儲存體帳戶) 來儲存資料檔案。

本教學課程中，您將在另一個儲存體帳戶上建立容器來儲存資料檔案和 MapReduce 應用程式。資料檔是模擬器工作站上的 **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common** 目錄中的文字檔。

**建立 Blob 儲存體和容器**

1. 開啟 Azure PowerShell。
2. 設定變數，然後執行命令：

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

	**$subscripionName** 變數與您的 Azure 訂用帳戶相關聯。您必須命名 **$storageAccountName_Data** 和 **$containerName_Data**。關於命名限制，請參閱＜[命名和參考容器、Blob 及中繼資料](http://msdn.microsoft.com/library/windowsazure/dd135715.aspx)＞。

3. 執行下列命令來建立儲存體帳戶，並在帳戶上建立 Blob 儲存體容器：

		# Select an Azure subscription
		Select-AzureSubscription $subscriptionName

		# Create a Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location

		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4. 執行下列命令以驗證儲存體帳戶和容器：

		Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
		Get-AzureStorageContainer -Context $destContext

**上傳資料檔案**

1. 開啟 Azure PowerShell。
2. 設定前三個變數，然後執行命令：

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	**$storageAccountName_Data** 和 **$containerName_Data** 變數與您在上一個程序中的定義相同。

	請注意，來源檔案資料夾是 **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT**，目的地資料夾是 **WordCount/Input**。

3. 執行下列命令來取得來源檔案資料夾中的 .txt 檔案清單：

		# Get a list of the .txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4. 執行下列命令來建立儲存體內容物件：

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. 執行下列命令來複製檔案：

		# Copy the files from the local workstation to the Blob container
		foreach ($file in $filesTxt){

		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"

		    write-host "Copying $fileName to $blobName"

		    Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
		}

6. 執行下列命令來列出已上傳的檔案：

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

	您應該會看到已上傳的文字資料檔案。

**上傳字數統計應用程式**

1. 開啟 Azure PowerShell。
2. 設定前三個變數，然後執行命令：

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$jarFile = "C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar"
		$blobFolder = "WordCount/jars"

	**$storageAccountName_Data** 和 **$containerName_Data** 變數與您在上一個程序中的定義相同，這表示您會將資料檔案和應用程式上傳至相同儲存體帳戶上的相同容器。

	請注意，目的地資料夾是 **WordCount/jars**。

4. 執行下列命令來建立儲存體內容物件：

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. 執行下列命令來複製應用程式：

		Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

6. 執行下列命令來列出已上傳的檔案：

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

	您應該會看到這裡列出的 JAR 檔案。

##<a name="run"></a>在 Azure HDInsight 上執行 MapReduce 工作

本節中，您將建立 Azure PowerShell 指令碼來執行下列工作：

1. 佈建 HDInsight 叢集

	1. 建立儲存體帳戶做為預設 HDInsight 叢集檔案系統
	2. 建立 Blob 儲存體容器
	3. 建立 HDInsight 叢集

2. 提交 MapReduce 工作

	1. 建立 MapReduce 工作定義
	2. 提交 MapReduce 工作
	3. 等待工作完成
	4. 顯示標準錯誤
	5. 顯示標準輸出

3. 刪除叢集

	1. 刪除 HDInsight 叢集
	2. 刪除做為預設 HDInsight 叢集檔案系統的儲存體帳戶


**執行 Azure PowerShell 指令碼**

1. 開啟記事本。
2. 複製並貼上下列程式碼：

		# The Storage account and the HDInsight cluster variables
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"
		$location = "<MicrosoftDataCenter>"     ### Must match the data Storage account location
		$clusterNodes = <NumberOFNodesInTheCluster>

		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"

		$clusterName = $stringPrefix + "hdicluster"

		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# The MapReduce job variables
		$jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
		$className = "org.apache.hadoop.examples.WordCount"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

		# Create a PSCredential object. The user name and password are hardcoded here. You can change them if you want.
		$password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ("Admin", $password)

		Select-AzureSubscription $subscriptionName

		#=============================
		# Create a Storage account used as the default file system
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

		#=============================
		# Create a Blob storage container used as the default file system
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

		New-AzureStorageContainer -Name $containerName_Default -Context $destContext

		#=============================
		# Create an HDInsight cluster
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }

		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data

		New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config

		#=============================
		# Create a MapReduce job definition
		Write-Host "Create a MapReduce job definition" -ForegroundColor Green
		$mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus

		#=============================
		# Run the MapReduce job
		Write-Host "Run the MapReduce job" -ForegroundColor Green
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600

		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput

		#=============================
		# Delete the HDInsight cluster
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Remove-AzureHDInsightCluster -Name $clusterName  

		# Delete the default file system Storage account
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. 設定指令碼中的前六個變數。**$stringPrefix** 變數是用於對 HDInsight 叢集名稱、儲存體帳戶名稱和 Blob 儲存體容器名稱的指定字串加上首碼。因為這些項目的名稱必須為 3 到 24 個字元，請確定您指定的字串和此指令碼使用的名稱，合計不超過名稱的字元限制。**$stringPrefix** 必須全部為小寫。

	**$storageAccountName_Data** 和 **$containerName_Data** 變數是用來儲存資料檔案和應用程式的儲存體帳戶和容器。**$location** 變數必須符合資料儲存體帳戶位置。

4. 檢閱其餘變數。
5. 儲存指令碼檔案。
6. 開啟 Azure PowerShell。
7. 執行下列命令將執行原則設為 RemoteSigned：

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. 出現提示時，輸入 HDInsight 叢集的使用者名稱和密碼。因為您在指令碼最後會刪除叢集，然後就不再需要使用者名稱和密碼，所以使用者名稱和密碼可以是任何字串。如果不希望出現認證提示，請參閱＜[在 Windows PowerShell 中使用密碼、安全字串和認證][powershell-PSCredential]＞。


##<a name="retrieve"></a>擷取 MapReduce 工作輸出
本節說明如何下載和顯示輸出。如需有關在 Excel 上顯示結果的詳細資訊，請參閱＜[使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight][hdinsight-ODBC]＞和＜[使用 Power Query 將 Excel 連接到 HDInsight][hdinsight-power-query]＞。


**擷取輸出**

1. 開啟 Azure PowerShell 視窗。
2. 切換至 **C:\\Tutorials\\WordCountJava** 目錄。預設 Azure PowerShell 資料夾是 **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**。您執行的 Cmdlet 會將輸出檔案下載至目前的資料夾。您沒有權限將檔案下載至系統資料夾。
2. 執行下列命令來設定值：

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-r-00000"

3. 執行下列命令來建立 Azure 儲存體內容物件：

		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

4. 執行下列命令來下載和顯示輸出：

		Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

工作完成之後，您可以選擇使用 [Sqoop][hdinsight-use-sqoop] 將資料匯出至 SQL Server 或 Azure SQL Database，或將資料匯出至 Excel。

##<a id="nextsteps"></a>接續步驟
本教學課程中，您學到如何開發 Java MapReduce 工作、如何在 HDInsight Emulator 上測試應用程式，以及如何撰寫 Azure PowerShell 指令碼來佈建 HDInsight 叢集並於叢集上執行 MapReduce 工作。若要深入了解，請參閱下列文章：

- [開發 HDInsight 的 C# Hadoop 串流 MapReduce 程式][hdinsight-develop-streaming]
- [Azure HDInsight 使用者入門][hdinsight-get-started]
- [開始使用 HDInsight Emulator][hdinsight-emulator]
- [在 HDInsight 上使用 Azure Blob 儲存體][hdinsight-storage]
- [使用 Azure PowerShell 管理 HDInsight][hdinsight-admin-powershell]
- [將資料上傳到 HDInsight][hdinsight-upload-data]
- [搭配 HDInsight 使用 Hivet][hdinsight-use-hive]
- [搭配 HDInsight 使用 Pig][hdinsight-use-pig]
- [使用 Power Query 將 Excel 連接到 HDInsight][hdinsight-power-query]
- [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight][hdinsight-ODBC]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install-configure]: ../install-configure-powershell.md



[image-emulator-wordcount-compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
[image-emulator-wordcount-run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png

<!---HONumber=July15_HO2-->