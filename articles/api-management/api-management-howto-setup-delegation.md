<properties 
	pageTitle="如何委派使用者註冊和產品訂閱" 
	description="了解如何在 Azure API Management 中將使用者註冊和產品訂閱委派給第三方。" 
	services="api-management" 
	documentationCenter="" 
	authors="antonba" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="antonba"/>

# 如何委派使用者註冊和產品訂閱

委派可讓您使用現有的網站來處理開發人員登入/註冊和產品訂閱，而非使用開發人員入口網站中的內建功能。這樣可讓您的網站擁有使用者資料，並以自訂方式來執行這些步驟的驗證。

## <a name="delegate-signin-up"> </a>委派開發人員登入和註冊

若要將開發人員登入和註冊委派給您現有的網站，您必須在網站上建立特殊的委派端點，作為從 API 管理開發人員入口網站起始的任何這類要求的進入點。

最終工作流程如下所示：

1. 開發人員在 API 管理開發人員入口網站按一下登入或註冊連結
2. 瀏覽器重新導向至委派端點
3. 委派端點再轉而重新導向至或呈現 UI，要求使用者登入或註冊
4. 成功時，將使用者重新導向回到他們所來自的 API 管理開發人員入口網站頁面


首先，請設定 API 管理透過委派端點來傳遞要求。在 API 管理發行者入口網站中，按一下 [**安全性**]，然後按一下 [**委派**] 索引標籤。按一下核取方塊以啟用 [Delegate sign-in & sign-up]。

![Delegation page][api-management-delegation-signin-up]

* 決定特殊委派端點的 URL，並在 [**Delegation endpoint URL**] 欄位中輸入。 

* 在 [**Delegation authentication key**] 欄位中輸入密碼，用來計算提供給您驗證的簽章，以確定要求確實來自 Azure API 管理。您可以按一下 [**產生**] 按鈕，讓 API 管理為您隨機產生金鑰。

現在您需要建立「**委派端點**」。必須執行一些動作：

1. 接收下列形式的要求：

	> *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*

	登入/註冊案例的查詢參數：- **operation**：識別委派要求的類型；此案例中只能是 **SignIn** - **returnUrl**：使用者按一下登入或註冊連結的頁面 URL - **salt**：特殊的 salt 字串，用於計算安全性雜湊 - **sig**：已經過計算的安全性雜湊，用於和您已計算的雜湊進行比較

2. 確認要求來自 Azure API 管理 (選擇性，但基於安全性理由，強烈建議這麼做)

	* 根據 **returnUrl** 和 **salt** 查詢參數，計算字串的 HMAC-SHA512 雜湊 ([以下提供範例程式碼])：
        > **returnUrl**
		 
	* 比較以上計算的雜湊和 **sig** 查詢參數的值。如果兩個雜湊相符，則繼續下一步，否則拒絕要求。

2. 確認您收到的是登入/註冊要求：**operation** 查詢參數會設為 "**SignIn**"。

3. 顯示 UI 讓使用者登入或註冊

4. 如果使用者要註冊，您必須在 API 管理中為他們建立對應的帳戶。使用 API Management REST API [建立使用者]。這樣做時，請確定您所設定的使用者識別碼與使用者存放區中的識別碼相同，或與您可追蹤的識別碼相同。

5. 成功驗證使用者之後：

	* 透過 API Management REST API [要求單一登入 (SSO) 權杖]

	* 將 returnUrl 查詢參數附加至您從上述 API 呼叫收到的 SSO URL：
		> 例如 https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url

	* 將使用者重新導向至以上產生的 URL

除了 **SignIn** 作業之外，您也可以遵循上述步驟來執行帳戶管理，並使用以下其中一項作業。

-	**ChangePassword**
-	**ChangeProfile**
-	**CloseAccount**

您必須傳遞下列查詢參數，供帳戶管理作業使用。

