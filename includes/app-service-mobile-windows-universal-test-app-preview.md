
返回 Visual Studio，並選取共用程式碼用戶端應用程式專案 (其名稱類似 `<your app name>.Shared`)

1. 展開 **App.xaml** 檔案，然後開啟 **App.xaml.cs** 檔案。找出使用 localhost URL 初始化之 `MobileService` 成員的宣告。註解此宣告 (使用 `CTRL+K,CTRL+C`)，並取消註解連接至您的託管服務的宣告 (`CTRL+K,CTRL+U`)：

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "http://localhost:58454"
        //);

        // This MobileServiceClient has been configured to communicate with the Azure Mobile Service and
        // Azure Gateway using the application key. You're all set to start working with your Mobile Service!
        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://mymobileapp-code.azurewebsites.net",
            "https://myresourcegroupgateway.azurewebsites.net/Microsoft.Azure.AppService.ApiApps.Gateway",
            "XXXX-APPLICATION-KEY-XXXXX"
        );

2. 按下 F5 鍵以重建專案並啟動 Windows 市集應用程式，這應該是您的預設啟始專案。

2. 在應用程式的 [**插入 TodoItem**] 中輸入有意義的文字，例如*完成教學課程*，然後按一下 [**儲存**]。

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-startup.png)

	這會將 POST 要求傳送至 Azure 中主控的新行動應用程式後端。

3. 停止偵錯，並將通用 Windows 方案中的預設啟始專案變更為 Windows Phone 市集應用程式 (以滑鼠右鍵按一下 `<your app name>.WindowsPhone` 專案，然後按一下 [**設定為啟始專案**])，然後再按下 F5 一次。

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-completed-wp8.png)

	請注意，從先前步驟中儲存的資料，會在 Windows 應用程式啟動後從行動應用程式載入。

<!---HONumber=62-->