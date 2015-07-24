<properties 
	pageTitle="從 .NET 用戶端使用 Azure App Service 中的 API 應用程式" 
	description="了解如何使用 App Service SDK 從 .NET 用戶端中的 API 應用程式。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2015" 
	ms.author="tdykstra"/>

# 從 .NET 用戶端使用 Azure App Service 中的 API 應用程式 

## 概觀

此教學課程展示如何使用 App Service SDK 撰寫程式碼，以呼叫針對 [**公用 (匿名)**] 或 [**公用 (驗證)**] 存取層級設定的 [API 應用程式](app-service-api-apps-why-best-platform.md)。本文涵蓋以下範例案例：

- 從主控台應用程式呼叫**公用 (匿名)** API 應用程式
- 從 Windows 桌面應用程式呼叫**公用 (驗證)** API 應用程式 

下列的教學課程章節各自獨立--您可以直接依照第二個案例的指示，而不需要完成的第一個案例的步驟。

如需關於如何呼叫**內部** API 應用程式的資訊，請參閱[從 .NET 用戶端使用內部 API 應用程式](app-service-api-dotnet-consume-internal.md)。

## 必要條件

此教學課程假設您已熟悉如何建立專案，並在 Visual Studio 中加入程式碼，以及如何 [在 Azure Preview 入口網站中管理 API 應用程式](app-service-api-apps-manage-in-portal.md)。

在本文中的專案和程式碼範例，係根據您在這些文件中所建立、部署和保護的 API 應用程式專案：

