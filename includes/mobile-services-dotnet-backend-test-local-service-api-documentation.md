
1. 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下服務專案，然後按一下 [偵錯] 內容功能表之下的 [開始新執行個體]。

    ![start mobile service project locally](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png)

    Visual Studio 會為您的服務開啟預設網頁。依預設，Visual Studio 使用 IIS Express 在本機代管您的行動服務。

2. 以滑鼠右鍵按一下 Windows 工作列中的 IIS Express 系統匣圖示，然後驗證行動服務是否已啟動。

	 ![verify the mobile service in the taskbar](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. 在 .NET 後端的開始頁面中按一下 [**立即試用**]。

    ![mobile service start up page](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png)

    如此能顯示 API 文件頁面，您可以使用它來測試行動服務。

	>[AZURE.NOTE]在本機執行時，您不需要通過驗證即可存取此頁面。在 Azure 中執行時，您必須提供應用程式金鑰以做為存取此頁面的密碼 (不需要使用者名稱)。

4. 按一下 [GET tables/TodoItem] 連結。

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
   	
	如此能顯示 API 的 GET 回應頁面。

5. 依序按一下 [try this out] 和 [傳送]。
 
	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

	如此能傳送 GET 要求給本機行動服務，以傳回 TodoItem 資料表中的所有資料列。由於初始設定式已植入資料表，因此系統會在回應訊息的本文中傳回兩個 TodoItem 物件。如需初始設定式的詳細資訊，請參閱[如何對 .NET 後端行動服務進行資料模型變更](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)。

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)

<!---HONumber=July15_HO2-->