
<properties
   pageTitle="開始使用網際網路面向的負載平衡器 | Microsoft Azure"
   description="為您的虛擬機器或雲端服務取得第一個網際網路面向的負載平衡器集合。"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma" />

# 開始設定網際網路面向的負載平衡器

Microsoft Azure 中的負載平衡服務可處理所有租用戶類型 (IaaS 或 PaaS) 和各種作業系統 (Windows 或任何以 Linux 為基礎的作業系統)。


## 為虛擬機器設定網際網路面向的負載平衡器

為了使雲端服務的各虛擬機器的網際網路流量達到負載平衡，您必須建立負載平衡的集合。此程序假設您已建立虛擬機器，而且全都在相同的雲端服務內。

請遵循下列步驟來設定虛擬機器的負載平衡器集合：

1. 在 Azure 管理入口網站中，按一下 [虛擬機器]，然後按一下負載平衡集合中虛擬機器的名稱。
2.	選取 [端點]，然後按一下 [新增]。

4.	在 [將端點加入至虛擬機器] 頁面上，按一下向右箭頭。

4.	在 [Specify the details of the endpoint] 頁面上：
	- 在 [名稱] 中輸入端點的名稱，或從常見通訊協定的預先定義端點清單中選取。
	-  在 [通訊協定] 中，選取端點類型所需的通訊協定 (視需要選擇 TCP 或 UDP)。
 	-  在 [公用連接埠] 和 [私人連接埠] 中，視需要輸入要虛擬機器使用的連接埠號碼。您可以在虛擬機器上使用私人連接埠和防火牆規則，以適合應用程式的方式來重新導向流量。私人連接埠可與公用連接埠相同。例如，對於 Web (HTTP) 端點的流量，您可以將連接埠 80 同時指派給公用和私人連接埠。

5.	選取 [Create a load-balanced set]，然後按一下向右箭頭。

6.	在 [Configure the load-balanced set] 頁面上，輸入負載平衡集合的名稱，然後指派 Azure 負載平衡器探查行為的值。負載平衡器會使用探查，來判斷負載平衡集合中的虛擬機器是否可用於接收連入流量。

7.	按一下核取記號以建立負載平衡端點。您會在虛擬機器的 [端點] 頁面的 [Load-balanced set name] 欄中看見 [是]。

8.	在管理入口網站中，依序按一下 [虛擬機器]、負載平衡集合中其他虛擬機器的名稱、[端點]，然後按一下 [新增]。

9.	在 [Add an endpoint to a virtual machine] 頁面上，按一下 [Add endpoint to an existing load-balanced set]，選取負載平衡集合的名稱，然後按一下向右箭頭。

10.	在 [Specify the details of the endpoint] 頁面上，輸入端點的名稱，然後按一下核取記號。針對負載平衡集合中的其他虛擬機器，重複步驟 8-10。

您也可以使用下列 Windows PowerShell Cmdlet 來設定端點：

- Add-AzureEndpoint

[Add-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495300)

- Get-AzureEndpoint

[Get-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495158)

- Remove-AzureEndpoint

[Remove-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495161)


## 為雲端服務設定網際網路面向的平衡器


雲端服務是使用負載平衡器自動設定，並可透過服務模型加以自訂

您可以利用 Azure SDK for .NET 2.5 來更新雲端服務。雲端服務的端點設定設於 [service definition](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef 中。

下列範例示範如何設定雲端部署的 servicedefinition.csdef 檔案：

檢查雲端部署所產生之 .csdef 檔的 snipet，您可以看到已設定為在連接埠 10000、10001 和 10002 上使用連接埠 HTTP 的外部端點。


	<ServiceDefinition name=“Tenant“>
   	<WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
  	</WorkerRole>
	</ServiceDefinition>




### 檢查雲端服務的負載平衡器健全狀態


以下是健全狀態探查的範例：

	 	<LoadBalancerProbes>
    	<LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
 	 	</LoadBalancerProbes>

負載平衡器會合併端點的資訊和探查的資訊，以建立形式為 http://{DIP of VM}:80/Probe.aspx 的 URL，該 URL 將用來查詢服務的健全狀況。

服務注意到相同的 IP 會定期予以存取。這是來自 VM 執行所在之節點主機的健全狀況探查要求。服務必須以負載平衡器的 HTTP 200 狀態碼回應，假設服務的狀況良好。任何其他 HTTP 狀態碼 (例如 503) 都會直接讓 VM 脫離循環。

探查定義也會控制探查的頻率。在上述案例中，負載平衡器每隔 5 秒探查端點一次。如果長達 10 秒 (兩個探查間隔) 未收到正面回應，則會認為探查已關閉，而 VM 會脫離循環。同樣地，如果服務已脫離循環並收到正面回應，則會立即將服務放回循環。如果服務在狀況良好 / 狀況不良之間變動，則負載平衡器可以決定延遲將循環重新帶回服務，直到有探查數次的狀況皆為良好為止。

如需詳細資訊，請查看服務定義結構描述中的[健全狀況探查](https://msdn.microsoft.com/library/azure/jj151530.aspx)。

## 後續步驟

[開始設定內部負載平衡器](load-balancer-internal-getstarted.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=July15_HO2-->