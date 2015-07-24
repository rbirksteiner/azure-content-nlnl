<properties
	pageTitle="開始使用 Azure 通知中心"
	description="了解如何使用 Azure 通知中心傳送推播通知。"
	services="notification-hubs"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-kindle"
	ms.devlang="Java"
	ms.topic="get-started-article" 
	ms.date="06/16/2015"
	ms.author="wesmc"/>

# 開始使用通知中心

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概觀

本主題將說明如何使用 Azure 通知中心將推播通知傳送至 Kindle 應用程式。在本教學課程中，您將使用 Amazon Device Messaging (ADM)，建立可接收推播通知的空白 Kindle 應用程式。

##必要條件

本教學課程需要下列各項：

+ Android SDK (假設您將使用 Eclipse)，您可以從<a href="http://go.microsoft.com/fwlink/?LinkId=389797">這裡</a>下載。
+ 請依照<a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">這裡</a>提供的步驟，針對 Kindle 設定您的開發環境。

##將新的應用程式新增至開發人員入口網站

1. 首先，請在[開發人員入口網站]中建立應用程式。

	![][0]

2. 複製 [應用程式金鑰]。

	![][1]

3. 在入口網站中，按一下您的應用程式名稱，然後按一下 [裝置訊息] 索引標籤。

	![][2]

4. 按一下 [建立新的安全性設定檔]，然後建立新的安全性設定檔 (例如 **TestAdm 安全性設定檔**)。然後按一下 [儲存]。

	![][3]

5. 按一下 [安全性設定檔] 以檢視您剛建立的安全性設定檔。複製 [用戶端 ID] 和 [用戶端密碼] 值，以供後續使用。

	![][4]

## 建立 API 金鑰

1. 以系統管理員權限開啟命令提示字元。
2. 導覽至 Android SDK 資料夾。
3. 輸入下列命令：

    	keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

	![][5]

4.  輸入 **android** 做為 [金鑰存放區] 密碼。

5.  複製 **MD5** 指模。
6.  返回開發人員入口網站，在 [訊息] 索引標籤中按一下 [Android/Kindle]，然後輸入您的應用程式的封裝名稱 (例如 **com.sample.notificationhubtest**)、[MD5] 值，然後按一下 [產生 API 金鑰]。

## 將認證新增至中心

在入口網站中，將用戶端密碼和用戶端 ID 新增至通知中心的 [設定] 索引標籤。

## 設定您的應用程式

> [AZURE.NOTE]建立應用程式時，至少應使用 API 層級 17。

將 ADM 程式庫新增至您的 Eclipse 專案。

1. 若要取得 ADM 程式庫，請[下載 SDK]。將 SDK zip 檔案解壓縮。
2. 在 Eclipse 中，以滑鼠右鍵按一下您的專案，然後按一下 [屬性]。選取左側的 [**Java 組建路徑**]，然後選取頂端的 [**程式庫**] 索引標籤。按一下 [**新增外部 Jar**]，然後從您解壓縮 Amazon SDK 的目錄中選取 `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` 檔案。3. 下載 NotificationHubs Android SDK (連結)。
4. 將封裝解壓縮，然後將 `notification-hubs-sdk.jar` 檔案拖曳到 Eclipse 的 `libs ` 資料夾中。

編輯您支援 ADM 的應用程式資訊清單：

1. 在根資訊清單元素中新增 Amazon 命名空間：


		xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. 在資訊清單元素下，將權限新增為第一個元素。將 **[YOUR PACKAGE NAME]** 取代為您用來建立應用程式的封裝。

		<permission
	     android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
	     android:protectionLevel="signature" />

		<uses-permission android:name="android.permission.INTERNET"/>

		<uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

		<!-- This permission allows your app access to receive push notifications
		from ADM. -->
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

		<!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
		<uses-permission android:name="android.permission.WAKE_LOCK" />

3. 插入下列元素，做為應用程式元素的第一個子項。請務必將 **[YOUR SERVICE NAME]** 取代為您在下一節中建立的 ADM 訊息處理常式的名稱 (包括封裝)，並將 **[YOUR PACKAGE NAME]** 取代為您用來建立應用程式的封裝名稱。

		<amazon:enable-feature
		      android:name="com.amazon.device.messaging"
		             android:required="true"/>
		<service
		    android:name="[YOUR SERVICE NAME]"
		    android:exported="false" />

		<receiver
		    android:name="[YOUR SERVICE NAME]$Receiver"

		    <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
		    android:permission="com.amazon.device.messaging.permission.SEND" >

		    <!-- To interact with ADM, your app must listen for the following intents. -->
		    <intent-filter>
		  <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
		  <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

		  <!-- Replace the name in the category tag with your app's package name. -->
		  <category android:name="[YOUR PACKAGE NAME]" />
		    </intent-filter>
		</receiver>

