<properties 
	pageTitle="媒體服務 REST API 概觀 - Azure" 
	description="媒體服務 REST API 概觀" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="juliako"/>


# 媒體服務 REST API 概觀 

Microsoft Azure 媒體服務會接受以 OData 為基礎的 HTTP 要求，而且可以使用詳細資訊 JSON 或 atom+pub 回應。由於媒體服務符合 Azure 設計指導方針，因此在連線到媒體服務時，每個用戶端都必須使用一組必要的 HTTP 標頭，以及一組可以使用的選擇性標頭。下列章節描述建立要求及接收來自媒體服務的回應時可以使用的標頭和 HTTP 指令動詞。


## 媒體服務支援的標準 HTTP 要求標頭

您對媒體服務每次呼叫，有一組必須在要求中包含的必要標頭，以及一組可能會想要包含的選擇性標頭。下表列出必要標頭：


<table border="1"> <tr><th>標頭</th><th>類型</th><th>值</th></tr> <tr><td>授權</td><td>Bearer</td><td>Bearer 是唯一接受的授權機制。此值也必須包含 ACS 所提供的存取權杖。</td></tr> <tr><td>x-ms-version</td><td>十進位</td><td>2.11</td></tr> <tr><td>DataServiceVersion</td><td>十進位</td><td>3.0</td></tr> <tr><td>MaxDataServiceVersion</td><td>十進位</td><td>3.0</td></tr> </table><br/>


>[AZURE.NOTE]媒體服務使用 OData 來透過 REST API 公開其基礎資產中繼資料儲存機制，因此 DataServiceVersion 和 MaxDataServiceVersion 標頭應該包含在任何要求中。不過，如果沒有，則目前媒體服務會假設使用的 DataServiceVersion 值是 3.0。

以下是一組選擇性標頭：

<table border="1"> <tr><th>標頭</th><th>類型</th><th>值</th></tr> <tr><td>Date</td><td>RFC 1123 日期</td><td>要求的時間戳記</td></tr> <tr><td>接受</td><td>內容類型</td><td>如下所示的回應要求內容類型：<ul><li>application/json;odata=verbose</li><li>application/atom+xml</li></ul></br>回應可能會有不同的內容類型，例如 Blob 擷取，成功的回應會在其中包含 Blob 資料流做為裝載。</td></tr> <tr><td>Accept-Encoding</td><td>Gzip、deflate</td><td>GZIP 和 DEFLATE 編碼 (適用時)。注意：若是大型資源，媒體服務可能會忽略此標頭，並傳回未壓縮的資料。</td></tr> <tr><td>Accept-Language</td><td>"en"、"es" 等等。</td><td>指定回應的慣用語言。</td></tr> <tr><td>Accept-Charset</td><td>字元集類型，如 "UTF-8"</td><td>預設值為 UTF-8。</td></tr> <tr><td>X-HTTP-Method</td><td>HTTP 方法</td><td>可讓不支援 PUT 或 DELETE 等 HTTP 方法的用戶端或防火牆，透過 GET 呼叫來經由通道使用這些方法。</td></tr> <tr><td>Content-Type</td><td>內容類型</td><td>PUT 或 POST 要求中的要求主體內容類型。</td></tr> <tr><td>client-request-id</td><td>字串</td><td>呼叫者定義的值，可識別指定的要求。如果指定，回應訊息中將包含此值以做為對應要求的方式。<br/><br/> <b>重要</b><br/>值的上限應該為 2096b (2k)。</td></tr> </table><br/>


## 媒體服務支援的標準 HTTP 回應標頭

以下是一組可能會根據您所要求的資源，以及您要執行的動作而傳回給您的標頭。


<table border="1"> <tr><th>標頭</th><th>類型</th><th>值</th></tr> <tr><td>request-id</td><td>字串</td><td>目前作業的唯一識別碼，由服務產生。</td></tr> <tr><td>client-request-id</td><td>字串</td><td>在原始要求中，呼叫者所指定的識別碼 (如果有的話)。</td></tr> <tr><td>Date</td><td>RFC 1123 日期</td><td>處理要求的日期。</td></tr> <tr><td>Content-Type</td><td>變數</td><td>回應主體的內容類型。</td></tr> <tr><td>Content-Encoding</td><td>變數</td><td>Gzip 或 deflate (視情況)。</td></tr> </table><br/>

## 媒體服務支援的標準 HTTP 指令動詞

以下是進行 HTTP 要求時，可以使用的 HTTP 指令動詞完整清單：


<table border="1"> <tr><th>動詞</th><th>說明</th></tr> <tr><td>GET</td><td>傳回物件的目前值。</td></tr> <tr><td>POST</td><td>根據提供的資料建立物件或提交命令。</td></tr> <tr><td>PUT</td><td>取代物件，或建立具名物件 (如果適用)。</td></tr> <tr><td>DELETE</td><td>刪除物件。</td></tr> <tr><td>MERGE</td><td>以具名屬性變更來更新現有的物件。</td></tr> <tr><td>HEAD</td><td>傳回 GET 回應的物件中繼資料。</td></tr> </table><br/>

## 探索媒體服務模型

若要讓使用者更容易找到媒體服務實體，可以使用 $metadata 作業。它可讓您擷取所有有效的實體類型、實體屬性、關聯、函式、動作等等。下列範例示範如何建構 URI：https://media.windows.net/API/$metadata。

如果您想要在瀏覽器檢視中繼資料，或是未在要求中包含 x-ms-version 標頭，您應該將 "?api-version=2.x" 附加到 URI 的結尾。


<!-- Anchors. -->


<!-- URLs. -->
  [Management Portal]: http://manage.windowsazure.com/



 

<!---HONumber=July15_HO1-->