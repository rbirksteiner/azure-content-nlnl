<properties 
	pageTitle="關於 Chef 和 Azure 虛擬機器" 
	description="說明如何在 Azure 的 VM 上安裝和設定 Chef" 
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
	ms.date="05/20/2015" 
	ms.author="kathydav"/>

#關於 Chef 和 Azure 虛擬機器

Chef 提供一個可用來建置、部署及管理基礎結構的自動化軟體。您可以使用配方來管理資源，配方是提供工作指示 (例如設定 Web 伺服器) 的可重複使用定義。

Chef 是一個主從式架構系統。若要了解使用 Chef 伺服器的選項，請參閱[選擇您的安裝](http://www.getchef.com/chef/choose-your-version/) (英文)。您需要 Chef 伺服器的相關資訊才能設定用戶端。

若要在 Azure 虛擬機器上安裝 Chef 用戶端，您有下列選擇：

- 建立執行 Windows Server 2012 或 Windows Server 2012 R2 的虛擬機器時，使用 Azure 管理入口網站安裝 Chef 用戶端。如需指示，請參閱 [Microsoft Azure 入口網站](https://docs.chef.io/azure_portal.html)。
- 使用 Azure PowerShell 在現有虛擬機器上安裝 Chef 用戶端。您可以在 GitHub 上找到[指令碼](https://gist.github.com/kaustubh-d/cea1aa75baebd3615609)範例。
- 使用 Chef 外掛程式 [knife-azure](http://docs.getchef.com/plugin_knife_azure.html)，來建立虛擬機器執行個體並安裝 Chef 用戶端。   


##其他資源
[Chef 和 Microsoft Azure]

[如何登入執行 Windows Server 的虛擬機器]

[如何登入執行 Linux 的虛擬機器]

[管理延伸模組]

<!--Link references-->
[Chef 和 Microsoft Azure]: http://www.getchef.com/solutions/azure/
[如何登入執行 Windows Server 的虛擬機器]: virtual-machines-log-on-windows-server.md
[如何登入執行 Linux 的虛擬機器]: virtual-machines-linux-how-to-log-on.md
[管理延伸模組]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409


 

<!---HONumber=July15_HO1-->