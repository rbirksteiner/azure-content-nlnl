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


#如何整合 ADM 與 Engagement

> [AZURE.IMPORTANT]您必須遵循＜如何在 Android 上整合 Engagement＞文件中所述的整合程序，才能接著依照本指南操作。
>
> 只有當您已整合 Reach 模組以用於任何時間促銷活動支援，才適用本文件。若要在應用程式中整合 Reach 促銷活動，請先閱讀「如何在 Android 上整合 Engagement Reach」。

##簡介

整合 ADM 可在即使應用程式未執行時，也能推播應用程式。

ADM 實際上不會傳送任何促銷活動資料，它只是告知應用程式去擷取 Engagement 推播的背景信號。如果在收到 ADM 推播時應用程式未執行，則會觸發連線至 Engagement 伺服器以便擷取推播，Engagement 連線會保留大約一分鐘時間，以備使用者因應推播而啟動應用程式。

> [AZURE.IMPORTANT]Amazon Device Messaging 只支援執行 Android 4.0.3 或更新版本的 Amazon Kindle 裝置；不過，您仍可將這段程式碼安全地整合至其他裝置。如果應用程式無法由 ADM 喚醒，則會在下次啟動應用程式時收到 Engagement 通知。

##註冊 ADM

如果尚未這麼做，您必須在 Amazon 帳戶上啟用 ADM。

有關此程序的詳細步驟，請參閱：[<https://developer.amazon.com/sdk/adm/credentials.html>]。

完成此程序後，您會得到：

-   OAuth 認證 (用戶端識別碼和用戶端密碼)，讓 Engagement 能夠推播您的裝置。
-   API 金鑰，必須整合到您的應用程式。

##SDK 整合

### 管理裝置註冊

每個裝置都必須傳送註冊命令給 ADM 伺服器，否則無法連線該裝置。

如果您已經使用 [ADM 用戶端程式庫]，而且已經擁有[整合的 ADM]，則可直接跳至 android-sdk-adm-receive。

如果您還沒有整合的 ADM，Engagement 提供一個在應用程式中啟用的更簡便方式：

編輯 `AndroidManifest.xml` 檔案：

-   新增 Amazon 命名空間，檔案應該類似這樣：

		<?xml version="1.0" encoding="utf-8"?>
		<manifest xmlns:android="http://schemas.android.com/apk/res/android"
		          xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   在 `<application/>` 標記中加入此區段：

		<amazon:enable-feature
		   android:name="com.amazon.device.messaging"
		   android:required="false"/>
		
		<meta-data android:name="engagement:adm:register" android:value="true" />

-   加入 amazon 標記後，如果您的專案建置目標低於 Android 2.1，可能會出現建置錯誤。您必須使用 **Android 2.1+** 建置目標 (別擔心，您仍可將 `minSdkVersion` 設為 4)。
-   依照[此程序]，將 ADM API 金鑰整合為資產。

然後依照下一節的指示進行。

### 將註冊識別碼傳遞給 Engagement 推播服務，並接收通知

若要將裝置的註冊識別碼傳遞給 Engagement 推播服務並接收其通知，請將下列內容加入 `AndroidManifest.xml` 檔案的 `<application/>` 標記中 (即使您未搭配 Engagement 使用 ADM，也請這麼做)：

		<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
		  android:exported="false">
		  <intent-filter>
		    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
		  </intent-filter>
		</receiver>
		
		 <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
		   android:permission="com.amazon.device.messaging.permission.SEND">
		  <intent-filter>
		    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
		    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
		    <category android:name="<your_package_name>"/>
		  </intent-filter>
		</receiver>   

確定您在 `AndroidManifest.xml` 中具有下列權限 (在 `</application>` 標記之前)。

		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
		<uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
		<permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##授與 Engagement OAuth 認證

在 $/#application/YOUR_APPID/native-push 提交您的 OAuth 認證 (用戶端識別碼和用戶端密碼)。

現在，您在建立 Reach 公告與輪詢時可以選取 [任何時間]。


[<https://developer.amazon.com/sdk/adm/credentials.html>]: https://developer.amazon.com/sdk/adm/credentials.html
[ADM 用戶端程式庫]: https://developer.amazon.com/sdk/adm/setup.html
[整合的 ADM]: https://developer.amazon.com/sdk/adm/integrating-app.html
[此程序]: https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
 

<!---HONumber=July15_HO2-->