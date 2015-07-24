<properties 
	pageTitle="如何在 Android 上使用 Engagement API" 
	description="最新 Android SDK - 如何在 Android 上使用 Engagement API"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />

#如何在 Android 上使用 Engagement API

本文件是[如何在 Android 上整合 Engagement](mobile-engagement-android-integrate-engagement.md)的附加說明：有關如何使用 Engagement API 來報告應用程式的統計資料，本文件提供了深入的詳細說明。

請記住，如果您只想要 Engagement 向您報告應用程式的工作階段、活動、當機和技術資訊，那麼最簡單的方法是讓所有 `Activity` 子類別繼承自對應的 `EngagementActivity` 類別。

如果您想要執行更多工作 (例如，若您需要報告應用程式的特定事件、錯誤和作業，或者您需要以不同於 `EngagementActivity` 類別中的方式來報告應用程式的活動)，則您需要使用 Engagement API。

Engagement API 是由 `EngagementAgent` 類別提供。此類別的執行個體，可以藉由呼叫 `EngagementAgent.getInstance(Context)` 靜態方法 (請注意，傳回的 `EngagementAgent` 物件為單一值) 來擷取。

##Engagement 概念

以下部分簡要說明適用於 Android 平台的一般 [Mobile Engagement 概念](mobile-engagement-concepts.md)。

### `Session`和`Activity`

如果使用者在兩個活動之間維持閒置超過幾秒鐘，其活動序列會分割成兩個相異的工作階段。這幾秒稱為「工作階段逾時」。

活動通常會與應用程式的某個畫面相關聯，也就是說，活動會在畫面顯示時啟動，並在畫面關閉時停止：這是使用 `EngagementActivity` 類別整合 Engagement SDK 時的情況。

但您也可以透過 Engagement API 手動控制活動。這樣可以將指定的畫面分隔為數個子部分，以取得關於此畫面使用方式的詳細資料 (例如，可了解此畫面內對話方塊的使用頻率與使用時間長度)。

##報告活動

> [AZURE.IMPORTANT]如果您依照＜如何在 Android 上整合 Engagement＞文件所述使用 `EngagementActivity` 類別與變體，您就不需要報告本節所述的各項活動。

### 使用者啟動新的活動

			EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
			// Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

每當使用者活動變更，您就需要呼叫 `startActivity()`。第一次呼叫此函式會啟動新的使用者工作階段。

呼叫此函式的最佳位置是在每個活動 `onResume` 回呼。

### 使用者結束其目前的活動

			EngagementAgent.getInstance(this).endActivity();

使用者完成最後一個活動時，您至少需要呼叫 `endActivity()` 一次。這會通知 Engagement SDK，說明使用者目前處於閒置狀態，且工作階段逾時到期時就得關閉使用者工作階段 (如果您在工作階段逾時到期前就呼叫 `startActivity()`，工作階段只會繼續)。

呼叫此函式的最佳位置是在每個活動 `onPause` 回呼。

##報告事件

### 工作階段事件

工作階段事件通常用來報告在其工作階段期間由使用者所執行的動作。

**不含額外資料的範例：**

			public MyActivity extends EngagementActivity {
			   [...]
			   @Override
			   public boolean onPrepareOptionsMenu(Menu menu) {
			      getEngagementAgent().sendSessionEvent("menu_shown", null);
			   }
			   [...]
			}

**含額外資料的範例：**

			public MyActivity extends EngagementActivity {
			  [...]
			  @Override
			  public boolean onMenuItemSelected(int featureId, MenuItem item) {
			    Bundle extras = new Bundle();
			    extras.putInt("id", item.getItemId());
			    getEngagementAgent().sendSessionEvent("menu_selected", extras);
			  }
			  [...]
			}

### 獨立事件

與工作階段事件相反，獨立的事件可能發生在工作階段的內容之外。

**範例：**

假設您想要在觸發廣播接收器時，報告發生事件：

			/** Triggered by Intent.ACTION_BATTERY_LOW */
			public BatteryLowReceiver extends BroadcastReceiver {
			  [...]
			  @Override
			  public void onReceive(Context context, Intent intent) {
			    EngagementAgent.getInstance(context).sendEvent("battery_low", null);
			  }
			  [...]
			}

##報告錯誤

### 工作階段錯誤

工作階段錯誤通常用來報告在其工作階段期間影響使用者的錯誤。

**範例：**

			/** The user has entered invalid data in a form */
			public MyActivity extends EngagementActivity {
			  [...]
			  public void onMyFormSubmitted(MyForm form) {
			    [...]
			    /* The user has entered an invalid email address */
			    getEngagementAgent().sendSessionError("sign_up_email", null);
			    [...]
			  }
			  [...]
			}

### 獨立錯誤

不同於工作階段錯誤，獨立錯誤可以出現在工作階段的內容之外。

