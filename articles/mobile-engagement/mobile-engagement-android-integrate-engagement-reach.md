<properties 
	pageTitle="Azure Mobile Engagement Android SDK 整合" 
	description="Android SDK for Azure Mobile Engagement 的最新更新和程序"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#如何在 Android 上整合 Engagement Reach

> [AZURE.IMPORTANT]您必須遵循＜如何在 Android 上整合＞文件中所述的整合程序，才能接著遵循本指南。

##標準整合

Reach SDK 需要「Android 支援程式庫 (v4)」。

在 Eclipse 中，將程式庫加入到專案的最快方法是 `Right click on your project -> Android Tools -> Add Support Library...`。

如果您未使用 Eclipse，可以先閱讀[這裡]的指示。

從專案中的 SDK 複製 Reach 資源檔：

-   將 SDK 所附 `res/layout` 資料夾中的檔案複製到應用程式的 `res/layout` 資料夾。
-   將 SDK 所附 `res/drawable` 資料夾中的檔案複製到應用程式的 `res/drawable` 資料夾。

編輯 `AndroidManifest.xml` 檔案：

-   加入下列區段 (在 `<application>` 和 `</application>` 標記之間)：

			<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT" />
			    <data android:mimeType="text/plain" />
			  </intent-filter>
			</activity>
			<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT" />
			    <data android:mimeType="text/html" />
			  </intent-filter>
			</activity>
			<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
			    <category android:name="android.intent.category.DEFAULT" />
			  </intent-filter>
			</activity>
			<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			  </intent-filter>
			</activity>
			<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			  <intent-filter>
			    <action android:name="android.intent.action.BOOT_COMPLETED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			  </intent-filter>
			</receiver>
			<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
			  <intent-filter>
			    <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			  </intent-filter>
			</receiver>

-   您需要此權限才能重新執行開機時未按下的系統通知 (否則它們將保留在磁碟上，但不會再顯示，您真的必須加入此區段)。

			<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

-   複製和編輯下列區段 (在 `<application>` 和 `</application>` 標記之間)，以指定用於通知的圖示 (在應用程式中的圖示以及系統圖示)：

			<meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [AZURE.IMPORTANT]如果您打算在建立 Reach 活動時使用系統通知，則「務必」使用此區段。Android 禁止顯示沒有圖示的系統通知。因此如果您省略此區段，使用者將無法接收系統通知。

-   建立活動時，當系統通知使用大圖片，若缺少下列權限便需先行加入 (在 `</application>` 標記之後)：

			<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
			<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

-   針對系統通知，如果裝置應該響鈴及/或震動，您也可以在 Reach 活動中指定。要讓其正常運作，您必須確定已宣告下列權限 (在 `</application>` 標記之後)：

			<uses-permission android:name="android.permission.VIBRATE" />

	若無此權限，如果您在 Reach 活動管理員中核取了響鈴或震動的選項，Android 會禁止顯示系統通知。

-   如果使用 ProGuard 建置應用程式，且發生與 Android 支援程式庫或 Engagement jar 相關的錯誤，請在 `proguard.cfg` 檔案中加入下列幾行：

			-dontwarn android.**
			-keep class android.support.v4.** { *; }

## 原生推送

現在，您會設定 Reach 模組，您需要設定原生推播以便在裝置上接收行銷活動。

我們在 Android 上支援兩種服務：

  - Google Play 裝置：遵循[如何整合 GCM 與 Engagement 指南](mobile-engagement-android-gcm-integrate.md)，來使用 [Google Cloud Messaging]。
  - Amazon 裝置：遵循[如何整合 ADM 與 Engagement 指南](mobile-engagement-android-adm-integrate.md)，來使用 [Amazon Device Messaging]

如果您的目標想要同時鎖定 Amazon 和 Google Play 裝置，可以將所有東西放在一個 AndroidManifest.xml/APK 進行開發。但提交給 Amazon 時，如果他們發現 GCM 程式碼可能會拒絕您的應用程式。

您應該在此情況下使用多個 APK。

**現在您的應用程式已準備好接收及顯示 Reach 活動！**

##如何處理資料推送

