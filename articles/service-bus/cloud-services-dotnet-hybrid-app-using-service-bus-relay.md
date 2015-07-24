<properties
	pageTitle="混合式內部部署/雲端應用程式 (.NET) - Azure"
	description="了解如何使用 Azure Service Bus Relay 建立 .NET 內部部署/雲端混合式應用程式。"
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
	ms.topic="article" 
	ms.date="06/02/2015"
	ms.author="sethm"/>






#使用服務匯流排轉送的 .NET 內部部署/雲端混合式應用程式

##簡介

使用 Visual Studio 2013 和免費的 Azure SDK for .NET，可輕鬆在 Microsoft Azure 開發混合式雲端應用程式。本指南假設您從未使用過 Azure。不用 30 分鐘，您就會獲得一個使用多個 Azure 資源，於雲端上啟動並執行的應用程式。

您將了解：

-   如何建立 Web 服務或調整現有的 Web 服務，以供 Web 方案使用。
-   如何使用 Azure 服務匯流排轉送，以在 Azure 應用程式與其他位置託管的 Web 服務之間共用資料。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

##服務匯流排轉送如何協助混合式方案

商業方案通常由下列項目組成：為了應付全新的獨特商業需求而撰寫的自訂程式碼，以及既有方案和系統提供的現有功能。

眾多方案架構爭相開始使用雲端，以期能夠更輕鬆地處理擴充需求並降低操作成本。在這麼做之後，它們發現想要運用作為其方案建置組塊的現有服務資產是在公司防火牆內，無法供雲端方案輕易存取。許多內部服務並不是以可輕易在公司網路邊緣公開的方式建置或主控。

