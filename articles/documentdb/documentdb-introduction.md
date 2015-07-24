<properties 
	pageTitle="Microsoft Azure DocumentDB 簡介 | Azure" 
	description="了解 Azure DocumentDB (NoSQL 文件資料庫) 及其對於雲端和行動應用程式的價值。同時也了解它如何管理資料及如何用於應用程式開發。" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="mimig"/>

#Microsoft Azure DocumentDB 簡介

本文針對開發人員、IT 專業人員和業務決策者，提供 Microsoft Azure DocumentDB (NoSQL 文件資料庫服務) 簡介。 

我們建議從觀看下列影片 (由 Ryan CrawCour 和 Scott Hanselman 介紹 Azure DocumentDB) 並試試[查詢遊樂場](http://www.documentdb.com/sql/demo) (您可以在其中試用 DocumentDB) 開始，並對我們的資料集執行 SQL 查詢。

> [AZURE.VIDEO documentdb-101-with-ryan-crawcour]

然後，回到這篇文章，您將了解下列問題的答案：  

-	[什麼是 DocumentDB，它為雲端和行動應用程式提供什麼價值？](#what-is-docdb)
-	[在 DocumentDB 中是如何管理資料，以及要如何存取它？](#data-management)
-	[如何使用 DocumentDB 來開發應用程式？](#develop)
-	[建置 DocumentDB 應用程式的後續步驟是什麼？](#next-steps)  

##<a name="what-is-docdb"></a>什麼是 Azure DocumentDB？  

新式應用程式會快速地產生、使用和回應非常大量的資料。不僅這些應用程式發展地極為快速，基礎資料結構描述也是如此。因此，開發人員越來越常選擇無結構描述的 NoSQL 資料庫，做為簡單、快速、彈性的方案來儲存和處理資料，同時還保有快速逐一查看應用程式資料模型和非結構化資料摘要的能力。不過，許多無結構描述資料庫都不允許非一般的查詢和交易式處理，以致於很難進行進階資料管理。這就 DocumentDB 的切入點。Microsoft 開發了 DocumentDB，以滿足在管理無結構描述資料時的這些需求。

DocumentDB 是針對新式行動和 Web 應用程式設計的 NoSQL 文件資料庫服務。DocumentDB 提供一樣快速的讀取和寫入、結構描述彈性，以及依需求輕鬆擴大和縮小資料庫的能力。DocumentDB 使用 SQL 方言來提供複雜的特定查詢、支援定義完善的一致性層級，以及使用預存程序、觸發程序和 UDF 的常見程式設計模型提供 JavaScript 語言整合式多文件交易處理。 

DocumentDB 原生支援 JSON 文件，能夠輕鬆地反覆運算應用程式的結構描述。它採用廣為使用的 JSON 和 JavaScript，以免應用程式類型與資料庫結構描述之間不相符。JavaScript 的深入整合也讓開發人員能夠在資料庫交易中，於資料庫引擎內既直接又有效率地執行應用程式邏輯。 

Azure DocumentDB 提供下列重要功能和優點：

-	**使用常見的 SQL 語法執行特定查詢：**將異質 JSON 文件儲存在 DocumentDB 中，然後透過常見的 SQL 語法查詢這些文件。DocumentDB 利用高度並行、無鎖定、記錄結構化索引技術，來自動為所有文件內容編制索引。這使得在不指定結構描述提示、次要索引或檢視表的情況下，即可執行豐富的即時查詢。

-	**在資料庫內執行 JavaScript：**使用標準 JavaScript 以預存程序、觸發程序及使用者定義函式 (UDF) 表示應用程式邏輯。這可讓您的應用程式邏輯在 JSON 資料上運作，而不需擔心應用程式與資料庫結構描述之間不相符。DocumentDB 提供直接在資料庫引擎內的 JavaScript 應用程式邏輯完整交易式執行。JavaScript 的深入整合可允許從 JavaScript 程式內，以獨立交易方式執行 INSERT、REPLACE、DELETE 及 SELECT 作業。 

-	**可微調的一致性層級：**從四個完善定義的一致性層級中選取，即可在一致性與效能之間做出最佳取捨。針對查詢和讀取操作，DocumentDB 提供四個不同的一致性層級：增強式、界限-陳舊、工作階段和最終。這些細微且定義完善的一致性層級可讓您在一致性、可用性及延遲之間做出最佳取捨。 

-	**完全受管理：**免除管理資料庫和電腦資源的需要。對於完全受管理的 Microsoft Azure 服務，您不需要管理虛擬機器、部署及設定軟體，或處理複雜的資料層升級。每個資料庫都會自動備份來防止區域性故障發生。您可以輕鬆地新增 DocumentDB 帳戶，並視需要佈建容量，讓您可以專注於您的應用程式，而不是操作和管理您的資料庫。

-	**可彈性調整的輸送量和儲存體：**可輕鬆地擴大或縮小 DocumentDB 規模來符合您的應用程式需求。調整時，是透過保留之 SSD 支援儲存體及輸送量的精細單位來調整。您可以隨著應用程式的成長購買更多容量單位，在可預測效能的情況下，彈性地調整 DocumentDB。 

-	**為開放而設計：**使用現有技能和工具快速入門。使用 DocumentDB 進行程式設計既簡單、可行，且不需要採用新工具或符合 JSON 或 JavaScript 的自訂延伸模組。您可以存取所有的資料庫功能，包括 CRUD、查詢和透過簡單的 RESTful HTTP 介面處理的 JavaScript。DocumentDB 融入現有的格式、語言和標準，同時提供最高價值的資料庫功能。

您可以使用 DocumentDB 來儲存需要查詢抓取和交易式處理的彈性資料集。應用程式案例可能包括互動式 Web 和行動應用程式的使用者資料，以及儲存、抓取和處理應用程式 JSON 資料。資料庫可以儲存任意數目的 JSON 文件，因為 DocumentDB 相當適用於在網際網路上依規模執行的應用程式。

##<a name="data-management"></a>Azure DocumentDB 資源
Azure DocumentDB 透過定義完善的資料庫資源管理資料。這些資源會進行複寫來達到高可用性，並且會透過其邏輯 URI 來進行唯一定址。DocumentDB 針對所有資源提供簡單的 HTTP 式 RESTful 程式設計模型。 

DocumentDB 資料庫帳戶是可讓您存取 Azure DocumentDB 的唯一命名空間。在建立資料庫帳戶之前，您必須先擁有 Azure 訂用帳戶，此帳戶可讓您存取各種 Azure 服務。 

DocumentDB 內的所有資源都會被建立模型，並儲存為 JSON 文件。管理資源時，是以項目 (含有中繼資料的 JSON 文件) 和摘要 (即項目集合) 的形式來管理。項目集包含在其各自的摘要內。

下圖顯示 DocumentDB 資源之間的關係：

![][1] 

資料庫帳戶是由一組資料庫所組成，每個資料庫都包含多個集合，而集合可包含預存程序、觸發程序、UDF、文件和相關附件。資料庫也有相關聯的使用者，每個使用者都有一組可存取其他各種集合、預存程序、觸發程序、UDF、文件或附件的權限。資料庫、使用者、權限和集合是系統所定義、具有已知結構描述的資源，而文件、預存程序、觸發程序、UDF 和附件則包含使用者定義的任意 JSON 內容。  

##<a name="develop"></a>針對 Azure DocumentDB 進行開發
Azure DocumentDB 公開資源的方式是透過 REST API，此 API 可供任何能發出 HTTP/HTTPS 要求的語言呼叫。另外，DocumentDB 還提供了幾種熱門語言的程式設計程式庫。這些程式庫可透過處理詳細資料 (例如位址快取、例外狀況管理、自動重試等) 來簡化使用 Azure DocumentDB 的各個層面。程式庫目前適用於下列語言和平台，以及正在研發的其他語言和平台：  

- [.NET](http://go.microsoft.com/fwlink/?LinkID=402989)  
-	[Node.js](http://go.microsoft.com/fwlink/?LinkID=402990)
-	[JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991)
-	[Python](http://go.microsoft.com/fwlink/?LinkID=402992)

除了基本的「建立」、「讀取」、「更新」和「刪除」作業之外，Azure DocumentDB 還提供可用來擷取 JSON 文件的豐富 SQL 查詢介面，並在伺服器端支援以交易方式執行 JavaScript 應用程式邏輯。查詢和指令碼執行介面可以透過所有平台程式庫以及 REST API 來取得。 

###SQL 查詢
Azure DocumentDB 支援使用根植於 JavaScript 類型系統的 SQL 語言及支援豐富階層式查詢的運算式來查詢文件。DocumentDB 查詢語言是可用來查詢 JSON 文件的簡單卻功能強大的介面。此語言支援 ANSI SQL 文法的子集，並新增 JavaScript 物件、陣列、物件建構和函式叫用的深入整合。DocumentDB 提供其查詢模型，而沒有來自開發人員的任何明確結構描述或編製索引提示。

使用者定義函式 (UDF) 可以向 DocumentDB 進行註冊，然後在 SQL 查詢中供參考，進而延伸文法來支援自訂應用程式邏輯。這些 UDF 是以 JavaScript 程式的形式撰寫，並在資料庫內執行。 

針對 .NET 開發人員，DocumentDB 也提供 LINQ 查詢提供者做為 .NET SDK 的一部分。 

###交易和 JavaScript 執行
DocumentDB 可讓您將應用程式邏輯撰寫成完全以 JavaScript 撰寫的具名程式。這些程式會針對集合進行註冊，而可對指定之集合內的文件進行資料庫操作。JavaScript 可以註冊成觸發程序、預存程序或使用者定義函式 (UDF) 來供執行。觸發程序和預存程序可以建立、讀取、更新和刪除文件，而使用者定義函式則可在查詢執行邏輯中執行，而不需要對集合進行寫入存取。

在 DocumentDB 內執行 JavaScript 的作法是仿造自關聯式資料庫系統所支援的概念，以 JavaScript 做為 T-SQL 的新式取代項目。所有 JavaScript 邏輯都是以隔離的快照在環境 ACID 交易內執行。在執行期間，如果 JavaScript 擲回例外狀況，則會中止整個交易。

##<a name="next-steps"></a>後續步驟
若要開始使用 Azure DocumentDB，請探索以下資源：

-   [立即試用 DocumentDB](https://portal.azure.com/#gallery/Microsoft.DocumentDB)
-   [查詢遊樂場](http://www.documentdb.com/sql/demo)
-	[DocumentDB 資源模型和概念](documentdb-resources.md)
-	[使用 DocumentDB 資源進行互動](documentdb-interactions-with-resources.md)
-	[建立 DocumentDB 資料庫帳戶](documentdb-create-account.md)
-	[開始使用 DocumentDB .NET SDK](documentdb-get-started.md)

[1]: ./media/documentdb-introduction/intro.png

<!--HONumber=49--> 