### 整合

如果希望應用程式能夠接收 Reach 資料推送，您必須建立 `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` 的子類別，並在 `AndroidManifest.xml` 檔案中加以參考 (在 `<application>` 及/或 `</application>` 標記之間)：

			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
			  </intent-filter>
			</receiver>

然後便可以覆寫 `onDataPushStringReceived` 和 `onDataPushBase64Received` 回呼。下列是一個範例：

			public class MyDataPushReceiver extends EngagementReachDataPushReceiver
			{
			  @Override
			  protected Boolean onDataPushStringReceived(Context context, String category, String body)
			  {
			    Log.d("tmp", "String data push message received: " + body);
			    return true;
			  }
			
			  @Override
			  protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
			  {
			    Log.d("tmp", "Base64 data push message received: " + encodedBody);
			    // Do something useful with decodedBody like updating an image view
			    return true;
			  }
			}

### 類別

當您建立「資料推送」活動時，類別參數是選用的，並且允許您篩選資料推送。若您有數個廣播接收器在處理不同類型的資料推送，或是您希望推送不同種類的 `Base64` 資料且想要在剖析之前識別其類型，便相當適合使用此範例。

### 回呼的傳回參數

以下是正確處理 `onDataPushStringReceived` 和 `onDataPushBase64Received` 傳回參數的部分指導方針：

-   如果廣播接收器不知道如何處理資料推送，應會在回呼中傳回 `null`。您應該使用類別目錄來判斷廣播接收器是否應處理資料推送。
-   如果接受資料推送，其中一個廣播接收器應會在回呼中傳回 `true`。
-   如果可辨識資料推送，但因為各種原因而捨棄，則其中一個廣播接收器應會在回呼中傳回 `false`。例如，當接收的資料無效時傳回 `false`。
-   如果一個廣播接收器傳回 `true`，而另一個針對相同的資料推送傳回 `false`，則屬於未定義行為，您絕不應該這麼做。

傳回類型只用於 Reach 統計資料：

-   如果其中一個廣播接收器傳回 `true` 或 `false`，則 `Replied` 會遞增。
-   只有在其中一個廣播接收器傳回 `true` 時，`Actioned` 才會遞增。

##如何自訂活動

若要自訂活動，您可以修改 Reach SDK 中提供的配置。

您應該保留配置中使用的所有識別碼，並保留使用識別碼的檢視類型，尤其是文字檢視和影像檢視。有些檢視只用來隱藏或顯示區域，所以可能會變更其類型。如果您想要變更所提供配置中的檢視類型，請檢查原始碼。

### 通知

有兩種類型的通知：系統和應用程式內通知，它們使用不同的配置檔。

#### 系統通知

