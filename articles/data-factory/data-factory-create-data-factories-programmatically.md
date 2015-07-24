<properties 
	pageTitle="使用 Data Factory SDK 來建立、監視及管理 Azure Data Factory" 
	description="了解如何使用 Data Factory .NET SDK，以程式設計方式建立、監視和管理 Azure Data Factory。" 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# 使用 Data Factory .NET SDK 來建立、監視及管理 Azure Data Factory
## 概觀
您可以使用 Data Factory .NET SDK，以程式設計方式建立、監視及管理 Azure Data Factory本文包含指導您建立範例 .NET 主控台應用程式的逐步解說，此應用程式將會建立並監視 Data Factory。請參閱 [Data Factory 類別庫參考][adf-class-library-reference]，以取得有關 Data Factory .NET SDK 的詳細資料。



## 必要條件

- Visual Studio 2012 或 2013
- 下載並安裝 [Azure .NET SDK][azure-developer-center]。
- 下載並安裝適用於 Azure Data Factory 的 NuGet 封裝逐步解說中包含相關指示。

## 逐步介紹
1. 使用 Visual Studio 2012 或 2013 來建立 C# .NET 主控台應用程式。
	<ol type="a">
	<li>啟動 <b>Visual Studio 2012</b> 或 <b>Visual Studio 2013</b>。</li>
	<li>按一下 [<b>檔案</b>]，指向 [<b>新增</b>]，然後按一下 [<b>專案</b>]。</li> 
	<li>展開 [範本]<b></b>，然後選取 [Visual C#]<b></b>。在此逐步解說中，您使用的是 C#，但您可以使用任何 .NET 語言。</li> 
	<li>從右邊的專案類型清單中選取 [主控台應用程式]<b></b>。</li>
	<li>在 [名稱]<b></b> 輸入 <b>DataFactoryAPITestApp</b>。</li> 
	<li>在 [<b>位置</b>] 中選取 <b>C:\ADFGetStarted</b>。</li>
	<li>按一下 [確定]<b></b> 以建立專案。</li>
</ol>
2. 按一下 [<b>工具</b>]，指向 [<b>NuGet 封裝管理員</b>]，然後按一下 [<b>封裝管理員主控台</b>]。
3.	在 [Package Manager Console]<b></b> 中，逐一執行下列命令。</b>。 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre
		Install-Package Microsoft.DataFactories.Runtime –Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. 將下列 **appSetttings** 區段加入 **App.config** 檔案。Helper 方法使用的是：**Microsoft.identitymodel.waad.preview.graph.graphinterface**。 

	以您的 Azure 訂用帳戶與租用戶識別碼取代 **SubscriptionId** 和 **ActiveDirectoryTenantId** 的值。從 Azure PowerShell 執行 **Get-AzureAccount** 即可取得這些值 (您可能需要先使用 Add-AzureAccount 登入)。
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="49fb6e5f-3098-4fb2-ba2f-6d6eed843a65" />
    		<add key="ActiveDirectoryTenantId" value="37330244-7828-4a28-99b7-c8c3a437c7ac" />
		</appSettings>
6. 將下列 **using** 陳述式加入專案的原始程式檔 (Program.cs) 中。

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
				
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure; 
6. 將下列會建立 **DataPipelineManagementClient** 類別執行個體的程式碼加入 **Main** 方法中。您將使用此物件來建立 Data Factory、連結的服務、輸入和輸出資料表，以及管線。您也將使用此物件來監視執行階段的資料表配量。    

        // create data pipeline management client
        string resourceGroupName = "ADF";
        string dataFactoryName = "APITutorialFactory";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataPipelineManagementClient client = new DataPipelineManagementClient(aadTokenCredentials, resourceManagerUri);
7. 將下列會建立 **Data Factory** 的程式碼加入 **Main** 方法中。

        // create a data factory
        Console.WriteLine("Creating a data factory");
        client.DataFactories.CreateOrUpdate(resourceGroupName,
            new DataFactoryCreateOrUpdateParameters()
            {
                DataFactory = new DataFactory()
                {
                    Name = dataFactoryName,
                    Location = "westus",
                    Properties = new DataFactoryProperties() { }
                }
            }
        );
8. 將下列會建立**連結服務**的程式碼加入 **Main** 方法中。
	> [AZURE.NOTE]**帳戶名稱****帳戶金鑰****ConnectionString** 

		// create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new AzureStorageLinkedService()
                    {
                        ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=account key",
                    }
                }
            }
        );
