<properties 
	pageTitle="在 Azure 中建立一個執行 Windows 的自訂虛擬機器" 
	description="了解如何在 Azure 中建立一個執行 Windows 的自訂虛擬機器。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>


<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="kathydav"/>

#如何建立一個執行 Windows 的自訂虛擬機器

「自訂」虛擬機器是指您使用 [從組件庫] 選項建立的虛擬機器，因為您可以有比 [快速建立] 選項更多的設定選擇。這些選擇包括：

- 將 VM 連線到虛擬網路
- 安裝 VM 代理程式和擴充功能，例如反惡意程式碼 
- 將 VM 加入現有的雲端服務
- 將 VM 加入現有的儲存體帳戶
- 將 VM 加入可用性設定組

> [AZURE.IMPORTANT]如果要讓虛擬機器使用虛擬網路，以便依主機名稱來連接虛擬機器，或設定跨單位連線，則必須在建立虛擬機器時指定虛擬網路。只有在建立虛擬機器時，才能將虛擬機器設定為加入虛擬網路。如需虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](http://go.microsoft.com/fwlink/p/?LinkID=294063)。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]


 

<!---HONumber=July15_HO2-->