Azure 客戶每月可以解除鎖定 25,000 封免費電子郵件。這些每月 25,000 封的免費電子郵件可讓您存取進階報告與分析及[所有 API][] (Web、SMTP、Event、Parse 及更多)。如需有關 SendGrid 所提供其他服務的資訊，請參閱 [SendGrid 功能][]頁面。

### 註冊 SendGrid 帳戶

1. 登入 [Azure 管理入口網站][]。

2. 在管理入口網站的下方窗格中，按一下 [新增]。

	![command-bar-new][command-bar-new]

3. 按一下 [Marketplace]。

	![sendgrid-store][sendgrid-store]

4. 在 [選擇應用程式和服務] 對話方塊中，選取 [SendGrid] 並按一下向右鍵。

5. 在 [個人化應用程式和服務] 對話方塊中，選取您想要註冊的 [SendGrid] 方案。

6. 在 Azure 設定中，輸入名稱來識別您的 **SendGrid** 服務，或使用預設值 [SendGridEmailDelivery.Simplified.SMTPWebAPI]。名稱的長度必須介於 1 到 100 個字元之間，而且只能包含英數字元、連字號、句點和底線。此名稱在已訂用的 Azure 市集項目清單中必須是唯一的。

	![store-screen-2][store-screen-2]

7. 選擇地區值，例如美國西部。

8. 按一下向右箭頭。

9. 在 [檢閱購買項目] 索引標籤中，檢閱方案和價格資訊，並檢閱法律條款。如果您同意接受這些條款，請按一下核取記號。按一下核取記號之後，您的 SendGrid 帳戶將會開始 [SendGrid 佈建程序]。

	![store-screen-3][store-screen-3]

10. 確認購買項目之後，系統會將您重新導向至附加元件儀表板，您將會看到 **[正在購買 SendGrid]** 的訊息。

	![sendgrid-purchasing-message][sendgrid-purchasing-message]

	系統會立即佈建您的 SendGrid 帳戶，您將會看到 **[已成功購買附加元件 SendGrid]** 的訊息。現在已建立您的帳戶和認證。您現在可以開始傳送電子郵件。

	若要修改您的訂用計畫或查看 SendGrid 連絡人設定，請按一下您的 SendGrid 服務名稱，以開啟 SendGrid Marketplace 儀表板。

	![sendgrid-add-on-dashboard][sendgrid-add-on-dashboard]

	若要使用 SendGrid 傳送電子郵件，您必須提供帳戶認證 (使用者名稱和密碼)。

### 尋找您的 SendGrid 認證 ###

1. 按一下 [連線資訊]。

	![sendgrid-connection-info-button][sendgrid-connection-info-button]

2. 在 *[連線資訊]* 對話方塊中，複製 [密碼] 和 [使用者名稱] 以供稍後在本教學課程中使用。

	![sendgrid-connection-info][sendgrid-connection-info]

	若要進行電子郵件傳遞能力設定，請按一下 [管理] 按鈕。此動作將會開啟 Sendgrid.com 網頁介面，您可在此登入並開啟您的 SendGrid 控制台。

	![sendgrid-control-panel][sendgrid-control-panel]

	如需有關開始使用 SendGrid 的詳細資訊，請參閱 [SendGrid 開始使用][]。

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[SendGrid 功能]: http://sendgrid.com/features
[Azure 管理入口網站]: https://manage.windowsazure.com
[SendGrid 開始使用]: http://sendgrid.com/docs
[SendGrid 佈建程序]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[所有 API]: https://sendgrid.com/docs/API_Reference/index.html

<!---HONumber=July15_HO2-->