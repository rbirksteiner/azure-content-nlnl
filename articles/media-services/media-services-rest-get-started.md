<properties 
	pageTitle="使用 REST API 傳遞點播視訊 (VoD) 使用者入門" 
	description="本教學課程將逐步引導您使用 REST API 完成利用 Azure 媒體服務來實作點播視訊 (VoD) 內容傳遞應用程式。" 
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
	ms.date="04/14/2015" 
	ms.author="juliako"/>

#使用 REST API 傳遞點播視訊 (VoD) 使用者入門 

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資料，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 免費試用</a>。

本快速入門將逐步引導您使用 REST API 完成利用 Azure 媒體服務 (AMS) 來實作點播視訊 (VoD) 內容傳遞應用程式。

教學課程中介紹基本的媒體服務工作流程，以及媒體服務開發最常用的程式設計物件和必要工作。完成本教學課程時，您將能夠串流或漸進式下載您已上傳、編碼和下載的範例媒體檔案。

## 必要條件
需要下列必要條件，才能開始使用 REST API 用媒體服務進行開發。

- 了解如何使用媒體服務 REST API 進行開發。如需詳細資訊，請參閱 [media-services-rest-overview](http://msdn.microsoft.com/library/azure/hh973616.aspx)。
- 由您選擇可以傳送 HTTP 要求和回應的應用程式。本教學課程使用 [Fiddler](http://www.telerik.com/download/fiddler)。 

本快速入門會顯示下列工作。

1.  使用入口網站建立媒體服務帳戶。
1.  使用 REST API 連接到媒體服務帳戶。
1.  使用 REST API 建立新資產並上傳視訊檔案。
1.  使用 REST API 來設定串流單位。
2.  使用 REST API 將來源檔案編碼為一組調適性位元速率 MP4 檔案。
1.  使用 REST API 來設定編碼資產的傳遞原則。
1.  使用 REST API 發行資產及取得串流和漸進式下載 URL。 
1.  播放您的內容。 


## <a id="create_ams"></a>使用入口網站建立媒體服務帳戶

1. 在[管理入口網站][]中，依序按一下 [新增]、[媒體服務] 和 [快速建立]。
   
	![Media Services Quick Create](./media/media-services-rest-get-started/wams-QuickCreate.png)

2. 在 [名稱] 中，輸入新帳戶的名稱。媒體服務帳戶名稱是全部小寫且不含空格的數字或字母，而且長度是 3 到 24 個字元。

3. 在 [區域] 中，選取將用來儲存您媒體服務帳戶之中繼資料記錄的地理區域。只有可用的媒體服務區域才會出現在下拉式清單中。

4. 在 [儲存體帳戶] 中，選取儲存體帳戶以從媒體服務帳戶提供媒體內容的 Blob 儲存體。您可以選取與媒體服務帳戶相同地理區域中的現有儲存體帳戶，也可以建立新的儲存體帳戶。新的儲存體帳戶會建立於相同的區域中。

5. 如果您已建立新的儲存體帳戶，請在 [NEW STORAGE ACCOUNT NAME] 中輸入儲存體帳戶的名稱。儲存體帳戶名稱的規則會與媒體服務帳戶相同。

6. 按一下表單底部的 [快速建立]。

	您可以在視窗底端的訊息區域監視程序的狀態。

	成功建立帳戶之後，狀態會變更為作用中。
	
	在頁面底部會顯示 [管理金鑰] 按鈕。當您按一下此按鈕時，會顯示對話方塊，其中具有媒體服務帳戶名稱和主要和次要金鑰。您將需要帳戶名稱和主要金鑰資訊，以便以程式設計方式存取媒體服務帳戶。

	
	![Media Services Page](./media/media-services-rest-get-started/wams-mediaservices-page.png)

	當您按兩下帳戶名稱時，預設會顯示 [快速入門] 頁面。此頁面可讓您執行一些在入口網站的其他頁面也可以執行的管理工作。例如，您可以從此頁面上傳視訊檔案，也可以從 [內容] 頁面上傳視訊檔案。


## <a id="connect"></a>使用 REST API 連接到媒體服務帳戶

存取 Azure 媒體服務時需要兩件事：由 Azure 存取控制服務 (ACS) 提供的存取權杖，以及媒體服務本身的 URI。建立這些要求時可以使用任何方式，前提是您指定正確的標頭值，並在呼叫媒體服務時正確傳入存取權杖。

當您使用媒體服務 REST API 連接到媒體服務時，下列步驟將說明最常見的工作流程：

1. 取得存取權杖。 
2. 連接至媒體服務 URI。 
	
	>[AZURE.NOTE]成功連線至 https://media.windows.net 後，您會收到指定另一個媒體服務 URI 的 301 重新導向。後續的呼叫必須送到新的 URI。
	> 
	> 
3. 將後續的 API 呼叫張貼到新的 URL。 
	
	例如，如果您在嘗試進行連接之後得到下列結果：
		
		HTTP/1.1 301 Moved Permanently
		Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

	您應該將後續的 API 呼叫張貼到 https://wamsbayclus001rest-hs.cloudapp.net/api/。

###取得存取權杖

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

您需要在這個要求的主體中提供 client_id 與 client_secret 值；client_id 與 client_secret 分別對應到 AccountName 與 AccountKey 值。當您設定帳戶時，媒體服務會提供這些值給您。

請注意，您的媒體服務帳戶的 AccountKey 在使用它做為存取權杖要求中 client_secret 值時必須已編碼 URL。

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
	   "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
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
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.8
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
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.8
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
	 


>[AZURE.NOTE]從現在起新 URI 將用於本教學課程。

## <a id="upload"></a>使用 REST API 建立新資產並上傳視訊檔案

在媒體服務中，您會將數位檔案上傳到到資產。**資產**實體可以包含視訊、音訊、影像、縮圖集合、文字播放軌及隱藏式字幕檔案 (以及這些檔案的相關中繼資料)。 一旦檔案會上傳到資產，您的內容會安全地儲存在雲端，以便進行進一步的處理和串流。

您必須建立資產時提供的值是資產建立選項。**Options** 屬性是描述可以使用建立資產之加密選項的列舉值。有效的值是以下清單的其中一個值，而不是值的組合。

 
- **None** = **0** - 不會使用加密。請注意，使用此選項時，您的內容在傳輸或儲存體中靜止時不會受到保護。如果您計劃使用漸進式下載傳遞 MP4，請使用此選項。 
- **StorageEncrypted** = **1** - 在本機使用 AES-256 位元加密將您的純文字內容加密，然後將它上傳到 Azure 儲存體，在該處以加密的方式儲存。以儲存體加密保護的資產會自動解除加密並在編碼前放置在加密的檔案系統中，並且會在上傳為新輸出資產之前選擇性地重新編碼。儲存體加密的主要使用案例是讓您可以使用強式加密來保護磁碟中靜止的高品質輸入媒體檔。
- **CommonEncryptionProtected** = **2** - 如果您要上傳已經使用一般加密或 PlayReady DRM (例如使用 PlayReady DRM 保護的 Smooth Streaming) 加密及保護的內容，請使用這個選項。
- **EnvelopeEncryptionProtected** = **4** – 如果您要上傳使用 AES 加密的 HLS，請使用這個選項。請注意，檔案必須已由 Transform Manager 編碼和加密。

### 建立資產

資產是媒體服務中多種類型或物件集的容器，包括視訊、音訊、影像、縮圖集合、文字播放軌和隱藏式字幕檔案。在 REST API 中，建立資產必須傳送 POST 要求給媒體服務，並將關於您資產的任何屬性資訊放在要求主體中。

下列範例示範如何建立資產。

**HTTP 要求**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 45
	
	{"Name":"BigBuckBunny.mp4", "Options":"0"}
	

**HTTP 回應**

如果成功，則會傳回下列內容：
	
	HTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 452
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
	request-id: e98be122-ae09-473a-8072-0ccd234a0657
	x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:06:40 GMT
		
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
	   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "State":0,
	   "Created":"2015-01-18T22:06:40.6010903Z",
	   "LastModified":"2015-01-18T22:06:40.6010903Z",
	   "AlternateId":null,
	   "Name":"BigBuckBunny2.mp4",
	   "Options":0,
	   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StorageAccountName":"storagetestaccount001"
	}
	
### 建立 AssetFile

[AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) 實體代表儲存在 blob 容器中的視訊或音訊檔案。資產檔案一律會與資產相關聯，資產可包含一或多個 Assetfile。如果資產檔案物件並未與 blob 容器中的數位檔案相關聯，媒體服務編碼器工作將會失敗。

您將數位媒體檔案上傳至 blob 容器之後，您將使用 **MERGE** HTTP 要求，以媒體檔案的相關資訊來更新 AssetFile (如本主題稍後所示)。

**HTTP 要求**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 164
	
	{  
	   "IsEncrypted":"false",
	   "IsPrimary":"false",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**HTTP 回應**

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 535
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
	Server: Microsoft-IIS/8.5
	request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 00:34:07 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "Name":"BigBuckBunny.mp4",
	   "ContentFileSize":"0",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "EncryptionVersion":null,
	   "EncryptionScheme":null,
	   "IsEncrypted":false,
	   "EncryptionKeyId":null,
	   "InitializationVector":null,
	   "IsPrimary":false,
	   "LastModified":"2015-01-19T00:34:08.1934137Z",
	   "Created":"2015-01-19T00:34:08.1934137Z",
	   "MimeType":"video/mp4",
	   "ContentChecksum":null
	}


### 建立具有寫入權限的 AccessPolicy。 

將任何檔案上傳到 blob 儲存體之前，請設定寫入資產的存取原則權限。若要這樣做，請 POST HTTP 要求到 AccessPolicies 實體集。請在建立時定義 DurationInMinutes 值，否則您會在回應中收到 500 內部伺服器錯誤訊息。如需 AccessPolicies 的詳細資訊，請參閱 [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx)。

下列範例示範如何建立 AccessPolicy：
		
**HTTP 要求**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 74
	
	{"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**HTTP 要求**

	If successful, the following response is returned:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 312
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
	Server: Microsoft-IIS/8.5
	request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:18:06 GMT

	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
	   "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "Created":"2015-01-18T22:18:06.6370575Z",
	   "LastModified":"2015-01-18T22:18:06.6370575Z",
	   "Name":"NewUploadPolicy",
	   "DurationInMinutes":440.0,
	   "Permissions":2
	}

### 取得上傳 URL

若要接收實際的上傳 URL，請建立 SAS 定位器。定位器為想要存取資產中之檔案的用戶端定義連線端點的開始時間和類型。您可以為指定的 AccessPolicy 與 Asset 配對建立多個 Locator 實體，以處理不同的用戶端要求與需求。這些 Locator 每個都會使用 StartTime 值加上 AccessPolicy 的 DurationInMinutes 值，以判斷可以使用 URL 的時間長度。如需詳細資訊，請參閱＜定位器＞[](http://msdn.microsoft.com/library/azure/hh974308.aspx)。


SAS URL 具有下列格式：

	{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

適用一些考量事項：

- 您一次不能有超過五個唯一定位器與指定的資產相關聯。如需詳細資訊，請參閱＜定位器＞。
- 如果您需要立即上傳檔案，您應該將 StartTime 值設為目前時間的五分鐘前。這是因為用戶端電腦與媒體服務之間可能有時間差。此外，您的 StartTime 值必須是以下日期時間格式：YYYY-MM-DDTHH:mm:ssZ (例如，"2014-05-23T17:53:50Z")。	
- 建立 Locator 之後到它可供使用時，中間可能會有 30 到 40 秒的延遲。此問題同時適用於 SAS URL 與原始定位器。

下列範例會示範如何建立 SAS URL 定位器，如要求主體中的 Type 屬性所定義 ("1" 代表 SAS 定位器，"2" 代表隨選原始定位器)。傳回的 **Path** 屬性包含上傳檔案必須使用的 URL。
	
**HTTP 要求**
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 178
	
	{  
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Type":1
	}


**HTTP 回應**

如果成功，則會傳回下列回應：
		
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 949
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
	Server: Microsoft-IIS/8.5
	request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 03:01:29 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
	   "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
	   "ExpirationDateTime":"2015-02-19T00:05:53",
	   "Type":1,
	   "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
	   "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Name":null
	}

### 將檔案上傳至 blob 儲存體容器
	
一旦設定 AccessPolicy 與 Locator，實際檔案會使用 Azure 儲存體 REST API 上傳至 Azure Blob 儲存容器。您可以使用頁面或區塊 blob 上傳。

>[AZURE.NOTE]您必須將要上傳的檔案名稱新增到上一節中所收到的 Locator **Path** 值。例如，https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

如需使用 Azure 儲存體 blob 的詳細資訊，請參閱 [Blob 服務 REST API](http://msdn.microsoft.com/library/azure/dd135733.aspx)。


### 更新 AssetFile 

現在，您已上傳您的檔案，請更新 FileAsset 大小 (及其他) 資訊。例如：
	
	MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	
	{  
	   "ContentFileSize":"1186540",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**HTTP 回應**

如果成功，會傳回下列訊息：HTTP/1.1 204 沒有內容

## 刪除 Locator 和 AccessPolicy 

**HTTP 要求**


	DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net

	
**HTTP 回應**

如果成功，則會傳回下列內容：

	HTTP/1.1 204 No Content 
	...

**HTTP 要求**

	DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP 回應**

如果成功，則會傳回下列內容：

	HTTP/1.1 204 No Content 
	...

 
## <a id="configure_streaming_units"></a>使用 REST API 來設定串流單位

使用 Azure 媒體服務時，其中一個最常見案例是提供調適性位元速率串流給您的用戶端。使用調適性位元速率串流，用戶端可以在視訊顯示時，根據目前網路頻寬、CPU 使用率以及其他因素，切換成較高或較低的位元速率串流。媒體服務支援下列調適性位元速率串流技術：HTTP 即時串流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/Access 授權)。

媒體服務提供動態封裝，這讓您以媒體服務支援的串流格式 (MPEG DASH, 、HLS、Smooth Streaming、HDS) 提供調適性位元速率 MP4 或 Smooth Streaming 編碼內容，而不必重新封裝成這些串流格式。

若要利用動態封裝，您需要執行下列動作：

- 取得您打算從該處傳遞內容的**串流端點**的至少一個串流單位 (如本節所述)。- 將夾層 (來源) 檔案編碼或轉碼為一組調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 檔案 (本教學課程稍後會示範編碼步驟)、  

使用動態封裝，您只需要以單一儲存格式儲存及播放檔案，媒體服務會根據來自用戶端的要求建置及傳遞適當的回應。


>[AZURE.NOTE]如需定價詳細資料的相關資訊，請參閱＜[媒體服務定價詳細資料](http://go.microsoft.com/fwlink/?LinkId=275107)＞。

若要變更串流保留單元的數目，請執行下列動作：
	
### 取得您想要更新的串流端點

比方說，讓我們取得您帳戶中的第一個串流端點 (您可以在同一時間最多有兩個處於執行狀態的串流端點)。

**HTTP 要求**：

	GET https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints()?$top=1 HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP 回應**
	
如果成功，則會傳回下列內容：
	
	HTTP/1.1 200 OK
	. . . 
	
### 調整串流端點
 
**HTTP 要求**：

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints('nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486')/Scale HTTP/1.1
	Content-Type: application/json;odata=verbose
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
	x-ms-version: 2.8
	x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
	Host: wamsbayclus001rest-hs.cloudapp.net
	
	{"scaleUnits":1}

**HTTP 回應**

	HTTP/1.1 202 Accepted
	Cache-Control: no-cache
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
	request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
	x-ms-request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
	operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Fri, 16 Jan 2015 22:16:43 GMT
	Content-Length: 0

	
### <a id="long_running_op_status"></a> 檢查長時間執行作業的狀態

任何新的單位配置需要大約 20 分鐘的時間才能完成。若要檢查作業的狀態，請使用 **Operations** 方法，並指定作業識別碼。作業識別碼是在 **Scale** 要求的回應中傳回。

	operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
 
**HTTP 要求**：
	
	GET https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7') HTTP/1.1
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	
**HTTP 回應**
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 515
	Content-Type: application/json;odata=verbose;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 829e1a89-3ec2-4836-a04d-802b5aeff5e8
	request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
	x-ms-request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Fri, 16 Jan 2015 22:57:39 GMT
	
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
	         "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Operation"
	      },
	      "Id":"nb:opid:UUID:cc339c28-6bba-4f7d-bee5-91ea4a0a907e",
	      "State":"Succeeded",
	      "TargetEntityId":"nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486",
	      "ErrorCode":null,
	      "ErrorMessage":null
	   }
	}


## <a id="encode"></a>將來源檔案編碼為一組調適性位元速率 MP4 檔案

將資產內嵌到媒體服務之後，可以先將媒體編碼、轉碼多工處理、加上浮水印等，再傳遞給用戶端。這些活動會針對多個背景角色執行個體排定和執行，以確保高效能與可用性。這些活動稱為工作，每個[工作](http://msdn.microsoft.com/library/azure/hh974289.aspx)包含對資產檔案執行實際工作的不可部分完成的工作。

如稍早所提及，使用 Azure 媒體服務時，其中一個最常見的案例是將調適性位元速率串流傳遞給用戶端。媒體服務可以以下列其中一種格式動態封裝一組可調位元速率 MP4 檔案：HTTP 即時資料流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/存取使用權)。

若要利用動態封裝，您需要執行下列動作：

- 將您的夾層 (來源) 檔編碼或轉換為一組調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 檔案。  
- 為您計畫從該處傳遞內容的串流端點至少取得一個串流單元。 

下一節示範如何建立包含一個編碼工作的工作。工作指定使用 **Azure Media Encoder**，將夾層檔案轉換為一組調適性位元速率 MP4。此節也會示範如何監視工作處理進度。工作完成時，您將能夠建立存取資產所需的定位器。

### 取得媒體處理器

在媒體服務中，媒體處理器是可處理特定處理工作的元件，例如編碼、格式轉換、加密或解密媒體內容。針對此教學課程中所示的編碼工作，我們將使用 Azure Media Encoder。

下列程式碼要求編碼器的識別碼。

**HTTP 要求**

	GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	

**HTTP 回應**

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
	x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 07:54:09 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
	   "value":[  
	      {  
	         "Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609",
	         "Description":"Azure Media Encoder",
	         "Name":"Azure Media Encoder",
	         "Sku":"",
	         "Vendor":"Microsoft",
	         "Version":"4.4"
	      }
	   ]
	}

