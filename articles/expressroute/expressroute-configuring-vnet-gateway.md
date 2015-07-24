<properties
   pageTitle="設定 Expressroute 的虛擬網路 | Microsoft Azure"
   description="本文將引導您逐步設定 ExpressRoute 的虛擬網路 (VNet)"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/25/2015"
   ms.author="cherylmc"/>

#  設定 ExpressRoute 的虛擬網路

1. 登入**管理入口網站**。
2. 按一下螢幕左下角的 [新增]。在導覽窗格中依序按一下 [網路服務] 和 [虛擬網路]。按一下 [Custom Create] 開始組態精靈。
3. 在 [虛擬網路詳細資料] 頁面上，輸入下列資訊。如需詳細資料頁面上設定的詳細資訊，請參閱[虛擬網路詳細資料頁面](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNetDetails)。

	- **名稱**：為虛擬網路命名。當您部署 VM 和 PaaS 執行個體時，將會使用此虛擬網路名稱，因此您可能不會想要太過複雜的名稱。
	- **位置**：位置會與您要存放資源 (VM) 的實體位置 (區域) 直接相關。例如，如果您要讓部署到此虛擬網路的 VM 實際上位於 East US 中，請選取該位置。建立關聯之後，您就無法變更與虛擬網路相關聯的區域。

4. 在 [DNS Servers and VPN Connectivity] 頁面上，輸入下列資訊，然後按一下右下角的下一步箭頭。如需此頁面上設定的詳細資訊，請參閱 [DNS 伺服器和 VPN 連線能力頁面](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETDNS)。

	- **DNS 伺服器**：輸入 DNS 伺服器名稱和 IP 位址，或從下拉式清單中選取先前註冊的 DNS 伺服器。此設定不會建立 DNS 伺服器，它可讓您指定您想要用於此虛擬網路的名稱解析的 DNS 伺服器。
	- **設定站台對站台 VPN**：選取 [**設定站台對站台 VPN**] 的核取方塊。
	- **選取 ExpressRoute**：選取 [**使用 ExpressRoute**] 核取方塊。唯有在上一個步驟選取 [***設定站台對站台 VPN***] 才會出現此選項。
	- **區域網路**：表示實體內部部署位置的本機網路。您可以選取先前建立的區域網路，或者可以建立新的區域網路。

	如果您選取現有區域網路，請略過步驟 5。

5. 如果您正在建立新的區域網路，將看到 [**站台對站台連線能力**] 頁面。如果您選取先前建立的區域網路，此頁面不會出現在精靈中，而您可以移至下一節。若要設定區域網路，請輸入下列資訊，然後按下一步箭頭。如需此頁面上設定的詳細資訊，請參閱[站台對站台連線能力頁面](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETSITE)。

	- **名稱**：您想要命名區域 (內部部署) 網站的名稱。
	- **位址空間**：包括起始 IP 和 CIDR (位址計數)。您可以指定任何位址範圍，只要不會與虛擬網路的位址範圍重疊即可。
	- **加入位址空間**：此設定與 ExpressRoute 無關。
	- 
**附註：**您必須建立 ExpressRoute 的區域網路站台。指定用於區域網路站台的位址前置詞會被忽略。透過 ExpressRoute 電路向 Microsoft 通告的位址前置詞將用於路由目的。

6. 在 [Virtual Network Address Spaces] 頁面上，輸入下列資訊，然後按一下右下角的核取記號來設定您的網路。虛擬網路位址空間有極多的相關規則，因此您也許希望參閱[虛擬網路位址空間頁面](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNET_ADDRESS)來了解詳細資訊。

	- **位址空間**：包括起始 IP 和位址計數。請確認您指定的位址空間沒有與您在區域網路上所擁有的任何位址空間重疊。
	- **新增子網路** - 包括起始 IP 和位址計數。您不需要其他子網路，但可能希望為將擁有動態 IP 位址 (DIPS) 的 VM 建立個別子網路。或者，您可以讓您的 VM 位於與其他 PaaS 執行個體不同的子網路中。
	- **新增閘道器子網路**：按一下以新增閘道器子網路。閘道器子網路僅用於虛擬網路閘道器，而且需要這項設定。***重要事項：***ExpressRoute 的閘道器子網路必須是 /28 或更大。

7. 按一下頁面底部的核取記號，然後您的虛擬網路即會開始建立。完成時，您將在管理入口網站的 [**網路**] 頁面上看到 [**狀態**] 下列出 [**已建立**]。

8. 在 [**網路**] 頁面上按一下您剛才建立的虛擬網路，然後按一下 [**儀表板**]。
9. 按一下 [儀表板] 頁面底部的 [**建立閘道器**]，然後按一下 [**是**]。

10. 當閘道器建立開始時，系統會顯示訊息來通知您閘道器已啟動。閘道器建立作業最多可能需要花費 15 分鐘的時間。
11. **將網路連結至線路。** 必須確認線路處於下列狀態和情況，才能繼續執行下列指示： 

	- ServiceProviderState: Provisioned
	- Status: Enabled

	請確認您至少已建立一個含有一個閘道器的 Azure 虛擬網路。閘道器子網路必須是 /28 或更大才能使用 ExpressRoute 連線，而且必須處於執行狀態。

			PS C:> $Vnet = "MyTestVNet"
			New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
			Provisioned

## 後續步驟
如果您想要將虛擬機器新增至虛擬網路，請參閱[如何建立自訂虛擬機器](../virtual-machines-create-custom.md)。

如果您想要深入了解 ExpressRoute，請參閱 [ExpressRoute 技術概觀](expressroute-introduction.md)。

如果您想要深入了解虛擬網路，請參閱[虛擬網路常見問題集](https://msdn.microsoft.com/library/azure/dn133803.aspx)。

 

<!---HONumber=July15_HO2-->