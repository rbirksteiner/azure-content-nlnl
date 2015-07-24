<properties
	pageTitle=".NET 多層式應用程式 - Azure 教學課程"
	description="協助您在 Azure 中開發多層式應用程式，以使用服務匯流排佇列在各層之間進行通訊的教學課程。.NET 的範例。"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="07/02/2015"
	ms.author="sethm"/>

# 使用服務匯流排佇列的 .NET 多層式應用程式

## 簡介

使用 Visual Studio 2013 和免費 Azure SDK for .NET 開發 Microsoft Azure 很容易。如果您還沒有 Visual Studio 2013，則 SDK 將自動安裝完全免費的 Visual Studio Express，供您開始進行 Azure 相關開發。本指南假設您從未使用過 Azure。完成本教學課程後，您將有個使用多個 Azure 資源的應用程式，這些資源在您本機環境中執行，用來示範多層式應用程式如何運作。

您將了解：

-   如何透過單一下載和安裝，讓電腦適合用於進行 Azure 開發。
-   如何使用 Visual Studio 進行 Azure 相關開發。
-   如何使用 Web 與背景工作角色，在 Azure 建立多層式應用程式。
-   如何使用服務匯流排佇列，在階層之間進行通訊。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

在本教學課程中，您將在 Azure 雲端服務中建置和執行多層式應用程式。前端將為 ASP.NET MVC Web 角色，而後端將為背景工作角色。您可以建立相同的多層式應用程式，並使其前端作為部署至 Azure 網站而非雲端服務的 Web 專案。如需 Azure 網站前端不同作法的指示，請參閱[後續步驟](#nextsteps)一節。

以下顯示完整應用程式的螢幕擷取畫面：

![][0]

**附註** Azure 也會提供儲存體佇列功能。如需 Azure 儲存體佇列和服務匯流排佇列的詳細資訊，請參閱 [Azure 佇列和 Azure 服務匯流排佇列 - 比較和對比][sbqueuecomparison]。

## 案例概觀：內部角色通訊

為了提交訂單進行處理，Web 角色中執行的前端 UI 元件需要與背景工作角色中執行的中間層互動。此範例使用由服務匯流排代理的傳訊，在各層之間進行通訊。

在 Web 層與中間層之間使用代理的通訊，可使這兩個元件彼此脫鉤。相較於直接傳訊 (亦即，TCP 或 HTTP)，Web 層不會直接連線至中間層，而是透過訊息的形式，將工作單位推播至服務匯流排，而後者會可靠地保管工作單位，直到中間層準備好取用並處理這些工作單位為止。

服務匯流排提供兩個實體來支援代理的傳訊，也就是：佇列和主題。使用佇列時，每個傳送至佇列的訊息都是由單一接收者取用。主題可支援發佈/訂用帳戶模式，亦即每個發佈的訊息會提供給在主題註冊的訂用帳戶。每個訂用帳戶在邏輯上會維護自己的訊息佇列。訂用帳戶也可以設定成使用篩選規則，以將傳遞至訂用帳戶佇列的訊息限制為符合篩選條件的訊息。本範例使用服務匯流排佇列。

![][1]

此通訊機制具有數個超越直接傳訊的優點，即：

-   **暫時分離。** 使用非同步傳訊模式時，生產者和取用者不需要同時處於線上狀態。服務匯流排會可靠地保管訊息，直到取用方準備好接收訊息為止。如此一來，分散式應用程式的元件即使中斷連線 (例如，基於維護原因而計劃性中斷，或基於元件損毀而意外中斷)，也不會影響整體系統。此外，取用方應用程式只需要在一天中的特定時間處於線上狀態即可。

-   **負載調節。** 在許多應用程式中，系統負載會隨時間改變，而處理每個工作單位所需的時間卻通常固定不變。有佇列作為訊息生產者與取用者之間的中間者後，就只需佈建取用方應用程式 (背景工作) 來容納平均負載而非尖峰負載。佇列的深度會隨著連入負載的改變而增加和縮短。就處理應用程式負載所需的基礎結構數量而言，如此可直接節省金錢。

-   **負載平衡。** 當負載增加時，可以新增更多的背景工作程序來讀取佇列中的訊息。每個訊息僅由其中一個背景工作程序處理。此外，這個提取型負載平衡機制可讓背景工作電腦獲得最佳利用率，即使背景工作電腦的處理能力有所不同也一樣，因為背景工作電腦將以自己的最大速率提取訊息。此模式通常稱為競爭取用者 (英文)模式。

    ![][2]

下列幾節討論實作此架構的程式碼。

## 設定開發環境

在開始開發 Azure 應用程式之前，請先下載工具並設定您的開發環境。

1.  若要安裝 Azure SDK for .NET，請按一下底下按鈕：

    [取得工具和 SDK (英文)][]

2. 	按一下您要使用的 Visual Studio 版本的連結。本教學課程中的步驟使用 Visual Studio 2013：

	![][32]

4. 	當系統提示您執行或儲存安裝檔案時，按一下 [執行]：

    ![][3]

5.  在 Web Platform Installer 中，按一下 [安裝] 並繼續進行安裝：

    ![][33]

6.  安裝完成之後，您即可開始進行開發。SDK 包括可讓您在 Visual Studio 中開發 Azure 應用程式的工具。如果您未安裝 Visual Studio，它也會安裝免費的 Visual Studio Express for Web。

## 設定服務匯流排命名空間

下一步是建立服務命名空間，並取得共用存取簽章 (SAS) 金鑰。服務命名空間會為每個透過服務匯流排公開的應用程式提供應用程式界限。建立服務命名空間時，系統會產生 SAS 金鑰。服務命名空間與 SAS 金鑰的結合提供了認證，供服務匯流排驗證對應用程式的存取權。

請注意，您也可以使用 Visual Studio 伺服器總管，來管理命名空間和服務匯流排傳訊實體，但是只能從入口網站內建立新的命名空間。

### 使用管理入口網站設定命名空間

1.  登入 [Azure 管理入口網站][]。

2.  在管理入口網站的左方瀏覽窗格中，按一下 [服務匯流排]。

3.  在管理入口網站的下方窗格中，按一下 [建立]。

    ![][6]

4.  在 [Add a new namespace] 對話方塊中，輸入命名空間名稱。系統會立即檢查此名稱是否可用。![][7]

5.  確定命名空間名稱可用之後，請選擇要代管命名空間的國家或區域 (必須使用您要部署運算資源的相同國家/區域)。另外，請確定您在命名空間的 [**類型**] 欄位中選取 [**傳訊**]，並在 [**傳訊層**] 欄位中選取 [**標準**]。

    重要事項：請挑選您想要選擇來部署應用程式的**相同區域**。這樣可以獲得最佳效能。

6.  按一下核取記號。此時系統會建立並啟用服務命名空間。系統為帳戶提供資源時，您可能需要等幾分鐘。

	![][27]

7.  在主視窗中，按一下服務命名空間的名稱。

	![][30]

8. 按一下 [連線資訊]。

	![][31]

9.  在 [**存取連線資訊**] 窗格中，尋找包含 SAS 金鑰和金鑰名稱的連接字串。

    ![][35]

10.  記下這些認證，或將它們複製到剪貼簿。

## 建立 Web 角色

在本節中，您將建置應用程式的前端。首先您將建立應用程式顯示的各個頁面。之後，您將新增程式碼，以提交項目給服務匯流排佇列，並顯示佇列的狀態資訊。

### 建立專案

1.  使用系統管理員權限，啟動 Microsoft Visual Studio 2013 或 Microsoft Visual Studio Express。若要以系統管理員權限啟動 Visual Studio，請在 [Microsoft Visual Studio 2013] (或 [Microsoft Visual Studio Express]) 上按一下滑鼠右鍵，然後按一下 [以系統管理員身分執行]。本指南稍後討論的 Azure 計算模擬器需要您以系統管理員權限啟動 Visual Studio。

    在 Visual Studio 的 [檔案] 功能表，按一下 [新增]，然後按一下 [專案]。

    ![][8]


2.  從 [已安裝的範本] 的 [Visual C#] 下，按一下 [雲端]，然後按一下 [Azure 雲端服務]。將專案命名為 **MultiTierApp**。然後按一下 [確定]。

    ![][9]

3.  從 **.NET Framework 4.5** 角色中，按兩下 [ASP.NET Web 角色]。

    ![][10]

4.  將滑鼠移至 [Azure Cloud Service solution 下的 [WebRole1]，按一下鉛筆圖示，將 Web 角色重新命名為 **FrontendWebRole**。然後按一下 [確定]。(請確定您輸入 "Frontend"，其中 e 為小寫，而不是 "FrontEnd"。)

    ![][11]

5.  從 [New ASP.NET Project] 對話方塊的 [選取範本] 清單中，按一下 [MVC]，然後按一下 [確定]。

    ![][12]

6.  在 [方案總管] 中，於 [參考] 上按一下滑鼠右鍵，然後按一下 [Manage NuGet Packages...] 或 [加入程式庫套件參考]。

7.  選取對話方塊左手邊的 [線上]。搜尋 "**Service Bus**"，並選取 [**Azure 服務匯流排**] 項目。然後完成安裝並關閉此對話方塊。

    ![][13]

8.  請注意，現在已參考必要的用戶端組件，並已新增一些新的程式碼檔案。

9.  在 [方案總管] 中，於 [模型] 上按一下滑鼠右鍵、按一下 [新增]，再按一下 [類別]。在 [**名稱**] 方塊中，輸入名稱 **OnlineOrder.cs**。然後按一下 [新增]。

### 撰寫 Web 角色的程式碼

在本節中，您將建立應用程式顯示的各個頁面。

1.  在 Visual Studio 的 **OnlineOrder.cs** 檔案中，將現有的命名空間定義取代為下列程式碼：

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  在 [方案總管] 中，按兩下 [Controllers\\HomeController.cs]。在檔案頂端新增下列 **using** 陳述式，以納入您剛建立之模型的命名空間，以及服務匯流排：

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  同時在 Visual Studio 的 **HomeController.cs** 檔案中，也將現有的命名空間定義取代為下列程式碼。此程式碼包含將項目提交給佇列的處理方法：

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit
                // Controller method for a view you will create for the submission
                // form
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit
                // Controller method for handling submissions from the submission
                // form
                [HttpPost]
				// Attribute to help prevent cross-site scripting attacks and
				// cross-site request forgery  
    			[ValidateAntiForgeryToken]
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        // Will put code for submitting to queue here.

                        return RedirectToAction("Submit");
                    }
                    else
                    {
                        return View(order);
                    }
                }
            }
        }

