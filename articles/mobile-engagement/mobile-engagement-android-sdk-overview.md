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
	ms.date="05/04/2015" 
	ms.author="kapiteir" />


#適用於 Azure Mobile Engagement 的 Android SDK

從這裡開始，取得有關如何將 Azure Mobile Engagement 整合在應用程式中的所有詳細資料。如果您想要先試用一下，請務必先完成我們的 [15 分鐘教學課程](mobile-engagement-android-get-started.md)。

按一下即可查看 [SDK 內容](mobile-engagement-android-sdk-content.md)。

##整合程序
1. 從這裡開始：[如何在 Android 應用程式中整合 Mobile Engagement](mobile-engagement-android-integrate-engagement.md)

2. 通知：[如何在 Android 應用程式中整合 Reach (通知)](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM)：[如何整合 GCM 與 Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM)：[如何整合 ADM 與 Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. 標記計劃實作：[如何在 Android 應用程式中使用進階的 Mobile Engagement 標記 API](mobile-engagement-android-use-engagement-api.md)


##版本資訊

###4.0.0 (07/06/2015)

-   內部通訊協定會變更以讓分析和推播更可靠。
-   原生推播 (GCM/ADM) 現在也用於應用程式通知，因此您必須為任何類型的推播行銷活動設定原生推播認證。
-   修正大圖片通知：它們只會在推播之後顯示 10 秒。
-   修正包含預設動作 URL 之網頁公告內的按一下連結。
-   修正與本機儲存體管理相關的罕見損毀。
-   修正動態組態字串管理。
-   更新 EULA。

如需較早版本，請參閱[完整版本資訊](mobile-engagement-android-release-notes.md)。

##升級程序

如果您已經整合我們的舊版 SDK 到您的應用程式，在升級 SDK 時您必須考慮以下幾點。

如果您錯過數個版本的 SDK，可能必須遵循幾個程序步驟，請參閱完整的[升級程序](mobile-engagement-android-upgrade-procedure.md)。例如，如果您要從 1.4.0 移轉到 1.6.0，必須先遵循「從 1.4.0 到 1.5.0」的程序，然後再依照「從 1.5.0 到 1.6.0」的程序進行。

不論您升級開始的版本為何，都必須將 `mobile-engagement-VERSION.jar` 替換為新的。

###從 3.0.0 到 4.0.0

#### 原生推播

原生推播 (GCM/ADM) 現在也用於應用程式通知，因此您必須為任何類型的推播行銷活動設定原生推播認證。

如果尚未完成，請遵循[此程序](mobile-engagement-android-integrate-engagement-reach.md#native-push)。

#### AndroidManifest.xml

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

#### 資源

內嵌新的 `res/layout/engagement_loading.xml` 檔案到您的專案。

<!---HONumber=July15_HO2-->