<properties
	pageTitle="行動服務中使用 .NET 後端的使用者服務端授權 |行動開發人員中心"
	description="了解如何在 Azure 行動服務的 .NET 後端為使用者授權。"
	services="mobile-services"
	documentationCenter="windows"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.topic="article"
	ms.devlang="dotnet"
	ms.date="05/10/2015"
	ms.author="krisragh"/>

# 在行動服務中的使用者服務端授權

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Any | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

本主題說明如何使用伺服器端邏輯來授權使用者。在此教學課程中，您會修改資料表控制器、根據使用者識別碼篩選查詢，以及只提供使用者對自己資料的存取權。根據使用者識別碼篩選使用者查詢結果是最基本的授權形式。根據特定案例，您也可能也會想要建立使用者或角色資料表，以便追蹤更詳細的使用者授權資訊，例如使用者可以存取哪些端點。

本教學課程是根據＜行動服務快速入門＞，並且以[將驗證新增至現有的行動服務應用程式]教學課程為基礎而建立。請先完成[將驗證新增至現有的行動服務應用程式]。

## <a name="register-scripts"></a>修改資料存取方法

1. 在 Visual Studio 中開啟行動專案，展開 DataObjects 資料夾，然後開啟 **TodoItem.cs**。**TodoItem** 類別可定義資料物件，而且您需要新增 **UserId** 屬性才能用於篩選。將下列新 UserId 屬性新增至 **TodoItem** 類別：

		public string UserId { get; set; }

	>[AZURE.NOTE]若要進行此資料模型變更，並保有資料庫的現有資料，必須使用 [Code First 移轉](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)。

2. 在 Visual Studio 中，展開 Controllers 資料夾，然後開啟 **TodoItemController.cs**。尋找 **PostTodoItem** 方法，並將下列程式碼新增至方法開頭。這個程式碼會將已驗證使用者的使用者識別碼新增至項目 (在它插入到 TodoItem 資料表之前)。

			// Get the logged in user
			var currentUser = User as ServiceUser;

			// Set the user ID on the item
			item.UserId = currentUser.Id;

3. 找出 **GetAllTodoItems** 方法，並使用下行程式碼行取代現有的 **return** 陳述式：This query filters the returned TodoItem objects so that each user only receives the items that they inserted.

				// Get the logged in user
				var currentUser = User as ServiceUser;

				return Query().Where(todo => todo.UserId == currentUser.Id);

4. 將行動服務專案重新發佈至 Azure。


## <a name="test-app"></a>測試應用程式

1. 請注意，現在當您執行您的用戶端應用程式時，雖然資料庫中已經有來自先前教學課程的項目，並不會傳回任何項目。這是因為先前的項目插入時沒有使用者識別碼資料行，現在則具有 Null 值。

2. 如果您有其他登入帳戶，請驗證使用者只能看到他們自己的資料，方法是關閉並刪除應用程式，然後重新執行。顯示登入認證對話方塊時，請輸入不同的登入，然後驗證是否不會顯示在前一次登入下所輸入的項目。



<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[將驗證新增至現有的行動服務應用程式]: mobile-services-dotnet-backend-ios-get-started-users.md
 

<!---HONumber=July15_HO1-->