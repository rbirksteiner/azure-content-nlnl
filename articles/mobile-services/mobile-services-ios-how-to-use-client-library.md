<properties
	pageTitle="如何使用適用於 Azure 行動服務的 iOS 用戶端程式庫"
	description="如何使用適用於行動服務的 iOS 用戶端程式庫"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="krisragh"/>

# 如何使用適用於 Azure 行動服務的 iOS 用戶端程式庫

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

本指南說明如何使用適用於 Azure 行動服務 [iOS SDK] 執行一般案例。如果您不熟悉行動服務，請先完成[行動服務快速入門]或[將行動服務新增至現有的應用程式]來設定您的帳戶、建立資料表，以及建立行動服務。

> [AZURE.NOTE]本指南使用最新的 [iOS 行動服務 SDK](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409)。如果您的專案使用舊版的 SDK，請先升級 Xcode 中的架構。

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>設定和必要條件

本指南假設您已建立包含資料表的行動服務。如需詳細資訊，請參閱[建立資料表]，或重複使用在[行動服務快速入門]或[將行動服務新增至現有的應用程式]中建立的 `TodoItem` 資料表。本指南假設資料表的結構描述與這些教學課程中的資料表相同。本指南也假設您的 Xcode 參考 `WindowsAzureMobileServices.framework` 並匯入 `WindowsAzureMobileServices/WindowsAzureMobileServices.h`。

##<a name="create-client"></a>作法：建立行動服務用戶端

若要在專案中存取 Azure 行動服務，請建立 `MSClient` 用戶端物件。將 `AppUrl` 和 `AppKey` 分別取代為行動服務 URL 和應用程式金鑰儀表板值。

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" applicationKey:@"AppKey"];
```

##<a name="table-reference"></a>作法：建立資料表參考

若要存取或更新 Azure 行動服務的資料，請建立資料表的參考。將 `TodoItem` 取代為您的資料表名稱。

```
	MSTable *table = [client tableWithName:@"TodoItem"];
```

##<a name="querying"></a>作法：查詢資料

若要建立資料庫查詢，請查詢 `MSTable` 物件。下列查詢會取得 `TodoItem` 中的所有項目並記錄每個項目的文字。

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="filtering"></a>作法：篩選傳回的資料

若要篩選結果，有許多可用的選項。

若要使用述詞篩選，請使用 `NSPredicate` 和 `readWithPredicate`。下列篩選器傳回的資料只尋找未完成的待辦事項。

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table and update the items property with the results from the service
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="query-object"></a>作法：使用 MSQuery

若要執行複雜的查詢 (包括排序和分頁)，請使用述詞直接建立 `MSQuery` 物件：

```
    MSQuery *query = [table query];
    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

`MSQuery` 可讓您控制下列幾種查詢行為。在其上呼叫 `readWithCompletion` 執行 `MSQuery` 查詢，如下一個範例所示。* 指定結果的順序 * 限制要傳回的欄位 * 限制要傳回的記錄數 * 指定回應中的總計數 * 指定要求中的自訂查詢字串參數 * 套用額外函數


## <a name="sorting"></a>做法：使用 MSQuery 排序資料

我們來看一下範例如何排序結果。若要先按照 `text` 欄位遞增排序，然後按照 `completion` 欄位遞減排序，請如下叫用 `MSQuery`：

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

## <a name="paging"></a>作法：使用 MSQuery 以分頁方式傳回資料

行動服務會限制單一回應中可傳回的記錄數。若要控制顯示給使用者的記錄數，您必須實作分頁系統。您可以使用 **MSQuery** 物件的下列三個屬性來執行分頁。

```
+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`
```

在下列範例中，簡單函數會向伺服器要求 5 筆記錄，然後將這些記錄附加到先前載入記錄的本機集合：

```
// Create and initialize these properties
@property (nonatomic, strong)   NSMutableArray *loadedItems; // Init via [[NSMutableArray alloc] init]
@property (nonatomic)   				BOOL moreResults;
```

```
-(void)loadResults
{
    MSQuery *query = [self.table query];

    query.includeTotalCount = YES;
    query.fetchLimit = 5;
    query.fetchOffset = self.loadedItems.count;


    [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(!error) {
            // Add the items to our local copy
            [self.loadedItems addObjectsFromArray:result.items];

            // Set a flag to keep track if there are any additional records we need to load
            self.moreResults = (self.loadedItems.count <= result.totalCount);
        }
    }];
}

