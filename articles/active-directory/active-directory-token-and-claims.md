<properties 
   pageTitle="支援的權杖和宣告類型"
   description="可供了解及評估 Azure Active Directory (AAD) 所簽發之 SAML 2.0 和 JSON Web Token (JWT) 權杖中的宣告的指南。"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   services="active-directory" 
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/01/2015"
   ms.author="mbaldwin"/>

# 支援的權杖和宣告類型

本主題旨在幫助您了解和評估 Azure Active Directory (Azure AD) 所簽發之 SAML 2.0 和 JSON Web Token (JWT) 權杖中的宣告。

本主題的開頭是各權杖宣告的說明，並顯示 SAML 權杖和 JWT 權杖中的適當宣告範例。處於預覽狀態的宣告分開列出。結尾是範例權杖，以便您看到內容中的宣告。

Azure 會隨著時間將宣告新增至權杖，以啟用新的案例。一般而言，我們會介紹預覽狀態的宣告，然後在測試期間後將它們轉換成完整的支援。若要準備宣告變更，接受來自 Azure AD 之權杖的應用程式應該忽略不熟悉的權杖宣告，新的宣告才不會中斷應用程式。使用處於預覽狀態的宣告的應用程式不應該相依於這些宣告，而如果此宣告未出現在權杖中，也不應該引發例外狀況。如果應用程式需要 Azure AD 所簽發的 SAML 或 JWT 權杖中未提供的宣告，請利用此頁面底部的「社群新增」一節，建議及討論新的宣告類型。

## 權杖宣告參考

本節列出及說明 Azure AD 傳回的權杖中的宣告。其中包含宣告的 SAML 版本和 JWT 版本以及宣告說明與其用法。宣告會依字母順序列出。

### 應用程式識別碼

應用程式識別碼宣告，用以識別使用權杖來存取資源的應用程式。應用程式代表本身或使用者行事。應用程式識別碼通常代表應用程式物件，但也可以代表 Azure AD 中的服務主體物件。

Azure AD 不支援 SAML 權杖中的應用程式識別碼宣告。

在 JWT 權杖中，應用程式識別碼會出現在 appid 宣告中。

    "appid":"15CB020F-3984-482A-864D-1D92265E8268"

### 對象
權杖的對象是權杖的預定接收者。接收權杖的應用程式必須確認對象值正確無誤，並拒絕任何適用於不同對象的權杖。

對象值是一個字串 -- 通常是所存取資源的基底位址，例如 "https://contoso.com"。在 Azure AD 權杖中，對象是要求權杖之應用程式的應用程式識別碼 URI。當應用程式 (也就是對象) 具有一個以上的應用程式識別碼 URI 時，權杖的 Audience 宣告中的應用程式識別碼 URI 會符合權杖要求中的應用程式識別碼 Uri。在 SAML 權杖中，對象宣告定義於 AudienceRestriction 元素的 Audience 元素中。

    <AudienceRestriction>
    <Audience>https://contoso.com</Audience>
    </AudienceRestriction>

在 JWT 權杖中，對象會出現在 aud 宣告中。

    "aud":"https://contoso.com"

### 應用程式驗證內容類別參考

應用程式驗證內容類別參考宣告指出用戶端的驗證方式。若為公用用戶端，此值為 0。如果使用用戶端識別碼和用戶端密碼，此值為 1。

在 JWT 權杖中，驗證內容類別參考值會出現在 appidacr (應用程式專屬的 ACR 值) 宣告中。

    "appidacr": "0"

### 驗證內容類別參考
驗證內容類別參考宣告指出主體的驗證方式 (相對於應用程式驗證內容類別參考宣告中的用戶端)。值為 "0" 表示使用者驗證不符合 ISO/IEC 29115 的需求。

- 在 JWT 權杖中，驗證內容類別參考宣告會出現在 acr (使用者專屬的 ACR 值) 宣告中。

    "acr": "0"

### 驗證即刻

驗證即刻宣告會記錄發生驗證時的日期和時間。

在 SAML 權杖中，驗證即刻會出現在 AuthnStatement 元素的 AuthnInstant 屬性中。它代表日期和時間 (採用 UTC (Z) 時間)。

    <AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">

在 JWT 權杖中，Azure AD 沒有對等的宣告。

### 驗證方法

驗證方法宣告指出如何驗證權杖的主體。在此範例中，身分識別提供者會使用密碼來驗證使用者。http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password

在 SAML 權杖中，驗證方法值會出現在 AuthnContextClassRef 元素中。

    <AuthnContextClassRef>http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password</AuthnContextClassRef>

在 JWT 權杖中，驗證方法值會出現在 amr 宣告中。

    “amr”: ["pwd"]

###名字

名字宣告會提供使用者的名字 (如 Azure AD 使用者物件上所設定)。

在 SAML 權杖中，名字會出現在 givenname SAML Attribute 元素的宣告中。

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>
    <AttributeValue>Frank<AttributeValue>

