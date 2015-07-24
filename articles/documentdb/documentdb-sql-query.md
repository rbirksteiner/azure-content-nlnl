<properties 
	pageTitle="使用 DocumentDB SQL 進行查詢 | Azure" 
	description="DocumentDB (一種 NoSQL 文件資料庫服務) 支援透過階層式 JSON 文件使用類似 SQL 文法來進行查詢，而不需要明確的結構描述或建立次要索引。" 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mimig"/>

#查詢 DocumentDB
Microsoft Azure DocumentDB 支援在階層式 JSON 文件上使用 SQL (結構化查詢語言) 來查詢文件。DocumentDB 確實是無結構描述。由於它是直接在資料庫引擎內使用 JSON 資料模型，因此它提供不需明確的結構描述或建立次要索引，即可自動編製 JSON 文件索引的功能。 

在為 DocumentDB 設計查詢語言時，我們有兩個謹記的目標：

-	<strong>利用 SQL</strong> - 我們並不想發明新的查詢語言，而是要利用 SQL 語言。SQL 是其中一個最熟悉且熱門的查詢語言。DocumentDB SQL 查詢語言提供一個正式的程式設計模型，可在 JSON 文件上進行豐富的查詢。
-	<strong>延伸 SQL</strong> - 為了做為能夠直接在資料庫引擎中執行 JavaScript 的 JSON 文件資料庫，我們想要使用 JavaScript 的程式設計模型做為 SQL 查詢語言的基礎。DocumentDB SQL 查詢語言是以 JavaScript 的類型系統、運算式評估和函式叫用為基礎。這除了其他功能之外，還進而提供自然程式設計模型來進行關聯式投射、跨 JSON 文件的階層式導覽、自我聯結，以及叫用完全以 JavaScript 撰寫的使用者定義函式 (UDF)。 

我們相信這些功能的重點是減少應用程式與資料庫之間的摩擦，而且對開發人員的生產力而言十分重要。

建議使用者從觀看下列影片開始入門，在影片中 Aravind Ramachandran 顯示 DocumentDB 的查詢功能，並且造訪我們的[查詢遊樂場](http://www.documentdb.com/sql/demo)，您可以在其中試試 DocumentDB 並對我們的資料集執行 SQL 查詢。

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

然後回到本文中，我們會先從逐步解說一些簡單的 JSON 文件和查詢開始。

## 開始使用
為了查看 DocumentDB SQL 如何運作，讓我們從一些簡單的 JSON 文件開始著手，然後對其逐步執行一些簡單查詢。請考慮有關兩個家族的這兩份 JSON 文件。請注意，使用 DocumentDB，我們不需要明確地建立任何結構描述或次要索引。我們只需要將 JSON 文件插入到 DocumentDB 集合中，再接著進行查詢即可。 
這裡有 Andersen 一家的簡單 JSON 文件，包括這一家的父母、孩子 (以及寵物)、地址及註冊資訊。這份文件包含字串、數字、布林值、陣列和巢狀屬性。 

**文件**  

	{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "isRegistered": true
	}


以下是具有些微差異的第二份文件：使用  `givenName` 和  `familyName`，而不是使用  `firstName` 和  `lastName`。

**文件**  

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	             "givenName": "Jesse", 
	            "gender": "female", "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	            "familyName": "Miller", 
	             "givenName": "Lisa", 
	             "gender": "female", 
	             "grade": 8 }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "isRegistered": false
	}



現在，讓我們嘗試對此資料執行一些查詢，以了解 DocumentDB SQL 的一些重要部分。例如，下列查詢將會傳回 id 欄位符合  `AndersenFamily` 的文件。因為它是 `SELECT *`，所以查詢輸出會是完整 JSON 文件：

**查詢**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "isRegistered": true
	}]


現在，請考慮我們需要以不同形式來重新格式化 JSON 輸出的情況。當地址所在城市的名稱與省/市的名稱相同時，此查詢會投射具有兩個所選欄位 (Name 和 City) 的新 JSON 物件。在此情況下，"NY, NY" 相符。

**查詢**	

	SELECT {"Name":f.id, "City":f.address.city} AS Family 
	FROM Families f 
	WHERE f.address.city = f.address.state

**結果**

	[{
	    "Family": {
	        "Name": "WakefieldFamily", 
	        "City": "NY"
	    }
	}]


下一個查詢會傳回家族中子女 id 符合  `WakefieldFamily` 的所有名字。

**查詢**

	SELECT c.givenName 
	FROM Families f 
	JOIN c IN f.children 
	WHERE f.id = 'WakefieldFamily'

**結果**

	[
	  { "givenName": "Jesse" }, 
	  { "givenName": "Lisa"}
	]


我們想要透過目前所看到的範例來指出 DocumentDB 查詢語言的一些重要部分：  
 
-	因為 DocumentDB SQL 處理 JSON 值，所以它處理樹狀形式的實體，而不是資料列和資料行。因此，此語言可讓您參照樹狀目錄中任意深度的節點 (例如  `Node1.Node2.Node3.....Nodem`)，這與關聯式 SQL 參照 `<table>.<column>` 的兩個部分參考類似。   
-	語言處理無結構描述的資料。因此，需要動態繫結類型系統。相同的運算式可能會對不同的文件產生不同的類型。查詢的結果會是有效的 JSON 值，但不保證會是固定的結構描述。  
-	DocumentDB 只支援嚴謹的 JSON 文件。這表示類型系統和運算式只能處理 JSON 類型。如需更多詳細資訊，請參閱 [JSON 規格](http://www.json.org/)。  
-	DocumentDB 集合是 JSON 文件的無結構描述容器。集合中文件內及跨文件之資料實體中的關係，會由內含項目以隱含方式擷取，而不是由主索引鍵和外部索引鍵關係擷取。這是本文稍後討論之文件內聯結中值得指出的重要部分。

##DocumentDB 索引

在進入 DocumentDB SQL 語言之前，有必要探索一下 DocumentDB 的索引設計。 

資料庫索引的目的是要在使用最少資源 (例如 CPU、輸入/輸出) 的情況下，以各種方式提供查詢，同時兼顧良好的輸送量和低延遲。通常，選擇適用於資料庫查詢的正確索引需要經過相當的規劃和實驗。此方式對資料不符合嚴謹結構描述的無結構描述資料庫而言是種挑戰，而且發展十分迅速。 

因此，我們在設計 DocumentDB 索引子系統時，設定了下列目標：

-	在不需要結構描述的情況下，對文件編製索引：索引子系統不需要任何結構描述資訊，或提出任何文件結構描述的相關假設。 

-	支援有效率、豐富階層式及關聯式查詢：索引可有效率地支援 DocumentDB 查詢語言，包括支援階層式和關聯式投射。

-	支援在面對持續大量寫入時進行一致查詢：在面對持續大量寫入時，針對具有一致查詢的高寫入輸送量工作負載，會以累加、有效率及線上方式更新索引。一致的索引更新對於提供一致性層級的查詢十分重要，在此層級中是由使用者設定文件服務。

-	支援多重租用：由於是使用保留型模型進行跨租用戶的資源控管，因此會在為每一複本配置的系統資源 (CPU、記憶體、輸入/輸出) 預算內執行索引更新。 

-	儲存體效率：基於成本效益，索引的磁碟儲存體額外負荷有所限制且可預測。這十分重要，因為 DocumentDB 允許開發人員在索引額外負荷與查詢效能之間做出成本取捨。  

如需示範如何為集合設定索引原則的範例，請參閱 MSDN 上的 [DocumentDB 範例](http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content)。現在，讓我們深入討論 DocumentDB SQL 語言。


##DocumentDB 查詢基礎
根據 ANSI-SQL 標準，每個查詢都會包含 SELECT 子句以及選擇性的 FROM 和 WHERE 子句。針對每個查詢，通常都會列舉 FROM 子句中的來源。接著，會對來源套用 WHERE 子句中的篩選，以擷取 JSON 文件的子集。最後，使用 SELECT 子句來投射選取清單中所要求的 JSON 值。
    
    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]    


