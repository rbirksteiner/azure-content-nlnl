<properties 
   pageTitle="如何刪除虛擬網路 (VNet)"
   description="了解如何刪除現有 VNet"
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
   ms.date="05/29/2015"
   ms.author="telmos" />

# 如何刪除虛擬網路 (VNet)

如果您想要刪除 VNet，僅按一下 [**刪除**] 無法完成刪除。您必須先進行幾項操作：

1. **儲存設定 (選擇性) –** 如果您要將虛擬網路設定儲存至本機檔案，則必須先匯出組態檔才能刪除虛擬網路。請參閱＜[將虛擬網路設定匯出至網路組態檔](https://msdn.microsoft.com/library/azure/dn133804.aspx)＞以取得詳細資訊。儲存設定可讓您未來在需要時重新建立 VNet。

1. **刪除虛擬網路閘道 –** 如果您設定 VNet 的閘道，則必須先刪除閘道才能刪除 VNet。若要刪除虛擬網路閘道，請移至您虛擬網路的 [儀表板] 頁面。按一下頁面底部的 [**刪除閘道**]。
						
	您可能必須等候 5-10 分鐘的閘道刪除，才能繼續進行接下來的步驟。

1. **刪除雲端服務、網站和 VM –** 如果您已在 VNet 中部署任何物件，則您必須先刪除那些物件才能刪除 VNet。

1. **刪除虛擬網路 –** 按一下 [*名稱*] 資料列右方來反白您想要刪除的 VNet，然後按一下頁面底部的 [**刪除**]。遵循螢幕上的指示。

1. **此外 –** 您也可以在刪除虛擬網路之後選擇刪除任何區域網路設定、DNS 伺服器和同質群組。
 

<!---HONumber=July15_HO2-->