**範例：**

下列範例示範如何在應用程式處理程序執行時，每當手機記憶體不足時便報告錯誤。

			public MyApplication extends EngagementApplication {
			
			  @Override
			  protected void onApplicationProcessLowMemory() {
			    EngagementAgent.getInstance(this).sendError("low_memory", null);
			  }
			}

##報告工作

### 範例

假設您想要報告登入程序持續時間：
			
			[...]
			public void signIn(Context context, ...) {
			
			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has started */
			  engagementAgent.startJob("sign_in", null);
			
			  [... sign in ...]
			
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### 報告工作期間的錯誤

錯誤可能與正在執行的工作關聯，而不是與目前的使用者工作階段關聯。

**範例：**

假設您想要報告登入程序期間的錯誤：

[...] public void signIn(Context context, ...) {

			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has been started */
			  engagementAgent.startJob("sign_in", null);
			
			  /* Try to sign in */
			  while(true)
			    try {
			      trySignin();
			      break;
			    }
			    catch(Exception e) {
			      /* Report the error to Engagement */
			      engagementAgent.sendJobError("sign_in_error", "sign_in", null);
			
			      /* Retry after a moment */
			      sleep(2000);
			    }
			  [...]
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### 在工作期間報告事件

事件可以與執行中的工作相關，而不是與目前的使用者工作階段相關。

**範例：**

假設我們有社交網路，且使用工作來報告使用者連線到伺服器這段期間的總時間。使用者可以在使用另一個應用程式或行動電話在休眠狀態時，保持在背景中連線，因此沒有工作階段。

使用者可以接收來自朋友的訊息，這就是工作事件。
			
			[...]
			public void signin(Context context, ...) {
			  [...Sign in code...]
			  EngagementAgent.getInstance(context).startJob("connection", null);
			}
			[...]
			public void signout(Context context) {
			  [...Sign out code...]
			  EngagementAgent.getInstance(context).endJob("connection");
			}
			[...]
			public void onMessageReceived(Context context) {
			  [...Notify in status bar...]
			  EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
			}
			[...]

##額外的參數

可以將任意資料附加到事件、錯誤、活動及工作。

此資料可以結構化，它會使用 Android 的組合類別 (事實上，它的運作方式如同在 Android Intents 的額外參數)。請注意，組合可以包含陣列或另一個組合執行個體。

> [AZURE.IMPORTANT]如果您放入 parcelable 或 serializable 參數，請確定已實作其 `toString()` 方法，以傳回使用者可閱讀的字串。包含無法序列化之非暫時性欄位的 serializable 類別，會使 Android 在您呼叫 `bundle.putSerializable("key",value);` 時當機

> [AZURE.WARNING]不支援額外參數中的疏鬆陣列，也就是它不會序列化為陣列。您應該將它們轉換成標準的陣列，然後才用於額外的參數。

### 範例

			Bundle extras = new Bundle();
			extras.putString("video_id", 123);
			extras.putString("ref_click", "http://foobar.com/blog");
			EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### 限制

#### 之間的信任

`Bundle` 中的每個索引鍵都必須符合下列規則運算式：

`^[a-zA-Z][a-zA-Z_0-9]*`

這表示索引鍵必須至少以一個字母開頭，後面連接字母、數字或底線 (_)。

#### 大小

額外項目限制為一次呼叫 **1024** 個字元 (由 Engagement 服務以 JSON 編碼之後)。

在上述範例中，傳送到伺服器的 JSON 會是 58 個字元：

			{"ref_click":"http://foobar.com/blog","video_id":"123"}

##報告應用程式資訊

您可以使用 `sendAppInfo()` 函式手動報告追蹤資訊 (或是任何其他應用程式特定資訊)。

請注意，這些資訊可以累加地傳送：只有指定的索引鍵的最新值會保留給指定的裝置。

就像事件的額外項目，Bundle 類別用來摘要應用程式資訊，請注意陣列或子組合會被視為一般字串 (使用 JSON 序列化)。

### 範例

以下是傳送使用者性別和出生日期的程式碼範例：

			Bundle appInfo = new Bundle();
			appInfo.putString("status", "premium");
			appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
			EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### 限制

#### 之間的信任

`Bundle` 中的每個索引鍵都必須符合下列規則運算式：

`^[a-zA-Z][a-zA-Z_0-9]*`

這表示索引鍵必須至少以一個字母開頭，後面連接字母、數字或底線 (_)。

#### 大小

應用程式資訊限制為一次呼叫 **1024** 個字元 (由 Engagement 服務以 JSON 編碼之後)。

在上述範例中，傳送到伺服器的 JSON 會是 44 個字元：

			{"expiration":"2016-12-07","status":"premium"}
 

<!---HONumber=July15_HO1-->