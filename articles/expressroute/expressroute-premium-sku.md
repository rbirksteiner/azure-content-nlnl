<properties 
   pageTitle="如何啟用或停用 ExpressRoute Premium 附加元件 |Microsoft Azure"
   description="如何啟用或停用 ExpressRoute 電路的 ExpressRoute Premium 附加元件。ExpressRoute Premium 可讓您新增多達 10,000 個路由進行公用和私人對等互連，以及將多達 10 個虛擬網路加入至您的 ExpressRoute 電路。您也可以將一個區域中的虛擬網路連結到另一個區域中的 ExpressRoute 電路。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/02/2015"
   ms.author="cherylmc" />

# 設定 ExpressRoute 電路的 ExpressRoute Premium 附加元件

ExpressRoute Premium 是下面所列功能的集合：

 - 將公用對等互連和私人對等互連的路由表限制，從 4000 個路由提高到 10,000 個路由。
 - 提高可連線到 ExpressRoute 電路的虛擬網路 (VNet) 數目 (預設值為 10)。 
 - 透過 Microsoft 核心網路的全球連線。您現在能夠將某一個地緣政治區域中的 VNet 與另一個區域中的 ExpressRoute 電路連結。**範例：**您可以將在歐洲西部建立的 VNet created 連結至在矽谷建立的 ExpressRoute 電路。

如需 ExpressRoute Premium 附加元件的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)頁面。如需成本相關資訊，請參閱[定價詳細資料](http://azure.microsoft.com/pricing/details/expressroute/)頁面。

以下指示協助您執行下列動作：

- 建立已啟用 Premium 附加元件的 ExpressRoute 電路。
- 更新現有的 ExpressRoute 電路以啟用 Premium 附加元件。
- 停用電路的 ExpressRoute Premium 附加元件。


## 建立已啟用 Premium 附加元件功能的 ExpressRoute 電路

###  開始之前

開始設定之前，請確認您具有下列必要條件：

- Azure 訂用帳戶
- 最新版的 Azure PowerShell

###  1.匯入 ExpressRoute 的 PowerShell 模組

Windows PowerShell 是功能強大的指令碼環境，可讓您控制和自動化部署及管理 Azure 中的工作負載。如需詳細資訊，請參閱 [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx) 中的 PowerShell 文件。

使用以下 Cmdlet 來匯入 ExpressRoute 的 PowerShell 模組：


	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'


### 2.設定已啟用 Premium 附加元件功能的新 ExpressRoute 電路

您可以在建立階段建立已啟用 Premium 附加元件的 ExpressRoute 電路。遵循有關如何透過 [NSP](expressroute-configuring-nsps.md) 或 [EXP](expressroute-configuring-exps.md) 建立 ExpressRoute 電路的指示。New-AzureDedicatedCircuit Cmdlet 中有新的選擇性參數可讓您指定 SKU。SKU 可以是 Standard 或 Premium。預設值為 Standard。在 SKU 上傳遞為 Premium，將會啟用具有 Premium 附加元件功能的電路。


		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -Sku Premium


### 3.確認已啟用 ExpressRoute Premium 附加元件
您可以查看您的電路是否已啟用 ExpressRoute Premium 附加元件。在以下範例中，ExpressRoute 電路尚未啟用 ExpressRoute Premium 附加元件功能。如果已啟用附加元件，SKU 將顯示為 ***Premium***。

		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled




## 啟用現有 ExpressRoute 電路的 ExpressRoute Premium 附加元件
您可以針對您已建立的任何 ExpressRoute 電路，啟用 ExpressRoute Premium 附加元件功能。


1. **取得 ExpressRoute 電路的詳細資料**

	您可以使用下列 PowerShell Cmdlet 取得 ExpressRoute 電路的詳細資料：
		

    	PS C:> Get-AzureDedicatedCircuit
	
	此命令會傳回您在訂閱中建立的所有電路清單。如果您有服務金鑰，可以使用下列命令來取得特定 ExpressRoute 電路的詳細資料。

		 PS C:> Get-AzureDedicatedCircuit -ServiceKey <skey>

	以實際的服務金鑰取代 <skey>。
	
		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


2. **啟用電路的 ExpressRoute Premium 附加元件**


	您可以使用下列 PowerShell Cmdlet，為現有的電路啟用 ExpressRoute Premium 附加元件：
	
		PS C:> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

	您的電路現在會啟用 ExpressRoute Premium 附加元件功能。請注意，順利執行命令後，我們會開始向您收取 Premium 附加功能的費用。


## 停用 ExpressRoute 電路的 ExpressRoute Premium 附加元件

您可以對已啟用 ExpressRoute Premium 附加元件的 ExpressRoute 電路停用 Premium 附加元件。

**注意**：您必須先確定您已停止使用 Premium 附加元件功能清單中所列的功能，才可以這麼做。如果您有 10 個以上連結至您的電路的 Vnet，我們將無法達成您停用 Premium 附加元件的要求。如果您在停用 Premium 附加元件後向我們通告大於 5000 個路由，您也會看到我們將捨棄 BGP 工作階段。

1. **取得 ExpressRoute 電路的詳細資料**

	您可以使用下列 PowerShell Cmdlet 取得 ExpressRoute 電路的詳細資料：
		

    	PS C:> Get-AzureDedicatedCircuit
	
	此命令會傳回您在訂閱中建立的所有電路清單。如果您有服務金鑰，可以使用下列命令來取得特定 ExpressRoute 電路的詳細資料。

		 PS C:> Get-AzureDedicatedCircuit -ServiceKey <skey>

	以實際的服務金鑰取代 <skey>。
	
		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled


3. **停用電路的 ExpressRoute Premium 附加元件**


	您可以使用下列 PowerShell Cmdlet，為現有的電路停用 ExpressRoute Premium 附加元件：
	
		PS C:> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Standard

	您的電路現在已停用 ExpressRoute Premium 附加元件。


 

<!---HONumber=July15_HO2-->