### 建立工作

每個工作可以有一或多個工作，視您想要完成的處理類型而定。您可以透過 REST API 以兩種方式的其中之一建立工作和其相關工作：工作可以透過 Job 實體上的 Tasks 導覽屬性，或透過 OData 批次處理進行內嵌定義。媒體服務 SDK 使用批次處理；不過，為了本主題中的程式碼範例可讀性，工作都是內嵌定義。如需批次處理的資訊，請參閱[開放式資料通訊協定 (OData) 批次處理](http://www.odata.org/documentation/odata-version-3-0/batch-processing/)。

下列範例會示範如何建立和張貼工作，並將一個工作設為在特定的解析度與品質將視訊編碼。下列文件區段包含 Azure 媒體處理器所支援之所有[工作預設](http://msdn.microsoft.com/library/azure/dn619392.aspx)的清單。

**HTTP 要求**
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Content-Type: application/json
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 482
	
	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
				<outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      }
	   ]
	}

**HTTP 回應**

如果成功，則會傳回下列回應：

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1215
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
	Server: Microsoft-IIS/8.5
	request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
	x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 08:04:35 GMT
		
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
	         "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
	      },
	      "Tasks":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
	         }
	      },
	      "OutputMediaAssets":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
	         }
	      },
	      "InputMediaAssets":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
	         }
	      },
	      "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	      "Name":"NewTestJob",
	      "Created":"2015-01-19T08:04:34.3287057Z",
	      "LastModified":"2015-01-19T08:04:34.3287057Z",
	      "EndTime":null,
	      "Priority":0,
	      "RunningDuration":0,
	      "StartTime":null,
	      "State":0,
	      "TemplateId":null,
	      "JobNotificationSubscriptions":{  
	         "__metadata":{  
	            "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
	         },
	         "results":[  
	
	         ]
	      }
	   }
	}


