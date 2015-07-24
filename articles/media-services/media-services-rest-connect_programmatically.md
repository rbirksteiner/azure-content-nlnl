<properties 
	pageTitle="使用 REST API 連接到媒體服務帳戶" 
	description="本主題示範如何使用 REST API 連接到媒體服務。" 
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
	ms.date="04/13/2015" 
	ms.author="juliako"/>


# 使用媒體服務 REST API 連接到媒體服務帳戶

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)和[媒體服務即時資料流工作流程](media-services-live-streaming-workflow.md)系列的一部分。

本主題描述如何在使用媒體服務 REST API 進行程式設計時，取得 Microsoft Azure 媒體服務的程式設計連線。

存取 Microsoft Azure 媒體服務時需要兩件事：由 Azure 存取控制服務 (ACS) 提供的存取權杖，以及媒體服務本身的 URI。建立這些要求時可以使用任何方式，前提是您指定正確的標頭值，並在呼叫媒體服務時正確傳入存取權杖。

當您使用媒體服務 REST API 連接到媒體服務時，下列步驟將說明最常見的工作流程：

1. 取得存取權杖 
2. 連接至媒體服務 URI 

	>[AZURE.NOTE]成功連線至 https://media.windows.net 後，您會收到指定另一個媒體服務 URI 的 301 重新導向。後續的呼叫必須送到新的 URI。您也可能會收到 HTTP/1.1 200 回應，其中包含 ODATA API 中繼資料描述。

3. 將後續的 API 呼叫張貼到新的 URL。

	例如，如果您在嘗試進行連接之後得到下列結果：

		HTTP/1.1 301 Moved Permanently
		Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

	您應該將後續的 API 呼叫張貼到 https://wamsbayclus001rest-hs.cloudapp.net/api/。

##取得存取權杖

若要直接透過 REST API 存取媒體服務，從 ACS 擷取存取權杖，並在每個您對服務提出的 HTTP 要求中使用它。這個權杖類似於 ACS 根據 HTTP 要求標頭中提供的存取宣告而提供的其他權杖，以及使用 OAuth v2 通訊協定。直接連接到媒體服務之前，您不需要其他任何必要條件。

下列範例會顯示用來擷取權杖的 HTTP 要求標頭和主體。

**標頭**：

	POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
	Content-Type: application/x-www-form-urlencoded
	Host: wamsprodglobal001acs.accesscontrol.windows.net
	Content-Length: 120
	Expect: 100-continue
	Connection: Keep-Alive
	Accept: application/json

	
**主體**：

您需要在這個要求的主體中證明 client_id 與 client_secret 值；client_id 與 client_secret 分別對應到 AccountName 與 AccountKey 值。當您設定帳戶時，媒體服務會提供這些值給您。

請注意，您的媒體服務帳戶的 AccountKey 在使用它做為存取權杖要求中 client_secret 值時必須已編碼 URL (請參閱 [Percent-Encoding](http://tools.ietf.org/html/rfc3986#section-2.1))。

	grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


例如：

	grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


下列範例會顯示在回應主體中包含存取權杖的 HTTP 回應。

	HTTP/1.1 200 OK
	Cache-Control: no-cache, no-store
	Pragma: no-cache
	Content-Type: application/json; charset=utf-8
	Expires: -1
	request-id: a65150f5-2784-4a01-a4b7-33456187ad83
	X-Content-Type-Options: nosniff
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Thu, 15 Jan 2015 08:07:20 GMT
	Content-Length: 670
	
	{  
	   "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
	   "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
	   "expires_in":"21600",
	   "scope":"urn:WindowsAzureMediaServices"
	}
	

>[AZURE.NOTE]建議您將 "access_token" 和 "expires_in" 值快取到外部儲存體。稍後可以從儲存體擷取權杖資料，然後重複使用在媒體服務 REST API 呼叫中。這特別適用於權杖可以在多個處理程序或電腦之間安全共用的情況。

請務必監控存取權杖的 "expires_in" 值，並視需要以新權杖更新您的 REST API 呼叫。

###連接至媒體服務 URI

媒體服務的根 URI 為 https://media.windows.net/。您應該一開始會連接到此 URI，而且如果回應中出現 301 重新導向，您應該將後續呼叫送到新的 URI。此外，請勿在要求中使用任何自動重新導向/跟隨邏輯。HTTP 指令動詞與要求主體不會轉送到新的 URI。

請注意，上傳與下載資產檔案的根 URI 是 https://yourstorageaccount.blob.core.windows.net/，其中儲存體帳戶名稱是您在媒體服務帳戶設定期間所用的相同名稱。

下列範例示範對媒體服務根 URI 的 HTTP 要求 (https://media.windows.net/)。此要求會在回應中得到 301 重新導向。後續的要求使用新的 URI (https://wamsbayclus001rest-hs.cloudapp.net/api/)。

**HTTP 要求**：
	
	GET https://media.windows.net/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.9
	Accept: application/json
	Host: media.windows.net


**HTTP 回應**：
	
	HTTP/1.1 301 Moved Permanently
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
	Server: Microsoft-IIS/8.5
	request-id: 987d7652-497a-44e5-b815-f492e02aef97
	x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:53 GMT
	Content-Length: 164
	
	<html><head><title>Object moved</title></head><body>
	<h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
	</body></html>


**HTTP 要求** (使用新的 URI)：
			
	GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.9
	Accept: application/json
	Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP 回應**：
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 1250
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:52 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
	 


>[AZURE.NOTE]取得新的 URI 之後，便應該使用此 URI 來與媒體服務通訊。


<!-- Anchors. -->


<!-- URLs. -->

<!---HONumber=62-->