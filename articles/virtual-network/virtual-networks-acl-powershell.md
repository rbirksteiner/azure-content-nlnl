<properties 
   pageTitle="如何使用 PowerShell 管理端點的存取控制清單 (ACL)"
   description="了解如何使用 PowerShell 管理 ACL"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# 如何使用 PowerShell 管理端點的存取控制清單 (ACL)

您可以使用 Azure PowerShell 或在管理入口網站中建立和管理端點的網路存取控制清單 (ACL)。在本主題中，您會了解一些可使用 PowerShell 完成 ACL 一般工作的程序。如需 Azure PowerShell Cmdlet 的清單，請參閱＜[Azure 管理 Cmdlet](http://go.microsoft.com/fwlink/?LinkId=317721)＞。如需有關 ACL 的詳細資訊，請參閱＜[什麼是網路存取控制清單 (ACL)？](../virtual-networks-acl)＞。若您要使用管理入口網站來管理 ACL，請參閱＜[如何設定虛擬機器的端點](../virtual-machines-set-up-endpoints/)＞。

## 使用 Azure PowerShell 來管理網路 ACL

您可以使用 Azure PowerShell Cmdlet 來建立、移除和設定 (Set) 網路存取控制清單 (ACL)。我們已加入一些您可以使用 PowerShell 設定 ACL 方式的幾個範例。

若要擷取 ACL PowerShell Cmdlet 的完整清單，您可以使用下列其中一項：

	Get-Help *AzureACL*
	Get-Command -Noun AzureACLConfig

### 建立網路 ACL 搭配規則以允許從遠端子網路進行存取

下方範例示範如何建立包含規則的新 ACL。此 ACL 接著會套用至虛擬機器端點。下方範例中的 ACL 規則將允許從遠端子網路進行存取。若要建立新的網路 ACL，並包含遠端子網路的允許規則，請開啟 Azure PowerShell ISE。複製並貼上下方的指令碼，接著使用您自己的值設定指令碼後執行。

1. 建立新的網路 ACL 物件。

		$acl1 = New-AzureAclConfig

1. 設定規則以允許從遠端子網路進行存取。在下方範例中，您可以將規則設定為 *100* (其中的優先順序高於 200 及以上) 以允許遠端子網路 *10.0.0.0/8* 存取虛擬機器端點。根據您自己的組態需求來取代值。「SharePoint ACL config」的名稱應該取代為您命名此規則的易記名稱。

		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
			–Action permit –RemoteSubnet "10.0.0.0/8" `
			–Description "SharePoint ACL config"

1. 如需其他規則，請重複執行 Cmdlet，並根據您自己的組態需求來取代值。請務必變更規則編號「Order」以反映您想要套用規則的順序。規則編號較低的優先順序高於較高的編號。

		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
			–Action permit –RemoteSubnet "157.0.0.0/8" `
			–Description "web frontend ACL config"

1. 接下來，您可以建立新的端點 (Add)，或設定現有端點 (Set) 的 ACL。在此範例中，我們將會新增稱為「web」的新虛擬機器端點，並使用 ACL 設定更新虛擬機器端點。

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
		| Update-AzureVM

1. 接下來，結合 Cmdlet 並執行指令碼。在此範例中，結合的 Cmdlet 如下所示：

		$acl1 = New-AzureAclConfig
		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
			–Action permit –RemoteSubnet "10.0.0.0/8" `
			–Description "Sharepoint ACL config"
		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
			–Action permit –RemoteSubnet "157.0.0.0/8" `
			–Description "web frontend ACL config"
		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		|Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
		|Update-AzureVM

### 設定網路 ACL 規則以允許從遠端子網路進行存取

下方範例示範移除網路 ACL 規則的方式。若要移除包含遠端子網路允許規則的網路 ACL 規則，請開啟 Azure PowerShell ISE。複製並貼上下方的指令碼，接著使用您自己的值設定指令碼後執行。

1. 第一個步驟是取得虛擬機器端點的網路 ACL 物件，然後移除 ACL 規則。在此案例中，我們依據規則 ID 進行移除。這只會從 ACL 移除規則 ID 0，並不會從虛擬機器端點移除 ACL 物件。 

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Get-AzureAclConfig –EndpointName "web" `
		| Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1

1. 接下來，您必須將網路 ACL 物件套用至虛擬機器端點，並更新虛擬機器。

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Set-AzureEndpoint –ACL $acl1 –Name "web" `
		| Update-AzureVM

### 從虛擬機器端點移除網路 ACL

在某些情況下，您可能會想要從虛擬機器端點移除網路 ACL 物件。若要這樣做，請開啟 Azure Powershell ISE。複製並貼上下方的指令碼，接著使用您自己的值設定指令碼後執行。

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Remove-AzureAclConfig –EndpointName "web" `
		| Update-AzureVM

## 另請參閱

[什麼是網路存取控制清單 (ACL)？](../virtual-networks-acl)

[如何設定與虛擬機器的通訊](http://go.microsoft.com/fwlink/?LinkId=303938)

<!---HONumber=July15_HO2-->