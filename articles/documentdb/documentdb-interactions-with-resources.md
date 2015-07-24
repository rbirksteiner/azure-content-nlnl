<properties 
	pageTitle="與 DocumentDB 資源進行 RESTful 互動 | Azure" 
	description="了解如何使用 HTTP 動詞命令來執行與 Microsoft Azure DocumentDB 資源之間的 RESTful 互動。" 
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
	ms.date="02/03/2015" 
	ms.author="mimig"/>

#與 DocumentDB 資源進行 RESTful 互動 

DocumentDB 支援使用 HTTP 方法來建立、讀取、取代、取得和刪除 DocumentDB 資源。

閱讀本文後，您將能夠回答下列問題：

- 標準 HTTP 方法如何與 DocumentDB 資源搭配使用？
- 如何使用 POST 建立新的資源？
- 如何使用 POST 註冊預存程序？
- DocumentDB 如何支援貨幣控制？
- HTTPS 和 TCP 的連接選項有哪些？

##HTTP 動詞命令的概觀
DocumentDB 資源可支援下列 HTTP 動詞命令及其標準解釋：

1.	POST 方法會建立新的項目資源 
2.	GET 方法會讀取現有項目或摘要資源 
3.	PUT 方法會取代現有項目資源 
4.	DELETE 方法會刪除現有項目資源
5.	HEAD 方法會為 sans 取得 (GET) 回應裝載 (即只有標頭) 

>[AZURE.NOTE] 日後，我們打算透過 PATCH 新增對於選擇性更新的支援。  

如下圖所示，POST 只能對摘要資源發出；PUT、DELETE 只能對項目資源發出；GET 和 HEAD 能對摘要或項目資源發出。 

![][1]  

**使用標準 HTTP 動詞命令的互動模型**

##使用 POST 建立新的資源 
為了更加了解互動模型，我們來看一下建立新資源 (也稱為 INSERT) 的情況。為了建立新資源，您必須發出 HTTP POST 要求，而且此要求的本文內要包含以資源所屬容器摘要的 URI 表示資源的程式碼。此要求唯一必要的屬性是資源 id。  

舉例來說，為了建立新資料庫，您需要對 /dbs 發佈 (POST) 資料庫資源 (透過設定具有唯一名稱的 id 屬性)，同樣地，為了建立新集合，您需要對 /dbs/_rid/colls/ 發佈 (POST) 集合資源，以此類推。回應將會包含具有系統所產生屬性 (包括可供導覽到其他資源的 _self 資源連結) 的完全認可資源。以下是簡單的 HTTP 互動模型範例，用戶端可以發出 HTTP 要求，以在帳戶內建立新資料庫。  

	POST http://fabrikam.documents.azure.net/dbs
	{
	      "id":"MyDb"
	}

DocumentDB 服務的回應是產生成功回應和狀態碼，指出已成功建立資料庫。  

	[201 Created]
	{
	      "id": "MyDb",
	      "_rid": "UoEi5w==",
	      "_self": "dbs/MyDb/",
	      "_ts": 1407370063,
	      "_etag": ""00002100-0000-0000-0000-50e71fda0000"",
	      "_colls": "colls/",
	      "_users": "users/"
	}
  
##使用 POST 註冊預存程序
再來看另一個建立和執行資源的範例，下列預存程序完全是以 JavaScript 撰寫。   

	function sproc(docToCreate, addedPropertyName, addedPropertyValue) {
	    var collectionManager = getContext().getCollection();
	    collectionManager.createDocument(collectionManager.getSelfLink(), docToCreate,   
	    function(err, docCreated) {
	        if(err) throw new Error('Error while creating document: ' + err.message);
	        
	        docCreated.addedPropertyName = addedPropertyValue;
	        getContext().getResponse().setBody(docCreated);
	    });
	}

