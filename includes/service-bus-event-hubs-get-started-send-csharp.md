## 將訊息傳送至事件中心

在本節中，您將撰寫一個 Windows 主控台應用程式，以將事件傳送至事件中心。

1. 在 Visual Studio 中，使用**主控台應用程式**專案範本建立新的「Visual C# 桌面應用程式」專案。將專案命名為 **Sender**。

   	![][7]

2. 在 [方案總管] 中，以滑鼠右鍵按一下方案，然後按一下 [**管理方案的 NuGet 封裝...**]。

	此時會顯示 [管理 NuGet 封裝] 對話方塊。

3. 搜尋 `Microsoft Azure Service Bus`，然後按一下 [**安裝**] 並接受使用條款。

	![][8]

	這會下載、安裝並新增 <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">Azure Service Bus 程式庫 NuGet 封裝</a>的參考。

4. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

		using System.Threading;
		using Microsoft.ServiceBus.Messaging;

5. 將下列欄位加入至 **Program** 類別，並將值替代為您在上一節中所建立的事件中樞名稱，以及將連接字串替代為 **Send** 權限：

		static string eventHubName = "{event hub name}";
        static string connectionString = "{send connection string}";

6. 將下列方法加入至 **Program** 類別：

		static void SendingRandomMessages()
        {
            var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
            while (true)
            {
                try
                {
                    var message = Guid.NewGuid().ToString();
                    Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                    eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
                }
                catch (Exception exception)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                    Console.ResetColor();
                }

                Thread.Sleep(200);
            }
        }

	這個方法會以 200 毫秒的延遲時間持續將事件傳送至事件中心。

7. 最後，將下列幾行加入至 **Main** 方法：

		Console.WriteLine("Press Ctrl-C to stop the sender process");
        Console.WriteLine("Press Enter to start now");
        Console.ReadLine();
        SendingRandomMessages();


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png

<!---HONumber=July15_HO2-->