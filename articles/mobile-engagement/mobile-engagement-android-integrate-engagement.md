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

#如何在 Android 上整合 Engagement

> [AZURE.SELECTOR] 
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

此程序描述在您的 Android 應用程式中啟動 Engagement 分析和監視功能的最簡單方法。

> [AZURE.IMPORTANT]最低 Android SDK API 層級必須是 10 或更高版本 (Android 2.3.3 或更高版本)。
 
下列步驟足以啟動計算使用者、工作階段、活動、當機和技術相關之所有統計資料所需的記錄檔報表。用來計算事件、錯誤及工作等其他統計資料所需的記錄檔報告必須使用 Engagement API 手動完成 (請參閱[如何在 Android 中使用進階的 Mobile Engagement 標記 API](mobile-engagement-android-use-engagement-api.md))，因為這些是與應用程式相依的統計資料。

##將 Engagement SDK 和服務嵌入至您的 Android 專案

從[這裡](http://go.microsoft.com/?linkid=9863935&clcid=0x409)下載 Android SDK，取得 `mobile-engagement-VERSION.jar` 並將它們放入 Android 專案的 `libs` 資料夾 (如果 libs 資料夾尚不存在，請建立此資料夾)。

> [AZURE.IMPORTANT]如果您使用 ProGuard 建立應用程式封裝，您需要保留一些類別。您可以使用下列組態程式碼片段：
>
> 
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			<methods>;
		 	}

在啟動器活動中呼叫下列方法，指定 Engagement 連接字串：

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

您應用程式的連接字串會顯示在 Azure 入口網站。

-   若有遺漏，請加入下列 Android 權限 (在 `<application>` 標記之前)：

			<uses-permission android:name="android.permission.INTERNET"/>
			<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   對於幾種裝置型號，我們無法從 ANDROID\\_ID 產生 Engagement 裝置識別碼 (可能會出現錯誤或無法使用)。在此情況下，SDK 會產生隨機裝置識別碼，並嘗試將它儲存在裝置的外部儲存體上，讓其他 Engagement 應用程式可以共用相同的裝置識別碼 (它也會儲存為共用喜好設定，以確保萬一外部儲存體故障，應用程式本身仍可使用相同的裝置識別碼)。為了讓此機制正常運作，如有遺漏，您必須新增下列權限 (在 `<application>` 標記之前)：

			<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

-   加入下列區段 (在 `<application>` 和 `</application>` 標記之間)：

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

-   將 `<Your application name>` 改為您應用程式的名稱。

> [AZURE.TIP]`android:label` 屬性可讓您選擇 Engagement 服務的名稱，此名稱會出現在使用者電話的「執行中服務」畫面中。建議將此屬性設定為 `"<Your application name>Service"` (例如 `"AcmeFunGameService"`)。

指定 `android:process` 屬性可確保 Engagement 服務在本身的處理程序中執行 (在與應用程式相同的處理程序中執行 Engagement，可能會造成主要/UI 執行緒回應速度較慢)。

> [AZURE.NOTE]您放置在 `Application.onCreate()` 和其他應用程式回呼中的程式碼會針對您所有應用程式的處理程序而執行，其中包括 Engagement 服務。可能會產生不必要的副作用 (例如 Engagement 處理程序中不必要的記憶體配置和執行緒、重複的廣播接收器或服務)。

如果覆寫 `Application.onCreate()`，建議在 `Application.onCreate()` 函數的開頭加入下列程式碼片段：

			 public void onCreate()
			 {
			   if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
			     return;
			
			   ... Your code...
			 }

您可以對 `Application.onTerminate()`、`Application.onLowMemory()` 和 `Application.onConfigurationChanged(...)` 執行相同的動作。

您也可以不延伸 `Application`，改為延伸 `EngagementApplication`：回呼 `Application.onCreate()` 會進行處理程序檢查，並在目前的處理程序不是裝載 Engagement 服務的處理程序時才會呼叫 `Application.onApplicationProcessCreate()`，相同規則也適用於其他回呼。

##基本報告

### 建議使用的方法：多載您的 `Activity` 類別

若要啟動 Engagement 需要的所有記錄檔報告以計算使用者、工作階段、活動、當機和技術統計資料，您只需要讓所有 `*Activity` 子類別繼承自對應的 `Engagement*Activity` 類別 (例如，如果您的舊版活動延伸 `ListActivity`，則使其延伸 `EngagementListActivity`)。

**沒有 Engagement：**

			package com.company.myapp;
			
			import android.app.Activity;
			import android.os.Bundle;
			
			public class MyApp extends Activity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

