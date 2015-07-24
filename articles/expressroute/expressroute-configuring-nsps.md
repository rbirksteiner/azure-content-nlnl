<properties 
   pageTitle="透過 NSP 設定 Expressroute"
   description="本教學課程帶領您透過 NSP 設定 ExpressRoute。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/29/2015"
   ms.author="cherylmc"/>

#  透過網路服務提供者設定 ExpressRoute 連線

若要透過網路服務提供者設定 ExpressRoute 連線，您需要依適當的順序完成多個步驟。這些指示將協助您：

- 建立和管理 ExpressRoute 線路
- 將虛擬網路連結至 ExpressRoute 線路

##  組態必要條件


開始設定之前，請驗證您符合下列必要條件：

- Azure 訂用帳戶
- 最新版本的 Windows PowerShell
- 下列虛擬網路需求：
	- Azure 中用於虛擬網路的一組 IP 位址首碼。
	- 內部部署的一組 IP 首碼 (可包含公用 IP 位址)。
	- 必須以 /28 子網路建立虛擬網路閘道器。
	- 虛擬網路外的另一組 IP 首碼 (/28)。將用於設定路由。您必須將這些資訊提供給網路服務提供者。
	- 網路的 AS 編號。您必須將這些資訊提供給網路服務提供者。您可以使用私人 AS 編號。如果您選擇執行此作業，此值必須大於 65000。如需 AS 編號的詳細資訊，請參閱「自發系統 (AS) 編號」。 

- 從網路服務提供者：
	- 您必須具有支援 ExpressRoute 的 VPN 服務。請洽詢網路服務提供者，以了解現有 VPN 服務是否合格。

##  部署工作流程

![網路服務提供者工作流程](./media/expressroute-configuring-nsps/expressroute-nsp-connectivity-workflow.png)

##  使用 PowerShell 進行組態設定
Windows PowerShell 是功能強大的指令碼環境，可讓您控制和自動化部署及管理 Azure 中的工作負載。如需詳細資訊，請參閱 [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx) 中的 PowerShell 文件



1. **匯入 ExpressRoute 的 PowerShell 模組。**

		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1' 

2. **取得支援的提供者、位置和頻寬清單。**

	建立線路之前，您需要有服務提供者、支援的位置和每個位置的頻寬選項等清單。下列 PowerShell Cmdlet 會傳回此資訊，在稍後的步驟中將會用到。

		PS C:> Get-AzureDedicatedCircuitServiceProvider

	傳回的資訊將與以下範例相似：

		PS C:> Get-AzureDedicatedCircuitServiceProvider
	
		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
		----                 -------------------------      --------------------------                                                                                                                                                                                   
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		British Telecom      London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     as,New York,Seattle,Silicon                                                                                                                                                                                                                 
		                     Valley,Washington                                                                                                                                                                                                                           
		                     DC,London,Hong                                                                                                                                                                                                                              
		                     Kong,Singapore,Sydney,Tokyo                                                                                                                                                                                                                 
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Level 3              London,Silicon                 200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
		Communications -     Valley,Washington DC                                                                                                                                                                                                                        
		Exchange                                                                                                                                                                                                                                                         
		Level 3              London,Silicon                 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Communications -     Valley,Washington DC                                                                                                                                                                                                                        
		IPVPN                                                                                                                                                                                                                                                            
		Orange               Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		International                                                                                                                                                                                                                                                    
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Telstra Corporation  Sydney                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Verizon              Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		

3. **提出服務金鑰要求，並將它傳遞給網路服務提供者。**

	您將使用 PowerShell Cmdlet 來提出此要求。在此範例中，我們將使用 AT&T Netbond 做為服務提供者，並且會指定位在美國矽谷的 50 Mbps ExpressRoute 線路。如果您使用不同的提供者和不同的設定，請在提出要求時替換成該資訊。

	以下是新服務金鑰的要求範例：

		##Creating a new circuit
		$Bandwidth = 50
		$CircuitName = "NetBondSVTest"
		$ServiceProvider = "at&t"
		$Location = "Silicon Valley"
		
		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location
		
		#Getting service key
		Get-AzureDedicatedCircuit

	回應如下列範例所示：

		Bandwidth                        : 50
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	您隨時可以使用 Get-AzureCircuit Cmdlet 擷取此資訊。執行呼叫時，若未指定任何參數，將會列出所有線路。ServiceKey 欄位會列出您的服務金鑰。

		PS C:> Get-AzureDedicatedCircuit
		
		Bandwidth                        : 500
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : 00-0000-0000-0000-0000000000
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	一旦完成此步驟，您將能連接 Azure 儲存體和其他服務。



4. **設定虛擬網路和閘道器。**

	請參閱[設定 ExpressRoute 的虛擬網路和閘道器](https://msdn.microsoft.com/library/azure/dn643737.aspx)。請注意，閘道器子網路必須是 /28，才能使用 ExpressRoute 連線。

5. **將網路連結至線路。**

	請務必在確認以下內容後再繼續進行下列步驟
 
	- ServiceProviderState: Provisioned
	- Status: Enabled

	請確認您至少已建立一個含有一個閘道器的 Azure 虛擬網路。該閘道器必須處於運作狀態。

		PS C:> $Vnet = "MyTestVNet"
		New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
		
		Provisioned 

<!---HONumber=July15_HO2-->