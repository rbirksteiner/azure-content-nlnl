<properties 
	pageTitle="在 Azure 中執行 Linux 的虛擬機器上設定軟體 RAID" 
	description="了解如何使用 mdadm，在 Azure 的 Linux 上設定 RAID。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	writer="szark" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/13/2015" 
	ms.author="szark"/>



# 在 Linux 上設定軟體 RAID
在 Azure 的 Linux 虛擬機器上使用軟體 RAID，以單一 RAID 裝置的形式顯示多個連接的資料磁碟，這種案例很常遇到。相較於只使用單一磁碟，這通常可用來提高效能並允許增加輸送量。


## 連接資料磁碟
設定 RAID 裝置通常需要兩個以上的空白資料磁碟。本文將不會詳細說明如何將資料磁碟連接至 Linux 虛擬機器。請參閱 Windows Azure 文章[連接磁碟](storage-windows-attach-disk.md#attachempty)，以取得如何在 Azure 上將空白資料磁碟連接至 Linux 虛擬機器的詳細指示。

>[AZURE.NOTE]ExtraSmall VM 大小不支援將多個資料磁碟連接到虛擬機器。如需支援的 VM 大小與資料磁碟數目的詳細資訊，請參閱 [Microsoft Azure 的虛擬機器和雲端服務大小](https://msdn.microsoft.com/library/azure/dn197896.aspx)。


## 安裝 mdadm 公用程式

- **Ubuntu**

		# sudo apt-get update
		# sudo apt-get install mdadm

- **CentOS & Oracle Linux**

		# sudo yum install mdadm

- **SLES 和 openSUSE**

		# zypper install mdadm


## 建立磁碟分割
在本範例中，我們將在 /dev/sdc 上建立單一磁碟分割。新磁碟分割的名稱會是 /dev/sdc1。

- 啟動 fdisk 開始建立磁碟分割

		# sudo fdisk /dev/sdc
		Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
		Building a new DOS disklabel with disk identifier 0xa34cb70c.
		Changes will remain in memory only, until you decide to write them.
		After that, of course, the previous content won't be recoverable.

		WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
				 switch off the mode (command 'c') and change display units to
				 sectors (command 'u').

- 出現提示時請按 'n'，以建立**新的**磁碟分割：

		Command (m for help): n

- 接著，請按 'p' 以建立**主要**磁碟分割：

		Command action
			e   extended
			p   primary partition (1-4)
		p

- 按 '1' 以選取磁碟分割編號 1：

		Partition number (1-4): 1

- 選取新磁碟分割的起始點，或按 `<enter>` 接受預設值，將磁碟分割置於磁碟機上可用空間的開始位置：

		First cylinder (1-1305, default 1):
		Using default value 1

- 選取磁碟分割的大小，例如，輸入 '+10G' 以建立 10 GB 的磁碟分割。或者，按 `<enter>` 建立跨越整個磁碟機的單一磁碟分割：

		Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
		Using default value 1305

- 接著，將磁碟分割的 ID 和類型 (**t**) 從預設的 ID '83' (Linux) 變更為 ID 'fd' (Linux raid auto)：

		Command (m for help): t
		Selected partition 1
		Hex code (type L to list codes): fd

- 最後，將磁碟分割資料表寫入磁碟機並結束 fdisk：

		Command (m for help): w
		The partition table has been altered!


## 建立 RAID 陣列

1. 下列範例將「分割」(RAID level 0) 位於三個不同資料磁碟 (sdc1、sdd1、sde1) 的三個磁碟分割：

		# sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
		  /dev/sdc1 /dev/sdd1 /dev/sde1

在本範例中，執行此命令後，便會建立一個名為 **/dev/md127** 的新 RAID 裝置。同時請注意，如果這些資料磁碟先前是另一個無用 RAID 陣列的一部分，則您可能需要在 `mdadm` 命令中加上 `--force` 參數。


2. 在新的 RAID 裝置上建立檔案系統

	**CentOS、Oracle Linux、openSUSE 和 Ubuntu**

		# sudo mkfs -t ext4 /dev/md127

	**SLES**

		# sudo mkfs -t ext3 /dev/md127

3. **SLES 和 openSUSE** - 啟用 boot.md 並建立 mdadm.conf

		# sudo -i chkconfig --add boot.md
		# sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf

	>[AZURE.NOTE]在 SUSE 系統上進行這些變更之後，可能需要重新開機。


## 將新的檔案系統新增至 /etc/fstab

**注意：**不當編輯 /etc/fstab 檔案會導致系統無法開機。如果不確定，請參閱散發套件的文件，以取得如何適當編輯此檔案的相關資訊。在編輯之前，也建議先備份 /etc/fstab 檔案。

1. 建立新檔案系統所需的掛接點，例如：

		# sudo mkdir /data

2. 編輯 /etc/fstab 時，應使用 **UUID** (而非使用裝置名稱) 來參考檔案系統。使用 `blkid` 公用程式來決定新檔案系統的 UUID：

		# sudo /sbin/blkid
		...........
		/dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"

3. 在文字編輯器中開啟 /etc/fstab，並為新檔案系統新增項目，例如：

		UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2

	或者在 **SLES 和 openSUSE** 上：

		/dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2

	接著，儲存並關閉 /etc/fstab。

4. 測試 /etc/fstab 項目是否正確：

		# sudo mount -a

	如果此命令產生一則錯誤訊息，請檢查 /etc/fstab 檔案中的語法。

	接下來，執行 `mount` 命令，以確保已掛接檔案系統：

		# mount
		.................
		/dev/md127 on /data type ext4 (rw)

5. (選擇性) 保全開機參數

	**fstab 組態**

	許多散發套件包含 `nobootwait` 或 `nofail` 掛接參數，可加入至 /etc/fstab 檔案。這些參數容許發生掛接特定檔案系統失敗，並容許 Linux 系統繼續開機，即使它無法正確地掛接 RAID 檔案系統。請查閱散發套件的文件，以取得這些參數的相關資訊。

	範例 (Ubuntu)：

		UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2

	**Linux 開機參數**

	除了上述參數之外，即使 RAID 看起來已損壞或效能不佳，核心參數 "`bootdegraded=true`" 仍可讓系統開機，例如，將資料磁碟機從虛擬機器中不當移除。依預設，這也會造成無法開機的系統。

	請參閱散發套件的文件，以取得如何正確編輯核心參數的相關資訊。例如，在許多散發套件 (CentOS、Oracle Linux、SLES 11) 中，可手動將這些參數加入至 "`/boot/grub/menu.lst`" 檔案。在 Ubuntu 上，可將此參數加入至 "/etc/default/grub" 上的 `GRUB_CMDLINE_LINUX_DEFAULT` 變數。

 

<!---HONumber=July15_HO1-->