對 /dbs/_rid-db/colls/_rid-coll/sprocs 發出 POST，即可向 MyDb 下轄的集合註冊預存程序。 

	POST /dbs/MyDb/colls/MyColl/sprocs HTTP/1.1
	
	{
	  "id": "sproc1",
	  "body": "function (docToCreate, addedPropertyName, addedPropertyValue) {
	                var collectionManager = getContext().getCollection();
	                collectionManager.createDocument(collectionManager.getSelfLink(), 
	                            docToCreate, function(err, docCreated) {
	                    if(err) throw new Error('Error while creating document: ' + 
	                                                     err.message);
	                    
	                    docCreated.addedPropertyName = addedPropertyValue;
	                    getContext().getResponse().setBody(docCreated);
	                });
	            }"
	}
DocumentDB 服務的回應是產生成功回應和狀態碼，指出已成功註冊預存程序。  

	[201 Created]
	{
	      "id": "sproc1",
	      "_rid": "EoEi5w==",
	      "_self": "dbs/MyDb/colls/MyColl/sprocs/sproc1",
	      "_etag": ""00002100-0000-0000-0000-50f71fda0000"",
	       ...
	}

##使用 POST 執行預存程序
最後，為了執行上述範例的預存程序，您需要對預存程序資源的 URI (/dbs/_rid-db/colls/_rid-coll/sprocs/sproc1) 發出 POST。下列程式碼就將此說明。  

	POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
	 [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]
 

DocumentDB 服務將做出下列回應。  

	HTTP/1.1 200 OK
	 { 
	  "id": "TestDocument",  
	  "_rid": "ZTlcANiwqwIBAAAAAAAAAA==",
	  "_ts": 1407370063,
	  "_self": "dbs/ZTlcAA==/colls/ZTlcANiwqwI=/docs/ZTlcANiwqwIBAAAAAAAAAA==/",
	  "_etag": "00000900-0000-0000-0000-53e2c34f0000",
	  "_attachments": "attachments/",
	  "book": "Autumn of the Patriarch", 
	  "price": 200
	}

請注意，POST 動詞命令可用於建立新資源、執行預存程序和發出 SQL 查詢。為了說明 SQL 查詢如何執行，請看下列說明。  

	POST /dbs/MyDb/colls/MyColl/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/sql
	
	SELECT * FROM root.children

服務會回應 SQL 查詢的結果。   

	HTTP/1.1 200 Ok
	x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
	x-ms-item-count: 2
	x-ms-session-token: 81
	x-ms-request-charge: 1.43
	Content-Length: 287
	
	{"_rid":"sehcAIE2Qy4=","Documents":[[{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}],[{"familyName":"Merriam","givenName":"Jesse","gender":"female","grade":1},{"familyName":"Miller","givenName":"Lisa","gender":"female","grade":8}]],"count":2}


##使用 PUT、GET、和 DELETE
取代或讀取資源分別等同於對資源的 _self 連結發出 PUT (具有有效要求本文) 和 GET 動詞命令。同樣地，刪除資源等同於對資源的 _self 連結發出 DELETE 動詞命令。必須指出的一點是，要想在 DocumentDB 的資源模型中以階層方式組織資源，就必須支援串聯刪除功能，以便只要刪除擁有者的資源就能刪除相依資源。相依資源可能會散佈在擁有者資源以外的其他節點，因此，刪除作業可能進展緩慢。不論記憶體回收的機制為何，資源一經刪除，就會立即釋出配額供您使用。請注意，系統會保留參考完整性。例如，您不可以將集合插入至已刪除的資料庫，或者取代或查詢不再存在之集合的文件。  
 
對資源的 feed 發出 GET 或是查詢集合都可能會導致產生數百萬個項目，因此這兩部伺服器根本不可能有辦法顯示這些項目，用戶端也不可能在一次往返/要求和回應交換內就完全取用這些項目。為了解決此問題，DocumentDB 允許用戶端以一次一頁的方式對大型摘要進行分頁處理。用戶端可以使用 [x-ms-continuationToken] 回應標頭做為資料指標，以瀏覽至下一頁。   

