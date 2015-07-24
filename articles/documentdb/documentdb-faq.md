<properties 
	pageTitle="DocumentDB 常見問題集 | Azure" 
	description="關於 Azure DocumentDB nosql 文件資料庫服務常見問題的解答。了解容量及要求單位，並了解如何按照應用程式的需求進行調整。" 
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


#DocumentDB 常見問題集

## <a id="fundamentals"></a> Microsoft Azure DocumentDB 基本概念

###什麼是 Microsoft Azure DocumentDB？ 
Microsoft Azure DocumentDB 是一種極具擴充性的 NoSQL 文件「資料庫即服務」，可在無結構描述的資料上進行豐富的查詢，協助提供可設定和可靠的效能，且支援快速開發，這些完全都是透過受管理的平台，背後有 Microsoft Azure 強大的功能與先進的技術做後盾。如果關鍵要求在於可預測的輸送量、低遲性和無結構描述的資料模型，則 DocumentDB 就是 Web 和行動應用程式最適當的解決方案。DocumentDB 透過原生 JSON 資料模型，提供結構描述的彈性和豐富的檢索能力，且包含多文件交易式支援與整合式 JavaScript。  
  
如需有關部署和使用此服務的指示，請參閱 [DocumentDB 文件頁面](http://go.microsoft.com/fwlink/p/?LinkID=402319)。

###DocumentDB 是何種資料庫？
DocumentDB 是 NoSQL 文件導向的資料庫，以 JSON 格式儲存資料。DocumentDB 支援巢狀、獨立式資料結構，可透過豐富的 DocumentDB SQL 查詢文法來查詢。DocumentDB 透過預存程序、觸發程序和使用者定義函數，提供伺服器端 JavaScript 高效能的交易式處理。資料庫也支援可由開發人員調整的一致性層級與相關聯的效能層級。
 
###DocumentDB 資料庫有類似 RDBMS 的資料表嗎？
否，DocumentDB 將資料儲存在 JSON 文件集合中。如需有關 DocumentDB 資源的詳細資訊，請參閱 [DocumentDB 資源模型和概念](documentdb-resources.md)一文。 

###DocumentDB 資料庫支援無結構描述的資料嗎？
是，DocumentDB 可讓應用程式儲存任意的 JSON 文件，而不需要結構描述定義或提示。透過 DocumentDB SQL 查詢介面就可立即查詢資料。   

###DocumentDB 支援 ACID 交易嗎？
是，DocumentDB 支援以 JavaScript 預存程序和觸發程序表達的跨文件交易。交易以單一集合為範圍，且以 ACID 語意執行，也就是全有或全無，與其他並行執行的程式碼和使用者要求隔離。如果伺服器端執行 JavaScript 應用程式碼期間擲回例外狀況，則會回復整個交易。 

###DocumentDB 有哪些一般使用案例？  
對於新的 Web 和行動應用程式，規模、效能和可查詢無結構描述的資料都很重要，DocumentDB 是不錯的選擇。DocumentDB 有助於快速開發，且支援應用程式資料模型的持續反覆運算。管理使用者產生之內容和資料的應用程式，就是 DocumentDB 的常見使用案例。  

###規模和容量有何限制？
每一個 Azure DocumentDB 帳戶都支援容量單位數上限，這可透過 Azure 入口網站來設定。如果您需要更多容量單位，請聯絡支援人員來增加帳戶配額。如需有關資源配額的詳細資訊，請參閱[預覽版本的 DocumentDB 限制](documentdb-limits.md)。


###Microsoft Azure DocumentDB 的費用是多少？
請參閱 [DocumentDB 定價詳細資料](http://go.microsoft.com/fwlink/p/?LinkID=402317)一文，以取得詳細資料。

## <a id="setup"></a> 設定 Microsoft Azure DocumentDB

###我如何註冊 Microsoft Azure DocumentDB？
新的 [Azure 預覽入口網站][preview-portal]中已提供 Microsoft Azure DocumentDB (預覽)。首先，您必須註冊 Microsoft Azure 訂閱。註冊 Microsoft Azure 訂用帳戶之後，您可以透過服務商場將 DocumentDB 帳戶加入至 Azure 訂閱。   

###什麼是主要金鑰？
主要金鑰是可存取帳戶的所有資源的安全性權杖。擁有此金鑰的人，對資料庫帳戶中的所有資源具有讀取和寫入存取權。分配主要金鑰時，務必謹慎。[Azure 預覽入口網站][preview-portal]的 [金鑰] 刀鋒視窗提供主要金鑰和次要金鑰。。

###我如何建立資料庫？
您可以使用其中一套 DocumentDB SDK 或透過 REST API 來建立資料庫。如需有關如何開發應用程式的資訊，請參閱 [DocumentDB 文件頁面](http://go.microsoft.com/fwlink/p/?LinkID=402319)的＜開發＞一節。 

###什麼是集合？
集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。查詢和交易的範圍設為集合。您可以將一組異質 JSON 文件儲存在單一集合內，全部會自動編製索引。   

###資料庫和集合有任何限制嗎？
購買的每一個容量單位都提供一個額度的資料庫儲存體和佈建的輸送量。服務所管理的每一項資源也都有各自的配額。如需詳細資訊，請參閱[預覽版本的 DocumentDB 限制](documentdb-limits.md)。  

###我如何設定使用者和權限？
您可以使用其中一套 DocumentDB SDK 或透過 REST API 來建立使用者和權限。如需有關如何開發應用程式的詳細資訊，請參閱 [DocumentDB 文件頁面](http://go.microsoft.com/fwlink/p/?LinkID=402319)的＜開發＞一節。  


## <a id="develop"></a>針對 Microsoft Azure DocumentDB 進行開發

###如何開始針對 DocumentDB 進行開發？
在預覽版本中，有 .NET、Python、Node.js 和 JavaScript 的 SDK 可用。開發人員也可以利用 RESTful HTTP API，從各種平台和語言來與 DocumentDB 資源互動。如需有關如何使用這些 SDK 的詳細資訊，請參閱 [DocumentDB 文件頁面](http://go.microsoft.com/fwlink/p/?LinkID=402319)的＜開發＞一節。

###DocumentDB 支援 SQL 嗎？
DocumentDB 的 SQL 查詢語言提供豐富的階層式和關聯式運算子，且透過 JavaScript 型使用者定義函數 (UDF) 支援擴充性。JSON 文法允許將 JSON 文件模型化為樹狀目錄，並以標籤當作樹狀節點 - 這是由 DocumentDB 的自動編製索引技術及 DocumentDB 的 SQL 查詢方言所使用。如需有關如何使用 SQL 文法的詳細資料，請參閱[使用 DocumentDB SQL 查詢][query]一文。

###DocumentDB 支援什麼資料類型？
DocumentDB 支援的基本資料類型與 JSON 相同。JSON 有一套簡單的類型系統，包含字串、數值 (IEEE754 雙精度)、布林值 - true 和 false 及 Null。透過使用 { } 運算子建立巢狀物件和使用 [ ] 運算子建立陣列，可以在 JSON 和 DocumentDB 中表示更複雜的資料類型 (例如 DateTime、Guid、Int64 和 Geometry)。 

###DocumentDB 如何提供並行存取？
DocumentDB 透過 HTTP 實體標記或 ETag，支援開放式並行存取控制 (OCC)。每一個 DocumentDB 資源都有一個 ETag，DocumentDB 用戶端會將最新讀取的版本包含在寫入要求中。如果 ETag 是最新，表示已認可變更。如果值已從外部變更，則伺服器會拒絕寫入，並傳回「HTTP 412 預先指定的條件失敗」回應碼。用戶端必須讀取最新版的資源，再重試要求。 

###我如何在 DocumentDB 中執行交易？
DocumentDB 透過 JavaScript 預存程序和觸發程序，支援語言整合式交易。指令碼內的所有資料庫操作都是在以集合為範圍的快照隔離之下執行。文件版本 (ETag) 的快照是在交易開始時取得，且只有當指令碼成功執行時才會認可。如果 JavaScript 擲回錯誤，則會回復交易。

###我如何將大量文件插入至 DocumentDB？ 
DocumentDB 支援預存程序，可提供有效率的方式執行批次插入。您可以開發簡單的 JavaScript 預存程序來接受並插入文件，以執行大量插入。額外的優點就是會以交易來執行大量插入，讓集合保持一致的狀態。如需有關程式設計模型的詳細資料，請參閱 [DocumentDB 文件頁面](http://go.microsoft.com/fwlink/p/?LinkID=402319)上的＜開發＞一節。

###DocumentDB 支援資源連結快取嗎？
是，因為 DocumentDB 是 RESTful 服務，資源連結是固定不變且可快取。DocumentDB 用戶端可以指定 "If-None-Match" 標頭來讀取任何資源，例如文件或集合，且只有當伺服器上的版本變更時才會更新本機複本。 




[preview-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md

<!--HONumber=49--> 