在 JWT 權杖中，名字會出現在 given_name 宣告中。

    "given_name": "Frank"

### 群組

群組宣告會提供代表主體群組成員資格的物件識別碼。這些值都是唯一的 (請參閱「物件識別碼」)，而且可安全地用來管理存取權，例如強制授權以存取資源。群組宣告中包含的群組會透過應用程式資訊清單的 "groupMembershipClaims"屬性，針對每個應用程式進行設定。Null 值將會排除所有群組，"SecurityGroup" 值只會包含 Active Directory 安全性群組成員資格，而 "All" 值將會包含安全性群組和 Office 365 通訊群組清單。在任何組態中，群組宣告都代表主體的可轉移群組成員資格。

在 SAML 權杖中，群組宣告會出現在 groups 屬性中。

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
    <AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>

在 JWT 權杖中，群組宣告會出現在 groups 宣告中。

    “groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]

### 身分識別提供者

身分識別提供者宣告記錄驗證權杖主體的身分識別提供者。除非使用者帳戶位於與簽發者不同的租用戶中，否則這個值與 Issuer 宣告值相同。

在 SAML 權杖中，身分識別提供者會出現在 identityprovider SAML Attribute 元素的宣告中。

    <Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>
    <AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>

在 JWT 權杖中，身分識別提供者會出現在 idp 宣告中。

    "idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### IssuedAt

IssuedAt 宣告會儲存權杖簽發的時間。這通常用來測量權杖有效時間。在 SAML 權杖中，IssuedAt 值會出現在 IssueInstant 判斷提示中。

    <Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">

在 JWT 權杖中，IssuedAt 值會出現在 iat 宣告中。此值是以從 1970-01-010:0:0Z 算起的秒數表示 (採用國際標準時間 (UTC))。

    "iat": 1390234181

### Issuer

Issuer 宣告會識別安全性權杖服務 (STS)，而該服務可建構並傳回權杖和 Azure AD 目錄租用戶。在 Azure AD 傳回的權杖中，簽發者為 sts.windows.net。簽發者宣告值中的 GUID 是 Azure AD 目錄的租用戶識別碼。租用戶識別碼是目錄的不可變且可靠的識別碼。

在 SAML 權杖中，Issuer 宣告會出現在 Issuer 元素中。

    <Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>

在 JWT 權杖中，Issuer 會出現在 iss 宣告中。

    "iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### 姓氏

姓氏宣告會提供使用者的姓氏 (如 Azure AD 使用者物件中所定義)。在 SAML 權杖中，姓氏會出現在 surname SAML Attribute 元素的宣告中。

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>
    <AttributeValue>Miller<AttributeValue>

在 JWT 權杖中，姓氏會出現在 family_name 宣告中。

    "family_name": "Miller"

### 名稱

名稱宣告提供人類看得懂的值，用以識別權杖的主體。此值不保證是租用戶中的唯一值，而且僅限用於顯示目的。在 SAML 權杖中，名稱會出現在 Name 屬性中。

    <Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>
    <AttributeValue>frankm@contoso.com<AttributeValue>

在 JWT 宣告中，名稱會出現在 unique_name 宣告中。

    "unique_name": "frankm@contoso.com"

### 物件識別碼

物件識別碼宣告包含 Azure AD 中物件的唯一識別碼。這個值不可變，而且無法重新指派或重複使用，因此您可以用它來安全地執行授權檢查，例如當權杖用於存取資源時。使用物件識別碼來識別 Azure AD 查詢中的物件。在 SAML 權杖中，物件識別碼會出現在 objectidentifier 屬性中。

    <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
    <AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>

在 JWT 權杖中，物件識別碼會出現在 oid 宣告中。

    "oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"

### 角色

角色宣告會提供好記的字串，代表 Azure AD 中主體的應用程式角色指派，而且可用來強制執行角色型存取控制。應用程式角色是透過應用程式資訊清單的 "appRoles" 屬性，針對每個應用程式進行定義。每個應用程式角色的 [值] 屬性就是出現在角色宣告中的值。角色宣告中包含的角色表示透過群組成員資格，直接或間接授與主體的所有應用程式角色。在 SAML 權杖中，roles 宣告會出現在 roles 屬性中。

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">
    <AttributeValue>Admin</AttributeValue>

在 JWT 權杖中，roles 宣告會出現在 roles 宣告中。

    “roles”: ["Admin", … ]

### Scope

權杖範圍指出授與用戶端應用程式的模擬權限。預設權限為 user_impersonation。受保護資源的擁有者可以在 Azure AD 中註冊其他的值。

在 JWT 權杖中，權杖的範圍是在 scp 宣告中指定。

    "scp": "user_impersonation"

### 主旨

權杖的主體是權杖判斷提示其相關資訊的主體，例如應用程式的使用者。這個值不可變，而且無法重新指派或重複使用，因此可用來安全地執行授權檢查，例如當權杖用於存取資源時。因為主體一律存在於 Azure AD 簽發的權杖中，所以建議您將此值使用於一般用途授權系統中。

