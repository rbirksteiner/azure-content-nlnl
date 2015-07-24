<properties 
	pageTitle="如何建立媒體處理器 - Azure" 
	description="了解如何建立媒體處理器元件，為 Azure 媒體服務的媒體內容進行編碼、格式轉換、加密或解密。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2015" 
	ms.author="juliako"/>


#如何：取得媒體處理器執行個體

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)系列的一部分。


##概觀

在媒體服務中，媒體處理器是可處理特定處理工作的元件，例如編碼、格式轉換、加密或解密媒體內容。您通常會在建立媒體內容的編碼、加密或格式轉換工作時建立媒體處理器。

下表提供每個可用媒體處理器的名稱和說明。

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>媒體處理器名稱</th>
       <th>說明</th>
	<th>相關資訊</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure Media Encoder</td>
       <td>讓您使用 Azure Media Encoder 執行編碼工作。</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx">Azure Media Encoder 的工作預設字串</a></td>
    </tr>
    <tr>
       <td>Media Encoder Premium Workflow</td>
       <td>可讓您使用 Media Encoder Premium Workflow 執行編碼工作。</td>
       <td><a href="http://azure.microsoft.com/documentation/articles/media-services-encode-with-premium-workflow/">使用 Media Encoder Premium Workflow 進行編碼。</a></td>
    </tr>    
	<tr>
        <td>Azure Media Indexer</td>
        <td>可讓您的媒體檔案和內容可供搜尋，以及產生隱藏式輔助字幕和關鍵字。</td>
		<td><a href="http://azure.microsoft.com/documentation/articles/media-services-index-content/">使用 Azure Media Indexer 編輯媒體檔案索引</a>。</td>
    </tr>
    <tr>
        <td>Windows Azure Media Packager</td>
        <td>可讓您將媒體資產從 .mp4 轉換為 Smooth Streaming 格式。此外，也可讓您將媒體資產從 Smooth Streaming 轉換為 Apple HTTP Live Streaming (HLS) 格式。</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Azure Media Packager 的工作預設字串</a></td>
    </tr>
    <tr>
        <td>Windows Azure Media Encryptor</td>
        <td>可讓您使用 PlayReady Protection 為媒體資產加密。</td>
        <td><a href=" http://go.microsoft.com/fwlink/?LinkId=613274">Azure Media Packager 的工作預設字串</a></td>
    </tr>
	<tr>
		<td>Azure Media Hyperlapse (預覽)</td>
		<td>可讓您使用影片穩定讓影片中的「巔簸」變得平滑。也可讓您將內容加速至可使用的片段。</td>
		<td><a href="http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db">Azure Media Hyperlapse</a></td>
	</tr>
    <tr>
        <td>Storage Decryption</td>
        <td>可讓您對使用儲存體加密功能加密的媒體資產進行解密。</td>
		<td>N/A</td>
    </tr>  </tbody>
</table>

<br />

##取得 MediaProcessor

>[AZURE.NOTE]使用媒體服務 REST API 時，適用下列考量事項：
>
>在媒體服務中存取實體時，您必須在 HTTP 要求中設定特定的標頭欄位和值。如需詳細資訊，請參閱[媒體服務 REST API 開發設定](media-services-rest-how-to-use.md)。

>成功連線至 https://media.windows.net 後，您會收到指定另一個媒體服務 URI 的 301 重新導向。您必須依照[使用 REST API 連線至媒體服務](media-services-rest-connect_programmatically.md)所述，對新的 URI 進行後續呼叫。



下列 REST 呼叫示範如何依名稱取得媒體處理器執行個體 (在此案例中，**Azure Media Encoder**)。

	
要求：

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-477b-2233-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
	x-ms-version: 2.8
	Host: media.windows.net
	
回應：
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 8a291764-4ed7-405d-aa6e-d3ebabb0b3f6
	request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	x-ms-request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 11 Feb 2015 00:19:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors","value":[{"Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609","Description":"Azure Media Encoder","Name":"Azure Media Encoder","Sku":"","Vendor":"Microsoft","Version":"4.4"}]}


##後續步驟
現在您已了解如何取得媒體處理器執行個體，接著請移至[如何為資產編碼][]主題，以了解如何使用 Azure Media Encoder 為資產編碼。

[如何為資產編碼]: media-services-rest-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[How to: Connect to Media Services Programmatically]: ../media-services-rest-connect_programmatically/

<!---HONumber=July15_HO2-->