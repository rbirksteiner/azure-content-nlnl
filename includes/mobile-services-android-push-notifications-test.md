
###設定用於測試的 Android 模擬器
當您在模擬器中執行此應用程式時，請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

> [AZURE.IMPORTANT]若要接收推播通知，您必須在您的 Android 虛擬裝置上設定 Google 帳戶 (在模擬器中，瀏覽到 [設定]，並按一下 [新增帳戶])。另外，確定模擬器已連線到網際網路。

1. 從 [工具] 中，按一下 [Open Android Emulator Manager]，選取您的裝置，然後按一下 [Edit]。

   	![Android 虛擬裝置管理員](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app7.png)

2. 在 [Target] 中選取 [Google API]，然後按一下 [OK]。

   	![編輯 Android 虛擬裝置](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app8.png)

3. 在頂端工具列上，按一下 [執行]，然後選取您的應用程式。這將啟動模擬器，並執行應用程式。

  應用程式將從 GCM 擷取 *registrationId*，並向通知中心註冊。

###插入新的項目會產生通知。

1. 在應用程式中，輸入有意義的文字，如 _A new Mobile Services task_，然後按一下 [加入] 按鈕。

2. 從螢幕頂端向下撥動將裝置的通知中心開啟，以檢視通知。

	![在通知中心檢視通知](./media/mobile-services-android-push-notifications-test/notification-area-received.png)

<!---HONumber=July15_HO2-->