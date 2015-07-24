<properties 
	pageTitle="Machine Learning 建議 API 文件 | Microsoft Azure" 
	description="Microsoft Azure Marketplace 中提供適用於建議引擎的 Azure Machine Learning 建議 API 文件。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="AharonGumnik" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2015" 
	ms.author="LuisCa"/>

#Azure Machine Learning 建議 API 文件

本文件說明 Microsoft Azure Machine Learning 建議 API。


[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

##1.一般概觀
本文件是 API 參考。您應該從＜Azure Machine Learning 建議 – 快速入門＞文件開始。

Azure Machine Learning 建議 API 可分成 10 個邏輯群組：

1.	<ins>模型基本操作</ins> – 可讓您對模型執行基本操作 (例如建立、更新及刪除模型) 的 API。
2.	<ins>模型進階操作</ins> – 可讓您深入了解模型相關進階資料的 API。
3.	<ins>模型商務規則</ins> – 可讓您管理模型建議結果之相關商業規則的 API。
4.	<ins>目錄</ins> – 可讓您對模型目錄執行基本操作的 API。目錄包含使用狀況資料項目的相關中繼資料資訊。
5.	<ins>特徵</ins> - 可讓您深入了解目錄中的項目，以及如何使用這項資訊來建立更佳建議的 API。
6.	<ins>使用狀況資料</ins> – 可讓您對模型使用狀況資料執行基本操作的 API。基本格式的使用狀況資料由資料列組成，這些資料列包含成對的 &#60;userId&#62;,&#60;itemId&#62;。
7.	<ins>組建</ins> – 能夠讓您觸發模型組建並執行與此組建相關之基本操作的 API。您可以在獲得有價值的使用狀況資料之後，觸發模型組建。
8.	<ins>建議</ins> – 模型組建結束之後，可讓您取用建議的 API。
9.	<ins>使用者資料</ins> - 可讓您擷取使用者使用資料之相關資訊的 API。
10.	<ins>通知</ins> – 可讓您接收與 API 操作相關之問題通知的 API。(例如，您透過資料擷取回報使用量資料，而大部分的事件處理都失敗了。這將會引發錯誤通知。)

##2.進階主題

###2.1.建議品質

建立建議模型通常足以允許系統提供建議。不過，建議品質取決於處理的使用量以及目錄的涵蓋範圍。例如，如果您有許多冷項目 (沒有高使用量的項目)，系統很難提供建議給這類項目，或使用這類項目做為建議項目。為了克服冷項目的問題，系統允許使用項目的中繼資料增強建議。中繼資料可稱為功能。典型的功能是書籍的作者或電影的演員。功能是透過目錄，以索引鍵/值字串的格式提供。若需目錄檔案的完整格式，請參閱[匯入目錄區段](#81-import-catalog-data)。下節說明如何使用功能來加強建議模型。

###2.2.排名組建

功能可增強建議模型，但若要這樣做需要使用有意義的功能。為了這個目的，引入新的組建 - 排名組建。此組建會對功能的效益進行排名。有意義的功能為排名分數 2 以上的功能。了解哪些是有意義的功能之後，會利用有意義功能的清單 (或子清單) 觸發建議組建。這樣就可以使用這些功能同時增強暖項目和冷項目。若要將它們用於暖項目，應設定 `UseFeatureInModel` 組建參數。若要將它們用於冷項目，應啟用 `AllowColdItemPlacement` 組建參數。注意：不可能啟用 `AllowColdItemPlacement` 而不啟用 `UseFeatureInModel`。

###2.3.建議推論

建議推論是功能使用方式的另一個層面。的確，Azure Machine Learning 建議引擎可以使用功能來提供建議說明 (也稱為推理)，讓建議取用者對建議項目產生更多信心。若要啟用推論，應在要求建議組建之前設定 `AllowFeatureCorrelation` 和 `ReasoningFeatureList` 參數。

##3.限制

- 每個訂用帳戶的模型數上限是 10。
- 一個目錄可以保留的項目數上限是 100,000。
- 保留的使用點數上限是 ~5,000,000。如果將上傳或回報新的點，就會將最舊的點刪除。
- POST 中可以傳送的資料大小上限 (例如：匯入目錄資料、匯入使用資料) 是 200 MB。
- 非作用中建議模型組建的每秒交易數目是 ~ 2TPS。作用中建議模型組建可以保留高達 20TPS。

##4.API – 一般資訊

###4.1.驗證
請遵循與驗證相關的 Microsoft Azure Marketplace 指導方針。Marketplace 可支援基本或 OAuth 驗證方法。

###4.2.服務 URI
Azure Machine Learning 建議 API 的服務根 URI 在[這裡。](https://api.datamarket.azure.com/amla/recommendations/v3/)

完整服務 URI 是使用 OData 規格的元素來表示。

###4.3.API 版本
每個 API 呼叫最後會有名為 apiVersion 的查詢參數 (應設為 1.0)。

###4.4.識別碼會區分大小寫
任何 API 所傳回的識別碼都會區分大小寫，且在後續 API 呼叫中做為參數傳遞時，也應該如此使用。例如，模型識別碼和目錄識別碼都會區分大小寫。

##5.模型基本操作

###5.1.建立模型
建立「建立模型」要求。

| HTTP 方法 | URI |
|:--------|:--------|
|POST |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	modelName |	只允許使用字母 (A-Z、a-z)、數字 (0-9)、連字號 (-) 及底線 (_)。<br>最大長度：20 |
|	apiVersion | 1.0 |
||| | 要求主體 | 無 |


**回應**：

HTTP 狀態碼：200

- `feed/entry/content/properties/id` – 包含模型識別碼。**注意**：模型識別碼會區分大小寫。

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
	  </entry>
	</feed>

###5.2.取得模型
建立一個「取得模型」要求。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>範例：<br>`<rootURI>/GetModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	id |	模型的唯一識別碼 (區分大小寫) |
|	apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

在下列項目之下，您可以找到模型資料：

- `feed/entry/content/properties/Id` – 模型的唯一識別碼。
- `feed/entry/content/properties/Name` – 模型名稱。
- `feed/entry/content/properties/Date` – 模型建立日期。
- `feed/entry/content/properties/Status` – 模型狀態。下列其中之一：
    - Created - 模型已建立且不包含目錄和使用方式。
	- ReadyForBuild – 模型已建立且包含目錄和使用方式。
- `feed/entry/content/properties/HasActiveBuild` – 表示模型是否已成功建置。
- `feed/entry/content/properties/BuildId` – 模型的作用中組建識別碼。
- `feed/entry/content/properties/Mpr` – 模型的平均值百分位數排名 (MPR - 如需詳細資訊，請參閱 ModelInsight)。
- `feed/entry/content/properties/UserName` – 模型的內部使用者名稱。

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Get A List Of All Models</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T14:35:51Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
        <d:Name m:type="Edm.String">vah-11111</d:Name>
        <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
        <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
		<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
        <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
        <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
        <d:Description m:type="Edm.String">short description</d:Description>
        <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
      </m:properties>
    </content>
	  </entry>
	</feed>

###5.3.取得所有模型
擷取目前使用者的所有模型。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetAllModels?apiVersion=%271.0%27`<br>範例：<br>`<rootURI>/GetAllModels?apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

- `feed/entry/content/properties/Id` – 模型的唯一識別碼。
- `feed/entry/content/properties/Name` – 模型名稱。
- `feed/entry/content/properties/Date` – 模型建立日期。
- `feed/entry/content/properties/Status` – 模型狀態。下列其中之一：
  - Created - 模型已建立且不包含目錄和使用方式。
  - ReadyForBuild – 模型已建立且包含目錄和使用方式。
- `feed/entry/content/properties/HasActiveBuild` – 表示模型是否已成功建置。
- `feed/entry/content/properties/BuildId` – 模型的作用中組建識別碼。
- `feed/entry/content/properties/Mpr` – 模型 MPR (如需詳細資訊，請參閱 ModelInsight)。
- `feed/entry/content/properties/UserName` – 模型的內部使用者名稱。
- `feed/entry/content/properties/UsageFileNames` – 逗號分隔的模型使用方式檔案清單。
- `feed/entry/content/properties/CatalogId` – 模型目錄識別碼。
- `feed/entry/content/properties/Description` – 模型說明。
- `feed/entry/content/properties/CatalogFileName` – 模型目錄檔案名稱。

OData XML


    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get A List Of All Models</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-28T14:35:51Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
					<d:Name m:type="Edm.String">vah-11111</d:Name>
					<d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
					<d:Status m:type="Edm.String">ReadyForBuild</d:Status>
					<d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
					<d:BuildId m:type="Edm.String">-1</d:BuildId>
					<d:Mpr m:type="Edm.String">0</d:Mpr>
					<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
					<d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
					<d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
					<d:Description m:type="Edm.String">short description</d:Description>
					<d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
				</m:properties>
			</content>
		</entry>
	</feed>

###5.4.更新模型

您可以更新模型說明或作用中組建識別碼。<br> <ins>作用中組建識別碼</ins> – 每個模型的每個組建都有組建識別碼。作用中組建識別碼是每個新模型的第一個成功組建。一旦您有作用中組建識別碼，而且您執行相同模型的其他組建，您必須是需要將它明確設為預設組建識別碼。當您取用建議時，如果您未指定想要使用的組建識別碼，將會自動使用預設組建識別碼。<br> 此機制可讓您在生產環境中有建議模型時建置新模型，並先加以測試，再將其提升至生產環境。


| HTTP 方法 | URI |
|:--------|:--------|
|PUT |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br>範例：<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	id | 模型的唯一識別碼 (區分大小寫) |
|	apiVersion | 1.0 |
||||要求本文 | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`<Description>New Description</Description>`<br>`<ActiveBuildId>-1</ActiveBuildId>`<br>` </ModelUpdateParams>`<br><br>請注意，XML 標記說明和 ActiveBuildId 是選擇性的。如果您不想設定說明或 ActiveBuildId，請移除整個標記。|

**回應**：

HTTP 狀態碼：200

###5.5.刪除模型
根據識別碼刪除現有的模型。

| HTTP 方法 | URI |
|:--------|:--------|
|刪除 |`<rootURI>/DeleteModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>範例：<br>`<rootURI>/DeleteModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	id |	模型的唯一識別碼 (區分大小寫) |
|	apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Delete Model by Id</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T10:39:33Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">DeleteModelByIdEntity</title>
    <updated>2014-10-28T10:39:33Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
		<d:string m:type="Edm.String"></d:string>
      </m:properties>
    </content>
	  </entry>
	</feed>

##6.模型進階操作

###6.1.模型資料深入了解
傳回建置此模型時所用之使用狀況資料的相關統計資料。

僅適用於建議組建。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>範例：<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	modelId |	模型的唯一識別碼 |
|	apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

傳回資料做為屬性的集合。

- `feed/entry/id/content/properties/key` - 保留屬性名稱。
- `feed/entry/id/content/properties/value` - 保留屬性值。

下表描述每個索引鍵表示的值。

|金鑰|說明|
|:-----|:----|
| AvgItemLength | 每個項目不同使用者的平均數目。 |
| AvgUserLength | 每個使用者不同項目的平均數目。 |
| DensificationNumberOfItems | 剪除不能模型化的項目之後的項目數目。 |
| DensificationNumberOfUsers | 剪除不能模型化的使用者和項目之後的始用點數目。 |
| DensificationNumberOfRecords | 剪除不能模型化的使用者和項目之後的始用點數目。 |
| MaxItemLength | 最受歡迎項目的不同使用者數目。 |
| MaxUserLength | 使用者之不同項目的最大數目。 |
| MinItemLength | 項目之不同使用者的最大數目。 |
| MinUserLength | 使用者之不同項目的最小數目。 |
| RawNumberOfItems | 剪除不能模型化的項目之前的項目數目。 |
| RawNumberOfUsers | 任何剪除動作之前的使用點數目。 |
| RawNumberOfRecords | 任何剪除動作之前的使用點數目。 |
| SamplingNumberOfItems | N/A |
| SamplingNumberOfRecords | N/A |
| SamplingNumberOfUsers | N/A |

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get data insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgItemLength</d:Key>
        <d:Value m:type="Edm.String">18.936</d:Value>
      </m:properties>
    </content>
	</entry>
	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgUserLength</d:Key>
        <d:Value m:type="Edm.String">51.879</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfItems</d:Key>
        <d:Value m:type="Edm.String">2,000</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfRecords</d:Key>
        <d:Value m:type="Edm.String">37,872</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
	<content type="application/xml">
		<m:properties>
			<d:Key m:type="Edm.String">DensificationNumberOfUsers</d:Key>
			<d:Value m:type="Edm.String">730</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxItemLength</d:Key>
				<d:Value m:type="Edm.String">4,704</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    	<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxUserLength</d:Key>
				<d:Value m:type="Edm.String">190</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinItemLength</d:Key>
				<d:Value m:type="Edm.String">8</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinUserLength</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    </feed>

###6.2.模型深入了解
傳回作用中組建或 (如果有) 特定組建上的模型深入了解。

僅適用於建議組建。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |具有作用中組建識別碼：<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/GetModelInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>具有特定組建識別碼：<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	modelId |	模型的唯一識別碼 |
|	buildId |	選擇性 – 識別成功組建的編號。 |
|	apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

傳回資料做為屬性的集合。

- `feed/entry/id/content/properties/key`
- `feed/entry/id/content/properties/value`


下表描述每個索引鍵表示的值。

| 金鑰 | 說明 |
|:---- |:----|
| CatalogCoverage | 目錄的哪個部分可以利用使用模式進行模型化。其餘的項目都需要以內容為基礎的功能。 |
| Mpr | 模型的平均值百分位數排名。越低越好。 |
| NumberOfDimensions | 矩陣分解演算法所使用的維度數目。 |


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get model insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">CatalogCoverage</d:Key>
				<d:Value m:type="Edm.String">1.000</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">Mpr</d:Key>
				<d:Value m:type="Edm.String">0.367</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">NumberOfDimensions</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###6.3.取得模型範例
取得建議模型的範例。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelSample?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>範例：<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>具有特定組建識別碼：<br>`<rootURI>/GetModelSample?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`<br>範例：<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&buildId=%271500068%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	modelId |	模型的唯一識別碼 |
|	apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

OData XML

以原始文字格式傳回的回應：

<pre>
層級 1
---------------
655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel
	655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel 評等：0.5215
	3f471802-f84f-44a0-99c8-6d2e7418eec1, Black Hawk Down: A Story of Modern War 評等：0.5151
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon 評等：0.5148
	6afc18e4-8c2a-43d1-9021-57543d6b11d8, Imajica 評等：0.5146
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List 評等：0.514
56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai
	56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai 評等：0.5218
	53156702-cc0c-443d-b718-6fb74b2491d3, Son of \ 評等：0.5212
	fb8cf7a6-8719-46ee-97d4-92f931d77a3a, Smoke and Mirrors: Short Fictions and Illusions 評等：0.5188
	8f5fe006-79e4-4679-816b-950989d1db4b, A Place I've Never Been (Contemporary American Fiction) 評等：0.5156
	d8db4583-cc0f-49ce-bc95-b7fa3491623f, Happiness: A Novel 評等：0.5156
50471eec-9aeb-4900-84d7-21567ab18546, If the Buddha Dated: A Handbook for Finding Love on a Spiritual Path
	cfe922a1-7ca0-4f8d-ad9d-b7cc87bfe0ef, Divine Secrets of the Ya-Ya Sisterhood: A Novel 評等：0.5266
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel 評等：0.5252
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven 評等：0.5244
	e2cbf7ad-0636-4117-8b30-298da6df7077, Animal Dreams 評等：0.5227
	6c818fd3-5a09-417d-9ab4-7ffe090f0fef, Confessions of an Ugly Stepsister: A Novel 評等：0.5222
5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club)
	5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club) 評等：0.537
	5dcbac37-2946-4f2a-a0b3-bbe710f9409a, Up Island: A Novel 評等：0.5277
	bc5b69db-733b-4346-adde-3927544258f7, Downtown 評等：0.5275
	31fe5c63-3e5a-48d0-802b-d3b0f989a634, Have a Nice Day: A Tale of Blood and Sweatsocks 評等：0.5252
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea 評等：0.5238
68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived
	68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived 評等：0.5379
	6724862e-e4e7-4022-9614-1468d8b902ff, Little House on the Prairie 評等：0.5345
	cdedb837-1620-496d-94c4-6ccfed888320, Little House in the Big Woods 評等：0.5325
	382164ba-406b-4187-b726-d7a54b9d790d, The Tao of Pooh 評等：0.5309
	6a068d6a-bb74-4ba3-b3f2-a956c4f9d1b5, On the Banks of Plum Creek 評等：0.5285
37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships
	37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars, Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships 評等：0.5397
	f2be16d4-5faf-4d32-ab83-7ba74d29261e, Politically Correct Bedtime Stories: Modern Tales for Our Life and Times 評等：0.5207
	ef732c5c-334b-4d6b-ab82-7255eb7286d0, Honor Among Thieves 評等：0.5195
	0b209b8c-7cdd-47fd-b940-05c7ff7c60fc, The Giving Tree 評等：0.5194
	883b360f-8b42-407f-b977-2f44ad840877, Scary Stories to Tell in the Dark: Collected from American Folklore (Scary Stories) 評等：0.5184
ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball
	d008dae9-c73a-40a1-9a9b-96d5cf546f36, The Gulag Archipelago 1918-1956: An Experiment in Literary Investigation I-II 評等：0.5416
	ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball 評等：0.5403
	49dec30e-0adb-411a-b186-48eaabf6f8bc, Fatherland 評等：0.5394
	cc7964fd-d30f-478e-a425-93ddbdf094ed, Magic the Gathering: Arena Vol. 1 評等：0.5379
	8a1e9f36-97af-4614-bed9-24e3940a05f3, More Sniglets: Any Word That Doesn't Appear in the Dictionary but Should 評等：0.5377
12a6d988-be21-4a09-8143-9d5f4261ba16, A Dream of Eagles
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon 評等：0.5417
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List 評等：0.5416
	1f1a34c4-9781-49f5-a3cc-acec3ae3c71d, The Family 評等：0.5371
	56daeffe-7d48-43cd-8ef8-7dffd0c103d3, Kilo Class 評等：0.5366
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender 評等：0.5366
df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish
	56d33036-dfda-46b9-8e2a-76cb03921bb0, The X-Files: Ground Zero 評等：0.5417
	0780cde8-6529-4e1d-b6c6-082c1b80e596, Twelve Red Herrings 評等：0.5416
	df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish 評等：0.5408
	400fe331-2c35-490c-adbc-b28b4b73d56c, Shall We Tell the President? 評等：0.5383
	f86ad7d0-5c03-42b3-aebf-13d44aec8b30, Shades of Grace 評等：0.5358
de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology
	de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology 評等：0.5422
	b303538f-e2c6-4a2c-b425-8d21e684fc3e, My Uncle Oswald 評等：0.5385
	34b84627-48af-4a4c-96c4-b26fb3863f56, Midnight In the Garden of Good and Evil 評等：0.5379
	306cbaa7-b1a8-4142-9d55-e11b5018a7a8, The Street Lawyer 評等：0.5376
	e53b4baa-8c09-45c4-95c0-b6a26b98770b, Miss Smillas Feeling for Snow 評等：0.5367

層級 2
---------------
352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony)
	352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony) 評等：0.5425
	74c49398-bc10-4af5-a658-a996a1201254, Children of the Storm (Peters Elizabeth) 評等：0.5387
	9ba80080-196e-43fd-8025-391d963f77e7, The Floating Girl 評等：0.5372
	e68f81d5-7745-4cc7-b943-fedb8fcc2ced, Killer Smile (Scottoline Lisa) 評等：0.5353
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender 評等：0.5332
c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea 評等：0.5433
	c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days 評等：0.543
	a00ae6ad-4a7f-4211-9836-75ce8834eb11, Sniglets (Snig'lit: Any Word That Doesn't Appear in the Dictionary But Should) 評等：0.5327
	6f6e192e-0d64-49ca-9b63-f09413ea1ee6, Politically Correct Holiday Stories: For an Enlightened Yuletide Season 評等：0.5307
	798051a8-147d-4d46-b0dc-e836325029e6, AGE OF INNOCENCE (MOVIE TIE-IN) 評等：0.5301
73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics)
	cba8163f-6536-436b-8130-47b4a43c827f, Trust No One (The Official Guide to the X-Files Vol. 2) 評等：0.5434
	5708e4cb-2492-49c0-94a8-cc413eec5d89, Small Gods (Discworld Novels (Paperback)) 評等：0.5406
	73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics) 評等：0.5403
	d885b0bd-ae4b-452d-bdf2-faa90197dbc9, The Color of Magic 評等：0.539
	b133a9c4-4784-4db3-b100-d0d6dffb94d2, The Truth Is Out There (The Official Guide to the X-Files Vol. 1) 評等：0.5367
271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings
	271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings 評等：0.5445
	2de1c354-90ff-47c5-a0db-1bad7d88ef94, The Salaryman's Wife (Children of Violence Series) 評等：0.5329
	d279416e-19c0-43f8-9ec9-a585947879ca, Zen Attitude 評等：0.5316
	c8f854d7-3de3-4b23-8217-f4f851670fd4, Revenge of the Cootie Girls: A Robin Hudson Mystery (Robin Hudson Mysteries (Paperback)) 評等：0.5305
	8ef4751c-7074-409e-a3ac-d49b222fc864, Where the Wild Things Are 評等：0.5289
9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God
	9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God 評等：0.5446
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees 評等：0.5389
	65ecbdd1-131c-40c3-a3d6-d86ca281377a, The God of Small Things 評等：0.5387
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries 評等：0.5355
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven 評等：0.5344
5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback))
	5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback)) 評等：0.5446
	57169b2b-9a8a-486b-9aac-1ed98ce57168, Final Appeal 評等：0.5332
	efcb1bc4-7278-4a8f-b491-befde02070d6, Moment of Truth 評等：0.5329
	1efa91a2-993b-4c43-9f5c-3454fc12612d, Burn Factor 評等：0.5309
	24c59962-458a-4ec8-b95d-d694e861919c, At Home in Mitford (The Mitford Years) 評等：0.5303
4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl
	4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl 評等：0.5449
	cd5f2c03-20cb-43be-a1fb-3b4233e63222, Pigs in Heaven 評等：0.5329
	19985fdb-d07a-4a25-ae4a-97b9cb61e5d1, Love in the Time of Cholera (Penguin Great Books of the 20th Century) 評等：0.5267
	15689d09-c711-4844-84d8-130a90237b26, Bel Canto 評等：0.5245
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel 評等：0.5235
98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories
	f874b5a3-5d40-4436-94ff-0fa1c090ddf5, The Sun Also Rises (A Scribner classic) 評等：0.5451
	98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories 評等：0.5442
	0ce0014a-9a48-4013-a08a-7f2c11877930, H.M.S.Unseen 評等：0.5421
	15316ca6-1e38-425f-893d-691944a47000, More Scary Stories To Tell In The Dark 評等：0.5409
	329d5682-3dc3-4206-8aa2-eef4b1032258, Letters from the Earth 評等：0.54
5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover))
	5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover)) 評等：0.5462
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel 評等：0.5372
	604eb3bd-6026-4f51-bffd-9fb54f180400, Family Pictures: A Novel 評等：0.5341
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) 評等：0.5334
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees 評等：0.5319
d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven
	d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven 評等：0.5491
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel 評等：0.5401
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries 評等：0.5393
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) 評等：0.5382
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven 評等：0.5367

</pre>

##7.模型商務規則
有 4 種規則類型：<strong>BlockList</strong> - BlockList 可讓您提供您不想在建議結果中傳回的項目清單。<strong>Upsale</strong> - Upsale 可讓您強制在建議結果中傳回項目。<strong>WhiteList</strong> - WhiteList 可讓您提供只能做為建議結果傳回的項目清單 (BlockList 的相反類型)。<strong>PerSeedBlockList</strong> - PerSeedBlockList 可讓您提供無法做為建議結果傳回的項目清單給每個項目。


###7.1.取得模型規則

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>範例：<br>`<rootURI>/GetModelRules?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	modelId |	模型的唯一識別碼 |
|	apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

- `feed/entry/content/properties/Id` – 此規則的唯一識別碼。
- `feed/entry/content/properties/Type` – 規則的類型。
- `feed/entry/content/properties/Parameter` – 規則參數。

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get a list of rules for a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T12:58:57Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000043</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1000"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000044</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1001"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7.2.新增規則

| HTTP 方法 | URI |
|:--------|:--------|
|POST |`<rootURI>/AddRule?apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	apiVersion | 1.0 |
||||要求本文 | <ins>若要新增 BlockList 規則：</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>BlockList</Type><Value>{"ItemsToExclude":["2406E770-769C-4189-89DE-1C9283F93A96","3906E110-769C-4189-89DE-1C9283F98888"]}</Value></ApiFilter>`<br><br><ins>若要新增 Upsale 規則：</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>Upsale</Type><Value>{"ItemsToUpsale":["2406E770-769C-4189-89DE-1C9283F93A96"]}</Value></ApiFilter>`<br><br><ins>若要新增 WhiteList 規則：</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>WhiteList</Type><Value>{"ItemsToInclude":["2406E770-769C-4189-89DE-1C9283F93A96","1116E770-769C-4189-89DE-1C9283F88888"]}</Value></ApiFilter>`<br><br><ins>若要新增 PerSeedBlockList 規則：</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>PerSeedBlockList</Type><Value>{"SeedItems":["9949"],"ItemsToExclude":["9862","8158","8244"]}</Value></ApiFilter>`|

**回應**：

HTTP 狀態碼：200

API 會傳回新建立的規則及其詳細資料。規則屬性可從下列路徑擷取：

- `feed/entry/content/properties/Id` – 此規則的唯一識別碼。
- `feed/entry/content/properties/Type` – 規則的類型：BlockList 或 Upsale。
- `feed/entry/content/properties/Parameter` – 規則參數。

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Add A Rule</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T11:13:28Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AddARuleEntity</title>
		<updated>2014-11-05T11:13:28Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000041</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1002"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7.3.刪除規則

| HTTP 方法 | URI |
|:--------|:--------|
|刪除 |`<rootURI>/DeleteRule?modelId=%27<model_id>%27&filterId=%27<filter_Id>%27&apiVersion=%271.0%27`<br><br>範例：<br>`DeleteRule?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&filterId=%271000011%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	modelId |	模型的唯一識別碼 |
|	filterId |	篩選器的唯一識別碼 |
|	apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

###7.4.刪除所有規則

| HTTP 方法 | URI |
|:--------|:--------|
|刪除 |`<rootURI>/DeleteAllRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>範例：<br>`DeleteAllRules?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	modelId |	模型的唯一識別碼 |
|	apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

##8.目錄

###8.1.匯入目錄資料

如果您將數個目錄檔案上傳至具有多個呼叫的相同模型，我們只會插入新的目錄項目。現有的項目會保留原始值。您無法使用這個方法更新目錄資料。

目錄資料應該遵循下列格式：

- 沒有功能 - `<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- 具有功能 - `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

注意：檔案大小上限為 200 MB。

**格式詳細資料**

| 名稱 | 強制 | 類型 | 說明 |
|:---|:---|:---|:---|
| 項目識別碼 |是 | [A-z]、[a-z]、[0-9]、[_] &#40;底線&#41;、[-] &#40;虛線&#41;<br> 最大長度：50 | 項目的唯一識別碼。 |
| 項目名稱 | 是 | 任何英數字元<br> 最大長度：255 | 項目名稱。 | 
| 項目類別 | 是 | 任何英數字元 <br> 最大長度：255 | 此項目所屬類別 (例如烹飪書籍、劇本...) 可以是空的。 |
| 說明 | 否，除非顯示功能 (但可能是空的) | 任何英數字元 <br> 最大長度：4000 | 此項目的說明。 |
| 功能清單 | 否 | 任何英數字元 <br> 最大長度：4000 | 以逗號分隔的功能名稱清單 = 功能值，可用來增強模型建議；請參閱[進階主題](#2-advanced-topics)一節。 |


| HTTP 方法 | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	modelId |	模型的唯一識別碼 |
| 檔名 | 目錄的文字識別碼。<br>只允許使用字母 (A-Z、a-z)、數字 (0-9)、連字號 (-) 及底線 (_)。<br>最大長度：50 |
|	apiVersion | 1.0 |
||| | 要求本文 |範例 (包含功能)：<br/>2406e770-769c-4189-89de-1c9283f93a96,Clara Callan,Book,the book description,author=Richard Wright,publisher=Harper Flamingo Canada,year=2001<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,The Forgetting Room: A Fiction (Byzantium Book),Book,,author=Nick Bantock,publisher=Harpercollins,year=1997<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework,Book,,author=Timothy Findley, publisher=HarperFlamingo Canada, year=2001<br>552a1940-21e4-4399-82bb-594b46d7ed54,Restraint of Beasts,Book,the book description,author=Magnus Mills, publisher=Arcade Publishing, year=1998</pre> |


**回應**：

HTTP 狀態碼：200

API 會傳回匯入的報表。- `feed\entry\content\properties\LineCount` – 已接受的行數。- `feed\entry\content\properties\ErrorCount` – 因錯誤而未插入的行數。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Import catalog file</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-05T06:58:04Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
	<entry>
   	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ImportCatalogFileEntity2</title>
		<updated>2014-10-05T06:58:04Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:LineCount m:type="Edm.String">4</d:LineCount>
				<d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
			</m:properties>
		</content>
	</entry>
	</feed>

###8.2.取得目錄
擷取所有目錄項目。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalog?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>範例：<br>`GetCatalog?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	modelId |	模型的唯一識別碼 |
|	apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

回應會包含每個目錄項目的一個項目。每個項目都有下列資料：

- `feed/entry/content/properties/ExternalId` – 目錄項目外部識別碼，由客戶提供。
- `feed/entry/content/properties/InternalId` – 目錄項目內部識別碼，由 Azure Machine Learning 建議產生。
- `feed/entry/content/properties/Name` – 目錄項目名稱。
- `feed/entry/content/properties/Category` – 目錄項目類別。
- `feed/entry/content/properties/Description` – 目錄項目說明。
- `feed/entry/content/properties/Metadata` – 目錄項目中繼資料。


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get All Catalog Items</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'" />
		<entry>
        	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">552A1940-21E4-4399-82BB-594B46D7ED54</d:ExternalId>
				<d:InternalId m:type="Edm.String">060db26a-e6a6-464e-bb52-436d2da82a50</d:InternalId>
				<d:Name m:type="Edm.String">Restraint of Beasts</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:ExternalId>
				<d:InternalId m:type="Edm.String">209d7bfe-2eb9-4455-92a3-7c867a41a74a</d:InternalId>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">3BB5CB44-D143-4BDD-A55C-443964BF4B23</d:ExternalId>
				<d:InternalId m:type="Edm.String">913ff79b-059b-4d4d-8042-6fa4cc1391dd</d:InternalId>
				<d:Name m:type="Edm.String">Spadework</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">21BF8088-B6C0-4509-870C-E1C7AC78304A</d:ExternalId>
				<d:InternalId m:type="Edm.String">ea65e4fa-768c-40b4-92c3-69d3e8178691</d:InternalId>
				<d:Name m:type="Edm.String">The Forgetting Room: A Fiction (Byzantium Book)</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	</feed>

###8.3.依語彙基元取得目錄項目

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalogItemsByToken?modelId=%27<modelId>%27&token=%27<token>%27&apiVersion=%271.0%27`<br><br>範例：<br>`GetCatalogItemsByToken?modelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&token=%27Cla%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	modelId |	模型的唯一識別碼 |
|	token |	目錄項目名稱的 token。應該至少包含 3 個字元。 |
|	apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

回應會包含每個目錄項目的一個項目。每個項目都有下列資料：

- `feed/entry/content/properties/ExternalId` – 目錄項目外部識別碼，由客戶提供。
- `feed/entry/content/properties/InternalId` – 目錄項目內部識別碼，由 Azure Machine Learning 建議產生。
- `feed/entry/content/properties/Name` – 目錄項目名稱。
- `feed/entry/content/properties/Category` – 目錄項目類別。
- `feed/entry/content/properties/Description` – 目錄項目說明。
- `feed/entry/content/properties/Metadata` – 目錄項目中繼資料。

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get Catalog items that contain a token</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:48:19Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">CatalogItemsThatContainATokenEntity</title>
			<updated>2014-10-29T11:48:19Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    		<content type="application/xml">
      			<m:properties>
					<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
        			<d:Name m:type="Edm.String">Clara Callan</d:Name>
        			<d:Rating m:type="Edm.Double">0</d:Rating>
        			<d:Reasoning m:type="Edm.String"></d:Reasoning>
        			<d:Metadata m:type="Edm.String"></d:Metadata>
        			<d:FormattedRating m:type="Edm.Double" m:null="true"></d:FormattedRating>
      			</m:properties>
			</content>
		</entry>
	</feed>

##9.使用狀況資料
###9.1.匯入使用資料
####9.1.1.上傳檔案
本節試範如何使用檔案上傳使用資料。您可以利用使用資料呼叫此 API 數次。將會針對所有呼叫儲存所有使用狀況資料。

| HTTP 方法 | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	modelId |	模型的唯一識別碼 |
| 檔名 | 目錄的文字識別碼。<br>只允許使用字母 (A-Z、a-z)、數字 (0-9)、連字號 (-) 及底線 (_)。<br>最大長度：50 |
|	apiVersion | 1.0 |
||||要求本文 |使用狀況資料。格式：<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>名稱</th><th>強制</th><th>類型</th><th>描述</th></tr><tr><td>使用者識別碼</td><td>是</td><td>[A-z]、[a-z]、[0-9]、[_] &#40;底線&#41;、[-] &#40;虛線&#41;<br> 最大長度：255 </td><td>使用者的唯一識別碼。</td></tr><tr><td>項目識別碼</td><td>是</td><td>[A-z]、[a-z]、[0-9]、[&#95;] &#40;底線&#41;、[-] &#40;虛線&#41;<br> 最大長度：50</td><td>項目的唯一識別碼。</td></tr><tr><td>時間</td><td>否</td><td>日期格式：YYYY/MM/DDTHH:MM:SS (例如 2013/06/20T10:00:00)</td><td>資料的時間。</td></tr><tr><td>事件</td><td>否；如果提供，也必須註明日期</td><td>下列其中之一：<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>檔案大小上限：200 MB<br><br>範例：<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**回應**：

HTTP 狀態碼：200

- `Feed\entry\content\properties\LineCount` – 已接受的行數。
- `Feed\entry\content\properties\ErrorCount` – 因錯誤而未插入的行數。
- `Feed\entry\content\properties\FileId` – 檔案識別碼。

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####9.1.2.使用資料擷取
本節示範如何將事件即時傳送到 Azure Machine Learning 建議 (通常是從您的網站)。

| HTTP 方法 | URI |
|:--------|:--------|
|POST |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	apiVersion | 1.0 |
|Request body| 您想要傳送的每個事件的事件資料項目。您應該為相同的使用者或瀏覽器工作階段在 SessionId 欄位中傳送相同的識別碼。(請參閱下面的事件本文範例。)|


- 事件 'Click' 的範例：

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
		<SessionId>11112222</SessionId>
		<EventData>
		<EventData>
		<Name>Click</Name>
		<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
		</EventData>
		</EventData>
		</Event>

- 事件 'RecommendationClick' 的範例：

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>RecommendationClick</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- 事件 'AddShopCart' 的範例：

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>AddShopCart</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- 事件 'RemoveShopCart' 的範例：

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>RemoveShopCart</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- 事件 'Purchase' 的範例：<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"> <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId> <SessionId>11112222</SessionId> <EventData> <EventData> <Name>Purchase</Name> <PurchaseItems> <PurchaseItems> <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId> <Count>3</Count> </PurchaseItems> </PurchaseItems> </EventData> </EventData> </Event>

- 傳送 2 個事件 ('Click' 和 'AddShopCart') 的範例：

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>Click</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
      	<ItemName>itemName</ItemName>
      	<ItemDescription>item description</ItemDescription>
      	<ItemCategory>category</ItemCategory>
    	</EventData>
    	<EventData>
      	<Name>AddShopCart</Name>
      	<ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
    	</EventData>
  		</EventData>
		</Event>

**回應**：HTTP 狀態碼：200

###9.2.列出模型使用方式檔案
擷取所有模型使用方式檔案的中繼資料。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/ListModelUsageFiles?forModelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/ListModelUsageFiles?forModelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	forModelId |	模型的唯一識別碼 |
|	apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

回應會包含每個使用方式檔案的一個項目。每個項目都有下列資料：

- `feed\entry\content\properties\Id` – 使用方式檔案識別碼。
- `feed\entry\content\properties\Length` – 使用方式檔案長度，以 MB 為單位。
- `feed\entry\content\properties\DateModified` – 使用方式檔案建立日期。
- `feed\entry\content\properties\UseInModel` – 使用方式檔案是否在模型中使用。

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of model's usage files info</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
			<updated>2014-10-30T09:40:25Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">4c067b42-e975-4cb2-8c98-a6ab80ed6d63</d:Id>
					<d:Length m:type="Edm.Double">0</d:Length>
					<d:DateModified m:type="Edm.String">10/30/2014 9:19:57 AM</d:DateModified>
					<d:UseInModel m:type="Edm.String">true</d:UseInModel>
				</m:properties>
			</content>
		</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">3126d816-4e80-4248-8339-1ebbdb9d544d</d:Id>
				<d:Length m:type="Edm.Double">0.001</d:Length>
				<d:DateModified m:type="Edm.String">10/30/2014 9:21:44 AM</d:DateModified>
				<d:UseInModel m:type="Edm.String">true</d:UseInModel>
          	</m:properties>
		</content>
	</entry>
</feed>

###9.3.取得使用狀況統計資料
取得使用狀況統計資料。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageStatistics?modelId=%27<modelId>%27& startDate=%27<date>%27&endDate=%27<date>%27&eventTypes=%27<types>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/GetUsageStatistics?modelId=%271d20c34f-dca1-4eac-8e5d-f299e4e4ad66%27&startDate=%272014%2F10%2F17T00%3A00%3A00%27&endDate=%272014%2F11%2F16T00%3A00%3A00%27&eventTypes=%271%2C2%2C3%2C4%2C5%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId |	模型的唯一識別碼 |
| startDate |	開始日期。格式：yyyy/MM/ddTHH:mm:ss |
| endDate |	結束日期。格式：yyyy/MM/ddTHH:mm:ss |
| eventTypes |	以逗號分隔的事件類型字串或是 null，可取得所有事件 |
| apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

索引鍵/值項目的集合。每個集合都包含以小時分組之特定事件類型的事件總和。

- `feed\entry[i]\content\properties\Key` – 包含時間 (以小時分組) 和事件類型。
- `feed\entry[i]\content\properties\Value` – 總事件計數。

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get usage statistics</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-18T11:39:16Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;Click</d:Event>
				<d:Value m:type="Edm.String">5</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;RecommendationClick</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
          	</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/1/2014 8:00:00 AM;RemoveShopCart</d:Event>
            	<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
        </content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/5/2014 8:00:00 AM;RemoveShopCart</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###9.4.取得使用方式檔案範例
擷取前 2KB 的使用方式檔案內容。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageFileSample?modelId=%27<modelId>%27& fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/GetUsageFileSample?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fileId=%274c067b42-e975-4cb2-8c98-a6ab80ed6d63%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId |	模型的唯一識別碼 |
| fileId |	模型使用方式檔案的唯一識別碼 |
| apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

回應會以原始文字格式傳回：<pre> 85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 </pre>


###9.5.取得模型使用方式檔案
擷取使用方式檔案的完整內容。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelUsageFile?mid=%27<modelId>%27& fid=%27<fileId>%27&download=%27<download_value>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/GetModelUsageFile?mid=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fid=%273126d816-4e80-4248-8339-1ebbdb9d544d%27&download=%271%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| mid |	模型的唯一識別碼 |
| fid |	模型使用方式檔案的唯一識別碼 |
| 下載 | 1 |
| apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

回應會以原始文字格式傳回：<pre> 85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 244881,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 50547,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 213090,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 260655,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 72214,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 36326,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189336,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260655,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 162100,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 54946,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260965,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 102758,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 112602,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 163925,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 262998,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 144717,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 </pre>

###9.6.刪除使用方式檔案
刪除指定的模型使用方式檔案。

| HTTP 方法 | URI |
|:--------|:--------|
|刪除 |`<rootURI>/DeleteUsageFile?modelId=%27<modelId>%27&fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/DeleteUsageFile?modelId=%270f86d698-d0f4-4406-a684-d13d22c47a73%27&fileId=%27f2e0b09d-be5c-46b2-9ac2-c7f622e5e1a5%27&apiVersion=%271.0%27`|

| 參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId |	模型的唯一識別碼 |
| fileId | 要刪除之檔案的唯一識別碼 |
| apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200


###9.7.刪除所有使用方式檔案
刪除所有模型使用方式檔案。

| HTTP 方法 | URI |
|:--------|:--------|
|刪除 |`<rootURI>/DeleteAllUsageFiles?modelId=%27<modelId>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/DeleteAllUsageFiles?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&apiVersion=%271.0%27`|

| 參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId |	模型的唯一識別碼 |
| apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

##10.特性
本節示範如何擷取功能資訊，例如匯入的功能和其值、其排名，以及此排名的配置時機。匯入功能做為目錄資料的一部分，然後在排名組建完成時建立和其排名的關聯。功能排名可根據使用狀況資料和項目類型而變更。但是對於一致的使用量/項目，排名只能有小幅的起伏。功能的排名為非負數的數字。編號 0 表示未排名功能 (如果您在第一個排名組建完成之前叫用這個 API，就會發生這個情形)。配置排名的日期稱為分數有效時間。

###10.1.取得功能資訊 (適用於上一個排名組建)
擷取最後一次成功排名組建的功能資訊，包括排名。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&apiVersion=%271.0%27`

| 參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId |	模型的唯一識別碼 |
|samplingSize| 每個功能要包含的值數目是根據出現在目錄中的資料。<br/>可能的值為：<br>-1 - 所有範例。<br>0 - 沒有取樣。<br>N - 傳回每個功能名稱的 N 範例。|
| apiVersion | 1.0 |
||| | 要求主體 | 無 |


**回應**：

HTTP 狀態碼：200

回應包含功能資訊項目的清單。每個項目包含：

- `feed/entry/content/m:properties/d:Name` - 功能名稱。
- `feed/entry/content/m:properties/d:RankUpdateDate` - 配置排名至此功能的日期，又稱為分數有效時間功能。歷程記錄日期 ('0001-01-01T00:00:00') 表示未執行排名組建。
- `feed/entry/content/m:properties/d:Rank` - 功能排名 (float)。排名 2.0 以上會被視為良好的功能。
- `feed/entry/content/m:properties/d:SampleValues` - 逗號分隔的值清單高達要求的取樣大小。

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get the features of a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-01-08T13:15:02Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Author</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Publisher</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1"/>
		<contenttype="application/xml">
		<m:properties>
			<d:Name m:type="Edm.String">Year</d:Name>
			<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
			<d:Rank m:type="Edm.String">0</d:Rank>
			<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
		</m:properties>
		</content>
	</entry>
</feed>


###10.2.取得功能資訊 (適用於特定排名組建)

擷取功能資訊，包括特定排名組建的排名。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&rankBuildId=%27<rankBuildId>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&rankBuildId=%271000551%27&apiVersion=%271.0%27`

| 參數名稱 |	有效值 |
|:--------			|:--------			|
| modelId |	模型的唯一識別碼 |
|samplingSize| 每個功能要包含的值數目是根據出現在目錄中的資料。<br/> 可能的值為：<br>-1 - 所有範例。<br>0 - 沒有取樣。<br>N - 傳回每個功能名稱的 N 範例。|
|rankBuildId| 排名組建的唯一識別碼或代表上一個排名組建的 -1|
| apiVersion | 1.0 |
||| | 要求主體 | 無 |


**回應**：

HTTP 狀態碼：200

回應包含功能資訊項目的清單。每個項目包含：

- `feed/entry/content/m:properties/d:Name` - 功能名稱。
- `feed/entry/content/m:properties/d:RankUpdateDate` - 配置排名至此功能的日期，又稱為分數有效時間功能。歷程記錄日期 ('0001-01-01T00:00:00') 表示未執行排名組建。
- `feed/entry/content/m:properties/d:Rank` - 功能排名 (float)。排名 2.0 以上會被視為良好的功能。
- `feed/entry/content/m:properties/d:SampleValues` - 逗號分隔的值清單高達要求的取樣大小。

OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get the features of a model</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:54:22Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Author</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">3.38867426</d:Rank>
					<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Publisher</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.67839336</d:Rank>
					<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Year</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.12352145</d:Rank>
					<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
				</m:properties>
			</content>
		</entry>
	</feed>


##11.建置

  本節說明和組建相關的不同 API。有 3 個類型的組建：建議組建、排名組建和 FBT (通常會一起購買) 組建。

建議組建的用途是產生用於預測的建議模型。這種組建類型的預測可分為兩種類別：* I2I - 又稱為項目對項目的建議 - 這個選項會根據指定的一個項目或項目清單，來預測可能高度感興趣的項目清單。* U2I - 又稱為使用者對項目的建議 - 這個選項會根據指定的使用者識別碼 (及選擇性的項目清單)，來預測指定使用者 (及針對額外選擇的項目) 可能高度感興趣的項目清單。U2I 建議是根據使用者在建置模型前感興趣之項目的歷程記錄。

排名組建為技術組建，可讓您了解功能的效益。通常，若要取得包含功能之建議模型的最佳結果，您應該採取下列步驟：- 觸發排名組建 (除非功能的分數很穩定)，並等待直到取得功能分數。- 藉由呼叫 [取得功能資訊](#101-get-features-info-for-last-rank-build) API 擷取功能的排名。- 以下列參數設定建議組建：- `useFeatureInModel` - 設定為 True。- `ModelingFeatureList` - 設定為分數 2.0 以上的逗號分隔功能清單 (根據您在上一個步驟中擷取到的排名)。- `AllowColdItemPlacement` - 設定為 True。- 您可以選擇性地設定 `EnableFeatureCorrelation` 為 True 和 `ReasoningFeatureList` 設定為您想要用於說明的功能清單 (通常是使用於模型化的相同功能清單或子清單)。- 以設定的參數觸發建議組建。

注意：如果您未設定任何參數 (例如叫用不含參數的建議組建) 或您沒有明確停用功能的使用方式 (例如 `UseFeatureInModel` 設為 False)，系統將會在排名組建存在時，將與功能相關的參數設定為上述的說明值。

同時執行相同模行的排名組建和建議組建時並沒有任何限制。不過，您無法在同一個模型上，以平行方式執行兩個相同類型的組建。

FBT (通常會一起購買) 組建也是另一種建議運算法，有時稱為「保守的」建議者，適用於本質上並不同質的目錄 (同質：書籍、電影、一些食物、流行 ；非同質：電腦與裝置、跨網域、高度差異)。

注意：如果您上傳的使用方式檔案包含選擇性欄位 [事件類型]，則 FBT 模型只會使用 "Purchase" 事件。如果沒有提供事件類型，所有事件都會視為 Purchase。


####11.1 組建參數

每個組建類型都可以透過一組參數 (如下所述) 進行設定。如果您未設定參數，系統會自動根據您觸發組建時出現的資訊分配值給參數。

#####11.1.1.使用狀況冷凝器
附帶少數使用點的使用者或項目可能包含較多雜訊而非資訊。系統會嘗試預測要用於模型之每個使用者/項目的使用點數目下限。這個數字會在項目的 ItemCutoffLowerBound 和 ItemCutoffUpperBound 參數所定義的範圍內，也在使用者的 UserCutOffLowerBound 和 UserCutoffUpperBound 參數所定義的範圍內。將至少一個對應的界限設為零，可以使冷凝器對項目或使用者的影響最小化。

#####11.1.2.排名組建參數

下表描述排名組建的組建參數。

|金鑰|說明|類型|有效值|
|:-----|:----|:----|:---|
|NumberOfModelIterations | 整體運算時間和模型精確度會反映模型執行反覆運算的次數。數字越高，得到的精確度就越高，但需要較久的運算時間。| Integer | 10-50 |
| NumberOfModelDimensions | 維度的數目與「功能」的數目相關，模型會嘗試尋找資料中的數目。增加維度的數目將允許結果進一步微調成較小的叢集。不過，太多維度會讓模型無法尋找項目之間的關聯。 | Integer | 10-40 |
|ItemCutOffLowerBound| 定義冷凝器的項目下限。請參閱上述的使用狀況冷凝器。 | Integer | 2 個以上 (0 代表停用冷凝器) |
|ItemCutOffUpperBound| 定義冷凝器的項目上限。請參閱上述的使用狀況冷凝器。 | Integer | 2 個以上 (0 代表停用冷凝器) |
|UserCutOffLowerBound| 定義冷凝器的使用者下限。請參閱上述的使用狀況冷凝器。 | Integer | 2 個以上 (0 代表停用冷凝器) |
|UserCutOffUpperBound| 定義冷凝器的使用者上限。請參閱上述的使用狀況冷凝器。 | Integer | 2 個以上 (0 代表停用冷凝器) |

#####11.1.3.建議組建參數
下表描述建議組建的組建參數。

|金鑰|說明|類型|有效值|
|:-----|:----|:----|:---|
|NumberOfModelIterations | 整體運算時間和模型精確度會反映模型執行反覆運算的次數。數字越高，得到的精確度就越高，但需要較久的運算時間。| Integer | 10-50 |
| NumberOfModelDimensions | 維度的數目與「功能」的數目相關，模型會嘗試尋找資料中的數目。增加維度的數目將允許結果進一步微調成較小的叢集。不過，太多維度會讓模型無法尋找項目之間的關聯。 | Integer | 10-40 |
|ItemCutOffLowerBound| 定義冷凝器的項目下限。請參閱上述的使用狀況冷凝器。 | Integer | 2 個以上 (0 代表停用冷凝器) |
|ItemCutOffUpperBound| 定義冷凝器的項目上限。請參閱上述的使用狀況冷凝器。 | Integer | 2 個以上 (0 代表停用冷凝器) |
|UserCutOffLowerBound| 定義冷凝器的使用者下限。請參閱上述的使用狀況冷凝器。 | Integer | 2 個以上 (0 代表停用冷凝器) |
|UserCutOffUpperBound| 定義冷凝器的使用者上限。請參閱上述的使用狀況冷凝器。 | Integer | 2 個以上 (0 代表停用冷凝器) |
| 說明 | 建置說明。 | String | 任何文字，最多 512 個字元 |
| EnableModelingInsights | 可讓您計算建議模型上的度量。 | Boolean | True/False |
| UseFeaturesInModel | 指出是否可以使用功能以加強建議模型。 | Boolean | True/False |
| ModelingFeatureList | 使用於建議組建中的功能名稱逗號分隔清單，可加強建議。 | String | 功能名稱，最多 512 個字元 |
| AllowColdItemPlacement | 指出建議是否也應該透過功能相似度推入冷項目。 | Boolean | True/False |
| EnableFeatureCorrelation | 指出功能是否可用於推論。 | Boolean | True/False |
| ReasoningFeatureList | 用於推論句 (例如建議說明) 的功能名稱逗號分隔清單。 | String | 功能名稱，最多 512 個字元 |
| EnableU2I | 允許個人化的建議，又稱為U2I (使用者對項目的建議)。 | Boolean | True/False (預設值為 True) |

#####11.1.4.FBT 組建參數
下表描述建議組建的組建參數。

|金鑰|說明|類型|有效的值 (預設值)|
|:-----|:----|:----|:---|
|FbtSupportThreshold | 模型的保守程度。模型化時要考量項目的共同出現次數。| Integer | 3-50 (6) |
|FbtMaxItemSetSize | 頻繁集合中的項目數界限。| Integer | 2-3 (2) |
|FbtMinimalScore | 頻繁集合應該具有的最低分數，以包含在傳回的結果中。愈高愈好。| 兩倍 | 0 以上 (0) |

###11.2.觸發建議組建

  根據預設，此 API 會觸發建議模型組建。若要觸發排名組建 (以評分功能)，應使用附帶組建類型參數的組建 API 變數。


| HTTP 方法 | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId |	模型的唯一識別碼 |
| userDescription | 目錄的文字識別碼。請注意，如果您使用空格，必須將其編碼改成 %20。請參閱上面的範例。<br>最大長度：50 |
| apiVersion | 1.0 |
||| | 要求本文 |如果保留空白，則組建會以預設的組建參數執行。<br><br>如果您想要設定組建參數，請傳送參數至本文做為 XML，如下列範例所示。(如需參數的說明，請參閱「組建參數」一節)`<NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance><EnableModelingInsights>true</EnableModelingInsights><UseFeaturesInModel>false</UseFeaturesInModel><ModelingFeatureList>feature_name_1,feature_name_2,...</ModelingFeatureList><AllowColdItemPlacement>false</AllowColdItemPlacement><EnableFeatureCorrelation>false</EnableFeatureCorrelation><ReasoningFeatureList>feature_name_a,feature_name_b,...</ReasoningFeatureList></BuildParametersList>` |

**回應**：

HTTP 狀態碼：200

這是非同步的 API。您會得到一個組建識別碼做為回應。若要知道組建何時結束，您應該呼叫「取得模型的組建狀態」API，並在回應中找出這個組建識別碼。請注意，組建可能會花數分鐘到數小時的時間，視資料的大小而定。

您無法取用建議直到組建結束。

有效的組建狀態：

- 建立 - 組建要求已建立。
- 已排入佇列 - 組建要求已傳送並排入佇列。
- 建置中 -建置進行中。
- 成功 – 組建已成功結束。
- 錯誤 – 組建已結束但發生失敗。
- 已取消 – 組建已取消。
- 取消中 - 組建的取消要求已傳送。


請注意，組建識別碼可以在下列路徑下找到：`Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>

###11.3.觸發組建 (建議、排名或 FBT)

| HTTP 方法 | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&buildType=%27<buildType>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&buildType=%27Ranking%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId |	模型的唯一識別碼 |
| userDescription | 目錄的文字識別碼。請注意，如果您使用空格，必須將其編碼改成 %20。請參閱上面的範例。<br>最大長度：50 |
| buildType | 要叫用的組建類型：<br/> - 'Recommendation' 為建議組建 <br> - 'Ranking' 為排名組建 <br/> - 'Fbt' 為 FBT 組建
| apiVersion | 1.0 |
||| | 要求本文 | 如果保留空白，則組建會以預設組建參數執行。<br><br>如果您想要設定組建參數，請將參數當做 XML 傳送至本文，如下列範例所示。(如需組建參數的說明和完整的參數清單，請參閱＜組建參數＞一節)。`<BuildParametersList><NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance></BuildParametersList>` |

**回應**：

HTTP 狀態碼：200

這是非同步的 API。您會得到一個組建識別碼做為回應。若要知道組建何時結束，您應該呼叫「取得模型的組建狀態」API，並在回應中找出這個組建識別碼。請注意，組建可能會花數分鐘到數小時的時間，視資料的大小而定。

您無法取用建議直到組建結束。

有效的組建狀態：

- 建立 – 模型已建立。
- 已排入佇列 – 已觸發模型組建，並已排入佇列。
- 建置中 – 模型正在建置中。
- 成功 – 組建已成功結束。
- 錯誤 – 組建已結束但發生失敗。
- 已取消 – 組建已取消。
- 取消中 – 正在取消組建。

請注意，組建識別碼可以在下列路徑下找到：`Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>




###11.4.取得模型的組建狀態
擷取指定之模型的組建及其狀態。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|


|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	modelId |	模型的唯一識別碼 |
|	onlyLastBuild |	指出是要傳回模型的所有組建歷程記錄，還是只傳回最近一個組建的狀態 |
|	apiVersion |	1.0 |


**回應**：

HTTP 狀態碼：200

回應會包含每個組建的一個項目。每個項目都有下列資料：

- `feed/entry/content/properties/UserName` – 使用者的名稱。
- `feed/entry/content/properties/ModelName` – 模型的名稱。
- `feed/entry/content/properties/ModelId` – 模型的唯一識別碼。
- `feed/entry/content/properties/IsDeployed` – 組建是否已部署 (又稱為作用中組建)。
- `feed/entry/content/properties/BuildId` – 組建的唯一識別碼。
- `feed/entry/content/properties/BuildType` - 組建類型。
- `feed/entry/content/properties/Status` – 組建狀態。可以是下列其中之一：錯誤、建置中、已排入佇列、取消中、已取消、成功。
- `feed/entry/content/properties/StatusMessage` – 詳細狀態訊息 (僅適用於特定狀態)。
- `feed/entry/content/properties/Progress` – 組建進度 (%)。
- `feed/entry/content/properties/StartTime` – 組建開始時間。
- `feed/entry/content/properties/EndTime` – 組建結束時間。
- `feed/entry/content/properties/ExecutionTime` – 組建持續時間。
- `feed/entry/content/properties/ProgressStep` – 正在進行中組建的目前階段相關詳細資料。

有效的組建狀態：- 已建立 – 已建立組建要求項目。已排入佇列 – 組建要求已觸發並已排入佇列。建置中 – 組建進行中。- 成功 – 建置成功結束。- 錯誤 – 組建已結束但發生失敗。- 已取消 – 組建已取消。- 取消中 – 正在取消組建。

組建類型的有效值：- Rank - 排名組建。- Recommendation - 建議組建。


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a model</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T17:51:10Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
		<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T17:51:10Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
					<d:ModelName m:type="Edm.String">ModelName</d:ModelName>
					<d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
					<d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
					<d:BuildId m:type="Edm.String">1000272</d:BuildId>
                    <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
					<d:Status m:type="Edm.String">Success</d:Status>
					<d:StatusMessage m:type="Edm.String"></d:StatusMessage>
					<d:Progress m:type="Edm.String">0</d:Progress>
					<d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
					<d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
					<d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
					<d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
					<d:ProgressStep m:type="Edm.String"></d:ProgressStep>
				</m:properties>
			</content>
		</entry>
	</feed>


###11.5.取得組建狀態
擷取使用者之所有模型的組建狀態。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=<bool>&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=true&apiVersion=%271.0%27`|


|	參數名稱 |	有效值 |
|:--------			|:--------								|
|	onlyLastBuild |	指出是要傳回模型的所有組建歷程記錄，還是只傳回最近一個組建的狀態。 |
|	apiVersion |	1.0 |


**回應**：

HTTP 狀態碼：200

回應會包含每個組建的一個項目。每個項目都有下列資料：

- `feed/entry/content/properties/UserName` – 使用者的名稱。
- `feed/entry/content/properties/ModelName` – 模型的名稱。
- `feed/entry/content/properties/ModelId` – 模型的唯一識別碼。
- `feed/entry/content/properties/IsDeployed` – 組建是否已部署。
- `feed/entry/content/properties/BuildId` – 組建的唯一識別碼。
- `feed/entry/content/properties/BuildType` - 組建類型。
- `feed/entry/content/properties/Status` – 組建狀態。可以是下列其中之一：錯誤、建置中、已排入佇列、已取消、取消中、成功。
- `feed/entry/content/properties/StatusMessage` – 詳細狀態訊息 (僅適用於特定狀態)。
- `feed/entry/content/properties/Progress` – 組建進度 (%)。
- `feed/entry/content/properties/StartTime` – 組建開始時間。
- `feed/entry/content/properties/EndTime` – 組建結束時間。
- `feed/entry/content/properties/ExecutionTime` – 組建持續時間。
- `feed/entry/content/properties/ProgressStep` – 正在進行中組建的目前階段相關詳細資料。

有效的組建狀態：- 已建立 – 已建立組建要求項目。已排入佇列 – 組建要求已觸發並已排入佇列。建置中 – 組建進行中。- 成功 – 建置成功結束。- 錯誤 – 組建已結束但發生失敗。- 已取消 – 組建已取消。- 取消中 – 正在取消組建。


組建類型的有效值：- Rank - 排名組建。- Recommendation - 建議組建。


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T18:41:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T18:41:21Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
					<d:ModelName m:type="Edm.String">ModelName</d:ModelName>
					<d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
					<d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
					<d:BuildId m:type="Edm.String">1000272</d:BuildId>
                    <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
					<d:Status m:type="Edm.String">Success</d:Status>
					<d:StatusMessage m:type="Edm.String"></d:StatusMessage>
					<d:Progress m:type="Edm.String">0</d:Progress>
					<d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
					<d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
					<d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
					<d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
					<d:ProgressStep m:type="Edm.String"></d:ProgressStep>
				</m:properties>
			</content>
		</entry>
	</feed>


###11.6.刪除組建
刪除組建。

注意：<br>您無法刪除作用中組建。應該先將模型更新至不同的作用中組建，再刪除模型。<br>您無法刪除進行中的組建。您應該先藉由呼叫 <strong>取消組建</strong> 來取消組建。

| HTTP 方法 | URI |
|:--------|:--------|
|刪除 |`<rootURI>/DeleteBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/DeleteBuild?buildId=%271500068%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| buildId | 組建的唯一識別碼。 |
| apiVersion | 1.0 |

**回應：**

HTTP 狀態碼：200

###11.7.取消組建
取消狀態為建置中的組建。

| HTTP 方法 | URI |
|:--------|:--------|
|PUT |`<rootURI>/CancelBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/CancelBuild?buildId=%271500076%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| buildId | 組建的唯一識別碼。 |
| apiVersion | 1.0 |

**回應：**

HTTP 狀態碼：200

###11.8.取得組建參數
擷取組建參數。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetBuildParameters?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/GetBuildParameters?buildId=%271000653%27&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| buildId | 組建的唯一識別碼。 |
| apiVersion | 1.0 |

**回應：**

HTTP 狀態碼：200

此 API 會傳回索引鍵/值項目的集合。每個項目都代表參數和它的值：- `feed/entry/content/properties/Key` – 組建參數名稱。- `feed/entry/content/properties/Value` – 組建參數值。

下表描述每個索引鍵表示的值。

|金鑰|說明|類型|有效值|
|:-----|:----|:----|:---|
|NumberOfModelIterations | 整體運算時間和模型精確度會反映模型執行反覆運算的次數。數字越高，得到的精確度就越高，但需要較久的運算時間。| Integer | 10-50 |
| NumberOfModelDimensions | 維度的數目與「功能」的數目相關，模型會嘗試尋找資料中的數目。增加維度的數目將允許結果進一步微調成較小的叢集。不過，太多維度會讓模型無法尋找項目之間的關聯。 | Integer | 10-40 |
|ItemCutOffLowerBound| 定義冷凝器的項目下限。請參閱上述的使用狀況冷凝器。 | Integer | 2 個以上 (0 代表停用冷凝器) |
|ItemCutOffUpperBound| 定義冷凝器的項目上限。請參閱上述的使用狀況冷凝器。 | Integer | 2 個以上 (0 代表停用冷凝器) |
|UserCutOffLowerBound| 定義冷凝器的使用者下限。請參閱上述的使用狀況冷凝器。 | Integer | 2 個以上 (0 代表停用冷凝器) |
|UserCutOffUpperBound| 定義冷凝器的使用者上限。請參閱上述的使用狀況冷凝器。 | Integer | 2 個以上 (0 代表停用冷凝器) |
| 說明 | 建置說明。 | String | 任何文字，最多 512 個字元 |
| EnableModelingInsights | 可讓您計算建議模型上的度量。 | Boolean | True/False |
| UseFeaturesInModel | 指出是否可以使用功能以加強建議模型。 | Boolean | True/False |
| ModelingFeatureList | 使用於建議組建中的功能名稱逗號分隔清單，可加強建議。 | String | 功能名稱，最多 512 個字元 |
| AllowColdItemPlacement | 指出建議是否也應該透過功能相似度推入冷項目。 | Boolean | True/False |
| EnableFeatureCorrelation | 指出功能是否可用於推論。 | Boolean | True/False |
| ReasoningFeatureList | 用於推論句 (例如建議說明) 的功能名稱逗號分隔清單。 | String | 功能名稱，最多 512 個字元 |


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get build parameters</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:50:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UseFeaturesInModel</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">AllowColdItemPlacement</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableFeatureCorrelation</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableModelingInsights</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelIterations</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
			<titletype="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelDimensions</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<linkrel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ModelingFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ReasoningFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">Description</d:Key>
					<d:Value m:type="Edm.String">rankBuild</d:Value>
				</m:properties>
			</content>
		</entry>
	</feed>

##12.建議
###12.1.取得項目建議 (適用於作用中組建)

根據種子 (輸入) 項目的清單，取得 "Recommendation" 或 "Fbt" 類型之作用中組建的建議。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId | 模型的唯一識別碼 |
| itemIds | 要建議的以逗號分隔項目清單。<br>如果作用中組建的類型是 FBT，您可以只傳送一個項目。<br>最大長度：1024 |
| numberOfResults | 必要結果的數目 |
| includeMetatadata | 未來使用，永遠為 false |
| apiVersion | 1.0 |

**回應：**

HTTP 狀態碼：200


回應會包含每個建議項目的一個項目。每個項目具有下列資料：- `Feed\entry\content\properties\Id` – 建議項目識別碼。- `Feed\entry\content\properties\Name` – 項目的名稱。- `Feed\entry\content\properties\Rating` – 建議的評等，數字越高表示信賴度越高。- `Feed\entry\content\properties\Reasoning` – 建議推論 (例如建議說明)。

以下範例回應包含 10 個建議項目。

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###12.2.取得項目建議 (屬於特定組建)

取得 "Recommendation" 或 "Fbt" 等類型之特定組建的建議。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId | 模型的唯一識別碼 |
| itemIds | 要建議的以逗號分隔項目清單。<br>如果作用中組建的類型是 FBT，您可以只傳送一個項目。<br>最大長度：1024 |
| numberOfResults | 必要結果的數目 |
| includeMetatadata | 未來使用，永遠為 false
| buildId | 要用於此建議要求的組建識別碼 |
| apiVersion | 1.0 |

**回應：**

HTTP 狀態碼：200


回應會包含每個建議項目的一個項目。每個項目具有下列資料：- `Feed\entry\content\properties\Id` – 建議項目識別碼。- `Feed\entry\content\properties\Name` – 項目的名稱。- `Feed\entry\content\properties\Rating` – 建議的評等，數字越高表示信賴度越高。- `Feed\entry\content\properties\Reasoning` – 建議推論 (例如建議說明)。

請參閱 12.1 中的回應範例

###12.3.取得 FBT 建議 (適用於作用中組建)

根據種子 (輸入) 項目，取得 "Fbt" 類型之作用中組建的建議。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=<double>&includeMetadata=false&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId | 模型的唯一識別碼 |
| itemId | 建議的項目。<br>最大長度：1024 |
| numberOfResults | 必要結果的數目 |
| minimalScore | 頻繁集合應該具有的最低分數，以包含在傳回的結果中 |
| includeMetatadata | 未來使用，永遠為 false |
| apiVersion | 1.0 |

**回應：**

HTTP 狀態碼：200


回應會包含每個建議項目集 (通常會與種子/輸入項目一起購買的一組項目) 的一個項目。每個項目具有下列資料：- `Feed\entry\content\properties\Id1` - 建議項目識別碼。- `Feed\entry\content\properties\Name1` - 項目的名稱。- `Feed\entry\content\properties\Id2` - 第二個建議項目識別碼 (選擇性)。- `Feed\entry\content\properties\Name2` - 第二個項目的名稱 (選擇性)。- `Feed\entry\content\properties\Rating` - 建議的評等，數字越高表示信賴度越高。- `Feed\entry\content\properties\Reasoning` - 建議推論 (例如建議說明)。

以下範例回應包含 3 個建議項目集。

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemId='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">159</d:Id1>
        <d:Name1 m:type="Edm.String">159</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">52</d:Id1>
        <d:Name1 m:type="Edm.String">52</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.533343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">35</d:Id1>
        <d:Name1 m:type="Edm.String">35</d:Name1>
		<d:Id2 m:type="Edm.String">102</d:Id2>
        <d:Name2 m:type="Edm.String">102</d:Name2>
        <d:Rating m:type="Edm.Double">0.523343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '35' and '102'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###12.4.取得 FBT 建議 (屬於特定組建)

取得 "Fbt" 類型之特定組建的建議。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=0.1&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId | 模型的唯一識別碼 |
| itemId | 建議的項目。<br>最大長度：1024 |
| numberOfResults | 必要結果的數目 |
| minimalScore | 頻繁集合應該具有的最低分數，以包含在傳回的結果中 |
| includeMetatadata | 未來使用，永遠為 false |
| buildId | 要用於此建議要求的組建識別碼 |
| apiVersion | 1.0 |

**回應：**

HTTP 狀態碼：200


回應會包含每個建議項目集 (通常會與種子/輸入項目一起購買的一組項目) 的一個項目。每個項目具有下列資料：- `Feed\entry\content\properties\Id1` - 建議項目識別碼。- `Feed\entry\content\properties\Name1` - 項目的名稱。- `Feed\entry\content\properties\Id2` - 第二個建議項目識別碼 (選擇性)。- `Feed\entry\content\properties\Name2` - 第二個項目的名稱 (選擇性)。- `Feed\entry\content\properties\Rating` - 建議的評等，數字越高表示信賴度越高。- `Feed\entry\content\properties\Reasoning` - 建議推論 (例如建議說明)。

請參閱 12.3 中的回應範例

###12.5.取得使用者建議 (適用於作用中組建)

取得標示為作用中組建之 "Recommendation" 類型之組建的使用者建議。

這個 API 會根據使用者的使用歷程記錄，傳回預測的項目清單。

注意：1.FBT 組建沒有使用者建議。2.如果作用中組建是 FBT，這個方法會傳回錯誤。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId | 模型的唯一識別碼 |
| userId | 使用者的唯一識別碼 |
| numberOfResults | 必要結果的數目 |
| includeMetatadata | 未來使用，永遠為 false |
| apiVersion | 1.0 |

**回應：**

HTTP 狀態碼：200


回應會包含每個建議項目的一個項目。每個項目具有下列資料：- `Feed\entry\content\properties\Id` – 建議項目識別碼。- `Feed\entry\content\properties\Name` – 項目的名稱。- `Feed\entry\content\properties\Rating` – 建議的評等，數字越高表示信賴度越高。- `Feed\entry\content\properties\Reasoning` – 建議推論 (例如建議說明)。

請參閱 12.1 中的回應範例

###12.6.使用項目清單取得使用者建議 (適用於作用中組建)

使用額外的項目清單，取得標示為作用中組建之 "Recommendation" 類型之組建的使用者建議。

這個 API 會根據使用者的使用歷程記錄和額外提供的項目，傳回預測的項目清單。

注意：1.FBT 組建沒有使用者建議。2.如果作用中組建是 FBT，這個方法會傳回錯誤。


| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>&itemIds=%27<itemIds>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId | 模型的唯一識別碼 |
| userId | 使用者的唯一識別碼 |
| itemIds | 要建議的以逗號分隔項目清單。最大長度：1024 |
| numberOfResults | 必要結果的數目 |
| includeMetatadata | 未來使用，永遠為 false |
| apiVersion | 1.0 |

**回應：**

HTTP 狀態碼：200


回應會包含每個建議項目的一個項目。每個項目具有下列資料：- `Feed\entry\content\properties\Id` – 建議項目識別碼。- `Feed\entry\content\properties\Name` – 項目的名稱。- `Feed\entry\content\properties\Rating` – 建議的評等，數字越高表示信賴度越高。- `Feed\entry\content\properties\Reasoning` – 建議推論 (例如建議說明)。

請參閱 12.1 中的回應範例

###12.7.取得使用者建議 (屬於特定組建)

取得 "Recommendation" 類型之特定組建的使用者建議。

這個 API 會根據使用者的使用歷程記錄 (用於特定組建)，傳回預測的項目清單。

注意：FBT 組建沒有使用者建議。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|


|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId | 模型的唯一識別碼 |
| userId | 使用者的唯一識別碼 |
| numberOfResults | 必要結果的數目 |
| includeMetatadata | 未來使用，永遠為 false |
| buildId | 要用於此建議要求的組建識別碼 |
| apiVersion | 1.0 |

**回應：**

HTTP 狀態碼：200


回應會包含每個建議項目的一個項目。每個項目具有下列資料：- `Feed\entry\content\properties\Id` – 建議項目識別碼。- `Feed\entry\content\properties\Name` – 項目的名稱。- `Feed\entry\content\properties\Rating` – 建議的評等，數字越高表示信賴度越高。- `Feed\entry\content\properties\Reasoning` – 建議推論 (例如建議說明)。

請參閱 12.1 中的回應範例


###12.8.使用項目清單取得使用者建議 (屬於特定組建)

取得 "Recommendation" 類型和額外的項目清單之特定組建的使用者建議。

這個 API 會根據使用者的使用歷程記錄和額外的項目清單，傳回預測的項目清單。

注意：FBT 組建沒有使用者建議。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|



|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId | 模型的唯一識別碼 |
| userId | 使用者的唯一識別碼 |
| itemIds | 要建議的以逗號分隔項目清單。最大長度：1024 |
| numberOfResults | 必要結果的數目 |
| includeMetatadata | 未來使用，永遠為 false |
| buildId | 要用於此建議要求的組建識別碼 |
| apiVersion | 1.0 |

**回應：**

HTTP 狀態碼：200


回應會包含每個建議項目的一個項目。每個項目具有下列資料：- `Feed\entry\content\properties\Id` – 建議項目識別碼。- `Feed\entry\content\properties\Name` – 項目的名稱。- `Feed\entry\content\properties\Rating` – 建議的評等，數字越高表示信賴度越高。- `Feed\entry\content\properties\Reasoning` – 建議推論 (例如建議說明)。

請參閱 12.1 中的回應範例

##13.使用者使用歷程記錄
一旦建置建議模型，系統即會允許擷取用於組建的使用者歷程記錄 (與特定使用者相關聯的項目)。這個 API 可擷取使用者歷程記錄

注意：目前只有建議組建可使用使用者歷程記錄。

###13.1 擷取使用者歷程記錄
擷取用於作用中組建或指定使用者識別碼之指定組建中的項目清單。

| HTTP 方法 | URI |
|:--------|:--------|
|GET | 取得作用中組建的使用者歷程記錄。<br/>`<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&apiVersion=%271.0%27`<br/><br/>取得指定組建的使用者歷程記錄 `<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&buildId=<int>&apiVersion=%271.0%27`<br/><br/>範例：`<rootURI>/GetUserHistory?modelId=%2727967136e8-f868-4258-9331-10d567f87fae%27&&userId=%27u_1013%27&apiVersion=%271.0%277`|


|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId | 模型的唯一識別碼。|
| userId | 使用者的唯一識別碼。
| buildId | 選擇性參數，可用來表示應從中擷取使用者歷程記錄的組建
| apiVersion | 1.0 |


**回應：**

HTTP 狀態碼：200

回應會包含每個建議項目的一個項目。每個項目具有下列資料：- `Feed\entry\content\properties\Id` - 建議項目識別碼。- `Feed\entry\content\properties\Name` - 項目的名稱。- `Feed\entry\content\properties\Rating` - N/A。- `Feed\entry\content\properties\Reasoning` - N/A。

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get User History</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-05-26T15:32:47Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">CatalogItemsThatContainATokenEntity</title>
		<updated>2015-05-26T15:32:47Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Rating m:type="Edm.Double">0</d:Rating>
				<d:Reasoning m:type="Edm.String"/>
				<d:Metadata m:type="Edm.String"/>
				<d:FormattedRating m:type="Edm.Double" m:null="true"/>
			</m:properties>
		</content>
	</entry>
</feed>

##14.通知
Azure Machine Learning 建議會在系統中持續發生錯誤時建立通知。有 3 個類型的通知：1.組建失敗 – 每個組建失敗都會觸發此通知。2.資料擷取處理失敗 - 當我們在處理每一模型的使用事件時，如果最後 5 分鐘有超過 100 個錯誤，就會觸發此通知。3.建議取用失敗 - 當我們在處理每一模型的建議要求時，如果最後 5 分鐘有超過 100 個錯誤，就會觸發此通知。


###14.1.取得通知
擷取所有模型或單一模型的所有通知。

| HTTP 方法 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetNotifications?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>取得所有模型的所有通知：<br>`<rootURI>/GetNotifications?apiVersion=%271.0%27`<br><br>取得特定模型通知的範例：<br>`<rootURI>/GetNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%277`|


|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId | 選擇性參數。如果省略此參數，您將會取得所有模型的所有通知。<br>有效值：模型的唯一識別碼。|
| apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應：**

HTTP 狀態碼：200

OData XML

    The response includes one entry per notification. Each entry has the following data:
		* feed\entry\content\properties\UserName – Internal user name identification.
		* feed\entry\content\properties\ModelId – Model ID.
		* feed\entry\content\properties\Message – Notification message.
		* feed\entry\content\properties\DateCreated – Date that this notification was created in UTC format.
		* feed\entry\content\properties\NotificationType – Notification types. Values are BuildFailure, RecommendationFailure, and DataAquisitionFailure.

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of Notifications for a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-04T13:24:23Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'" />
		<entry>
				<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfNotificationsForAUserEntity</title>
			<updated>2014-11-04T13:24:23Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">515506bc-3693-4dce-a5e2-81cb3e8efb56@dm.com</d:UserName>
					<d:ModelId m:type="Edm.String">967136e8-f868-4258-9331-10d567f87fae</d:ModelId>
					<d:Message m:type="Edm.String">Build failed for user</d:Message>
					<d:DateCreated m:type="Edm.String">2014-11-04T13:23:14.383</d:DateCreated>
					<d:NotificationType m:type="Edm.String">BuildFailure</d:NotificationType>
				</m:properties>
			</content>
		</entry>
	</feed>

###14.2.刪除模型通知
刪除模型的所有已讀通知。

| HTTP 方法 | URI |
|:--------|:--------|
|刪除 |`<rootURI>/DeleteModelNotifications?modelId=%<model_id>%27&apiVersion=%271.0%27`<br><br>範例：<br>`<rootURI>/DeleteModelNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%27`|


|	參數名稱 |	有效值 |
|:--------			|:--------								|
| modelId | 模型的唯一識別碼 |
| apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200

###14.3.刪除使用者通知
刪除所有模型的所有通知。

| HTTP 方法 | URI |
|:--------|:--------|
|刪除 |`<rootURI>/DeleteUserNotifications?apiVersion=%271.0%27`|


|	參數名稱 |	有效值 |
|:--------			|:--------								|
| apiVersion | 1.0 |
||| | 要求主體 | 無 |

**回應**：

HTTP 狀態碼：200




##15.法律
這份文件係依 「現狀」提供。本文件中說明的資訊與畫面 (包括 URL 及其他網際網路網站參考資料) 如有變更， 恕不另行通知。<br><br> 此處描述的一些範例僅供說明之用，純屬虛構。並未影射或關聯任何真實人事物。<br><br> 本文件未提供給您任何 Microsoft 產品中任何智慧財產的任何法定權利。您可以複製並使用這份文件，供內部參考之用。<br><br> © 2015 Microsoft.著作權所有，並保留一切權利。
 

<!---HONumber=July15_HO2-->