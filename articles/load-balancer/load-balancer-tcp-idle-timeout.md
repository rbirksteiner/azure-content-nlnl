<properties 
   pageTitle="設定負載平衡器 TCP 閒置逾時 |Microsoft Azure"
   description="設定負載平衡器 TCP 閒置逾時"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma" />

# 如何變更負載平衡器的 TCP 閒置逾時設定

在預設組態中，Azure 負載平衡器的 [閒置逾時] 設定為 4 分鐘。

這表示如果 tcp 或 http 工作階段有一段閒置時間超過逾時值，則不保證用戶端與您的服務能夠維持連線。

當連線關閉時，用戶端應用程式將收到如下的錯誤訊息：「基礎連線已關閉：應該保持運作的連線卻被伺服器關閉」。

讓連線保持作用中的時間更長的常見作法是使用 TCP Keep-alive (您可以在[這裡](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)找到 .NET 範例)。

若未在此連線上偵測到任何活動，則會傳送封包。保持持續網路活動，就永遠不會達到閒置逾時值，而且會長期維持連線。

其概念是使用比預設逾時設定短的間隔來設定 TCP Keep-alive，以避免連線中斷，或增加 TCP 連線工作階段保持連線的閒置逾時值。

儘管 TCP Keep-alive 很適合於電池不是條件約束的情況下，但這通常不是行動應用程式的有效選項。從行動應用程式使用 TCP Keep-alive 可能會更快耗盡裝置電池。

為了支援這類情況，我們加入了可設定閒置逾時的支援。您現在可以設定 4 至 30 分鐘的持續時間。此設定僅適用於輸入連線。

![tcptimeout](./media/load-balancer-tcp-idle-timeout/image1.png)


## 如何在虛擬機器和雲端服務中變更閒置逾時設定

- 透過 PowerShell 或服務管理 API 設定虛擬機器上端點的 TCP 逾時
- 透過 PowerShell 或服務管理 API，設定負載平衡端點集的 TCP 逾時。
- 設定執行個體層級公用 IP 的 TCP 逾時
- 透過服務模型設定您的 Web/背景工作角色的 TCP 逾時。
 

>[AZURE.NOTE]請記住，有些命令只會存在於最新的 Azure PowerShell 封裝。如果 powershell 命令不存在，請下載最新的 PowerShell 套件。

 
### 將執行個體層級公用 IP 的 TCP 逾時值設定為 15 分鐘。

	Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

IdleTimeoutInMinutes 是選擇性的。若未設定，則預設的逾時為 4 分鐘。

>[AZURE.NOTE]可接受的逾時範圍介於 4 與 30 分鐘之間。
 
### 在虛擬機器上建立 Azure 端點時設定閒置逾時

若要變更端點的逾時設定

	Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
 
擷取閒置逾時設定

	PS C:> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint
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
 
### 變更雲端服務的逾時設定

您可以利用 Azure SDK for .NET 2.4 來更新雲端服務。

雲端服務的端點設定是設定於 .csdef 中。若要更新雲端服務部署的 TCP 逾時，就需要部署升級。如果只針對公用 IP 指定 TCP 逾時，則為例外狀況。公用 IP 設定位於 .cscfg 中，它們可以透過部署更新和升級來更新。

端點設定的 .csdef 變更如下：

	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
	  </Endpoints>
	</WorkerRole>

公用 IP 上逾時設定的 .cscfg 變更如下：

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

## Rest API 範例

您可以使用服務管理 API 來設定 TCP 閒置逾時。請確定 x-ms-version 標頭已設定為 2014-06-01 版或更高版本。
 
在部署中的所有虛擬機器上，更新指定負載平衡輸入端點的設定
	
	Request

	POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

	Response

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

## 後續步驟

[內部負載平衡器概觀](load-balancer-internal-overview.md)

[開始設定網際網路面向的負載平衡器](load-balancer-internet-getstarted.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

 

<!---HONumber=July15_HO2-->