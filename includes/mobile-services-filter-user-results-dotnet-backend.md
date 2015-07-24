

既然需要驗證才能存取 TodoItem 資料表的資料，您可以使用行動服務指派的 userID 值來篩選傳回的資料。

>[AZURE.NOTE]下列方法應在 **[使用者]** 的 **Authorizationlevel** 套用 **AuthorizeLevel** 屬性。這會限制只有經過驗證的使用者才可以存取資料表。

1. 在 Visual Studio 2013，開啟行動服務專案，展開 DataObjects 資料夾，然後開啟 TodoItem.cs 專案檔案。

	TodoItem 類別可定義資料物件，而且您需要新增 UserId 屬性才能使用於篩選。

2. 將下列新 UserId 屬性新增至 **TodoItem** 類別：

		public string UserId { get; set; }

	>[AZURE.NOTE] 使用預設資料庫初始設定式時，每當 Entity Framework 在 Code First 模型定義中偵測到資料模型變更，就會捨棄並重新建立資料庫。若要進行此資料模型變更，並保有資料庫的現有資料，必須使用 Code First Migrations。無法針對 Azure 中的 SQL Database 使用預設的初始設定式。如需詳細資訊，請參閱[如何使用 Code First Migrations 更新資料模型](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)。

3. 在 [方案總管] 中，展開 Controllers 資料夾，開啟 TodoItemController.cs 專案檔案，再加入下列 **using** 陳述式：

		using Microsoft.WindowsAzure.Mobile.Service.Security;

	**TodoItemController** 類別會實作 TodoItem 資料表的資料存取。 
 
4. 尋找 **PostTodoItem** 方法，並將下列程式碼新增至方法開頭：

		// Get the logged-in user.
	    var currentUser = User as ServiceUser;
	
	    // Set the user ID on the item.
	    item.UserId = currentUser.Id;

    在將 UserId 值 (這就是經驗證使用者的使用者識別碼) 插入 TodoItem 資料表之前，此程式碼會先將此值新增至項目。 
	

5. 尋找 **GetAllTodoItems** 方法，並使用下行程式碼取代現有的 **return** 陳述式：

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        return Query().Where(todo => todo.UserId == currentUser.Id);

   	此查詢會篩選傳回的 TodoItem 物件，讓每個使用者只接收他們所插入的項目。 

6. 將行動服務專案重新發佈至 Azure。

<!--HONumber=42-->