##FROM 子句
 `FROM <from_specification>` 為選用子句，除非稍後在查詢中篩選或投射來源。此子句的目的是指定查詢必須操作的資料來源。整個集合通常是來源，但是您可以改為指定集合的子集。 

類似  `SELECT * FROM Families` 的查詢指出整個 Families 集合是要列舉的來源。可使用特殊識別碼 ROOT 來代表此集合，而不使用集合名稱。 
下列清單包含會針對每個查詢強制執行的規則：

- 集合可以進行別名處理，例如  `SELECT f.id FROM Families AS f` 或只是  `SELECT f.id FROM Families f` 。在此， `f` 相當於 `Families`. `AS` 是一個選擇性的關鍵字，可別名處理識別項。

-	請注意，進行別名處理之後，無法繫結原始來源。例如， `SELECT Familes.id FROM Families f` 在語句構造上無效，因為無法再解析識別碼 "Families"。

-	所有需要參照的屬性都必須是完整的。如果未遵循嚴謹的結構描述，則會強制執行以避免任何模糊的繫結。因此， `SELECT id FROM Families f` 在語句構造上無效，因為不會繫結屬性  `id`。
	
###子文件
來源也可以減少為更小的子集。例如，若只要列舉每個文件中的樹狀子目錄，則子根目錄可能會變成來源 (如下列範例所示)。

**查詢**

	SELECT * 
	FROM Families.children

**結果**  

	[
	  [
	    {
	        "firstName": "Henriette Thaulow",
	        "gender": "female",
	        "grade": 5,
	        "pets": [
	          {
	              "givenName": "Fluffy"
	          }
	        ]
	    }
	  ],
	  [
	    {
	        "familyName": "Merriam",
	        "givenName": "Jesse",
	        "gender": "female",
	        "grade": 1
	    },
	    {
	        "familyName": "Miller",
	        "givenName": "Lisa",
	        "gender": "female",
	        "grade": 8
	    }
	  ]
	]

雖然上面的範例使用陣列做為來源，但是也可以使用物件做為來源 (如下列範例所示)。任何可在來源中找到的有效 JSON 值 (非 undefined)，都會被考量納入查詢的結果中。如果有些家族沒有  `address.state` 值，則會在查詢結果中予以排除。

**查詢**

	SELECT * 
	FROM Families.address.state

**結果**

	[
	  "WA", 
	  "NY"
	]


##WHERE 子句
WHERE 子句 (**`WHERE <filter_condition>`**) 為選擇性子句。它會指定條件，而且來源所提供的 JSON 文件必須滿足這些條件才能併入為結果的一部分。任何 JSON 文件都必須將指定的條件評估為 "true"，才能視為結果。索引層使用 WHERE 子句，來判斷可為結果一部分的來源文件的絕對最小子集。 

下列查詢會要求包含名稱屬性且其值為 `AndersenFamily` 的文件。不包含名稱屬性或值不符合 `AndersenFamily` 的任何其他文件。 

**查詢**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


前一個範例已顯示簡單的相等查詢。DocumentDB SQL 也支援各種純量運算式。最常用的是二元和一元運算式。來源 JSON 物件中的屬性參考也是有效的運算式。 

下列是目前支援並可在查詢中使用 (如下列範例所示) 的二元運算子：  
<table>
<tr>
<td>算術</td>	
<td>+、-、*、/、%</td>
</tr>
<tr>
<td>位元</td>	
<td>|、&、^、<<、>>、>>> (右移位並填滿零) </td>
</tr>
<tr>
<td>邏輯</td>
<td>AND、OR</td>
</tr>
<tr>
<td>比較</td>	
<td>=、!=、>、>=、<、<=、<></td>
</tr>
<tr>
<td>字串</td>	
<td>|| (串連)</td>
</tr>
</table>  

讓我們了解一些使用二元運算子的查詢。

	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade % 2 = 1     -- matching grades == 5, 1
	
	SELECT * 
	FROM Families.children[0] c
	WHERE c.grade ^ 4 = 1    -- matching grades == 5
	
	SELECT *
	FROM Families.children[0] c
	WHERE c.grade >= 5     -- matching grades == 5


一元運算子 +、-、~ 及 NOT 也是支援的運算子，可在查詢內使用 (如下列範例所示)：

	SELECT *
	FROM Families.children[0] c
	WHERE NOT(c.grade = 5)  -- matching grades == 1
	
	SELECT *
	FROM Families.children[0] c
	WHERE (-c.grade = -5)  -- matching grades == 5



除了二元和一元運算子之外，還允許屬性參照。例如： `SELECT * FROM Families f WHERE f.isRegistered` 所傳回的 JSON 文件包含屬性  `isRegistered` 且屬性值等於 JSON  `true` 值。任何其他值 (false、null、Undefined、`<number>`、`<string>`、`<object>`、`<array>` 等) 則會導致從結果中排除來源文件。 

###相等和比較運算子
下表顯示 DocumentDB SQL 中任何兩個 JSON 類型之間的相等比較結果。
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Undefined</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Boolean</strong>
         </td>
         <td valign="top">
            <strong>Number</strong>
         </td>
         <td valign="top">
            <strong>字串</strong>
         </td>
         <td valign="top">
            <strong>Object</strong>
         </td>
         <td valign="top">
            <strong>Array</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Undefined<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Boolean<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Number<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>字串<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Object<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Array<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

其他比較運算子 (例如 >、>=、!=、< 及 <=) 則適用下列規則：   

-	不同類型的比較會導致 Undefined。
-	兩個物件或兩個陣列之間的比較會導致 Undefined。   

如果篩選中純量運算式的結果是 Undefined，則不會將對應的文件併入結果中，因為 Undefined 邏輯上不會等於 "true"。

###BETWEEN 關鍵字
您也可以使用 BETWEEN 關鍵字來表示像對 ANSI SQL 中的值範圍執行查詢。BETWEEN 可用於任何 JSON 基本類型 (數字、字串、布林值和 null)。 

例如，此查詢會傳回其長子的成績介於 1-5 (兩者皆含) 之間的所有家族文件。 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

不同於 ANSI SQL，您也可以在 FROM 子句中使用 BETWEEN 子句，如下列範例所示 。

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