若要自訂系統通知，您需要使用 [類別]。您可以跳到 [類別][](#categories)。

#### 應用程式內通知

根據預設，應用程式內通知是採用 Android 方法 `addContentView()`，以動態方式加入至目前活動使用者介面的檢視。這稱為通知重疊。通知重疊非常適合快速整合，因為它們不需要您修改應用程式中的任何配置。

若要修改通知重疊的外觀，您只需依照需求修改檔案 `engagement_notification_area.xml` 即可。

> [AZURE.NOTE]檔案 `engagement_notification_overlay.xml` 是用來建立通知重疊的檔案，其中也包含檔案 `engagement_notification_area.xml`。您也可以自訂以配合您的需求 (例如在覆疊內定位通知區域)。

##### 包含通知配置做為活動配置的一部分

覆疊非常適合快速整合，但可能在特殊情況下造成不便或有副作用。可以在活動層級自訂覆疊系統，以便輕鬆避免特殊活動的副作用。

您可以決定使用 Android **include** 陳述式，將通知配置納入您現有的配置。以下是修改過的 `ListActivity` 配置範例，且其中只包含 `ListView`。

**Engagement 整合之前：**

			<?xml version="1.0" encoding="utf-8"?>
			<ListView
			  xmlns:android="http://schemas.android.com/apk/res/android"
			  android:id="@android:id/list"
			  android:layout_width="fill_parent"
			  android:layout_height="fill_parent" />

**Engagement 整合之後：**

			<?xml version="1.0" encoding="utf-8"?>
			<LinearLayout
			  xmlns:android="http://schemas.android.com/apk/res/android"
			  android:orientation="vertical"
			  android:layout_width="fill_parent"
			  android:layout_height="fill_parent">
			
			  <ListView
			    android:id="@android:id/list"
			    android:layout_width="fill_parent"
			    android:layout_height="fill_parent"
			    android:layout_weight="1" />
			
			  <include layout="@layout/engagement_notification_area" />
			
			</LinearLayout>

在此範例中我們加入了父容器，因為原始配置使用清單檢視做為最上層項目。我們也加入了 `android:layout_weight="1"`，以便在使用 `android:layout_height="fill_parent"` 設定的清單檢視下加入檢視。

Engagement Reach SDK 會自動偵測到通知配置已包含在此活動中，且不會加入此活動的覆疊。

> [AZURE.TIP]如果您在應用程式中使用 ListActivity，可見的 Reach 覆疊會使您無法再對清單檢視中的被按項目做出反應。這是已知的問題。若要暫時解決這個問題，我們建議您在自己的清單活動配置中內嵌通知配置，就像在先前範例那樣。

##### 停用關於活動的應用程式通知

如果您不想要覆疊加入至您的活動，且您自己的配置中也未包含通知配置，您可以在 `AndroidManifest.xml` 停用此活動的覆疊，方法是加入 `meta-data` 區段，如同下列範例所示：

			<activity android:name="SplashScreenActivity">
			  <meta-data android:name="engagement:notification:overlay" android:value="false"/>
			</activity>

#### <a name="categories"></a> 類別

當您修改提供的配置時，您會修改所有通知的外觀。類別允許您定義通知的各種目標外觀 (也可能是行為)。當您建立觸達活動時可以指定類別。請記住，類別也可讓您自訂宣告與輪詢，本文件稍後將會說明。

若要登記通知的類別處理常式，您需要在應用程式初始化時加入呼叫。

> [AZURE.IMPORTANT]請閱讀＜如何在 Android 上整合 Engagement＞主題中關於 android:process attribute <android-sdk-engagement-process> 的警告，然後再繼續。

下列範例假設您已知悉先前的警告，並使用 `EngagementApplication` 的子類別：

			public class MyApplication extends EngagementApplication
			{
			  @Override
			  protected void onApplicationProcessCreate()
			  {
			    // [...] other init
			    EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
			    reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
			  }
			}

`MyNotifier` 物件是通知類別處理常式的實作。這是 `EngagementNotifier` 介面的實作，或是預設實作的子類別：`EngagementDefaultNotifier`。

請注意，相同通知程式能夠處理數種類別，您可以像這樣登記它們：

			reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

若要取代預設類別實作，您可以如下列範例中地登記您的實作：

			public class MyApplication extends EngagementApplication
			{
			  @Override
			  protected void onApplicationProcessCreate()
			  {
			    // [...] other init
			    EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
			    reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
			  }
			}

處理常式中目前使用的類別會在您可以於 `EngagementDefaultNotifier` 覆寫的大部分方法中傳遞做為參數。

這能以 `String` 參數的形式傳遞，或是間接在 `EngagementReachContent` 物件中傳遞 (使用 `getCategory()` 方法)。

您可以在 `EngagementDefaultNotifier` 重新方法定義方法，藉此變更大部分的通知建立程序；如需更進一步自訂，歡迎查看技術文件和原始程式碼。

##### 應用程式內通知

如果您只想要針對特定類別使用替代配置，可以如下列範例所示實作：
			
			public class MyNotifier extends EngagementDefaultNotifier
			{
			  public MyNotifier(Context context)
			  {
			    super(context);
			  }
			
			  @Override
			  protected int getOverlayLayoutId(String category)
			  {
			    return R.layout.my_notification_overlay;
			  }
			
			
			  @Override
			  public Integer getOverlayViewId(String category)
			  {
			    return R.id.my_notification_overlay;
			  }
			
			  @Override
			  public Integer getInAppAreaId(String category)
			  {
			    return R.id.my_notification_area;
			  }
			}

**`my_notification_overlay.xml` 的範例：**

			<?xml version="1.0" encoding="utf-8"?>
			<RelativeLayout
			  xmlns:android="http://schemas.android.com/apk/res/android"
			  android:id="@+id/my_notification_overlay"
			  android:layout_width="fill_parent"
			  android:layout_height="fill_parent">
			
			  <include layout="@layout/my_notification_area" />
			
			</RelativeLayout>

如您所見，覆疊檢視識別碼與標準不同。很重要的是，每個配置要針對覆疊使用唯一識別碼。

**`my_notification_area.xml` 的範例：**

			<?xml version="1.0" encoding="utf-8"?>
			<merge
			  xmlns:android="http://schemas.android.com/apk/res/android"
			  android:layout_width="fill_parent"
			  android:layout_height="fill_parent">
			
			  <RelativeLayout
			    android:id="@+id/my_notification_area"
			    android:layout_width="fill_parent"
			    android:layout_height="64dp"
			    android:layout_alignParentTop="true"
			    android:background="#B000">
			
			    <LinearLayout
			      android:orientation="horizontal"
			      android:layout_width="fill_parent"
			      android:layout_height="fill_parent"
			      android:gravity="center_vertical">
			
			      <ImageView
			        android:id="@+id/engagement_notification_icon"
			        android:layout_width="48dp"
			        android:layout_height="48dp" />
			
			      <LinearLayout
			        android:id="@+id/engagement_notification_text"
			        android:orientation="vertical"
			        android:layout_width="fill_parent"
			        android:layout_height="fill_parent"
			        android:layout_weight="1"
			        android:gravity="center_vertical">
			
			        <TextView
			          android:id="@+id/engagement_notification_title"
			          android:layout_width="fill_parent"
			          android:layout_height="wrap_content"
			          android:singleLine="true"
			          android:ellipsize="end"
			          android:textAppearance="@android:style/TextAppearance.Medium" />
			
			        <TextView
			          android:id="@+id/engagement_notification_message"
			          android:layout_width="fill_parent"
			          android:layout_height="wrap_content"
			          android:maxLines="2"
			          android:ellipsize="end"
			          android:textAppearance="@android:style/TextAppearance.Small" />
			
			      </LinearLayout>
			
			      <ImageView
			        android:id="@+id/engagement_notification_image"
			        android:layout_width="wrap_content"
			        android:layout_height="fill_parent"
			        android:adjustViewBounds="true" />
			
			      <ImageButton
			        android:id="@+id/engagement_notification_close_area"
			        android:visibility="invisible"
			        android:layout_width="wrap_content"
			        android:layout_height="fill_parent"
			        android:src="@android:drawable/btn_dialog"
			        android:background="#0F00" />
			
			    </LinearLayout>
			
			    <ImageButton
			      android:id="@+id/engagement_notification_close"
			      android:layout_width="wrap_content"
			      android:layout_height="fill_parent"
			      android:layout_alignParentRight="true"
			      android:src="@android:drawable/btn_dialog"
			      android:background="#0F00" />
			
			  </RelativeLayout>
			
			</merge>

如您所見，通知區域檢視識別碼與標準不同。很重要的是，每個配置要針對通知區域使用唯一識別碼。

這個簡單的類別範例會讓應用程式 (或應用程式內) 通知顯示在畫面頂端。我們並未變更在通知區域本身所使用的標準識別碼。

如果您要加以變更，則必須重新定義 `EngagementDefaultNotifier.prepareInAppArea` 方法。如果想要這個層級的進一步自訂，建議查看 `EngagementNotifier` 和 `EngagementDefaultNotifier` 的技術文件和原始程式碼。

##### 系統通知

藉由延伸 `EngagementDefaultNotifier`，您可以覆寫 `onNotificationPrepared` 以改變預設實作所準備的通知。

例如：

			@Override
			protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
			  throws RuntimeException
			{
			  if ("ongoing".equals(content.getCategory()))
			    notification.flags |= Notification.FLAG_ONGOING_EVENT;
			  return true;
			}

此範例在使用「進行中」類別時，針對正在顯示為進行中事件的內容產生系統通知。

如果您想要從頭建置 `Notification` 物件，可以將 `false` 傳回給方法，然後自行在 `NotificationManager` 呼叫 `notify`。在此情況下，務必要保留 `contentIntent`、`deleteIntent` 和 `EngagementReachReceiver` 所使用的通知識別碼。

以下是這類實作的正確範例：

			@Override
			protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
			{
			  /* Required fields */
			  NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
			    .setSmallIcon(notification.icon)              // icon is mandatory
			    .setContentIntent(notification.contentIntent) // keep content intent
			    .setDeleteIntent(notification.deleteIntent);  // keep delete intent
			
			  /* Your customization */
			  // builder.set...
			
			  /* Dismiss option can be managed only after build */
			  Notification myNotification = builder.build();
			  if (!content.isNotificationCloseable())
			    myNotification.flags |= Notification.FLAG_NO_CLEAR;
			
			  /* Notify here instead of super class */
			  NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
			  manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier
			
			  /* Return false, we notify ourselves */
			  return false;
			}

##### 僅通知的公告

對於僅限通知之公告的點擊管理，可以藉由覆寫 `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` 以修改已備妥之 `Intent` 來自訂。使用此方法可讓您輕鬆地調整旗標。

例如，若要加入 `SINGLE_TOP` 旗標：
			
			@Override
			protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
			  Intent intent)
			{
			  intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
			  return intent;
			}