9. 將下列會建立**輸入和輸出資料表**的程式碼加入 **Main** 方法中。 

	請注意，輸入 Blob 的 **FolderPath** 設定為 **adftutorial/**，其中 **adftutorial** 是 Blob 儲存體中的容器名稱。如果 Azure Blob 儲存體中沒有此容器，請以下列名稱建立容器：**adftutorial**，並將文字檔上傳至容器。
	
	請注意，輸出 Blob 的 FolderPath 設為：**adftutorial/apifactoryoutput/{Slice}**，其中 **Slice** 是根據 **SliceStart** (每個配量的開始日期時間) 的值自動計算而得。

 
        // create input and output tables
        Console.WriteLine("Creating input and output tables");
        string Table_Source = "TableBlobSource";
        string Table_Destination = "TableBlobDestination";

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Source,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/",
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                            WaitOnExternal = new WaitOnExternal()
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Destination,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                            PartitionedBy = new Collection<Partition>()
                            {
                                new Partition()
                                {
                                    Name = "Slice",
                                    Value = new DateTimePartitionValue()
                                    {
                                        Date = "SliceStart",
                                        Format = "yyyyMMdd-HH"
                                    }
                                }
                            },
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });
10. 將下列會**建立並啟用管線**的程式碼加入 **Main** 方法中。此管線有一個 **CopyActivity**，它以 **BlobSource** 為來源，**BlobSink** 為接收器。 

        // create a pipeline
        Console.WriteLine("Creating a pipeline");
        DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
        DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
        string PipelineName = "PipelineBlobSample";

        client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new PipelineCreateOrUpdateParameters()
            {
                Pipeline = new Pipeline()
                {
                    Name = PipelineName,
                    Properties = new PipelineProperties()
                    {
                        Description = "Demo Pipeline for data transfer between blobs",

                        // Initial value for pipeline's active period. With this, you won't need to set slice status
                        Start = PipelineActivePeriodStartTime,
                        End = PipelineActivePeriodEndTime,

                        Activities = new List<BaseActivity>()
                        {
                            new CopyActivity()
                            {
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput()
                                    {
                                        Name = Table_Source,
                                    }
                                },
                        
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Table_Destination, 
                                    }
                                },
                     
                                Transformation = new CopyActivityProperties()
                                {
                                    Source = new BlobSource()
                                    {
                                        BlobColumnSeparators = ",",
                                    },
                            
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    },

                                },

                                Policy = new ActivityPolicy()
                                {
                                    ExecutionPriorityOrder = ExecutionPriorityOrder.NewestFirst,
                                    Concurrency = 1,
                                    Retry = 2,
                                    Timeout = TimeSpan.FromMinutes(10),
                                }
                            }

                        },
                    }
                }
            });

11. 將 **Main** 方法所使用的下列 Helper 方法加入 **Program** 類別中。此方法會顯示一個對話方塊，讓您提供登入 Azure 入口網站的**使用者名稱**和**密碼**。
 
		public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AdfClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  
 
13. 將下列程式碼加入 **Main** 方法中，以取得輸出資料表的資料配量狀態。在此範例中只預期有配量。
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString());

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.Status == DataSliceStatus.Failed || slice.Status == DataSliceStatus.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.Status);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.Status);
                }
            }
        }

14. 將下列會取得資料配量之執行詳細資料的程式碼加入 **Main** 方法中。

        Console.WriteLine("Getting run details of a data slice");

        var datasliceRunListResponse = client.DataSliceRuns.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString());

        foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
        {
            Console.WriteLine("Status: \t\t{0}", run.Status);
            Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
            Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
            Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
            Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
            Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
            Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
        }

        Console.WriteLine("\nPress any key to exit.");
        Console.ReadKey();
    }

15. 建置主控台應用程式。按一下功能表上的 [建置]，再按一下 [建置方案]。
16. 確認您 Azure Blob 儲存體之 adftutorial 容器中至少有一個檔案。如果沒有，請在「記事本」中以下列內容建立 Emp.txt 檔案，然後將它上傳至 adftutorial 容器。

        John, Doe
		Jane, Doe
	 
17. 按一下功能表上的 [偵錯] -> [開始偵錯]，執行範例。
18. 使用 Azure Preview 入口網站確認 Data Factory：**APITutorialFactory** 是使用下列成品所建立： 
	- 連結服務：**LinkedService_AzureStorage** 
	- 資料表：**TableBlobSource** 和 **TableBlobDestination**。
	- 管線：**PipelineBlobSample** 
18. 確認輸出檔案已建立在 **adftutorial** 容器的 **apifactoryoutput** 資料夾中。


## 另請參閱

文章 | 說明
------ | ---------------
[Azure Data Factory 開發人員參考][developer-reference] | 開發人員參考包含 .NET 類別庫、Cmdlet、JSON 指令碼、函式等完整參考內容。 


[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/
 

<!---HONumber=62-->