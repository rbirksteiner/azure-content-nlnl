1. 在您的 **app** 專案中開啟 `AndroidManifest.xml` 檔案。在下兩個步驟的程式碼中，以專案的應用程式套件名稱 (即 `manifest` 標籤中的 `package` 屬性值) 取代 _`**my_app_package**`_。 

2. 在現有 `uses-permission` 元素中新增下列新權限：

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. 在 `application` 起始標籤後新增下列程式碼：

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            						 	android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. 在應用程式目錄中 **build.gradle** 檔案的 *dependencies* 底下新增下列程式碼行，並重新同步 gradle 與專案：

	    compile(group: 'com.microsoft.azure', name: 'azure-notifications-handler', version: '1.0.1', ext: 'jar')


5. 開啟 *ToDoItemActivity.java* 檔案，新增下列 import 陳述式：

		import com.microsoft.windowsazure.notifications.NotificationsManager;


6. 將下列的私用變數新增至類別：以先前程序中由 Google 指派給應用程式的專案編號取代 _`<PROJECT_NUMBER>`_。

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

7. 將 *MobileServiceClient* 的定義從 [私用] 變更為 [公用靜態]，如下所示：

		public static MobileServiceClient mClient;



8. 接下來我們需要加入新類別來處理通知。在 [專案總管] 中，開啟 **src** => **main** => **java** 節點，然後以滑鼠右鍵按一下封裝名稱節點：按一下 [**新增**]，然後按一下 [**Java 類別**]。

9. 在 [名稱] 中，輸入 `MyHandler`，然後按一下 [確定]。


	![](./media/mobile-services-android-get-started-push/android-studio-create-class.png)


10. 在 MyHandler 檔案中，將類別宣告取代為

		public class MyHandler extends NotificationsHandler {


11. 為 `MyHandler` 類別新增下列匯入陳述式：

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.AsyncTask;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;

	
12. 接著為 `MyHandler` 類別新增下列成員：

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;


13. 在 `MyHandler` 類別中，新增下列程式碼以覆寫 **onRegistered** 方法 (向行動服務通知中心註冊您的裝置)。

		@Override
		public void onRegistered(Context context,  final String gcmRegistrationId) {
		    super.onRegistered(context, gcmRegistrationId);
	
		    new AsyncTask<Void, Void, Void>() {
		    		    	
		    	protected Void doInBackground(Void... params) {
		    		try {
		    		    ToDoActivity.mClient.getPush().register(gcmRegistrationId, null);
		    		    return null;
	    		    }
	    		    catch(Exception e) { 
			    		// handle error    		    
	    		    }
					return null;  		    
	    		}
		    }.execute();
		}



14. 在 `MyHandler` 類別中，新增下列程式碼以覆寫 **onReceive** 方法 (在收到通知時隨即顯示)。

		@Override
		public void onReceive(Context context, Bundle bundle) {
		    ctx = context;
		    String nhMessage = bundle.getString("message");
	
		    sendNotification(nhMessage);
		}
	
		private void sendNotification(String msg) {
			mNotificationManager = (NotificationManager)
		              ctx.getSystemService(Context.NOTIFICATION_SERVICE);
	
		    PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		          new Intent(ctx, ToDoActivity.class), 0);
	
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


15. 回到 TodoActivity.java 檔案，更新 *ToDoActivity* 類別的 **onCreate** 方法，以註冊通知處理常式類別。請務必在 *MobileServiceClient* 具現化之後，新增此程式碼。


		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    您的應用程式現在已更新為支援推播通知。

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q

<!---HONumber=July15_HO2-->