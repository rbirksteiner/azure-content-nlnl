<properties 
   pageTitle="Azure 負載平衡器概觀 | Microsoft Azure"
   description="Azure 負載平衡器功能、架構和實作的概觀。有助於了解負載平衡器的運作方式，並將其用於雲端"
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


# 負載平衡器概觀 
Azure 負載平衡器可為您的應用程式提供高可用性和網路效能。這是 Layer-4 (TCP、UDP) 類型負載平衡器，可將連入流量分散在負載平衡器集合中定義之雲端服務或虛擬機器集中狀況良好的服務執行個體。
 
可以設定為：

- 對虛擬機器的連入網際網路流量進行負載平衡。我們稱之為[網際網路面向的負載平衡](load-balancer-overview.md)。
- 平衡虛擬網路中的虛擬機器之間、雲端服務中的虛擬機器之間，或內部部署電腦與跨單位部署虛擬網路中的虛擬機器之間的流量負載。我們稱之為[內部負載平衡 (ILB)](load-balancer-internal-overview.md)。
- 	將外部流量轉送到特定的虛擬機器執行個體


## 負載平衡器功能

### Layer-4 負載平衡器，雜湊型分散

Azure 負載平衡器會使用雜湊型分散演算法。預設使用有 5 個 Tuple (來源 IP、來源連接埠、目的地 IP、目的地連接埠、通訊協定類型) 的雜湊，以將流量對應至可用的伺服器。它只在傳輸工作階段內提供綁定。相同 TCP 或 UDP 工作階段中的封包會被導向至負載平衡端點後面的相同資料中心 IP (DIP) 執行個體。當用戶端關閉並重新開啟連線或從相同的來源 IP 啟動新的工作階段時，來源連接埠會變更。這可能會造成流量移至不同的 DIP 端點。


如需詳細資訊，請參閱 [Azure內部負載平衡分配模式](load-balancer-distribution-mode.md)。

![雜湊型負載平衡器](./media/load-balancer-overview/load-balancer-distribution.png)

### 連接埠轉送

Azure 負載平衡器可供您控制如何管理輸入通訊，例如：從網際網路主機或其他雲端服務或虛擬網路中虛擬機器起始的流量。此控制項是以端點 (也稱為輸入端點) 表示。

端點會在公用連接埠上接聽，並將流量轉送至內部連接埠。您可以對應的內部或外部端點的相同連接埠，或對它們使用不同的連接埠。例如：當公用端點對應為連接埠 80 時，您可以將 Web 伺服器設定為接聽連接埠 81。建立公用端點便會觸發建立 Azure 負載平衡器。

您使用 Azure 管理入口網站建立的虛擬機器上端點的預設用途和組態，可供遠端桌面通訊協定 (RDP) 和遠端 Windows PowerShell 工作階段流量使用。這些端點可讓您透過網際網路從遠端管理虛擬機器。


### 在相應放大/縮小時自動重新設定

當您相應增加或減少執行個體 (由於增加 Web/背景工作角色的執行個體計數，或由於將額外虛擬機器放在相同的負載平衡集合之下) 時，Azure 負載平衡器本身會立即重新設定。


### 服務監視
Azure 負載平衡器可供探查各種伺服器執行個體的健全狀況。當探查無法回應時，Azure 負載平衡器會停止傳送新的連線至狀況不良的執行個體。現有的連線不受影響。

支援的探查類型有三種：
 