「服務匯流排轉送」是針對採取現有 Windows Communication Foundation (WCF) Web 服務，並使那些服務可供公司週邊外之方案安全存取的使用案例而設計的，不需要進行會干擾公司網路基礎結構的變更。這類服務匯流排轉送服務仍然裝載在其現有的環境，但它們會委派接聽連入工作階段和雲端裝載的服務匯流排的要求。服務匯流排也可使用[共用存取簽章](https://msdn.microsoft.com/library/dn170478.aspx) (SAS) 驗證，保護這些服務免受未經授權的存取。

##方案案例

在本教學課程中，您將建立 ASP.NET MVC 網站，讓您可在產品庫存頁面上看到產品清單。

![][0]

本教學課程假設您有現有內部部署系統中的產品資訊，並使用服務匯流排轉送來連接該系統。這項模擬是由簡單主控台應用程式中執行的 Web 服務來進行，並由記憶體內的產品集支援。您將能夠在自己的電腦上執行這個主控台應用程式，並將 Web 角色部署至 Azure。在這麼做之後，您將看到在 Azure 資料中心執行的 Web 角色將確實呼叫至電腦，即使電腦幾乎確定會位於至少一個防火牆和網路位址轉譯 (NAT) 層後面也一樣。

下列是已完成之 Web 應用程式的開始頁面螢幕擷取畫面。

![][1]

##設定開發環境

在開始開發 Azure 應用程式之前，請取得工具，並設定開發環境：

1.  若要安裝 Azure SDK for .NET，請按一下底下連結：

    [取得工具和 SDK (英文)][]

2. 	按一下您所使用 Visual Studio 版本的 [**安裝 SDK**]。本教學課程中的步驟使用 Visual Studio 2013：

	![][42]

4.  當系統提示您執行或儲存安裝程式時，按一下 [**執行**]：

    ![][2]

5.  在 **Web Platform Installer** 中，按一下 [**安裝**] 並繼續進行安裝：

    ![][3]

6.  完成安裝後，您將具有開始進行開發所需的一切。SDK 包含可讓您在 Visual Studio 輕易開發 Azure 應用程式的工具。如果您未安裝 Visual Studio，則 SDK 也會安裝免費的 Visual Studio Express。

## 建立服務命名空間

若要開始在 Azure 中使用服務匯流排功能，首先必須建立服務命名空間。命名空間提供範圍容器，可在應用程式內定址服務匯流排資源。

您可以使用 [Azure 管理入口網站][]或 Visual Studio 伺服器總管，來管理命名空間和服務匯流排訊息實體，但是只能從入口網站內建立新的命名空間。

### 若要使用入口網站建立服務命名空間：

1.  登入 [Azure 管理入口網站][]。

2.  在管理入口網站的左方瀏覽窗格中，按一下 [**服務匯流排**]。

3.  在管理入口網站的下方窗格中，按一下 [**建立**]。![][5]

4.  在 [Add a new namespace] 對話方塊中，輸入命名空間名稱。系統會立即檢查此名稱是否可用。![][6]

5.  確定命名空間名稱可用之後，請選擇要代管命名空間的國家或區域 (必須使用您要部署計算資源的相同國家/區域)。

    重要事項：請挑選您想要選擇來部署應用程式的**相同區域**。這樣可以獲得最佳效能。

6.	讓對話方塊中的其他欄位保留其預設值 ([**傳訊**] 和 [**標準層**])，然後按一下核取記號。此時系統會建立並啟用命名空間。系統為帳戶提供資源時，您可能需要等幾分鐘。

	![][38]

您所建立的命名空間會出現在管理入口網站，不過啟動能可需要花一些時間。等到狀態變成 [作用中] 之後再繼續。

## 取得命名空間的預設管理認證

為了在新的命名空間上執行管理作業，例如建立訊息實體，您必須取得命名空間的認證。

1.  在主視窗中，按一下服務命名空間的名稱。

	![][39]


2.  按一下 [連線資訊]。

	![][40]


3.  在 [**存取連線資訊**] 窗格中，尋找包含 SAS 金鑰和金鑰名稱的連接字串。

	![][45]


4.  記下這些認證，或將它們複製到剪貼簿。

## 建立內部部署伺服器

首先，您將建置 (模擬) 內部部署產品目錄系統。此作業相當簡單；您可將它看成是呈現實際內部部署產品目錄系統，此系統具有我們正在嘗試整合的完整服務面。

此專案啟動為 Visual Studio 主控台應用程式。專案會使用服務匯流排 NuGet 套件，來併入服務匯流排程式庫和組態設定。NuGet Visual Studio 擴充功能可讓您輕易地安裝和更新 Visual Studio 和 Visual Studio Express 中的程式庫和工具。服務匯流排 NuGet 套件為取得服務匯流排 API，並設定具有所有服務匯流排相依性的應用程式的最容易方式。如需使用 NuGet 和服務匯流排套件的詳細資料，請參閱[使用 NuGet 服務匯流排套件][] (英文)。

### 建立專案

1.  使用系統管理員權限，啟動 Microsoft Visual Studio 2013 或 Microsoft Visual Studio Express。若要以系統管理員權限啟動 Visual Studio，請在 **Microsoft Visual Studio 2013 (或 Microsoft Visual Studio Express)** 上按一下滑鼠右鍵，然後按一下 [以系統管理員身份執行]。

2.  在 Visual Studio 的 [檔案] 功能表，按一下 [新增]，然後按一下 [專案]。

    ![][10]

3.  從 [已安裝的範本] 的 [Visual C#] 下，按一下 [主控台應用程式]。在 [名稱] 方塊中，鍵入名稱 **ProductsServer**：

    ![][11]

4.  按一下 [確定] 以建立 **ProductsServer** 專案。

5.  在 [方案總管] 的 **ProductsServer** 上按一下滑鼠右鍵，然後按一下 [屬性]。

6.  按一下左側的 [**應用程式**] 索引標籤，然後確定 **.NET Framework 4** 或 **.NET Framework 4.5** 出現在 [**目標 Framework:**] 下拉式清單中。如果未出現，請從下拉式清單中選取它，然後在系統提示您重新載入專案時按一下 [是]。

    ![][12]

7.  如果已安裝 Visual Studio 的 NuGet 套件管理員，請跳至下一個步驟。否則，請造訪 [NuGet][] (英文)，然後按一下 [[安裝 NuGet]](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) (英文)。按照提示安裝 NuGet 套件管理員，然後重新啟動 Visual Studio。

7.  在 [方案總管] 的 [參考] 上按一下滑鼠右鍵，然後按一下 [Manage NuGet Packages]。

8.  在 NuGet 對話方塊的左欄中，按一下 [線上]。

9. 	在右欄中，按一下 [**搜尋**] 方塊、鍵入 "**服務匯流排**"，然後選取 [**Microsoft Azure 服務匯流排**] 項目。按一下 [安裝] 完成安裝作業，然後關閉此對話方塊。

    ![][13]

    請注意，現在會參考必要的用戶端組件。

9.  新增產品連絡人的新類別。在 [方案總管] 的 **ProductsServer** 專案上按一下滑鼠右鍵、按一下 [新增]，然後按一下 [類別]。

    ![][14]

10. 在 [名稱] 方塊中，鍵入名稱 **ProductsContract.cs**。然後按一下 [新增]。

11. 在 **ProductsContract.cs** 中，將命名空間定義取代為下列程式碼，以定義服務的連絡人：

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties
            public class ProductData
            {
                [DataMember]
                public string Id { get; set; }
                [DataMember]
                public string Name { get; set; }
                [DataMember]
                public string Quantity { get; set; }
            }

            // Define the service contract.
            [ServiceContract]
            interface IProducts
            {
                [OperationContract]
                IList<ProductData> GetProducts();

            }

            interface IProductsChannel : IProducts, IClientChannel
            {
            }
        }

12. 在 Program.cs 中，將命名空間定義取代為下列程式碼，為它新增設定檔服務和主機：

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface
            class ProductsService : IProducts
            {

                // Populate array of products for display on Website
                ProductData[] products =
                    new []
                        {
                            new ProductData{ Id = "1", Name = "Rock",
                                             Quantity = "1"},
                            new ProductData{ Id = "2", Name = "Paper",
                                             Quantity = "3"},
                            new ProductData{ Id = "3", Name = "Scissors",
                                             Quantity = "5"},
                            new ProductData{ Id = "4", Name = "Well",
                                             Quantity = "2500"},
                        };

                // Display a message in the service console application
                // when the list of products is retrieved
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application
                static void Main(string[] args)
                {
                    var sh = new ServiceHost(typeof(ProductsService));
                    sh.Open();

                    Console.WriteLine("Press ENTER to close");
                    Console.ReadLine();

                    sh.Close();
                }
            }
        }

