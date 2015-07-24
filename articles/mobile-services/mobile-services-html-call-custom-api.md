<properties 
	pageTitle="從 HTML 用戶端呼叫自訂 API - 行動服務" 
	description="了解如何定義自訂 API 然後從使用 Azure 行動服務的 HTML 應用程式呼叫它。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="bureado"  
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# 從 HTML 應用程式呼叫自訂 API

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

本主題說明如何從 HTML 應用程式呼叫自訂 API。自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

本主題中所建立的自訂 API 可讓您傳送單一 POST 要求，並將資料表中所有 todo 項目的已完成旗標設定為 `true`。若沒有此自訂 API，用戶端必須傳送個別要求，來為資料表中的每個 todo 項目更新旗標。

本教學課程會以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務]或[新增行動服務至現有應用程式]。

## <a name="define-custom-api"></a>定義自訂 API

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

##<a name="update-app"></a>更新應用程式以呼叫自訂 API

1. 使用您的文字編輯器開啟 index.html 檔案，找出名為 `buttonRefresh` 的 [按鈕] 元素，然後在後面新增以下新元素： 

		<button id="buttonCompleteAll">Complete All</button> 

	這會將新按鈕新增至頁面。

2. 在 page.js 中的 **refreshTodoItems** 函數後面新增下列程式碼：

		var completeAllTodoItems = function () {
			// Asynchronously call the custom API using the POST method.
			client.invokeApi("completeall", {
				body: null,
				method: "post"
			}).done(function (results) {
				var message = results.result.count + " item(s) marked as complete.";
				alert(message);
				refreshTodoItems();
			}, function(error) {
				alert(error.message);
			});
		};

		$('#buttonCompleteAll').click(function () {
			completeAllTodoItems();
		});

	此方法會處理新按鈕的 **Click** 事件。**invokeApi** 方法是在用戶端上呼叫，可將 POST 要求傳送給新的自訂 API。如有任何錯誤，自訂 API 傳回的結果會顯示在訊息對話方塊中。

## <a name="test-app"></a>測試應用程式

1. 重新整理您的瀏覽器。

2. 在應用程式的 [Insert a TodoItem] 中鍵入一些文字，然後按一下 [儲存]。

3. 重複前一個步驟，直到將數個 Todo 項目新增至清單為止。

4. 按一下 [Complete All] 按鈕。出現訊息對話方塊，指出標示為完成的項目數，並重新執行篩選查詢，以便清除清單的所有項目。

## 後續步驟

本主題示範如何使用 **invokeApi** 函數從 HTML/JavaScript 應用程式呼叫相當簡單的自訂 API。若要深入了解如何使用 **invokeApi** 函數，請參閱 [Azure 行動服務中的自訂 API](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx) 文章。

並請考慮更深入了解下列行動服務主題：

* [行動服務伺服器指令碼參照] <br/> (英文) 深入了解建立自訂 API。

* [在來源控制中儲存伺服器指令碼] <br/>了解如何使用來源控制功能，來更輕鬆及安全地開發並發佈自訂 API 指令碼。

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[行動服務伺服器指令碼參照]: http://go.microsoft.com/fwlink/?LinkId=262293
[開始使用行動服務]: mobile-services-html-get-started.md
[新增行動服務至現有應用程式]: mobile-services-html-get-started-data.md
[在來源控制中儲存伺服器指令碼]: mobile-services-store-scripts-source-control.md

<!---HONumber=July15_HO1-->