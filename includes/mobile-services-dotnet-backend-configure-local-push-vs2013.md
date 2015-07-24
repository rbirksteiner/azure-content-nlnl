
在發行至 Azure 前，您可以透過在本機電腦或 VM 上執行的行動服務，選擇性地測試推送通知。若要這樣做，您必須在 web.config 檔案中設定應用程式所使用的通知中樞相關資訊。只有在本機執行連接至通知中樞時才會使用此資訊，此資訊在發行至 Azure 時會被忽略。

1. 開啟 Windows 或 WindowsPhone 應用程式專案資料夾內的 readme.html 檔案。 

	如此會顯示 [Push setup is almost complete] 頁面 (如果您尚未開啟的話)。「步驟 3：修改 Web 組態」區段中含有通知中心連線資訊。

2. 在 Visual Studio 的行動服務專案中，開啟服務的 Web.config 檔案，接著在 **connectionStrings** 中新增 [Push setup is almost complete] 頁面中的 **MS_NotificationHubConnectionString** 連接字串。

3. 在 **appSettings** 中，將 **MS_NotificationHubName** 應用程式設定的值取代為 [Push setup is almost complete] 頁面中的通知中心名稱。

4. 以滑鼠右鍵按一下行動服務專案，並依序按一下 [偵錯] 和 [開始新執行個體]，然後記下瀏覽器顯示之開始頁面的服務根目錄 URL。

	此 URL 是 .NET 後端專案的本機主機 URL。您將需要使用此 URL 來對本機電腦上執行的行動服務測試應用程式。

現在，已設定行動服務專案在本機執行時連接至 Azure 的通知中樞。注意，請務必使用相同的通知中心名稱和連接字串作為入口網站，因為在 Azure 執行時，入口網站設定會覆寫 Web.config 檔案中的這些專案設定。

<!---HONumber=July15_HO2-->