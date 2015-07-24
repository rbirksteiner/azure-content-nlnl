
## <a id="register"></a>針對推播通知註冊應用程式

* [註冊應用程式的應用程式識別碼](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW991)。在註冊應用程式時，檢查 **[App Service]** 中的選擇性 **[推播通知]** 核取方塊。

> [AZURE.NOTE]建立明確的應用程式識別碼 (避免萬用字元應用程式識別碼)，並在 **[組合識別碼]** 中使用確切的 Xcode 快速入門專案 **[組合識別碼]**。在註冊應用程式識別碼時，也請務必檢查 **[App Service]** 中的 **[推播通知]** 選項。如果您沒有核取此方塊，推播通知將無法運作。

* 接下來，[針對應用程式啟用推播通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW6)。您也可以建立「開發」或「散發」SSL 憑證 (請務必在稍後前往行動服務入口網站選取 [沙箱] 或 [生產] 等對應的選項)。

* 接下來，[確認應用程式識別碼可啟用推播通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW8)。

* 最後，[重新整理 Xcode 快速入門專案中的佈建設定檔](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW10)，然後[確認佈建設定檔已建立或重新產生，以便啟用推播通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW12)。

<!---HONumber=July15_HO2-->