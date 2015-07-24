<properties
   pageTitle="Service Fabric 提供的預設通訊堆疊"
   description="本文將說明讓服務和用戶端可以通訊的可靠服務架構，所提供的預設通訊堆疊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# 可靠服務架構所提供的預設通訊堆疊
若為不受限於特定實作通訊堆疊 (WebAPI、WCF 等) 的服務作者，此架構將提供用戶端和服務端通訊項目，可以用來設定服務端與用戶端之間的通訊。

> [AZURE.NOTE]請更新至最新的 nuget 套件以取得如下所述的功能。

## 服務通訊接聽程式
服務的預設通訊接聽程式實作於 `ServiceCommunicationListener` 類別中

```csharp

public class ServiceCommunicationListener<TServiceImplementation> : ICommunicationListener where TServiceImplementation : class
{
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType);
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType, string endpointResourceName);

    public void Abort();
    public Task CloseAsync(CancellationToken cancellationToken);
    public void Initialize(ServiceInitializationParameters serviceInitializationParameters);
    public Task<string> OpenAsync(CancellationToken cancellationToken);
}

```
服務服務實作並想要公開給其用戶端的方法，在繼承自 `IService` 介面的介面中定義為非同步方法。然後服務可以只具現化 `ServiceCommunicationListener` 物件，並在 [`CreateCommunicationListener` 方法](service-fabric-reliable-services-communication.md)中傳回。例如，設定此通訊堆疊的 HelloWorld 服務程式碼的定義可能如下所示。

```csharp

[DataContract]
public class Message
{
    [DataMember]
    public string Content;
}

public interface IHelloWorld : IService
{
    Task<Message> GetGreeting();
}

public class HelloWorldService : StatelessService, IHelloWorld
{
    public const string ServiceTypeName = "HelloWorldUsingDefaultCommunicationType";

    private Message greeting = new Message() { Content = "Default greeting" };

    protected override ICommunicationListener CreateCommunicationListener()
    {
        return new ServiceCommunicationListener<HelloWorldService>(this);
    }

    public Task<Message> GetGreeting()
    {
        return Task.FromResult(this.greeting);
    }
}

```
> [AZURE.NOTE]在服務介面的引數和傳回類型 (例如上述訊息類別)，將預期以 .Net [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) 序列化。


## 撰寫用戶端以 ServiceCommunicationListener 進行通訊
如需用戶端使用 `ServiceCommunicationListener` 與服務進行通訊，該架構提供 `ServiceProxy` 類別。

```csharp

public abstract class ServiceProxy : IServiceProxy
{
...

    public static TServiceInterface Create<TServiceInterface>(Uri serviceName);

...
}

```

用戶端可以具現化服務的 Proxy 物件，並實作相對應的「服務介面」，和在 proxy 物件上叫用方法。

```csharp

var helloWorldClient = ServiceProxy.Create<IHelloWorld>(helloWorldServiceName);

var message = await helloWorldClient.GetGreeting();

Console.WriteLine("Greeting is {0}", message.Content);


```

>[AZURE.NOTE]通訊架構會負責處理服務在用戶端擲回的傳播例外狀況。因此在用戶端使用 ServiceProxy 的例外狀況處理邏輯，可以直接處理服務可能擲回的例外狀況。
 

<!---HONumber=July15_HO2-->