<properties 
	pageTitle="Azure API 管理原則參考文件" 
	description="了解可用來設定 API 管理的原則。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="sdanie"/>

# Azure API 管理原則參考文件

本節提供 [API 管理原則參考文件][]中原則的索引。如需有關新增和設定原則的資訊，請參閱 [API 管理中的原則][]。

如果原則不另行指定，則可以在任何 API 管理原則中，使用原則運算式做為屬性值或文字值。某些原則是以原則運算式為基礎，例如[控制流程][]和[設定變數][]原則。如需詳細資訊，請參閱[進階原則][]和[原則運算式][]。

## 原則參考索引

-	[存取限制原則][]
	-	[檢查 HTTP 標頭][] - 強制必須存在和/或強制採用 HTTP 標頭的值。
	-	[限制呼叫率][] - 限制呼叫數和/或頻寬耗用率以防止 API 使用量暴增。
	-	[限制呼叫端 IP][] - 篩選 (允許/拒絕) 來自特定 IP 位址和/或位址範圍的呼叫。
	-	[設定使用量配額][] - 讓您可以強制採用可更新或有存留期的呼叫量和/或頻寬配額。
	-	[驗證 JWT][] - 強制擷取自指定 HTTP 標頭或指定查詢參數的 JWT 必須存在且有效。
-	[進階原則][]
	-	[控制流程][] - 根據布林值[運算式][]的評估結果，有條件地套用原則陳述式。
	-	[設定變數][] - 保存具名 [context][] 變數中的值，供日後存取使用。
-	[驗證原則][]
	-	[使用基本驗證進行驗證][] - 使用基本驗證來驗證後端服務。
	-	[使用用戶端憑證進行驗證][] - 使用用戶端憑證來驗證後端服務。
-	[快取原則][] 
	-	[從快取中取得][] - 執行快取查閱並傳回有效的快取回應 (如果有的話)。
	-	[儲存至快取][] - 根據指定的快取控制組態來快取回應。
-	[跨網域原則][] 
	-	[允許跨網域呼叫][] - 將 API 設為可供 Adobe Flash 和 Microsoft Silverlight 瀏覽器型用戶端存取。
	-	[CORS][] - 將跨原始來源資源分享 (CORS) 支援加入至操作或 API，以允許來自瀏覽器型用戶端的跨網域呼叫。
	-	[JSONP][] - 將 JSON 與補充的 (JSONP) 支援加入至操作或 API，以允許來自 JavaScript 瀏覽器型用戶端的跨網域呼叫。
-	[轉換原則][] 
	-	[將 JSON 轉換成 XML][] - 將要求或回應內文從 JSON 轉換成 XML。
	-	[將 XML 轉換成 JSON][] - 將要求或回應內文從 XML 轉換成 JSON。
	-	[在內文中尋找並取代字串][] - 尋找要求或回應子字串，並替換為其他子字串。
	-	[遮罩內容中的 URL][] - 重寫 (遮罩) 回應內文和位置標頭中的連結，使它們經由 Proxy 指向同等的連結。
	-	[設定後端服務][] - 變更傳入要求的後端服務。
	-	[設定本文][] - 設定傳入和傳出要求的訊息本文。
	-	[設定 HTTP 標頭][] - 指派值給現有的回應及/或要求標頭，或加入新的回應及/或要求標頭。
	-	[設定查詢字串參數][] - 新增、取代值或刪除要求查詢字串參數。
	-	[重寫 URL][] - 將要求 URL 從公用格式轉換成 Web 服務所需的格式。

## 後續步驟

如需原則運算式的詳細資訊，請觀看以下影片。

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[存取限制原則]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[檢查 HTTP 標頭]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[限制呼叫率]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[限制呼叫端 IP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[設定使用量配額]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[驗證 JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[進階原則]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[控制流程]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[設定變數]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[運算式]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables

[驗證原則]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[使用基本驗證進行驗證]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[使用用戶端憑證進行驗證]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[快取原則]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[從快取中取得]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[儲存至快取]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[跨網域原則]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[允許跨網域呼叫]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[轉換原則]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[將 JSON 轉換成 XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[將 XML 轉換成 JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[在內文中尋找並取代字串]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[遮罩內容中的 URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[設定後端服務]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[設定本文]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[設定 HTTP 標頭]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[設定查詢字串參數]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[重寫 URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[API 管理中的原則]: api-management-howto-policies.md
[API 管理原則參考文件]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[原則運算式]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 

<!---HONumber=62-->