
1. 在 [方案] 檢視中，展開 Xamarin.Android 應用程式中的 [元件] 資料夾，並確認已安裝 Azure 行動服務封裝。 

2. 在 [元件] 資料夾上按一下滑鼠右鍵，按一下 [取得更多元件...]，搜尋 [Google Cloud Messaging 用戶端] 元件，然後將它新增至專案。

1. 開啟 ToDoActivity.cs 專案檔案，然後將下列 using 陳述式加入至類別：

		using Gcm.Client;

2. 將下列新程式碼加入至 **ToDoActivity** 類別：

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

	這樣做可讓您從服務處理程序從取行動服務用戶端執行個體。

3. 將現有的行動服務用戶端宣告變更為 public，如下所示：

		public MobileServiceClient client { get; private set; }

4.	在建立 **MobileServiceClient** 之後，將下列程式碼加入至 **OnCreate** 方法：

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

您的 **ToDoActivity** 現在可供新增推送通知。

<!---HONumber=July15_HO2-->