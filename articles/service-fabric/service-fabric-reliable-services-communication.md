<properties
   pageTitle="服務通訊模型概觀"
   description="本文說明受可靠服務 API 支援通訊模型的基本概念。"
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

# 服務通訊模型

可靠服務程式設計模型允許服務作者指定他們想要用來公開其服務端點的通訊機制，也提供用戶端可用來與服務端點進行探索和通訊的抽象概念。

## 設定服務通訊堆疊

可靠服務 API允許服務作者在其選擇的服務中外掛程式通訊堆疊，並在其服務中實作下列方法，

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

`ICommunicationListener` 介面定義必須由服務通訊接聽程式實作的介面。

```csharp

public interface ICommunicationListener
{
    void Initialize(ServiceInitializationParameters serviceInitializationParameters);

    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```
服務所需的端點將透過＜端點＞一節下的＜[服務資訊清單](service-fabric-application-model.md)＞進行說明。

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

通訊接聽程式可以從 `ServiceInitializationParameters` 中的 `CodePackageActivationContext` 存取配置予其的端點資源，並在開啟時開始接聽要求。

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]該「端點」資源通用於整個服務封裝，並在服務封裝啟動時由 Service Fabric 配置 (請參閱＜[Service Fabric ServiceModel](../service-fabric-service-model.md)＞以獲取詳細資訊)。因此，所有裝載於相同 ServiceHost 的複本都共用相同的連接埠。這表示通訊接聽程式應該支援連接埠共用。建議做法是讓通訊接聽程式在產生接聽位址時，使用資料分割識別碼和複本/執行個體識別碼。

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
  replicaOrInstanceId = parameters.InstanceId;
}
else
{
  replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

## 用戶端對服務間的通訊
可靠服務 API提供下列抽象概念讓撰寫用於與服務進行通訊的用戶端更簡單。

## ServicePartitionResolver
ServicePartitionResolver 類別可協助用戶端在執行階段決定 Service Fabric 服務的端點。

> [AZURE.TIP]判斷服務端點的程序在 Service Fabric 術語中稱為「服務端點解析」。

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
    long partitionKey,
    CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
    CancellationToken cancellationToken);


```
> [AZURE.NOTE]FabricClient 是為了 Service Fabric 叢集上各種管理作業而用來與 Service Fabric 叢集通訊的物件。

通常用戶端程式碼不需要直接搭配 `ServicePartitionResolver`。其在可靠服務 API中建立並傳遞給其他協助程式類別，並在內部使用解析程式及協助用戶端與服務進行通訊。

## CommunicationClientFactory
`ICommunicationClientFactory` 定義通訊用戶端處理站所實作的基底介面，並產生可以與 ServiceFabric 服務通訊的用戶端。CommunicationClientFactory 的實作將取決於用戶端想要通訊的 Service Fabric 服務所使用的通訊堆疊。可靠的服務 API 提供 CommunicationClientFactoryBase<TCommunicationClient>，而其提供這個 `ICommunicationClientFactory` 的基底實作介面，並執行常見的所有通訊堆疊作業 (就像使用 `ServicePartitionResolver` 決定服務端點)。用戶端通常會實作 CommunicationClientFactoryBase 抽象類別來處理通訊堆疊的特定邏輯。

```csharp

protected CommunicationClientFactoryBase(
    ServicePartitionResolver servicePartitionResolver = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
    public MyCommunicationClient(MyCommunicationChannel communicationChannel)
    {
      this.CommunicationChannel = communicationChannel;
    }
    public MyCommunicationChannel CommunicationChannel { get; private set; }
    public ResolvedServicePartition ResolvedServicePartition;

}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient1 client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(ResolvedServiceEndpoint endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(ResolvedServiceEndpoint endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

## ServicePartitionClient
透過處理一般通訊和 Service Fabric 暫時性例外狀況的重試，`ServicePartitionClient` 使用的 CommunicationClientFactory (內部的 ServicePartitionResolver) 將有助於與服務通訊。

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

一般使用模式如下所示，

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

  var result = await myServicePartitionClient.InvokeWithRetryAsync(
      client =>
      {
        // Communicate with the service using the client.
        throw new NotImplementedException();
      },
      CancellationToken.None);


... other client code ...

```

## 後續步驟
* ＜[可靠服務架構所提供的預設通訊堆疊](service-fabric-reliable-services-communication-default.md)＞

* ＜[可靠服務架構所提供的 WCF 式通訊堆疊](service-fabric-reliable-services-communication-wcf.md)＞

* ＜[撰寫使用 WebAPI 通訊堆疊的可靠服務 API 服務](service-fabric-reliable-services-communication-webapi.md)＞
 

<!---HONumber=July15_HO2-->