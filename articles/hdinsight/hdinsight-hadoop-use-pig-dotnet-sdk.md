<properties
   pageTitle="在 HDInsight 中搭配使用 Hadoop Pig 與 .NET | Microsoft Azure"
   description="了解如何使用 .NET SDK for Hadoop 將 Pig 工作提交至 HDInsight 上的 Hadoop。"
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#在 HDInsight 中使用 .NET SDK for Hadoop 執行 Pig 工作

[AZURE.INCLUDE [Pig 選取器](../../includes/hdinsight-selector-use-pig.md)]

本文件提供使用 .NET SDK for Hadoop 將 Pig 工作提交至 HDInsight 叢集上的 Hadoop 的範例。

HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您輕鬆地從 .NET 使用 HDInsight 叢集。Pig 可讓您透過建立一系列資料轉換的模型，來建立 MapReduce 作業。您將學習如何使用基本 C# 應用程式將 Pig 工作提交至 HDInsight 叢集。

##<a id="prereq"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目。

* Azure HDInsight (HDInsight 上的 Hadoop) 叢集 (Windows 或 Linux 型)

* Visual Studio 2012 或 2013

##<a id="certificate"></a>建立管理憑證

若要向 Azure HDInsight 驗證應用程式，您必須建立自我簽署憑證、將它安裝在您的開發工作站上，同時將它上傳至您的 Azure 訂閱。

如需如何執行這項作業的相關指示，請參閱[建立自我簽署憑證](http://go.microsoft.com/fwlink/?LinkId=511138)。

> [AZURE.NOTE]建立憑證時，請一定要記下您使用的易記名稱，供稍後使用。

##<a id="subscriptionid"></a>尋找訂閱識別碼

每個 Azure 訂閱都是透過 GUID 值 (稱為訂閱識別碼) 予以識別。請使用下列步驟來尋找此值。

1. 瀏覽 [Azure 管理主控台](https://manage.windowsazure.com/)。

2. 從入口網站左側的列中，選取 [**設定**]。

3. 在頁面右側上所呈現的資訊中，找出您要使用的訂閱，並記下 [**訂閱識別碼**] 資料欄中的值。

儲存訂閱識別碼，供稍後使用。

##<a id="create"></a>建立應用程式

1. 開啟 Visual Studio 2012 或 2013

2. 從 [**檔案**] 功能表中，選取 [**新增**]，然後選取 [**專案**]。

3. 對於新的專案，輸入或選取下列值。

	<table>
<tr>
<th>屬性</th>
<th>值</th>
</tr>
<tr>
<th>類別</th>
<th>範本/Visual C#/Windows</th>
</tr>
<tr>
<th>範本</th>
<th>主控台應用程式</th>
</tr>
<tr>
<th>名稱</th>
<th>SubmitPigJob</th>
</tr>
</table>

4. 按一下 [確定] 以建立專案。

5. 從 [**工具**] 功能表中，選取 [**程式庫封裝管理員**] 或 [**Nuget 封裝管理員**]，然後選取 [**封裝管理員主控台**]。

6. 在主控台中執行下列命令，以安裝 .NET SDK 封裝。

		Install-Package Microsoft.Windowsazure.Management.HDInsight

7. 在 [方案總管] 中，按兩下 **Program.cs** 加以開啟。將現有程式碼取代為下者。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;

		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

		namespace SubmitPigJob
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            // Get the subscription ID
		            string subscriptionID = PromptForInput("Enter your Azure Subscription ID:");

		            // Get the certificate name
		            string certFriendlyName = PromptForInput("Enter the management certificate name:");

		            // Get the cluster name
		            string clusterName = PromptForInput("Enter the HDInsight cluster name:");

		            // Set the folder that job status is written to
		            string statusFolderName = @"/tutorials/usepig/status";

		            // The Pig Latin statements to run
		            string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;";

		            // Define the Pig job
		            PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
		            {
		                Query = queryString,
		                StatusFolder = statusFolderName
		            };

		            // Get the certificate object from certificate store using the friendly name to identify it
		            X509Store store = new X509Store();
		            store.Open(OpenFlags.ReadOnly);
		            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);

		            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

		            // Create a hadoop client to connect to HDInsight
		            var jobClient = JobSubmissionClientFactory.Connect(creds);

		            // Run the MapReduce job
		            Console.WriteLine("----- Submit the Pig job ...");
		            JobCreationResults mrJobResults = jobClient.CreatePigJob		(myJobDefinition);

		            // Wait for the job to complete
		            Console.WriteLine("----- Wait for the Pig job to complete ...");
		            WaitForJobCompletion(mrJobResults, jobClient);

		            // Display the error log
		            Console.WriteLine("----- The Pig job error log.");
		            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }

		            // Display the output log
		            Console.WriteLine("----- The Pig job output log.");
		            using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }

		            Console.WriteLine("----- Press ENTER to continue.");
		            Console.ReadLine();
		        }

		        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
		        {
		            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
		            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
		            {
		                jobInProgress = client.GetJob(jobInProgress.JobId);
		                Thread.Sleep(TimeSpan.FromSeconds(10));
		            }
		        }

		        private static string PromptForInput(string message)
		        {
		            Console.WriteLine(message);
		            return Console.ReadLine();
		        }
		    }
		}


7. 儲存檔案。

##<a id="run"></a>執行應用程式。

使用 **F5** 啟動應用程式。出現提示時，請輸入 [**訂閱識別碼**]、[**憑證易記名稱**] 和 [**HDInsight 叢集名稱**]。應用程式會在執行時產生數行資訊，而結尾則是與下列類似的內容。

```
----- The Pig job output log.
(TRACE,816)
(DEBUG,434)
(INFO,96)
(WARN,11)
(ERROR,6)
(FATAL,2)

----- Press ENTER to continue.
```

按 **ENTER** 結束應用程式。

##<a id="summary"></a>摘要

如您所見，.NET SDK for Hadoop 可讓您建立 .NET 應用程式，以將 Pig 工作提交至 HDInsight 叢集、監視工作狀態，以及擷取輸出。

##<a id="nextsteps"></a>接續步驟

如需 HDInsight 中 Pig 的一般資訊。

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊。

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

<!---HONumber=July15_HO2-->