有任何工作要求中有一些重要事項要注意：

- TaskBody 屬性必須使用 XML 常值來定義工作所使用的輸入或輸出資產數目。工作主題包含 XML 的 XML 結構描述定義。
- 在 TaskBody 定義中，<inputAsset> 和 <outputAsset> 的每一個內部值必須設定為 JobInputAsset(value) 或 JobOutputAsset(value)。
- 每個工作可以有多個輸出資產。一個 JobOutputAsset(x) 只能使用一次做為工作中的工作輸出。
- 您可以指定 JobInputAsset 或 JobOutputAsset 做為工作的輸入資產。
- 工作不能形成循環。
- 您傳遞至 JobInputAsset 或 JobOutputAsset 的 value 參數代表資產的索引值。實際資產定義在作業實體定義上的 InputMediaAsset 與 OutputMediaAsset 導覽屬性。 

>[AZURE.NOTE]由於媒體服務建置在 OData v3 之上，因此 InputMediaAsset 與 OutputMediaAsset 導覽屬性集合中的個別資產會透過 "__metadata : uri" 名稱 / 值組參考。.

- InputMediaAsset 對應至您在媒體服務中建立的一個或多個資產。OutputMediaAsset 由系統建立。它們不會參考現有的資產。
- OutputMediaAsset 可以使用 assetName 屬性命名。如果這個屬性不存在，則 OutputMediaAsset 的名稱將是 <outputAsset> 元素的任何內部文字值，並且尾碼為工作名稱值或工作識別碼值 (在未定義 Name 屬性的情況下)。例如，如果您將 assetName 的值設為 "Sample"，則 OutputMediaAsset Name 屬性會設為 "Sample"。不過，如果您未設定 assetName 的值，但已將工作名稱設為 "NewJob"，則 OutputMediaAsset Name 會是 "JobOutputAsset(value)_NewJob"。 

	下列範例示範如何設定 assetName 屬性：
	
		"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"


