<properties 
	pageTitle="使用 Azure Data Factory 複製資料" 
	description="了解如何使用 Azure Data Factory 中的複製活動將資料來源的資料複製到其他資料來源。" 
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

# 使用 Azure Data Factory 複製資料 (複製活動)
## 概觀
您可以在管線中使用 [**複製活動**]，以批次方式將資料從來源複製到接收器 (目的地)。複製活動可以用於下列案例：

- **輸入至 Azure**。在此案例中，資料會針對下列子案例，從內部部署資料來源 (例如 SQL Server) 複製到 Azure 資料存放區 (例如 Azure Blob、Azure 資料表或 Azure SQL Database)：
	- 在 Azure 上的集中位置收集資料，以進一步處理。
	- 將資料從內部部署上或非 Azure 雲端平台移轉至 Azure。
	- 將資料封存或備份至 Azure，以進行符合成本效益的分層式儲存。
- **從 Azure 輸出**。在此案例中，資料會針對下列子案例，從 Azure (例如 Azure Blob、Azure 資料表或 Azure SQL Database) 複製到內部部署資料超市和資料倉儲 (例如 SQL Server)：
	- 因缺乏雲端資料倉儲支援而將資料傳輸至內部部署。
	- 將資料傳輸至內部部署，以利用現有的內部部署解決方案或報告基礎結構。
	- 將資料封存或備份至內部部署，以進行分層式儲存
- **Azure 至 Azure 複製**。在此案例中，分散在 Azure 資料來源的資料會彙總到集中式的 Azure 資料存放區中。範例：Azure 資料表至 Azure Blob、Azure Blob 至 Azure 資料表，Azure 資料表至 Azure SQL、Azure Blob 至 Azure SQL。

若要深入了解，您可以：

- 請參閱影片 [Azure Data Factory 複製活動簡介][copy-activity-video]
- 請參閱[開始使用 Azure Data Factory][adfgetstarted] 中的教學課程，以了解如何使用「複製活動」將資料從 Azure Blob 儲存體複製到 Azure SQL Database。 
- 請參閱[讓您的管線使用內部部署資料][use-onpremises-datasources]中的逐步解說，以了解如何使用「複製活動」將資料從內部部署 SQL Server 資料庫複製到 Azure Blob 儲存體。


## 支援的來源與接收器
複製活動可支援下列資料移動案例：

- 將資料從 Azure Blob 複製至 Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server 或 IaaS 上的 SQL Server。
- 將資料從 Azure SQL Database 複製至 Azure Blob、Azure 資料表、Azure SQL Database、內部部署 SQL Server 或 IaaS 上的 SQL Server。
- 將資料從 Azure 資料表複製至 Azure Blob、Azure 資料表或 Azure SQL Database。
- 將資料從內部部署 SQL Server/IaaS 上的 SQL Server 複製至 Azure Blob 或 Azure SQL Database
- 將資料從內部部署 Oracle 資料庫複製至 Azure Blob
- 將資料從內部部署檔案系統複製至 Azure Blob
 

<table border="1">	
	<tr>
		<th><i>來源/接收器<i></th>
		<th>Azure Blob</th>
		<th>Azure 資料表</th>
		<th>Azure SQL Database</th>
		<th>內部部署 SQL Server</th>
		<th>IaaS 上的 SQL Server</th>
		<th>Azure DocumentDB</th>
	</tr>	

	<tr>
		<td><b>Azure Blob</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>

	<tr>
		<td><b>Azure 資料表</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
		<td>X</td>
	</tr>	

	<tr>
		<td><b>Azure SQL Database</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>


	<tr>
		<td><b>內部部署 SQL Server</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>IaaS 上的 SQL Server</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>內部部署檔案系統</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>內部部署 Oracle 資料庫</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>內部部署檔案系統</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>內部部署 MySQL 資料庫</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>內部部署 DB2 資料庫</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>內部部署 Teradata 資料庫</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>內部部署 Sybase 資料庫</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>內部部署 PostgreSQL 資料庫</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Azure DocumentDB</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

</table>

