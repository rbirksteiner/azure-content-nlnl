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

#如何整合 GCM 與 Mobile Engagement

> [AZURE.IMPORTANT]您必須遵循＜如何在 Android 上整合＞文件中所述的整合程序，才能接著遵循本指南。
>
> 只有當您已整合 Reach 模組以用於任何時間促銷活動支援，才適用本文件。若要在應用程式中整合 Reach 促銷活動，請先閱讀「如何在 Android 上整合 Engagement Reach」。

##簡介

整合 GCM 可在即使應用程式未執行時，也能推播應用程式。

GCM 實際上不會傳送任何促銷活動資料，它只是告知應用程式去擷取 Engagement 推播的背景信號。如果在收到 GCM 推播時應用程式未執行，則會觸發連線至 Engagement 伺服器以便擷取推播，Engagement 連線會保留大約一分鐘時間，以備使用者因應推播而啟動應用程式。

以下資訊供您參考：Engagement 只使用 [Send-to-Sync] 訊息搭配 `engagement.tickle` 摺疊機碼。

> [AZURE.IMPORTANT]執行 Android 2.2 或更高版本的裝置已安裝 Google Play 並已啟用 Google 背景連線，才能由 GCM 喚醒；不過，您仍可將這段程式碼安全地整合至舊版 Android SDK 以及不支援 GCM 的裝置 (只使用對應方式)。如果應用程式無法由 GCM 喚醒，則會在下次啟動應用程式時收到 Engagement 通知。


> [AZURE.WARNING]如果在設定 Engagement SDK 使用 GCM 時，是由您自己的用戶端程式碼管理 C2DM 註冊識別碼，則會發生註冊識別碼衝突，因此請只在您自己的程式碼不使用 C2DM 時才在 Engagement 中使用 GCM。

##註冊 GCM 並啟用 GCM 服務

如果尚未這麼做，請在您的 Google 帳戶上啟用 GCM 服務。

在撰寫本文的時候 (2014 年 2 月 5 日)，您可以依照下列程序進行：[<http://developer.android.com/guide/google/gcm/gs.html>]。

請只進行該程序中在您的帳戶上啟用 GCM 的部分。當您讀到＜取得 API 金鑰＞章節時，請停止閱讀並返回此頁面，不要再進一步遵循任何 Google 程序。

此程序說明「專案編號」可做為「GCM 傳送者識別碼」，此程序稍後需要用到此識別碼。

> [AZURE.IMPORTANT]「專案編號」不應與「專案識別碼」混淆。專案識別碼現在與專案編號不同 (在新專案上是一個名稱)。您需要在 Engagement SDK 中整合的是「專案編號」，此編號會顯示於 [Google 開發人員主控台]的 [概觀] 功能表中。

##SDK 整合

### 管理裝置註冊

每個裝置都必須傳送註冊命令給 Google 伺服器，否則無法連線該裝置。

也可以從 GCM 通知取消註冊裝置 (解除安裝應用程式時會自動取消裝置的註冊)。

如果您使用 [GCM 用戶端程式庫]，您可以直接讀取 android-sdk-gcm-receive。

如果您尚未自行傳送註冊對應方式，您可以讓 Engagement 自動註冊您的裝置。

若要啟用此功能，請將下列內容加入 `AndroidManifest.xml` 檔案的 `<application/>` 標記中：

			<!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
			<meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### 將註冊識別碼傳遞給 Engagement 推播服務，並接收通知

若要將裝置的註冊識別碼傳遞給 Engagement 推播服務並接收其通知，請將下列內容加入 `AndroidManifest.xml` 檔案的 `<application/>` 標記中 (即使您自行管理裝置註冊，也請這麼做)：

			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			  <intent-filter>
			    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
			    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
			    <category android:name="<your_package_name>" />
			  </intent-filter>
			</receiver>

確定您在 `AndroidManifest.xml` 中具有下列權限 (在 `</application>` 標記之後)。

			<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
			<uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
			<permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##授與伺服器 API 金鑰的存取權給 Engagement

如果尚未這麼做，請在 [Google 開發人員主控台]上建立「伺服器 API 金鑰」。

伺服器金鑰不能有 IP 限制。

在撰寫本文的時候 (2014 年 2 月 5 日)，此程序如下：

-   若要完成此作業，請開啟 [Google 開發人員主控台]。
-   選取先前程序中的同一個專案 (具有在 `AndroidManifest.xml` 中整合之「專案編號」的專案)。
-   移至 [API 與驗證 -> 認證]，在 [公用 API 存取] 區段中按一下 [建立新金鑰]。
-   選取 [伺服器金鑰]。
-   在下一個畫面上保留空白 (沒有 IP 限制)，然後按一下 [建立]。
-   複製所產生的 API 金鑰。
-   移至 $/#application/YOUR_ENGAGEMENT_APPID/native-push。
-   在 GCM 區段中，以您剛剛產生並複製的 API 金鑰進行編輯。

現在，您在建立 Reach 公告與輪詢時可以選取 [任何時間]。

> [AZURE.IMPORTANT]Engagement 實際上需要「伺服器金鑰」，Engagement 伺服器無法使用 Android 金鑰。

##測試

現在請閱讀＜如何在 Android 上測試 Engagement 整合＞，確認您的整合。


[Send-to-Sync]: http://developer.android.com/google/gcm/adv.html#collapsible
[<http://developer.android.com/guide/google/gcm/gs.html>]: http://developer.android.com/guide/google/gcm/gs.html
[Google Developers Console]: https://cloud.google.com/console
[GCM 用戶端程式庫]: http://developer.android.com/guide/google/gcm/gs.html#libs
[Google 開發人員主控台]: https://cloud.google.com/console

 

<!---HONumber=July15_HO2-->