<properties 
	pageTitle="在 Azure Data Factory 中使用「複製活動」的範例" 
	description="提供在 Azure Data Factory 中使用「複製活動」的範例。" 
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

# 在 Azure Data Factory 中使用「複製活動」的範例
您可以在管線中使用 [**複製活動**]，以批次方式將資料從來源複製到接收器 (目的地)。本主題提供在 Data Factory 管線中使用「複製活動」的一些範例。如需「複製活動」的詳細概觀及其支援的核心案例，請參閱[使用 Azure Data Factory 複製資料][adf-copyactivity]。

## 將資料從內部部署 SQL Server 資料庫複製至 Azure Blob
在此範例中會定義輸入資料表和輸出資料表，並且在管線內的「複製活動」中使用這些資料表，將資料從內部部署 SQL Server 資料庫複製到 Azure Blob。

### 假設
這個範例假設您已經有下列 Azure Data Factory 構件：

* 名為 **ADF** 的資源群組。
* 名為 **CopyFactory** 的 Azure Data Factory。
* 名為 **mygateway** 的資料管理閘道已建立且上線。  

### 建立來源內部部署 SQL Server 資料庫的連結服務
在此步驟中，您建立指向內部部署 SQL Server 資料庫的連結服務，名為 **MyOnPremisesSQLDB**。

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
	        "connectionString": "Data Source=<servername>;Initial Catalog=<database>;Integrated Security=False;User ID=<username>;Password=<password>;",
	        "gatewayName": "mygateway"
	    }
	}

請注意：

- **type** 設為 **OnPremisesSqlLinkedService**。
- **connectionString** 設為 SQL Server 資料庫的連接字串。 
- **gatewayName** 設定為安裝在內部部署電腦，並向 Azure Data Factory 服務入口網站註冊過的資料管理閘道名稱。 

