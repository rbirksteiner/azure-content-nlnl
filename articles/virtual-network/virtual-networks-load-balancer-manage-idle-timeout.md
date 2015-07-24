<properties 
   authors="danielceckert" 
   documentationCenter="dev-center-name" 
   editor=""
   manager="jefco" 
   pageTitle="管理：負載平衡器的閒置逾時" 
   description="Azure 負載平衡器閒置逾時的管理功能" 
   services="virtual-network" 
   />

<tags
   ms.author="danecke"
   ms.date="05/27/2015"
   ms.devlang="na"
   ms.service="virtual-network"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   />
   
# 管理虛擬網路：負載平衡器的 TCP 閒置逾時

「TCP 閒置逾時」讓開發人員可以在與 Azure 負載平衡器相關的用戶端-伺服器工作階段期間，指定保證無活動的閾值。4 分鐘的 TCP 閒置逾時值 (Azure 負載平衡器的預設值) 表示，如果在牽涉到 Azure 負載平衡器的用戶端-伺服器工作階段期間，有一段閒置時間長達超過 4 分鐘的期間，則將關閉連線。

當用戶端-伺服器連線關閉時，用戶端應用程式將收到如下的錯誤訊息：「基礎連接已關閉：應該保持運作的連接卻被伺服器關閉」。

[TCP 持續連線](http://tools.ietf.org/html/rfc1122#page-101)是在長時間被視為處於非作用狀態的期間保持連線的常見做法 [(MSDN 範例)](http://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)。使用 TCP 持續連線時，用戶端會定期傳送簡單的封包 (通常頻率期間會比伺服器的閒置逾時閾值還短)。伺服器認為即使沒有其他活動發生，這些傳輸還是可做為連線活動的證明 -- 因此，永遠都不會符合閒置逾時值，而且連線可維持一段很長的時間。

儘管 TCP 持續連線可良好運作，但它通常不是行動應用程式的選項，因為它會耗用行動裝置上的有限電源。使用 TCP 持續連線的行動應用程式將會更快耗盡裝置電池，因為它會為網路使用量而持續消耗電力。

為了支援行動裝置案例，Azure 負載平衡器支援可設定的 TCP 閒置逾時。開發人員可以針對輸入連線將 TCP 閒置逾時設定為介於 4 分鐘到 30 分鐘之間的任何期間 (可設定的 TCP 閒置逾時不會套用到輸出連線)。這讓用戶端可以與伺服器維持一段較長的非使用狀態工作階段。行動裝置上的應用程式還是可以選擇運用 TCP 持續連線技術，來保留預期超過 30 分鐘處於非使用狀態期間的連線，但是，這個較長的 TCP 閒置逾時讓應用程式發出 TCP 持續連線要求的頻率遠比之前還少，可大幅降低對行動裝置電源的負擔。

## 實作

您可以針對下列各項設定 TCP 閒置逾時：

* [執行個體層級的公用 IP](http://msdn.microsoft.com/library/azure/dn690118.aspx)
* [負載平衡的端點集](http://msdn.microsoft.com/library/azure/dn655055.aspx)
* [虛擬機器端點](../virtual-machines/virtual-machines-set-up-endpoints.md)
* [Web 角色](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [背景工作角色](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## 後續步驟
* TBD

## PowerShell 範例
請下載[最新版的 Azure PowerShell](https://github.com/Azure/azure-sdk-tools/releases)，以獲得最佳結果。

### 將執行個體層級公用 IP 的 TCP 逾時值設定為 15 分鐘

    Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

IdleTimeoutInMinutes 是選擇性的。若未設定，則預設的逾時為 4 分鐘。它的值現在可設為介於 4 到 30 分鐘之間。

### 在虛擬機器上建立 Azure 端點時設定閒置逾時

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

### 擷取閒置逾時設定

    PS C:\> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint
    
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
    
### 在負載平衡端點集上設定 TCP 逾時

如果端點是負載平衡端點集的一部分，就必須在負載平衡端點集上設定 TCP 逾時：

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## 雲端服務範例

您可以利用 Azure SDK for.NET 來更新雲端服務

雲端服務的端點設定是設定於 .csdef 中。因此，若要更新雲端服務部署的 TCP 逾時，就需要部署升級。如果只針對公用 IP 指定 TCP 逾時，則為例外狀況。公用 IP 設定位於 .cscfg 中，它們可以透過部署更新和升級來更新。

端點設定的 .csdef 變更如下：

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>The .cscfg changes for the timeout setting on Public IPs are:
    
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

開發人員可以使用服務管理 API 來設定負載平衡器分配。請務必新增已設定為 2014-06-01 版或更高版本的 x-ms-version 標頭。

### 在部署中的所有虛擬機器上，更新指定負載平衡輸入端點的設定

#### 要求

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

LoadBalancerDistribution 的值可以是 sourceIP (適用於 2-tuple 同質性)、sourceIPProtocol (適用於 3-tuple 同質性) 或 none (適用於沒有同質性。也就是 5-tuple)

#### Response

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
        <LocalPort>local-port-number</LocalPort>
        <Port>external-port-number</Port>
        <LoadBalancerProbe>
          <Path>path-of-probe</Path>
          <Port>port-assigned-to-probe</Port>
          <Protocol>probe-protocol</Protocol>
          <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
          <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
        </LoadBalancerProbe>
        <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
        <Protocol>endpoint-protocol</Protocol>
        <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
        <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
        <EndpointACL>
          <Rules>
            <Rule>
              <Order>priority-of-the-rule</Order>
              <Action>permit-rule</Action>
              <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
              <Description>description-of-the-rule</Description>
            </Rule>
          </Rules>
        </EndpointACL>
      </InputEndpoint>
    </LoadBalancedEndpointList>
 

<!---HONumber=July15_HO1-->