- 啟用工作鏈結：

	- 工作必須有至少 2 個工作
	- 必須有至少一個工作的輸入是工作中另一項工作的輸出。

如需詳細資訊，請參閱[使用媒體服務 REST API 建立編碼工作](http://msdn.microsoft.com/library/azure/jj129574.aspx)。

### 監看處理進度

您可以使用 State 屬性擷取工作狀態，如下列範例所示。

**HTTP 要求**

	GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-2233-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908022&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=RYXOraO6Z%2f7l9whWZQN%2bypeijgHwIk8XyikA01Kx1%2bk%3d
	Host: wamsbayclus001rest-hs.net
	Content-Length: 0


**HTTP 回應**

如果成功，則會傳回下列回應：

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 17
	Content-Type: application/json;odata=verbose;charset=utf-8
	Server: Microsoft-IIS/7.5
	x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Sun, 13 May 2012 05:16:53 GMT
	
	{"d":{"State":2}}


### 取消工作

媒體服務可讓您透過 CancelJob 函式取消正在執行的工作。如果您嘗試取消的工作狀態為已取消、取消中、錯誤或已完成，這項呼叫將傳回 400 錯誤碼。

下列範例示範如何呼叫 CancelJob。


**HTTP 要求**


	GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.2
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908753&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=kolAgnFfbQIeRv4lWxKSFa4USyjWXRm15Kd%2bNd5g8eA%3d
	Host: wamsbayclus001rest-hs.net


如果成功，會傳回 204 回應碼且沒有訊息主體。

>[AZURE.NOTE]將工作識別碼做為參數傳遞到 CancelJob 時，您必須將工作識別碼進行 URL 編碼 (通常是 nb:jid:UUID: somevalue)。


### 取得輸出資產 

下一節我們要設定工作輸出資產 (編碼資產) 的傳遞原則。下列程式碼示範如何要求輸出資產識別碼。


**HTTP 要求**

	GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	

**HTTP 回應**

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 445
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
	x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 08:28:13 GMT
		
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
	   "value":[  
	      {  
	         "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	         "State":0,
	         "Created":"2015-01-19T07:52:15.603",
	         "LastModified":"2015-01-19T07:52:15.603",
	         "AlternateId":null,
	         "Name":"Multibitrate MP4s",
	         "Options":0,
	         "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	         "StorageAccountName":"storagetestaccount001"
	      }
	   ]
	}


