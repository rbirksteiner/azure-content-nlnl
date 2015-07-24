## 使用 EventProcessorHost 接收訊息

[EventProcessorHost] 是一個 .NET 類別，透過管理持續檢查點以及來自事件中樞的平行接收，簡化來自事件中樞之事件的接收作業。使用 [EventProcessorHost]，您可以將事件分割到多個接收者，即使裝載於不同的節點時也是一樣。這個範例顯示單一接收者如何使用 [EventProcessorHost]。[擴充事件處理]範例顯示如何搭配使用 [EventProcessorHost] 與多個接收者。

若要使用 [EventProcessorHost]，您必須擁有 [Azure 儲存體帳戶]：

1. 登入 [Azure 管理入口網站]，並按一下畫面底部的 [新增]。

2. 依序按一下 [**資料服務**]、[**儲存體**] 和 [**快速建立**]，然後輸入儲存體帳戶的名稱。選取您所需的區域，然後按一下 [**建立儲存體帳戶**]。

    ![][11]

3. 按一下新建立的儲存體帳戶，然後按一下 [**管理存取金鑰**]：

    ![][12]

    複製存取金鑰以便稍後在此教學課程中使用。

4. 在 Visual Studio 中，使用**主控台應用程式**專案範本建立新的「Visual C# 桌面應用程式」專案。將專案命名為 **Receiver**。

    ![][14]

5. 在 [方案總管] 中，以滑鼠右鍵按一下方案，然後按一下 [**管理 NuGet 封裝**]。

	[**管理 NuGet 封裝**] 對話方塊隨即出現。

6. 搜尋 `Microsoft Azure Service Bus Event Hub - EventProcessorHost`，然後按一下 [**安裝**] 並接受使用條款。

    ![][13]

	這會下載、安裝並新增 <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost">Azure Service Bus Event Hub - EventProcessorHost NuGet 封裝</a>的參考與其所有相依性。

7. 在 [**接收者**] 專案上按一下滑鼠右鍵，按一下 [**新增**]，然後按一下 [**類別**]。將新類別命名為 **SimpleEventProcessor**，然後按一下 [**確定**] 以建立該類別。

8. 在 SimpleEventProcessor.cs 檔案開頭處新增下列陳述式：

		using Microsoft.ServiceBus.Messaging;
		using System.Diagnostics;
		using System.Threading.Tasks;

	接著，將該類別的主體取代為下列程式碼：

		class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;

	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }

	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }

	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());

	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                    context.Lease.PartitionId, data));
	            }

	            //Call checkpoint every 5 minutes, so that worker can resume processing from the 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
                {
                    await context.CheckpointAsync();
                    this.checkpointStopWatch.Restart();
                }
	        }
	    }

	**EventProcessorHost** 會呼叫這個類別來處理接收自事件中樞的事件。請注意，`SimpleEventProcessor` 類別會使用馬錶定期在 **EventProcessorHost** 內容上呼叫檢查點方法。這可確保重新啟動接收者時，遺失的處理工作不超過五分鐘。

9. 在 **Program** 類別中，於頂端新增下列 `using` 陳述式：

		using Microsoft.ServiceBus.Messaging;
		using Microsoft.Threading;
		using System.Threading.Tasks;

	然後，如下所示修改 **Program** 類別的 **Main** 方法，替代事件中樞名稱和連接字串，以及您在先前各節中複製的儲存體帳戶和金鑰：

        static void Main(string[] args)
        {
          string eventHubConnectionString = "{event hub connection string}";
          string eventHubName = "{event hub name}";
          string storageAccountName = "{storage account name}";
          string storageAccountKey = "{storage account key}";
          string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
              storageAccountName, storageAccountKey);

          string eventProcessorHostName = Guid.NewGuid().ToString();
          EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
          Console.WriteLine("Registering EventProcessor...");
          eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>().Wait();

          Console.WriteLine("Receiving. Press enter key to stop worker.");
          Console.ReadLine();
          eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }

> [AZURE.NOTE]本教學課程使用單一 [EventProcessorHost] 執行個體。若要增加輸送量，建議您執行多個 [EventProcessorHost] 執行個體 (如[擴充事件處理]範例所示)。在這些情況下，各種執行個體會自動彼此協調以對已接收的事件進行負載平衡。如果您想要多個接收者都處理*所有*事件，則必須使用 **ConsumerGroup** 概念。收到來自不同電腦的事件時，根據在其中執行 [EventProcessorHost] 執行個體的電腦 (或角色) 來指定名稱可能十分有用。如需這些主題的詳細資訊，請參閱[事件中樞概觀]和[事件中樞程式設計指南]主題。

<!-- Links -->
[事件中樞概觀]: http://msdn.microsoft.com/library/azure/dn836025.aspx
[擴充事件處理]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Azure 儲存體帳戶]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Azure 管理入口網站]: http://manage.windowsazure.com

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png

[事件中樞程式設計指南]: http://msdn.microsoft.com/library/azure/dn789972.aspx
[Async Await in Console Apps]: http://blogs.msdn.com/b/pfxteam/archive/2012/01/20/10259049.aspx
[AsyncPump.cs]: http://blogs.msdn.com/cfs-file.ashx/__key/communityserver-components-postattachments/00-10-25-90-49/AsyncPump_2E00_cs

<!---HONumber=July15_HO2-->