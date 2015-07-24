

1. 如果您尚未註冊您的應用程式，請瀏覽至 Windows 市集應用程式之開發人員中心的[提交應用程式頁面]，使用您的 Microsoft 帳戶登入，然後按一下 **[應用程式名稱]**。

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2. 在 **[應用程式名稱]** 中為您的應用程式輸入名稱，然後依序按一下 **[保留應用程式名稱]** 和 **[儲存]**。

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

   	This creates a new Windows Store registration for your app.

3. 在 Visual Studio 中，開啟您完成**開始使用行動服務** (英文) 教學課程時所建立的專案。

4. 在 [方案總管] 中，以滑鼠右鍵按一下此專案，然後依序按一下 [市集] 和 [將應用程式與市集建立關聯...]。

  	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

   	這將會顯示 **[將您的應用程式與 Windows 市集建立關聯]** 精靈。

5. 在此精靈中，按一下 [登入]，然後使用您的 Microsoft 帳戶登入。

6. 選取您在步驟 2 中註冊的應用程式，按 **[下一步]**，然後按一下 **[關聯]**。

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

   	This adds the required Windows Store registration information to the application manifest.

7. (選用) 重複執行步驟 4-6，也會註冊通用 Windows 應用程式的 Windows Phone 市集專案。

8. 回到新應用程式的 Windows 開發人員中心頁面，按一下 **[服務]**。

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png)

9. 在 [服務] 頁面中，按一下 [Azure 行動服務] 下的 [Live Services site]。

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. 按一下 **[驗證您的服務]**，並記下 **[用戶端密碼]** 和 **[封裝安全性識別碼 (SID)]** 的值。

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    > [AZURE.NOTE]用戶端密碼和封裝 SID 是重要的安全性認證。請勿與任何人共用這些密碼，或與您的應用程式一起散發密碼。

11. 登入 [Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的應用程式。

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. 按一下 [推播] 索引標籤，輸入您從 WNS 取得的 [用戶端密碼] 和 [封裝 SID] 值，然後按一下 [儲存]。

	>[AZURE.NOTE]當您使用舊版行動服務完成這個教學課程時，可能會在 [推送] 索引標籤底部看見名為 [啟用進階推送] 的連結。立即按一下這個連結，即可升級您的行動服務來與通知中樞整合。這個變更無法回復。如需如何在生產行動服務中啟用進階推播通知的詳細資訊，請參閱<a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">此指引</a>。

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-push-tab.png)

	>[AZURE.NOTE]當您在入口網站的 [推播] 索引標籤中設定進階推播通知的 WNS 認證時，這些認證將會與通知中樞共用，以設定您應用程式適用的通知中樞。

<!-- URLs. -->
[Get started with Mobile Services]: ../articles/mobile-services-windows-store-get-started.md
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure 管理入口網站]: https://manage.windowsazure.com/

<!---HONumber=July15_HO2-->