## <a id="configure_delivery_method"></a>設定編碼資產的傳遞原則

媒體服務內容傳遞工作流程中的其中一個步驟是設定資產傳遞原則。資產傳遞原則設定包括一些事項：什麼通訊協定可以用來傳遞資產 (例如，MPEG DASH、HLS、HDS、Smooth Streaming 或 all)，您是否想要以動態方式加密資產，以及加密方法 (信封或一般加密)。

下列 **AssetDeliveryPolicies** HTTP 要求指定不要套用動態加密 (AssetDeliveryPolicyType 可以是下列值之一：None = 0, Blocked = 1, NoDynamicEncryption = 2, DynamicEnvelopeEncryption = 3, DynamicCommonEncryption = 4) 及在下列任何一個通訊協定中傳遞串流：MPEG DASH、HLS 和 Smooth Streaming 通訊協定 (AssetDeliveryProtocol 可以是下列值的組合： None = 0, SmoothStreaming = 1, Dash = 2, HLS = 4, Hds = 8, All = 65535)。


### 建立 AssetDeliveryPolicies


**HTTP 要求**
		
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/AssetDeliveryPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421679198&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=aUvBcDwRAFk1JLxceWu%2bf9dVrCZM7PrTRbZd0TtoKvU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 83
	
	{"Name":"Clear Policy", "AssetDeliveryPolicyType":"2","AssetDeliveryProtocol":"7"} 


