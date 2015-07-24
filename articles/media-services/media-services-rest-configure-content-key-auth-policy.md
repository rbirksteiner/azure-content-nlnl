<properties 
	pageTitle="使用 REST 設定內容金鑰授權原則" 
	description="了解如何設定內容金鑰的授權原則。" 
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
	ms.date="06/05/2015" 
	ms.author="juliako"/>



#動態加密：設定內容金鑰授權原則 
[AZURE.INCLUDE [媒體-服務-選取器-內容-金鑰-auth-原則](../../includes/media-services-selector-content-key-auth-policy.md)]

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)和[媒體服務即時資料流工作流程](media-services-live-streaming-workflow.md)系列的一部分。

##概觀

Microsoft Azure 媒體服務可讓您傳遞利用進階加密標準 (AES) (使用 128 位元加密金鑰) 和 PlayReady DRM 所加密的內容 (動態)。媒體服務也提供服務，傳遞金鑰和 PlayReady 授權給授權用戶端。

如果您想要媒體服務加密資產，您需要建立加密金鑰 (**CommonEncryption** 或 **EnvelopeEncryption**) 與資產 (如[這裡](media-services-rest-create-contentkey.md)所述) 的，並且設定金鑰的授權原則 (如本文中所述)。

目前，您可以加密下列串流格式：HLS、MPEG DASH 和 Smooth Streaming。無法加密 HDS 串流格式，或漸進式下載。

播放程式要求串流時，媒體服務便會使用 AES 或 PlayReady 加密，使用指定的金鑰動態加密您的內容。為了將串流解密，播放程式將從金鑰傳遞服務要求金鑰。為了決定使用者是否有權取得金鑰，服務會評估為金鑰指定的授權原則。

媒體服務支援多種方式來驗證提出金鑰要求的使用者。內容金鑰授權原則可能會有一個或多個授權限制：**open**、**token** 限制或 **IP** 限制。權杖限制原則必須伴隨著安全權杖服務 (STS) 所發出的權杖。媒體服務支援**簡單 Web 權杖 ** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) 格式和 **JSON Web** 格式和(JWT) 格式的權杖。

媒體服務不提供安全權杖服務。您可以建立自訂 STS，或利用 Microsoft Azure ACS 來發行權杖。STS 必須設定為建立使用指定金鑰簽署的權杖，並發行在權杖限制組態中指定的宣告 (如本文中所述)。如果權杖有效，且權杖中的宣告符合為內容金鑰設定的宣告，媒體服務金鑰傳遞服務會將加密金鑰傳回給用戶端。

如需詳細資訊，請參閱

