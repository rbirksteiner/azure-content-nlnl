<properties
	pageTitle="將磁碟附加至在 Azure 中執行 Linux 的虛擬機器"
	description="了解如何將資料磁碟附加至 Azure 虛擬機器，並初始化磁碟以便開始使用。"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/29/2015"
	ms.author="kathydav"/>

# 如何將資料磁碟連接至 Linux 虛擬機器

您可以附加空的磁碟和含有資料的磁碟。在這兩種情況下，磁碟實際上是位於 Azure 儲存體帳戶中的 .vhd 檔案。另外，在這兩種情況下，當您附加磁碟之後，磁碟必須完成初始化才能使用。

> [AZURE.NOTE]最好使用一或多個不同的磁碟來儲存虛擬機器的資料。當您建立 Azure 虛擬機器時，它會有作業系統磁碟和暫存磁碟。**請勿使用暫存磁碟來儲存資料。** 顧名思義，它只提供暫存儲存空間。它並不提供備援或備份，因為它不在 Azure 儲存體內。暫存磁碟通常是由 Azure Linux 代理程式管理，並自動掛接到 **/mnt/resource** (或 Ubuntu 映像中的**/mnt**)。換句話說，在 Linux 上，核心可能會將資料磁碟命名為 `/dev/sdc`。若是如此，您將需要分割、格式化及掛接該資源。如需詳細資訊，請參閱 [Azure Linux 代理程式使用者指南][Agent]。

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## 做法：在 Linux 中初始化新的資料磁碟

1. 連接至虛擬機器。如需指示，請參閱[如何登入執行 Linux 的虛擬機器][Logon]。



2. 在 SSH 視窗中，輸入下列命令，然後輸入您為了管理虛擬機器而建立之帳戶的密碼：

		# sudo grep SCSI /var/log/messages

	>[AZURE.NOTE]針對最新的 Ubuntu 散發套件，您可能需要使用 `sudo grep SCSI /var/log/syslog`，因為預設可能會停用記錄到 `/var/log/messages` 的功能。

	在出現的訊息中，您可以找到最後一個新增之資料磁碟的識別碼。



	![取得磁碟訊息](./media/virtual-machines-linux-how-to-attach-disk/DiskMessages.png)



3. 在 SSH 視窗中，輸入下列命令來建立新的裝置，然後輸入帳戶密碼：

		# sudo fdisk /dev/sdc

	>[AZURE.NOTE]在此範例中，如果 /sbin 或 /usr/sbin 不在您的 `$PATH` 中，您可能需要在部份散發套件上使用 `sudo -i`。


4. 輸入 **n** 建立新的磁碟分割。


	![建立新的裝置](./media/virtual-machines-linux-how-to-attach-disk/DiskPartition.png)

5. 輸入 **p** 將磁碟分割設為主要磁碟分割，輸入 **1** 設為第一個磁碟分割，然後按 Enter 鍵接受預設的磁柱值。


	![建立磁碟分割](./media/virtual-machines-linux-how-to-attach-disk/DiskCylinder.png)



6. 輸入 **p** 查看目前分割之磁碟的詳細資料。


	![列出磁碟資訊](./media/virtual-machines-linux-how-to-attach-disk/DiskInfo.png)



7. 輸入 **w** 寫入磁碟的設定。


	![寫入磁碟變更](./media/virtual-machines-linux-how-to-attach-disk/DiskWrite.png)

8. 在新的磁碟分割上建立檔案系統。例如，輸入下列命令，然後輸入帳戶密碼：

		# sudo mkfs -t ext4 /dev/sdc1

	![建立檔案系統](./media/virtual-machines-linux-how-to-attach-disk/DiskFileSystem.png)

	>[AZURE.NOTE]請注意，SUSE Linux Enterprise 11 系統只支援對 ext4 檔案系統的唯讀存取。針對這些系統，建議您將新檔案系統格式化為 ext3，而非 ext4。


9. 建立目錄以掛接新的檔案系統。例如，輸入下列命令，然後輸入帳戶密碼：

		# sudo mkdir /datadrive


10. 輸入下列命令來掛接磁碟機：

		# sudo mount /dev/sdc1 /datadrive

	資料磁碟現在可以當做 **/datadrive** 來使用。


11. 將新的磁碟機新增至 /etc/fstab：

	為了確保重新開機之後自動重新掛接磁碟機，必須將磁碟機新增至 /etc/fstab 檔案。此外，強烈建議在 /et/fstab 中使用全域唯一識別碼 (Universally Unique IDentifier, UUID) 來參考磁碟機，而不只是裝置名稱 (例如，/dev/sdc1)。若要尋找新磁碟機的 UUID，您可以使用 **blkid** 公用程式：

		# sudo -i blkid

	輸出類似如下範例：

		/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
		/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
		/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


	>[AZURE.NOTE]不當編輯 **/etc/fstab** 檔案會導致系統無法開機。如果不確定，請參閱散發套件的文件，以取得如何適當編輯此檔案的相關資訊。在編輯之前，也建議先備份 /etc/fstab 檔案。

	接下來，在文字編輯器中開啟 **/etc/fstab** 檔案。請注意，/etc/fstab 是系統檔案，因此您需要使用 `sudo` 才能編輯這個檔案，例如：

		# sudo vi /etc/fstab

	在此範例中，我們使用先前步驟所建立之新的 **/dev/sdc1** 裝置的 UUID 值，並使用掛接點 **/datadrive**。在 **/etc/fstab** 檔案的結尾加入以下程式碼：

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2

	或者，在基於 SUSE Linux 的系統上，您可能需要使用稍微不同的格式：

		/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2

	現在，您可以測試檔案系統是否適當掛接，方法是取消掛接檔案系統，再重新掛接，例如使用先前步驟中建立的範例掛接點 `/datadrive`：

		# sudo umount /datadrive
		# sudo mount /datadrive

	如果 `mount` 命令發生錯誤，請檢查 /etc/fstab 檔案的語法是否正確。如果還有建立其他資料磁碟機或磁碟分割，同樣也需要分別在 /etc/fstab 中輸入。


>[AZURE.NOTE]後續移除資料磁碟而不編輯 fstab，可能會造成 VM 無法開機。如果這是常見情況，那麼多數散發套件會提供 `nofail` 和/或 `nobootwait` fstab 選項，即使磁碟在開機時無法掛接，也能讓系統開機。請查閱散發套件的文件，以取得這些參數的相關資訊。

## 其他資源
[如何登入執行 Linux 的虛擬機器][Logon]


<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-how-to-log-on.md
 

<!---HONumber=July15_HO1-->