**HTTP 回應**
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 361
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aef97ae36-b898-4b8a-b427-7819ee726276')
	Server: Microsoft-IIS/8.5
	request-id: 7391a4b2-995d-4fc5-aca5-a398786ea38e
	x-ms-request-id: 7391a4b2-995d-4fc5-aca5-a398786ea38e
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 09:13:18 GMT

	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AssetDeliveryPolicies/@Element",
	   "Id":"nb:adpid:UUID:ef97ae36-b898-4b8a-b427-7819ee726276",
	   "Name":"Clear Policy",
	   "AssetDeliveryProtocol":7,
	   "AssetDeliveryPolicyType":2,
	   "AssetDeliveryConfiguration":null,
	   "Created":"2015-01-19T09:13:18.911615Z",
	   "LastModified":"2015-01-19T09:13:18.911615Z"
	}
    

### 連結資產傳遞原則與資產

下列 HTTP 要求會將指定的傳遞原則與指定資產產生關聯。

**HTTP 要求**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/$links/DeliveryPolicies HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Content-Type: application/json
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421679198&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=aUvBcDwRAFk1JLxceWu%2bf9dVrCZM7PrTRbZd0TtoKvU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 140
	
	{ "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aef97ae36-b898-4b8a-b427-7819ee726276')" }


