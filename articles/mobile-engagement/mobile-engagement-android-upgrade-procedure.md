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


#升級程序

如果您已經整合我們的舊版 SDK 到您的應用程式，在升級 SDK 時您必須考慮以下幾點。

如果您有錯過幾個版本的 SDK，您必須遵循幾個步驟。例如，如果您要從 1.4.0 移轉到 1.6.0，必須先遵循「從 1.4.0 到 1.5.0」的程序，然後再依照「從 1.5.0 到 1.6.0」的程序進行。

不論您升級開始的版本為何，都必須將 `mobile-engagement-VERSION.jar` 替換為新的。

##從 3.0.0 到 4.0.0

### 原生推播

原生推播 (GCM/ADM) 現在也用於應用程式通知，因此您必須為任何類型的推播行銷活動設定原生推播認證。

如果尚未完成，請遵循[此程序](mobile-engagement-android-integrate-engagement-reach.md#native-push)。

### AndroidManifest.xml

``AndroidManifest.xml`` 中的 Reach 整合已修改。

取代下列項目：

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

依據

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
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

現在當您按一下公告 (具有文字/網頁內容) 或輪詢，可能是載入畫面。您必須加入此項目，這些行銷活動才能在 4.0.0 中運作：

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### 資源

內嵌新的 `res/layout/engagement_loading.xml` 檔案到您的專案。

##從 2.4.0 到 3.0.0

以下說明如何將 SDK 整合從 Capptain SAS 提供的 Capptain 服務，移轉到由 Azure Mobile Engagement 提供的應用程式內。如果您是從較早版本移轉，請參閱 Capptain 網站，先移轉到 2.4.0 後再套用以下程序。

>[AZURE.IMPORTANT]Capptain 和 Mobile Engagement 是不同的服務，而以下程序只適用於移轉用戶端應用程式。移轉應用程式中的 SDK「不會」將您的資料從 Capptain 伺服器移轉到 Mobile Engagement 伺服器。

### JAR 檔案

將 `libs` 資料夾中的 `capptain.jar` 以 `mobile-engagement-VERSION.jar`取代。

### 資源檔

我們提供的每個資源檔 (前置詞為 `capptain_`) 都必須替換為新的資源檔 (前置詞為 `engagement_`)。

如果您已自訂這些檔案，則必須在新的檔案上重新套用自訂，資源檔中的所有識別碼也已重新命名。

### 應用程式識別碼

現在 Engagement 使用連接字串來設定 SDK 識別碼，例如應用程式識別碼。

您必須在啟動程式活動中使用 `EngagementAgent.init` 方法，如下所示：

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

您應用程式的連接字串會顯示在 Azure 入口網站。

請移除對 `CapptainAgent.configure` 的所有呼叫，因為 `EngagementAgent.init` 已取代該方法。

無法再使用 `AndroidManifest.xml` 設定 `appId`。

如果您的 `AndroidManifest.xml` 有下列區段，請將其移除：

			<meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### Java API

對 SDK 任何 Java 類別的各個呼叫都必須重新命名，例如 `CapptainAgent.getInstance(this)` 必須重新命名為 `EngagementAgent.getInstance(this)`、`extends CapptainActivity` 必須重新命名為 `extends EngagementActivity`，以此類推...

如果您已整合預設代理程式喜好設定檔，現在預設檔案名稱是 `engagement.agent`，而索引鍵為 `engagement:agent`。

建立 Web 公告時，Javascript 繫結器現在是 `engagementReachContent`。

### AndroidManifest.xml

這裡有許多變更，服務不再共用，且許多接收器也不再能匯出。

服務宣告現在更為簡單，移除意圖篩選及其內所有中繼資料，然後加入 `exportable=false`。

再加上所有項目重新命名以使用 Engagement。

現在的樣貌如下：

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

當您想要啟用測試記錄檔時，中繼資料現在已經移至應用程式標記，並且已重新命名：

			<application>
			
			  <meta-data android:name="engagement:log:test" android:value="true" />
			
			  <service/>
			
			</application>

所有其他中繼資料都已重新命名，完整清單如下 (當然，請只重新命名您所使用的項目)：

			<meta-data
			  android:name="engagement:reportCrash"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:sessionTimeout"
			  android:value="10000"/>
			<meta-data
			  android:name="engagement:burstThreshold"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:connection:delay"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:locationReport:lazyArea"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:background"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:fine"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:agent:settings:name"
			  android:value="engagement.agent"/>
			<meta-data
			  android:name="engagement:agent:settings:mode"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:gcm:sender"
			  android:value="<YOUR_PROJECT_NUMBER>\n"/>
			<meta-data
			  android:name="engagement:adm:register"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:reach:notification:icon"
			  android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
			
			<activity android:name="SomeActivityWithoutReachOverlay">
			  <meta-data
			    android:name="engagement:notification:overlay"
			    android:value="false"/>
			</activity>

已從 SDK 移除 Google Play 和 SmartAd 追蹤，您只需要移除它，不必取代：

			<meta-data 
				android:name="capptain:track:installReferrerForwardList"
				android:value="com.class1,com.class2"/>
			<meta-data
				android:name="capptain:track:adservers"
				android:value="smartad" />

Reach 活動現在宣告如下：

			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/plain"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/html"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			  </intent-filter>
			</activity>
			
如果您有自訂的 Reach 活動，只需要變更意圖動作，以符合 `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` 或 `com.microsoft.azure.engagement.reach.intent.action.POLL`。

廣播接收器已重新命名，此外我們現在也已加入 `exported=false`。以下是新規格之接收器的完整清單 (當然，請只重新命名您所使用的項目)：

			<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="android.intent.action.BOOT_COMPLETED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
			    <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
			  android:permission="com.google.android.c2dm.permission.SEND">
			  <intent-filter>
			    <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
			    <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
			  android:permission="com.amazon.device.messaging.permission.SEND">
			  <intent-filter>
			    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
			    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
			  </intent-filter>
			</receiver>

已移除追蹤接收器，所以您必須移除此區段：

		  <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
		    <intent-filter>
		      <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
		      <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
		    </intent-filter>
		  </receiver>

請注意，您的廣播接收器 **EngagementMessageReceiver** 實作的宣告已在 `AndroidManifest.xml` 中變更。這是因為已經移除從任意 XMPP 實體傳送和接收任意 XMPP 訊息的 API，以及在裝置之間傳送和接收訊息的 API。因此，您也必須從您的 **EngagementMessageReceiver** 實作刪除下列回呼：

			protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

and

			protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

然後刪除 **EngagementAgent** 對下列項目的任何呼叫：

			sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

and

			sendXMPPMessage(android.os.Bundle msg)

### Proguard

Proguard 組態受到品牌重新命名的影響，規則現在類似：

			-dontwarn android.**
			-keep class android.support.v4.** { *; }
			
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			  <methods>;
			}
 

<!---HONumber=July15_HO2-->