如需定義內部部署 SQL 連結服務之 JSON 項目的詳細資料，請參閱[內部部署 SQL 連結服務](https://msdn.microsoft.com/library/dn893523.aspx)。
 
### 建立目的地 Azure Blob 的連結服務
在此步驟中，您建立指向 Azure Blob 儲存體的連結服務，名為 **MyAzureStorage**。

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

請注意：

- **type** 設為 **AzureStorageLinkedService**。
- **connectionString** - 指定 Azure 儲存體的帳戶名稱和帳戶金鑰。

如需定義 Azure 儲存體連結服務之 JSON 項目的詳細資料，請參閱 [Azure 儲存體連結服務](https://msdn.microsoft.com/library/dn893522.aspx)。

### 輸入資料表 JSON
下列 JSON 指令碼定義參照 SQL 資料表的輸入資料表：**MyTable** 是由 **MyOnPremisesSQLDB** 連結服務定義的內部部署 SQL Server 資料庫。請注意，**name** 是 Azure Data Factory 資料表的名稱，而 **tableName** 是 SQL Server 資料庫中的 SQL 資料表名稱。

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

請注意：

- **type** 設為 **OnPremisesSqlServerTableLocation**。
- **tableName** 設為 **MyTable**，其中包含來源資料。 
- **linkedServiceName** 設為 **MyOnPremisesSQLDB**，這是您為內部部署 SQL 資料庫建立的連結服務。

如需定義 Data Factory 資料表 (其參照 SQL Server 資料表) 之 JSON 項目的詳細資料，請參閱[內部部署 SQL 位置屬性](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL)。

### 輸出資料表 JSON
下列 JSON 指令碼定義輸出資料表：**MyAzureBlob** (這指的是 Azure Blob)：**MyBlob** (位於 Blob 資料夾)：**MySubFolder** (位於 Blob 容器)：**MyContainer**。
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

請注意：
 
- **type** 設為 **AzureBlobLocation**。
- **folderPath** 設為 **MyContainer/MySubFolder**，其中包含存放已複製資料的 Blob。 
- **fileName** 設為 **MyBlob** (將存放輸出資料的 Blob)。
- **linkedServiceName** 設為 **MyAzureStorge** (您為 Azure 儲存體建立的連結服務)。    

如需定義 Data Factory 資料表 (其參照 Azure Blob) 之 JSON 項目的詳細資料，請參閱 [Azure Blob 位置屬性](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)。

### 管線 (具有複製活動) JSON
在此範例中，管線：**CopyActivityPipeline** 使用下列屬性定義：

- **type** 屬性設為 **CopyActivity**。
- **MyOnPremTable** 指定為輸入 (**inputs** 標記)。
- **MyAzureBlob** 指定為輸出 (**outputs** 標記)。
- [**轉換**] 區段包含兩個子區段：[**來源**] 和 [**接收器**]。來源的類型設為 **SqlSource**，而接收器的類型設為 **BlobSink**。**sqlReaderQuery** 定義要在來源上執行的轉換 (投射)。如需有關所有屬性的詳細資料，請參閱 [JSON 指令碼參考][json-script-reference]。

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}

請參閱[管線 JSON 參考](https://msdn.microsoft.com/library/dn834988.aspx)，以了解定義 Data Factory 管線之 JSON 項目的詳細資料；以及[支援的來源和接收](https://msdn.microsoft.com/library/dn894007.aspx)，以了解 SqlSource (例如：在此範例中為 **sqlReaderQuery **) 和 BlobSink 的屬性。


## 將資料從內部部署檔案系統複製至 Azure Blob
您可以使用「複製活動」，將檔案從內部部署系統 (Windows/Linux 網路共用或 Windows 本機主機) 複製至 Azure Blob。主機可以是已設定 Samba 的 Windows 或 Linux。資料管理閘道應該安裝在可連線至主機的 Windows 電腦上。

### 假設
此範例的假設如下：

- **主機** - 裝載檔案系統的伺服器名稱是：**\contoso**。
- **資料夾** - 含有輸入檔案的資料夾名稱是：**marketingcampaign\regionaldata\{slice}，裡面的檔案會在 {slice} 資料夾中分割，例如 2014121112 (2014 年 12 月 11 日 12 時)。
### 建立內部部署檔案系統連結服務
下列範例 JSON 可以用來建立名為 **FolderDataStore** 的連結服務，其類型為 **OnPremisesFileSystemLinkedService**。

	{
	    "name": "FolderDataStore",
	    "properties": {
	        "type": "OnPremisesFileSystemLinkedService",
	        "host": "\contoso",
	        "userId": "username",
	        "password": "password",
	        "gatewayName": "ContosoGateway"
	    }
	}

> [AZURE.NOTE]JSON 檔案中的主機和資料夾名稱一定要使用逸出字元 ''。如果是 **\Contoso**，請使用 **\Contoso**。

如需定義內部部署檔案系統連結服務之 JSON 項目的詳細資料，請參閱[內部部署檔案系統連結服務](https://msdn.microsoft.com/library/dn930836.aspx)。

### 建立目的地 Azure Blob 的連結服務
下列範例 JSON 可以用來建立名為 **MyAzureStorage** 的連結服務，其類型為 **AzureStorageLinkedSerivce**。

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

如需定義 Azure 儲存體連結服務之 JSON 項目的詳細資料，請參閱 [Azure 儲存體連結服務](https://msdn.microsoft.com/library/dn893522.aspx)。

### 建立輸入資料表
下列 JSON 指令碼定義的輸入資料表，是參照先前所建立的內部部署檔案系統連結服務。

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\{Slice}",
	            "partitionedBy": [
	                { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }
	            ],
	            "linkedServiceName": "FolderDataStore"
	        },
	        "availability": {
	            "waitOnExternal": { },
	            "frequency": "Hour",
	            "interval": 24
	        }
	    }
	}

如需定義 Data Factory 資料表 (其參照內部部署檔案系統) 之 JSON 項目的詳細資料，請參閱[內部部署檔案系統位置屬性](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)。

### 建立輸出資料表
下列 JSON 指令碼定義輸出資料表：**AzureBlobDest** (這指的是 Azure Blob)：**MyBlob** (位於 Blob 資料夾)：**MySubFolder** (位於 Blob 容器)：**MyContainer**。
         
	{
   		"name": "AzureBlobDest",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

如需定義 Data Factory 資料表 (其參照 Azure Blob) 之 JSON 項目的詳細資料，請參閱 [Azure Blob 位置屬性](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)。

### 建立管線
下列管線 JSON 定義具有「複製活動」的管線，可將資料從內部部署檔案系統複製至目的地 Azure Blob。
 
	{
	    "name": "CopyFileToBlobPipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "Ingress",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "OnPremFileSource" } ],
	                "outputs": [ { "name": "AzureBlobDest" } ],
	                "transformation": {
	                    "source": {
	                        "type": "FileSystemSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 4,
	                    "timeout": "00:05:00"
	                }
	            }
	        ]
	    }
	}

在此範例中，管線會將內容複製為二進位，而不需要進行任何剖析或執行任何轉換。請注意，您可以利用**並行**，平行複製檔案的配量。當您想要移動過去已發生的配量時，這非常有用。

> [AZURE.NOTE]具有相同主機、透過 UNC 路徑並使用不同使用者帳戶的並行複製活動可能會導致錯誤，例如「不允許相同使用者使用多個使用者名稱進行多個伺服器或共用資源連線」。這是作業系統的安全性限制。請排程具有不同閘道的複製活動，或在主機內安裝閘道，使用 “localhost” 或 “local”，而非 UNC 路徑。

