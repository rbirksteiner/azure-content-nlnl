<properties 
	pageTitle="什麼是 Azure WebJobs SDK" 
	description="Azure WebJobs SDK 簡介。說明 SDK 是什麼、適用哪些典型案例，以及程式碼範例。" 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/03/2015" 
	ms.author="tdykstra"/>

# 什麼是 Azure WebJobs SDK

## <a id="overview"></a>概觀

本文說明 WebJobs SDK 是什麼、檢閱部分適用的典型案例，以及提供在程式碼中的使用方式概觀。

[WebJobs](web-sites-create-web-jobs.md) 是一項 Azure App Service 功能，可讓您在與 Web 應用程式相同的內容中執行程式或指令碼。WebJobs SDK 的目的旨在簡化程式碼撰寫 (包括以 WebJob 的形式執行，及使用 Azure 儲存體佇列、Blob 和資料表，和服務匯流排佇列等) 工作。

WebJobs SDK 包含下列元件：

* **NuGet 封裝**。新增至 Visual Studio 主控台應用程式專案的 NuGet 封裝提供一個架構，您的程式碼可透過此架構來使用 Azure 儲存體服務或服務匯流排佇列。   
  
* **儀表板**。Azure App Service 中包含部分的 WebJobs SDK，該部份項目可針對使用 NuGet 封裝的程式提供豐富的監控和診斷功能。您無需撰寫程式碼就可以使用這些監視和診斷功能。

## <a id="scenarios"></a>案例

下列是 Azure WebJobs SDK 可協助您輕鬆處理的部分典型案例：

* 影像處理或其他需要大量 CPU 的工作。網站的一項常見功能是上傳影像或影片的能力。在許多時候，您想要在內容上傳後處理該內容，但又不想在您執行此作業時讓使用者等候。

* 佇列處理。Web 前端與後端服務的一個常見通訊方式是使用佇列。當網站需要完成工作時，它會將訊息推播至佇列。後端服務會從佇列提取訊息，並完成工作。您可以在影像處理中使用佇列：例如，在使用者上傳多個檔案之後，將檔案名稱放置於佇列訊息中，由後端挑選佇列訊息進行處理。或者您可以使用佇列來提升網站回應能力。例如，與其將目錄直接寫入 SQL Database，請寫入佇列並告知使用者已完成，然後由後端服務處理高延遲的關聯式資料庫工作。如需使用影像處理的佇列處理範例，請參閱 [WebJobs SDK 入門教學課程](websites-dotnet-webjobs-sdk-get-started.md)。

* RSS 彙總。如果您有維持 RSS 摘要清單的網站，您可以在背景處理序中提取摘要的所有文章。

* 檔案維護，例如彙總或清除記錄檔案。您可能擁有由數個網站在不同的時間範圍內所建立的記錄檔案，您想要結合這些檔案以便執行分析工作。或者您想要排程每週執行的工作，來將舊的記錄檔案清除。

* 輸入 Azure 資料表。您可能會有想要剖析的儲存檔案和 Blob，並想要將資料儲存在資料表中。輸入函數可能會寫入許多行 (在某些情況下可能有上百萬行)，而 WebJobs SDK 讓您可以輕易地實作此功能。SDK 還提供進度指標的即時監控，例如資料表中的寫入行數。

* 其他您想要在背景執行序中執行的長時間執行工作，例如[傳送電子郵件](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure)。

## <a id="code"></a> 程式碼範例

使用 Azure 儲存體的處理傳統工作程式碼十分簡單。在主控台應用程式中，您可以針對您要執行的背景工作寫入方法，然後使用 WebJobs SDK 的屬性加以裝飾。您的 `Main` 方法將建立可在寫入方法中協調呼叫的 `JobHost` 物件。WebJobs SDK 架構會根據方法中所用的 WebJobs SDK 屬性，知道何時呼叫方法。

下列是個簡單程式，可用來輪詢佇列並為收到的每則佇列訊息建立 Blob：

		public static void Main()
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
		{
		    writer.WriteLine(inputText);
		}

`JobHost` 物件是一組背景功能的容器。`JobHost` 物件可監視功能、注意可觸發功能的事件，以及發生觸發事件時執行功能。您可以呼叫 `JobHost` 方法，指出您要在目前執行緒或背景執行緒上執行容器程序。在此範例中，`RunAndBlock` 方法會在目前執行緒上持續執行程序。

