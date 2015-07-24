<properties 
	pageTitle="" 
	description="說明您該如何在 Cordova 專案中開始使用 Azure 行動服務" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="patshea123"/>

# 開始使用行動服務 (Cordova 專案)

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

依照這些範例中之程式碼執行的第一個步驟取決於您要連線的行動服務類型。

- 對於 JavaScript 後端行動服務，請建立名為 TodoItem 的資料表。若要建立資料表，請在 [伺服器總管] 的 [Azure] 節點下尋找行動服務，在行動服務的節點上按一下滑鼠右鍵以開啟內容功能表，然後選擇 [建立資料表]。輸入 "TodoItem" 做為資料表名稱。

- 若要使用 .NET 後端行動服務，在 Visual Studio 為您建立的預設專案範本中已有 TodoItem 資料表，但您必須將它發行到 Azure。若要發行它，請開啟行動服務專案的內容功能表，然後選擇 [發行 Web]。接受預設值，然後選擇 [發行] 按鈕。



#####建立資料表的參考

下列程式碼可取得資料表的參考，而該資料表包含 TodoItem 的資料，可在後續操作中用來讀取和更新資料表。建立行動服務時會自動建立 TodoItem 資料表。

    var todoTable = mobileServiceClient.getTable('TodoItem');

資料表的權限必須設為「具有應用程式金鑰的任何人」，這些範例才能運作。稍後您可以設定驗證。請參閱[開始使用驗證](mobile-services-html-get-started-users.md) (英文)。

#####將項目加入資料表

將新的項目插入至資料表。自動會建立 id (字串類型的 GUID) 作為新資料列的主要索引鍵。在傳回的 [Promise](https://msdn.microsoft.com/library/dn802826.aspx) 物件上呼叫 `done()` 方法，以取得所插入物件的複本，並處理任何錯誤。

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }
    
    var items = new Array();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            items.push(item)
        });
    };

#####讀取或查詢資料表

下列程式碼會查詢資料表的所有項目，並依文字欄位排序。您可以在成功處理常式中新增程式碼來處理查詢結果。在此案例中是更新項目的本機陣列。

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
        });

您可以使用 where 方法來修改查詢。以下範例是濾除已完成的項目。

    todoTable.where(function () {
            return (this.complete === false);
        })
        .read().done(function (results) {
            items = results.slice();
        });

有關更多您可以使用的查詢範例，請參閱 [query](http://msdn.microsoft.com/library/azure/jj613353.aspx) 物件。

#####更新資料表項目

更新資料表中的資料列。在此程式碼中，當行動服務回應時，項目就從清單中移除。在傳回的 [Promise](https://msdn.microsoft.com/library/dn802826.aspx) 物件上呼叫 `done()` 方法，以取得所插入物件的複本，並處理任何錯誤。

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

#####刪除資料表項目

使用 **del** 方法刪除資料表中的資料列。在傳回的 [Promise](https://msdn.microsoft.com/library/dn802826.aspx) 物件上呼叫 `done()` 方法，以取得所插入物件的複本，並處理任何錯誤。

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

[深入了解行動服務](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=July15_HO2-->