##開放式並行存取控制
大部分的 Web 應用程式都依賴以實體標記為基礎的開放式並行存取控制，以避免令人厭煩的「遺失更新」和「遺失刪除」問題。實體標記是支援 HTTP 且與資源相關聯的邏輯時間戳記。DocumentDB 透過確定每個 HTTP 回應都 (持久) 包含與特定資源相關聯的版本，原生支援開放式並行存取控制。在下列情況時，可正確偵測到並行存取控制衝突：  

1.	如果兩個用戶端同時以最新版本的資源 (透過 [if-match] 要求標頭來指定) 對資源發出變更要求 (透過 PUT/ DELETE 動詞命令)，則 DocumentDB 資料庫引擎會讓它們遵循交易式並行存取控制。
2.	如果用戶端提出舊版資源 (透過 [if-match] 要求標頭來指定)，則會拒絕其要求。  

##連線選項
在 DocumentDB 所公開的邏輯定址模型中，每個資源都具有可透過其 _self 連結來加以識別的邏輯和穩定 URI。做為遍布多個區域的分散式儲存體系統，DocumentDB 中的各種資料庫帳戶所下轄的資源會分割給多部電腦，並且會複寫每個資料分割以提供高可用性。管理給定資料分割資源的複本會註冊實體位址。雖然實體位址在一段時間後就會因為失敗而有所變更，但是其邏輯位址仍會維持不變。同時以資源形式在內部提供使用的路由表，會保存將邏輯位址轉譯為實體位址的方法。DocumentDB 公開兩種連線模式：  

1.	**閘道模式：**在從邏輯至實體位址的轉換或路由詳細資料中，會避免轉譯用戶端；僅處理邏輯 URI，並安穩地瀏覽資源模型。用戶端使用邏輯 URI 發出要求，而且 Edge 機器將邏輯 URI 轉譯為管理資源並轉送要求的複本的實體位址。利用 Edge 機器快取 (和定期重新整理) 路由表，路由極有效率。 
2.	**直接連線模式：**用戶端直接在其處理空間中管理路由表和定期重新整理。用戶端可以直接與複本連線，並略過 Edge 機器。   


<table width="300">
    <tbody>
        <tr>
            <td width="120" valign="top">
                <p>
                    <strong>連線模式</strong>
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    <strong>通訊協定</strong>
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    <strong>詳細資料</strong>
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    <strong>DocumentDB SDK</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    閘道
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    應用程式使用邏輯 URI 直接與 Edge 節點連線。這會發生額外的網路躍點。
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    REST API
                </p>
                <p>
                    .NET、JavaScript、Node.js、Python
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    直接連線
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS 和
                </p>
                <p>
                    TCP
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    應用程式可以直接存取路由表，並執行用戶端路由直接與複本連線。
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    .NET
                </p>
            </td>
        </tr>
    </tbody>
</table>

##後續步驟
瀏覽 [Azure DocumentDB REST API 參考](https://msdn.microsoft.com/library/azure/dn781481.aspx)以深入了解使用 REST API 運用資源。

##參考
-   [Azure DocumentDB REST API 參考](https://msdn.microsoft.com/library/azure/dn781481.aspx) 
-	REST [http://en.wikipedia.org/wiki/Representational_state_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
-	JSON 規格  [http://www.ietf.org/rfc/rfc4627.txt](http://www.ietf.org/rfc/rfc4627.txt)
-	HTTP 規格[http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
-	實體標籤[http://en.wikipedia.org/wiki/HTTP_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)
-	[查詢 DocumentDB](documentdb-sql-query.md)
-	[DocumentDB SQL 參考](https://msdn.microsoft.com/library/azure/dn782250.aspx)
-	[DocumentDB 程式設計：預存程序、觸發程序和 UDF](documentdb-programming.md)
-	[DocumentDB 參考文件](https://msdn.microsoft.com/library/azure/dn781482.aspx)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png

<!--HONumber=49--> 