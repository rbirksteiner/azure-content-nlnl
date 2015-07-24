<properties
   pageTitle="可靠服務 API 所提供的 WCF 式通訊堆疊"
   description="本文將說明可靠服務 API 所提供的 WCF 式通訊堆疊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# 可靠服務的 WCF 式通訊堆疊
可靠服務架構允許服務作者決定其想要使用服務的通訊堆疊。他們可以透過 [`CreateCommunicationListener`](../service-fabric-reliable-service-communication.md) 方法傳回的 `ICommunicationListener` 在其選擇的通訊堆疊上外掛程式。服務作者如果想要使用 WCF 式通訊，架構提供以 WCF 式實作的通訊堆疊。

## WCF 通訊接聽程式
WCF 的 `ICommunicationListener` 特定實作是由 `WcfCommunicationListener` 類別所提供。

```csharp

public WcfCommunicationListener(
    Type communicationInterfaceType,
    Type communicationImplementationType);

protected override ICommunicationListener CreateCommunicationListener()
    {
        WcfCommunicationListener communicationListener = new WcfCommunicationListener(typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // which identifies the endpoint that the wcf servicehost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        };

        return communicationListener;
    }

```

## 撰寫 WCF 通訊堆疊的用戶端
如需撰寫使用 WCF 讓用戶端與服務進行通訊，架構提供 `WcfClientCommunicationFactory`，也就是 WCF 的 [`ClientCommunicationFactoryBase`](../service-fabric-reliable-service-communication.md) 特定實作。

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

可以從 `WcfCommunicationClientFactory` 建立的 `WcfCommunicationClient` 存取 WCF 通訊通道

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

用戶端程式碼可以使用 `WcfCommunicationClientFactory` 連同 `ServicePartitionClient` 以判斷服務端點，並與服務進行通訊。

```csharp

//
// Create a service resolver for resolving the endpoints of the calculator service.
//
ServicePartitionResolver serviceResolver = new ServicePartitionResolver(() => new FabricClient());

//
// Create the binding.
//
NetTcpBinding binding = CreateClientConnectionBinding();

var clientFactory = new WcfCommunicationClientFactory<ICalculator>(
    serviceResolver,// ServicePartitionResolver
    binding,        // Client binding
    null,           // Callback object
    null);          // donot retry Exception types


//
// Create a client for communicating with the calc service which has been created with
// Singleton partition scheme.
//
var calculatorServicePartitionClient = new ServicePartitionClient<WcfCommunicationClient<ICalculator>>(
    clientFactory,
    ServiceName);

//
// Call the service to perform the operation.
//
var result = calculatorServicePartitionClient.InvokeWithRetryAsync(
    client => client.Channel.AddAsync(2, 3)).Result;


```
 

<!---HONumber=July15_HO2-->