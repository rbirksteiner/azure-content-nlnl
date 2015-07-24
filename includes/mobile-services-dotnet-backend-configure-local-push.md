
在發行至 Azure 前，您可以透過在本機電腦或 VM 上執行的行動服務，選擇性地測試推送通知。若要這樣做，您必須在 web.config 檔案中設定應用程式所使用的通知中樞相關資訊。只有在本機執行連接至通知中樞時才會使用此資訊，此資訊在發行至 Azure 時會被忽略。

1. 返回行動服務的 [推送] 索引標籤，按一下 [通知中樞] 連結。

	![](./media/mobile-services-dotnet-backend-configure-local-push/link-to-notification-hub.png)

	這會瀏覽至行動服務所使用的通知中樞。

2. 在通知中樞頁面中，記下您的通知中樞名稱，然後按一下 [View Connection String]。

	![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-page.png)

3. 在 [Access connection information] 中，複製 **DefaultFullSharedAccessSignature** 連接字串。

	![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-connection-string.png)

4. 在 Visual Studio 的行動服務專案中，開啟服務的 Web.config 檔案，並在 **connectionStrings** 中，使用上一個步驟的連接字串來取代 **MS_NotificationHubConnectionString** 的連接字串。

5. 在 **appSettings** 中，使用通知中樞的名稱來取代 **MS_NotificationHubName** 應用程式設定值。

現在，已設定行動服務專案在本機執行時連接至 Azure 的通知中樞。請注意，請務必使用相同通知中樞名稱和連接字串作為入口網站，因為在 Azure 執行時，入口網站設定會覆寫這些 Web.config 專案設定。

<!---HONumber=July15_HO2-->