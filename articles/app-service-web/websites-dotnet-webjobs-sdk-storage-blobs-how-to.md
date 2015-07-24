<properties 
	pageTitle="如何透過 WebJobs SDK 使用 Azure Blob 儲存體" 
	description="了解如何使用 WebJobs SDK 來使用 Azure Blob 儲存體。在容器中出現新的 Blob 時觸發程序並處理「有害 Blob」 。" 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="tdykstra"/>

# 如何透過 WebJobs SDK 使用 Azure Blob 儲存體

## 概觀

本指南提供 C# 程式碼範例，示範如何在建立或更新 Azure Blob 時觸發程序。此程式碼範例會使用 [WebJobs SDK](websites-dotnet-webjobs-sdk.md) 1.x 版。

如需示範如何建立 Blob 的程式碼範例，請參閱[如何使用 WebJobs SDK 來使用 Azure 佇列儲存體](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)。
		
本指南假設您知道[如何使用指向您儲存體帳戶的連接字串，在 Visual Studio 中建立 WebJob 專案](websites-dotnet-webjobs-sdk-get-started.md)。

## <a id="trigger"></a> 如何在建立或更新 Blob 時觸發函數

本節示範如何使用 `BlobTrigger` 屬性。

> **附註：**WebJobs SDK 會掃描要監看的的記錄檔，找出新的或變更的 Blob。此程序的速度原本就很慢；可能直到建立 Blob 之後數分鐘或更久，才會觸發函數。如果您的應用程式需要立即處理 Blob，建議的方法是當您建立 Blob 時建立佇列訊息，並在處理 Blob 的函數上使用 [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) 屬性，而不是 `BlobTrigger` 屬性。

### 適用於含有副檔名之 Blob 名稱的單一預留位置  

