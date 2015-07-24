<properties 
   pageTitle="每一雲端服務有多重 VIP"
   description="MultiVIP 的概觀以及如何在雲端服務上設定多重 VIP"
   services="load-balancer"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/01/2015"
   ms.author="telmos" />

# 每一雲端服務有多重 VIP
您可以使用 Azure 所提供的 IP 位址，透過公用網際網路存取 Azure 雲端服務。此公用 IP 位址也稱為 VIP (虛擬 IP)，因為它會連結至 Azure 負載平衡器，而且不是雲端服務中真正的 VM 執行個體。您可以使用單一 VIP 來存取雲端服務中的任何 VM 執行個體。

不過，在某些情況下，您可能需要一個以上的 VIP 作為相同雲端服務的進入點。比方說，您的雲端服務可以裝載多個需要使用預設 SLSL 連接埠 443 進行 SSL 連線的網站，而每個網站是針對不同的客戶或租用戶進行裝載。在這種情況下，每個網站都需要有不同的公開 IP 位址。下圖顯示典型的多租用戶 Web 裝載，其在相同的公用連接埠上需要有多個 SSL 憑證。

![多重 VIP SSL 案例](./media/load-balancer-multivip/Figure1.png)

在上述情況下，所有 VIP 都使用相同的公用連接埠 (443)，而流量會重新導向至裝載所有網站之雲端服務的內部 IP 位址中唯一私人連接埠上的一或多個負載平衡 VM。

>[AZURE.NOTE]使用多重 VIP 的另一個案例是在同一組虛擬機器上裝載多個 SQL AlwaysOn 可用性群組接聽程式。

VIP 預設是動態的，這表示指派給雲端服務的實際 IP 位址會隨著時間改變。若要防止發生該狀況，您可以為您的服務保留 VIP。若要深入了解保留的 VIP，請參閱[保留的公用 IP](../virtual-networks-reserved-public-ip)。

>[AZURE.NOTE]如需有關 VIP 和保留的 IP 的定價資訊，請參閱 [IP 位址定價](http://azure.microsoft.com/pricing/details/ip-addresses/)。

您可以使用 PowerShell 來驗證雲端服務所使用的 VIP，以及新增和移除 VIP、將 VIP 關聯到端點，以及在特定 VIP 上設定負載平衡。

## 限制

目前，多重 VIP 功能受限於下列案例：

- **僅限 IaaS**。您只能針對包含 VM 的雲端服務啟用多重 VIP。您無法在 PaaS 案例中將多重 VIP 與角色執行個體搭配使用。
- **僅限 PowerShell**。您只能使用 PowerShell 來管理多重 VIP。
- **不支援 ARM**。Azure 資源管理員中不支援多重 VIP。

>[AZURE.IMPORTANT]這些限制都是暫時的，隨時可能變更。請務必再次瀏覽此頁面，以確認未來變更。


## 如何將 VIP 新增至雲端服務
若要將 VIP 新增至您的服務，請執行下列 PowerShell 命令：

    Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

上述命令會顯示類似以下範例的結果：

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## 如何從雲端服務移除 VIP
若要移除在上述範例中新增您的服務的 VIP，請執行下列 PowerShell 命令：

    Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

>[AZURE.IMPORTANT]您只能移除沒有任何相關端點的 VIP。

## 如何從雲端服務擷取 VIP 資訊
若要擷取與雲端服務相關聯的 VIP，請執行下列 PowerShell 指令碼：

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

上述指令碼會顯示類似以下範例的結果：

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

在此範例中，雲端服務有 3 個 VIP：

- **Vip1** 是預設 VIP，您知道因為 IsDnsProgrammedName 的值已設定為 true。
- **Vip2** 和 **Vip3** 因為沒有任何 IP 位址而不會予以使用。唯有將端點關聯到 VIP，才會予以使用。

>[AZURE.NOTE]有額外的 VIP 與端點相關聯時，才會向您的訂用帳戶收費。如需定價的詳細資訊，請參閱 [IP 位址定價](http://azure.microsoft.com/pricing/details/ip-addresses/)。

## 如何將 VIP 關聯至端點
若要將雲端服務上的 VIP 關聯至端點，請執行下列 PowerShell 命令：

    Get-AzureVM -ServiceName myService -Name myVM1 `
    | Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 `
    | Update-AzureVM

上述命令會使用連接埠 *8080* 上的 *TCP* 建立一個端點 (該端點會連結至連接埠 *80* 上名為 *Vip2* 的 VIP)，並將它連結至名為 *myService* 的雲端服務中名為 *myVM1* 的 VM。

若要驗證組態，請執行下列 PowerShell 命令：

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

而輸出類似下面的結果：

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## 如何在特定 VIP 上啟用負載平衡
您可以將單一 VIP 與多部虛擬機器產生關聯，以達到負載平衡。例如，假設您有名為 *myService* 的雲端服務，以及名為 *myVM1* 和 *myVM2* 的兩部虛擬機器。而您的雲端服務有多重 VIP，其中一個名為 *Vip2*。如果您想確保對 *Vip2* 上連接埠 *81* 的所有流量會在連接埠 *8181* 上的 *myVM1* 與 *myVM2* 之間達到平衡，請執行下列 PowerShell 指令碼：

    Get-AzureVM -ServiceName myService -Name myVM1 `
    | Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
    | Update-AzureVM

    Get-AzureVM -ServiceName myService -Name myVM2 `
    | Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
    | Update-AzureVM

您也可以更新您的負載平衡器，以使用不同的 VIP。比方說，如果您執行下列 PowerShell 命令，您將將負載平衡集變更為使用名為 Vip1 的 VIP：

    Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1

## 另請參閱

[網際網路面向的負載平衡器概觀](load-balancer-internet-overview.md)

[開始使用網際網路面向的負載平衡器](load-balancer-internet-getstarted.md)

[虛擬網路概觀](https://msdn.microsoft.com/library/azure/jj156007.aspx)

[保留的 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 

<!---HONumber=July15_HO2-->