如需更快速的查詢執行時間，請記得針對經過 BETWEEN 子句篩選的任何數值屬性/路徑，建立使用範圍索引類型的檢索原則。 

在 DocumentDB 和 ANSI SQL 中使用 BETWEEN 的主要差異在於，您可以表示針對混合類型屬性的範圍查詢，比方說，在某些文件中 「 成績 」 可能是數字 (5) ，而在其他文件中可能會是字串 ("grade4")。在這些情況下 (像在 JavaScript 中)，這兩種不同類型之間的比較會產生 「 未定義 」，並略過文件。


###邏輯 (AND、OR 和 NOT) 運算子
邏輯運算子的運算對象是布林值。下表顯示這些運算子的邏輯真值表。

<table style = "width:300px">
    <tbody>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>OR</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    True
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
            <td width="45" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="68" valign="top">
                <p>
                    Undefined
                </p>
            </td>
            <td width="87" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
    </tbody>
</table>

<table style = "width:300px">
    <tbody>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>AND</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    True
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    False
                </p>
            </td>
        </tr>
        <tr>
            <td width="55" valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
            <td width="54" valign="top">
                <p>
                    Undefined
                </p>
            </td>
            <td width="58" valign="top">
                <p>
                    False
                </p>
            </td>
            <td width="107" valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
    </tbody>
</table>

<table style = "width:300px">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>NOT</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong></strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>True</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    False
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>False</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    True
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>Undefined</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Undefined
                </p>
            </td>
        </tr>
    </tbody>
</table>

###三元 (?) 和聯合 (??) 運算子：
三元和聯合運算子可用來建立條件運算式，與熱門程式設計語言 (如 C# 和 JavaScript) 類似。 

快速建構新的 JSON 屬性時，三元 (?) 運算子可以說非常好用。比方說，您現在可以撰寫查詢將類別層級分類為一般人可判讀的格式 (例如初級/中級/進階)，如下所示。
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

您也可以巢狀運算子的呼叫，如下方查詢所示。
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

和其他查詢運算子一樣，如果任何文件中的條件運算式遺漏了參考屬性，或如果要比較的類型不同，則查詢結果會將這些文件排除在外。

聯合 (??) 運算子可用來有效率地檢查文件中是否有屬性存在 (a.k.a. 已定義)。針對半結構化或混合類型的資料執行查詢時，這非常有用。例如，此查詢會傳回 "lastName" (如果存在) 或 "surname" (如果不存在)。

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

同樣地，您也可以查詢是否缺少屬性 ("undefined")，如下列範例所示。

    SELECT *
    FROM classes c
    WHERE c.lastName ?? true

##SELECT 子句
SELECT 子句 (**`SELECT <select_list>`**) 是必要子句，並可指定要從查詢擷取的值 (就像在 ANSI-SQL 中一樣)。在來源文件上篩選出來的子集會傳遞給投射階段，而在此階段中，會擷取指定的 JSON 值並建構新的 JSON 物件 (針對每個傳遞給它的輸入)。 

下列範例示範一般的 SELECT 查詢。 

**查詢**

	SELECT f.address
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	  "address": {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }
	}]


###巢狀屬性
在下列範例中，我們將投射兩個巢狀屬性 `f.address.state` 和 `f.address.city`。

**查詢**

	SELECT f.address.state, f.address.city
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	  "state": "WA", 
	  "city": "seattle"
	}]


投射也支援 JSON 運算式 (如下列範例所示)。

**查詢**

	SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle", 
	    "name": "AndersenFamily"
	  }
	}]


讓我們在這裡查看 `$1` 角色。 `SELECT` 子句需要建立 JSON 物件，而且，因為未提供任何索引鍵，所以我們將使用以 `$1` 開頭的隱含引數變數名稱。例如，此查詢會傳回兩個隱含引數變數，名稱為 `$1` 和 `$2`。

**查詢**

	SELECT { "state": f.address.state, "city": f.address.city }, 
	       { "name": f.id }
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	  "$1": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "$2": {
	    "name": "AndersenFamily"
	  }
	}]


###別名
現在，讓我們使用值的明確別名處理來擴充上面的範例。AS 是用來加上別名的關鍵字。請注意，當將第二個值作為 `NameInfo` 投射時，此為選擇性項目 (如所示)。 

如果查詢具有兩個同名的屬性，則必須使用別名處理來重新命名一或兩個屬性，使其在投射的結果中變得更為明確。

**查詢**

	SELECT 
	       { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
	       { "name": f.id } NameInfo
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	  "AddressInfo": {
	    "state": "WA", 
	    "city": "seattle"
	  }, 
	  "NameInfo": {
	    "name": "AndersenFamily"
	  }
	}]


###純量運算式
除了屬性參照之外，SELECT 子句也支援純量運算式 (例如常數、算術運算式、邏輯運算式等)。例如，以下是簡單 "Hello World" 查詢。

**查詢**

	SELECT "Hello World"

**結果**

	[{
	  "$1": "Hello World"
	}]


以下是使用純量運算式的較複雜範例。

**查詢**

	SELECT ((2 + 11 % 7)-2)/3	

**結果**

	[{
	  "$1": 1.33333
	}]


在下列範例中，純量運算式結果是布林值。

**查詢**

	SELECT f.address.city = f.address.state AS AreFromSameCityState
	FROM Families f	

**結果**

	[
	  {
	    "AreFromSameCityState": false
	  }, 
	  {
	    "AreFromSameCityState": true
	  }
	]


###物件和陣列建立
DocumentDB SQL 的另一個重要功能是建立陣列/物件。在前一個範例中，請注意，我們已建立新的 JSON 物件。同樣地，您也可以建構陣列 (如下列範例所示)。

**查詢**

	SELECT [f.address.city, f.address.state] AS CityState 
	FROM Families f	

**結果**  

	[
	  {
	    "CityState": [
	      "seattle", 
	      "WA"
	    ]
	  }, 
	  {
	    "CityState": [
	      "NY", 
	      "NY"
	    ]
	  }
	]

###VALUE 關鍵字
**VALUE** 關鍵字可提供傳回 JSON 值的方法。例如，下面顯示的查詢會傳回純量 `"Hello World"`，而不是 `{$1: "Hello World"}`。

**查詢**

	SELECT VALUE "Hello World"

**結果**

	[
	  "Hello World"
	]


下列查詢會在結果中傳回不含 `"address"` 標籤的 JSON 值。

**查詢**

	SELECT VALUE f.address
	FROM Families f	

**結果**  

	[
	  {
	    "state": "WA", 
	    "county": "King", 
	    "city": "seattle"
	  }, 
	  {
	    "state": "NY", 
	    "county": "Manhattan", 
	    "city": "NY"
	  }
	]

下列範例會延伸此程式碼，以示範如何傳回 JSON 基本值 (JSON 樹狀目錄的分葉層級)。 

**查詢**

	SELECT VALUE f.address.state
	FROM Families f	

**結果**

	[
	  "WA",
	  "NY"
	]