**HTTP 回應**

	HTTP/1.1 204 No Content
    . . . 


## <a id="publish_get_urls"></a>使用 REST API 發行資產及取得串流和漸進式下載 URL

若要串流處理或下載資產，您必須先建立定位器來「發佈」它。定位器可以存取資產中所含的檔案。媒體服務支援兩種類型的定位器：OnDemandOrigin 定位器，用於串流媒體 (例如，MPEG DASH、HLS 或 Smooth Streaming) 和存取簽章 (SAS) 定位器，用來下載媒體檔案。

建立定位器之後，您便可以建立用來串流或下載檔案的 URL。


Smooth Streaming 的串流 URL 具有下列格式：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS 的串流 URL 具有下列格式：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH 的串流 URL 具有下列格式：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


用來下載檔案的 SAS URL 具有下列格式：

	{blob container name}/{asset name}/{file name}/{SAS signature}

本節示範如何執行「發行」您的資產所需的下列工作。

- 建立具有讀取權限的 AccessPolicy 
- 建立下載內容用的 SAS URL 
- 建立串流內容用的原始 URL 

###建立具有讀取權限的 AccessPolicy

下載或串流任何媒體內容之前，請先定義具有讀取權限的 AccessPolicy，並建立適當的 Locator 實體，指定您想要為用戶端啟用的傳遞機制類型。如需可用屬性的詳細資訊，請參閱 [AccessPolicy 實體屬性](https://msdn.microsoft.com/library/azure/hh974297.aspx#accesspolicy_properties)。

下列範例示範如何針對指定資產指定讀取權限的 AccessPolicy。

	POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
	Host: wamsbayclus001rest-hs.net
	Content-Length: 74
	Expect: 100-continue
	
	{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

如果成功，會傳回 201 成功碼，描述您所建立的 AccessPolicy 實體。然後，您將使用 AccessPolicy 識別碼以及資產的資產識別碼，其中此資產包含您要傳遞 (例如做為輸出資產) 以建立 Locator 實體的檔案。

>[AZURE.NOTE]這個基本工作流程在擷取資產 時 (如本主題前面所討論) 與上傳檔案相同。此外，就像上傳檔案，如果您 (或您的用戶端) 需要立即存取檔案，請將 StartTime 值設定為目前時間之前五分鐘。需要進行此動作是因為用戶端電腦與媒體服務之間可能有時間差。StartTime 值必須是以下日期時間格式：YYYY-MM-DDTHH:mm:ssZ (例如，"2014-05-23T17:53:50Z")。


###建立下載內容用的 SAS URL 

下列程式碼示範如何取得可以用來下載先前建立及上傳之媒體檔案的 URL。AccessPolicy 具有讀取權限集，而定位器路徑指向 SAS 下載 URL。

	POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
	Host: wamsbayclus001rest-hs.net
	Content-Length: 182
	Expect: 100-continue
	
	{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

如果成功，則會傳回下列回應：

	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1150
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Mon, 14 May 2012 21:41:32 GMT
		
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
	         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
	      },
	      "AccessPolicy":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
	         }
	      },
	      "Asset":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
	         }
	      },
	      "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
	      "ExpirationDateTime":"/Date(1337049393000)/",
	      "Type":1,
	      "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
	      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
	      "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	      "StartTime":"/Date(1337031393000)/"
	   }
	}