[JWT 權杖驗證](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[整合 Azure 媒體服務 OWIN MVC 型應用程式與 Azure Active Directory 並根據 JWT 宣告限制內容金鑰傳遞](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)。

[使用 Azure ACS 發行權杖](http://mingfeiy.com/acs-with-key-services)。

###適用一些考量事項：

- 為了能夠使用動態封裝和動態加密，您至少有一個串流保留單元。如需詳細資訊，請參閱[如何調整媒體服務](media-services-manage-origins.md#scale_streaming_endpoints)。 
- 您的資產必須包含一組調適性位元速率 MP4 或調適性位元速率 Smooth Streaming 檔案。如需詳細資訊，請參閱[為資產編碼](media-services-encode-asset.md)。  
- 使用 **AssetCreationOptions.StorageEncrypted** 選項，上傳資產並為其編碼。
- 如果您計劃有多個內容金鑰需要相同的原則組態，強烈建議建立一個授權原則，並針對多個內容金鑰重複使用。
- 金鑰傳遞服務會快取 ContentKeyAuthorizationPolicy 和其相關物件 (原則選項和限制) 15 分鐘。如果您建立 ContentKeyAuthorizationPolicy，並指定要使用 "Token" 的限制，那麼便測試它，然後將原則更新為"Open" 限制，將需要大約 15 分鐘，原則才會切換為 "Open" 版本的原則。
- 如果您加入或更新您的資產傳遞原則，您必須刪除現有的定位程式 (如果有的話)，並建立新的定位器。


##AES-128 動態加密 

>[AZURE.NOTE]使用媒體服務 REST API 時，適用下列考量事項：
>
>在媒體服務中存取實體時，您必須在 HTTP 要求中設定特定的標頭欄位和值。如需詳細資訊，請參閱[媒體服務 REST API 開發設定](media-services-rest-how-to-use.md)。

>成功連線至 https://media.windows.net 後，您會收到指定另一個媒體服務 URI 的 301 重新導向。您必須依照[使用 REST API 連線至媒體服務](media-services-rest-connect_programmatically.md)所述，對新的 URI 進行後續呼叫。


###Open 限制

Open 限制表示系統將會傳送金鑰給提出金鑰要求的任何人。這項限制可用於測試用途。

下列範例會建立 open 授權原則，並將它加入至內容金鑰。

####<a id="ContentKeyAuthorizationPolicies"></a>建立 ContentKeyAuthorizationPolicies

要求：
		
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423578086&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lZlyQ2%2bvH73qtJsb42%2fH3xF7r7EvQFR3UXyezuDENFU%3d
	x-ms-version: 2.11
	x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 36
	
	{"Name":"Open Authorization Policy"}
	
回應：
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 211
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Adb4593da-f4d1-4cc5-a92a-d20eacbabee4')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
	request-id: aabfa731-e884-4bf3-8314-492b04747ac4
	x-ms-request-id: aabfa731-e884-4bf3-8314-492b04747ac4
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 08:25:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:db4593da-f4d1-4cc5-a92a-d20eacbabee4","Name":"Open Authorization Policy"}

####<a id="ContentKeyAuthorizationPolicyOptions"></a>建立 ContentKeyAuthorizationPolicyOptions
	
要求：

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 3.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423580006&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Ref3EsonGF7fUKCwGwGgiMnZitzIzsDOvvMTeVrVVPg%3d
	x-ms-version: 2.11
	x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 168
	
	{"Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}

回應：
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 349
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
	request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
	x-ms-request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 08:56:40 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:57829b17-1101-4797-919b-f816f4a007b7","Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}

####<a id="LinkContentKeyAuthorizationPoliciesWithOptions"></a>連結 ContentKeyAuthorizationPolicies 與選項

要求：
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3A0baa438b-8ac2-4c40-a53c-4d4722b78715')/$links/Options HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Content-Type: application/json
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423580006&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Ref3EsonGF7fUKCwGwGgiMnZitzIzsDOvvMTeVrVVPg%3d
	x-ms-version: 2.11
	x-ms-client-request-id: 9847f705-f2ca-4e95-a478-8f823dbbaa29
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 154
	
	{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')"}

回應：

	HTTP/1.1 204 No Content

####<a id="AddAuthorizationPolicyToKey"></a>將授權原則加入內容金鑰

要求：

	PUT https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A2e6d36a7-a17c-4e9a-830d-eca23ad1a6f9') HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423581565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=JiNSG3w6r2C0nIyfKvTZj1uPJGjuitD%2b0sbfZ%2b2JDZI%3d
	x-ms-version: 2.11
	x-ms-client-request-id: e613efff-cb6a-41b4-984a-f4f8fb6e76a4
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 78
	
	{"AuthorizationPolicyId":"nb:ckpid:UUID:c06cebb8-c4f0-4d1a-ba00-3273fb2bc3ad"}

回應：

	HTTP/1.1 204 No Content

###Token 限制

本節描述如何建立內容金鑰授權原則，然後建立它與內容金鑰的關聯。授權原則描述必須符合哪些授權需求，以判斷使用者是否有權接收金鑰 (例如，「驗證金鑰」清單是否包含簽署權杖用的金鑰)。

若要設定 token 限制選項，您需要使用 XML 來描述權杖的授權需求。token 限制組態 XML 必須符合下列 XML 結構描述。

####<a id="schema"></a>Token 限制結構描述
	
	<?xml version="1.0" encoding="utf-8"?>
	<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
	  <xs:complexType name="TokenClaim">
	    <xs:sequence>
	      <xs:element name="ClaimType" nillable="true" type="xs:string" />
	      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	  <xs:complexType name="TokenRestrictionTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
	      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
	      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
	  <xs:complexType name="ArrayOfTokenVerificationKey">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	  <xs:complexType name="TokenVerificationKey">
	    <xs:sequence />
	  </xs:complexType>
	  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	  <xs:complexType name="ArrayOfTokenClaim">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
	  <xs:complexType name="SymmetricVerificationKey">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:TokenVerificationKey">
	        <xs:sequence>
	          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
	        </xs:sequence>
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
	</xs:schema>

設定 **token** 限制原則時，您必須指定主要**驗證金鑰**、**簽發者**和**對象**參數。**主要驗證金鑰**包含簽署權杖使用的金鑰，**簽發者**是發行權杖的安全權杖服務。**對象** (有時稱為**範圍**) 描述權杖或權杖獲授權存取之資源的用途。媒體服務金鑰傳遞服務會驗證權杖中的這些值符合在範本中的值。

下列範例會建立具有 token 限制的授權原則。在此範例中，用戶端必須提出權杖，權杖中包含簽署金鑰 (VerificationKey)、權杖簽發者和必要的宣告。
	
###建立 ContentKeyAuthorizationPolicies

建立「權杖限制原則」，如[這裡](#ContentKeyAuthorizationPolicies)所示。


###建立 ContentKeyAuthorizationPolicyOptions

要求：
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 3.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423580720&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=5LsNu%2b0D4eD3UOP3BviTLDkUjaErdUx0ekJ8402xidQ%3d
	x-ms-version: 2.11
	x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 1079
	
	{"Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1"><AlternateVerificationKeys><TokenVerificationKey i:type="SymmetricVerificationKey"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testacs.com/</Issuer><PrimaryVerificationKey i:type="SymmetricVerificationKey"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil="true" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

回應：
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1260
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae1ef6145-46e8-4ee6-9756-b1cf96328c23')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
	request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
	x-ms-request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 09:10:37 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e1ef6145-46e8-4ee6-9756-b1cf96328c23","Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1"><AlternateVerificationKeys><TokenVerificationKey i:type="SymmetricVerificationKey"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testacs.com/</Issuer><PrimaryVerificationKey i:type="SymmetricVerificationKey"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil="true" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
	
####連結 ContentKeyAuthorizationPolicies 與選項

連結 ContentKeyAuthorizationPolicies 與選項，如[這裡](#ContentKeyAuthorizationPolicies)所示。

####將授權原則加入內容金鑰

將 AuthorizationPolicy 加入 ContentKey，如[這裡](#AddAuthorizationPolicyToKey)所示。


##PlayReady 動態加密 

媒體服務可讓您設定您要 PlayReady DRM 執行階段在使用者嘗試播放受保護內容時強制執行的權限和限制。

使用 PlayReady 保護內容時，您需要在驗證原則中指定的其中一件事是定義 [PlayReady 授權範本](https://msdn.microsoft.com/library/azure/dn783459.aspx)的 XML 字串。

###Open 限制
	
Open 限制表示系統將會傳送金鑰給提出金鑰要求的任何人。這項限制可用於測試用途。

下列範例會建立 open 授權原則，並將它加入至內容金鑰。
	
####<a id="ContentKeyAuthorizationPolicies2"></a>建立 ContentKeyAuthorizationPolicies

要求：

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423581565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=JiNSG3w6r2C0nIyfKvTZj1uPJGjuitD%2b0sbfZ%2b2JDZI%3d
	x-ms-version: 2.11
	x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 58
	
	{"Name":"Deliver Common Content Key"}
	
回應：
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 233
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Acc3c64a8-e2fc-4e09-bf60-ac954251a387')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
	request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
	x-ms-request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 09:26:00 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:cc3c64a8-e2fc-4e09-bf60-ac954251a387","Name":"Deliver Common Content Key"}
	

#### 建立 ContentKeyAuthorizationPolicyOptions

要求：
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 3.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423581565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=JiNSG3w6r2C0nIyfKvTZj1uPJGjuitD%2b0sbfZ%2b2JDZI%3d
	x-ms-version: 2.11
	x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 593
	
	{"Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type="ContentEncryptionKeyFromHeader" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}
	
回應：
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 774
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A1052308c-4df7-4fdb-8d21-4d2141fc2be0')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
	request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
	x-ms-request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 09:23:24 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:1052308c-4df7-4fdb-8d21-4d2141fc2be0","Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type="ContentEncryptionKeyFromHeader" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}

####連結 ContentKeyAuthorizationPolicies 與選項

連結 ContentKeyAuthorizationPolicies 與選項，如[這裡](#ContentKeyAuthorizationPolicies)所示。

####將授權原則加入內容金鑰

將 AuthorizationPolicy 加入 ContentKey，如[這裡](#AddAuthorizationPolicyToKey)所示。


###Token 限制

若要設定 token 限制選項，您需要使用 XML 來描述權杖的授權需求。Token 限制組態 XML 必須符合[此](#schema)節。
	
####建立 ContentKeyAuthorizationPolicies
	
建立 ContentKeyAuthorizationPolicies，如[這裡](#ContentKeyAuthorizationPolicies2)所示。

####建立 ContentKeyAuthorizationPolicyOptions
	
要求：

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 3.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423583561&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=5eZnkOsSv%2fLLEKmS%2bWObBlsNYyee8BQlp%2bUYbjugcJg%3d
	x-ms-version: 2.11
	x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 1525
	
	{"Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type="ContentEncryptionKeyFromHeader" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1"><AlternateVerificationKeys><TokenVerificationKey i:type="SymmetricVerificationKey"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testacs.com/</Issuer><PrimaryVerificationKey i:type="SymmetricVerificationKey"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil="true" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

回應：
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1706
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae42bbeae-de42-4077-90e9-a844f297ef70')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
	request-id: ccf8a4ba-731e-4124-8192-079592c251cc
	x-ms-request-id: ccf8a4ba-731e-4124-8192-079592c251cc
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 09:58:47 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e42bbeae-de42-4077-90e9-a844f297ef70","Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type="ContentEncryptionKeyFromHeader" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1"><AlternateVerificationKeys><TokenVerificationKey i:type="SymmetricVerificationKey"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testacs.com/</Issuer><PrimaryVerificationKey i:type="SymmetricVerificationKey"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil="true" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

####連結 ContentKeyAuthorizationPolicies 與選項

連結 ContentKeyAuthorizationPolicies 與選項，如[這裡](#ContentKeyAuthorizationPolicies)所示。

####將授權原則加入內容金鑰

將 AuthorizationPolicy 加入 ContentKey，如[這裡](#AddAuthorizationPolicyToKey)所示。


##<a id="types"></a>定義 ContentKeyAuthorizationPolicy 時使用的類型

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
        None = 0,
        PlayReadyLicense = 1,
        BaselineHttp = 2,
    }


##後續步驟
現在，您已設定內容金鑰授權原則，請移至[如何設定資產傳遞原則](media-services-rest-configure-asset-delivery-policy.md)主題。

 

<!---HONumber=July15_HO1-->