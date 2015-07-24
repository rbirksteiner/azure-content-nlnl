
1. 如果您尚未註冊您的應用程式，請瀏覽至 Windows 市集應用程式之開發人員中心的[提交應用程式頁面]，使用您的 Microsoft 帳戶登入，然後按一下 **[應用程式名稱]**。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. 選取 [保留唯一名稱來建立新應用程式]，再按一下 [繼續]，然後在 [應用程式名稱] 中輸入應用程式的名稱，按一下 [保留應用程式名稱]，然後按一下 [儲存]。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	This creates a new Windows Store registration for your app.

3. 在 Visual Studio 中，開啟您完成[開始使用行動服務] (英文) 教學課程時所建立的專案。

4. 在 [方案總管] 中，以滑鼠右鍵按一下 Windows 市集應用程式專案，然後依序按一下 [市集] 和 [將應用程式與市集建立關聯...]。

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	這將會顯示 **[將您的應用程式與 Windows 市集建立關聯]** 精靈。

5. 在精靈中，按一下 [登入]，然後以您的 Microsoft 帳戶登入，選取您在步驟 2 註冊的應用程式，按 [下一步]，然後按一下 [關聯]。

   	這會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。

6. (選擇性) 若為 Windows 通用 app，請針對 Windows Phone 市集專案重複執行步驟 4 與 5。

6. 回到新應用程式的 Windows 開發人員中心頁面，按一下 **[服務]**。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png)

7. 在 [服務] 頁面中，按一下 [Azure 行動服務] 下的 [Live Services site]。

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png)

8. 按一下 [API 設定]，選取啟用 [行動或桌面用戶端應用程式]，提供行動服務 URL 當成 [目標網域]，在 [重新導向 URL] 中提供 `https://<mobile_service>.azure-mobile.net/login/microsoftaccount/` 的值，然後按一下 [儲存]。

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

9. 在 [API 設定] 中，記下 [用戶端識別碼]、[用戶端密碼] 與 [封裝安全性識別碼 (SID)] 的值。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]用戶端密碼和封裝 SID 是重要的安全性認證。請勿與任何人共用這些密碼，或與您的應用程式一起散發密碼。

10. 登入 [Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的應用程式。

11. 按一下 [身分識別] 索引標籤，輸入您在步驟 4 中從 WNS 取得的 [用戶端密碼] 和 [封裝 SID] 值，然後按一下 [儲存]。

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. 按一下 [身分識別] 索引標籤。請注意，[用戶端密碼] 及 [封裝 SID] 的值已在上個步驟設定。輸入您之前記下的 [用戶端識別碼] ，然後按一下 [儲存] 。

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
現在，您已可在應用程式中使用 Microsoft 帳戶進行驗證。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[開始使用行動服務]: /develop/mobile/tutorials/get-started/#create-new-service
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure 管理入口網站]: https://manage.windowsazure.com/

<!---HONumber=July15_HO2-->