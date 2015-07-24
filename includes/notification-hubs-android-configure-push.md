

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，並按一下畫面底部的 [+新增]。

2. 依序按一下 [App Services]、[服務匯流排]、[Notification Hub]、[快速建立]。

3. 為您的通知中心輸入名稱、選取所需的區域，然後按一下 [Create a new Notification Hub]。

   	![設定通知中樞屬性](./media/notification-hubs-android-configure-push/notification-hub-create-from-portal2.png)

4. 在 [**服務匯流排**] 之下，按一下您剛才建立的命名空間 (通常為 ***notification hub name*-ns**)。

5. 在命名空間上，按一下頂端的 [**通知中樞**] 索引標籤，然後按一下您剛才建立的通知中樞。

6. 按一下頂端的 [設定] 索引標籤，輸入前一節中取得的 [API Key] 值，然後按一下 [儲存]。

   	![在 [設定] 索引標籤中設定 GCM API 金鑰](./media/notification-hubs-android-configure-push/notification-hub-configure-android.png)

7. 選取頂端的 [儀表板] 索引標籤，然後按一下 [檢視連接字串]。記下這兩個連接字串。

現在已將您的通知中樞設定成使用 GCM，而且您已擁有可用來註冊應用程式以接收通知和傳送推播通知的連接字串。

<!---HONumber=July15_HO2-->