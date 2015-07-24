<properties 
	pageTitle="將磁碟附加至虛擬機器 | Azure" 
	description="了解如何將資料磁碟附加至 Azure 虛擬機器，並初始化磁碟以便開始使用。" 
	services="virtual-machines, storage" 
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
	ms.date="04/24/2015" 
	ms.author="kathydav"/>

# 如何將資料磁碟附加至 Windows 虛擬機器

您可以連結空的磁碟和含有資料的磁碟。在這兩種情況下，磁碟實際上是位於 Azure 儲存體帳戶中的 .vhd 檔案。另外，在這兩種情況下，當您附加磁碟之後，磁碟必須完成初始化才能使用。

> [AZURE.NOTE]最好使用一或多個不同的磁碟來儲存虛擬機器的資料。當您建立 Azure 虛擬機器時，它會有一個作業系統的磁碟對應至 C 磁碟機，還有一個暫存磁碟對應至 D 磁碟機。**請勿使用 D 磁碟機來儲存資料。** 顧名思義，它只提供暫存儲存空間。它並不提供備援或備份，因為它不在 Azure 儲存體內。

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>做法：在 Windows Server 中初始化新的資料磁碟

1. 連接至虛擬機器。如需指示，請參閱[如何登入執行 Windows Server 的虛擬機器][logon]。

2. 登入虛擬機器之後，開啟 [伺服器管理員]。在左窗格中，選取 [File and Storage Services]。

	![開啟伺服器管理員](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. 展開功能表，然後選取 [磁碟]。

4. [磁碟] 區段會列出磁碟 0、磁碟 1 和磁碟 2。磁碟 0 是 OS 磁碟、磁碟 1 是暫存磁碟 (不應用於資料儲存體)，而磁碟 2 是您已連結至虛擬機器的資料磁碟。資料磁碟的容量是 5 GB，端視您連接磁碟時指定的容量而定。以滑鼠右鍵按一下磁碟 2，然後選取 [初始化]。

5.	初始化磁碟時，您會收到將清除所有資料的通知。按一下 [是]，認可此警告並初始化磁碟。接著，再次以滑鼠右鍵按一下磁碟 2，然後選取 [新增磁碟區]。

6.	使用預設值完成精靈。當精靈完成時，[磁碟區] 區段會列出新的磁碟區。磁碟此時將上線，可用來儲存資料。

	![成功初始化磁碟區](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE]虛擬機器的大小會決定您可以連結的磁碟數目。如需詳細資訊，請參閱[雲端服務和虛擬機器大小](https://msdn.microsoft.com/library/azure/dn197896.aspx)。

[logon]: virtual-machines-log-on-windows-server.md

<!---HONumber=July15_HO2-->