**有 Engagement：**

			package com.company.myapp;
			
			import com.microsoft.azure.engagement.activity.EngagementActivity;
			import android.os.Bundle;
			
			public class MyApp extends EngagementActivity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

> [AZURE.IMPORTANT]使用 `EngagementListActivity` 或 `EngagementExpandableListActivity` 時，務必先呼叫 `requestWindowFeature(...);` 再呼叫 `super.onCreate(...);`，否則會發生當機。

我們提供 `FragmentActivity` 和 `MapActivity` 的子類別，但為了避免使用 ProGuard 的應用程式發生問題，所以未將這些項目包含在 `engagement.jar` 中。

您可以在 `src` 資料夾中找到這些類別，並將其複製到您的專案。這些類別也會顯示在 JavaDoc 中。

### 替代方法：手動呼叫 `startActivity()` 和 `endActivity()`

如果您無法或不想多載 `Activity` 類別，可以改用直接呼叫 `EngagementAgent` 的方式來開始及結束您的活動。

> [AZURE.IMPORTANT]Android SDK 絕不會呼叫 `endActivity()` 方法，即使在關閉應用程式後也不會呼叫 (在 Android 上，應用程式其實永遠不會關閉)。因此，「強烈」建議在您「所有」活動的 `onResume` 回呼中呼叫 `startActivity()` 方法，以及在您「所有」活動的 `onPause()` 回呼中呼叫 `endActivity()` 方法。這是確保不會遺漏工作階段的唯一方法。如果遺漏了工作階段，Engagement 服務將永遠不會從 Engagement 後端中斷連線 (因為只要工作階段處於暫止狀態，服務就會保持連線)。

下列是一個範例：

			public class MyActivity extends Some3rdPartyActivity
			{
			  @Override
			  protected void onResume()
			  {
			    super.onResume();
			    String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
			    EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
			  }
			
			  @Override
			  protected void onPause()
			  {
			    super.onPause();
			    EngagementAgent.getInstance(this).endActivity();
			  }
			}

這個範例非常類似於 `EngagementActivity` 類別與其變體，在 `src` 資料夾中提供會其原始程式碼。

##測試

現在請確認您的整合，方法是在模擬器或裝置中執行您的行動應用程式，並確認它會在 [監視] 索引標籤上註冊工作階段。

下一節是選擇性的。

##位置報告

如果想要報告位置，您需要加入幾行組態 (在 `<application>` 和 `</application>` 標記之間)。

### 延遲區域位置報告

延遲區域位置報告允許報告國家、地區以及與裝置相關聯的位置。這類位置報告只會使用網路位置 (根據基地台識別碼或 WIFI)。每個工作階段最多報告一次裝置區域。絕不會使用 GPS，因此這類位置報告對於電池的影響很小 (但不是沒有)。

報告的區域用來計算關於使用者、工作階段、事件與錯誤的地理統計資料。它們也可用來做為觸達活動的準則。針對裝置報告的最後已知區域可以藉由[裝置 API] 來擷取。

若要啟用消極式區域位置報告，請加入：

			<meta-data android:name="engagement:locationReport:lazyArea" android:value="true"/>

您還需要新增下列權限 (如果未有此權限)：

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

### 即時位置報告

即時位置報告允許報告與裝置相關聯的緯度和經度。根據預設，這類位置報告只會使用網路位置 (根據基地台識別碼或 WIFI)，且只會在應用程式於前景中執行 (也就是在工作階段) 時，報告才會為作用中。

即時位置「不會」用來計算統計資料。其唯一用途，是允許在 Reach 活動中使用即時地理圍欄 <Reach-Audience-geofencing> 準則。

若要啟用即時位置報告，請加入：

			<meta-data android:name="engagement:locationReport:realTime" android:value="true" />

您還需要新增下列權限 (如果未有此權限)：

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

#### GPS 式報告

根據預設，即時位置報告只會使用網路位置。若要啟用 GPS 位置 (精準度大為提高)，請加入：

			<meta-data android:name="engagement:locationReport:realTime:fine" android:value="true" />

您還需要新增下列權限 (如果未有此權限)：

			<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### 背景報告

根據預設，即時位置報告只在應用程式在前景中執行 (也就是在工作階段) 時才會為作用中。若要也在背景中啟用報告，請加入：

			<meta-data android:name="engagement:locationReport:realTime:background" android:value="true" />

> [AZURE.NOTE]當應用程式在背景中執行，即使啟用 GPS，也只會報告網路位置。

如果使用者重新啟動裝置，就會停止背景位置報表，您可以加入以下內容，讓它在開機時自動重新啟動：

			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>

