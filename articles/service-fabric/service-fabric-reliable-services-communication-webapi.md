<properties
   pageTitle="Service Fabric Web API 服務與 OWIN 自我裝載 | Microsoft Azure"
   description="本 Service Fabric 文章解釋如何使用 ASP.NET Web API 與 OWIN 自我裝載在可靠服務中實作服務通訊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="05/18/2015"
   ms.author="vturecek"/>

# 開始使用 Microsoft Azure Service Fabric Web API 服務與 OWIN 自我裝載

Service Fabric 讓您能決定您的服務與使用者以及服務彼此之間如何進行通訊。本教學課程著重於使用 ASP.NET Web API 與 OWIN 自我裝載在 Service Fabric 的*可靠服務* API 中實作服務通訊。我們會深入了解到*可靠服務*隨插即用的通訊 API，並逐步示範如何為您的服務設定自訂通訊接聽程式，且使用 Web API 做為範例。若要查看 Web API 通訊接聽程式的完整範例，請參閱 [GitHub 上的 Service Fabric WebApplication 範例](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/WebApplication)。


## Service Fabric 中的 Web API 簡介

ASP.NET Web API 是在 .NET Framework 建置 HTTP API 的常用且功能強大的架構。如果您尚不熟悉 Web API，請前往 [www.asp.net/webapi](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) 以深入了解它。

Service Fabric 中的 Web API 是您熟知而且喜愛的相同 ASP.NET Web API。不同之處在於您如何*裝載* Web API 應用程式 (提示：您不會使用 IIS)。若要進一步了解差異，讓我們將它分成兩個部分：

 1. Web API 應用程式 (您的控制器、模型等)
 2. 主機 (Web 伺服器，通常是 IIS)

Web API 應用程式本身不會在此處變更，它與您過去撰寫的 Web API 應用程式並無不同，您應該能夠直接搬移大部分的應用程式程式碼。如果您過去裝載在 IIS 上，那麼裝載應用程式可能會與您過去習慣的稍有不同。但我們進入裝載部分之前，讓我們從較熟悉的部分開始：Web API 應用程式。


## 設定 Web API 應用程式

先在 Visual Studio 2015 中建立新的無狀態服務：

