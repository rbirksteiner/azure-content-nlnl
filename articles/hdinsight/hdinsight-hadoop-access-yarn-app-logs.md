<properties 
	pageTitle="以程式設計方式存取 Hadoop YARN 應用程式記錄檔 | Microsoft Azure" 
	description="在 HDInsight 中的 Hadoop 叢集上以程式設計方式存取應用程式記錄檔" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# 在 HDInsight 中的 Hadoop 上，以程式設計方式存取 YARN 應用程式記錄檔

本主題說明如何以程式設計方式列舉在 Azure HDInsight 中的 Hadoop 叢集上已完成的 YARN (另一種資源協調程式) 應用程式，以及如何以程式設計方式存取應用程式記錄檔，而不需使用端桌面通訊協定 (RDP) 連線到您的叢集。具體來說，已加入一個新元件和一個新 API：

  1. 已啟用 HDInsight 叢集上的泛型應用程式歷程記錄伺服器。它是 YARN Timeline Server 內的一個元件，負責儲存和抓取已完成之應用程式的相關泛型資訊。
  2. Azure HDInsight .Net SDK 中的 API 可讓您以程式設計方式列舉已在您叢集上執行的應用程式，並下載相關應用程式特定或容器特定的記錄檔 (純文字)，以協助對發生的任何應用程式問題進行偵錯。


## 必要條件

若要在 .NET Framework 應用程式中使用本主題提供的程式碼，必須有 Azure HDInsight SDK。您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 取得最新發佈的 SDK 組建。

若要從 Visual Studio 應用程式安裝 HDInsight SDK，請到 [**工具**] 功能表，按一下 [**Nuget 封裝管理員**]，然後按一下 [**封裝管理員主控台**]。在主控台中執行下列命令，以安裝封裝：

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

此命令會將 HDInsight 的 .NET 程式庫及其參考加入至目前的 Visual Studio 專案。


## <a name="YARNTimelineServer"></a>YARN Timeline Server

<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a> 透過兩個不同的介面提供已完成之應用程式的相關泛型資訊，以及架構特定應用程式資訊。具體而言：

* 儲存及擷取 HDInsight 叢集上泛型應用程式資訊的功能已在版本 3.1.1.374 或更新版本上啟用。 
* Timeline Server 的架構特定應用程式資訊元件目前在 HDInsight 叢集上並未提供。


應用程式的相關泛型資訊包含下列類型的資料：

* 應用程式識別碼，應用程式的唯一識別碼 
* 啟動應用程式的使用者 
* 為完成應用程式而進行之嘗試的相關資訊 
* 任何指定之應用程式嘗試所使用的容器 

在您的 HDInsight 叢集上，會由「Azure 資源管理員」將這項資訊儲存至您預設 Azure 儲存體帳戶之預設容器中的歷程記錄存放區。透過 REST API 即可抓取這項已完成之應用程式的相關泛型資料：

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps

我們已將新 API 加入至 HDInsight .NET SDK，方便以程式設計方式擷取這項資料。請注意，您也可以透過直接在叢集節點上執行 YARN 命令列介面 (CLI) 命令 (在使用 RDP 連線到叢集之後) 來擷取泛型資料。

## <a name="YARNAppsAndLogs"></a>YARN 應用程式和記錄檔

YARN 藉由將資源管理從應用程式排程/監視分離，支援多種程式設計模型 (MapReduce 為其中之一)。這是透過全域「*資源管理員*」(RM)、每一背景工作節點「*節點管理員*」(NM) 及每一應用程式「*應用程式主機*」(AM) 來達成。每一應用程式 AM 會與 RM 交涉用來執行您應用程式的資源 (CPU、記憶體、磁碟、網路)。RM 會與 NM 合作來授與這些資源 (以「*容器*」的形式授與)。AM 則是負責追蹤 RM 指派給它之容器的進度。視應用程式的本質而定，一個應用程式可能會需要許多容器。

此外，每個應用程式可能包含多個「*應用程式嘗試*」，以在應用程式發生當機時，或因 AM 與 RM 之間通訊中斷時，完成應用程式。因此，容器是被授與應用程式的特定嘗試。就某方面來說，容器會提供 YARN 應用程式所執行之基本工作單位的內容，而在容器的內容中完成的所有工作，都是在配置給該容器的單一背景工作節點上執行。請參閱 [YARN 概念][YARN-concepts]，以取得進一步的參考資料。

應用程式記錄檔 (和關聯的容器記錄檔) 在對有問題的 Hadoop 應用程式進行偵錯上相當重要。YARN 以[記錄檔彙總][log-aggregation]功能提供一個良好的架構，以收集、彙總及儲存應用程式記錄檔。「記錄檔彙總」功能讓存取應用程式記錄檔更具確定性，因為它會彙總背景工作節點上所有容器的記錄檔，並在應用程式完成之後，將它們以每一背景工作節點一個彙總記錄檔方式儲存在預設檔案系統上。您的應用程式可能使用數百或數千個容器，但在單一工作者節點上執行之所有容器的記錄檔將一律彙總成單一檔案，也就是為您應用程式所使用的每一背景工作節點產生一個記錄檔。「記錄檔彙總」在 HDInsight 叢集 (3.0 版和更新版本) 上預設為啟用，而在您叢集的預設容器中，即可找到彙總的記錄檔，位置如下：

	wasb:///app-logs/<user>/logs/<applicationId>

在該位置中，*user* 是啟動應用程式之使用者的名稱，*applicationId* 是 YARN RM 所指派的應用程式唯一識別碼。

