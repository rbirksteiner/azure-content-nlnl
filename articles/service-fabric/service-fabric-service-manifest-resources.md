<properties
   pageTitle="Service Fabric 服務資訊清單資源"
   description="如何描述服務資訊清單中的資源"
   services="service-fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/23/2015"
   ms.author="sumukhs"/>

# 服務資訊清單資源

## 概觀

服務資訊清單可讓服務使用資源，並用於宣告/變更，且不需變更已編譯的程式碼。Service Fabric 支援針對服務的端點資源組態。透過應用程式資訊清單中的 SecurityGroup，即可控制存取服務資訊清單中的指定資源。資源宣告可讓您在部署階段變更這些資源，而不需要讓服務導入新的組態機制。

## Endpoints

在服務資訊清單中定義端點資源時，Service Fabric 會從保留的應用程式連接埠範圍指派連接埠。此外，服務也可以在資源中要求特定連接埠。不同的連接埠號碼可以指派給在不同叢集節點上執行的服務複本，而在同一節點上執行的相同服務複本也可以共用同一個連接埠。服務複本可將此類連接埠用於各種用途，例如複寫、接聽用戶端要求等。

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint" Protocol="http"/>
    <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
    <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

請參閱[設定具狀態的可靠服務](../Service-Fabric/service-fabric-reliable-services-configuration.md)，從設定封裝設定檔 (settings.xml) 深入了解參考端點。

## 範例
以下服務資訊清單在 &lt;Resources&gt; 項目中定義了 1 個 TCP 端點資源和 2 個 HTTP 端點資源。

Service-Fabric 會自動將 HTTP 端點處理為 ACL。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="SP1" Version="V1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Description>Test Service</Description>
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="PersistType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="CP1" Version="V1">
    <EntryPoint>
      <ExeHost>
        <Program>CB\Code.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="CP1.Config0" Version="V1" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http"/>
      <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
      <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
 

<!---HONumber=July15_HO2-->