13. 在 [**方案總管**] 中，按兩下 **App.config** 檔案，以在 **Visual Studio** 編輯器中開啟它。將 **&lt;system.ServiceModel&gt;** 的內容取代為下列 XML 程式碼。務必要將 *yourServiceNamespace* 取代為服務命名空間的名稱，並將 *yourKey* 取代為您先前從 Azure 管理入口網站擷取到的 SAS 金鑰：

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </bindingExtensions>
          </extensions>
          <services>
             <service name="ProductsServer.ProductsService">
               <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts"
        behaviorConfiguration="products"/>
             </service>
          </services>
          <behaviors>
             <endpointBehaviors>
               <behavior name="products">
                 <transportClientEndpointBehavior>
                    <tokenProvider>
                       <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14. 按 **F6**，或從 [建置] 功能表中按一下 [建置方案] 來建置應用程式，以驗證您的工作到目前為止是否正確無誤。

## 建立 ASP.NET MVC 應用程式

在本節中，您將建置簡單的 ASP.NET 應用程式，來顯示從產品服務擷取的資料。

### 建立專案

1.  確定 Visual Studio 是以系統管理員權限來執行。如果不是，則若要以系統管理員權限啟動 Visual Studio，請在 **Microsoft Visual Studio 2013 (或 Microsoft Visual Studio Express)** 上按一下滑鼠右鍵，然後按一下 [以系統管理員身份執行]。本指南稍後討論的 Microsoft Azure 運算模擬器需要您以系統管理員權限啟動 Visual Studio。

