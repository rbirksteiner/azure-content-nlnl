
<properties 
   pageTitle="網際網路面向的負載平衡器概觀 |Microsoft Azure"
   description="網際網路面向的負載平衡器及其功能的概觀。負載平衡器如何作用於使用虛擬機器和雲端服務的 Azure。"
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


# 多個虛擬機器或服務之間的網際網路面向負載平衡器

端點的其中一個用途是 Azure 負載平衡器的組態，該組態可在多個虛擬機器或服務之間散發特定類型的流量。例如，您可以將 Web 要求的流量負載分散在多個 Web 伺服器或 Web 角色。

Azure 負載平衡器將連入流量的公用 IP 位址和連接埠號碼對應至虛擬機器的私人 IP 位址和連接埠號碼，來自虛擬機器的回應流量也是如此。

![建立負載平衡器範例](./media/load-balancer-internet-overview/IC727496.png))

>[AZURE.NOTE]當您使用預設值在多個虛擬機器或服務間設定流量的負載平衡時，Azure 可為連入流量提供常態分配。如果您要尋找工作階段同質性 (或黏性工作階段)，請參閱 [負載平衡器分配模式](load-balancer-distribution-mode.md)

對於包含 Web 角色或背景工作角色執行個體的雲端服務，您可以在服務定義 (.csdef) 中定義公用端點。
 
Servicedefinition.csdef 檔案將包含端點組態，而當一個 Web 或背景工作角色部署有多個角色執行個體時，將會為它設定負載平衡器。將執行個體加入至雲端部署的方法就是變更服務組態檔 (.csfg) 上的執行個體計數。

下圖顯示在三部虛擬機器中共用，且公用和私人 TCP 連接埠均為 443 的已加密 Web 流量負載平衡端點。這三部虛擬機器均位在負載平衡集合中。

當網際網路用戶端傳送網頁要求至雲端服務的公用 IP 位址與 TCP 連接埠 443 時，Azure 負載平衡器會對負載平衡集合中，介於這三部虛擬機器之間的要求執行隨機的平衡。


## 後續步驟

[內部負載平衡器概觀](load-balancer-internal-overview.md)

[開始設定網際網路面向的負載平衡器](load-balancer-internet-getstarted.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)


 

<!---HONumber=July15_HO2-->