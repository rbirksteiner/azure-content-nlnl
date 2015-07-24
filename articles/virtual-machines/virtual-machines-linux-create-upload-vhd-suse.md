<properties 
	pageTitle="在 Azure 中建立及上傳 SUSE Linux VHD" 
	description="了解如何建立及上傳包含 SUSE Linux 作業系統的 Azure 虛擬硬碟 (VHD)。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="szarkos"/>


# 準備執行 Azure 的 SLES 或 openSUSE 虛擬機器

- [準備執行 Azure 的 SLES 11 SP3 虛擬機器](#sles11)
- [準備執行 Azure 的 openSUSE 13.1+ 虛擬機器](#osuse)

##必要條件##

本文假設您已將 SUSE 或 openSUSE Linux 作業系統安裝到虛擬硬碟。有多個工具可用來建立 .vhd 檔案，例如，像是 Hyper-V 的虛擬化解決方案。如需指示，請參閱[安裝 Hyper-V 角色及設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。


**SLES / openSUSE 安裝注意事項**

 - [SUSE Studio](http://www.susestudio.com) 可讓您輕鬆建立及管理 Azure 和 Hyper-V 的 SLES / openSUSE 映像。這是建議用來自訂您自己的 SUSE 和 openSUSE 映像的方法。您也可以將 SUSE Studio Gallery 中的下列正式映像下載或複製到您專屬的 SUSE Studio：

  - [SUSE Studio Gallery 上的 SLES 11 SP3 for Azure](http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure)
  - [SUSE Studio Gallery 上的 openSUSE 13.1 for Azure](https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure)

- Azure 不支援較新的 VHDX 格式。您可以使用 Hyper-V 管理員或 convert-vhd Cmdlet，將磁碟轉換為 VHD 格式。

- 安裝 Linux 系統時，建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。如果願意，您可以在資料磁碟上使用 LVM 或 [RAID](virtual-machines-linux-configure-raid.md)。

- 請勿在作業系統磁碟上設定交換磁碟分割。您可以設定 Linux 代理程式在暫存資源磁碟上建立交換檔。您可以在以下步驟中找到與此有關的詳細資訊。

- 所有 VHD 的大小都必須是 1 MB 的倍數。


## <a id="sles11"> </a>準備執行 SUSE Linux Enterprise Server 11 SP3 ##

1. 在 Hyper-V 管理員的中央窗格中，選取虛擬機器。

2. 按一下 **[連接]**，以開啟虛擬機器的視窗。

3. 註冊您的 SUSE Linux Enterprise 系統，以允許下載更新並安裝封裝。

4. 為系統更新最新的修補程式：

		# sudo zypper update

5. 從 SLES 儲存機制安裝 Azure Linux 代理程式：

		# sudo zypper install WALinuxAgent

6. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 "/boot/grub/menu.lst"，並確定預設核心包含以下參數：

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。

7.	建議您編輯檔案 "/etc/sysconfig/network/dhcp"，並將 `DHCLIENT_SET_HOSTNAME` 參數變更如下：

		DHCLIENT_SET_HOSTNAME="no"

8.	在 "/etc/sudoers" 中，註解化或移除下列程式碼行 (如果存在的話)：

		Defaults targetpw   # ask for the password of the target user i.e. root
		ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.	確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。這通常是預設值。

10.	請不要在 OS 磁碟上建立交換空間

	Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是*暫存*磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

12. 在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。您現在可以將 Linux VHD 上傳至 Azure。


----------

## <a id="osuse"> </a>準備 openSUSE 13.1+ ##

1. 在 Hyper-V 管理員的中央窗格中，選取虛擬機器。

2. 按一下 [連接]，以開啟虛擬機器的視窗。

3. 在 Shell 上執行命令 '`zypper lr`'。若此命令傳回類似以下的輸出 (請注意，版本號碼可能會不同)：

		# | Alias                 | Name                  | Enabled | Refresh
		--+-----------------------+-----------------------+---------+--------
		1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
		2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
		3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

	則儲存機制的設定符合預期，不需要任何調整。

	如果命令傳回 "No repositories defined..."，則使用下列命令來新增這些儲存機制：

		# sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1 
		# sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
		# sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

	您接著可以重新執行命令 '`zypper lr`' 來驗證已新增的儲存機制。如果有其中一個相關的更新儲存機制未啟用，請使用下列命令加以啟用：

		# sudo zypper mr -e [NUMBER OF REPOSITORY]


4. 將核心更新為最新的可用版本：

		# sudo zypper up kernel-default

	或使用所有最新的修補程式來更新系統：

		# sudo zypper update

5.	安裝 Azure Linux 代理程式

		# sudo zypper install WALinuxAgent

6.	修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 "/boot/grub/menu.lst"，並確定預設核心包含以下參數：

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。此外，請從核心開機程式行中移除以下參數 (如果有)：

		libata.atapi_enabled=0 reserve=0x1f0,0x8

7.	建議您編輯檔案 "/etc/sysconfig/network/dhcp"，並將 `DHCLIENT_SET_HOSTNAME` 參數變更如下：

		DHCLIENT_SET_HOSTNAME="no"

8.	**重要事項：**在 "/etc/sudoers" 中，註解化或移除下列程式碼行 (如果存在的話)：

		Defaults targetpw   # ask for the password of the target user i.e. root
		ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.	確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。這通常是預設值。

10.	請不要在 OS 磁碟上建立交換空間

	Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是*暫存*磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

12. 確定 Azure Linux 代理程式會在啟動時執行：

		# sudo systemctl enable waagent.service

13. 在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。您現在可以將 Linux VHD 上傳至 Azure。


 

<!---HONumber=July15_HO1-->