您無法直接閱讀彙總的記錄檔，因為它們是以 [TFile][T-file]、由容器編制索引的[二進位格式][binary-format]撰寫。YARN 提供 CLI 工具，可針對您感興趣的應用程式或容器，將這些記錄檔傾印成純文字。您可以直接在叢集節點上 (透過 RDP 連線到節點之後) 執行下列其中一個 YARN 命令，以純文字格式檢視這些記錄檔：

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address> 

下一節說明如何以程式設計方式存取應用程式特定或容器特定記錄檔，而不需使用 RDP 連線到您的 HDInsight 叢集。

## <a name="enumerate-and-download"></a>以程式設計方式列舉應用程式及下載記錄檔

若要使用下列程式碼範例，您必須下載最新版的 HDInsight .NET SDK，以滿足上述必要條件。請參閱此處提供的指示。

以下程式碼說明如何使用新 API 來列舉應用程式，以及下載已完成之應用程式的記錄檔。

> [AZURE.NOTE]以下 API 只能在版本為 3.1.1.374 或更新版本的「正在執行」Hadoop 叢集上運作。新增下列指示詞：

	using Microsoft.Hadoop.Client;
	using Microsoft.WindowsAzure.Management.HDInsight;

這些會參考以下程式碼中新定義的 API。下列程式碼片段會在您訂用帳戶中的「正在執行」叢集上，建立一個「應用程式歷程記錄用戶端」。

	string subscriptionId = "<your-subscription-id>";
	string clusterName = "<your-cluster-name>";
	string certName = "<your-subscription-management-cert-name>";
	
	// Create an HDInsight client
	X509Store store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
	store.Open(OpenFlags.ReadOnly);
	X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>()
	                            .Single(x => x.FriendlyName == certName);
	
	HDInsightCertificateCredential creds = 
				new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
	
	IHDInsightClient client = HDInsightClient.Connect(creds);
	
	// Get the cluster on which your applications were run
	// The cluster needs to be in the "Running" state
	ClusterDetails cluster = client.GetCluster(clusterName);
	
	// Create an Application History client against your cluster
	IHDInsightApplicationHistoryClient appHistoryClient = 
				cluster.CreateHDInsightApplicationHistoryClient(TimeSpan.FromMinutes(5));


您現在可以使用「應用程式歷程記錄用戶端」來列出已完成的應用程式、根據您的條件篩選應用程式，以及下載相關的應用程式記錄檔。下列程式碼片段示範如何以程式設計方式執行這項操作：

	// Local download folder location where the logs will be placed
	string downloadLocation = "E:\\YarnApplicationLogs";
	
	// List completed applications on your cluster that were submitted in the last 24 hours but failed
	// Search for applications based on application name
	string appNamePrefix = "your-app-name-prefix";
	DateTime endTime = DateTime.UtcNow;
	DateTime startTime = endTime.AddHours(-24);
	IEnumerable<ApplicationDetails> applications = appHistoryClient
	                .ListCompletedApplications(startTime, endTime)
	                .Where(app => 
	                    app.GetApplicationFinalStatusAsEnum() == ApplicationFinalStatus.Failed 
	                    && app.Name.StartsWith(appNamePrefix));
	
	// Download logs for failed or killed applications
	// This will generate one log file for each application
	foreach (ApplicationDetails application in applications)
	{
	    appHistoryClient.DownloadApplicationLogs(application, downloadLocation);
	}

上述程式碼會使用「應用程式歷程記錄用戶端」來列出/尋找感興趣的應用程式，然後將這些應用程式的記錄檔下載到本機資料夾。

或者，也可以使用以下程式碼片段，針對已知其應用程式識別碼的應用程式來下載記錄檔。應用程式識別碼是 RM 指派給應用程式的全域唯一識別碼。其建構方式是使用 RM 的開始時間，連同提交給它之應用程式的依序遞增計數器，來進行建構。應用程式識別碼的格式為 "application_&lt;RM-start-time&gt;_&lt;Counter&gt;"。請注意，應用程式識別碼與工作識別碼截然不同。工作識別碼是 MapReduce 架構特定的概念，而應用程式識別碼則是跨架構的 YARN 概念。在 YARN 中，工作識別碼可識別特定的 MapReduce 工作，此工作是由提交給 RM 之 MapReduce 應用程式的 AM 處理。

	// Download application logs for an application whose application ID is known
	string applicationId = "application_1416017767088_0028";
	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	appHistoryClient.DownloadApplicationLogs(someApplication, downloadLocation);

您也可以視需要為應用程式所使用的每個容器 (或任何特定容器) 下載記錄檔，如下所示。

	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	
	// Download logs separately for each container of application(s) of interest
	// This will generate one log file per container
	IEnumerable<ApplicationAttemptDetails> applicationAttempts =
				appHistoryClient.ListApplicationAttempts(someApplication);
	
	ApplicationAttemptDetails finalAttempt = applicationAttempts
	    		.Single(x => x.ApplicationAttemptId == someApplication.LatestApplicationAttemptId);
	
	IEnumerable<ApplicationContainerDetails> containers =
				appHistoryClient.ListApplicationContainers(finalAttempt);
	
	foreach (ApplicationContainerDetails container in containers)
	{
	    appHistoryClient.DownloadApplicationLogs(container, downloadLocation);
	}



[YARN-timeline-server]: http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]: http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]: https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]: https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]: http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
 

<!---HONumber=July15_HO2-->