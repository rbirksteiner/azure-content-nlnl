<properties 
	pageTitle="如何搭配使用通知中樞與 Java" 
	description="了解如何從 Java 後端使用 Azure 通知中樞。" 
	services="notification-hubs" 
	documentationCenter="" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="java" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="yuaxu"/>

# 如何從 Java 使用通知中樞
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-java-backend-how-to/" title="Java" class="current">Java</a><a href="/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a><a href="/documentation/articles/notification-hubs-python-backend-how-to/" title="Python">Python</a><a href="/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/" title="Node.js">Node.js</a>
</div>

本主題說明最新完整支援的官方 Azure 通知中樞 Java SDK 有哪些主要功能。這是開放原始碼專案，您可以在 [Java SDK] 檢視完整的 SDK 程式碼。

一般而言，您可以使用通知中心 REST 介面，存取 Java/PHP/Python/Ruby 後端的所有通知中心功能，如 MSDN 主題[通知中樞 REST API](http://msdn.microsoft.com/library/dn223264.aspx) 中所述。此 Java SDK 透過 Java 中的這些 REST 介面提供了精簡型包裝函式。

SDK 目前支援：

- 通知中樞的 CRUD 
- 註冊的 CRUD
- 安裝管理
- 匯入/匯出註冊
- 定期傳送
- 排程的傳送
- 透過 Java NIO 的非同步作業
- 受支援的平台：APNS (iOS)、GCM (Android)、WNS (Windows 市集應用程式)、MPNS (Windows Phone)、ADM (Amazon Kindle Fire)、Baidu (沒有 Google 服務的 Android) 

## SDK 的使用方式

### 編譯和建置

使用 [Maven]

若要建置：

	mvn package

## 代碼

### 通知中樞 CRUD

**建立 NamespaceManager：**
	
	NamespaceManager namespaceManager = new NamespaceManager("connection string")

**建立通知中樞：**
	
	NotificationHubDescription hub = new NotificationHubDescription("hubname");
	hub.setWindowsCredential(new WindowsCredential("sid","key"));
	hub = namespaceManager.createNotificationHub(hub);
	
 或

	hub = new NotificationHub("connection string", "hubname");

**取得通知中樞：**
	
	hub = namespaceManager.getNotificationHub("hubname");

**更新通知中樞：**
	
	hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
	hub = namespaceManager.updateNotificationHub(hub);

**刪除通知中樞：**
	
	namespaceManager.deleteNotificationHub("hubname");

### 註冊 CRUD
**建立通知中樞用戶端：**

	hub = new NotificationHub("connection string", "hubname");

**建立 Windows 註冊：**

	WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");    
	hub.createRegistration(reg);

**建立 iOS 註冊：**

	AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");
	hub.createRegistration(reg);

同樣地，您可以建立 Android (GCM)、Windows Phone (MPNS) 和 Kindle Fire (ADM) 的註冊。

**建立範本註冊：**

	WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
	reg.getHeaders().put("X-WNS-Type", "wns/toast");
	hub.createRegistration(reg);

**使用建立 registrationid + upsert 模式來建立註冊**

如果將註冊識別碼儲存在裝置上，請在發生任何遺失回應時移除複本：

	String id = hub.createRegistrationId();
	WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
	hub.upsertRegistration(reg);

**更新註冊：**
	
	hub.updateRegistration(reg);

**刪除註冊：**
	
	hub.deleteRegistration(regid);

**查詢註冊：**

* 	**取得單一註冊：**
	
		hub.getRegistration(regid);
	
* 	**取得中樞的所有註冊：**
	
		hub.getRegistrations();
	
* 	**取得具有標籤的註冊：**
	
		hub.getRegistrationsByTag("myTag");
	
* 	**依通道取得註冊：**
	
		hub.getRegistrationsByChannel("devicetoken");

所有集合查詢都支援 $top 和接續權杖。

### 安裝 API 的使用方式
安裝 API 是註冊管理的替代機制。要維護多個註冊並非易事，並且可能容易出錯或效率低落，但現在您已可以使用單一安裝物件。安裝包含所需的一切：推播通道 (裝置權杖)、標籤、範本、次要磚 (適用於 WNS 和 APNS)。現在您無須呼叫服務即可取得識別碼 - 只要產生 GUID 或任何其他識別碼、將它保存在裝置上，並透過推播通道傳送至您的後端 (裝置權杖) 即可。您只能在後端執行單一呼叫：CreateOrUpdateInstallation，它是完全等冪的，因此您可以儘管在必要時重試。

以 Amazon Kindle Fire 為例，將如下所示：

	Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
	hub.createOrUpdateInstallation(installation);

如果您想要加以更新：

	installation.addTag("foo");
	installation.addTemplate("template1", new InstallationTemplate("{"data":{"key1":"$(value1)"}}","tag-for-template1"));
	installation.addTemplate("template2", new InstallationTemplate("{"data":{"key2":"$(value2)"}}","tag-for-template2"));
	hub.createOrUpdateInstallation(installation);

在進階案例中，我們提供了部分更新功能，僅允許使用者對安裝物件的特定屬性進行修改。基本上，部分更新是您可以對安裝物件執行的 JSON Patch 作業子集。

	PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
	PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
	PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{"data":{"key3":"$(value3)"}}","tag-for-template1")).toJson());
	hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

刪除安裝：

	hub.deleteInstallation(installation.getInstallationId());

CreateOrUpdate、Patch 和 Delete 最終都會與 Get 一致。您要求的作業只會在呼叫期間進入系統佇列，並會在背景中執行。請注意，Get 不是針對主要執行階段案例而設計的，而是專門用於偵錯和疑難排解的目的，因此受到服務嚴格的節流。

安裝的傳送流量與註冊相同。我們僅介紹以特定安裝的通知為目標的選項 - 僅使用標籤 "InstallationId:{desired-id}"。就上述案例而言，將如下所示：

	Notification n = Notification.createWindowsNotification("WNS body");
	hub.sendNotification(n, "InstallationId:{installation-id}");

數個範本之一：

	Map<String, String> prop =  new HashMap<String, String>();
	prop.put("value3", "some value");
	Notification n = Notification.createTemplateNotification(prop);
	hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### 排程通知 (適用於 STANDARD 層)

與定期傳送相同，但使用了一個額外參數 scheduledTime，指出何時應傳遞通知。服務可接受目前 + 5 分鐘與目前 + 7 天之間的任何時間點。

**排程 Windows 原生通知：**

	Calendar c = Calendar.getInstance();
	c.add(Calendar.DATE, 1);    
	Notification n = Notification.createWindowsNotification("WNS body");
	hub.scheduleNotification(n, c.getTime());

### 匯入/匯出 (適用於 STANDARD 層)
有時候您需要對註冊執行大量作業。通常這是為了與另一個系統整合，或是要進行大規模修正，例如更新標籤。如果註冊數高達數千個，強烈建議您不要使用 Get/Update 流程。匯入/匯出功能可因應此案例。基本上，您會在儲存體帳戶提供對某個 Blob 容器的的存取權，做為內送資料的來源和輸出的位置。

**提交匯出工作：**

	NotificationHubJob job = new NotificationHubJob();
	job.setJobType(NotificationHubJobType.ExportRegistrations);
	job.setOutputContainerUri("container uri with SAS signature");
	job = hub.submitNotificationHubJob(job);


**提交匯入工作：**

	NotificationHubJob job = new NotificationHubJob();
	job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
	job.setImportFileUri("input file uri with SAS signature");
	job.setOutputContainerUri("container uri with SAS signature");
	job = hub.submitNotificationHubJob(job);

**等到工作完成：**

	while(true){
	    Thread.sleep(1000);
	    job = hub.getNotificationHubJob(job.getJobId());
	    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
	        break;
	}       

**取得所有工作：**

	List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**具備 SAS 簽章的 URI：**這是某個 Blob 檔案或 Blob 容器的 URL，加上參數集 (如權限和到期時間)，再加上所有使用帳戶 SAS 金鑰之項目的簽章。Azure Storage Java SDK 具有豐富的功能，包括建立此類的 URI。此外您可以參考 ImportExportE2E 測試類別 (從 github 位置) 的簡單替代方法，它可實作非常基本而精簡的簽署演算法。

###傳送通知
通知物件是附有標頭的本文，某些公用程式方法有助於建立原生和範本通知物件。

* **Windows 市集和 Windows Phone 8.1 (非 Silverlight)**

		String toast = "<toast><visual><binding template="ToastText01"><text id="1">Hello from Java!</text></binding></visual></toast>";
		Notification n = Notification.createWindowsNotification(toast);
		hub.sendNotification(n);

* **iOS**

		String alert = "{"aps":{"alert":"Hello from Java!"}}";
		Notification n = Notification.createAppleNotification(alert);
		hub.sendNotification(n);

* **Android**

		String message = "{"data":{"msg":"Hello from Java!"}}";
		Notification n = Notification.createGcmNotification(message);
		hub.sendNotification(n);

* **Windows Phone 8.0 和 8.1 Silverlight**

		String toast = "<?xml version="1.0" encoding="utf-8"?>" +
			        "<wp:Notification xmlns:wp="WPNotification">" +
			           "<wp:Toast>" +
			                "<wp:Text1>Hello from Java!</wp:Text1>" +
			           "</wp:Toast> " +
			        "</wp:Notification>";
		Notification n = Notification.createMpnsNotification(toast);
		hub.sendNotification(n);

* **Kindle Fire**

		String message = "{"data":{"msg":"Hello from Java!"}}";
		Notification n = Notification.createAdmNotification(message);
		hub.sendNotification(n);

* **傳送至標籤**

		Set<String> tags = new HashSet<String>();
		tags.add("boo");
		tags.add("foo");
		hub.sendNotification(n, tags);

* **傳送至標籤運算式**

		hub.sendNotification(n, "foo && ! bar");

* **傳送範本通知**

		Map<String, String> prop =  new HashMap<String, String>();
		prop.put("prop1", "v1");
		prop.put("prop2", "v2");
		Notification n = Notification.createTemplateNotification(prop);
		hub.sendNotification(n);

執行 Java 程式碼現在應會產生一則顯示於目標裝置的通知。

##<a name="next-steps"></a>後續步驟
在本主題中，我們會說明如何為通知中心建立簡單的 Java REST 用戶端。您可以在這裡執行下列動作：

* 下載完整 [Java SDK]，其中包含完整的 SDK 程式碼。 
* 試用範例：
	- [開始使用通知中樞]
	- [傳送即時新聞]
	- [傳送當地語系化的即時新聞]
	- [傳送通知給已驗證的使用者]
	- [傳送跨平台通知給已驗證的使用者]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[開始使用通知中樞]: http://www.windowsazure.com/manage/services/notification-hubs/getting-started-windows-dotnet/
[傳送即時新聞]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-dotnet/
[傳送當地語系化的即時新聞]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-localized-dotnet/
[傳送通知給已驗證的使用者]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users/
[傳送跨平台通知給已驗證的使用者]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Maven]: http://maven.apache.org/
 

<!---HONumber=July15_HO2-->