請參閱MSDN Library 上的「[支援的來源與接收](https://msdn.microsoft.com/library/dn894007.aspx)」主題，以了解詳細資料。

### 基礎架構即服務 (IaaS) 上的 SQL
IaaS 上的 SQL Server 也可以當做來源和接收器受到支援。必須有資料管理閘道，才能與 IaaS 上的 SQL Server 建立連結服務。您應該考慮在裝載 SQL Server 外的其他虛擬機器上安裝資料管理閘道，以避免因 SQL Server 和閘道競爭資源所造成的效能降低。如需有關「資料管理閘道」的詳細資料，請參閱[讓您的管線使用內部部署資料][use-onpremises-datasources]。

1.	具有公用 DNS 名稱以及靜態公用連接埠：私用連接埠對應的 VM
2.	具有公用 DNS 名稱、但未公開 SQL 端點的 VM
3.	虛擬網路
	<ol type='a'>
<li>在清單結尾處具有下列拓撲的 Azure 雲端 VPN。</li>	
<li>具有內部部署至雲端站對站 VPN (使用 Azure 虛擬網路) 的 VM。</li>	
</ol>![使用複製活動的 Data Factory][image-data-factory-copy-actvity]

## 複製活動 - 元件
複製活動包含下列元件：

- **輸入資料表**。資料表是具有結構描述的資料集，並且是矩形的。輸入資料表元件描述活動的輸入資料，包括下列各項：資料表的名稱、資料表的類型，以及參考含有輸入資料之資料來源的連結服務。
- **輸出資料表**。輸出資料表描述活動的輸入資料，包括下列各項：資料表的名稱、資料表的類型，以及參考含有輸出資料之資料來源的連結服務。
- **轉換規則**。轉換規則指定如何從來源擷取輸入資料以及輸出資料如何載入至接收器等等...
 
一個複製活動可以有一個**輸入資料表**和一個**輸出資料表**。

## <a name="CopyActivityJSONSchema"></a>複製活動的 JSON
管線由一或多個活動所組成。管線中的活動是在 [**活動**] 區段中定義。管線的 JSON 如下所示：
         
	{
		"name": "PipelineName",
		"properties": 
    	{
        	"description" : "pipeline description",
        	"activities":
        	[
	
    		]
		}
	}

[**活動**] 區段內的每個活動都有下列最上層結構。**type** 屬性應設為 **CopyActivity**。複製活動只可以有一個輸入資料表和一個輸出資料表。
         

	{
		"name": "ActivityName",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [],
		"outputs":  [],
		"transformation":
		{

		},
		"policy":
		{
		
		}
	}

下表說明用於活動區段的標記。

<table border="1">	
	<tr>
		<th align="left">標記</th>
		<th align="left">描述</th>
		<th align="left">必要</th>
	</tr>	

	<tr>
		<td>名稱</td>
		<td>活動的名稱。</td>
		<td>Y</td>
	</tr>	

	<tr>
		<td>說明</td>
		<td>說明活動用途的文字。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>類型</td>
		<td>指定活動的類型。<br/><br/><b>type</b> 應設為 <b>CopyActivity</b>。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>輸入</td>
		<td>活動所使用的輸入資料表。只能為「複製活動」指定一個輸入資料表。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>輸出</td>
		<td>活動所使用的輸出資料表。只能為「複製活動」指定一個輸出資料表。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>轉換</td>
		<td>轉換中的屬性取決於類型。<b>複製活動</b>要求您指定 [<b>轉換</b>] 區段內的 [<b>來源</b>] 和 [<b>接收器</b>] 區段。本文後面提供更多的詳細資料。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>原則</td>
		<td>會影響活動之執行階段行為的原則。如果未指定，則會使用預設值。</td>
		<td>N</td>
	</tr>


</table>

請參閱 [JSON 指令碼參考][json-script-reference]，以取得有關 JSON 屬性/標記的詳細資訊。

### 來源和接收器類型
如需來源和接收器類型的清單以及這些類型支援的屬性，請參閱 MSDN Library 上的[支援的來源和接收器][msdn-supported-sources-sinks]主題。

## 複製活動 - 範例
在此範例中會定義輸入資料表和輸出資料表，並且在管線內的「複製活動」中使用這些資料表，將資料從內部部署 SQL Server 資料庫複製到 Azure Blob。

**假設**後續的範例 JSON 指令碼將參考下列 Azure Data Factory 成品：

* 名為 **ADF** 的資源群組。
* 名為 **CopyFactory** 的 Azure Data Factory。
* 指向內部部署 SQL Server 資料庫且名為 **MyOnPremisesSQLDB** 的連結服務。
* 指向 Azure Blob 儲存體且名為 **MyAzureStorage** 的連結服務。

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

下列範例 Azure PowerShell 命令會使用 **New-AzureDataFactoryTable**，其中使用的 JSON 檔案會包含前述指令碼在 Azure Data Factory 中建立資料表 (**MyOnPremTable**)：**CopyFactory**。
         
	New-AzureDataFactoryTable -ResourceGroupName ADF –Name MyOnPremTable –DataFactoryName CopyFactory –File <Filepath>\MyOnPremTable.json.

請參閱 [Cmdlet 參考][cmdlet-reference]，以取得 Data Factory Cmdlet 的相關詳細資料。

### 輸出資料表 JSON
下列 JSON 指令碼會定義輸出資料表：**MyDemoBlob** (這指的是 Azure Blob)：**MyBlob** (位於 blob 資料夾)：**MySubFolder** (位於 blob 容器)：**MyContainer**。
         
	{
   		"name": "MyDemoBlob",
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

下列範例 Azure PowerShell 命令會使用 **New-AzureDataFactoryTable**，其中使用的 JSON 檔案會包含前述指令碼在 Azure Data Factory 中建立資料表 (**MyDemoBlob**)：**CopyFactory**。
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory –File <Filepath>


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


 下列範例 Azure PowerShell 命令會使用 **New-AzureDataFactoryPipeline**，其中使用的 JSON 檔案會包含前述指令碼在 Azure Data Factory 中建立管線 (**CopyActivityPipeline**)：**CopyFactory**。
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF –DataFactoryName CopyFactory –File <Filepath>

> [AZURE.NOTE]如需使用複製活動的更多範例，請參閱[在 Azure Data Factory 中使用複製活動的範例][copy-activity-examples]。

## Security
本章節包含完整的安全性方針和最佳作法，可協助建立複製活動資料存放區的安全存取。

在提供 HTTPS 連線的資料存放區中，請為複製活動選擇 HTTPS 連線，以建立透過網路的安全通訊。例如，若是 **Azure 儲存體**，請在連接字串中使用 **DefaultEndpointsProtocol = https**。

若是 **Azure SQL Database**，明確要求加密的連線，且不要信任伺服器憑證，以避免「攔截式」攻擊。若要達到此目的，請在連接字串中使用 **Encrypt = True** 和 **TrustServerCertificate = False**。如需詳細資訊，請參閱 Azure [SQL Database 方針和限制](https://msdn.microsoft.com/library/azure/ff394108.aspx)。

若是傳統的資料庫 (例如 **SQL Server**，尤其當執行個體位於 Azure 虛擬機器中時)，可透過設定簽署的憑證，並在連接字串中設定 **Encrypt = True** 和 **TrustServerCertificate = False** 來啟用加密的連接選項。如需詳細資訊，請參閱 [啟用 Database Engine 的加密連接 (SQL Server 組態管理員)](https://msdn.microsoft.com/library/ms191192(v=sql.110).aspx) 和「[連接字串語法](https://msdn.microsoft.com/library/ms254500.aspx)」。

## 進階案例
- **使用結構定義的資料行篩選**。根據資料表的類型，您可以藉由在資料表定義的**結構**定義中指定比存在於基礎資料來源中的資料行還少的資料行，來指定資料行的子集。
- **轉換規則 - 資料行對應**。資料行對應可以用來指定來源資料表中的資料行如何對應至接收器資料表中的資料行。
- **複製活動的資料類型處理**。說明在哪些情況下，會接受/忽略資料表定義的結構區段中所指定的資料類型。
- **叫用 SQL 接收器的預存程序**。將資料複製到 SQL Server 或 Azure SQL Database 時，可以設定並叫用使用者特定的預存程序。

請參閱[搭配 Azure Data Factory 使用複製活動的進階案例][copy-activity-advanced]一文，以取得這些案例的詳細資訊。

## 逐步解說
請參閱[開始使用 Azure Data Factory][adfgetstarted] 中的教學課程，以了解如何使用「複製活動」將資料從 Azure Blob 儲存體複製到 Azure SQL Database。
 
請參閱[讓您的管線使用內部部署資料][use-onpremises-datasources]中的逐步解說，以了解如何使用「複製活動」將資料從內部部署 SQL Server 資料庫複製到 Azure Blob 儲存體

## 另請參閱
- [複製活動 - 範例][copy-activity-examples]
- [影片：Azure Data Factory 複製活動簡介][copy-activity-video]
- [MSDN Library 上的複製活動主題][msdn-copy-activity]
- [搭配 Azure Data Factory 使用複製活動的進階案例][copy-activity-advanced]

[msdn-copy-activity]: https://msdn.microsoft.com/library/dn835035.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-tables-topic]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-supported-sources-sinks]: https://msdn.microsoft.com/library/dn894007.aspx
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx

[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[copy-activity-advanced]: data-factory-copy-activity-advanced.md
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png
 

<!---HONumber=62-->