<properties 
	pageTitle="通知中樞即時新聞教學課程 - Android" 
	description="了解如何使用 Azure 服務匯流排通知中樞將本地化重大新聞通知傳送至 Android 裝置。" 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="wesmc"/>


# 使用通知中心傳送即時新聞

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

##概觀

本主題將說明如何使用 Azure 通知中心，將即時新聞通知廣播至 Android 應用程式。完成時，您便能夠註冊您所感興趣的即時新聞類別，並僅接收這些類別的推播通知。此情況是許多應用程式的共同模式，這些應用程式必須將通知傳送給先前宣告對通知有興趣的使用者群組，例如，RSS 閱讀程式、供樂迷使用的應用程式等等。

在通知中心內建立註冊時，您可以透過包含一或多個 _tags_ 來啟用廣播案例。當標籤收到通知時，所有已註冊此標籤的裝置都會收到通知。由於標籤只是簡單的字串而已，您無需預先佈建標籤。如需標籤的詳細資訊，請參閱[通知中心指引]。


##必要條件

本主題會以您在[開始使用通知中心][get-started]中所建立的應用程式為基礎。開始本教學課程之前，您必須已完成[開始使用通知中心][get-started]。

##在應用程式中新增類別選項

第一個步驟是在您現有的主要活動上新增 UI 元素，以便使用者選取要註冊的類別。使用者所選取的類別會儲存在裝置上。啟動應用程式時，您的通知中心內會建立以所選取類別作為標籤的裝置註冊。

1. 開啟您的 res/layout/activity_main.xml 檔案，並將內容取代為下列項目：
			
		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		    xmlns:tools="http://schemas.android.com/tools"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:paddingBottom="@dimen/activity_vertical_margin"
		    android:paddingLeft="@dimen/activity_horizontal_margin"
		    android:paddingRight="@dimen/activity_horizontal_margin"
		    android:paddingTop="@dimen/activity_vertical_margin"
		    tools:context="com.example.breakingnews.MainActivity"
		    android:orientation="vertical">
		
		        <CheckBox
		            android:id="@+id/worldBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_world" />
		        <CheckBox
		            android:id="@+id/politicsBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_politics" />
		        <CheckBox
		            android:id="@+id/businessBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_business" />
		        <CheckBox
		            android:id="@+id/technologyBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_technology" />
		        <CheckBox
		            android:id="@+id/scienceBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_science" />
		        <CheckBox
		            android:id="@+id/sportsBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_sports" />
			    <Button
			        android:layout_width="wrap_content"
			        android:layout_height="wrap_content"
			        android:onClick="subscribe"
			        android:text="@string/button_subscribe" />
		</LinearLayout>

2. 開啟您的 res/values/strings.xml 檔案，並新增以下幾行：

	    <string name="button_subscribe">Subscribe</string>
	    <string name="label_world">World</string>
	    <string name="label_politics">Politics</string>
	    <string name="label_business">Business</string>
	    <string name="label_technology">Technology</string>
	    <string name="label_science">Science</string>
	    <string name="label_sports">Sports</string>

	您的 main_activity.xml 圖形配置看起來應該如下所示：

	![][A1]

3. 現在，在與 [MainActivity] 類別相同的封裝中建立類別 [通知]。

		import java.util.HashSet;
		import java.util.Set;
		
		import android.content.Context;
		import android.content.SharedPreferences;
		import android.os.AsyncTask;
		import android.util.Log;
		import android.widget.Toast;
		
		import com.google.android.gms.gcm.GoogleCloudMessaging;
		import com.microsoft.windowsazure.messaging.NotificationHub;		
		
		public class Notifications {
			private static final String PREFS_NAME = "BreakingNewsCategories";
			private GoogleCloudMessaging gcm;
			private NotificationHub hub;
			private Context context;
			private String senderId;
			
			public Notifications(Context context, String senderId) {
				this.context = context;
				this.senderId = senderId;
				
				gcm = GoogleCloudMessaging.getInstance(context);
		        hub = new NotificationHub(<hub name>, <connection string with listen access>, context);
			}
			
			public void storeCategoriesAndSubscribe(Set<String> categories)
			{
				SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
			    settings.edit().putStringSet("categories", categories).commit();
			    subscribeToCategories(categories);
			}
			
			public void subscribeToCategories(final Set<String> categories) {
				new AsyncTask<Object, Object, Object>() {
					@Override
					protected Object doInBackground(Object... params) {
						try {
							String regid = gcm.register(senderId);
					        hub.register(regid, categories.toArray(new String[categories.size()]));
						} catch (Exception e) {
							Log.e("MainActivity", "Failed to register - " + e.getMessage());
							return e;
						}
						return null;
					}
		
					protected void onPostExecute(Object result) {
						String message = "Subscribed for categories: "
								+ categories.toString();
						Toast.makeText(context, message,
								Toast.LENGTH_LONG).show();
					}
				}.execute(null, null, null);
			}
			
		}

	本類別會使用本機儲存體來儲存此裝置必須接收的新聞類別。它也包含註冊這些類別的方法。

4. 在上述程式碼中，使用您的通知中樞名稱及先前取得的 *DefaultListenSharedAccessSignature* 連接字串，來取代 `<hub name>` 和 `<connection string with listen access>` 預留位置。

	> [AZURE.NOTE]因為隨用戶端應用程式散佈的憑證通常不安全，您應只將接聽存取權的金鑰隨用戶端應用程式散佈。您的應用程式可透過接聽存取權來註冊通知，但無法修改現有的註冊或無法傳送通知。在安全的後端服務中，會使用完整存取金鑰來傳送通知和變更現有的註冊。

