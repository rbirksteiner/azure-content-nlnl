
* 請依照[在伺服器上安裝用戶端 SSL 簽署身分識別](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW15)的步驟，將您在上一個步驟中下載的憑證匯出至 .p12 檔案。

* 在 Azure 入口網站中，按一下 [**行動服務**] > 您的應用程式 > [**推播**] 索引標籤 > "apple 推播通知設定** > "**上傳**。上傳 .p12 檔案，並確定已選取正確的 [**模式**] (不是 [沙箱] 就是 [實際執行]，這對應於您產生的用戶端 SSL 憑證為 [開發] 或 [配送])。 您的行動服務現在已設定成在 iOS 上使用推播通知！

<!---HONumber=July15_HO2-->