請參閱[管線 JSON 參考](https://msdn.microsoft.com/library/dn834988.aspx)，以了解定義 Data Factory 管線之 JSON 項目的詳細資料；以及[支援的來源和接收](https://msdn.microsoft.com/library/dn894007.aspx)，以了解 FileSystemSource 和 BlobSink 的屬性。

### 使用檔案系統資料表的其他案例

#### 複製特定資料夾下的所有檔案
請注意，範例 JSON 中只指定 **folderPath**。

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}
 
#### 複製特定資料夾下的所有 CSV 檔案
請注意，**fileFilter** 設為 ***.csv**。

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "*.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

#### 複製特定檔案
請注意，**fileFiter** 設定為特定檔案：**201501.csv**。

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "201501.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

## 將資料從內部部署 Oracle 資料庫複製至 Azure Blob
您可以使用「複製活動」，將檔案從內部部署 Oracle 資料庫複製至 Azure Blob。

### 建立內部部署 Oracle 資料庫的連結服務
下列 JSON 可以用來建立指向內部部署 Oracle 資料庫的連結服務。請注意，**type** 設為 **OnPremisesOracleLinkedService**。

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

如需定義內部部署 Oracle 連結服務之 JSON 項目的詳細資料，請參閱[內部部署 Oracle 連結服務](https://msdn.microsoft.com/library/dn948537.aspx)。

### 建立目的地 Azure Blob 的連結服務
下列範例 JSON 可以用來建立名為 **MyAzureStorage** 的連結服務，其類型為 **AzureStorageLinkedSerivce**。

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

如需定義 Azure 儲存體連結服務之 JSON 項目的詳細資料，請參閱 [Azure 儲存體連結服務](https://msdn.microsoft.com/library/dn893522.aspx)。

### 建立輸入資料表
下列範例 JSON 可以用來建立 Azure Data Factory 資料表，而此資料表參照內部部署 Oracle 資料庫中的資料表。請注意，**位置類型**設為 **OnPremisesOracleTableLocation**。

	{
	    "name": "TableOracle",
	    "properties": {
	        "location": {
	            "type": "OnPremisesOracleTableLocation",
	            "tableName": "LOG",
	            "linkedServiceName": "OnPremOracleSource"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": "1",
	            "waitOnExternal": {}
	        },
	        "policy": {}
	    }
	} 

如需定義 Data Factory 資料表 (其參照內部部署 Oracle 資料庫中的資料表) 之 JSON 項目的詳細資料，請參閱[內部部署 Oracle 位置屬性](https://msdn.microsoft.com/library/dn894089.aspx#Oracle)。

### 建立輸出資料表
下列 JSON 指令碼定義輸出資料表：**MyAzureBlob** (這指的是 Azure Blob)：**MyBlob** (位於 Blob 資料夾)：**MySubFolder** (位於 Blob 容器)：**MyContainer**。
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "AzureBlobDest",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

如需定義 Data Factory 資料表 (其參照 Azure Blob) 之 JSON 項目的詳細資料，請參閱 [Azure Blob 位置屬性](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)。

### 建立管線
下列範例管線有一個「複製活動」，會將資料從 Oracle 資料庫資料表複製至 Azure 儲存體 Blob。

	{
	    "name": "PipelineCopyOracleToBlob",
	    "properties": {
	        "activities": [
	            {
	                "name": "CopyActivity",
	                "description": "copy slices of oracle records to azure blob",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "TableOracle" } ],
	                "outputs": [ { "name": "TableAzureBlob" } ],
	                "transformation": {
	                    "source": {
	                        "type": "OracleSource",
	                        "oracleReaderQuery": "$$Text.Format('select * from LOG where "Timestamp" >= to_date('{0:yyyy-MM-dd}', 'YYYY-MM-DD') AND "Timestamp" < to_date('{1:yyyy-MM-dd}', 'YYYY-MM-DD')', SliceStart, SliceEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 3,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2015-03-01T00:00:00Z",
	        "end": "2015-03-15T00:00:00Z",
	        "isPaused": false
	    }
	}

請參閱[管線 JSON 參考](https://msdn.microsoft.com/library/dn834988.aspx)，以了解定義 Data Factory 管線之 JSON 項目的詳細資料；以及[支援的來源和接收](https://msdn.microsoft.com/library/dn894007.aspx)，以了解 OracleSource 和 BlobSink 的屬性。

## 另請參閱

- [使用 Azure Data Factory 複製資料][adf-copyactivity]
- [複製活動 - JSON 指令碼參考](https://msdn.microsoft.com/library/dn835035.aspx)
- [影片：Azure Data Factory 複製活動簡介][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!---HONumber=62-->