###* 運算子
支援使用特殊運算子 (*) 來依原樣投射文件。使用時，它必須是唯一投射的欄位。雖然 `SELECT * FROM Families f` 等查詢有效，但 `SELECT VALUE * FROM Families f ` 和  `SELECT *, f.id FROM Families f ` 無效。

**查詢**

	SELECT * 
	FROM Families f 
	WHERE f.id = "AndersenFamily"

**結果**

	[{
	    "id": "AndersenFamily",
	    "lastName": "Andersen",
	    "parents": [
	       { "firstName": "Thomas" },
	       { "firstName": "Mary Kay"}
	    ],
	    "children": [
	       {
	           "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
	           "pets": [{ "givenName": "Fluffy" }]
	       }
	    ],
	    "address": { "state": "WA", "county": "King", "city": "seattle" },
	    "isRegistered": true
	}]

##進階概念
###反覆運算
在 DocumentDB SQL 中已透過 **IN** 關鍵字加入新的建構，以支援反覆運算 JSON 陣列。FROM 來源支援反覆運算。讓我們從下列範例開始：

**查詢**

	SELECT * 
	FROM Families.children

**結果**  

	[
	  [
	    {
	      "firstName": "Henriette Thaulow", 
	      "gender": "female", 
	      "grade": 5, 
	      "pets": [{ "givenName": "Fluffy"}]
	    }
	  ], 
	  [
	    {
	        "familyName": "Merriam", 
	        "givenName": "Jesse", 
	        "gender": "female", 
	        "grade": 1
	    }, 
	    {
	        "familyName": "Miller", 
	        "givenName": "Lisa", 
	        "gender": "female", 
	        "grade": 8
	    }
	  ]
	]

現在，讓我們查看另一個查詢，以執行反覆運算集合中的子系。請注意輸出陣列中的差異。此範例會將分割  `children` 並將結果壓平合併為單一陣列。  

**查詢**

	SELECT * 
	FROM c IN Families.children

**結果**  

	[
	  {
	      "firstName": "Henriette Thaulow",
	      "gender": "female",
	      "grade": 5,
	      "pets": [{ "givenName": "Fluffy" }]
	  },
	  {
	      "familyName": "Merriam",
	      "givenName": "Jesse",
	      "gender": "female",
	      "grade": 1
	  },
	  {
	      "familyName": "Miller",
	      "givenName": "Lisa",
	      "gender": "female",
	      "grade": 8
	  }
	]

這可以進一步用來篩選陣列的每個個別項目 (如下列範例所示)。

**查詢**

	SELECT c.givenName
	FROM c IN Families.children
	WHERE c.grade = 8

**結果**  

	[{
	  "givenName": "Lisa"
	}]

###聯結
在關聯式資料庫中，跨資料表的聯結需求極為重要。就設計正規化結構描述而言，邏輯上需要它。與此相反的是，DocumentDB 會處理無結構描述文件的反正規化資料模型。這在邏輯上等同於「自我聯結」。

此語言支援的語法是 <from_source1> JOIN <from_source2> JOIN ...JOIN <from_sourceN>。整體而言，這會傳回一組 **N**-Tuple (具有 **N** 值的 Tuple)。每個 Tuple 所擁有的值，都是將所有集合別名在其個別集合上反覆運算所產生。換句話說，這是參與聯結之集的完整交叉乘積。

下列範例示範 JOIN 子句的運作方式。在下列範例中，結果是空的，因為來源中每個文件與空集合的交叉乘積是空的。

**查詢**

	SELECT f.id
	FROM Families f
	JOIN f.NonExistent

**結果**  

	[{
	}]


在下列範例中，聯結介於文件根目錄與  `children` 子根目錄。它是兩個 JSON 物件之間的交叉乘積。子系是陣列的事實不會影響 JOIN，因為我們是處理為子陣列的單一根目錄。因此，結果只會包含兩個結果，因為每個含有陣列之文件的交叉乘積都只會產生一個文件。

**查詢**

	SELECT f.id
	FROM Families f
	JOIN f.children
 
**結果**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]


下列範例示範較傳統的聯結：

**查詢**

	SELECT f.id
	FROM Families f
	JOIN c IN f.children 

**結果**

	[
	  {
	    "id": "AndersenFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }, 
	  {
	    "id": "WakefieldFamily"
	  }
	]



第一件要注意的事是 **JOIN** 子句的 `from_source` 是迭代器。因此，此案例中的流程如下：  

-	展開陣列中的每個子元素 **c**。
-	套用文件 **f** 的根目錄與第一個步驟中所壓平合併之每個子元素 **c** 的交叉乘積。
-	最後，單獨投射根物件 **f** 名稱屬性。 

第一個文件 (`AndersenFamily`) 只包含一個子元素，因此結果集只包含與此文件對應的單一物件。第二個文件 (`WakefieldFamily`) 包含兩個子系。因此，交叉乘積會產生每個子系的個別物件，進而導致兩個物件 (一個對應至此文件的子系一個)。請注意，這兩個文件中的根欄位會相同，就像您在交叉乘積中預期地一樣。

JOIN 的實際作用是透過圖形中很難投射的交叉乘積來形成 Tuple。此外，如下面範例所示，您可以根據 Tuple 的組合進行篩選，讓使用者選擇全部 Tuple 都符合的條件。

**查詢**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
 
**結果**

	[
	  {
	    "familyName": "AndersenFamily", 
	    "childFirstName": "Henriette Thaulow", 
	    "petName": "Fluffy"
	  }, 
	  {
	    "familyName": "WakefieldFamily", 
	    "childGivenName": "Jesse", 
	    "petName": "Goofy"
	  }, 
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]



此範例是上述範例的自然延伸，用來執行雙重聯結。因此，交叉乘積可被視為下列虛擬程式碼。

	for-each(Family f in Families)
	{	
		for-each(Child c in f.children)
		{
			for-each(Pet p in c.pets)
			{
				return (Tuple(f.id AS familyName, 
	              c.givenName AS childGivenName, 
	              c.firstName AS childFirstName,
	              p.givenName AS petName));
			}
		}
	}

`AndersenFamily` 有一個擁有一隻寵物的小孩。因此，交叉乘積會從此家族產生 1 個資料列 (1*1*1)。不過，WakefieldFamily 有兩個小孩，但只有一個小孩 "Jesse" 養了多隻寵物。而 Jesse 擁有 2 隻寵物。因此，交叉乘積會從此家族產生 1*1*2 = 2 個資料列。

在下一個範例中，對 `pet` 有一個額外篩選。這會排除寵物名稱不是 "Shadow" 的所有 Tuple。請注意，我們可以從陣列建置 Tuple、根據 Tuple 的任何元素進行篩選，以及投射元素的任何組合。 

**查詢**

	SELECT 
		f.id AS familyName,
		c.givenName AS childGivenName,
		c.firstName AS childFirstName,
		p.givenName AS petName 
	FROM Families f 
	JOIN c IN f.children 
	JOIN p IN c.pets
	WHERE p.givenName = "Shadow"

**結果**

	[
	  {
	   "familyName": "WakefieldFamily", 
	   "childGivenName": "Jesse", 
	   "petName": "Shadow"
	  }
	]


##JavaScript 整合
DocumentDB 提供一個程式設計模型，以根據預存程序和觸發程序，直接對集合執行 JavaScript 型應用程式邏輯。這允許：

-	藉由直接在資料庫引擎內深入整合 JavaScript 執行階段，對集合中的文件執行高效能交易式 CRUD 操作和查詢。 
-	將控制流程、變數範圍限制、指派以及例外狀況處理基本類型與資料庫交易的整合自然模型化。如需 JavaScript 整合之 DocumentDB 支援的詳細資料，請參閱 JavaScript 伺服器端程式設計文件。

###使用者定義函數 (UDF)
除了本文中已定義的類型之外，DocumentDB SQL 還支援使用者定義函式 (UDF)。特別的是，如果開發人員可以傳入零個或多個引數並傳回單一引數結果，則支援純量 UDF。系統會檢查所有這些引數是否為合法的 JSON 值。  

DocumentDB SQL 文法已延伸，可支援使用這些使用者定義函數的自訂應用程式邏輯。您可以向 DocumentDB 註冊 UDF，然後在 SQL 查詢中參照它。實際上，UDF 是特別設計來透過查詢進行叫用。這項選擇的必然結果，就是 UDF 無法存取其他 JavaScript 類型 (預存程序和觸發程序) 擁有的內容物件。因為查詢是以唯讀形式執行，所以可以在主要或次要複本上執行。因此，與其他 JavaScript 類型不同，UDF 是設計成在次要複本上執行。

以下是如何在 DocumentDB 資料庫中註冊 UDF 的範例 (特別是在文件集合下)。

   
	   UserDefinedFunction sqrtUdf = new UserDefinedFunction
	   {
	       Id = "SQRT",
	       Body = @"function(number) { 
	                   return Math.sqrt(number);
	               };",
	   };
	   UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
	       collectionSelfLink/* link of the parent collection*/, 
	       sqrtUdf).Result;  
                                                                             
