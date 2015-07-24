<properties 
   pageTitle="管理虛擬網路 (VNet) 所使用的 DNS 伺服器"
   description="了解如何新增和移除虛擬網路 (VNet) 中的 DNS 伺服器"
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

# 管理虛擬網路 (VNet) 所使用的 DNS 伺服器

您可以在管理入口網站或網路組態檔中，管理用於 VNet 的 DNS 伺服器清單。您可以為每個 VNet 新增最多 12 部 DNS 伺服器。指定 DNS 伺服器時，請務必確認您針對環境以正確的順序列出您的 DNS 伺服器。DNS 伺服器清單不會使用循環配置資源，而會依其指定的順序來使用。如果可以連接至清單上的第一部 DNS 伺服器，則用戶端將使用該 DNS 伺服器，無論 DNS 伺服器是否運作正常。若要變更虛擬網路的 DNS 伺服器順序，請從清單中移除 DNS 伺服器，然後以您想要的順序將其重新加入。

>[AZURE.WARNING]更新 DNS 清單之後，您必須重新啟動位於虛擬網路的虛擬機器，以便讓其挑選新的 DNS 伺服器設定。虛擬機器將繼續使用其目前的組態，直到重新啟動為止。

## 使用管理入口網站編輯虛擬網路的 DNS 伺服器清單

1. 登入**管理入口網站**。

1. 在瀏覽窗格中按一下 [**網路**]，然後按一下 [**名稱**] 資料行中的虛擬網路名稱。

1. 按一下 [**設定**]。

1. 在 **DNS 伺服器**中，您可以設定下列項目：

	- **登錄 (add) 新的 DNS 伺服器 –** 僅需在方塊中輸入名稱和 IP 位址。這會將 DNS 伺服器新增至虛擬網路 DNS 伺服器清單，並且也會向 Azure 登錄 DNS 伺服器。

	- **新增先前登錄的 DNS 伺服器 –** 如果您已向 Azure 登錄 DNS 伺服器，則可以從預先填入的清單進行選取。

	- **從虛擬網路移除 DNS 伺服器 –** 在您想要移除的伺服器旁，按一下 [X]。請注意，這僅會從此虛擬網路清單移除伺服器。DNS 伺服器會在 Azure 中保持登錄狀態，以便您其他的虛擬網路使用。若要從您的訂用帳戶中刪除 DNS 伺服器，請移至 **[網路] -> [DNS 伺服器]** 頁面。

	- **重新排序 DNS 伺服器 –** 移除所有列出的 DNS 伺服器，然後將其以您想要的順序重新加入。請記住，這不是循環配置資源 DNS 清單。

	- **重新命名 DNS 伺服器 –** 反白清單中的 DNS 伺服器，然後輸入新名稱。這會在 Azure 中登錄新的 DNS 伺服器，以及將其新增至您虛擬網路的 DNS 伺服器清單。舊的 DNS 伺服器及其 IP 位址會在 Azure 保持登錄狀態。如果您不會將其用於任何其他虛擬網路，則可以在 [**DNS 伺服器**] 頁面上將其刪除。

1. 按一下頁面底部的 [**儲存**] 來儲存新的 DNS 伺服器組態。

1. 重新啟動位於虛擬網路的虛擬機器，以便取得新的 DNS 設定。

## 編輯使用網路組態檔的 DNS 伺服器清單

若要使用網路組態檔來編輯 DNS 伺服器清單，您需要先從管理入口網站匯出您的組態設定。接下來，您需要編輯網路組態檔，並透過管理入口網站將它匯入回來。下方是完成此程序的高階步驟清單。

1. 將您的虛擬網路設定匯出至網路組態檔。如需匯出網路組態設定的詳細資訊，請參閱＜[將虛擬網路設定匯出至網路組態檔](https://msdn.microsoft.com/library/azure/dn133804.aspx)＞。

1. 指定您虛擬網路的 DNS 伺服器資訊。如需有關指定 DNS 伺服器的詳細資訊，請參閱＜[在虛擬網路組態檔中指定 DNS 伺服器](https://msdn.microsoft.com/library/windowsazure/jj156098.aspx)＞。如需有關網路組態檔的詳細資訊，請參閱＜[Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)＞和＜[使用網路組態檔設定虛擬網路](https://msdn.microsoft.com/library/azure/jj156097.aspx)＞。

1. 匯入網路組態檔。如需匯入網路組態檔的詳細資訊，請參閱＜[匯入網路組態檔](https://msdn.microsoft.com/library/azure/jj156213.aspx)＞。

1. 重新啟動位於虛擬網路的虛擬機器，以便取得新的 DNS 設定。

## 後續步驟

[如何管理虛擬網路 (VNet) 屬性](../virtual-networks-settings)

[如何在虛擬網路中使用公用 IP 位址](../virtual-networks-public-ip-within-vnet)

[如何刪除虛擬網路 (VNet)](../virtual-networks-delete-vnet)

<!---HONumber=July15_HO2-->