<properties 
	pageTitle="在 Azure Data Factory 中使用複製活動的進階案例" 
	description="說明在 Azure Data Factory 中使用複製活動的進階案例" 
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
	ms.date="06/15/2015" 
	ms.author="spelluru"/>

# 在 Azure Data Factory 中使用複製活動的進階案例 
## 概觀
您可以在管線中使用 [**複製活動**]，以批次方式將資料從來源複製到接收器 (目的地)。本主題說明「複製活動」支援的進階案例。如需「複製活動」的詳細概觀及其支援的核心案例，請參閱[使用 Azure Data Factory 複製資料][adf-copyactivity]。


## 使用結構定義的資料行篩選
根據資料表類型，您可以在資料表定義的**結構**定義中，指定比存在於基礎資料來源中的資料行還少的資料行，即可從來源指定資料行的子集。下表提供不同資料表類型之資料行篩選邏輯的相關資訊。

<table>

	<tr>
		<th align="left">資料表類型</th>
		<th align="left">資料行篩選邏輯</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>資料表 JSON 中的<b>結構</b>定義必須與 Blob 的結構相符。若要選取資料行的子集，請使用下一節中所述的資料行對應功能：轉換規則 - 資料行對應。</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation 和 OnPremisesSqlServerTableLocation</td>
		<td align="left">
			如果複製活動定義中指定屬性 <b>SqlReaderQuery</b>，則資料表的<b>結構</b>定義應該與查詢中選取的資料行一致。<br/><br/>
			如果未指定屬性 <b>SqlReaderQuery</b>，複製活動將會根據資料表定義的<b>結構</b>定義中指定的資料行，自動建構 SELECT 查詢。
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			資料表定義中的<b>結構</b>區段可以包含基礎 Azure 資料表的全部或一部分資料行。
		</td>
	<tr>

</table>

## 轉換規則 - 資料行對應
資料行對應可以用來指定來源資料表中的資料行如何對應至接收器資料表中的資料行。它支援下列案例：

- 將來源資料表「結構」中的所有資料行對應至目的地資料表「結構」。
- 來源資料表「結構」中的資料行子集會對應至所有目的地資料表「結構」。

它不支援下列項目，而會擲回例外狀況：

- 目的地的資料行會較多或較少。
- 重複的對應。
- SQL 查詢結果沒有資料行名稱

尤其，在兩個 Azure Blob 之間複製資料時，複製活動通常將此動作視為直接二進位資料複製，除非符合下列 3 個案例：


1. 如果輸入和輸出資料表的格式不同，複製活動會執行格式轉換；
2. 如果輸入資料表指定為可能含有多個檔案的資料夾，而輸出資料表指定為檔案，複製活動會將來源資料夾下的檔案合併成單一接收器檔案；
3. 如果指定 "columnMapping"，複製活動會執行對應的資料轉換。


### 範例 1 – 從 SQL Server 到 Azure Blob 的資料行對應
在此範例中，**輸入資料表**的定義如下。輸入資料表都有一個結構，會指向 SQL Server 資料庫中的 SQL 資料表。
         
		{
		    "name": "MyOnPremTable",
    		"properties":
    		{
				"structure": 
				[
            		{ "name": "userid", "type": "String"},
            		{ "name": "name", "type": "String"},
            		{ "name": "group", "type": "Decimal"}
				],
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

在此範例中，**輸出資料表**的定義如下。輸出資料表有一個結構，會指向 Azure Blob 儲存體中的 Blob。
         
		
	{
		"name": "MyDemoBlob",
		"properties":
		{
    		"structure":
			[
        	    { "name": "myuserid", "type": "String"},
        	    { "name": "mygroup", "type": "String"},
        	    { "name": "myname", "type": "Decimal"}
			],
			"location":
    		{
    	    	"type": "AzureBlobLocation",
		        "folderPath": "MyContainer/MySubFolder",
				"fileName": "MyBlobName",
    	    	"linkedServiceName": "MyLinkedService",
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

如果您未指定**輸出資料表**的 **fileName**，**folderPath** 中產生的檔案會依照下列格式命名：<Guid>.txt (例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

若要根據 **SliceStart** 時間動態設定 **folderPath** 和 **fileName**，請使用 **partitionedBy** 屬性。在下列範例中，**folderPath** 使用 SliceStart (處理配量的開始時間) 中的 Year、Month 和 Day，fileName 使用 SliceStart 中的 Hour。例如，如果配量產生於 2014-10-20T08:00:00，folderName 設定為 wikidatagateway/wikisampledataout/2014/10/20，而 fileName 設定為 08.csv。

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

#### 範例 – 定義資料行對應
在此範例中，管線中的活動定義如下。來源的資料行使用 **Translator** 屬性，對應至接收器中的資料行 (**columnMappings**)。

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "MyOnPremTable"  } ],
		"outputs":  [ { "name": "MyDemoBlob" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource"
    		},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
    		}
		}
	}

![資料行對應][image-data-factory-column-mapping-1]

### 範例 2 – 透過 SQL 查詢從 SQL Server 至 Azure Blob 的資料行對應
在此範例中，SQL 查詢 (相對於前一個範例中的資料表) 會用來從內部部署 SQL Server 擷取資料，且查詢結果中的資料行會對應至來源成品，然後對應至目的地成品。根據此範例的用途，查詢會傳回 5 個資料行。

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource",
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = '{0:yyyyMMdd-HH}'', SliceStart)"
			},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
    		}
		}
	}