4.  從 [**建置**] 功能表中，按一下 [**建置方案**] 以測試您的工作到目前為止是否正確無誤。

5.  現在，您將為上面建立的 **Submit()** 方法建立檢視。在 Submit() 方法內按一下滑鼠右鍵，然後選擇 [**加入檢視**]。

    ![][14]

6.  隨即出現對話方塊，供您建立檢視。在 [**範本**] 下拉式清單中，選擇 [**建立**]。在 [**模型類別**] 下拉式清單中，按一下 [**OnlineOrder**] 類別。

    ![][15]

7.  按一下 [新增]。

8.  現在，變更您的應用程式的顯示名稱。在 [**方案總管**] 中，按兩下 **Views\\Shared\\_Layout.cshtml** 檔案以在 Visual Studio 編輯器中開啟。

9.  將所有出現的 **My ASP.NET Application** 取代為 **LITWARE'S Products**。

10. 移除 [首頁]、[關於] 和 [連絡人] 連結。刪除反白顯示的程式碼：

	![][28]

11. 最後，修改提交頁面，以納入佇列的一些相關資訊。在 [方案總管] 中，按兩下 [Views\\Home\\Submit.cshtml] 檔案以在 Visual Studio 編輯器中開啟。在 **&lt;h2>Submit&lt;/h2>** 後面新增下列一行。**ViewBag.MessageCount** 現在暫時是空的。稍後您將在其中填入資料。

        <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>


