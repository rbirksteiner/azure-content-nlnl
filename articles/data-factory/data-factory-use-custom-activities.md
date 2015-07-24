<properties 
	pageTitle="在 Azure 資料處理站管線中使用自訂活動" 
	description="了解如何建立自訂活動，並在 Azure 資料處理站管線中使用這些活動。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="spelluru"/>

# 在 Azure 資料處理站管線中使用自訂活動
Azure Data Factory 支援在管線中使用內建活動來移動和處理資料，例如**複製活動**和 **HDInsight 活動**。您也可以使用您自己的轉換/處理邏輯建立自訂的 .Net 活動，並在管線中使用該活動。您可以將活動設定為使用 **Azure HDInsight** 叢集或 **Azure Batch** 服務來執行。

本文說明如何建立自訂活動，並在 Azure 資料處理站管線中使用它。本文也提供建立及使用自訂活動的詳細逐步解說與逐步指示。本逐步解說會使用 HDInsight 連結服務。若要改用 Azure Batch 連結服務，您需要建立 **AzureBatchLinkedService** 類型的連結服務，並將它用於管線 JSON 的活動區段 (**linkedServiceName**)。如需有關使用 Azure Batch 搭配自訂活動的詳細資訊，請參閱 [Azure Batch 連結服務](#AzureBatch)一節。

## 必要條件
下載並安裝 [Azure Data Factory 的 NuGet 封裝][nuget-package]最新版本。本文的[逐步解說](#SupportedSourcesAndSinks)中包含相關指示。

## 建立自訂活動

若要建立自訂活動：
 
1.	在 Visual Studio 2013 中建立「類別庫」專案。
3. 在類別庫中原始程式檔的頂端加入下列 using 陳述式。
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. 更新類別以實作 **IDotNetActivity** 介面。
	<ol type='a'>
	<li>
		從 <b>IDotNetActivity</b> 衍生類別。
		<br/>
		範例： <br/>
		public class <b>MyDotNetActivity : IDotNetActivity</b>
	</li>

	<li>
		實作 <b>IDotNetActivity</b> 介面的 <b>Execute</b> 方法
	</li>

</ol>
5. 編譯專案。


## 在管線中使用自訂活動
若要在管線中使用自訂活動：

1.	「壓縮」專案的 **bin\debug** 或 **bin\release** 輸出資料夾中的所有二進位檔案。 
2.	以 Blob 形式「上傳 zip」檔案至您的「Azure Blob 儲存體」。 
3.	更新「管線 JSON」檔案，以參考 zip 檔案、自訂活動 DLL、活動類別，以及包含管線 JSON 中 zip 檔案的 Blob。在 JSON 檔案中：
	<ol type ="a">
	<li>「活動類型」<b></b>應該設定為 <b>DotNetActivity</b>。</li>
	<li><b>AssemblyName</b> 是 Visual Studio 專案的輸出 DLL 的名稱。</li>
	<li><b>EntryPoint</b> 指定實作 <b>IDotNetActivity</b> 介面的「類別」<b></b>的「命名空間」<b></b>和「名稱」<b></b>。</li>
	<li><b>PackageLinkedService</b> 是連結服務，參考包含 zip 檔案的 Blob。</li>
	<li><b>PackageFile</b> 指定已上傳至 Azure Blob 儲存體的 zip 檔案的位置和名稱 。</li>
	<li><b>LinkedServiceName</b> 是連結服務的名稱，此服務將 HDInsight 叢集 (隨選或您所有) 連結至 Data Factory。自訂活動在指定 HDInsight 叢集上以僅限對應的作業執行。</li>
</ol>**部分 JSON 範例**

		"Name": "MyDotNetActivity",
    	"Type": "DotNetActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyDotNetActivity.dll",
    	    "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",

## 更新自訂活動
如果您更新自訂活動的程式碼，請建置它，並將包含新二進位檔案的 zip 檔案上傳至 Blob 儲存體。

## <a name="walkthrough" /> 逐步解說：
本逐步解說為您提供建立自訂活動以及在 Azure Data Factory 管線中使用該活動的逐步指示。本逐步解說延伸[開始使用 Azure Data Factory][adfgetstarted] 中的教學課程。如果您想要看自訂活動運作，您需要先依照入門教學課程操作，然後執行此逐步解說。

**必要條件：**


- [開始使用 Azure Data Factory][adfgetstarted] 中的教學課程。您必須完成本文中的教學課程，再執行本逐步解說。
- Visual Studio 2012 或 2013
- 下載並安裝 [Azure .NET SDK][azure-developer-center]
- 下載並安裝 [Azure Data Factory 的 NuGet 封裝][nuget-package]最新版本。逐步解說中包含相關指示。
- 下載並安裝 Azure 儲存體的 NuGet 封裝。逐步解說中有指示，因此您可以略過此步驟。

## 步驟 1：建立自訂活動

1.	建立 .NET 類別庫專案。
	<ol type="a">
	<li>啟動 <b>Visual Studio 2012</b> 或 <b>Visual Studio 2013</b>。</li>
	<li>按一下 [<b>檔案</b>]，指向 [<b>新增</b>]，然後按一下 [<b>專案</b>]。</li> 
	<li>展開 [範本]<b></b>，然後選取 [Visual C#]<b></b>。在此逐步解說中，您使用 C# 中，但您可以使用任何 .NET 語言來開發自訂活動。</li> 
	<li>從右邊的專案類型清單中選取 [類別庫]<b></b>。</li>
	<li>針對 [名稱]<b></b> 輸入 <b>MyDotNetActivity</b>。</li> 
	<li>在 [<b>位置</b>] 中選取 <b>C:\ADFGetStarted</b>。</li>
	<li>按一下 [確定]<b></b> 以建立專案。</li>
</ol>
2.  按一下 [<b>工具</b>]，指向 [<b>NuGet 封裝管理員</b>]，然後按一下 [<b>封裝管理員主控台</b>]。
3.	在 [封裝管理員主控台]<b></b> 中，執行下列命令匯入 <b>Microsoft.Azure.Management.DataFactories</b>。 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre

3.	在 [封裝管理員主控台]<b></b> 中，執行下列命令匯入 <b>Microsoft.DataFactories.Runtime</b>。資料夾以包含下載的資料處理站 NuGet 封裝的位置取代。

		Install-Package Microsoft.DataFactories.Runtime –Pre

4. 將 Azure 儲存體 NuGet 封裝匯入專案。

		Install-Package Azure.Storage

5. 將下列 **using** 陳述式加入至專案的原始程式檔。

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. 將 **namespace** 的名稱變更為 **MyDotNetActivityNS**。

		namespace MyDotNetActivityNS

7. 將類別的名稱變更為 **MyDotNetActivity**，並從 **IDotNetActivity** 介面延伸它，如下所示。

		public class MyDotNetActivity : IDotNetActivity

8. 對 **MyDotNetActivity** 類別實作 (加入) **IDotNetActivity** 介面的 **Execute** 方法，並將下列範例程式碼複製到方法。

	顧名思義，**inputTables** 和 **outputTables** 參數代表活動的輸入和輸出資料表。在可從 Azure 入口網站下載的記錄檔中，您可以使用 **logger** 物件，或使用 Cmdlet，以查看您記錄的訊息。**extendedProperties** 字典包含您在 JSON 檔案中為活動和其值指定的延伸屬性清單。

	下列範例程式碼會計算輸入 Blob 中的行數，並在輸出 Blob 中產生下列內容：Blob 的路徑、Blob 中的行數、執行活動的電腦、目前的日期時間。

        public IDictionary<string, string> Execute(
                    IEnumerable<ResolvedTable> inputTables, 
                    IEnumerable<ResolvedTable> outputTables, 
                    IDictionary<string, string> extendedProperties, 
                    IActivityLogger logger)
        {
            string output = string.Empty;

            logger.Write(TraceEventType.Information, "Before anything...");

            logger.Write(TraceEventType.Information, "Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write(TraceEventType.Information, "<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            foreach (ResolvedTable inputTable in inputTables)
            {
                string connectionString = GetConnectionString(inputTable.LinkedService);
                string folderPath = GetFolderPath(inputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Reading blob from: {0}", folderPath);

                CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
                CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

                BlobContinuationToken continuationToken = null;

                do
                {
                    BlobResultSegment result = inputClient.ListBlobsSegmented(folderPath, 
												true, 
												BlobListingDetails.Metadata, 
												null, 
												continuationToken, 
												null, 
												null);
                    foreach (IListBlobItem listBlobItem in result.Results)
                    {
                        CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                        int count = 0;
                        if (inputBlob != null)
                        {
                            using (StreamReader sr = new StreamReader(inputBlob.OpenRead()))
                            {
                                while (!sr.EndOfStream)
                                {
                                    string line = sr.ReadLine();
                                    if (count == 0)
                                    {
                                        logger.Write(TraceEventType.Information, "First line: [{0}]", line);
                                    }
                                    count++;
                                }

                            }

                        }
                        output += string.Format(CultureInfo.InvariantCulture,
                                        "{0},{1},{2},{3},{4}\n",
                                        folderPath,
                                        inputBlob.Name,
                                        count,
                                        Environment.MachineName,
                                        DateTime.UtcNow);

                    }
                    continuationToken = result.ContinuationToken;

                } while (continuationToken != null);
            }

            foreach (ResolvedTable outputTable in outputTables)
            {
                string connectionString = GetConnectionString(outputTable.LinkedService);
                string folderPath = GetFolderPath(outputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Writing blob to: {0}", folderPath);

                CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
                Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

                CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
                outputBlob.UploadText(output);

            }
            return new Dictionary<string, string>();

        }

9. 新增下列 Helper 方法。**Execute** 方法會叫用這些 Helper 方法。**GetConnectionString** 方法會擷取 Azure 儲存體連接字串，**GetFolderPath** 方法會擷取 Blob 位置。


        private static string GetConnectionString(LinkedService asset)
        {
            AzureStorageLinkedService storageAsset;
            if (asset == null)
            {
                return null;
            }

            storageAsset = asset.Properties as AzureStorageLinkedService;
            if (storageAsset == null)
            {
                return null;
            }

            return storageAsset.ConnectionString;
        }

        private static string GetFolderPath(Table dataArtifact)
        {
            AzureBlobLocation blobLocation;
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            blobLocation = dataArtifact.Properties.Location as AzureBlobLocation;
            if (blobLocation == null)
            {
                return null;
            }

            return blobLocation.FolderPath;
        }
   


10. 編譯專案。按一下功能表中的 [建置]，然後按一下 [建置方案]。
11. 啟動「Windows 檔案總管」，瀏覽至 **bin\debug** 或 **bin\release** 資料夾，根據建置類型而定。
12. 建立 zip 檔案 **MyDotNetActivity.zip**，檔案中包含 <project folder>\bin\Debug 資料夾中的所有二進位檔。
13. 將 **MyDotNetActivity.zip** 當做 Blob 上傳至 Blob 容器：Azure Blob 儲存體中的 **customactvitycontainer**，由 **ADFTutorialDataFactory** 中的 **MyBlobStore** 連結服務使用。建立 Blob 容器 **blobcustomactivitycontainer** (如果不存在)。 


## 步驟 2：在管線中使用自訂活動
以下是您將在此步驟中執行的步驟：

1. 建立 HDInsight 叢集的連結服務，而自訂活動將在叢集上以僅限對應的作業執行。 
2. 建立此範例中的管線將會產生的輸出資料表。
3. 建立並執行管線，該管線使用您在步驟 1 中建立的自訂活動。 
 
### 建立 HDInsight 叢集的連結服務，將會用來執行自訂活動
Azure Data Factory 服務支援建立隨選叢集，並使用它處理輸入來產生輸出資料。您也可以使用自己的叢集執行相同作業。當您使用隨選 HDInsight 叢集時，系統會為每個配量建立叢集。然而，如果您使用自己的 HDInsight 叢集，叢集已經準備好立即處理配量。因此，在使用隨選叢集時，可能無法像使用自己的叢集那麼快看到輸出資料。

> [AZURE.NOTE]在執行階段，.NET 活動的執行個體只在 HDInsight 叢集的一個背景工作節點上執行，無法擴展到多個節點上執行。.NET 活動的多個執行個體可以在 HDInsight 叢集的不同節點上平行執行。

如果您已透過[搭配 Azure Data Factory 使用 Pig 和 Hive][hivewalkthrough] 的逐步解說來延伸[開始使用 Azure Data Factory][adfgetstarted] 的教學課程，則可以略過建立此連結服務，並使用 ADFTutorialDataFactory 中已有的連結服務。


#### 若要使用隨選 HDInsight 叢集

1. 在「Azure 入口網站」，按一下 Data Factory 首頁中的 [製作和部署]。
2. 在 Data Factory 編輯器中，從命令列按一下 [新增計算]，然後從功能表選取 [隨選 HDInsight 叢集]。
2. 在 JSON 指令碼中，執行下列動作： 
	1. 在 **clusterSize** 屬性中，指定 HDInsight 叢集的大小。
	2. 在 **jobsContainer** 屬性中，指定要儲存叢集記錄檔的預設容器名稱。基於本教學課程的目的，請指定 **adfjobscontainer**。
	3. 在 **timeToLive** 屬性中，指定客戶閒置多久之後會被刪除。 
	4. 在 **version** 屬性中，指定您要使用的 HDInsight 版本。如果您排除此屬性，則會使用最新版本。  
	5. 在 **linkedServiceName** 中，指定您在「開始使用」教學課程中建立的 **StorageLinkedService**。 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. 按一下命令列上的 [部署]，部署連結服務。
   
#### 若要使用您自己的 HDInsight 叢集： 

1. 在「Azure 入口網站」，按一下 Data Factory 首頁中的 [製作和部署]。
2. 在 [Data Factory 編輯器] 中，從命令列按一下 [新增計算]，然後從功能表選取 [HDInsight 叢集]。
2. 在 JSON 指令碼中，執行下列動作： 
	1. 在 **clusterUri** 屬性中，輸入您的 HDInsight 的 URL。例如：https://<clustername>.azurehdinsight.net/     
	2. 在 **UserName** 屬性中，輸入具有 HDInsight 叢集存取權的使用者名稱。
	3. 在 **Password** 屬性中，輸入使用者的密碼。 
	4. 在 **LinkedServiceName** 屬性中，輸入 **StorageLinkedService**。這是您在「開始使用」教學課程中建立的連結服務。 

2. 按一下命令列的 [部署]，部署連結服務。

### 建立輸出資料表

1. 在 [Data Factory 編輯器] 中，按一下 [新增資料集]，然後從命令列按一下 [Azure Blob 儲存體]。
2. 使用下列 JSON 指令碼取代右窗格中的 JSON 指令碼：

		{
    		"name": "OutputTableForCustom",
    		"properties":
    		{
        		"location": 
        		{
					"type": "AzureBlobLocation",
					"folderPath": "adftutorial/customactivityoutput/{Slice}",
					"partitionedBy": [ { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }],

					"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1
        		}   
    		}
		}


 	輸出位置是 **adftutorial/customactivityoutput/YYYYMMDDHH/**，其中 YYYYMMDDHH 是產生配量的年、月、日、時。如需詳細資訊，請參閱[開發人員參考資料][adf-developer-reference]。

2. 按一下命令列的 [部署]，部署資料表。


### 建立並執行使用自訂活動的管線
   
1. 在 Data Factory 編輯器中，按一下工具列上的 [**新增管線**]。如果看不到此命令，請按一下 [...] (省略符號) 就可看到。 
2. 使用下列 JSON 指令碼取代右窗格中的 JSON。如果您想要使用自己的叢集，且已遵循步驟建立 **HDInsightLinkedService** 連結服務，請在下列 JSON 中以 **HDInsightLinkedService** 取代 **HDInsightOnDemandLinkedService**。 

		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyDotNetActivity",
                     	"Type": "DotNetActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "HDInsightLinkedService",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyDotNetActivity.dll",
                            "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
                            "ExtendedProperties":
							{
								"SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
							}
                      	},
                        "Policy":
                        {
                        	"Concurrency": 1,
                            "ExecutionPriorityOrder": "OldestFirst",
                            "Retry": 3,
                            "Timeout": "00:30:00",
                            "Delay": "00:00:00"		
						}
					}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	以目前日期的前三天取代 **StartDateTime** 值，並以目前日期取代 **EndDateTime** 值。StartDateTime 和 EndDateTime 都必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。將輸出資料表排定為每天產生，因此將產生三個配量。

	請注意：

	- activities 區段中有一個活動，它的類型是：**DotNetActivity**。
	- 使用您在「開始使用」教學課程中所用的相同輸入資料表 **EmpTableFromBlob**。
	- 使用您將在下一個步驟中建立的新輸出資料表 **OutputTableForCustom**。
	- **AssemblyName** 設定為此 DLL 的名稱：**MyActivities.dll**。
	- **EntryPoint** 設定為 **MyDotNetActivityNS.MyDotNetActivity**。
	- **PackageLinkedService** 設定為[開始使用 Azure Data Factory][adfgetstarted] 的教學課程中所建立的 **MyBlobStore**。此 Blob 存放區包含自訂活動 zip 檔案。
	- **PackageFile** 設定為 **customactivitycontainer/MyDotNetActivity.zip**。
     
4. 按一下命令列的 [部署] 以部署管線。
8. 確認 Blob 儲存體的 **adftutorial** 容器中已產生輸出檔案。

	![自訂活動的輸出][image-data-factory-ouput-from-custom-activity]

9. 如果您開啟輸出檔，您應該會看到類似下面的輸出：
	
	adftutorial/,emp.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(Blob 位置), (Blob 的名稱), (Blob 中的行數), (執行活動的節點), (日期時間戳記)

10.	使用 [Azure 入口網站][azure-preview-portal]或 Azure PowerShell Cmdlet 來監視您的 Data Factory、管線和資料集。在可從入口網站下載的記錄檔中，您可以從程式碼中的 **ActivityLogger**，或使用 Cmdlet，以查看自訂活動的訊息。

	![從自訂活動下載記錄檔][image-data-factory-download-logs-from-custom-activity]
   
如需有關監視資料集和管線的詳細步驟，請參閱[開始使用 Azure Data Factory][adfgetstarted]。
    
## <a name="AzureBatch"></a> 使用 Azure Batch 連結服務 
> [AZURE.NOTE]請參閱 [Azure Batch 技術概觀][batch-technical-overview]，以取得 Azure Batch 服務的概觀，另請參閱[開始使用適用於 .NET 的 Azure Batch 程式庫][batch-get-started]，以快速開始使用 Azure Batch 服務。

以下是上一節所述的逐步解說中使用 Azure Batch 連結服務的高階步驟：

1. 使用 Azure 管理入口網站建立 Azure Batch 帳戶。如需指示，請參閱 [Azure Batch 技術概觀][batch-create-account]。記下 Azure Batch 帳戶名稱和帳戶金鑰。 

	您也可以使用 [New-AzureBatchAccount][new-azure-batch-account] Cmdlet 建立 Azure Batch 帳戶。如需使用此 Cmdlet 的詳細指示，請參閱[使用 Azure PowerShell 管理 Azure Batch 帳戶][azure-batch-blog]。 
2. 建立 Azure Batch 集區。您可以下載並使用 [Azure Batch 總管工具][batch-explorer]建立集區 (或) 使用[適用於 .NET 的 Azure Batch 程式庫][batch-net-library] 建立集區。如需有關使用 Azure Batch 總管的逐步指示，請參閱 [Azure Batch 總管範例逐步解說][batch-explorer-walkthrough]。
	
	您也可以使用 [New-AzureBatchPool][new-azure-batch-pool] Cmdlet 建立 Azure Batch 集區。

2. 使用下列 JSON 範本建立 Azure Batch 連結服務。Data Factory 編輯器會顯示類似的範本讓您開始使用。JSON 片段中指定的 Azure Batch 帳戶名稱、帳戶金鑰和集區名稱。

		{
		    "name": "AzureBatchLinkedService",
		    "properties": {
		        "type": "AzureBatchLinkedService",
		        "accountName": "<Azure Batch account name>",
		        "accessKey": "<Azure Batch account key>",
		        "poolName": "<Azure Batch pool name>",
		        "linkedServiceName": "<Specify associated storage linked service reference here>"
		  }
		}

	如需這些屬性的描述，請參閱 [Azure Batch 連結服務 MSDN 主題](https://msdn.microsoft.com/library/mt163609.aspx)。

2.  在 Data Factory 編輯器 中，開啟您在逐步解說中建立的管線的 JSON 定義，並以 **AzureBatchLinkedService** 取代 **HDInsightLinkedService**。
3.  您可能希望變更管線的開始和結束時間，讓您能夠使用 Azure Batch 服務來測試案例。 
4.  在 Azure Batch 總管中，您可以看到與處理配量相關聯的 Azure Batch 工作，如下圖所示。

	![Azure Batch 工作][image-data-factory-azure-batch-tasks]

## 另請參閱

[Azure Data Factory 更新：使用 Azure Batch 執行 ADF 自訂 .NET 活動](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/)。

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-technical-overview.md/#batch-concepts
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-get-started.md
[hivewalkthrough]: data-factory-pig-hive-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png
 

<!---HONumber=62-->