對於舊版的 Engagement 使用者，請注意，沒有動作 URL 的系統通知現在會啟動在背景中的應用程式，所以可以用沒有動作 URL 的公告來呼叫這個方法。自訂意圖時，您應該考慮它。

您也可以從頭實作 `EngagementNotifier.executeNotifAnnouncementAction`。

##### 通知生命週期

使用預設類別時，會在 `EngagementReachInteractiveContent` 物件上呼叫部分生命週期方法，來報告統計資料並更新活動狀態：

-   當通知顯示在應用程式中或放在狀態列時，如果 `handleNotification` 傳回 `true`，則 `EngagementReachAgent` 會呼叫 `displayNotification` 方法 (這會報告統計資料)。
-   如果關閉通知，則會呼叫 `exitNotification` 方法、報告統計資料，且可立即處理下一個活動。
-   如果按一下通知，則會呼叫 `actionNotification`、報告統計資料，並啟動相關聯的意圖。

如果您 `EngagementNotifier` 的實作略過預設行為，您必須自行呼叫這些生命週期方法。以下範例說明一些會略過預設行為的情況：

-   您不需延伸 `EngagementDefaultNotifier`，例如從頭開始實作類別處理。
-   若為系統通知，您已覆寫 `onNotificationPrepared` 並修改 `Notification` 物件中的 `contentIntent` 或 `deleteIntent`。
-   若為應用程式內通知，您已覆寫 `prepareInAppArea`，請務必至少將 `actionNotification` 對應到其中一個 U.I 控制項。

