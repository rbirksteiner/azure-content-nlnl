<properties 
   pageTitle="管理：負載平衡器分配模式 (來源 IP 同質性)"
   description="Azure 負載平衡器分配模式的管理功能" 
   services="virtual-network" 
   documentationCenter="" 
   authors="telmosampaio" 
   manager="carolz" 
   editor=""
   />

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="telmos"
   />
   
# 管理虛擬網路：負載平衡器分配模式 (來源 IP 同質性)
「來源 IP 同質性」 (也稱為「工作階段同質性」或「用戶端 IP 同質性」)，Azure 負載平衡器分配模式，將來自單一用戶端的連線繫結到單一的 Azure 裝載伺服器，而不是將每個用戶端連線動態分配到不同的 Azure 裝載伺服器 (預設的負載平衡器行為)。

使用來源 IP 同質性，可設定 Azure 負載平衡器設定來使用 2-tuple 組合 (來源 IP、目的地 IP) 或 3-tuple 組合 (來源 IP、目的地 IP 及通訊協定)，以將流量對應至可用的 Azure 裝載伺服器集區。使用來源 IP 同質性時，從相同用戶端電腦初始化的連線是由單一 DIP 端點 ( Azure 裝載的單一伺服器) 所處理。

## 服務來源

來源 IP 同質性可解決先前[在 Azure 負載平衡器和 RD 閘道 (DOC) 之間的不相容](http://go.microsoft.com/fwlink/p/?LinkId=517389)。

## 實作

來源 IP 同質性可針對下列項目設定：

* [虛擬機器端點](../virtual-machines/virtual-machines-set-up-endpoints.md)
* [負載平衡的端點集](http://msdn.microsoft.com/library/azure/dn655055.aspx)
* [Web 角色](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [背景工作角色](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## 案例
1. 使用單一雲端服務的遠端桌面閘道叢集
2. 媒體上傳 (例如，適用於資料的 UDP、用來控制的 TCP)
  * 用戶端會將 TCP 工作階段初始化為 Azure 裝載且負載平衡的公用 IP 位址
  * 負載平衡器會將用戶端要求導向到 DIP；此通道會保持作用中以監視連線的健全狀況
  * 用戶端會將 UDP 工作階段初始化為相同的 Azure 裝載且負載平衡的公用 IP 位址
  * Azure 負載平衡器會將要求導向到與 TCP 連線相同的 DIP 端點
  * 用戶端會使用較高的 UDP 輸送量來上傳媒體，同時透過 TCP 維持控制通道以提供可靠性
  
## 需要注意的事項
* 如果負載平衡集變更 (例如，加入或移除虛擬機器)，就會重新計算用戶端通道分配；來自現有用戶端的新連線可能是由原本使用之伺服器以外的伺服器所處理
* 使用來源 IP 同質性，可能導致跨 Azure 裝載伺服器分配了不相等的流量
* 透過 Proxy 路由傳送流量的用戶端可能會被 Azure 負載平衡器視為單一用戶端

## PowerShell 範例
請下載[最新版的 Azure PowerShell](https://github.com/Azure/azure-sdk-tools/releases)，以獲得最佳結果。

### 將 Azure 端點新增到虛擬機器，並設定負載平衡器分配模式

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 â€“LoadBalancerDistribution â€œsourceIPâ€�| Update-AzureVM  

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 Ã¢â‚¬â€œLoadBalancerDistribution Ã¢â‚¬Å“sourceIPÃ¢â‚¬ï¿½| Update-AzureVM  

LoadBalancerDistribution 可設定為 sourceIP 以用於 2-tuple (來源 IP、目的地 IP) 負載平衡、sourceIPProtocol 以用於 3-tuple (來源 IP、目的地 IP、通訊協定) 負載平衡，或者，如果您想要預設行為 (5-tuple 負載平衡)，則可設為 none。

### 擷取端點負載平衡器分配模式組態
    PS C:\> Get-AzureVM â€“ServiceName "mySvc" -Name "MyVM1" | Get-AzureEndpoint
    
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

如果 LoadBalancerDistribution 項目不存在，則 Azure 負載平衡器會使用預設的 5-tuple 演算法

### 在負載平衡端點集上設定分配模式

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 â€“LoadBalancerDistribution "sourceIP"

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 Ã¢â‚¬â€œLoadBalancerDistribution "sourceIP"
    
如果端點是負載平衡端點集的一部分，就必須在負載平衡端點集上設定分配模式。

## 雲端服務範例

您可以利用 Azure SDK for.NET 來更新雲端服務

雲端服務的端點設定是設定於 .csdef 中。若要更新雲端服務部署的負載平衡器分配模式，部署升級是必要的。

適用於端點設定的 .csdef 變更範例如下：

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
      </Endpoints>
    </WorkerRole>
    <NetworkConfiguration>
      <VirtualNetworkSite name="VNet"/>
      <AddressAssignments>
        <InstanceAddress roleName="VMRolePersisted">
          <PublicIPs>
            <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
          </PublicIPs>
        </InstanceAddress>
      </AddressAssignments>
    </NetworkConfiguration>
    
## API 範例

開發人員可以使用服務管理 API 來設定負載平衡器分配。請務必新增已設定為 2014-09-01 版或更高版本的 x-ms-version 標頭。

### 在部署中更新指定負載平衡集的設定

#### 要求

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet 
    
    x-ms-version: 2014-09-01 
    
    Content-Type: application/xml 
    
    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"> 
      <InputEndpoint> 
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName> 
        <LocalPort> local-port-number </LocalPort> 
        <Port> external-port-number </Port> 
        <LoadBalancerProbe> 
          <Port> port-assigned-to-probe </Port> 
          <Protocol> probe-protocol </Protocol> 
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds> 
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds> 
        </LoadBalancerProbe> 
        <Protocol> endpoint-protocol </Protocol> 
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn> 
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes> 
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution> 
      </InputEndpoint> 
    </LoadBalancedEndpointList>

LoadBalancerDistribution 的值可以是 sourceIP (適用於 2-tuple 同質性)、sourceIPProtocol (適用於 3-tuple 同質性) 或 none (適用於沒有同質性。也就是 5-tuple)

#### Response

    HTTP/1.1 202 Accepted 
    Cache-Control: no-cache 
    Content-Length: 0 
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0 
    x-ms-servedbyregion: ussouth2 
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af 
    Date: Thu, 16 Oct 2014 22:49:21 GMT
 

<!---HONumber=July15_HO1-->