```

## <a name="selecting"></a><a name="parameters"></a>作法：使用 MSQuery 限制欄位和展開查詢字串參數

若要限制在查詢中傳回的欄位，請在 **selectFields** 屬性中指定欄位的名稱。這僅會傳回文字和已完成欄位：

```
	query.selectFields = @[@"text", @"completed"];
```

若要在伺服器要求中包含額外的查詢字串參數 (例如有某個自訂的伺服器端指令碼使用這些參數)，請如下填入 `query.parameters`：

```
	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};
```

##<a name="inserting"></a>作法：插入資料

若要插入新的資料表資料列，請建立新的 `NSDictionary` 並叫用 `table insert`。行動服務會根據 `NSDictionary` 自動產生新的資料欄 (如果未啟用[動態結構描述])。

如果未提供 `id`，則後端會自動產生新的唯一識別碼。提供您自己的 `id`，以使用電子郵件地址、使用者名稱或您自己自訂的值作為識別碼。提供您自己的識別碼可以讓聯結和商務導向的資料庫邏輯變得更容易。

```
	NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
	[self.table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
						NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
		}
	}];
```

##<a name="modifying"></a>作法：修改資料

若要更新現有的資料列，請修改項目並呼叫 `update`：

```
	NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
	[newItem setValue:@"Updated text" forKey:@"text"];
	[self.table update:newItem completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

或者，提供資料列識別碼和更新的欄位：

```
	[self.table update:@{@"id":@"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete":@YES} completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

進行更新時，至少必須設定 `id` 屬性。

##<a name="deleting"></a>作法：刪除資料

若要刪除項目，請叫用 `delete` 搭配項目：

```
	[self.table delete:item completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

或者，提供資料列識別碼來進行刪除：

```
	[self.table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

進行刪除時，至少必須設定 `id` 屬性。


##<a name="authentication"></a>作法：驗證使用者

Azure 行動服務支援各種識別提供者。如需基本教學課程，請參閱[驗證]。

Azure 行動服務支援兩個驗證工作流程：

- **伺服器管理的登入**：Azure 行動服務會代表您的應用程式管理登入程序。它會顯示提供者專屬的登入頁面，並且使用選擇的提供者進行驗證。

- **用戶端管理的登入**：_應用程式_向識別提供者要求權杖，然後向 Azure 行動服務出示此權杖以進行驗證。

當驗證成功時，您會取回具使用者識別碼值和驗證權杖的使用者物件。若要使用此使用者識別碼來授權使用者，請參閱[伺服器端授權]。若要將資料表存取權限制為僅通過驗證的使用者，請參閱[權限]。

### 伺服器管理的登入

以下是您可以新增伺服器管理的登入至[行動服務快速入門]專案的方式；您可以為其他專案使用類似的程式碼。如需詳細資訊，以及查看有作用的端對端範例，請參閱[驗證]。

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

### 用戶端管理的登入 (單一登入)

您可以在行動服務用戶端外部執行登入程序來啟用單一登入，或者您的應用程式是否要直接連絡識別提供者。在這類情況下，您可以透過提供從支援的識別提供者獨立取得的權杖來登入行動服務。

下列範例會使用 [Live Connect SDK] 來啟用 iOS 應用程式的單一登入。此程式碼假設您已在控制站中有名為 `liveClient` 的 **LiveConnectClient** 執行個體，且使用者已登入。

```
	[client loginWithProvider:@"microsoftaccount"
		token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
		completion:^(MSUser *user, NSError *error) {
				// Handle success and errors
	}];
```

##<a name="caching-tokens"></a>作法：快取驗證權杖

我們來看看如何在[行動服務快速入門]專案中快取權杖；您可以對任何專案套用類似步驟。[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="errors"></a>作法：處理錯誤

呼叫行動服務時，completion 區塊會包含 `NSError *error` 參數。發生錯誤時，此參數便會傳回非 Nil。您應檢查程式碼中的此參數，並視需要處理錯誤。

[`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) 檔案定義 `MSErrorResponseKey`、`MSErrorRequestKey` 和 `MSErrorServerItemKey` 常數以取得有關錯誤的更多資料。此外，檔案也定義每個錯誤代碼的常數。如需如何使用這些常數的範例，請參閱[衝突處理常式]了解 `MSErrorServerItemKey` 和 `MSErrorPreconditionFailed` 的用法。

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[將行動服務新增至現有的應用程式]: /develop/mobile/tutorials/get-started-data
[行動服務快速入門]: /develop/mobile/tutorials/get-started-ios
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[驗證]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[權限]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[伺服器端授權]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[動態結構描述]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[建立資料表]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[衝突處理常式]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=July15_HO2-->