> [AZURE.NOTE]如果 `handleNotification` 擲回例外狀況，會刪除內容然後呼叫 `dropContent`。這報告在統計資料中，並且立即可以處理接下來的活動。

### 宣告和輪詢

#### 版面配置

您可以修改 `engagement_text_announcement.xml`、`engagement_web_announcement.xml` 和 `engagement_poll.xml` 檔案，以自訂文字公告、Web 公告和輪詢。

這些檔案的標題區域和按鈕區域共用兩個共同的配置。標題的配置是 `engagement_content_title.xml`，並針對背景使用具名的可繪製檔案。動作和結束按鈕的配置是 `engagement_button_bar.xml`，並針對背景使用具名的可繪製檔案。

在輪詢中，問題配置和選項會多次針對問題使用 `engagement_question.xml` 配置檔，並針對選項使用 `engagement_choice.xml` 檔案，動態地膨脹。

#### 類別

##### 替代版面配置

類似於通知，活動類別可以用來做為宣告和輪詢的替代版片配置。

例如，若要為文字公告建立類別，您可以延伸 `EngagementTextAnnouncementActivity` 並在 `AndroidManifest.xml` 檔案中加以參考：

			<activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.ANNOUNCEMENT"/>
			    <category android:name="my_category" />
			    <data android:mimeType="text/plain" />
			  </intent-filter>
			</activity>