上述範例會建立名為  `SQRT` 的 UDF。它接受單一 JSON 值  `number` 並使用 Math 程式庫來計算數字的平方根。


我們現在可以在投射的查詢中使用此 UDF。從查詢中呼叫 UDF 時，必須以區分大小寫的前置詞 "udf." 限定。 

>[AZURE.NOTE] 在 2015 年 3 月 17 日以前，DocumentDB 支援無需 "udf." 前置詞 的 UDF，像是 SELECT SQRT(5)。這種呼叫模式已被取代。  

**查詢**

	SELECT udf.SQRT(c.grade)
	FROM c IN Families.children

**結果**

	[
	  {
	    "$1": 2.23606797749979
	  }, 
	  {
	    "$1": 1
	  }, 
	  {
	    "$1": 2.8284271247461903
	  }
	]

UDF 也可以用於篩選內 (如下面範例所示)，也可以用 "udf." 前置詞限定：

**查詢**

	SELECT c.grade
	FROM c IN Familes.children
	WHERE udf.SQRT(c.grade) = 1

**結果**

	[{
	    "grade": 1
	}]


在本質上，UDF 是有效的純量運算式，而且可以用於投射和篩選。 

為了擴展 UDF 的能力，請查看另一個含有條件式邏輯的範例：

	   UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
	   {
	       Id = "SEALEVEL",
	       Body = @"function(city) {
	       		switch (city) {
	       		    case 'seattle':
	       		        return 520;
	       		    case 'NY':
	       		        return 410;
	       		    case 'Chicago':
	       		        return 673;
	       		    default:
	       		        return -1;
	                }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(collection.SelfLink, seaLevelUdf);
	
	
以下是執行 UDF 的範例。

**查詢**

	SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
	FROM Families f	

**結果**

	 [
	  {
	    "city": "seattle", 
	    "seaLevel": 520
	  }, 
	  {
	    "city": "NY", 
	    "seaLevel": 410
	  }
	]


如同上述範例所示，UDF 可將 JavaScript 語言的強大功能與 DocumentDB SQL 整合來提供可進行程式設計的豐富介面，以在內建 JavaScript 執行階段功能的協助下，執行複雜的程序性、條件式邏輯。

在處理 UDF 的目前階段 (WHERE 子句或 SELECT 子句) 中，DocumentDB SQL 針對來源中的每個文件提供 UDF 的引數。結果會緊密整合至整體執行管線。如果 JSON 值中沒有 UDF 參數所參照的屬性，則會將參數視為 undefined，因此，而整個略過 UDF 叫用。同樣地，如果 UDF 的結果為未定義，便不會被納入結果中。 

簡言之，UDF 是在進行查詢時執行複雜商務邏輯的不錯工具。

###運算子評估
DocumentDB 憑藉著為 JSON 資料庫，來描繪 JavaScript 運算子和其評估語意。雖然 DocumentDB 嘗試保留 JSON 支援方面的 JavaScript 語意，但是在部分執行個體中，作業評估還是會偏離。

與傳統 SQL 不同，在 DocumentDB SQL 查詢語言中，在從資料庫實際抓取值之前，通常不知道值的類型。為了有效率地執行查詢，大部分的運算子都有嚴謹的類型需求。 

與 JavaScript 不同，DocumentDB SQL 不會執行隱含轉換。例如，類似 `SELECT * FROM Person p WHERE p.Age = 21` 的查詢會針對包含 Age 屬性且值為 21 的文件進行比對。其中 Age 屬性符合字串 "21" 或
符合其他可能無限變化 (如 "021"、"21.0"、"0021"、"00021" 等) 的任何其他文件都將不會進行比對。 
這與 JavaScript 形成對比，在 JavaScript 中，會將字串值隱含地轉換成數字 (根據運算子，例如：==)。這項選擇對 DocumentDB SQL 中有效率的索引比對十分重要。 

##參數化 SQL
DocumentDB 支援查詢使用以類似 @ 標記法表示的參數。參數化 SQL 提供使用者輸入的健全處理和逸出，防止透過 SQL 插入式意外洩露資料。 

比方說，您可以撰寫查詢，將姓氏和地址 (州) 做為參數，然後根據使用者輸入，使用各種不同姓氏和地址 (州) 的值執行查詢。

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

您接著可以將這項要求傳送至 DocumentDB 做為參數化 JSON 查詢，如下所示。

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

參數值可以是任何有效的 JSON (字串、數字、布林值、null，甚至是陣列或巢狀 JSON)。而且因為 DocumentDB 屬於無結構描述，系統不會針對任何類型驗證參數。

##內建函數
DocumentDB 也支援一般作業的數個內建函數，這些函數可用於查詢內，例如使用者定義函數 (UDF)。

<table>
<tr>
<td>數學函數</td>	
<td>ABS、CEILING、EXP、FLOOR、LOG、LOG10、POWER、ROUND、SIGN、SQRT、SQUARE 和 TRUNC</td>
</tr>
<tr>
<td>類型檢查函數</td>	
<td>IS_ARRAY、IS_BOOL、IS_NULL、IS_NUMBER、IS_OBJECT 和 IS_STRING</td>
</tr>
</table>  

如果您目前使用的使用者定義函數 (UDF) 已提供內建函數，您應該使用相對應的內建函數，因為這樣會加快執行速度並更有效率。 

###數學函數
每個數學函數都會執行計算，通常以提供做為引數的輸入值為基礎，並且會傳回數值。以下是支援的內建數學函數資料表。

<table>
<tr>
<td><strong>使用方式</strong></td>
<td><strong>說明</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td>	
<td>傳回指定之數值運算式的絕對 (正) 值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">CEILING (num_expr)</a></td>	
<td>傳回大於或等於指定之數值運算式的最小整數值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">FLOOR (num_expr)</a></td>	
<td>傳回小於或等於指定之數值運算式的最大整數。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td>	
<td>傳回指定之數值運算式的指數。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">LOG (num_expr [、base])</a></td>	
<td>傳回指定之數值運算式的自然對數，或使用指定之基底的對數</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td>	
<td>傳回指定之數值運算式的以 10 為基底的對數值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">ROUND (num_expr)</a></td>	
<td>傳回數值，四捨五入到最接近的整數值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">TRUNC (num_expr)</a></td>	
<td>傳回數值，截斷至最接近的整數值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">SQRT (num_expr)</a></td>	
<td>傳回指定之數值運算式的平方根。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">SQUARE (num_expr)</a></td>	
<td>傳回指定之數值運算式的平方。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">POWER (num_expr, num_expr)</a></td>	
<td>傳回指定之數值運算式的乘冪給指定的值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">SIGN (num_expr)</a></td>	
<td>傳回指定之數值運算式的正負號值 (-1、0、1)。</td>
</tr>
</table> 

例如，您現在可以執行下列的查詢：

**查詢**

    SELECT VALUE ABS(-4)

**結果**

    [4]

相較於 ANSI SQL，DocumentDB 函數的主要差異在於它們的設計用途是有效搭配使用無結構描述資料和混合的結構描述資料。例如，如果您有一個文件的 [大小] 屬性已遺失，或具有非數值的值 (例如 "unknown")，該文件就會被略過而不會傳回錯誤。

###類型檢查函數
類型檢查函數可讓您檢查 SQL 查詢中的運算式類型。類型檢查函數可在屬性類型為變數或未知時，用來快速判斷文件中的屬性類型。以下是支援的內建類型檢查函數資料表。

<table>
<tr>
  <td><strong>使用方式</strong></td>
  <td><strong>說明</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>傳回布林值，表示值的類型是否為陣列。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>傳回布林值，表示值的類型是否為布林值。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>傳回布林值，表示值的類型是否為 null。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>傳回布林值，表示值的類型是否為數字。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>傳回布林值，表示值的類型是否為 JSON 物件。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>傳回布林值，表示值的類型是否為字串。</td>
</tr>
</table>

藉由使用這些函數，您現在可以執行下列查詢：

**查詢**

    SELECT VALUE IS_NUMBER(-4)

**結果**

    [true]


##LINQ 到 DocumentDB SQL
LINQ 是一種 .NET 程式設計模型，此模型會將運算表示為對物件串流的查詢。DocumentDB 提供用戶端程式庫，以透過促進 JSON 與 .NET 物件之間的轉換，以及從小部分的 LINQ 查詢對應至 DocumentDB 查詢，來與 LINQ 互動。 

下圖顯示使用 DocumentDB 支援 LINQ 查詢的架構。開發人員可以使用 DocumentDB 用戶端來建立 **IQueryable** 物件，此物件會直接查詢 DocumentDB 查詢提供者，而此查詢提供者會接著將 LINQ 查詢轉譯成 DocumentDB 查詢。然後，將查詢傳遞給 DocumentDB 伺服器，以擷取一組具有 JSON 格式的結果。傳回的結果會在用戶端進行還原序列化，變成 .NET 物件的串流。

![][1]
 


###.NET 和 JSON 對應
.NET 物件與 JSON 文件之間的對應是自然的，亦即每個資料成員欄位都會對應至 JSON 物件，其中欄位名稱對應至物件的「索引鍵」部分，而「值」部分則遞迴地對應至物件的值部分。請思考一下下列範例。建立的 Family 物件對應至 JSON 文件 (如下所示)。而反之亦然，JSON 文件會對應回 .NET 物件。

**C# 類別**

	public class Family
	{
	    [JsonProperty(PropertyName="id")]
	    public string Id;
	    public Parent[] parents;
	    public Child[] children;
	    public bool isRegistered;
	};
	
	public struct Parent
	{
	    public string familyName;
	    public string givenName;
	};
	
	public class Child
	{
	    public string familyName;
	    public string givenName;
	    public string gender;
	    public int grade;
	    public List<Pet> pets;
	};
	
	public class Pet
	{
	    public string givenName;
	};
	
	public class Address
	{
	    public string state;
	    public string county;
	    public string city;
	};
	
	// Create a Family object.
	Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
	Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
	Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
	Pet pet = new Pet { givenName = "Fluffy" };
	Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
	Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

	{
	    "id": "WakefieldFamily",
	    "parents": [
	        { "familyName": "Wakefield", "givenName": "Robin" },
	        { "familyName": "Miller", "givenName": "Ben" }
	    ],
	    "children": [
	        {
	            "familyName": "Merriam", 
	            "givenName": "Jesse", 
	            "gender": "female", 
	            "grade": 1,
	            "pets": [
	                { "givenName": "Goofy" },
	                { "givenName": "Shadow" }
	            ]
	        },
	        { 
	          "familyName": "Miller", 
	          "givenName": "Lisa", 
	          "gender": "female", 
	          "grade": 8 
	        }
	    ],
	    "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
	    "isRegistered": false
	};



###LINQ 與 SQL 轉譯
DocumentDB 查詢提供者執行從 LINQ 查詢到 DocumentDB SQL 查詢的最佳對應。在下列描述中，我們假設讀者對 LINQ 有基本的認識。

首先，針對類型系統，我們支援所有 JSON 基本類型：數值類型、布林值、字串及 null。只支援這些 JSON 類型。下列是支援的純量運算式。

-	常數值：這些包括評估查詢時之基本資料類型的常數值。

-	屬性/陣列索引運算式：這些運算式參照物件或陣列項目的屬性。

		family.Id;
		family.children[0].familyName;
		family.children[0].grade;
		family.children[n].grade; //n is an int variable

-	算術運算式：這些包括數值和布林值的一般算術運算式。如需完整清單，請參閱 SQL 規格。

		2 * family.children[0].grade;
		x + y;

-	字串比較運算式：這些包括比較字串值與某個常數字串值。  
 
		mother.familyName == "Smith";
		child.givenName == s; //s is a string variable

-	物件/陣列建立運算式：這些運算式傳回複合值類型或匿名類型的物件，或是這類物件的陣列。這些值可以是巢狀值。

		new Parent { familyName = "Smith", givenName = "Joe" };
		new { first = 1, second = 2 }; //an anonymous type with 2 fields              
		new int[] { 3, child.grade, 5 };

###查詢運算子
以下一些範例說明如何將一些標準 LINQ 查詢運算子往下轉譯為 DocumentDB 查詢。

####Select 運算子
語法為 `input.Select(x => f(x))`，其中 `f` 是純量運算式。

**LINQ Lambda 運算式**

	input.Select(family => family.parents[0].familyName);

**SQL** 

	SELECT VALUE f.parents[0].familyName
	FROM Families f



**LINQ Lambda 運算式**

	input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

	SELECT VALUE f.children[0].grade + c
	FROM Families f 



**LINQ Lambda 運算式**

	input.Select(family => new
	{
	    name = family.children[0].familyName,
	    grade = family.children[0].grade + 3
	});


**SQL** 

	SELECT VALUE {"name":f.children[0].familyName, 
	              "grade": f.children[0].grade + 3 }
	FROM Families f



####SelectMany 運算子
語法為 `input.SelectMany(x => f(x))`，其中 `f` 是傳回集合類型的純量運算式。

**LINQ Lambda 運算式**

	input.SelectMany(family => family.children);

**SQL** 

	SELECT VALUE child
	FROM child IN Families.children



####Where 運算子
語法為 `input.Where(x => f(x))`，其中 `f` 是傳回布林值的純量運算式。

**LINQ Lambda 運算式**

	input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith" 



**LINQ Lambda 運算式**

	input.Where(
	    family => family.parents[0].familyName == "Smith" && 
	    family.children[0].grade < 3);

**SQL** 

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"
	AND f.children[0].grade < 3


###複合查詢
您可以包含上述的運算子，以形成功能更強大的查詢。由於 DocumentDB 支援巢狀集合，因此這個組合可以是串連或巢狀形式。

####串連 

語法為 `input(.|.SelectMany())(.Select()|.Where())*`. 串連查詢的開頭可以是  `SelectMany` 查詢，後接多個 `Select` or `Where` 運算子。


**LINQ Lambda 運算式**

	input.Select(family=>family.parents[0])
	    .Where(familyName == "Smith");

**SQL**

	SELECT *
	FROM Families f
	WHERE f.parents[0].familyName = "Smith"



**LINQ Lambda 運算式**

	input.Where(family => family.children[0].grade > 3)
	    .Select(family => family.parents[0].familyName);

**SQL** 

	SELECT VALUE f.parents[0].familyName
	FROM Families f
	WHERE f.children[0].grade > 3



**LINQ Lambda 運算式**

	input.Select(family => new { grade=family.children[0].grade}).
	    Where(anon=> anon.grade < 3);
            
**SQL** 

	SELECT *
	FROM Families f
	WHERE ({grade: f.children[0].grade}.grade > 3)



**LINQ Lambda 運算式**

	input.SelectMany(family => family.parents)
	    .Where(parent => parents.familyName == "Smith");

**SQL** 

	SELECT *
	FROM p IN Families.parents
	WHERE p.familyName = "Smith"



####巢狀

語法為 `input.SelectMany(x=>x.Q())`，其中 Q 是 `Select`、 `SelectMany` 或 `Where` 運算子。

在巢狀查詢中，會將內部查詢套用至外部集合的每個項目。其中一個重要功能是內部查詢可以參照外部集合中項目的欄位 (例如自我聯結)。

**LINQ Lambda 運算式**

	input.SelectMany(family=> 
	    family.parents.Select(p => p.familyName));

**SQL** 

	SELECT VALUE p.familyName
	FROM Families f
	JOIN p IN f.parents


**LINQ Lambda 運算式**

	input.SelectMany(family => 
	    family.children.Where(child => child.familyName == "Jeff"));
            
**SQL** 

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = "Jeff"



**LINQ Lambda 運算式**
            
	input.SelectMany(family => family.children.Where(
	    child => child.familyName == family.parents[0].familyName));

**SQL** 

	SELECT *
	FROM Families f
	JOIN c IN f.children
	WHERE c.familyName = f.parents[0].familyName


##執行查詢
DocumentDB 公開資源的方式是透過 REST API，此 API 可供任何能發出 HTTP/HTTPS 要求的語言呼叫。此外，DocumentDB 還為數種熱門語言 (例如 .NET、Node.js、JavaScript 和 Python) 提供程式設計程式庫。REST API 和各種程式庫都支援透過 SQL 進行查詢。.NET SDK 除了 SQL 之外，也支援 LINQ 查詢。

下列範例顯示如何建立查詢，並針對 DocumentDB 資料庫帳戶提交它。
###REST API
DocumentDB 提供透過 HTTP 的開放 RESTful 程式設計模型。可以使用 Azure 訂用帳戶佈建資料庫帳戶。DocumentDB 資源模型包含某個資料庫帳戶下的一組資源，而每個資源都可透過邏輯和穩定 URI 進行定址。在本文件中，一組資源稱為摘要。資料庫帳戶包含一組資料庫，而每個資料庫都包含多個集合，且各集合因此會包含文件、UDF 和其他資源類型。

與這些資源的基本互動模型是透過具有其標準解譯的 HTTP 動詞命令 GET、PUT、POST 和 DELETE。POST 動詞命令用於建立新的資源、執行預存程序或發出 DocumentDB 查詢。查詢一律是唯讀作業，而且沒有任何副作用。

下列範例示範針對集合進行之 DocumentDB 查詢的 POST，此集合包含我們到目前為止檢閱過的兩個範例文件。查詢具有 JSON 名稱屬性的簡單篩選。請注意 `x-ms-documentdb-isquery`和內容類型的使用： `application/query+json` 標頭表示該作業是一項查詢。


**要求**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
	

**結果**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
	x-ms-item-count: 1
	x-ms-request-charge: 0.32
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "id":"AndersenFamily",
	         "lastName":"Andersen",
	         "parents":[  
	            {  
	               "firstName":"Thomas"
	            },
	            {  
	               "firstName":"Mary Kay"
	            }
	         ],
	         "children":[  
	            {  
	               "firstName":"Henriette Thaulow",
	               "gender":"female",
	               "grade":5,
	               "pets":[  
	                  {  
	                     "givenName":"Fluffy"
	                  }
	               ]
	            }
	         ],
	         "address":{  
	            "state":"WA",
	            "county":"King",
	            "city":"seattle"
	         },
	         "_rid":"u1NXANcKogEcAAAAAAAAAA==",
	         "_ts":1407691744,
	         "_self":"dbs/u1NXAA==/colls/u1NXANcKogE=/docs/u1NXANcKogEcAAAAAAAAAA==/",
	         "_etag":"00002b00-0000-0000-0000-53e7abe00000",
	         "_attachments":"_attachments/"
	      }
	   ],
	   "count":1
	}