在 SAML 權杖，權杖的主體是指定於 Subject 元素的 NameID 元素中。NameID 是主體的唯一、不可重複使用的識別碼，而主體可以是使用者、應用程式或服務。

SubjectConfirmation 不是宣告。它會描述如何驗證權杖的主體。"Bearer" 表示主體已經由持有權杖來確認。

    <Subject>
    <NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>
    <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
    </Subject>

在 JWT 權杖中，主體會出現在 sub 宣告中。

    "sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"

### 租用戶識別碼
租用戶識別碼是不可變、無法重複使用的識別碼，用以識別簽發權杖的目錄租用戶。您可以使用此值來存取多租用戶應用程式中的租用戶特定目錄資源。例如，您可以使用此值來識別在圖形 API 呼叫中的租用戶。

在 SAML 權杖中，租用戶識別碼會出現在 tenantid SAML Attribute 元素的宣告中。

    <Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>
    <AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>

在 JWT 權杖中，租用戶識別碼會出現在 tid 宣告中。

    "tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"

### 權杖存留期
權杖存留期宣告定義權杖有效的時間間隔。驗證權杖的服務應確認目前的日期在權杖存留期內。否則，應該拒絕此權杖。此服務最多允許超過權杖存留期範圍五分鐘，以考量 Azure AD 與服務之間的時鐘時間差異 (「時間偏差」)。

在 SAML 權杖中，權杖存留期宣告會使用 NotBefore 和 NotOnOrAfter 屬性定義於 Conditions 元素中。

    <Conditions
    NotBefore="2013-03-18T21:32:51.261Z"
    NotOnOrAfter="2013-03-18T22:32:51.261Z"
    >

在 JWT 權杖，權杖存留期是由 nbf (不早於) 和 exp (到期時間) 宣告所定義。這些宣告的值是以從 1970-01-010:0:0Z 算起的秒數表示 (採用國際標準時間 (UTC))。如需詳細資訊，請參閱 RFC 3339。

    "nbf":1363289634,
    "exp":1363293234

### 使用者主體名稱
使用者主體名稱宣告會儲存使用者主體的使用者名稱。

在 JWT 權杖中，使用者主體名稱會出現在 upn 宣告中。

    "upn": frankm@contoso.com

### 版本
Version 宣告儲存權杖的版本號碼。在 JWT 權杖中，使用者主體名稱會出現在 ver 宣告中。

    "ver": "1.0"

## 權杖範例

本節顯示 Azure AD 所傳回的 SAML 和 JWT 權杖範例。這些範例可讓您查看內容中的宣告。SAML 權杖

這是典型的 SAML 權杖範例。

	<?xml version="1.0" encoding="UTF-8"?>
	<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
	  <t:Lifetime>
		<wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
		<wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
	  </t:Lifetime>
	  <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
		<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
		  <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
		</EndpointReference>
	  </wsp:AppliesTo>
	  <t:RequestedSecurityToken>
		<Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
		  <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
		  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
			<ds:SignedInfo>
			  <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
			  <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
			  <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
				<ds:Transforms>
				  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
				  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
				</ds:Transforms>
				<ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
				<ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
			  </ds:Reference>
			</ds:SignedInfo>
			<ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
			<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
			  <X509Data>
				<X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
			  </X509Data>
			</KeyInfo>
		  </ds:Signature>
		  <Subject>
			<NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
			<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
		  </Subject>
		  <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
			<AudienceRestriction>
			  <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
			</AudienceRestriction>
		  </Conditions>
		  <AttributeStatement>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
			  <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
			  <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
			  <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
			  <AttributeValue>Admin</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
			  <AttributeValue>Sample</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
			  <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
			  <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
			  <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
			  <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
			  <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
			  <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
			  <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
			  <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
			  <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
			  <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
			  <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
			  <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
			  <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
			  <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
			</Attribute>
		  </AttributeStatement>
		  <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
			<AuthnContext>
			  <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
			</AuthnContext>
		  </AuthnStatement>
		</Assertion>
	  </t:RequestedSecurityToken>
	  <t:RequestedAttachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedAttachedReference>
	  <t:RequestedUnattachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedUnattachedReference>
	  <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
	  <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
	  <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
	</t:RequestSecurityTokenResponse>

### JWT 權杖 - 使用者模擬

這是在使用者模擬 Web 流程中使用的典型 JSON Web 權杖 (JWT) 範例。除了宣告以外，權杖包含 **ver** 和 **appidacr** 中的版本號碼、驗證內容類別參考 (這表示用戶端的驗證方式)。若為公用用戶端，此值為 0。如果使用用戶端識別碼或用戶端密碼，此值為 1。

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

##另請參閱

[Azure Active Directory 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn151124.aspx)
 

<!---HONumber=62-->