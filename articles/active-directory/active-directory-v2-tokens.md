<properties
	pageTitle="應用程式模型 v2 | Microsoft Azure"
	description="v2.0 端點所發出的權杖和宣告的類型"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# 應用程式模型 v2.0 預覽版本：權杖參考

V2.0 端點會在每個[驗證流程](active-directory-v2-flows.md)的處理中發出數種安全性權杖。本文件說明每種權杖的格式、安全性特性和內容。

> [AZURE.NOTE]此資訊適用於 v2.0 應用程式模型公開預覽版本。如需有關如何整合公開上市 Azure AD 服務的指示，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

## 權杖的類型

V2.0 端點支援 [OAuth 2.0 授權通訊協定](active-directory-v2-protocols.md)，該通訊協定會使用 access\_token 與 refresh\_token。它也支援透過 [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) 驗證和登入，其引進了第三種類型的權杖 (id\_token)。每個權杖都是以「持有人權杖」表示。

持有人權杖是輕巧型的安全性權杖，授權「持有者」存取受保護的資源。從這個意義上說，「持有者」是可出示權杖的任何一方。雖然某一方必須先向 Azure AD 驗證以收到持有者權杖，但如果傳輸和儲存時未採取必要的步驟來保護權杖，它可能會被非預期的一方攔截和使用。雖然某些安全性權杖都有內建的機制，可防止未經授權的人士使用權杖，但持有者權杖沒有這項機制，而必須以安全通道來傳輸，例如傳輸層安全性 (HTTPS)。如果持有人權杖以純文字傳輸，惡意人士可能使用攔截式攻擊來取得權杖，然後未經授權存取受保護的資源。儲存或快取持有者權杖供以後使用時，也適用相同的安全性原則。務必確定您的應用程式以安全的方式傳輸和儲存持有人權杖。關於持有者權杖的其他安全性考量，請參閱 [RFC 6750 第 5 節](http://tools.ietf.org/html/rfc6750)。

許多由 v2.0 端點所簽發的權杖都會實作為 Json Web 權杖或 JWT。JWT 是一種精簡的 URL 安全方法，可在兩方之間傳輸資訊。JWT 中包含的資訊也稱為權杖持有人及主體相關資訊的「宣告」或判斷提示。JWT 中的宣告是為了傳輸而編碼和序列化的 JSON 物件。因為 v2.0 端點所簽發的 JWT 已簽署但未加密，所以您可以輕鬆地檢查 JWT 的內容以便偵錯。有數個工具可以進行這項操作，例如 [calebb.net](https://calebb.net)。如需 JWT 的詳細資訊，您可以參考 [JWT 規格](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)。

## Id\_Token

Id\_token 是您的應用程式使用 [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) 執行驗證時收到的一種登入安全性權杖形式。其以 [JWT](#types-of-tokens) 表示，而且包含可用於讓使用者登入您的應用程式的宣告。您可以適時使用 id\_token 中的宣告 - 通常用來顯示顯示帳戶資訊或在應用程式中進行存取控制決策。V2.0 端點只會簽發一種類型的 id\_token，而不論使用者登入的類型為何，都具有一組一致的宣告。這表示 id\_token 的格式和內容會與個人 Microsoft 帳戶使用者及公司或學校帳戶的格式和內容相同。

Id\_token 已簽署，但這次不加密。當您的應用程式收到 id\_token 時，它必須[驗證簽章](#validating-tokens)以證明權杖的真實性，以及驗證權杖中的幾個宣告來證明其有效性。應用程式所驗證的宣告會視案例需求而有所不同，但您的應用程式必須在每一種案例中執行一些[常見的宣告驗證](#validating-tokens)。

下面提供 id\_token 中宣告的完整詳細資料以及範例 id\_token。請注意，id\_token 中的宣告不依任何特定順序傳回。此外，新的宣告可以隨時引進 id\_token 中 - 您的應用程式不會在引進新的宣告時中斷。以下清單包含您的應用程式在此書寫時能確實地解譯的宣告。練習時，請試著將範例 id\_token 中的宣告貼入 [calebb.net](https://calebb.net) 中進行檢查。如有需要，在 [OpenID Connect 規格](http://openid.net/specs/openid-connect-core-1_0.html)中可找到更多詳細資料。

#### 範例 Id\_Token
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ
19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI0OTIxMDI1My0wYmExLTRhOWEtYTQyNC02MTY5OTlmYWI2MjAiL
CJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTAzMTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMW
YzNzZlL3YyLjAvIiwiaWF0IjoxNDM4NTM1NTQzLCJuYmYiOjE0Mzg1MzU1NDMsImV4cCI6MTQzODUzOTQ0MywidmVyIjoiMi4
wIiwidGlkIjoiYjk0MTAzMTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlIiwib2lkIjoiYTFlYmRkZTgtZTRmOS00NTcx
LWFkOTMtMzA1OWUzNzUwZDIzIiwicHJlZmVycmVkX3VzZXJuYW1lIjoic2FtcGxlLmFkbWluQHN0cm9ja2lzZGV2Lm9ubWljc
m9zb2Z0LmNvbSIsInN1YiI6IjJvMmQ5SVBGVzI5MGo0RVkySXg0RUdoaEtlWnVGaC1LcFhHS2tuZkNxRWMiLCJuYW1lIjoiU2
FtcGxlIEFkbWluIiwibm9uY2UiOiIxMjM0NSIsImNfaGFzaCI6IngxeU92VTZRaXE0Y1lVcVIxeDBvM2cifQ.Qk9exyv04I6a
P6Sju2xNG9O2sj8dG-aEoJeS5dmnjdLo8k1ZzgZd7w-6yCrKXgPh4FJ1YY-08DZnHNmP3oxm3zmEv3RUIBEyTmo3598PRYLWl
vttis1KD5PoNgAyKfHqiOCL5q_Owd0m9oAKDagbJhRVZOS89phllA0AQnaI6hJOKvMsbOYJt-w00y6TXf1Nkzp_Yey8EmRiwN
7gqvudL1UfZ7_UbST2DBjPIyZsv0gT8gpApz6CecCOyX1NNWpUg8ZRkNnOjGL-IMhq4okPCTTfYriOo93z9Y9v6NmaJxV5bBN
V-DIguXSzLVKnnflfSLyvhinsjLKCnu9L3oXHxw
```

#### Id\_Token 中的宣告
| 名稱 | 宣告 | 範例值 | 說明 |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| 對象 | `aud` | `49210253-0ba1-4a9a-a424-616999fab620` | 識別權杖的預定接收者。在 id\_token 中，對象是在應用程式註冊入口網站中指派給應用程式的應用程式識別碼。您的應用程式應驗證此值並拒絕不相符的權杖。 |
| Issuer | `iss` | `https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0/` | 識別可建構並傳回權杖的 Security Token Service (STS)，以及用以使用者驗證的 Azure AD 租用戶。您的應用程式應驗證簽發者宣告，以確保權杖來自 v2.0 端點。它也可以使用宣告的 guid 部分來限制允許登入應用程式的租用戶集合。 |
| 發出時間 | `iat` | `1438535543` | 簽發權杖的時間 (以新紀元 (Epoch) 時間表示)。 |
| 到期時間 | `exp` | `1438539443` | 權杖失效的時間 (以新紀元 (Epoch) 時間表示)。您的應用程式應使用此宣告來驗證權杖存留期的有效性。 |
| 版本 | `ver` | `2.0` | Azure AD 所定義的 id\_token 版本。在應用程式模型 v2.0 中，此值會是 `2.0`。 |
| 租用戶識別碼 | `tid` | `b9410318-09af-49c2-b0c3-653adc1f376e` | 代表使用者來自之 Azure AD 租用戶的 guid。若為公司與學校帳戶，guid 就是使用者所屬組織的不可變租用戶識別碼。若為個人帳戶，此值會是 `9188040d-6c67-4c5b-b112-36a304b66dad`。 |
| 程式碼雜湊 | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 只有在 id\_token 隨著 OAuth 2.0 授權碼一起簽發時，雜湊程式碼才會包含在 id\_token 中。它可用來驗證授權碼的真實性。如需有關執行此驗證的詳細資訊，請參閱 [OpenID Connect 規格](http://openid.net/specs/openid-connect-core-1_0.html)。 |
| 存取權杖雜湊 | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 只有在 id\_token 隨著 OAuth 2.0 存取權杖一起簽發時，存取權杖才會包含在 id\_token 中。它可用來驗證存取權杖的真實性。如需有關執行此驗證的詳細資訊，請參閱 [OpenID Connect 規格](http://openid.net/specs/openid-connect-core-1_0.html)。 |
| Nonce | `nonce` | `12345` | Nonce 是緩和權杖重新執行攻擊的策略。您的應用程式可以使用 `nonce` 查詢參數，在授權要求中指定 Nonce。您在要求中提供的值將會在 id\_token 的 `nonce` 宣告中發出 (未經修改)。這可讓您的應用程式根據在要求上指定的值驗證此值，使應用程式的工作階段與給定的 id\_token 產生關聯。您的應用程式應在 id\_token 驗證程序中執行這項驗證。 |
| 名稱 | `name` | `Leonardo DaVinci` | 名稱宣告提供人類看得懂的值，用以識別權杖的主體。此值不保證是唯一值，可變動，且僅限使用於顯示目的。 |
| 慣用的使用者名稱 | `preferred_username` | `leo@outlook.com` | 用來代表 v2.0 端點中使用者的主要使用者名稱。它可以是電子郵件地址、電話號碼或未指定格式的一般使用者名稱。其值可變動，對指定的使用者而言會隨著時間改變。 |
| 主旨 | `sub` | `AAAAAAAAAAAAAAAAAAAAAOUtxUJsxQtHuMcFCIA1NC0` | 權杖判斷提示其相關資訊的主體，例如應用程式的使用者。這個值不可變，而且無法重新指派或重複使用，因此可用來安全地執行授權檢查，例如當權杖用於存取資源時。因為主體一律存在於 Azure AD 簽發的權杖中，所以建議您將此值使用於一般用途授權系統中。 |
| ObjectId | `oid` | `27cb5cec-7c0c-40b4-a69a-22500b6ea853` | Azure AD 系統中公司或學校帳戶的物件識別碼。不會針對個人 Microsoft 帳戶發出此宣告。 |

<!---
| Not Before | `nbf` | `1438535543` |  The time at which the token becomes valid, represented in epoch time. It is usually the same as the issuance time.  Your app should use this claim to verify the validity of the token lifetime.  |
-->



## 存取權杖

v2.0 端點所簽發的存取權杖有兩種不同的格式。代表公司或學校帳戶簽發的存取權杖為 JWT，類似 id\_token。代表個人 Microsoft 帳戶簽發的存取權杖會採用所謂的「壓縮票證」格式。基於這個理由，在開發過程中，您可能會發現 v2.0 端點所簽發的存取權杖使用不同的字串格式。經過一段時間，存取權杖中的這項差異會從 v2.0 端點中排除。

雖然如此，v2.0 端點所簽發的存取權杖此時僅適用於 Microsoft 服務。在任何目前支援的案例中，您的應用程式應該不需要執行任何的存取權杖驗證或檢查。您可以將存取權杖視為完全不透明 - 這些都是您的應用程式可以在 HTTP 要求中傳遞給 Microsoft 的字串。

在不久的將來，v2.0 端點將引進可讓您的應用程式從其他用戶端接收存取權杖的功能。屆時，此資訊將會更新為應用程式執行存取權杖驗證和其他類似工作所需的資訊。

當您向 v2.0 端點要求存取權杖時，v2.0 端點也會傳回一些有關存取權杖的中繼資料，以供您的應用程式取用。此資訊包括存取權杖的到期時間以及其有效範圍。這可讓您的應用程式執行存取權杖的智慧型快取，而不需剖析存取權杖本身。

## 重新整理權杖

重新整理權杖是您的應用程式可用來在 OAuth 2.0 流程中取得新存取權杖的安全性權杖。它可讓您的應用程式代表使用者長期存取資源，而不需使用者互動。

重新整理權杖屬於多資源權杖。也就是說，在一個資源的權杖要求期間收到的重新整理權杖可以兌換完全不同資源的存取權杖。

若要在權杖回應中接收重新整理權杖，您的應用程式必須要求並獲得 `offline_acesss` 範圍。若要深入了解 `offline_access` 範圍，請參閱[同意和範圍文章](active-directory-v2-scopes.md)。

重新整理權杖永遠對您的應用程式完全不透明。它們是由 Azure AD v2.0 端點所簽發，只能由 v2.0 端點檢查和解譯。它們屬於長效權杖，但不得將您的應用程式撰寫成預期重新整理權杖將持續任何一段時間。重新整理權杖可能會因為各種原因而隨時失效。讓您的應用程式知道重新整理權杖是否有效的唯一方式，就是對 v2.0 端點提出權杖要求以嘗試兌換。

當您兌換重新整理權杖做為新的存取權杖 (而且如果您的應用程式已獲得 `offline_access` 範圍) 時，您會在權杖回應中收到新的重新整理權杖。您應儲存新簽發的重新整理權杖，並取代您使用於要求中的重新整理權杖。這將保證您的重新整理權杖盡可能長期保持有效。

## 驗證權杖

此時，您的應用程式必須執行的唯一權杖驗證就是驗證 id\_token。若要驗證 id\_token，您的應用程式應該驗證 id\_token 簽章和 id\_token 中的宣告。

<!-- TODO: Link -->
我們提供的程式庫和程式碼範例會示範如何輕鬆地處理權杖驗證 - 想要了解基礎程序的使用者可以參閱以下資訊。另外還有多個協力廠商開放原始碼程式庫可用於 JWT 驗證 - 幾乎每個平台和語言都有至少一個選項。

#### 驗證簽章
JWT 包含三個區段 (以 `.` 字元分隔)。第一個區段稱為**標頭**、第二個稱為**主體**，而第三個稱為**簽章**。簽章區段可用來驗證 id\_token 的真實性，您的應用程式才得以信任。

Id\_Token 會使用業界標準非對稱式加密演算法 (例如 RSA 256) 進行簽署。Id\_token 標頭包含用來簽署權杖的金鑰和加密方法相關資訊：

```
{
		typ: "JWT",
		alg: "RS256",
		x5t: "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

`alg` 宣告表示用來簽署權杖的演算法，而 `kid` 或 `x5t` 宣告表示用來簽署權杖的特定公用金鑰。

在任何指定的時間點，v2.0 端點可能會使用一組特定公開-私密金鑰組的其中一個金鑰組來簽署 id\_token。V2.0 端點會定期替換一組可能的金鑰，所以應將您的應用程式撰寫成自動處理這些金鑰變更。檢查 v2.0 端點所用公開金鑰的更新的合理頻率大約為 24 小時。

您可以使用位於下列位置的 OpenID Connect 中繼資料文件來取得驗證簽章所需的簽署金鑰資料：

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`

此中繼資料文件是 JSON 物件，內含幾項實用的資訊，例如執行 OpenID Connect 驗證所需的各種端點的位置。此外，還包含 `jwks_uri`，其提供用來簽署權杖的公用金鑰組的位置。該位置如下所示，但最好使用中繼資料文件並剖析出 `jwks_uri` 來動態抓取該位置：

`https://login.microsoftonline.com/common/discovery/v2.0/keys`

位於此 url 的 JSON 文件包含在該特定時間點使用的所有公開金鑰資訊。您的應用程式可以使用 JWT 標頭中的 `kid` 或 `x5t` 宣告來選取此文件中的哪一個公開金鑰已用來簽署特定權杖。接著可以使用正確的公開金鑰和指定的演算法來執行簽章驗證。

執行簽章驗證已超出本文件的範圍 - 有許多開放原始碼程式庫可協助您這麼做 (如有必要)。

#### 驗證宣告
當您的應用程式在使用者登入時收到 id\_token，還應該對 id\_token 中的宣告執行一些檢查。其中包含：

- **對象**宣告 - 確認 id\_token 預定要提供給您的應用程式。
- **發出時間**和**到期時間**宣告 - 確認 id\_token 尚未過期。
- **簽發者**宣告 - 確認權杖確實是由 v2.0 端點簽發給您的應用程式。
- **Nonce** - 緩和權杖重新執行攻擊。

這些宣告的預期值詳細資料包含在上面的 [id\_token 一節](#id_tokens)中。


## 權杖存留期

下列權杖存留期有助於開發及偵錯應用程式，完全是為了讓您了解而提供的。不得將您的應用程式撰寫成預期任何一個存留期會維持不變 - 它們可以並將在任何指定的時間點變更。

| 權杖 | 存留期 | 說明 |
| ----------------------- | ------------------------------- | ------------ |
| Id\_Token (公司或學校帳戶) | 1 小時 | Id\_Token 的有效期通常為 1 小時。您的 Web 應用程式可將此相同的存留期使用於維護自己與使用者的工作階段 (建議)，或選擇完全不同的工作階段存留期。如果您的應用程式需要取得新的 id\_token，它只需要對 v2.0 授權端點提出新的登入要求。如果使用者有 v2.0 端點的有效瀏覽器工作階段，則可能不需要再次輸入其認證。 |
| Id\_Token (個人帳戶) | 24 小時 | 個人帳戶的 Id\_Token 有效期通常為 24 小時。您的 Web 應用程式可將此相同的存留期使用於維護自己與使用者的工作階段 (建議)，或選擇完全不同的工作階段存留期。如果您的應用程式需要取得新的 id\_token，它只需要對 v2.0 授權端點提出新的登入要求。如果使用者有 v2.0 端點的有效瀏覽器工作階段，則可能不需要再次輸入其認證。 |
| 存取權杖 (公司或學校帳戶) | 1 小時 | 在權杖回應中指出為權杖中繼資料的一部分。 |
| 存取權杖 (個人帳戶) | 1 小時 | 在權杖回應中指出為權杖中繼資料的一部分。可針對不同的存留期設定代表個人帳戶簽發的 Access\_token，但存留期通常為一小時。 |
| 重新整理權杖 (公司或學校帳戶) | 最多 14 天 | 單一重新整理權杖的有效期最多 14 天。不過，重新整理權杖可能會因為任何原因而隨時失效，所以您的應用程式應該繼續嘗試並使用重新整理權杖直到失敗，或直到您的應用程式以新的重新整理權杖取代它為止。如果使用者輸入其認證已 90 天，則重新整理權杖也會失效。 |
| 重新整理權杖 (個人帳戶) | 最多 1 年 | 單一重新整理權杖的有效期最多 1 年。不過，重新整理權杖可能會因為任何原因而隨時失效，所以您的應用程式應該繼續嘗試並使用重新整理權杖直到失敗為止。 |
| 授權碼 (公司或學校帳戶) | 10 分鐘 | 授權碼的存留期特意較短，且應在收到時立即兌換 access\_token 和 refresh\_token。 |
| 授權碼 (個人帳戶) | 5 分鐘 | 授權碼的存留期特意較短，且應在收到時立即兌換 access\_token 和 refresh\_token。代表個人帳戶簽發的授權碼也是單次使用。 |

<!---HONumber=August15_HO7-->