由於此範例中的 `ProcessQueueMessage` 方法具有 `QueueTrigger` 屬性，接收新的佇列訊息時便會觸發該功能。`JobHost` 物件會注意指定佇列 (在此範例中是 "webjobsqueue") 上的新佇列訊息，找到新佇列訊息時，此物件便會呼叫 `ProcessQueueMessage`。`QueueTrigger` 屬性也會通知架構，將 `inputText` 參數繫結到佇列訊息的值：

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] <mark>string inputText</mark>,
    [Blob("containername/blobname")]TextWriter writer)</pre>

架構也會將 `TextWriter` 物件繫結至 "containername" 容器中名為 "blobname" 的 Blob：

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText,
    <mark>[Blob("containername/blobname")]TextWriter writer</mark>)</pre>

此功能會接著使用這些參數來將佇列訊息的值寫入 Blob：

		writer.WriteLine(inputText);

WebJobs SDK 的觸發和繫結器功能可大幅簡化若要使用 Azure 儲存體和服務匯流排佇列所必須撰寫的程式碼。WebJobs SDK 架構會為您完成處理佇列和 Blob 處理所需的低階程式碼 -- 此架構會建立尚未存在的佇列、開啟此佇列、讀取佇列訊息、並在處理完成後刪除佇列訊息、建立尚未存在的 Blob 容器、寫入 Blob 等。

WebJobs SDK 提供多種使用 Azure 儲存體的方法。例如，如果使用 `QueueTrigger` 屬性加以裝飾的參數是位元組陣列或自訂類型，則此參數會從 JSON 自動還原序列化。您也可以使用 `BlobTrigger` 屬性，每當 Azure 儲存體帳戶中建立新的 Blob 時便觸發程序。(請注意，`QueueTrigger` 可以在幾秒內找到新的佇列訊息，但 `BlobTrigger` 最多可能需要花上 20 分鐘的時間才能偵測到新的 Blob。`BlobTrigger` 會在 `JobHost` 每次啟動時掃描以尋找 Blob，然後定期檢查 Azure 儲存體記錄以偵測新的 Blob。)

## <a id="workerrole"></a>在 WebJobs 外部使用 WebJobs SDK

使用 WebJobs SDK 的程式是指可在任意位置執行的標準主控台應用程式 -- 它不一定要以 WebJob 的形式執行。您可以在開發電腦上本機測試程式，並在實際執行環境中，以雲端服務背景工作角色或 Windows 服務的身分執行此程式 (如果您慣用其中一個環境)。

不過，對於 Azure App Service Web 應用程式，儀表板只能做為延伸模組來使用。如果您想要在 WebJob 外部執行但仍然使用儀表板，則您可以設定 Web 應用程式使用 WebJobs SDK 儀表板連接字串所參考的相同儲存體帳戶，接著該 Web 應用程式的 WebJobs 儀表板便會顯示有關這個異地執行程式中的執行函數資料。您可以使用 URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions 來進入儀表板。如需詳細資訊，請參閱[使用 WebJobs SDK 來取得本機開發的儀表板](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx) (英文)，但請注意，此部落格文章顯示舊的連接字串名稱。

## <a id="nostorage"></a>使用 WebJobs SDK 叫用任何函數

即使您不需要直接處理 Azure 儲存體佇列、資料表、或 Blob 或服務匯流排佇列，WebJobs SDK 仍然提供下列優點：

* 您可以從儀表板叫用函數。
* 您可以從儀表板轉送函數。
* 您可以在儀表板中檢視記錄檔，連結到特定的 WebJob (使用 Console.Out、Console.Error、Trace 等編寫的應用程式記錄檔) 或連結到產生它們的特定函式引動過程 (使用 SDK 傳遞至函數做為參數的 `TextWriter` 物件編寫的記錄檔)。 

* 如需詳細資訊，請參閱[如何手動叫用函數](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual)和[如何寫入記錄](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)

## <a id="nextsteps"></a>接續步驟

如需 WebJobs SDK 的詳細資訊，請參閱 [Azure WebJobs 建議使用的資源](http://go.microsoft.com/fwlink/?linkid=390226)。
 

<!---HONumber=62-->