![資料行對應 2][image-data-factory-column-mapping-2]

## 複製活動的資料類型處理

資料表定義的「結構」區段中指定的資料類型，僅適用於 **BlobSource**。下表說明為其他類型的來源和接收器處理資料類型的方式。

<table>	
	<tr>
		<th align="left">來源/接收器</th>
		<th align="left">資料類型處理邏輯</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>資料表定義的<b>結構</b>區段中定義的資料類型會被忽略。基礎 SQL 資料庫上定義的資料類型將在複製活動期間，用來擷取資料。</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>資料表定義的<b>結構</b>區段中定義的資料類型會被忽略。基礎來源和目的地上的資料類型將被比較，如果有類型不符的情況，就會以隱含方式進行類型轉換。</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td>從 <b>BlobSource</b> 傳輸至 <b>BlobSink</b> 時，不會轉換類型。資料表定義的<b>結構</b>區段中定義的資料類型會被忽略。針對 <b>BlobSink</b> 以外的目的地，將會接受資料表定義的<b>結構</b>區段中定義的資料類型。<br/><br/>
		如果資料表定義中未指定<b>結構</b>，則會根據 <b>BlobSource</b> 資料表的 <b>format</b> 屬性來處理類型：
		<ul>
			<li> <b>TextFormat：</b>所有資料行類型都視為字串，且所有資料行名稱都設為 "Prop_&lt;0-N>"</li> 
			<li><b>AvroFormat：</b>使用 Avro 檔案中的內建資料行類型和名稱。</li> 
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>資料表定義的<b>結構</b>區段中定義的資料類型會被忽略。會使用定義於基礎輸入資料存放區的資料類型。資料行將針對 Avro 序列化指定為可為 Null。</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>資料表定義的<b>結構</b>區段中定義的資料類型會被忽略。將使用基礎 Azure 資料表上定義的資料類型。</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>資料表定義的<b>結構</b>區段中定義的資料類型會被忽略。會使用定義於基礎輸入資料存放區的資料類型。</td>
	</tr>

</table>

**注意：**Azure 資料表僅支援一組有限的資料類型，請參閱[了解表格服務資料模型][azure-table-data-type]。

## 叫用 SQL 接收器的預存程序
將資料複製到 SQL Server 或 Azure SQL Database 時，可以設定指定的預存程序並搭配其他參數來叫用。
### 範例
1. 定義輸出資料表的 JSON 如下 (以 Azure SQL Database 資料表為例)：

    	{
    		"name": "MyAzureSQLTable",
    		"properties":
    		{
    			"location":
    			{
    				"type": "AzureSqlTableLocation",
    				"tableName": "Marketing",
    				"linkedServiceName": "AzureSqlLinkedService"
    			},
    			"availability":
    			{
    				"frequency": "Hour",
    				"interval": 1
    			}
    		}
    	}

2. 在複製活動 JSON 中定義 **SqlSink** 區段如下。若要在插入資料時呼叫預存程序，則需要 **SqlWriterStoredProcedureName** 和 **SqlWriterTableType** 屬性。

		"sink":
	    {
			"type": "SqlSink",
	        "SqlWriterTableType": "MarketingType",
		    "SqlWriterStoredProcedureName": "spOverwriteMarketing",	
			"storedProcedureParameters":
					{
                    	"stringData": 
						{
                        	"value": "str1"		
						}
                    }
	    }

3. 在資料庫中，使用與 **SqlWriterStoredProcedureName** 相同的名稱定義預存程序。它會處理來自指定來源的輸入資料，並插入至輸出資料表。請注意，預存程序的參數名稱應該與資料表 JSON 檔案中定義的 **tableName** 相同。

		CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
		AS
		BEGIN
			DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    		INSERT [dbo].[Marketing](ProfileID, State)
    		SELECT * FROM @Marketing
		END


4. 在資料庫中，使用與 **SqlWriterTableType** 相同的名稱定義資料表類型。請注意，資料表類型的結構描述應該與輸入資料所傳回的結構描述相同。

		CREATE TYPE [dbo].[MarketingType] AS TABLE(
    	    [ProfileID] [varchar](256) NOT NULL,
    	    [State] [varchar](256) NOT NULL,
    	)

預存程序功能使用[資料表值參數][table-valued-parameters]。

## 指定文字檔的編碼。
雖然 UTF-8 編碼很常用，但由於歷史因素，Azure Blob 中的文字檔經常採用其他編碼。**encodingName** 屬性可讓您使用字碼頁名稱，對 TextFormat 類型的資料表指定編碼。如需有效編碼名稱的清單，請參閱：Encoding.EncodingName 屬性。例如：windows-1250 或 shift_jis。預設值為 UTF-8。請參閱「[Encoding 類別](https://msdn.microsoft.com/library/system.text.encoding.aspx)」，取得有效的編碼名稱。

## 另請參閱

- [使用複製活動的範例][copy-activity-examples]
- [使用 Azure Data Factory 複製資料][adf-copyactivity]
- [複製活動 - JSON 指令碼參考](https://msdn.microsoft.com/library/dn835035.aspx)
- [影片：Azure Data Factory 複製活動簡介][copy-activity-video]


[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx


[adfgetstarted]: data-factory-get-started.md
[adf-copyactivity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[azure-table-data-type]: https://msdn.microsoft.com/zh-tw/library/azure/dd179338.aspx

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample2.png
 

<!---HONumber=62-->