第二個範例顯示透過聯結傳回多個結果的較複雜查詢。

**要求**

	POST https://<REST URI>/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/query+json
	
    {      
        "query": "SELECT 
				     f.id AS familyName, 
				     c.givenName AS childGivenName, 
				     c.firstName AS childFirstName, 
				     p.givenName AS petName 
				  FROM Families f 
				  JOIN c IN f.children 
				  JOIN p in c.pets",     
        "parameters": [] 
    }


**結果**

	HTTP/1.1 200 Ok
	x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
	x-ms-item-count: 1
	x-ms-request-charge: 7.84
	
	<indented for readability, results highlighted>
	
	{  
	   "_rid":"u1NXANcKogE=",
	   "Documents":[  
	      {  
	         "familyName":"AndersenFamily",
	         "childFirstName":"Henriette Thaulow",
	         "petName":"Fluffy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Goofy"
	      },
	      {  
	         "familyName":"WakefieldFamily",
	         "childGivenName":"Jesse",
	         "petName":"Shadow"
	      }
	   ],
	   "count":3
	}


如果查詢的結果無法放入結果的單一頁面內，則 REST API 會透過  `x-ms-continuation-token` 回應標頭傳回接續 Token。用戶端可以透過在後續結果中包括標頭，以將結果分頁。每頁的結果數目也可以透過 `x-ms-max-item-count` 數字標頭控制。

