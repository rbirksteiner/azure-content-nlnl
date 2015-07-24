<properties 
	pageTitle="從 Windows Phone 應用程式呼叫自訂 API - 行動服務" 
	description="了解如何定義自訂 API 然後從使用 Azure 行動服務的 Windows Phone 應用程式呼叫它。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# 從用戶端呼叫自訂 API

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

本主題將說明如何從 Windows Phone 應用程式呼叫自訂 API。自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

本主題中所建立的自訂 API 可讓您傳送單一 POST 要求，並將資料表中所有 todo 項目的已完成旗標設定為 `true`。若沒有此自訂 API，用戶端必須傳送個別要求，來為資料表中的每個 todo 項目更新旗標。

本教學課程以 GetStartedWithData 範例 (簡單的 TodoList 應用程式) 為基礎。在開始本教學課程之前，您必須先完成[將行動服務新增至現有的應用程式](mobile-services-dotnet-backend-windows-phone-get-started-data.md)。

## <a name="define-custom-api"></a>定義自訂 API

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-windows-phone-call-custom-api](../../includes/mobile-services-windows-phone-call-custom-api.md)]


## 後續步驟

本主題示範如何使用 **InvokeApiAsync** 方法從您的 Windows Phone 應用程式呼叫相當簡單的自訂 API。若要詳細了解使用 **InvokeApiAsync** 方法的相關資訊，請參閱 [Azure 行動服務中的自訂 API](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx) 文章。

並請考慮更深入了解下列行動服務主題：

* [行動服務伺服器指令碼參照] <br/> (英文) 深入了解建立自訂 API。

* [在來源控制中儲存伺服器指令碼] <br/>了解如何使用來源控制功能，來更輕鬆及安全地開發並發佈自訂 API 指令碼。

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[行動服務伺服器指令碼參照]: http://go.microsoft.com/fwlink/?LinkId=262293
[在來源控制中儲存伺服器指令碼]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO1-->