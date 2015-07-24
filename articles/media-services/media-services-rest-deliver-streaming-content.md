<properties 
	pageTitle="如何從媒體服務傳遞串流內容" 
	description="了解如何建立定位器，用來建置串流 URL。程式碼使用 REST API。" 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>


#作法：傳遞串流內容

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)和[媒體服務即時資料流工作流程](media-services-live-streaming-workflow.md)系列的一部分。

##概觀


您可以建立隨選串流定位器及建置串流 URL，串流處理調適性位元速率 MP4 集。[為資產編碼](media-services-rest-encode-asset.md)主題說明如何編碼為調適性位元速率 MP4 集。建立定位器之前，您應該如[此](media-services-rest-configure-asset-delivery-policy.md)主題所述設定資產傳遞原則。

您也可以使用隨選串流定位器來建置指向可漸進式下載之 MP4 檔案的 URL。

本主題說明如何建立隨選串流定位器以發佈資產及建置 Smooth、MPEG DASH 和 HLS 串流 URL。它也會示範如何建置漸進式下載 URL。

[下列章節](#types)說明列舉類型，REST 呼叫中會使用這些類型的值。
  
##建立隨選串流定位器

若要建立隨選串流定位器並取得 URL，您需要執行下列動作：


   1. 定義存取原則。
   2. 建立隨選串流定位器。
   3. 如果您想要串流處理，請取得資產內的串流資訊清單檔案 (.ism)。 
   		
	如果您想要漸進式地下載，請取得資產中的 MP4 檔案名稱。 
   4. 建置資訊清單檔或 MP4 檔案的 URL。 


###建立存取原則

要求：
		
	POST https://media.windows.net/api/AccessPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstest1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1424263184&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=NWE%2f986Hr5lZTzVGKtC%2ftzHm9n6U%2fxpTFULItxKUGC4%3d
	x-ms-version: 2.8
	x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
	Host: media.windows.net
	Content-Length: 68
	
	{"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
	
回應：
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 311
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
	Server: Microsoft-IIS/8.5
	request-id: a877528a-bdb4-4414-9862-273f8e64f882
	x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
	x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 18 Feb 2015 06:52:09 GMT
	
	{"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

###建立隨選串流定位器

建立指定資產和資產原則的定位器。

要求：
	
	POST https://media.windows.net/api/Locators HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstest1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1424263184&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=NWE%2f986Hr5lZTzVGKtC%2ftzHm9n6U%2fxpTFULItxKUGC4%3d
	x-ms-version: 2.8
	x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
	Host: media.windows.net
	Content-Length: 181
	
	{"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}

回應：
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 637
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
	Server: Microsoft-IIS/8.5
	request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
	x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
	x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 18 Feb 2015 06:58:37 GMT
	
	{"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"http://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"http://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

###建置串流 URL

建立定位器之後，使用傳回的 **Path** 值建置 Smooth、HLS 和 MPEG DASH URL。

Smooth Streaming：**Path** + 資訊清單檔案名稱 + "/manifest"

例如：

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS：**Path** + 資訊清單檔案名稱 + "/manifest(format=m3u8-aapl)"

例如：

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH：**Path** + 資訊清單檔案名稱 + "/manifest(format=mpd-time-csf)"


例如：

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


###建置漸進式下載 URL

建立定位器之後，使用傳回的 **Path** 值建置漸進式下載 URL。

URL：**Path** + 資產檔案 MP4 名稱

例如：

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

##<a id="types"></a>列舉類型

    [Flags]
    public enum AccessPermissions
    {
        None = 0,
        Read = 1,
        Write = 2,
        Delete = 4,
        List = 8,
    }

    public enum LocatorType
    {
        None = 0,
        Sas = 1,
        OnDemandOrigin = 2,
    } 

<!---HONumber=July15_HO1-->