您還需要新增下列權限 (如果未有此權限)：

			<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

##進階報告

此外，如果您想要報告應用程式的特定事件、錯誤及工作，必須透過 `EngagementAgent` 類別的方法使用 Engagement API。此類別的物件可以透過呼叫 `EngagementAgent.getInstance()` 靜態方法來擷取。

Engagement API 可允許使用所有 Engagement 的進階功能，詳情請見＜如何在 Android 上使用 Engagement API＞(以及 `EngagementAgent` 類別的技術文件)。

##進階組態 (在 AndroidManifest.xml 中)

如果您希望在使用 Wifi 或關閉螢幕時仍確保即時傳送統計資料，請新增下列選用權限：

			<uses-permission android:name="android.permission.WAKE_LOCK"/>

如果您想要停用當機報告，請加入下列內容 (在 `<application>` 和 `</application>` 標記之間)：

			<meta-data android:name="engagement:reportCrash" android:value="false"/>

根據預設，Engagement 會即時報告記錄檔。如果應用程式報告記錄檔的頻率很高，建議您緩衝記錄檔，以固定時段一次報告全部的記錄檔 (此稱為「高載模式」)。若要完成此作業，請加入下列內容 (在 `<application>` 和 `</application>` 標記之間)：

			<meta-data android:name="engagement:burstThreshold" android:value="<interval between too bursts (in milliseconds)>"/>

高載模式可以稍微延長電池使用時間但對 Engagement 監視器會有影響： 所有工作階段和工作持續時間將調整為高載閾值 (因此，可能將看不到時間比高載閾值短的工作階段和作業)。建議使用低於 30000 (30 秒) 的閾值。

根據預設，Engagement 服務在可使用網路時會立即與我們的伺服器建立連線。如果您想要延遲連線，請加入下列內容 (在 `<application>` 和 `</application>` 標記之間)：

			<meta-data android:name="engagement:connection:delay" android:value="<delay (in milliseconds)>"/>

根據預設，工作階段會在最後一個活動結束後 10 秒終止 (最後一個活動通常發生在按 [首頁] 或 [上一步] 鍵、將行動電話設定為閒置或跳到另一個應用程式)。這是為了避免當使用者退出但很快又返回應用程式 (例如使用者挑選影像、檢查通知等等) 時，工作階段產生分割的狀況。您可以修改這個參數。若要完成此作業，請加入下列內容 (在 `<application>` 和 `</application>` 標記之間)：

			<meta-data android:name="engagement:sessionTimeout" android:value="<session timeout (in milliseconds)>"/>

##停用記錄報告

### 使用方法呼叫

如果您想要 Engagement 停止傳送記錄檔，可以呼叫：

			EngagementAgent.getInstance(context).setEnabled(false);

這個呼叫是持續性的：它使用共用的喜好設定檔案。

如果當您呼叫此函式時 Engagement 已啟用，停止服務可能需要 1 分鐘時間。不過，當您下次啟動應用程式時，完全不會啟動服務。

您可以使用 `true` 呼叫相同函式，即可再次啟用記錄報告。

### 在您自己的 `PreferenceActivity` 中整合

如不呼叫此函數，您也可以在現有的 `PreferenceActivity` 中直接整合此設定。

您可以在 `AndroidManifest.xml` 檔案中使用 `application meta-data`，設定 Engagement 使用您的喜好設定檔案 (搭配所需的模式)：

-   `engagement:agent:settings:name` 機碼可用來定義共用喜好設定檔案的名稱。
-   `engagement:agent:settings:mode` 機碼可用來定義共用喜好設定檔案的模式，您應該使用與 `PreferenceActivity` 中相同的模式。模式必須以數字傳遞：如果您在程式碼中使用常數旗標的組合，請檢查總值。

Engagement 在喜好設定檔案內會一律使用 `engagement:key` 布林值機碼來管理這項設定。

下列 `AndroidManifest.xml` 範例顯示的是預設值：

			<application>
			    [...]
			    <meta-data
			      android:name="engagement:agent:settings:name"
			      android:value="engagement.agent" />
			    <meta-data
			      android:name="engagement:agent:settings:mode"
			      android:value="0" />

接著您可以將 `CheckBoxPreference` 加入您的喜好設定配置，如下所示：

			<CheckBoxPreference
			  android:key="engagement:enabled"
			  android:defaultValue="true"
			  android:title="Use Engagement"
			  android:summaryOn="Engagement is enabled."
			  android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[裝置 API]: http://go.microsoft.com/?linkid=9876094
 

<!---HONumber=July15_HO2-->