![](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

這讓我們有空的無狀態服務，它會裝載 Web API 應用程式。我們將從頭設定應用程式，看看它如何全部放在一起。

第一個步驟是提取一些 Web API 的 NuGet 封裝。我們想要使用的封裝是 **Microsoft.AspNet.WebApi.OwinSelfHost**。此封裝包含所有必要的 Web API 封裝和*主機*封裝，這在稍後會很重要。

![](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

安裝封裝後，我們就可以馬上開始建置出基本的 Web API 專案結構。如果您已使用 Web API，專案結構看起來應該很熟悉。從建立基本的 Web API 目錄開始：

 + App_Start
 + Controllers
 + Models

在 App_Start 目錄中加入基本的 Web API 組態類別：

 + FormatterConfig.cs

```csharp

namespace WebApi
{
    using System.Net.Http.Formatting;

    public static class FormatterConfig
    {
        public static void ConfigureFormatters(MediaTypeFormatterCollection formatters)
        {
        }
    }
}

```

 + RouteConfig.cs

```csharp

namespace WebApi
{
    using System.Web.Http;

    public static class RouteConfig
    {
        public static void RegisterRoutes(HttpRouteCollection routes)
        {
            routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { controller = "Default", id = RouteParameter.Optional }
                );
        }
    }
}

```

在 Controllers 目錄中加入預設控制器：

 + DefaultController.cs

```csharp

namespace WebApi.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    public class DefaultController : ApiController
    {
        // GET api/values
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5
        public void Delete(int id)
        {
        }
    }
}

```

最後，在專案根目錄加入 Startup 類別以註冊路由、格式器及任何其他組態設定。這也是 Web API 插入至*主機*的位置，稍後我們將再重返此處。在設定 Startup 類別時，為 Startup 類別建立稱為 *IOwinAppBuilder* 的介面，定義 Configuration 方法。雖然技術上不需要 Web API 即可運作，它能讓稍後使用 Startup 類別時更有彈性。

 + Startup.cs

```csharp

namespace WebApi
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            FormatterConfig.ConfigureFormatters(config.Formatters);
            RouteConfig.RegisterRoutes(config.Routes);

            appBuilder.UseWebApi(config);
        }
    }
}

```

 + IOwinAppBuilder.cs

```csharp

namespace WebApi
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

應用程式部分就這樣。現在我們剛設定好基本的 Web API 專案配置。與您過去可能已撰寫的 Web API 專案或基本的 Web API 範本相比，到目前為止它不應該看起來有太多不同。您的商務邏輯如往常般在控制器和模型中運作。

現在我們怎麼辦才能實際執行裝載？


## 服務裝載

在 Service Fabric 中，您的服務會在服務主機處理序中執行，這是執行您的服務程式碼的可執行檔。使用可靠服務 API 撰寫服務時，以及其實您在 .NET 中的 Service Fabric 上撰寫服務的大多數情況下，您的服務專案會編譯為 .EXE，其註冊您的服務類型並執行程式碼。事實上，如果您在無狀態服務專案中開啟 **Program.cs**，您應該會看到：

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType(Service.ServiceTypeName, typeof(Service));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e);
            throw;
        }
    }
}

```

如果看起來疑似主控台應用程式的進入點，這是因為它是：

![](media/service-fabric-reliable-services-communication-webapi/webapi-projectproperties.png)

關於服務主機處理序和服務註冊的詳細資料超出本文的範圍，但現在知道**您的服務程式碼是在自己的處理序中執行**是很重要的。

## 自我裝載 Web API 與 OWIN 主機

假設您的 Web API 應用程式程式碼裝載在自己的處理序中，我們如何將它連結到 Web 伺服器？ 輸入 [OWIN](http://owin.org/)。OWIN 只是 .NET Web 應用程式和 Web 伺服器之間的合約。傳統上使用 ASP.NET (直到 MVC 5)，Web 應用程式已透過 System.Web 緊密結合至 IIS。不過，Web API 會實作 OWIN，可讓您撰寫獨立於裝載它的 Web 伺服器的 Web 應用程式。這可讓您使用自我裝載 OWIN Web 伺服器，您可以在自己的處理序中啟動，完全符合我們先前所提到的 Service Fabric 裝載模型。

在本文中，我們將使用 Katana 做為 Web API 應用程式的 OWIN 主機。Katana 是開放原始碼的 OWIN 主機實作。

> [AZURE.NOTE]若要深入了解 Katana，請前往 [Katana 網站](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana)，如需如何使用 Katana 自我裝載 Web API 的快速概觀，請參閱這篇文章以了解如何[使用 OWIN 自我裝載 ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api)。


## 設定 Web 伺服器

可靠服務 API 為您的商務邏輯提供兩個進入點：

 + 開放式的進入點方法，您可以在這裡開始執行任何工作負載，這主要是供長時間執行的運算工作負載使用：

```csharp

protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}

```

 + 通訊進入點，您可以在這裡插入選擇的通訊堆疊：

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

Web 伺服器和您未來可能使用的任何其他通訊堆疊，例如 WebSockets，應該使用 ICommunicationListener 介面來正確地與系統整合。這樣做的原因會在接下來的步驟中變得明顯。

先建立一個稱為 OwinCommunicationListener 的類別，其實作 ICommunicationListener：

 + OwinCommunicationListener.cs：

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services;

    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}

```

ICommunicationListener 介面提供 4 個方法來管理服務的通訊接聽程式：

 + **Initialize**：這通常是您設定服務將接聽位址的地方。對於 Web 伺服器而言，這是 URL 設定位置。
 + **OpenAsync**：開始接聽要求。
 + **CloseAsync**：停止接聽要求，完成任何進行中的要求，並正常關機。
 + **Abort**：取消所有項目，並立即停止。

若要開始，請加入 URL 路徑前置詞的私用類別成員和稍早建立的 **Startup** 類別。這些會透過建構函式初始化，並在稍後設定接聽 URL 時使用。另外也加入私用類別成員，以儲存分別在初始化期間和稍後啟動伺服器時建立的接聽位址和伺服器控制代碼。

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup)
    {
        this.startup = startup;
        this.appRoot = appRoot;
    }

    ...

```

### Initialize

Web 伺服器 URL 將在此處設定。若要這樣做，您需要一些資訊：

 + **URL 路徑前置詞**。雖然是選擇性的，但最好現在就設定，以便您能安全地在應用程式中裝載多個 Web 服務。
 + **連接埠**。

我們抓取 Web 伺服器的連接埠之前，必須了解 Service Fabric 提供一個應用程式層，做為您的應用程式與其執行所在之基礎作業系統之間的緩衝區。因此，Service Fabric 讓您能夠設定服務的端點。Service Fabric 會負責確定端點可供服務使用，因此您不需要在基礎作業系統環境自行設定。這可讓您輕鬆地在不同的環境裝載 Service Fabric 應用程式，而不必對應用程式進行任何變更 (例如，您可以在 Azure 或您自己的資料中心裝載相同的應用程式)。

在 PackageRoot\\ServiceManifest.xml 中設定 HTTP 端點：

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

這個步驟很重要，因為服務主機處理序在限制認證下執行 (Windows 上為網路服務)，這表示您的服務沒有自行設定 HTTP 端點的存取權。藉由使用端點組態，Service Fabric 會知道要為服務將接聽的 URL 設定適當的 ACL，同時提供標準位置來設定端點。

回到 OwinCommunicationListener.cs 中，在 Initialize 方法中取得端點資訊以取得連接埠。建立服務將接聽的 URL，並將它儲存至先前建立的類別成員變數。這將用於 OpenAsync 中以啟動 Web 伺服器。

```csharp

public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
{
    EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = String.Format(
        CultureInfo.InvariantCulture,
        "http://+:{0}/{1}",
        port,
        String.IsNullOrWhiteSpace(this.appRoot)
            ? String.Empty
            : this.appRoot.TrimEnd('/') + '/');
}

```

請注意這裡用 "http://+"。這是為了確定 Web 伺服器正在接聽所有可用的位址，包括 localhost、FQDN，以及電腦的 IP。

### OpenAsync

OpenAsync 在 Initialize 之後由平台呼叫。您在這裡使用 Initialize 中建立的位址，以開啟 Web 伺服器。

實作 OpenAsync 是 Web 伺服器 (或任何通訊堆疊) 之所以實作為 ICommunicationListener，而非直接從服務中的 RunAsync() 開啟它，最重要的原因之一。OpenAsync 的傳回值是 Web 伺服器正在接聽的位址。當傳回這個位址給系統時，它會向服務註冊位址。Service Fabric 會提供一個 API，讓用戶端或其他服務依服務名稱來要求這個位址。這很重要，因為服務位址並非靜態，因為服務會在叢集中移動，以達到資源平衡與可用性的目的。這是可讓用戶端解析服務接聽位址的機制。

記住這一點之後，OpenAsync 會啟動 Web 伺服器，並傳回它接聽的位址。請注意它會接聽 "http://+"，但傳回位址之前，"+" 會取代為目前所在節點的 IP 或 FQDN。這是因為方法傳回的這個位址是向系統註冊的位址，當用戶端和其他服務要求提供服務的位址時會看到此位址。用戶端若要能正確地連接到它，它們需要位址中有實際的 IP 或 FQDN。

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));

    string resultAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", resultAddress);

    return Task.FromResult(resultAddress);
}

