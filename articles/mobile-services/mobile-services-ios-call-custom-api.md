<properties
	pageTitle="如何從 iOS 用戶端呼叫自訂 API"
	description="了解如何定義自訂 API 然後從使用 Azure 行動服務的 iOS 應用程式呼叫它。"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	writer="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/04/2015"
	ms.author="krisragh"/>

# 如何從 iOS 用戶端 (JavaScript 後端) 呼叫自訂 API

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

本主題將示範如何從 iOS 應用程式呼叫自訂 API。自訂 API 可讓您定義具有伺服器功能的自訂端點，但無法對應資料庫的插入、更新、刪除或讀取作業。您可以藉由使用自訂 API 進一步控制訊息，包括 HTTP 標頭和主體格式。

## <a name="define-custom-api"></a>定義自訂 API

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]

## 後續步驟

本主題示範如何使用 **invokeApi** 方法從您的 iOS 應用程式呼叫相當簡單的自訂 API。若要深入了解使用 **invokeApi** 方法，請參閱 [Azure 行動服務中的自訂 API](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx) 文章。

並請考慮更深入了解下列行動服務主題：

* [行動服務伺服器指令碼參照] <br/> (英文) 深入了解建立自訂 API。

* [在來源控制中儲存伺服器指令碼] <br/>了解如何使用來源控制功能，來更輕鬆及安全地開發並發佈自訂 API 指令碼。

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[行動服務伺服器指令碼參照]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Mobile Services Quick Start]: mobile-services-ios-get-started.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md
[Get started with authentication]: mobile-services-ios-get-started-users.md
[Get started with push notifications]: ../mobile-services-ios-get-started-push.md
[在來源控制中儲存伺服器指令碼]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=62-->