若要管理查詢的資料一致性原則，請使用 `x-ms-consistency-level` 標頭 (如所有 REST API 要求)。如需工作階段一致性，查詢要求中還必須回應最新的 `x-ms-session-token` Cookie 標題。請注意，所查詢集合的索引原則也可能會影響查詢結果的一致性。運用預設索引原則設定，集合的索引一律會具有最新文件內容，而且查詢結果會符合針對資料所選擇的一致性。如果編索引原則放寬為 Lazy，則查詢可能會傳回過時的結果。如需詳細資訊，請參閱 [DocumentDB 一致性層級][consistency-levels]。

如果集合上所設定的索引原則無法支援指定的查詢，則 DocumentDB 伺服器會傳回 400「不正確的要求」。針對範圍查詢 (針對雜湊 (相等) 查閱所設定的路徑) 以及明確地排除不進行編製索引的路徑，會傳回此訊息。 `x-ms-documentdb-query-enable-scan` 標頭可以指定成允許查詢在無法使用索引時執行掃描。

###C# (.NET) SDK
.NET SDK 支援 LINQ 和 SQL 查詢。下列範例顯示如何執行稍早在本文件中介紹的簡單篩選查詢。


	foreach (var family in client.CreateDocumentQuery(collectionLink, 
	    "SELECT * FROM Families f WHERE f.id = "AndersenFamily""))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

	foreach (var family in (
	    from f in client.CreateDocumentQuery(collectionLink)
	    where f.Id == "AndersenFamily"
	    select f))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in client.CreateDocumentQuery(collectionLink)
	    .Where(f => f.Id == "AndersenFamily")
	    .Select(f => f))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