-	**operation**：識別委派要求的類型 (ChangePassword、ChangeProfile 或 CloseAccount)
-	**userId**：要管理之帳戶的使用者 ID
-	**salt**：特殊 salt 字串，用於計算安全性雜湊
-	**sig**：已經過計算的安全性雜湊，用於和您已計算的雜湊進行比較

## <a name="delegate-product-subscription"> </a>委派產品訂閱

委派產品訂閱的作法類似於委派使用者登入/註冊。最終工作流程如下所示：

1. 開發人員在 API 管理開發人員入口網站中選取產品，然後按一下 [訂閱] 按鈕
2. 瀏覽器重新導向至委派端點
3. 委派端點執行必要的產品訂閱步驟 - 這由您決定，可能需要重新導向至另一個頁面來要求帳單資訊、詢問其他問題，或只是儲存資訊而不要求任何使用者動作
4. 成功時，將使用者重新導向回到他們所來自的 API 管理開發人員入口網站頁面

若要啟用此功能，請在 [委派] 頁面按一下 [Delegate product subscription]。

然後，確定委派端點會執行下列動作：


1. 接收下列形式的要求：

	> *http://www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*

	產品訂閱案例的查詢參數：- **operation**：識別委派要求的類型。產品訂閱要求的有效選項為：- "Subscribe"：用提供的 ID (請參閱下述) 為使用者訂閱特定產品的要求 - "Unsubscribe"：為使用者取消訂閱產品的要求 - "Renew"：續訂的要求 (例如，可能即將過期) - **productId**：使用者要求訂閱的產品 ID - **userId**：提出要求的使用者 ID - **salt**：用於計算安全性雜湊的特殊 salt 字串 - **sig**：已經過計算的安全性雜湊，用於和您已計算的雜湊進行比較


2. 確認要求來自 Azure API 管理 (選擇性，但基於安全性理由，強烈建議這麼做)

	* 根據 **productId**、**userId** 和 **salt** 查詢字串，計算字串的 HMAC-SHA512：
		> **productId****userId**
		 
	* 比較以上計算的雜湊和 **sig** 查詢參數的值。如果兩個雜湊相符，則繼續下一步，否則拒絕要求。
	
3. 根據 **operation** 中要求的操作類型 (例如帳單、進一步的問題等)，執行任何產品訂閱處理

4. 當使用者在您這邊成功訂閱產品時，[呼叫 REST API 來訂閱產品]，讓使用者訂閱 API 管理產品。

5. 收到要求時，將使用者重新導向回提供的 **returnUrl**。

## <a name="delegate-example-code"> </a> 範例程式碼 ##

這些程式碼範例示範如何取得*委派驗證金鑰* (在 API 管理入口網站的 [委派] 畫面中設定)，以建立隨後用於驗證簽章的 HMAC，藉此證明所傳遞之 returnUrl 的有效性。相同的程式碼稍微修改一下後，也適用於 productId 和 userId。

**產生 returnUrl 雜湊的 C# 程式碼**

    using System.Security.Cryptography;

    string key = "delegation validation key";
    string returnUrl = "returnUrl query parameter";
    string salt = "salt query parameter";
    string signature;
    using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
    {
        signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
        // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
        // compare signature to sig query parameter
    }


**產生 returnUrl 雜湊的 NodeJS 程式碼**

	var crypto = require('crypto');
	
	var key = 'delegation validation key'; 
	var returnUrl = 'returnUrl query parameter';
	var salt = 'salt query parameter';
	
	var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
	var digest = hmac.update(salt + '\n' + returnUrl).digest();
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
	
	var signature = digest.toString('base64');

## 後續步驟

如需委派的詳細資訊，請觀看以下影片。

> [AZURE.VIDEO delegating-user-authentication-and-product-subscription-to-a-3rd-party-site]

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[要求單一登入 (SSO) 權杖]: http://go.microsoft.com/fwlink/?LinkId=507409
[建立使用者]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[呼叫 REST API 來訂閱產品]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[以下提供範例程式碼]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png

<!---HONumber=62-->