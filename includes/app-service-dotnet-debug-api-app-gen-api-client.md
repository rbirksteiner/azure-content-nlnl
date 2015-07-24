## 產生 API 應用程式用戶端 

Visual Studio 中的 API 應用程式工具可讓您輕鬆地產生 C# 程式碼，而該程式碼可從桌面、存放區和行動應用程式呼叫 Azure API 應用程式。

1. 在 Visual Studio 中，開啟方案，其中包含來自[建立 API 應用程式](../article/app-service-api/app-service-dotnet-create-api-app.md)主題的 API 應用程式。 

2. 從 [**方案總管**中，以滑鼠右鍵按一下方案，然後選取 [**新增**] > [**新增專案**]。

	![新增專案](./media/app-service-dotnet-debug-api-app-gen-api-client/01-add-new-project-v3.png)

3. 在 [**新增專案**] 對話方塊中，執行下列步驟：

	1. 選取 [**Windows 桌面**] 類別。
	
	2. 選取 [**主控台應用程式**] 專案範本。
	
	3. 命名專案。
	
	4. 按一下 [**確定**]，以在現有方案中產生新專案。
	
	![新增專案](./media/app-service-dotnet-debug-api-app-gen-api-client/02-contact-list-console-project-v3.png)

4. 以滑鼠右鍵按一下剛建立的主控台應用程式專案，然後選取 [**新增**] > [**Azure API 應用程式用戶端**]。

	![新增用戶端](./media/app-service-dotnet-debug-api-app-gen-api-client/03-add-azure-api-client-v3.png)
	
5. 在 [**新增 Microsoft Azure API 應用程式用戶端**] 對話方塊中，執行下列步驟：

	1. 選取 [**下載**] 選項。 
	
	2. 從下拉式清單中，選取您先前建立的 API 應用程式。
	
	3. 按一下 [確定]。

	![產生畫面](./media/app-service-dotnet-debug-api-app-gen-api-client/04-select-the-api-v3.png)

	此精靈會下載 API 中繼資料檔案，並產生可供叫用 API 應用程式的具型別介面。

	![產生中](./media/app-service-dotnet-debug-api-app-gen-api-client/05-metadata-downloading-v3.png)

	程式碼產生完成之後，您會在 [方案總管] 中看到採用 API 應用程式名稱的新資料夾。此資料夾包含實作用戶端和資料模型的程式碼。

	![產生完成](./media/app-service-dotnet-debug-api-app-gen-api-client/06-code-gen-output-v3.png)

6. 從專案根目錄開啟 **Program.cs** 檔案，並將 **Main** 方法取代為下列程式碼：

		static void Main(string[] args)
	    {
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
	    }

## 測試 API 應用程式用戶端

一旦編碼了 API 應用程式，現在是時候測試程式碼。

1. 開啟 [**方案總管**]。

2. 以滑鼠右鍵按一下您已在上一節中建立的主控台應用程式。

3. 從主控台應用程式的內容功能表中，選取 [**偵錯 > 啟動新的執行個體**]。

4. 主控台視窗應該開啟並顯示所有連絡人。

	![執行主控台應用程式](./media/app-service-dotnet-debug-api-app-gen-api-client/running-console-app.png)

5. 按 **Enter** 鍵以關閉主控台視窗。

<!---HONumber=58-->