- 客體代理程式探查 (只適用於 PaaS VM)。只有在執行個體處於 [就緒] 狀態 (即執行個體不是處於 [忙碌]、[回收中]、[停止中] 等狀態) 時，Azure 負載平衡器才會利用虛擬機器內的客體代理程式、接聽並以「HTTP 200 確定」作為回應。如果客體代理程式無法以「HTTP 200 確定」回應，則 Azure 負載平衡器會將執行個體標示為沒有回應，並停止傳送流量到該執行個體。Azure 負載平衡器將會繼續 ping 執行個體，而如果客體代理程式以 HTTP 200 回應，則 Azure 負載平衡器會再次傳送流量到該執行個體。使用 Web 角色時，您的網站程式碼通常會在不受 Azure 網狀架構或客體代理程式監視的 w3wp.exe 中執行，這表示不會向客體代理程式回報 w3wp.exe 中的失敗 (如 HTTP 500 回應)，而且負載平衡器不知道要將該執行個體退出循環。

- HTTP 自訂探查。自訂負載平衡器探查會覆寫預設客體代理程式探查，讓您建立自己的自訂邏輯來判斷角色執行個體的健全狀況。負載平衡器會定期探查您的端點 (預設為每隔 15 秒)，而如果在逾時期限 (預設值為 31 秒) 內以 TCP ACK 或 HTTP 200 回應，執行個體將會被視為在循環中。這適合用於實作自己的邏輯，以從負載平衡器循環中移除執行個體，例如，執行個體超過 90% 的 CPU 時會傳回非 200 狀態。對於使用 w3wp.exe 的 Web 角色，這也表示您可自動監視您的網站，因為網站程式碼中的錯誤會將非 200 狀態傳回給負載平衡器探查。

- TCP 自訂探查。TCP 探查依賴於將 TCP 工作階段成功建立至定義的探查連接埠。

如需詳細資訊，請參閱[負載平衡器健全狀況探查](https://msdn.microsoft.com/library/azure/jj151530.aspx)。

### 來源 NAT (SNAT)


從您的服務送至網際網路的所有輸出流量都已進行來源 NAT (SNAT)，其對連入流量使用相同的 VIP 位址。SNAT 提供一些重要優勢：

- 它能夠輕鬆進行服務的升級及嚴重損壞修復，因為 VIP 可以動態對應到服務的另一個執行個體。

- 它讓 ACL 管理變得更輕鬆，因為 ACL 可以根據 VIP 表示，因此不會隨著服務相應增加或減少或進行重新部署而改變。

Azure 負載平衡器組態支援 UDP 的完全錐形 NAT。在完全錐形 NAT 中，連接埠允許來自任何外部主機的輸入連線 (以回應輸出要求)。

![snat](./media/load-balancer-overview/load-balancer-snat.png)

請注意，對於 VM 起始的每個新輸出連線，Azure 負載平衡器也會配置輸出連接埠。外部主機將會看到流量以「VIP：配置的連接埠」送來。如果您的案例需要大量輸出連線，則建議 VM 使用執行個體層級的公用 IP，以便有專用的輸出 IP 進行來源網路位址轉譯 (SNAT)。這會降低連接埠耗盡的風險。

**支援虛擬機器有多個負載平衡 IP**

您可以取得指派給一組虛擬機器的多個負載平衡公用 IP 位址。運用這項功能，您可以在同一組虛擬機器上裝載多個 SSL 網站和/或多個 SQL AlwaysOn 可用性群組接聽程式。如需詳細資訊，請參閱[每一雲端服務有多重 VIP](load-balancer-multivip.md)

**使用 Azure 資源管理員的範本型部署 (公開預覽版)** Azure 資源管理員 (ARM) 是 Azure 中新的服務管理架構。Azure 負載平衡器現在可使用以 Azure 資源管理員為基礎的 API 和工具來管理。若要深入了解 Azure 資源管理員，請參閱 [Iaas 因為 Azure 資源管理員變得更容易](http://azure.microsoft.com/blog/2015/04/29/iaas-just-got-easier-again/)


## 後續步驟

[網際網路面向的負載平衡器概觀](load-balancer-internet-overview.md)

[內部負載平衡器概觀](load-balancer-internal-overview.md)

[開始使用 - 網際網路面向的負載平衡器](load-balancer-internet-getstarted.md)
 

<!---HONumber=July15_HO2-->