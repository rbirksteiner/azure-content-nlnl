<properties 
	writer="ricksal" 
	pageTitle="從 Android 用戶端呼叫自訂 API | 行動開發人員中心" 
	description="了解如何定義自訂 API，然後從使用 Microsoft Azure 行動服務的 Android 應用程式呼叫它。" 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="ricksal"/>

# 從用戶端呼叫自訂 API

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

本主題將示範如何從 Android 應用程式呼叫自訂 API。自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

本主題中所建立的自訂 API 可讓您傳送單一 POST 要求，該要求會將行動服務的資料表中所有 todo 項目的「已完成」旗標設定為 `true`。若沒有此自訂 API，用戶端必須傳送個別要求，來為資料表中的每個 todo 項目更新旗標。

將此功能新增至您在完成[開始使用行動服務]或[開始使用資料]教學課程時所建立的應用程式。

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務]。

## <a name="define-custom-api"></a>定義自訂 API

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-android-call-custom-api](../../includes/mobile-services-android-call-custom-api.md)]

## 後續步驟

您已建立自訂 API，並從您的 Android 應用程式呼叫此 API，現在請考慮搜尋有關下列行動服務主題的更多資訊：

* [行動服務伺服器指令碼參照] <br/> (英文) 深入了解建立自訂 API。

* [在來源控制中儲存伺服器指令碼] <br/>了解如何使用來源控制功能，來更輕鬆及安全地開發並發佈自訂 API 指令碼。

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[行動服務伺服器指令碼參照]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[開始使用行動服務]: mobile-services-dotnet-backend-android-get-started.md
[開始使用資料]: mobile-services-dotnet-backend-android-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-android-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-android-get-started-push.md

[在來源控制中儲存伺服器指令碼]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO1-->