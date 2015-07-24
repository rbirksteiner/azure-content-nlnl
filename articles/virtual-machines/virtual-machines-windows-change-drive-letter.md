<properties 
	pageTitle="如何變更 Windows 暫存磁碟的磁碟機代號" 
	description="說明如何在 Azure 的 Windows VM 上重新對應暫存磁碟" 
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
	ms.date="05/27/2015" 
	ms.author="kathydav"/>

#如何變更 Windows 暫存磁碟的磁碟機代號

如果您需要使用 D 磁碟機來儲存資料，請遵循下列指示，使用不同的磁碟機作為暫存磁碟。切勿使用暫存磁碟機儲存需要保留的資料。

在開始之前，您需要將資料磁碟連接至虛擬機器，您才能在此程序期間儲存 Windows 分頁檔 (pagefile.sys)。如果您沒有資料磁碟，請參閱[如何將資料磁碟連接至 Windows 虛擬機器][Attach]。如需如何找出哪些磁碟已連接的指示，請參閱[關於 Azure 中的虛擬機器磁碟][Disks]的＜管理您的磁碟＞。

如果您要使用 D 磁碟機現有的資料磁碟，請確定已將 VHD 上傳至儲存體帳戶。如需指示，請參閱[建立 Windows Server VHD 並上傳至 Azure][VHD] 中的步驟 3 和 4。

> [AZURE.WARNING]如果您調整虛擬機器大小，並將虛擬機器移至不同的主機，則暫存磁碟機將變更回 D 磁碟機。

##變更磁碟機代號

1. 登入虛擬機器。如需詳細資訊，請參閱[如何登入執行 Windows Server 的虛擬機器][Logon]。

2. 將 pagefile.sys 從 D 磁碟機移至另一個磁碟機。

3. 重新啟動虛擬機器。

4. 重新登入，並將磁碟機代號從 D 變更為 E。

5. 在 [Azure 管理入口網站](http://manage.windowsazure.com)中，連接現有的資料磁碟或空的資料磁碟。

6.	重新登入虛擬機器、初始化磁碟，並指定 D 作為剛連接磁碟的磁碟機代號。

7.	驗證 E 是否已對應至暫存磁碟。

8.	將 pagefile.sys 從其他磁碟機移至 E 磁碟機。

## 其他資源
[如何登入執行 Windows Server 的虛擬機器][Logon]

[如何從虛擬機器卸離資料磁碟][Detach]

[關於 Azure 儲存體帳戶][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md

[Disks]: ../dn790303.aspx

[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md


 

<!---HONumber=July15_HO1-->