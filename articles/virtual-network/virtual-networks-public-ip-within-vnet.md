<properties 
   pageTitle="如何在虛擬網路中使用公用 IP 位址"
   description="了解如何設定虛擬網路以使用公用 IP 位址"
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

# 在虛擬網路 (VNet) 中使用公用 IP 位址

您現在可以將公用 IP 位址空間加入至 VNet。先前，您僅可以將 RFC 1918 位址區塊 (私人空間) 新增至 VNet。當您新增公用 IP 位址範圍時，其將會視為私人 VNet IP 位址空間的一部分，該位址空間僅能在 VNet、互連式 VNet 中，以及從內部部署位置進行連線。

新增公用 IP 位址空間的運作方式在概念上就像這樣：

![公用 IP 概念](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## 如何新增公用 IP 位址範圍？

新增公用 IP 位址範圍與新增私人 IP 位址範圍的方式相同，即使用 *netcfg* 檔案，或在入口網站中進行設定。您可以在建立 VNet 時新增公用 IP 位址範圍，或者您可以返回並在之後新增。下方範例會同時顯示在相同虛擬網路中設定的公用和私人 IP 位址空間。

![入口網站中的公用 IP 位址](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## 是否有任何限制？

系統不允許少數 IP 位址範圍：

- 224.0.0.0/4 (多點傳送)

- 255.255.255.255/32 (廣播)

- 127.0.0.0/8 (回送)

- 169.254.0.0/16 (連結-本機)

- 68.63.129.16/32 (內部 DNS)

## 後續步驟

[如何管理虛擬網路 (VNet) 屬性](../virtual-networks-settings)

[如何管理虛擬網路 (VNet) 所使用的 DNS 伺服器](../virtual-networks-manage-dns-in-vnet)

[如何刪除虛擬網路 (VNet)](../virtual-networks-delete-vnet)

<!---HONumber=July15_HO2-->