12. 您現在已實作 UI。您可以按 **F5** 執行應用程式，確認它的外觀與預期一樣。

    ![][17]

### 撰寫增程式碼以提交項目給服務匯流排佇列

現在，您將新增程式碼以提交項目給佇列。首先，您將建立一個類別，並使其包含服務匯流排佇列連接資訊。接著，您將從 **Global.aspx.cs** 初始化連線。最後，您將更新稍早在 **HomeController.cs** 建立的提交程式碼，以將項目實際提交給服務匯流排佇列。

1.  在 [方案總管] 中，於 [FrontendWebRole] 上按一下滑鼠右鍵 (在專案而非角色上按一下滑鼠右鍵)。按一下 [加入]，然後按一下 [類別]。

2.  將類別命名為 **QueueConnector.cs**。按一下 [加入] 以建立類別。

3.  您現在要加入可封裝連線資訊、並初始化與服務匯流排佇列連線的程式碼。在 QueueConnector.cs 中，加入下列程式碼，並在 [**命名空間**] (您的服務命名空間) 和 [**yourKey**] (這是您稍早從 [Azure 管理入口網站][Azure Management Portal]中取得的 SAS 金鑰) 中輸入值。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

        namespace FrontendWebRole
        {
            public static class QueueConnector
            {
                // Thread-safe. Recommended that you cache rather than recreating it
                // on every request.
                public static QueueClient OrdersQueueClient;

                // Obtain these values from the Management Portal
                public const string Namespace = "your service bus namespace";

                // The name of your queue
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey");
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls
                    ServiceBusEnvironment.SystemConnectivity.Mode =
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to
                    // management operations
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address,
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

    請注意，稍後在本教學課程中，您將學會如何將您的**命名空間**名稱和您的 SAS 金鑰值儲存在組態檔中。

4.  現在，請確定會呼叫自己的 **Initialize** 方法。在 [方案總管] 中，按兩下 [Global.asax\\Global.asax.cs]。

5.  將下列一行新增至 **Application_Start** 方法底部：

        FrontendWebRole.QueueConnector.Initialize();

6.  最後，更新稍早建立的 Web 程式碼，以提交項目給佇列。在 [方案總管] 中，按兩下 [Controllers\\HomeController.cs]。

7.  將 **Submit()** 方法更新為如下，以取得佇列的訊息計數：

        public ActionResult Submit()
        {
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  將 **Submit(OnlineOrder order)** 方法更新為如下，以提交訂單資訊給佇列：

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order
                var message = new BrokeredMessage(order);

                // Submit the order
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  現在您可以重新執行應用程式。每次您一提交訂單，訊息計數便會增加。

    ![][18]

## 雲端組態管理員

**Microsoft.WindowsAzure.Configuration.CloudConfigurationManager** 類別中的 **GetSettings** 方法可讓您從平台的組態存放區中讀取組態設定。例如，如果您的程式碼是在 Web 或背景工作角色中執行，則 **GetSettings** 方法會讀取 ServiceConfiguration.cscfg 檔案，如果您的程式碼是在標準的主控台應用程式中執行，則 **GetSettings** 方法會讀取 app.config 檔案。

如果您將服務匯流排命名空間的連接字串儲存在組態檔中，則您可以使用 **GetSettings** 方法來讀取可用來具現化 **NamespaceMananger** 物件的連接字串。您可以使用 **NamespaceMananger** 執行個體，以程式設計的方式來設定服務匯流排命名空間。您可以使用相同的連接字串來具現化可用來執行執行階段作業 (例如傳送和接收訊息) 的用戶端物件 (例如 **QueueClient**、**TopicClient**，和 **EventHubClient** 物件)。

### Connection string

若要具現化用戶端 (例如，服務匯流排 **QueueClient**)，您可以以連接字串代表組態資訊。在用戶端中，`CreateFromConnectionString()` 方法可以使用該連接字串來具現化該用戶端類型。例如，假設有下列組態區段：

	<ConfigurationSettings>
    ...
    	<Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=RootManageSharedAccessKey;SharedSecretValue=[yourKey]" />
	</ConfigurationSettings>

下列程式碼會擷取連接字串、建立佇列，以及具現化對佇列的連線：

	QueueClient Client;

	string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString);

	if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

	// Initialize the connection to Service Bus Queue
	Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

下節中的程式碼會使用 **CloudConfigurationManager** 類別。

## 建立背景工作角色

您現在將建立背景工作角色來處理所提交的訂單。本範例使用 **Worker Role with Service Bus Queue** Visual Studio 專案範本。首先，使用 Visual Studio 中的 [伺服器總管] 取得必要認證。

1. 請確定您已依使用 Visual Studio 伺服器總管管理命名空間和訊息實體一節中所述，將 Visual Studio 連接到您的 Azure 帳戶 。

2.  在 Visual Studio 的 [方案總管] 中，於 [MultiTierApp] 專案下的 [角色] 資料夾上按一下滑鼠右鍵。

3.  按一下 [加入]，然後按一下 [新的背景工作角色專案]。[加入新的角色專案] 對話方塊隨即出現。

	![][26]

4.  在 [加入新的角色專案] 對話方塊中，按一下 [Worker Role with Service Bus Queue]，如下圖所示：

	![][23]

5.  在 [名稱] 方塊中，將專案命名為 **OrderProcessingRole**。然後按一下 [新增]。

6.  在 [伺服器總管] 中，於您的服務命名空間的名稱上按一下滑鼠右鍵，再按一下 [屬性]。在 Visual Studio [**屬性**] 窗格中，第一個項目會包含已填入命名空間端點 (其含必要的授權認證) 的連接字串。例如，請參閱下圖。按兩下 [ConnectionString]，再按 **Ctrl+C**，將此字串複製到剪貼簿。

	![][24]

7.  在 [方案總管] 中，請以滑鼠右鍵按一下您在步驟 5 中所建立的 **OrderProcessingRole** (請確定您是在 [**角色**] 下，而不是類別下，以滑鼠右鍵按一下 **OrderProcessingRole**)。然後按一下 [屬性]。

8.  在 [**屬性**] 對話方塊的 [**設定**] 索引標籤中，按一下 **Microsoft.ServiceBus.ConnectionString** 的 [**值**] 方塊內部，然後貼上您在步驟 6 中複製的端點值。

	![][25]

9.  建立 **OnlineOrder**，以代表您從佇列處理它們時的順序。您可以重複使用已建立的類別。在 [方案總管] 中，於 [OrderProcessingRole] 專案上按一下滑鼠右鍵 (在專案而不是角色上按一下滑鼠右鍵)。按一下 [加入]，然後按一下 [現有項目]。

10. 瀏覽至 **FrontendWebRole\\Models** 的子資料夾，並按兩下 [OnlineOrder.cs]，以將它新增至這個專案。

11. 在 WorkerRole.cs 中，將 **WorkerRole.cs** 中的 **QueueName** 變數值從 `"ProcessingQueue"` 取代為 `"OrdersQueue"`，如下列程式碼所示：

		// The name of your queue
		const string QueueName = "OrdersQueue";

12. 在 WorkerRole.cs 檔案頂端新增下列 using 陳述式：

		using FrontendWebRole.Models;

13. 在 `Run()` 函式的 `OnMessage` 呼叫內，於 `try` 子句內加入下列程式碼：

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

14. 您已完成應用程式。您可以測試完整的應用程式，方法是以滑鼠右鍵按一下 [方案總管] 中的 MultiTierApp 專案、選取 [**設定為啟始專案**]，然後按下 F5。請注意，訊息計數不會增加，因為背景工作角色會處理佇列中的項目，並將它們標示為完成。您可以檢視 Azure 計算模擬器 UI，來查看背景工作角色的追蹤輸出。作法為在工作列的通知區中的計算模擬器圖示上按一下滑鼠右鍵，並選取 [Show Compute Emulator UI]。

    ![][19]

    ![][20]

## 後續步驟  

若要深入了解服務匯流排，請參閱下列資源：

* [Azure 服務匯流排][sbmsdn]  
* [服務匯流排服務頁面][sbwacom]  
* [如何使用服務匯流排佇列][sbwacomqhowto]  

若要深入了解多層式案例，或了解如何將應用程式部署至雲端服務，請參閱：

* [使用儲存體資料表、佇列與 Blob 的 .NET 多層式應用程式][mutitierstorage]  

您可能想要在 Azure 網站而非 Azure 雲端服務中實作多層式應用程式的前端。若要深入了解網站與雲端服務之間的差異，請參閱 [Azure 執行模型][executionmodels] (英文)。

若要以標準 Web 專案而非雲端服務 Web 角色的形式實作您在本教學課程中建立的應用程式，請在遵循本教學課程中的步驟時注意下列差異：

1. 建立專案時，請選擇 [**Web**] 類別中的 **ASP.NET MVC Web Application** 專案範本，而不是 [**雲端**] 類別中的 [**雲端服務**] 範本。然後，遵循建立 MVC 應用程式的相同指示，直到**雲端組態管理員**一節。

2. 建立背景工作角色時，請在新的個別方案中建立，類似於 Web 角色的原始指示。不過，您現在只在雲端服務專案中建立背景工作角色。然後，遵循建立背景工作角色的相同指示。

3. 您可以個別測試前端與後端，也可以同時在不同 Visual Studio 執行個體中執行這兩者。

若要了解如何將前端部署至 Azure 網站，請參閱[將 ASP.NET Web 應用程式部署至 Azure 網站](http://azure.microsoft.com/develop/net/tutorials/get-started/)。若要了解如何將後端部署至 Azure 雲端服務，請參閱[使用儲存體資料表、佇列與 Blob 的 .NET 多層式應用程式][mutitierstorage]。


  [0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: http://msdn.microsoft.com/library/hh767287.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [取得工具和 SDK (英文)]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png



  [Azure Management Portal]: http://manage.windowsazure.com
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [32]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [33]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [35]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/multi-web-45.png
  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx
  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: /develop/net/how-to-guides/service-bus-queues/
  [mutitierstorage]: /develop/net/tutorials/multi-tier-web-site/1-overview/
  [executionmodels]: http://azure.microsoft.com/develop/net/fundamentals/compute/
 

<!---HONumber=July15_HO2-->