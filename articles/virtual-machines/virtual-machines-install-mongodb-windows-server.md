<properties 
	pageTitle="在執行 Windows Server 的虛擬機器上安裝 MongoDB" 
	description="了解如何在執行 Windows Server 的 Azure VM 上安裝 MongoDB。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="kathydav"/>

#在執行 Windows Server 的虛擬機器上安裝 MongoDB

[MongoDB][MongoDB] (英文) 是受歡迎的高效能開放原始碼 NoSQL 資料庫。您可以使用 [Azure 管理入口網站][AzureManagementPortal]，從映像庫建立一個執行 Windows Server 的虛擬機器。然後在虛擬機器上安裝及設定 MongoDB 資料庫。

本文探討如何：

- 使用管理入口網站從映像庫建立執行 Windows Server 的虛擬機器
- 使用遠端桌面連接虛擬機器
- 將資料磁碟連接至虛擬機器
- 在虛擬機器上安裝 MongoDB

## 建立執行 Windows Server 的虛擬機器

下列是一般指示，您可以透過建立端點以允許遠端存取 MongoDB，來修改這些指示。(您也可以稍後再建立端點，會在安裝 MongoDB 的指示之後加以說明。) 在精靈的最後一個頁面上，新增端點並進行如下所示的設定：

- 將它命名為 **Mongo**
- 使用 **TCP** 作為通訊協定
- 將公用和私用連接埠設定為 **27017**。
 
[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## 連接資料磁碟
若要為虛擬機器提供儲存體，請連接並初始化資料磁碟，這樣 Windows 才能使用該磁碟。您可以連接現有磁碟 (如果您已擁有想要使用的資料的話)，或連接空白磁碟。

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

如需初始化磁碟的指示，請參閱[如何將資料磁碟附加至 Windows 虛擬機器](storage-windows-attach-disk.md)中的「作法：在 Windows Server 中初始化新的資料磁碟」。

## 在虛擬機器上安裝及執行 MongoDB 

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

##摘要
在本教學課程中，您已了解如何建立執行 Windows Server 的虛擬機器、從遠端連線至此虛擬機器，以及連接資料磁碟。同時也已了解如何在 Windows 虛擬機器上安裝及設定 MongoDB。如需有關 MongoDB 的詳細資訊，請參閱 [MongoDB 文件][MongoDocs] (英文)。

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com
 

<!---HONumber=July15_HO1-->