- [建立 API 應用程式](app-service-dotnet-create-api-app.md)
- [部署 API 應用程式](app-service-dotnet-deploy-api-app.md)
- [保護 API 應用程式](../app-service-dotnet-add-authentication.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

本教學課程需要 2.6 版或更新版本的 Azure SDK for.NET。

## 來自主控台應用程式的未驗證呼叫

本節中，您會建立主控台應用程式專案並在其中加入程式碼，以在不需要驗證的情況下呼叫 API 應用程式。

### 設定 API 應用程式並建立專案

1. 如果您尚未這麼做，請依照[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)來將 ContactsList 範例專案部署至 Azure 訂用中 API 應用程式。

	該教學課程會引導您在 Visual Studio 發佈對話方塊中將存取層級設定為 [**任何人皆可用**]，這相當於入口網站中的 [**公用 (匿名)**]。然而，如果您在這之後進行[保護 API 應用程式](../app-service-dotnet-add-authentication.md)教學課程，則存取層級已被設定為 [**公用 (驗證)**]。在此情況下，您需要依照下列步驟的引導進行變更。

2. 在 [Azure Preview 入口網站](https://portal.azure.com/)，針對欲呼叫之 API 應用程式，在 [**API 應用程式**] 刀鋒視窗中移至 [**設定 > 應用程式設定**]，並將 [**存取層級**] 設定為 [**公用 (匿名)**]。

	![](./media/app-service-api-dotnet-consume/setpublicanon.png)
 
2. 在 Visual Studio 中，建立主控台應用程式專案。
 
### 加入 App Service SDK 產生的用戶端程式碼

3. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案 (而非方案)，並依序選取 [**加入 > Azure API 應用程式用戶端**]。 

	![](./media/app-service-api-dotnet-consume/03-add-azure-api-client-v3.png)
	
3. 在 [**加入 Azure API 應用程式用戶端**] 對話方塊中，按一下 [**從 Azure API 應用程式下載**]。

5. 在下拉式清單中，選取要呼叫的 API 應用程式。

7. 按一下 [確定]。

	![產生畫面](./media/app-service-api-dotnet-consume/04-select-the-api-v3.png)

	精靈下載 API 中繼資料檔案，並產生具型別的介面來呼叫 API 應用程式。

	![產生中](./media/app-service-api-dotnet-consume/05-metadata-downloading-v3.png)

	程式碼產生完成之後，您會在 [**方案總管**] 中看到該 API 應用程式名稱的新資料夾。此資料夾包含實作用戶端類別和資料模型的程式碼。

	![產生完成](./media/app-service-api-dotnet-consume/06-code-gen-output-v3.png)

### 加入呼叫 API 應用程式的程式碼

若要呼叫 API 應用程式，您只需要建立用戶端物件，然後對它呼叫方法，如下例所示：

        var client = new ContactList();
        var contacts = client.Contacts.Get();

1. 開啟 *Program.cs*，然後在 `Main` 方法中加入下列程式碼。

		var client = new ContactsList();
		
		// Send GET request.
		var contacts = client.Contacts.Get();
		foreach (var c in contacts)
		{
		    Console.WriteLine("{0}: {1} {2}",
		        c.Id, c.Name, c.EmailAddress);
		}
		
		// Send POST request.
		client.Contacts.Post(new Models.Contact
		{
		    EmailAddress = "lkahn@contoso.com",
		    Name = "Loretta Kahn",
		    Id = 4
		});
		Console.WriteLine("Finished");
		Console.ReadLine();

3. 按 CTRL+F5 執行應用程式。

	![產生完成](./media/app-service-api-dotnet-consume/consoleappoutput.png)

## 從 Windows 桌面應用程式發出經驗證的呼叫

本節中，您會建立 Windows 桌面應用程式專案並在其中加入程式碼，以在需要驗證的情況下呼叫 API 應用程式。此程式碼會實作 Oauth 2 *伺服器驗證流程*，這表示 API 應用程式閘道會從驗證提供者取得權杖，而不是用戶端應用程式。

Azure API 應用程式也支援用戶端驗證流程。未來此教學課程中也會加入用戶端流程驗證案例。

### 設定 API 應用程式並建立專案

1. 請遵循[保護 API 應用程式](../app-service-dotnet-add-authentication.md)教學課程，以設定存取層級為 [**公用 (驗證)**] 的 API 應用程式。

1. 在 Visual Studio 中，建立 Windows Forms 桌面專案。

2. 在表單設計工具中，加入下列控制項：

	* 按鈕控制項
	* 文字方塊控制項
	* 網頁瀏覽器控制項

3. 將文字方塊控制項設定為多行。

	您的表單外觀會如下例所示。

	![](./media/app-service-api-dotnet-consume/form.png)

### 加入 App Service SDK 產生的用戶端程式碼

3. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案 (而非方案)，並依序選取 [**加入 > Azure API 應用程式用戶端**]。 

3. 在 [**加入 Azure API 應用程式用戶端**] 對話方塊中，按一下 [**從 Azure API 應用程式下載**]。

5. 在下拉式清單中，選取要呼叫的 API 應用程式，然後按一下 [**確定**]。

### 加入呼叫 API 應用程式的程式碼

5. 在 Azure Preview 入口網站中，複製您的 API 應用程式閘道的 URL。您將在下一個步驟中使用此值。

	![](./media/app-service-api-dotnet-consume/gatewayurl.png)

4. 在 *Form1.cs* 原始程式碼中，於 `Form1()` 建構函式之前加入下列程式碼，並使用您在上一個步驟複製的值取代 GATEWAY_URL 值。請確定您包含結尾斜線 (/)。

		private const string GATEWAY_URL = "https://resourcegroupnameb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string URL_TOKEN = "#token=";

4. 在表單設計工具中，按兩下按鈕以加入 click 處理常式，然後在處理常式方法中加入程式碼以移至閘道的登入 URL，例如：

		webBrowser1.Navigate(string.Format(@"{0}login/[authprovider]", GATEWAY_URL));

	以您在閘道中設定的身分識別服務提供者代碼來取代 "[authprovider]"，例如 "aad"、"twitter"、google"、"microsoftaccount" 或 "facebook"。例如：

		webBrowser1.Navigate(string.Format(@"{0}login/aad", GATEWAY_URL));

7. 加入網頁瀏覽器控制項的 `DocumentCompleted` 事件處理常式，並將下列程式碼加入處理常式方法。

		if (e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		{
		    var encodedJson = e.Url.AbsoluteUri.Substring(e.Url.AbsoluteUri.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		    var decodedJson = Uri.UnescapeDataString(encodedJson);
		    var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		    string userId = result.user.userId;
		    string userToken = result.authenticationToken;
		
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    appServiceClient.SetCurrentUser(userId, userToken);
		
		    var contactsListClient = appServiceClient.CreateContactsList();
		    var contacts = contactsListClient.Contacts.Get();
		    foreach (Contact contact in contacts)
		    {
		        textBox1.Text += contact.Name + " " + contact.EmailAddress + System.Environment.NewLine;
		    }
		    //appServiceClient.Logout();
		    //webBrowser1.Navigate(string.Format(@"{0}login/aad", GW_URL));
		}

	會在使用者利用網頁瀏覽器控制項登入後，會執行已加入的程式碼。成功登入之後，回應 URL 會包含使用者識別碼和密碼。程式碼會從 URL 擷取這些值，並提供給 App Service 用戶端物件，然後使用該物件來建立 API 應用程式用戶端物件。接著，您可以藉由對此 API 應用程式用戶端物件呼叫方法，來呼叫 API。

8. 按 CTRL+F5 執行應用程式。

9. 按一下按鈕，並當瀏覽器控制項顯示登入頁面時，輸入獲授權可以呼叫 API 應用程式的使用者認證。

	Azure 會驗證該認證，然後應用程式會呼叫 API 應用程式並顯示回應。

	![](./media/app-service-api-dotnet-consume/formaftercall.png)

## 後續步驟

本文示範對於存取層級設為 [**公用 (驗證)**] 和 [**公用 (匿名)**] 的 API 應用程式，如何從 .NET 用戶端使用 API 應用程式。

如需其他從 .NET 用戶端呼叫 API 應用程式的程式碼範例，請下載 [Azure Cards](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample) 範例應用程式。
 

<!---HONumber=62-->