下列程式碼範例會將出現在 *input* 容器中的文字 Blob 複製到 *output* 容器：

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("output/{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

屬性建構函式會接受字串參數，以指定容器名稱和 Blob 名稱的預留位置。在此範例中，如果已在 *input* 容器中建立名為 *Blob1.txt*的 Blob，此函數便會在 *output* 容器中建立名為 *Blob1.txt*的 Blob。

您可以使用 Blob 名稱預留位置來指定名稱模式，如下列程式碼範例所示：

		public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
		    [Blob("output/copy-{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

此程式碼只會複製以 "original-" 做為名稱開頭的 Blob。例如，*input* 容器中的*原始 Blob1.txt* 會複製到 *output* 容器中的 *copy-Blob1.txt*。

如果您需要針對名稱中包含大括號的 Blob 名稱指定名稱模式，請按兩下大括號。例如，如果您想要在 *images* 容器中尋找具備如下名稱的 Blob：

		{20140101}-soundfile.mp3

針對您的模式使用下一行程式碼：

		images/{{20140101}}-{name}

在此範例中，*name* 預留位置的值會是 *soundfile.mp3*。

### 個別的 Blob 名稱和副檔名預留位置

下列程式碼範例會在將出現於 *input* 容器中的 Blob 複製到 *output* 容器時變更副檔名。此程式碼會記錄 *input* Blob 的副檔名，並將 *output* Blob 的副檔名設為 *.txt*。

		public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
		    [Blob("output/{name}.txt")] out string output,
		    string name,
		    string ext,
		    TextWriter logger)
		{
		    logger.WriteLine("Blob name:" + name);
		    logger.WriteLine("Blob extension:" + ext);
		    output = input.ReadToEnd();
		}

## <a id="types"></a> 您可以繫結至 Blob 的型別

您可將 `BlobTrigger` 屬性用於下列型別：

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* 透過 [ICloudBlobStreamBinder](#icbsb) 還原序列化的其他型別 

如果您想要直接使用 Azure 儲存體帳戶，也可以將 `CloudStorageAccount` 參數新增至方法簽章。

## <a id="string"></a> 繫結至字串來取得文字 Blob 內容

如果預期會取得文字 Blob，`BlobTrigger` 就可以將 套用至 `string` 參數。下列程式碼範例會將文字 Blob 繫結至名為 `logMessage` 的 `string` 參數。此函數會使用該參數，將 Blob 的內容寫入 WebJobs SDK 儀表板。
 
		public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
		    string name, 
		    TextWriter logger)
		{
		     logger.WriteLine("Blob name: {0}", name);
		     logger.WriteLine("Content:");
		     logger.WriteLine(logMessage);
		}

## <a id="icbsb"></a> 使用 ICloudBlobStreamBinder 來取得序列化的 Blob 內容

下列程式碼範例會使用一個類別，實作 `ICloudBlobStreamBinder` 來啟用 `BlobTrigger` 屬性，以便將 Blob 繫結至 `WebImage` 型別。

		public static void WaterMark(
		    [BlobTrigger("images3/{name}")] WebImage input,
		    [Blob("images3-watermarked/{name}")] out WebImage output)
		{
		    output = input.AddTextWatermark("WebJobs SDK", 
		        horizontalAlign: "Center", verticalAlign: "Middle",
		        fontSize: 48, opacity: 50);
		}
		public static void Resize(
		    [BlobTrigger("images3-watermarked/{name}")] WebImage input,
		    [Blob("images3-resized/{name}")] out WebImage output)
		{
		    var width = 180;
		    var height = Convert.ToInt32(input.Height * 180 / input.Width);
		    output = input.Resize(width, height);
		}

`WebImage` 繫結程式碼存在於衍生自 `ICloudBlobStreamBinder` 的 `WebImageBinder` 類別中。

		public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
		{
		    public Task<WebImage> ReadFromStreamAsync(Stream input, 
		        System.Threading.CancellationToken cancellationToken)
		    {
		        return Task.FromResult<WebImage>(new WebImage(input));
		    }
		    public Task WriteToStreamAsync(WebImage value, Stream output,
		        System.Threading.CancellationToken cancellationToken)
		    {
		        var bytes = value.GetBytes();
		        return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
		    }
		}

## <a id="poison"></a> 如何處理有害的 Blob

當 `BlobTrigger` 函數失敗時，SDK 會再次呼叫它，以防失敗是因暫時性錯誤所造成。如果失敗是因為 Blob 的內容所造成，則此函數會在其每次嘗試處理該 Blob 時失敗。根據預設，SDK 最多會針對指定的 Blob 呼叫函數 5 次。如果第五次嘗試失敗，則 SDK 會在名為 *webjobs-blobtrigger-poison* 的佇列中新增一則訊息。

您可以設定重試次數上限。相同的 [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) 設定可用於處理有害的 Blob 和處理有害的佇列訊息。

適用於有害 Blob 的佇列訊息是一個 JSON 物件，其中包含下列屬性：

* FunctionId (格式為 *{WebJob name}*.Functions.*{Function name}*，例如： WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" 或 "PageBlob")
* ContainerName
* BlobName
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

在下列程式碼範例中，`CopyBlob` 函數具有會導致每次呼叫它時發生失敗的程式碼。在 SDK 呼叫它的重試次數達到上限之後，就會在有害的 Blob 佇列中建立一則訊息，而該訊息會由 `LogPoisonBlob` 函數來處理。

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("textblobs/output-{name}")] out string output)
		{
		    throw new Exception("Exception for testing poison blob handling");
		    output = input.ReadToEnd();
		}
		
		public static void LogPoisonBlob(
		[QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
		    TextWriter logger)
		{
		    logger.WriteLine("FunctionId: {0}", message.FunctionId);
		    logger.WriteLine("BlobType: {0}", message.BlobType);
		    logger.WriteLine("ContainerName: {0}", message.ContainerName);
		    logger.WriteLine("BlobName: {0}", message.BlobName);
		    logger.WriteLine("ETag: {0}", message.ETag);
		}

SDK 會自動將該 JSON 訊息還原序列化。以下是 `PoisonBlobMessage` 類別：

		public class PoisonBlobMessage
		{
		    public string FunctionId { get; set; }
		    public string BlobType { get; set; }
		    public string ContainerName { get; set; }
		    public string BlobName { get; set; }
		    public string ETag { get; set; }
		}

### <a id="polling"></a> Blob 輪詢演算法

WebJobs SDK 會在應用程式啟動時，掃描 `BlobTrigger` 屬性所指定的所有容器。在大型儲存體帳戶中，這個掃描需要花費一些時間，因此，可能需要一段時間才能找到新的 Blob 以及執行 `BlobTrigger` 函數。

為了在應用程式啟動之後偵測新的或已變更的 Blob，SDK 會定期讀取 Blob 儲存體記錄檔。Blob 記錄檔會進行緩衝處理，只會大約每隔 10 分鐘才進行實際寫入，因此，在建立或更新 Blob 之後且在執行相對應的 `BlobTrigger` 函數之前可能會有明顯的延遲。

您使用 `Blob` 屬性建立的 Blob 會有一個例外狀況。當 WebJobs SDK 建立新的 Blob 時，會立即將新的 Blob 傳遞到任何相符的 `BlobTrigger` 函數。因此，如果您具有 Blob 輸入和輸出的鏈結，就能有效率地處理它們。但是，如果您想要降低在針對透過其他方法建立或更新的 Blob 執行 Blob 處理函數時的延遲，建議使用 `QueueTrigger` 而非 `BlobTrigger`。

### <a id="receipts"></a> Blob 回條

WebJobs SDK 可確保不會有任何 `BlobTrigger` 函數會針對相同的新或更新的 Blob 呼叫一次以上。它的運作方式是藉由維護 *Blob 回條*來判斷指定的 Blob 版本是否已處理過。

Blob 回條儲存於 AzureWebJobsStorage 連接字串所指定之 Azure 儲存體帳戶中名為 *azure-webjobs-hosts* 的容器中。Blob 回條具有下列資訊：

* Blob 已呼叫的函數 ("*{WebJob name}*.Functions.*{Function name}*"，例如："WebJob1.Functions.CopyBlob")
* 容器名稱
* Blob 類型 ("BlockBlob" 或 "PageBlob")
* Blob 名稱
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

如果您想要強制重新處理某個 Blob，可以從 *azure-webjobs-hosts* 容器中手動刪除該 Blob 的 Blob 回條。

## <a id="queues"></a>佇列文章所涵蓋的相關主題

如需如何處理佇列訊息所觸發的 Blob 處理的相關資訊，或是非 Blob 處理特有的 WebJobs SDK 案例，請參閱[如何透過 WebJobs SDK 使用 Azure 佇列儲存體](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)。

該文章中涵蓋的相關主題如下：

* Async 函數
* 多個執行個體
* 正常關機
* 在函式主體中使用 WebJobs SDK 屬性
* 在程式碼中設定 SDK 連接字串。
* 在程式碼中設定 WebJobs SDK 建構函式參數的值
* 設定 `MaxDequeueCount` 來處理有害的 Blob。
* 手動觸發函式
* 寫入記錄檔

## <a id="nextsteps"></a> 後續步驟

本指南提供了程式碼範例，示範如何處理使用 Azure Blob 的常見案例。如需 Azure WebJobs 和 WebJobs SDK 的詳細資訊，請參閱[Azure WebJobs 建議使用的資源](http://go.microsoft.com/fwlink/?linkid=390226)。
 

<!---HONumber=62-->