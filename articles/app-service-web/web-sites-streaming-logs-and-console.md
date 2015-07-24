<properties 
	pageTitle="串流記錄和主控台" 
	description="串流記錄和主控台概觀"
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="adamab"/>

#串流記錄和主控台

### 串流記錄 ###

[Azure Preview 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)提供整合式串流記錄檢視器，可讓您使用 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web 應用程式即時檢視追蹤事件。

設定此功能只需要一些簡單的步驟：

- 在程式碼中撰寫追蹤
- 從 Azure Preview 入口網站啟用應用程式診斷
- 在 Web 應用程式刀鋒視窗上按一下串流記錄動態磚

### 如何在程式碼中撰寫追蹤 ###

在程式碼中撰寫追蹤很簡單。在 C# 中可輕易撰寫下列程式碼：

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

Trace 類別位於 System.Diagnostics 命名空間。

您可以在 node.js 應用程式中，撰寫此程式碼以取得相同的結果：

`````````````````````````
console.log("My trace statement").
`````````````````````````

### 啟用和檢視串流記錄 ###

可依每個網站為基礎啟用診斷。

1. 從 [Azure Preview 入口網站](https://portal.azure.com)中，按一下 [瀏覽] > [Web Apps]，以取得所有 Web 應用程式清單。  

2. 按一下要設定的 Web 應用程式名稱。

3. 按一下 [所有設定] > [診斷記錄]，再將 [Application Logging (Filesystem)] 切換到 [ON] 設定。

4. 接著出現 [等級] 選項，可讓您變更要擷取之追蹤的嚴重性等級。如果您才剛熟悉此功能，應該將此選項設定為 [詳細資訊]，因此這可確保記錄追蹤陳述式。

5. 按一下刀鋒視窗頂端的 [儲存]，便後可準備檢視記錄。

6. 若要從入口網站中檢視串流記錄，請按一下 Web 應用程式刀鋒視窗上的 [串流記錄] 動態磚。如果您的應用程式主動撰寫追蹤陳述式，則您應在結果視窗中近乎即時地看到這些追蹤陳述式。

![][StreamingLogsScreenshot]

## 存取主控台 ##

Azure Preview 入口網站提供可存取 Web 應用程式的主控台。您可以探索 Web 應用程式的檔案系統並執行 powershell/cmd 指令碼。執行主控台命令時，您會受到與執行 Web 應用程式程式碼之相同權限集的限制。您無法存取受保護的目錄或執行需要提高權限的指令碼。

1. 依上節所述，瀏覽至 Web 應用程式刀鋒視窗。

2. 按一下 [主控台] 動態磚，主控台隨即開啟。

![][ConsoleScreenshot]

若要熟悉主控台，請嘗試如下的基本命令：

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
 

<!---HONumber=62-->