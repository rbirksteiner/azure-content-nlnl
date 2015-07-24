<properties 
   pageTitle="在多個訂用帳戶中共用 ExpressRoute 電路 | Microsoft Azure"
   description="本文將逐步引導您在多個 Azure 訂用帳戶中共用 ExpressRoute 電路。"
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
   ms.date="06/25/2015"
   ms.author="cherylmc" />

# 在多個訂用帳戶中共用 ExpressRoute 電路

多個訂用帳戶間可共用一個 ExpressRoute 電路。**圖 1** 顯示簡單的圖解，示範在多個訂用帳戶間共用 ExpressRoute 電路的運作方式。

大型雲端內的每個較小型雲端，會用來代表屬於組織內不同部門的訂用帳戶。組織內的每個部門可以使用自己的訂用帳戶來部署服務，但可以共用一個 ExpressRoute 專用電路以連接回公司網路。單一部門 (在此範例中為：IT) 可以擁有 ExpressRoute 專用電路。專用電路的擁有者需支付專用電路的連線和頻寬費用。組織內的其他訂用帳戶可以使用 ExpressRoute 電路。

**圖 1**

![訂用帳戶共用](./media/expressroute-share-circuit/IC766124.png)

## 系統管理

電路擁有者是訂用帳戶管理員/共同管理員，而 ExpressRoute 專用電路即建立在該帳戶中。電路擁有者可以授權其他訂用帳戶管理員/共同管理員 (工作流程圖中稱為電路使用者) 使用他們擁有的專用電路。一旦獲得使用組織專用電路的授權，這些電路使用者就可以將他們訂用帳戶中的 VNet 連結至專用電路。

電路擁有者能夠隨時修改及撤銷授權。如果撤銷授權，則在存取權遭撤銷的訂用帳戶中，所有連結均會被刪除。

## 工作流程

1. 電路擁有者可授權其他訂用帳戶管理員使用指定的電路。在以下範例中，電路管理員 (Contoso IT) 藉由指定另一位訂用帳戶管理員 (Contoso Sales) 的 Microsoft (Live) ID，使對方可以將最多 2 個 Vnet 連結至電路。此 Cmdlet 不會傳送電子郵件給指定的 Microsoft ID。電路擁有者必須明確通知其他訂用帳戶擁有者，告知授權已完成。

		PS C:> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -Description 'SalesTeam' -Limit 2 -MicrosoftIds 'salesadmin@contoso.com'
		
		Description         : SalesTeam 
		Limit               : 2 
		LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
		MicrosoftIds        : salesadmin@contoso.com 
		Used                : 0

1. 收到電路擁有者的通知後，獲得授權的訂用帳戶管理員即可執行以下 Cmdlet 來擷取電路的服務金鑰。在此範例中，Contoso Sales 的管理員必須先使用指定的 Microsoft ID salesadmin@contoso.com 登入。

		PS C:> Get-AzureAuthorizedDedicatedCircuit
		
		Bandwidth                        : 100
		CircuitName                      : ContosoIT
		Location                         : Washington DC
		MaximumAllowedLinks              : 2
		ServiceKey                       : 6ed7e310-1a02-4261-915f-6ccfedc416f1
		ServiceProviderName              : ###########
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled
		UsedLinks                        : 0

1. 獲得授權的訂用帳戶管理員可以執行以下 Cmdlet 來完成連結作業。

		PS C:> New-AzureDedicatedCircuitLink –servicekey 6ed7e310-1a02-4261-915f-6ccfedc416f1 –VnetName 'SalesVNET1' 
		
			State VnetName 
			----- -------- 
			Provisioned SalesVNET1

就這麼簡單。現在 Azure 上 Conto 的 Sales VNet 已連結到 ContosoIT 建立/擁有的電路。

## 管理授權

電路擁有者可以與最多 10 個 Azure 訂用帳戶共用一個電路。電路擁有者可以檢視已獲授權使用電路的人員。擁有者可以隨時撤銷授權。當電路擁有者撤銷授權時 (依 LinkAuthorizationId 來識別)，該授權允許的所有連結都會立即刪除。已連結的 VNET 會失去透過 ExpressRoute 電路與內部部署網路的連線。

	PS C:> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: 6ed7e310-1a02-4261-915f-6ccfedc416f1 
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : cc958457-c8c1-4f16-af09-e7f099da64bf 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : d972726f-c7b9-4658-8598-ad3208ac9348 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : SalesTeam 
	Limit               : 2 
	LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	
	PS C:> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -AuthorizationId 'e2bc2645-6fd4-44a4-94f5-f2e43e6953ed'


下圖顯示授權工作流程：

![訂用帳戶共用工作流程](./media/expressroute-share-circuit/IC759525.png)

## 後續步驟

如需關於 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 概觀](expressroute-introduction.md)。

<!---HONumber=July15_HO2-->