```

請注意，這會參考傳遞給建構函式中之 OwinCommunicationListener 的 **Startup** 類別。這個 Startup 執行個體由 Web 伺服器用來啟動 Web API 應用程式。

稍後當您執行應用程式時，ServiceEventSource.Current.Message() 列會出現在 [診斷事件] 視窗，讓您知道已成功啟動 Web 伺服器。

### CloseAsync 和 Abort

最後，實作 CloseAsync 和 Abort 可停止 Web 伺服器。處置 OpenAsync 時所建立的伺服器控制代碼，可以停止 Web 伺服器。

```csharp

public Task CloseAsync(CancellationToken cancellationToken)
{
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.serverHandle != null)
    {
        try
        {
            this.serverHandle.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}

```

在此範例實作中，CloseAsync 和 Abort 只會停止 Web 伺服器。您可以選擇在 CloseAsync 中執行適當協調的 Web 伺服器關機；例如，等候進行中的要求完成然後才傳回。

## 啟動 Web 伺服器

您現在可以開始建立並傳回 OwinCommunicationListener 的執行個體以啟動 Web 伺服器。回到 Service 類別 (Service.cs)，覆寫 **CreateCommunicationListener()** 方法：

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
	return new OwinCommunicationListener("api", new Startup());
}

```

這是 Web API 應用程式和 OWIN 主機最後相會的地方：會給予主機 (**OwinCommunicationListener**) 應用程式的執行個體 (透過 **Startup** 的 Web API)，且 Service Fabric 會管理其生命週期。通常可以針對任何通訊堆疊運用相同的模式。

## 總整理

在此範例中，您不需要在 RunAsync() 方法中執行任何動作，因此可以移除覆寫。

最終的服務實作應該非常簡單，因為它只需要建立通訊接聽程式：

```csharp

namespace WebApi
{
    using Microsoft.ServiceFabric.Services;

    public class Service : StatelessService
    {
        public const string ServiceTypeName = "WebApiType";

        protected override ICommunicationListener CreateCommunicationListener()
        {
            return new OwinCommunicationListener("api", new Startup());
        }
    }
}

```

與完整的 OwinCommunicationListener 類別：

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private IDisposable serverHandle;
        private string listeningAddress;

        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup)
        {
            this.startup = startup;
            this.appRoot = appRoot;
        }

        public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
        {
            EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
            int port = serviceEndpoint.Port;

            this.listeningAddress = String.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/{1}",
                port,
                String.IsNullOrWhiteSpace(this.appRoot)
                    ? String.Empty
                    : this.appRoot.TrimEnd('/') + '/');
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));

            string resultAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", resultAddress);

            return Task.FromResult(resultAddress);
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.serverHandle != null)
            {
                try
                {
                    this.serverHandle.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}

```

所有細節就緒之後，您的專案現在看起來應該像一般 Web API 應用程式，並且具有可靠服務 API 進入點與 OWIN 主機：


![](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## 透過 Web 瀏覽器執行並連線

如果您尚未這麼做，請[設定開發環境](service-fabric-get-started.md)。


您現在可以建置並部署您的服務。在 Visual Studio 中按 **F5** 以建置及部署應用程式。在 [診斷事件] 視窗中，您應該會看到一則訊息指出 Web 伺服器在 **http://localhost:80/api** 中開啟


![](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE]如果連接埠由您電腦上的另一個處理序開啟，您可能會看到錯誤，指出無法開啟接聽程式。如果是這種情況，請嘗試在 ServiceManifest.xml 中的端點組態中使用不同的通訊埠。


一旦服務正常執行，請開啟瀏覽器並瀏覽至 [http://localhost/api](http://localhost/api) 進行測試。

## 相應放大

相應放大無狀態的 Web 應用程式通常表示加入更多電腦，並加快其上的 Web 應用程式。每當新的節點加入至叢集時，Service Fabric 的協調流程引擎便可以為您完成。在建立無狀態服務的執行個體時，您可以指定要建立的執行個體數目。Service Fabric 會相應地將該數目的執行個體放在叢集節點上，並確保不會在任何一個節點上建立多個執行個體。您也可以指定 "-1" 的執行個體計數，指示 Service Fabric，一定要在每個節點上建立執行個體。這樣可保證每當您加入節點相應放大您的叢集，便會在新節點上建立無狀態服務的執行個體。這個值會是服務執行個體的屬性，因此它會在建立服務執行個體時透過 PowerShell 設定：

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

或是在 Visual Studio 無狀態服務專案中定義預設服務時設定：

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

如需建立應用程式和服務執行個體的詳細資訊，請參閱[如何部署和移除應用程式](service-fabric-deploy-remove-applications.md)。

## ASP.NET 5

在 ASP.NET 5 中，分隔應用程式與 Web 應用程式中的主機的概念和程式設計模型維持不變。它也可以套用至其他形式的通訊。此外，雖然主機在 ASP.NET 5 中可能不同，但 Web API 應用程式層維持不變，這是大量應用程式邏輯實際存在的位置。

## 後續步驟

[在 Visual Studio 中偵錯 Service Fabric 應用程式](service-fabric-debugging-your-application.md)
 

<!---HONumber=July15_HO2-->