<properties 
	pageTitle="如何使用 Xamarin 元件用戶端 - Azure行動服務功能指南" 
	description="了解如何使用適用於 Azure 行動服務的 Xamarin 元件用戶端。" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services" 
	documentationCenter="xamarin"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="lindydonna"/>

# 如何使用適用於 Azure 行動服務的 Xamarin 元件用戶端
[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

本指南將示範如何在 iOS 版和 Android 版 Xamarin 應用程式中，使用適用於 Azure 行動服務的 Xamarin 元件用戶端來執行常見案例。所涵蓋的案例包括查詢資料、插入、更新及刪除資料、驗證使用者以及處理錯誤。如果您不熟悉行動服務，您應考慮首先完成「行動服務快速入門」教學課程 ([Xamarin.iOS][Xamarin.iOS quickstart tutorial]/[Xamarin.Android][Xamarin.Android quickstart tutorial]) 和「開始使用 .NET 中的資料」教學課程 ([Xamarin.iOS][Xamarin.iOS data tutorial]/[Xamarin.Android][Xamarin.Android data tutorial])。快速入門教學課程需要 [Xamarin][Xamarin download] [Mobile Services SDK]，來協助您設定帳戶並建立第一個行動服務。

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

## <a name="setup"></a>設定和必要條件

我們假設您已建立行動服務和資料表。如需詳細資訊，請參閱[建立資料表](http://go.microsoft.com/fwlink/?LinkId=298592)。在本主題使用的程式碼中，資料表的名稱為 `TodoItem`，且其內容包含下列資料行：`id`、`Text` 和 `Complete`。

其對應的具類型用戶端 .NET 類型如下：


	public class TodoItem
	{
		public string id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}
	
啟用動態結構描述時，Azure 行動服務會根據插入或更新要求中的物件自動產生新欄位。如需詳細資訊，請參閱[動態結構描述](http://go.microsoft.com/fwlink/?LinkId=296271)。

## <a name="create-client"></a>作法：建立行動服務用戶端

下列程式碼將建立用來存取行動服務的 `MobileServiceClient` 物件。
			
	MobileServiceClient client = new MobileServiceClient( 
		"AppUrl", 
		"AppKey" 
	); 

在上述程式碼中，請將 `AppUrl` 和 `AppKey` 依序取代為行動服務 URL 和應用程式金鑰。您可在 Azure 管理入口網站上找到這兩項資訊，方法是選取您的行動服務，然後按一下 [儀表板]。

## <a name="instantiating"></a>作法：建立資料表參考

只要是可存取或修改行動服務資料表中之資料的所有程式碼，都會呼叫 `MobileServiceTable` 物件上的函數。您可透過呼叫 `MobileServiceClient` 執行個體上的 [GetTable](http://msdn.microsoft.com/library/windowsazure/jj554275.aspx) 函數，來取得資料表的參考。

    IMobileServiceTable<TodoItem> todoTable = 
		client.GetTable<TodoItem>();

此為具類型的序列化模型。請參閱下面的<a href="#untyped">不具類型的序列化模型</a>討論。
			
## <a name="querying"></a>作法：查詢行動服務中的資料 

本節將說明如何對行動服務發出查詢。小節將說明例如排序、篩選及分頁等其他方面。
			
### <a name="filtering"></a>作法：篩選傳回的資料

下列程式碼說明如何在查詢中包含 `Where` 子句，以篩選資料。它會從 `todoTable` 傳回其 `Complete` 屬性等於 `false` 的所有項目。`Where` 函數會套用資料列篩選述語來查詢資料表。
	

	// This query filters out completed TodoItems and 
	// items without a timestamp. 
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

您可以使用訊息檢查軟體 (例如瀏覽器開發人員工具或 Fiddler) 來檢視傳送至行動服務的要求 URI。如果您查看下面的要求 URI，您會注意到我們打算修改查詢字串本身：

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1				   
此要求通常會被概略轉譯成下列伺服器端上的 SQL 查詢：
			
	SELECT * 
	FROM TodoItem 			
	WHERE ISNULL(complete, 0) = 0
			
傳遞至 `Where` 方法的函數可以有任意數目的條件。以下面的程式碼行為例：

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false
		   && todoItem.Text != null)
	   .ToListAsync();

會被概略轉譯 (針對先前顯示的相同要求) 成
			
	SELECT * 
	FROM TodoItem 
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

上述 `where` 陳述式將會尋找 `Complete` 狀態設為 false，且 `Text` 不是 Null 的項目。

我們也可以將它改寫成多行程式碼：

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

這兩種方法是相同的，而且可以交換使用。在一個查詢中串連多個述語的舊有選項較為精簡，也是我們給您的建議。

`where` 子句可支援被轉譯成行動服務 OData 子集的作業。這包括關係運算子 (==、!=、<、<=、>、>=)、算術運算子 (+、-、/、*、%)、精確度位數 (Math.Floor、Math.Ceiling)、字串函數 (Length、Substring、Replace、IndexOf、StartsWith、EndsWith)、日期屬性 (Year、Month、Day、Hour、Minute、Second)、物件的存取屬性，及結合上述所有的運算式。

### <a name="sorting"></a>作法：排序傳回的資料

下列程式碼說明如何在查詢中加入 `OrderBy` 或 `OrderByDescending` 函數，以將資料排序。它會從 `todoTable` 傳回項目，並依據 `Text` 欄位以遞增順序排列。依預設，伺服器只傳回前 50 個元素。

> [AZURE.NOTE]預設會使用伺服器控制的頁面大小，以防止傳回所有元素。這可避免預設的大型資料集要求對服務造成負面影響。

您可以按照下節所述呼叫 `Take`，增加要傳回的項目數。

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();			

### <a name="paging"></a>作法：以分頁方式傳回資料

下列程式碼顯示如何在查詢中使用 `Take` 和 `Skip` 子句，以便在傳回的資料中實作分頁。執行下列查詢時，會傳回資料表中的前三個項目。

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);                              
	List<TodoItem> items = await query.ToListAsync();

下列已修訂查詢會略過前三個結果，並傳回接下來的後三個結果。實際上這就是第二「頁」資料，頁面大小為三個項目。

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);                              
	List<TodoItem> items = await query.ToListAsync();
			
您也可以使用 [IncludeTotalCount](http://msdn.microsoft.com/library/windowsazure/jj730933.aspx) 方法，確保查詢會忽略指定的任何採取分頁/限制子句，而取得已傳回 <i>all</i> 記錄的總數：

	query = query.IncludeTotalCount();

這是傳遞硬式編碼分頁值至 `Take` 和 `Skip` 方法的簡化案例。在實際的應用程式中，您可以搭配頁面巡覽區控制項或類似的 UI 來使用類似上述的查詢，讓使用者導覽至上一頁和下一頁。

### <a name="selecting"></a>作法：選取特定資料欄

若要指定將哪些屬性集包含在結果中，您可以在查詢中加入 `Select` 子句。例如，下列程式碼將示範如何只選取一個欄位以及如何選取及格式化多個欄位：

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();
	
	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();
			
到目前為止，上述的所有函數都是加法，因此我們可以持續呼叫這些函數，每一次查詢受到的影響就會越大。再提供一個範例：

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();
	
### <a name="lookingup"></a>作法：按識別碼查詢資料

`LookupAsync` 函數可用來查閱具有特定識別碼之資料庫中的物件。

	// This query filters out the item with the ID of 25
	TodoItem item25 = await todoTable.LookupAsync(25);

## <a name="inserting"></a>作法：將資料插入行動服務

> [AZURE.NOTE]如果您想要執行某類型的插入、查詢、刪除或更新操作，則您必須建立名為 **Id** (不需分大小寫) 的成員。這是此範例類別 **TodoItem** 具有成員名稱為 **Id** 的原因。ID 值不得在插入作業期間設定為預設值以外的任何值；相反地，ID 值應該始終設定為非預設值，且存在於更新和刪除作業中。

下列程式碼將說明如何將新的資料列插入資料表中。參數包含要作為 .NET 物件插入的資料。

	await todoTable.InsertAsync(todoItem);

等候 `todoTable.InsertAsync` 呼叫傳回之後，伺服器中之物件的識別碼將填入用戶端的 `todoItem` 物件中。

若要插入不具類型的資料，您可以充份利用 Json.NET，如下所示。同樣地，請注意，插入物件時不得指定識別碼。

	JObject jo = new JObject(); 
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

如果您嘗試插入已設定 [Id] 欄位的項目，您將會從服務收到 `MobileServiceInvalidOperationException`。

## <a name="modifying"></a>作法：修改行動服務中的資料

下列程式碼將說明如何使用含有新資訊的相同 ID 來更新現有的執行個體。參數包含要作為 .NET 物件更新的資料。

	await todoTable.UpdateAsync(todoItem);


若要插入不具類型的資料，您可以充份利用 Json.NET，如下所示。請注意，進行更新時必須指定 ID，因為那是行動服務識別要更新哪個執行個體的方式。您可以從 `InsertAsync` 呼叫的結果取得 ID。

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);
			
如果您嘗試在未設定 [Id] 欄位的情況下更新項目，則服務沒有辦法分辨要更新哪個執行個體，因此您將從服務收到 `MobileServiceInvalidOperationException`。同樣地，如果您嘗試在未設定 [Id] 欄位的情況下更新不具類型的項目，您將會再次從服務收到 `MobileServiceInvalidOperationException`。
			
			
## <a name="deleting"></a>作法：刪除行動服務中的資料

下列程式碼將說明如何刪除現有的執行個體。您可以透過 `todoItem` 上所設定的 [Id] 欄位來識別執行個體。

	await todoTable.DeleteAsync(todoItem);

若要刪除不具類型的資料，您可以充份利用 Json.NET，如下所示。請注意，進行刪除要求時必須指定 ID，因為那是行動服務識別要刪除哪個執行個體的方式。刪除要求只需要 ID 即可；其他屬性不會傳遞至服務，如果有傳遞其他屬性，服務也會將他們忽略。`DeleteAsync` 呼叫的結果通常也會是 `null`。您可以從 `InsertAsync` 呼叫的結果取得所要傳入的 ID。

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	await table.DeleteAsync(jo);
			
如果您嘗試在未設定 [Id] 欄位的情況下刪除項目，則服務沒有辦法分辨要刪除哪個執行個體，因此您將從服務收到 `MobileServiceInvalidOperationException`。同樣地，如果您嘗試在未設定 [Id] 欄位的情況下刪除不具類型的項目，您將會再次從服務收到 `MobileServiceInvalidOperationException`。
		


## <a name="authentication"></a>作法：驗證使用者

行動服務支援使用各種外部識別提供者 (Facebook、Google、Microsoft 帳戶、Twitter 以及 Azure Active Directory) 來驗證與授權應用程式使用者。您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。您也可以使用通過驗證使用者的身分識別來實作伺服器指令碼中的授權規則。如需詳細資訊，請參閱「開始使用驗證」教學課程 ([Xamarin.iOS][Xamarin.iOS authentication]/[Xamarin.Android][Xamarin.Android authentication])。

支援兩個驗證流程：_伺服器流程_和_用戶端流程_。由於伺服器流程採用提供者的 Web 驗證介面，因此所提供的驗證體驗也最為簡單。因為用戶端流程採用提供者特定的裝置特定 SDK，因此可允許與裝置特定功能的深入整合。

### 伺服器流程
若要讓行動服務管理 Windows 市集或 Windows Phone 應用程式中的驗證程序，您必須向識別提供者註冊您的應用程式。接著在您的行動服務中，您必須設定提供者所提供的應用程式 ID 和密碼。如需詳細資訊，請參閱「開始使用驗證」教學課程 ([Xamarin.iOS][Xamarin.iOS authentication]/[Xamarin.Android][Xamarin.Android authentication])。

在註冊識別提供者之後，您只需使用提供者的 [MobileServiceAuthenticationProvider] 值來呼叫 [LoginAsync 方法]即可。例如，下列程式碼將透過使用 Facebook 來初始化伺服器流程登入。

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
				message = 
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

如果您打算使用除了 Facebook 以外的識別提供者，請將上方的 [MobileServiceAuthenticationProvider] 值變更成您提供者。

在此案例中，行動服務透過顯示所選提供者的登入頁面，並在使用識別提供者成功登入後產生行動服務驗證權杖的方式，來管理 OAuth 2.0 驗證流程。[LoginAsync 方法] 會傳回 [MobileServiceUser]，並提供通過驗證使用者的 [userId] 和 [MobileServiceAuthenticationToken]，以作為 JSON Web 權杖 (JWT)。您可以快取並重複使用此權杖，直到它到期為止。如需詳細資訊，請參閱[快取驗證權杖]。

### 用戶端流程

您的應用程式也可以個別連絡識別提供者，然後將傳回的權杖提供給行動服務進行驗證。此用戶端流程可讓您為使用者提供單一登入體驗，或從識別提供者擷取其他使用者資料。

您可以最簡化形式來使用用戶端流程，如下列 Facebook 或 Google 的程式碼片段中所示。

	var token = new JObject();
	// Replace access_token_value with actual value of your access token obtained 
	// using the Facebook or Google SDK.
	token.Add("access_token", "access_token_value");
			
	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				// Change MobileServiceAuthenticationProvider.Facebook 
				// to MobileServiceAuthenticationProvider.Google if using Google auth.
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message = 
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

### <a name="caching"></a>快取驗證權杖
在某些情況下，在使用者首次驗證之後就可以避免呼叫登入方法。您可以使用本機安全存放區 (例如 [Xamarin.Auth][Xamarin.Auth component])，便可在目前使用者首次登入時快取其使用者識別，之後您可以每次在快取中查看是否已有此使用者識別存在。當沒有快取時，您仍需透過登入程序傳送使用者。

	using Xamarin.Auth;
	var accountStore = AccountStore.Create(); // Xamarin.iOS
	//var accountStore = AccountStore.Create(this); // Xamarin.Android

	// After logging in
	var account = new Account (user.UserId, new Dictionary<string,string> {{"token",user.MobileServiceAuthenticationToken}});
	accountStore.Save(account, "Facebook");

	// Log in 
	var accounts = accountStore.FindAccountsForService ("Facebook").ToArray();
	if (accounts.Count != 0)
	{
		user = new MobileServiceUser (accounts[0].Username);
		user.MobileServiceAuthenticationToken = accounts[0].Properties["token"];
	}
	else
	{
		// Regular login flow
		user = new MobileServiceuser( await client
			.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
		var token = new JObject();
		// Replace access_token_value with actual value of your access token
		token.Add("access_token", "access_token_value");
	}
			
	 // Log out
	client.Logout();
	accountStore.Delete(account, "Facebook");


## <a name="errors"></a>作法：處理錯誤

在行動服務中遇到、驗證及解決錯誤的方式有數種。

例如，您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。想像定義與註冊可用來驗證與修改資料的伺服器指令碼，如下所示：

	function insert(item, user, request) 
	{
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
		  request.execute();
	   }
	}

此伺服器端指令碼可驗證傳送至行動服務之字串資料長度，並拒絕太長的字串，在此案例中為不得超過 10 個字元。

現在，行動服務正在伺服器端上驗證資料並傳送錯誤回應，您可以更新您的 .NET 應用程式以便處理驗證的錯誤回應。

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and Mobile Services has assigned an Id, the item is added to the CollectionView
		try
		{
			await todoTable.InsertAsync(todoItem);
			items.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}

## <a name="untyped"></a>作法：使用不具類型的資料

Xamarin 元件用戶端是專為強型別案例所設計的。不過，較弱型別體驗有時候非常方便；例如，當處理具有開放式結構描述的物件時便是如此。已依下列方式啟用該案例。在查詢中，您可以放棄 LINQ 並使用電傳格式。

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");			

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

您將收到可用作屬性包的 JSON 值。如需有關 JToken 和 Json.NET 的詳細資訊，請參閱 [Json.NET](http://json.codeplex.com/)

## <a name="unit-testing"></a>作法：設計單位測試

`MobileServiceClient.GetTable` 所傳回的值和查詢均為介面。這可讓它們在測試用途中更容易被模仿，因此您可以建立 `MyMockTable : IMobileServiceTable<TodoItem>` 來實作您的測試邏輯。

## <a name="nextsteps"></a>接續步驟

現在，您已完成本作法概念參考資料主題，下列內容將詳細說明如何在行動服務中執行重要工作：

* 開始使用行動服務 ([Xamarin.iOS][Get started with Mobile Services iOS]/[Xamarin.Android][Get started with Mobile Services Android]) <br/>了解如何使用行動服務的基本概念。

* 開始使用資料 ([Xamarin.iOS][Get started with data iOS]/[Xamarin.Android][Get started with data Android]) <br/>深入了解使用行動服務來排序和查詢資料。

* 開始使用驗證 ([Xamarin.iOS][Get started with authentication iOS]/[Xamarin.Android][Get started with authentication Android]) <br/>了解如何向身分識別提供者驗證應用程式的使用者。

* 使用指令檔驗證與修改資料 ([Xamarin.iOS][Validate and modify data with scripts ios]/[Xamarin.Android][Validate and modify data with scripts android]) <br/>深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

* 使用分頁縮小查詢範圍 ([Xamarin.iOS][Refine queries with paging iOS]/[Xamarin.Android][Refine queries with paging Android]) <br/>了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

* 使用指令碼授權使用者 ([Xamarin.iOS][Authorize users with scripts iOS]/[Xamarin.Android][Authorize users with scripts Android]) <br/>了解如何使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[Look up data by ID]: #lookingup
[How to: Bind data to user interface in a mobile service]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Query data from a mobile service]: #querying
[How to: Customize the client]: #customizing
[How to: Work with untyped data]: #untyped
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next steps]: #nextsteps
[快取驗證權杖]: #caching

<!-- URLs. -->
[Get started with Mobile Services iOS]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with Mobile Services Android]: /develop/mobile/tutorials/get-started-xamarin-android
[Xamarin download]: http://xamarin.com/download/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Xamarin.iOS quickstart tutorial]: /develop/mobile/tutorials/get-started-xamarin-ios/
[Xamarin.Android quickstart tutorial]: /develop/mobile/tutorials/get-started-xamarin-android/
[Xamarin.iOS data tutorial]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios/
[Xamarin.Android data tutorial]: /develop/mobile/tutorials/get-started-with-data-xamarin-android/
[Xamarin.iOS authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios/
[Xamarin.Android authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-android/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Xamarin.Auth component]: https://components.xamarin.com/view/xamarin.auth

[Mobile Services SDK]: http://nuget.org/packages/WindowsAzure.MobileServices/
[Get started with data iOS]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with data Android]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Get started with authentication iOS]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with authentication Android]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Validate and modify data with scripts ios]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Validate and modify data with scripts android]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[Refine queries with paging iOS]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Refine queries with paging Android]: /develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Authorize users with scripts iOS]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Authorize users with scripts Android]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
[LoginAsync 方法]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[LoginAsync 方法]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
 

<!---HONumber=July15_HO2-->