## 建立您的 ADM 訊息處理常式：

1. 建立繼承自 `com.amazon.device.messaging.ADMMessageHandlerBase` 的新類別，並將其命名為 `MyADMMessageHandler`，如下圖所示：

	![][6]

2. 新增下列 `import` 陳述式：

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.support.v4.app.NotificationCompat;
		import com.amazon.device.messaging.ADMMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub

3. 在您建立的類別中新增下列程式碼。請務必取代中心名稱和連接字串 (接聽)：

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
      	private static NotificationHub hub;
		public static NotificationHub getNotificationHub(Context context) {
			Log.v("com.wa.hellokindlefire", "getNotificationHub");
			if (hub == null) {
				hub = new NotificationHub("[hub name]", "[listen connection string]", context);
			}
			return hub;
		}

		public MyADMMessageHandler() {
				super("MyADMMessageHandler");
			}

			public static class Receiver extends ADMMessageReceiver
    		{
        		public Receiver()
        		{
            		super(MyADMMessageHandler.class);
        		}
    		}

			private void sendNotification(String msg) {
				Context ctx = getApplicationContext();

	   		 mNotificationManager = (NotificationManager)
	    			ctx.getSystemService(Context.NOTIFICATION_SERVICE);

	    	PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
	          	new Intent(ctx, MainActivity.class), 0);

	    	NotificationCompat.Builder mBuilder =
	          	new NotificationCompat.Builder(ctx)
	          	.setSmallIcon(R.drawable.ic_launcher)
	          	.setContentTitle("Notification Hub Demo")
	          	.setStyle(new NotificationCompat.BigTextStyle()
	                     .bigText(msg))
	          	.setContentText(msg);

	     	mBuilder.setContentIntent(contentIntent);
	     	mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}

4. 將下列程式碼新增至 `OnMessage()` 方法：

		String nhMessage = intent.getExtras().getString("msg");
		sendNotification(nhMessage);

5. 將下列程式碼新增至 `OnRegistered` 方法：

			try {
		getNotificationHub(getApplicationContext()).register(registrationId);
			} catch (Exception e) {
		Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
			}

6.	將下列程式碼新增至 `OnUnregistered` 方法：

			try {
				getNotificationHub(getApplicationContext()).unregister();
			} catch (Exception e) {
				Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
			}

7. 然後，在 `MainActivity` 方法中新增下列 import 陳述式：

		import com.amazon.device.messaging.ADM;

8. 現在，在 `OnCreate` 方法的結尾新增下列程式碼：

		final ADM adm = new ADM(this);
		if (adm.getRegistrationId() == null)
		{
		   adm.startRegister();
		} else {
			new AsyncTask() {
			      @Override
			      protected Object doInBackground(Object... params) {
			         try {			        	 MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
			         } catch (Exception e) {
			        	 Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
			        	 return e;
			         }
			         return null;
			     }
			   }.execute(null, null, null);
		}

## 將您的 APIKey 新增至應用程式

1. 在 Eclipse 中，在您專案的目錄資產中建立名為 **api_key.txt** 的新檔案。
2. 開啟該檔案，並複製您在 Amazon 開發人員入口網站中產生的 [API 金鑰]。

## 執行應用程式

1. 啟動模擬器。
2. 在模擬器中從頂端撥動，然後按一下 [設定]，再按一下 [我的帳戶]，然後使用有效的 Amazon 帳戶進行註冊。
3. 在 Eclipse 中執行應用程式。

> [AZURE.NOTE]如果發生問題，請檢查模擬器 (或裝置) 的時間。時間值必須是正確的。若要變更 Kindle 模擬器的時間，您可以從 Android SDK platform-tools 目錄執行下列命令：

		adb shell  date -s "yyyymmdd.hhmmss"

## 傳送訊息

若要使用 .NET 傳送訊息：

		static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{"data":{"msg" : "Hello from .NET!"}}").Wait();
        }

![][7]

<!-- URLs. -->
[開發人員入口網站]: https://developer.amazon.com/home.html
[下載 SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
 

<!---HONumber=July15_HO2-->