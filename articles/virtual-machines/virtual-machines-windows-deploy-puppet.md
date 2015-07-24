<properties 
	pageTitle="關於 Puppet 和 Azure 虛擬機器" 
	description="說明如何在 Azure VM 上安裝和設定 Puppet" 
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

#關於 Puppet 和 Azure 虛擬機器

<p>Puppet Enterprise 是一個可用來建置、部署及管理基礎結構的自動化軟體。您可以用來管理 IT 基礎結構生命週期，包括：探索、佈建、作業系統和應用程式的組態管理、協調流程，以及報告。

Puppet 是一個主從式架構系統。Puppet Master 和 Puppet Enterprise 代理程式皆可透過 Azure 進行安裝：

- Puppet Master 會以預先設定、安裝於 Ubuntu 伺服器上的映像形式提供。您也可以在現有伺服器上安裝 Puppet Enterprise，但使用映像是入門的最簡單方式。您需要伺服器的相關資訊才能設定代理程式。 
- Puppet Enterprise 代理程式會以虛擬機器延伸模組的形式提供，您可以在建立虛擬機器時安裝，或將它安裝在現有的虛擬機器上。

如需指示，您可以從 [Microsoft Windows 和 Azure](http://puppetlabs.com/solutions/microsoft) 頁面下載《入門指南》。


##其他資源
[與 Microsoft Azure 和 Visual Studio 的全新整合]

[如何登入執行 Windows Server 的虛擬機器]

[如何登入執行 Linux 的虛擬機器]

[管理延伸模組]

<!--Link references-->
[與 Microsoft Azure 和 Visual Studio 的全新整合]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
[如何登入執行 Windows Server 的虛擬機器]: virtual-machines-log-on-windows-server.md
[如何登入執行 Linux 的虛擬機器]: virtual-machines-linux-how-to-log-on.md
[Azure VM Extensions and Features]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409


 

<!---HONumber=July15_HO1-->