<properties 
	pageTitle="在 Azure 中建立及上傳 Ubuntu Linux VHD" 
	description="了解如何建立及上傳包含 Ubuntu Linux 作業系統的 Azure 虛擬硬碟 (VHD)。" 
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


# 準備執行 Azure 的 Ubuntu 虛擬機器

##必要條件##

本文假設您已將 Ubuntu Linux 作業系統安裝到虛擬硬碟。有多個工具可用來建立 .vhd 檔案，例如，像是 Hyper-V 的虛擬化解決方案。如需指示，請參閱[安裝 Hyper-V 角色及設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

**Ubuntu 安裝注意事項**

- Azure 不支援較新的 VHDX 格式。您可以使用 Hyper-V 管理員或 convert-vhd Cmdlet，將磁碟轉換為 VHD 格式。

- 安裝 Linux 系統時，建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。如果願意，您可以在資料磁碟上使用 LVM 或 [RAID](virtual-machines-linux-configure-raid.md)。

- 請勿在作業系統磁碟上設定交換磁碟分割。您可以設定 Linux 代理程式在暫存資源磁碟上建立交換檔。您可以在以下步驟中找到與此有關的詳細資訊。

- 所有 VHD 的大小都必須是 1 MB 的倍數。


## <a id="ubuntu"> </a>Ubuntu 12.04+ ##

1. 在 Hyper-V 管理員的中央窗格中，選取虛擬機器。

2. 按一下 **[連接]**，以開啟虛擬機器的視窗。

3.	取代映像中的目前儲存機制，以使用 Ubuntu 的 Azure 儲存機制。此步驟會隨著 Ubuntu 版本而略有不同。

	建議您在編輯 /etc/apt/sources.list 之前先進行備份。

		# sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

	Ubuntu 12.04：

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
		# sudo apt-get update

	Ubuntu 12.10：

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
		# sudo apt-get update

	Ubuntu 14+：

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-get update

4. 執行下列命令，將作業系統更新為最新的核心：

	Ubuntu 12.04：

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

	Ubuntu 12.10：

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot
	
	Ubuntu 14+：

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

5.	(選用) 如果 Ubuntu 系統遇到問題並重新開機，它通常會在 grub 開機提示時等候使用者輸入，這會阻止系統正常開機。若要防止此情況發生，請完成下列步驟：

	a) 開啟 /etc/grub.d/00_header 檔案。

	b) 在函數 **make_timeout()** 中，搜尋 **if ["${recordfail}" = 1 ]; then**

	c) 將此行下的陳述式變更為 **set timeout=5**。

	d) 執行 'sudo update-grub'。

6. 修改 Grub 的核心開機程式行，使其額外包含用於 Azure 的核心參數。若要執行這個動作，請在文字編輯器中開啟 "/etc/default/grub"，找到名為 `GRUB_CMDLINE_LINUX_DEFAULT` 的變數 (或視需要加入它)，然後進行編輯以使其包含以下參數：

		GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

	儲存並關閉此檔案，然後執行 '`sudo update-grub`'。這將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 技術支援團隊進行問題偵錯程序。

8.	確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。這通常是預設值。

9.	安裝 Azure Linux 代理程式：

		# sudo apt-get update
		# sudo apt-get install walinuxagent

	請注意，若已安裝 `NetworkManager` 和 `NetworkManager-gnome` 封裝，則會在安裝 `walinuxagent` 封裝時移除它們。

10.	執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

11. 在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。您現在可以將 Linux VHD 上傳至 Azure。


 

<!---HONumber=July15_HO1-->