4. 在您的 [MainActivity] 類別中，移除您 [NotificationHub] 和 [GoogleCloudMessaging] 的私人欄位，並新增 [通知] 的欄位：

		// private GoogleCloudMessaging gcm;
		// private NotificationHub hub;
		private Notifications notifications;
 
5. 接著，在 **onCreate** 方法中，將 **hub** 欄位的初始設定和 **registerWithNotificationHubs** 方法移除。接著，新增以下幾行以初始化 [通知] 類別的執行個體。此方法應包含以下幾行：

		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);
	
			NotificationsManager.handleNotifications(this, SENDER_ID,
					MyHandler.class);
	
			notifications = new Notifications(this, SENDER_ID);
		}

6. 然後，新增下列方法：
	
	    public void subscribe(View sender) {
			final Set<String> categories = new HashSet<String>();
	
			CheckBox world = (CheckBox) findViewById(R.id.worldBox);
			if (world.isChecked())
				categories.add("world");
			CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
			if (politics.isChecked())
				categories.add("politics");
			CheckBox business = (CheckBox) findViewById(R.id.businessBox);
			if (business.isChecked())
				categories.add("business");
			CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
			if (technology.isChecked())
				categories.add("technology");
			CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
			if (science.isChecked())
				categories.add("science");
			CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
			if (sports.isChecked())
				categories.add("sports");
	
			notifications.storeCategoriesAndSubscribe(categories);
	    }
	
	此方法會建立一份類別清單，並使用 **Notifications** 類別在本機儲存體中儲存清單，並在通知中心註冊對應標籤。變更類別時，系統會使用新類別重新建立註冊。

您的應用程式現在可以在裝置上的本機儲存體中儲存一組類別，並在使用者每次變更類別選項時在通知中心註冊。

##註冊通知

這些步驟會在啟動時，使用已儲存在本機儲存體中的類別在通知中心註冊。

> [AZURE.NOTE]由於 Google 雲端通訊 (GCM) 所指派的 registrationId 可以隨時變更，您應經常註冊通知以避免通知失敗。此範例會在應用程式每次啟動時註冊通知。若是經常執行 (一天多次) 的應用程式，如果距離上次註冊的時間不到一天，則您可能可以略過註冊以保留頻寬。

1. 在 **Notifications** 類別中新增下列程式碼：

		public Set<String> retrieveCategories() {
			SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
			return settings.getStringSet("categories", new HashSet<String>());
		}

	這會傳回類別中所定義的類別。

2. 現在，在 [MainActivity] 類別中的 [onCreate] 方法結尾處新增下列程式碼：

		notifications.subscribeToCategories(notifications.retrieveCategories());

	這會確保應用程式每次啟動時都會從本機儲存體擷取類別，並要求這些類別的註冊。[開始使用通知中心] 教學課程的一部分是建立 **InitNotificationsAsync** 方法，但本主題中將不會用到。

3. 接著，將下列方法新增至 [MainActivity]：

		@Override
		protected void onStart() {
			super.onStart();
			
			Set<String> categories = notifications.retrieveCategories();
			
			CheckBox world = (CheckBox) findViewById(R.id.worldBox);
			world.setChecked(categories.contains("world"));
			CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
			politics.setChecked(categories.contains("politics"));
			CheckBox business = (CheckBox) findViewById(R.id.businessBox);
			business.setChecked(categories.contains("business"));
			CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
			technology.setChecked(categories.contains("technology"));
			CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
			science.setChecked(categories.contains("science"));
			CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
			sports.setChecked(categories.contains("sports"));
		}

	這會根據原先儲存的類別狀態更新主要活動。

現在已完成此應用程式，且可在裝置本機儲存體中儲存一組類別，以供每次使用者變更類別選項在通知中心註冊時使用。接著，我們會定義可將類別通知傳送至此應用程式的後端。

##從後端傳送通知

[AZURE.INCLUDE [notification-hubs-back-end](../../includes/notification-hubs-back-end.md)]

##執行應用程式並產生通知

1. 在 Eclipse 中建置應用程式，並在裝置或模擬器上加以啟動。
	
	請注意，應用程式 UI 提供一組切換，可讓您選擇要訂閱的類別。

2. 啟用一或多個類別切換，然後按一下 [訂閱]。

	應用程式會將選取的類別轉換成標籤，並在通知中心內為選取的標籤要求新裝置註冊。系統會傳回已註冊類別並顯示在對話方塊中。

4. 若要從後端傳送新通知，您可以使用下列其中一種方式：

	+ **.NET 主控台應用程式：**啟動主控台應用程式。

	+ **Java/PHP：**執行您的應用程式/指令碼。

	選取的類別通知會以快顯通知方式出現。

##後續步驟

在本教學課程中，我們了解到如何按類別廣播即時新聞。請考慮完成下列其中一個強調其他進階通知中心案例的教學課程：

+ [使用通知中樞廣播已當地語系化的即時新聞]

	了解如何擴充即時新聞應用程式，以啟用傳送已當地語系化的通知。

+ [使用通知中樞來通知使用者]

	了解如何推播通知給特定的經驗證使用者。在僅傳送通知給特定使用者的情況下，這是很好的解決方案。




<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-get-started.md
[使用通知中樞廣播已當地語系化的即時新聞]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[使用通知中樞來通知使用者]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[通知中心指引]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
 

<!---HONumber=July15_HO1-->