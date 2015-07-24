<properties 
 pageTitle="排程器限制、預設值和錯誤碼" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="04/22/2015" 
 ms.author="krisragh"/>
 
# 排程器限制、預設值和錯誤碼

## 排程器配額、限制、預設值和節流

下表描述 Azure 排程器中的每一個主要配額、限制、預設值和節流。

|資源|限制說明|
|---|---|
|**工作大小**|工作大小上限為 16k。如果 PUT 或 PATCH 導致工作大於這些限制，則會傳回「400 要求錯誤」狀態碼。|
|**要求 URL 大小**|要求 URL 的大小上限為 2048 個字元。|
|**彙總標頭大小**|彙總標頭大小上限為 4096 個字元。|
|**標頭計數**|標頭計數上限為 50 個標頭。|
|**內文大小**|內文大小上限為 8192 個字元。|
|**週期範圍**|週期範圍上限為 18 個月。|
|**開始時間**|「開始時間」上限為 18 個月。|
|**工作歷程記錄**|工作歷程記錄中儲存的最大回應本文為 2048 個位元組。|
|**頻率**|在可用工作集合中，預設最大頻率配額為 1 小時，而在標準工作集合中，則為 1 分鐘。最大頻率可在工作集合上設定為低於最大值。工作集合中的所有工作都限制為工作集合上設定的值。如果您嘗試在工作集合上建立頻率高於最大頻率的工作，則要求將失敗，並顯示「409 衝突」狀態碼。|
|**工作**|在可用工作集合中，預設最大工作配額為 5 個工作，而在標準工作集合中，則為 50 個工作。可在工作集合上設定工作數上限。工作集合中的所有工作都限制為工作集合上設定的值。如果您嘗試建立的工作數超過最大工作配額，則要求會失敗，並顯示「409 衝突」狀態碼。|
|**保留工作歷程記錄**|工作歷程記錄最多保留 2 個月。|
|**保留已完成和發生錯誤的工作**|已完成和發生錯誤的工作保留 60 天。|
|**逾時**|HTTP 動作有 30 秒的靜態 (不可設定) 要求逾時。對於執行時間較長的作業，請依照 HTTP 非同步通訊協定；例如，立即傳回 202，但在背景中繼續工作。|

## x-ms-request-id 標頭

每一個對排程器服務提出的要求都會傳回名為 **x-ms-request-id** 的回應標頭。此標頭包含專門識別要求的不透明值。

如果要求一直失敗，而且您已確認要求表達正確，則可以使用此值將錯誤回報給 Microsoft。在您的報告中，包括值 ofx-ms-request-id、提出要求的大約時間、訂用帳戶的識別碼、雲端服務、工作集合及/或工作，以及要求所嘗試的作業類型。

## 排程器狀態和錯誤碼

除了標準 HTTP 狀態碼外，Azure 排程器 REST API 還會傳回延伸的錯誤碼和錯誤訊息。延伸的錯誤碼不會取代標準 HTTP 狀態碼，但會提供其他可採取動作的資訊，可以搭配標準 HTTP 狀態碼使用。

例如，發生 HTTP 404 錯誤可能有許多原因，因此延伸訊息中具有其他資訊可以協助解決問題。如需 REST API 所傳回之標準 HTTP 狀態碼的詳細資訊，請參閱[服務管理狀態和錯誤碼](https://msdn.microsoft.com/library/windowsazure/ee460801.aspx)。服務管理 API 的 REST API 作業會傳回標準 HTTP 狀態碼，其定義在 [HTTP/1.1 狀態碼定義](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)中。下表描述可能由服務傳回的常見錯誤。

|錯誤碼|HTTP 狀態碼|使用者訊息|
|----|----|----|
|MissingOrIncorrectVersionHeader|要求錯誤 (400)|未指定或未正確指定版本控制標頭。|
|InvalidXmlRequest|要求錯誤 (400)|要求內文的 XML 無效或未正確指定。|
|MissingOrInvalidRequiredQueryParameter|要求錯誤 (400)|未對此要求指定必要的查詢參數，或未正確指定。|
|InvalidHttpVerb|要求錯誤 (400)|伺服器無法辨識指定的 HTTP 動詞或不是這個資源的有效動詞。|
|AuthenticationFailed|禁止 (403)|伺服器無法驗證要求。請確認憑證有效，而且與此訂用帳戶相關聯。|
|ResourceNotFound|找不到 (404)|指定的資源不存在。|
|InternalError|內部伺服器錯誤 (500)|伺服器發生內部錯誤。請重試要求。|
|OperationTimedOut|內部伺服器錯誤 (500)|無法在允許的時間內完成作業。|
|ServerBusy|服務無法使用 (503)|伺服器 (或內部元件) 目前無法用來接收要求。請重試您的要求。|
|SubscriptionDisabled|禁止 (403)|訂用帳戶處於已停用狀態。|
|BadRequest|要求錯誤 (400)|參數不正確。|
|ConflictError|衝突 (409)|發生衝突，致使作業無法完成。|
|TemporaryRedirect|暫時重新導向 (307)|無法使用要求的物件。可從回應中的 [位置] 欄位取得物件的新位置的暫時 URI。可在新的 URI 上重複原始要求。|

API 作業也可能傳回管理服務所定義的其他錯誤資訊。此其他錯誤資訊是以回應本文傳回。錯誤回應的本文遵循底下顯示的基本格式。

		<?xml version="1.0" encoding="utf-8"?>  
		<Error>  
			<Code>string-code</Code>  
			<Message>detailed-error-message</Message>  
		</Error>  

## 另請參閱

 [排程器概念、術語及實體階層](scheduler-concepts-terms.md)
 
 [在管理入口網站中開始使用排程器](scheduler-get-started-portal.md)
 
 [Azure 排程器的計劃和計費](scheduler-plans-billing.md)
 
 [如何使用 Azure 排程器建立複雜的排程和進階週期](scheduler-advanced-complexity.md)
 
 [排程器 REST API 參考](https://msdn.microsoft.com/library/dn528946)
 
 [排程器高可用性和可靠性](scheduler-high-availability-reliability.md)
 
 [排程器輸出驗證](scheduler-outbound-authentication.md)

  

<!---HONumber=July15_HO2-->