2.  在 Visual Studio 的 [檔案] 功能表，按一下 [新增]，然後按一下 [專案]。

3.  從 [已安裝的範本] 的 [Visual C#] 下，按一下 [ASP.NET Web 應用程式]。將專案命名為 **ProductsPortal**。然後按一下 [確定]。

    ![][15]

4.  從 [選取範本] 清單中，按一下 [MVC]，然後按一下 [確定]。

    ![][16]

5.  在 [方案總管] 中，於 [模型] 上按一下滑鼠右鍵、按一下 [新增]，再按一下 [類別]。在 [名稱] 方塊中，鍵入名稱 **Product.cs**。然後按一下 [新增]。

    ![][17]

### 修改 Web 應用程式

1.  在 Visual Studio 的 Product.cs 檔案中，將現有的命名空間定義取代為下列程式碼：

        // Declare properties for the products inventory
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  在 Visual Studio 的 HomeController.cs 檔案中，將現有的命名空間定義取代為下列程式碼：

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product>
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  在 [方案總管] 中，展開 Views\\Shared：

    ![][18]

4.  接著，按兩下 _Layout.cshtml，以在 Visual Studio 編輯器中開啟它。

5.  將所有出現的 **My ASP.NET Application** 變更為 **LITWARE's Products**。

6. 移除 [首頁]、[關於] 和 [連絡人] 連結。刪除反白顯示的程式碼：

	![][41]

7.  在 [方案總管] 中，展開 Views\\Home：

    ![][20]

8.  按兩下 Index.cshtml，以在 Visual Studio 編輯器中開啟它。將檔案的整個內容取代為下列程式碼：

		@model IEnumerable<ProductsWeb.Models.Product>

		@{
    		ViewBag.Title = "Index";
		}

		<h2>Prod Inventory</h2>

		<table>
    		<tr>
        		<th>
            		@Html.DisplayNameFor(model => model.Name)
        		</th>
                <th></th>
        		<th>
            		@Html.DisplayNameFor(model => model.Quantity)
        		</th>
    		</tr>

		@foreach (var item in Model) {
    		<tr>
        		<td>
            		@Html.DisplayFor(modelItem => item.Name)
        		</td>
        		<td>
            		@Html.DisplayFor(modelItem => item.Quantity)
        		</td>
    		</tr>
		}

		</table>


9.  若要驗證您的工作到目前為止是否正確無誤，您可以按 **F6** 或 **Ctrl+Shift+B** 來建置專案。


### 在本機執行您的應用程式

執行應用程式以驗證它是否能正常運作。

1.  確定 **ProductsPortal** 為作用中專案。在 [方案總管] 的專案名稱上按一下滑鼠右鍵，然後選取 [設定為啟始專案]。
2.  在 **Visual Studio** 內按 **F5**。
3.  您的應用程式應該就會出現在瀏覽器中並正在執行：

    ![][21]

## 將應用程式準備好部署到 Azure

您可以將您的應用程式部署到 Azure 雲端服務或 Azure 網站。若要深入了解網站與雲端服務之間的差異，請參閱 [Azure 執行模型][executionmodels] (英文)。若要了解如何將應用程式部署至 Azure 網站，請參閱[將 ASP.NET Web 應用程式部署至 Azure 網站](http://azure.microsoft.com/develop/net/tutorials/get-started/) (英文)。本節包含將應用程式部署至 Azure 雲端服務的詳細步驟。

若要將應用程式部署至雲端服務，您需要新增雲端服務專案部署專案至方案。部署專案包含在雲端適當地執行應用程式所需的組態資訊。

1.  若要使應用程式可以部署至雲端，請在 [**方案總管**] 的 **ProductsPortal** 專案上按一下滑鼠右鍵，然後依序按一下 [**轉換**] 和 [**Microsoft Azure 雲端服務專案**]。

    ![][22]

2.  若要測試應用程式，請按 **F5**。

3.  這將啟動 Azure 運算模擬器。運算模擬器會使用本機電腦來模擬 Azure 中執行的應用程式。您可以查看系統匣來確認模擬器是否已啟動：

       ![][23]

4.  瀏覽器仍將顯示本機執行的應用程式，而且它的外觀和運作方式，將和您先前將它當作一般 ASP.NET MVC 4 應用程式執行時相同。

## 組合在一起

下一步是利用 ASP.NET MVC 應用程式連接內部部署產品伺服器。

1.  請在 Visual Studio 重新開啟您在＜建立 ASP.NET MVC 應用程式＞一節中建立的 **ProductsPortal** 專案 (如果尚未開啟)。

2.  類似於＜建立內部部署伺服器＞一節中的步驟，將 NuGet 套件新增至 References 專案。在 [方案總管] 中，以滑鼠右鍵按一下 [喜好設定]，然後按一下 [Manage NuGet Packages]。

3.  搜尋「服務匯流排」並選取 [**Microsoft Azure 服務匯流排**] 項目。然後完成安裝並關閉此對話方塊。

4.  在 [方案總管] 的 **ProductsPortal** 專案上按一下滑鼠右鍵，再按一下 [新增]，然後按一下 [現有項目]。

5.  從 **ProductsServer** 主控台專案導覽至 **ProductsContract.cs** 檔案。按一下以反白顯示 ProductsContract.cs。按一下 [**新增**] 旁邊的向下箭頭，然後按一下 [**加入做為連結**]。

	![][24]

6.  現在會在 Visual Studio 編輯器中開啟 **HomeController.cs** 檔案，並將命名空間定義取代為下列程式碼。務必要將 *yourServiceNamespace* 取代為您的服務命名空間名稱，並將 *yourKey* 取代為您的 SAS 金鑰。這可讓用戶端呼叫內部部署服務，並傳回呼叫結果。

            namespace ProductsWeb.Controllers
            {
                using System.Linq;
                using System.ServiceModel;
                using System.Web.Mvc;
                using Microsoft.ServiceBus;
                using Models;
                using ProductsServer;

                public class HomeController : Controller
                {
                    // Declare the channel factory
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication
                        channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                            "sb://yourServiceNamespace.servicebus.windows.net/products");
                        channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                            TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                                "RootManageSharedAccessKey", "yourKey") });
                    }

                    public ActionResult Index()
                    {
                        using (IProductsChannel channel = channelFactory.CreateChannel())
                        {
                            // Return a view of the products inventory
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name,
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }
7.  在 [方案總管] 的 **ProductsPortal** 方案上按一下滑鼠右鍵、按一下 [新增]，然後按一下 [現有專案]。

8.  導覽至 **ProductsServer** 專案，然後按兩下 **ProductsServer.csproj** 方案檔來新增它。

9.  在 [方案總管] 的 **ProductsPortal** 方案上按一下滑鼠右鍵，然後按一下 [屬性]。

10. 在左側，按一下 [啟始專案]。在右側，按一下 [多個啟始專案]。確定 **ProductsServer**、**ProductsPortal.Azure** 和 **ProductsPortal** 依該順序出現，同時將 **Start** 設為 **ProductsServer** 和 **ProductsPortal.Azure** 的動作，並將 **None** 設為 **ProductsPortal** 的動作。例如：

      ![][25]

11. 仍是在 [屬性] 對話方塊中，按一下左側的 **ProjectDependencies**。

12. 在 [專案] 下拉式清單中，按一下 **ProductsServer**。確定已取消核取 **ProductsPortal**，並已核取 **ProductsPortal.Azure**。然後按一下 [確定]：

    ![][26]

## 執行應用程式

1.  從 Visual Studio 的 [檔案] 功能表中，按一下 [全部儲存]。

2.  按 **F5** 以建置並執行應用程式。內部部署伺服器 (**ProductsServer** 主控台應用程式) 應該會第一個啟動，然後 **ProductsWeb** 應用程式應該會在瀏覽器視窗中啟動，如下面的螢幕擷取畫面所示。此時，您將看到從產品服務內部部署系統擷取的產品庫存清單資料。

    ![][1]

## 將應用程式部署至 Azure

1.  在 [**方案總管**] 的 **ProductsPortal** 專案上按一下滑鼠右鍵，然後按一下 [**發佈到 Azure**]。

2.  您可能必須登入，才能看到所有訂用帳戶。

    按一下 [Sign in to see more subscriptions]：

    ![][27]

3.  使用 Microsoft 帳戶登入。


8.  按 [下一步]。如果訂用帳戶未包含任何託管服務，系統會要求您建立一個。託管服務會充當 Azure 訂用帳戶內應用程式的容器。輸入應用程式的識別名稱，並選擇應該最佳化其應用程式的區域。(從這個區域存取它的使用者預期會有更快的載入時間。)

9.  選取要將應用程式發佈至其中的託管服務。其餘設定請保留如下所示的預設值。按 [下一步]：

    ![][33]

10. 在最後一個頁面上，按一下 [發佈] 以啟動部署程序：

    ![][34]

這將需要大約 5-7 分鐘的時間。因為這是您第一次發佈，所以 Azure 會佈建虛擬機器 (VM)、執行安全性強化、在 VM 上建立 Web 角色來主控應用程式、將程式碼部署至該 Web 角色，最後並設定負載平衡器及網路，以便應用程式可供大眾使用。

11. 發佈期間，您將能夠在 [Azure 活動記錄檔] 視窗中監視活動，此視窗通常會固定在 Visual Studio 或 Visual Web Developer 底部：

    ![][35]

12. 部署完成時，您可以按一下監視視窗中的 [**網站 URL**] 連結來檢視網站。

    ![][36]

    網站需要仰賴內部部署伺服器，因此您必須在本機執行 **ProductsServer** 應用程式，網站才能正常運作。在雲端網站上執行要求時，您將看到要求進入內部部署主控台應用程式，如下列螢幕擷取畫面中所顯示的 "GetProducts called" 輸出所指出。

    ![][37]

若要深入了解網站與雲端服務之間的差異，請參閱 [Azure 執行模型][executionmodels] (英文)。

## 停止並刪除您的應用程式

在部署應用程式之後，您可能想要停用它，以便可以在每月 750 小時 (以每月 31 天來計算) 免費的伺服器時間內建置並部署其他應用程式。

Azure 會就每小時伺服器時間所使用的 Web 角色執行個體數進行收費。一旦部署應用程式，即會耗用伺服器時間，即使執行個體不在執行中，並處於停止狀態也一樣。免費帳戶有每月 750 小時 (以每月 31 天來計算) 的專用虛擬機器伺服器時間可供主控這些 Web 角色執行個體。

下列步驟示範如何停止並刪除應用程式。

1.  登入 [Azure 管理入口網站]，按一下 [**雲端服務**]，然後按一下服務名稱。

2.  按一下 [儀表板] 索引標籤，然後按一下 [停止] 以暫停應用程式。只需按一下 [啟動]，即可重新啟動它。按一下 [刪除]，可從 Azure 完全移除應用程式，移除後便再也無法還原。

	![][43]

## 後續步驟  

若要深入了解服務匯流排，請參閱下列資源：

* [Azure 服務匯流排][sbmsdn]  
* [服務匯流排作法][sbwacom]  
* [如何使用服務匯流排佇列][sbwacomqhowto]  


  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [取得工具和 SDK (英文)]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png


  [Azure 管理入口網站]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png



  [使用 NuGet 服務匯流排套件]: http://go.microsoft.com/fwlink/?LinkId=234589
  [10]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg

  [20]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png


  [30]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [45]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-45.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx
  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: /develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://azure.microsoft.com/develop/net/fundamentals/compute/
 

<!---HONumber=July15_HO2-->