傳回的 **Path** 屬性包含 SAS URL。

>[AZURE.NOTE]如果您下載儲存體加密內容，則必須手動將其解密才能呈現，或是在處理工作中使用儲存體解密 MediaProcessor，以純文字將處理的檔案輸出到 OutputAsset，然後從該資產下載。如需有關處理的詳細資訊，請參閱＜使用媒體服務 REST API 建立編碼工作＞。此外，已建立 SAS URL 定位器之後，無法更新它們。例如，您無法以更新的 StartTime 值重複使用相同的定位器。這是因為建立 SAS URL 的方式。如果您想要在定位器過期之後存取資產以便下載，您必須用新的 StartTime 建立一個新的定位器。

###下載檔案

設定 AccessPolicy 與 Locator 之後，您可以使用 Azure 儲存體 REST API 下載檔案。

>[AZURE.NOTE]您必須將要下載的檔案名稱新增到前一節中所收到的 Locator **Path** 值。例如，https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

如需使用 Azure 儲存體 blob 的詳細資訊，請參閱 [Blob 服務 REST API](http://msdn.microsoft.com/library/azure/dd135733.aspx)。

由於您先前執行的編碼工作 (編碼成自調適性 MP4 集)，您有多個可以漸進式下載的 MP4 檔案。例如：
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


###建立串流內容用的串流 URL


下列程式碼示範如何建立串流 URL 定位器：

	POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
	Host: wamsbayclus001rest-hs
	Content-Length: 182
	Expect: 100-continue
	
	{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

如果成功，則會傳回下列回應：

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 981
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Mon, 14 May 2012 21:41:39 GMT
		
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
	         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
	      },
	      "AccessPolicy":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
	         }
	      },
	      "Asset":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
	         }
	      },
	      "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
	      "ExpirationDateTime":"/Date(1337049395000)/",
	      "Type":2,
	      "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
	      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
	      "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
	      "StartTime":"/Date(1337031395000)/"
	   }
	}

若要在串流媒體播放器中串流 Smooth Streaming 原始 URL，您必須在 Path 屬性後附加 Smooth Streaming 資訊清單檔案的名稱，後面再接 "/manifest"。

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

若要串流 HLS，請在 "/manifest" 之後附加 (format=m3u8-aapl) 。

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

若要串流 MPEG DASH，請在 "/manifest" 之後附加 (format=mpd-time-csf) 。

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>播放您的內容  

若要串流您的視訊，請使用 [Azure 媒體服務播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。

若要測試漸進式下載，請將 URL 貼入瀏覽器 (例如，IE、Chrome、Safari)。

##後續步驟

深入了解建置點播視訊應用程式的相關資訊[建置 VoD 應用程式](media-services-video-on-demand-workflow.md)


##其他資源
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - 立即在線上取得您的影片！</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - 動態封裝和行動裝置</a>


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [管理入口網站]: http://manage.windowsazure.com/


 

<!---HONumber=62-->