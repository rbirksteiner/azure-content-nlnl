<properties 
	pageTitle="登入執行 Windows Server 的虛擬機器" 
	description="了解如何使用 Azure 管理入口網站來登入執行 Windows Server 的虛擬機器。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/12/2015" 
	ms.author="kathydav"/>


# 如何登入執行 Windows Server 的虛擬機器#

您會使用 Azure 入口網站中的 [連線] 按鈕啟動「遠端桌面」工作階段。(對於 Linux VM，請參閱[如何登入執行 Linux 的虛擬機器](virtual-machines-linux-how-to-log-on.md))。

## 如何登入

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## 疑難排解秘訣

以下是一些可以快速嘗試的方法：

如果是遠端桌面連線的問題，請嘗試由入口網站重設組態。從虛擬機器儀表板的**快速概覽**底下，按一下 [重設遠端組態]。

如果是您的密碼有問題，請嘗試從入口網站重設密碼。從虛擬機器儀表板的**快速概覽**底下，按一下 [重設密碼]。

如果這些都無法解決問題，您必須進行更深入的疑難排解。如需相關指示，請參閱[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](virtual-machines-troubleshoot-remote-desktop-connections.md)。
 
 

<!---HONumber=July15_HO2-->