此範例會比較每個文件內的兩個屬性是否相等，以及使用匿名投射。 


	foreach (var family in client.CreateDocumentQuery(collectionLink,
	    @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
	    FROM Families f 
	    WHERE f.address.city = f.address.state"))
	{
	    Console.WriteLine("\tRead {0} from SQL", family);
	}
	
	foreach (var family in (
	    from f in client.CreateDocumentQuery<Family>(collectionLink)
	    where f.address.city == f.address.state
	    select new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ query", family);
	}
	
	foreach (var family in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .Where(f => f.address.city == f.address.state)
	    .Select(f => new { Name = f.Id, City = f.address.city }))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", family);
	}


下一個範例顯示聯結 (透過 LINQ SelectMany 表示)。


	foreach (var pet in client.CreateDocumentQuery(collectionLink,
	      @"SELECT p
	        FROM Families f 
	             JOIN c IN f.children 
	             JOIN p in c.pets 
	        WHERE p.givenName = ""Shadow"""))
	{
	    Console.WriteLine("\tRead {0} from SQL", pet);
	}
	
	// Equivalent in Lambda expressions
	foreach (var pet in
	    client.CreateDocumentQuery<Family>(collectionLink)
	    .SelectMany(f => f.children)
	    .SelectMany(c => c.pets)
	    .Where(p => p.givenName == "Shadow"))
	{
	    Console.WriteLine("\tRead {0} from LINQ lambda", pet);
	}



.NET 用戶端會在 foreach 區塊中自動逐一查看查詢結果的所有頁面 (如上所示)。.NET SDK 中也提供 REST API 小節所介紹的查詢選項，方法是在 CreateDocumentQuery 方法中使用 `FeedOptions` 和 `FeedResponse` 類別。頁數可以使用 `MaxItemCount` 設定進行控制。 

開發人員可以使用 `IQueryable`物件建立 `IDocumentQueryable`，然後讀取 `ResponseContinuationToken` 值，並將它們做為 `RequestContinuationToken`傳遞回 `FeedOptions`中，即可明確地控制分頁。當設定的索引原則無法支援查詢時， `EnableScanInQuery` 可以設定為啟用掃描。

如需更多包含查詢的範例，請參閱 [DocumentDB .NET 範例](http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content)。 

###JavaScript 伺服器端 API 
DocumentDB 提供一個程式設計模型，以使用預存程序和觸發程序，直接對集合執行 JavaScript 型應用程式邏輯。在集合層級註冊的 JavaScript 邏輯接著可以對給定集合之文件的作業發出資料庫作業。這些作業會包裝在環境 ACID 交易中。

下列範例示範如何在 JavaScript 伺服器 API 中使用 queryDocuments，從預存程序和觸發程序內發出查詢。


	function businessLogic(name, author) {
	    var context = getContext();
	    var collectionManager = context.getCollection();
	    var collectionLink = collectionManager.getSelfLink()
	
	    // create a new document.
	    collectionManager.createDocument(collectionLink,
	        { name: name, author: author },
	        function (err, documentCreated) {
	            if (err) throw new Error(err.message);
	
	            // filter documents by author
	            var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
	            collectionManager.queryDocuments(collectionLink,
	                filterQuery,
	                function (err, matchingDocuments) {
	                    if (err) throw new Error(err.message);
	context.getResponse().setBody(matchingDocuments.length);
	
	                    // Replace the author name for all documents that satisfied the query.
	                    for (var i = 0; i < matchingDocuments.length; i++) {
	                        matchingDocuments[i].author = "George R. R. Martin";
	                        // we don't need to execute a callback because they are in parallel
	                        collectionManager.replaceDocument(matchingDocuments[i]._self,
	                            matchingDocuments[i]);
	                    }
	                })
	        });
	}


##參考
1.	[Azure DocumentDB 簡介][introduction]
2.	[DocumentDB SQL 語言規格](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.	[DocumentDB .NET 範例](http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content)
4.	[DocumentDB 一致性層級][consistency-levels]
5.	ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.	JSON [http://json.org/](http://json.org/)
7.	Javascript 規格 [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.	LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.	大型資料庫的查詢評估技術 [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611) (英文)
10.	平行關聯式資料庫系統中的查詢處理 (IEEE Computer Society Press，1994 年)
11.	Lu, Ooi, Tan, 平行關聯式資料庫系統中的查詢處理 (IEEE Computer Society Press，1994 年)。
12.	Christopher Olston、Benjamin Reed、Utkarsh Srivastava、Ravi Kumar、Andrew Tomkins：Pig Latin：資料處理的 Not-So-Foreign 語言，SIGMOD 2008。
13.     G. Graefe.The Cascades framework for query optimization.IEEE Data Eng. Bull., 18(3):1995.


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md

<!--HONumber=49--> 