請注意，意圖篩選中的類別會用來產生與預設公告活動的差異。

Reach SDK 使用意圖系統來解析特定類別的正確活動，如果解析失敗便會回到預設類別。

那麼，您必須實作 `MyCustomTextAnnouncementActivity`，如果只想要變更配置 (但保留相同的檢視識別碼)，您只需要像下列範例所示定義類別：

			public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
			{
			  @Override
			  protected String getLayoutName()
			  {
			    return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
			  }
			}

若要取代文字公告的預設類別，只要在您的實作中取代 `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` 即可。

可以以類似的方式自訂 web 公告與輪詢。

針對 Web 公告，您可以延伸 `EngagementWebAnnouncementActivity` 並在 `AndroidManifest.xml` 中宣告活動，如下列範例所示：

			<activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.ANNOUNCEMENT"/>
			    <category android:name="my_category" />
			    <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
			  </intent-filter>
			</activity>

針對輪詢，您可以延伸 `EngagementPollActivity` 並在 `AndroidManifest.xml` 中宣告活動，如下列範例所示：

			<activity android:name="com.your_company.MyCustomPollActivity">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.POLL"/>
			    <category android:name="my_category" />
			  </intent-filter>
			</activity>
			
##### 從頭實作

您可以實作公告 (和輪詢) 活動的類別，而不延伸 Reach SDK 所提供的其中一個 `Engagement*Activity` 類別。這非常適用於您想要定義不會使用與標準配置相同之檢視的配置。

就像對進階通知自訂一樣，也建議您查看標準實作的原始程式碼。

請記住以下幾點：Reach 將會以特定的意圖 (相對應於意圖篩選)，加上額外的參數，也就是內容識別碼來啟動活動。

若要擷取包含您在網站上建立活動時指定的內容物件，您可以這樣做：

			public class MyCustomTextAnnouncement extends EngagementActivity
			{
			  private EngagementAnnouncement mContent;
			
			  @Override
			  protected void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			
			    /* Get content */
			    mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
			    if (mContent == null)
			    {
			      /* If problem with content, exit */
			      finish();
			      return;
			    }
			
			    setContentView(R.layout.my_text_announcement);
			
			    /* Configure views by querying fields on mContent */
			    // ...
			  }
			}

若為統計資料，您應該報告內容已顯示在 `onResume` 事件中：
			
			@Override
			protected void onResume()
			{
			 /* Mark the content displayed */
			 mContent.displayContent(this);
			 super.onResume();
			}

接著，別忘了在活動進入背景之前，在內容物件上呼叫 `actionContent(this)` 或 `exitContent(this)`。

如果您不呼叫 `actionContent` 或 `exitContent`，將不會傳送統計資料 (亦即不會分析活動)；更重要的是，在應用程式處理程序重新啟動之前，都不會通知接下來的活動。

方向或其他組態變更可能會使程式碼難以判定活動是否進入背景，而標準的實作則可確保在使用者離開活動時 (按下 `HOME` 或 `BACK`)，會在結束當下報告內容，但方向變更時不會。

以下是實作的有趣部分：

			@Override
			protected void onUserLeaveHint()
			{
			  finish();
			}
			
			@Override
			protected void onPause()
			{
			  if (isFinishing() && mContent != null)
			  {
			    /*
			     * Exit content on exit, this is has no effect if another process method has already been
			     * called so we don't have to check anything here.
			     */
			    mContent.exitContent(this);
			  }
			  super.onPause();
			}

如您所見，如果您呼叫 `actionContent(this)` 然後完成活動，則可以安全地呼叫 `exitContent(this)` 而不會有任何影響。

##測試

現在請閱讀＜如何在 Android 上測試 Engagement 整合＞，確認您的整合。

[這裡]: http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]: http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]: https://developer.amazon.com/sdk/adm.html
 

<!---HONumber=July15_HO2-->