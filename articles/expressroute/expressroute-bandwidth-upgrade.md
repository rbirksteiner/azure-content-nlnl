<properties 
   pageTitle="以動態方式升級 ExpressRoute 頻寬 | Microsoft Azure"
   description="如何在不停機的情況下以動態方式增加 ExpressRoute 電路的頻寬大小。"
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
   ms.date="06/03/2015"
   ms.author="cherylmc" />

# 在不停機的情況下以動態方式升級 ExpressRoute 電路頻寬

您可以在完全不停機的情況下增加 ExpressRoute 電路的大小。這些指示將協助您使用 PowerShell 更新 ExpressRoute 電路的頻寬。如需限制的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

##  組態必要條件

開始設定之前，請驗證您符合下列必要條件：

- Azure 訂用帳戶
- 最新版的 Azure PowerShell
- 已設定且正常運作的使用中 ExpressRoute 電路


##  使用 PowerShell 進行組態設定

Windows PowerShell 是功能強大的指令碼環境，可讓您控制和自動化部署及管理 Azure 中的工作負載。如需詳細資訊，請參閱 [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx) 中的 PowerShell 文件。

1. **匯入 ExpressRoute 的 PowerShell 模組。**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **取得 ExpressRoute 電路的詳細資料**

	您可以使用下列 PowerShell Cmdlet 取得 ExpressRoute 電路的詳細資料：
		

    	PS C:> Get-AzureDedicatedCircuit
	
	此命令會傳回訂用帳戶中所有已建立的電路清單。如果您擁有服務金鑰，可以使用下列命令取得特定 ExpressRoute 電路的詳細資料：

		 PS C:> Get-AzureDedicatedCircuit -ServiceKey <skey>

	將 <skey> 取代為實際的服務金鑰。
	
		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


3. **在 Microsoft 端增加 ExpressRoute 電路的頻寬**
	
	請查閱 [ExpressRoute 常見問題集](expressroute-faqs.md)頁面，以取得提供者支援的頻寬選項。您可以挑選任何比現有電路規模還大的大小。一旦決定需要的大小後，您可以使用下列命令來調整電路大小。

		PS C:> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	我們會在 Microsoft 端調整電路的大小。請注意，從這個階段起，我們將開始計算更新頻寬選項的費用。

4. **在服務提供者端增加 ExpressRoute 電路的頻寬**

	請連絡連線服務提供者 (NSP / EXP)，並提供更新之頻寬的相關資訊。請依照服務提供者開立的訂單更新程序來完成工作。

 

<!---HONumber=July15_HO2-->