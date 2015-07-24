<properties 
	pageTitle="在 Azure 中建立及上傳 CentOS 型 Linux VHD" 
	description="了解如何建立及上傳包含 CentOS 型 Linux 作業系統的 Azure 虛擬硬碟 (VHD)。" 
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

# 準備執行 Azure 的 CentOS 型虛擬機器

- [準備 Azure 的 CentOS 6.x 虛擬機器](#centos6)
- [準備 Azure 的 CentOS 7.0+ 虛擬機器](#centos7)

##必要條件##

本文假設您已將 CentOS (或類似的衍生物件) Linux 作業系統安裝到虛擬硬碟。有多個工具可用來建立 .vhd 檔案，例如，像是 Hyper-V 的虛擬化解決方案。如需指示，請參閱[安裝 Hyper-V 角色及設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。


**CentOS 安裝注意事項**

- Azure 不支援較新的 VHDX 格式。您可以使用 Hyper-V 管理員或 convert-vhd Cmdlet，將磁碟轉換為 VHD 格式。

- 安裝 Linux 系統時，建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。如果願意，您可以在資料磁碟上使用 LVM 或 [RAID](virtual-machines-linux-configure-raid.md)。

- 由於 2.6.37 以下的 Linux 核心版本有錯誤，因此較大的 VM 不支援 NUMA。這個問題主要會影響使用上游 Red Hat 2.6.32 kernel 的散發套件。手動安裝 Azure Linux 代理程式 (waagent) 將會自動停用 Linux Kernel GRUB 組態中的 NUMA。您可以在以下步驟中找到與此有關的詳細資訊。

- 請勿在作業系統磁碟上設定交換磁碟分割。您可以設定 Linux 代理程式在暫存資源磁碟上建立交換檔。您可以在以下步驟中找到與此有關的詳細資訊。

- 所有 VHD 的大小都必須是 1 MB 的倍數。


## <a id="centos6"> </a>CentOS 6.x ##

1. 在 Hyper-V 管理員中，選取虛擬機器。

2. 按一下 [連接]，以開啟虛擬機器的主控台視窗。

3. 執行下列命令以解除安裝 NetworkManager：

		# sudo rpm -e --nodeps NetworkManager

	**注意：**如果尚未安裝封裝，此命令將會失敗，並出現錯誤訊息。這是預期行為。

4.	在 `/etc/sysconfig/` 目錄中，建立名為 **network** 且包含下列文字的檔案：

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

5.	在 `/etc/sysconfig/network-scripts/` 目錄中，建立名為 **ifcfg-eth0** 且包含下列文字的檔案：

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

6.	移動 (或移除) udev 角色可防止產生乙太網路介面的靜態規則。在 Microsoft Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/


7. 要確保開機時會啟動網路服務，可執行以下命令：

		# sudo chkconfig network on


8. **僅限 CentOS 6.3**：安裝 Linux Integration Services 的驅動程式

	**重要事項：此步驟僅適用於 CentOS 6.3 和較舊的版本。** 在 CentOS 6.4+ 中，Linux Integration Services *已是核心中的可用項目*。

	a) 從 [Microsoft 下載中心](http://www.microsoft.com/download/details.aspx?id=41554)取得包含 Linux Integration Services 驅動程式的 .iso 檔案。

	b) 在 Hyper-V 管理員的 **[動作]** 窗格中，按一下 **[設定]**。

	![開啟 Hyper-V 設定](./media/virtual-machines-linux-create-upload-vhd-centos/settings.png)

	c) 在 **[硬體]** 窗格中，按一下 **[IDE 控制器 1]**。

	![使用安裝媒體新增 DVD 光碟機](./media/virtual-machines-linux-create-upload-vhd-centos/installiso.png)

	d) 在 **[IDE 控制器]** 方塊中，按一下 **[DVD 光碟機]**，再按 **[新增]**。

	e) 選取 **[映像檔]**，瀏覽至 **Linux IC v3.2.iso**，然後按一下 **[開啟]**。

	f) 在 **[設定]** 頁面中，按一下 **[確定]**。

	g) 按一下 **[連接]**，以開啟虛擬機器的視窗。

	h) 在 [命令提示字元] 視窗中，輸入下列命令：

		# sudo mount /dev/cdrom /media
		# sudo /media/install.sh
		# sudo reboot

9. 執行下列命令以安裝 python-pyasn1 封裝：

		# sudo yum install python-pyasn1

10. 如果您想要使用在 Azure 資料中心內託管的 OpenLogic 鏡像，請使用下列儲存機制來取代 /etc/yum.repos.d/CentOS-Base.repo 檔案。這也會新增包含 Azure Linux 代理程式封裝的 **[openlogic]** 儲存機制：

		[openlogic]
		name=CentOS-$releasever - openlogic packages for $basearch
		baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
		enabled=1
		gpgcheck=0
		
		[base]
		name=CentOS-$releasever - Base
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#released updates
		[updates]
		name=CentOS-$releasever - Updates
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#additional packages that may be useful
		[extras]
		name=CentOS-$releasever - Extras
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#additional packages that extend functionality of existing packages
		[centosplus]
		name=CentOS-$releasever - Plus
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#contrib - packages by Centos Users
		[contrib]
		name=CentOS-$releasever - Contrib
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

	**注意：**本指南的其他部分將假設您至少會使用 [openlogic] 儲存機制，該儲存機制稍後將用來安裝 Azure Linux 代理程式。


11.	在 /etc/yum.conf 中加入這一行：

		http_caching=packages

	若是 **CentOS 6.3**，則加入這一行：

		exclude=kernel*

12. 編輯檔案 "/etc/yum/pluginconf.d/fastestmirror.conf" 以停用 yum 模組 "fastestmirror"，然後在 `[main]` 下方輸入下列內容

		set enabled=0

13.	執行下列命令，以清除目前的 yum 中繼資料：

		# yum clean all

14. **僅限 CentOS 6.3**，使用下列命令更新核心：

		# sudo yum --disableexcludes=all install kernel

15.	修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。作法是，在文字編輯器中開啟 "/boot/grub/menu.lst"，並確定預設核心包含以下參數：

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。因為 CentOS 6 所使用核心版本的一個錯誤，這將會停用 NUMA。

	除了上述以外，我們還建議您*移除*下列參數：

		rhgb quiet crashkernel=auto

	在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。

	如有需要，您可以保留 `crashkernel` 選項的設定，但請注意，此參數將會減少 VM 中約 128MB 或以上的可用記憶體數量，這在較小的 VM 中可能會是個問題。


16.	確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。這通常是預設值。

17. 執行以下命令來安裝 Azure Linux 代理程式：

		# sudo yum install WALinuxAgent

	請注意，如果 NetworkManager 和 NetworkManager-gnome 套件沒有如步驟 2 所述遭到移除，則在安裝 WALinuxAgent 套件時會將這兩個套件移除。

18.	請不要在 OS 磁碟上建立交換空間

	Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是*暫存*磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19.	執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

20. 在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。您現在可以將 Linux VHD 上傳至 Azure。


----------


## <a id="centos7"> </a>CentOS 7.0+ ##

**CentOS 7 (和類似的衍生項目) 中的變更**

準備適用於 Azure 的 CentOS 7 虛擬機器會與 CentOS 6 極為類似，不過，其中有幾個重要差異值得注意：

 - NetworkManager 封裝不會再與 Azure Linux 代理程式發生衝突。依預設會安裝此封裝，建議您不要將它移除。
 - GRUB2 現已作為預設的開機載入器使用，因此我們已變更編輯核心參數的程序 (如下所示)。
 - XFS 現為預設的檔案系統。如有需要，您仍可使用 ext4 檔案系統。


**組態步驟**

1. 在 Hyper-V 管理員中，選取虛擬機器。

2. 按一下 [連接]，以開啟虛擬機器的主控台視窗。

3.	在 `/etc/sysconfig/` 目錄中，建立名為 **network** 且包含下列文字的檔案：

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

4.	在 `/etc/sysconfig/network-scripts/` 目錄中，建立名為 **ifcfg-eth0** 且包含下列文字的檔案：

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

5.	移動 (或移除) udev 角色可防止產生乙太網路介面的靜態規則。在 Microsoft Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6. 要確保開機時會啟動網路服務，可執行以下命令：

		# sudo chkconfig network on

7. 執行下列命令以安裝 python-pyasn1 封裝：

		# sudo yum install python-pyasn1

8. 如果您想要使用在 Azure 資料中心內託管的 OpenLogic 鏡像，請使用下列儲存機制來取代 /etc/yum.repos.d/CentOS-Base.repo 檔案。這也會新增包含 Azure Linux 代理程式封裝的 **[openlogic]** 儲存機制：

		[openlogic]
		name=CentOS-$releasever - openlogic packages for $basearch
		baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
		enabled=1
		gpgcheck=0
		
		[base]
		name=CentOS-$releasever - Base
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#released updates
		[updates]
		name=CentOS-$releasever - Updates
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#additional packages that may be useful
		[extras]
		name=CentOS-$releasever - Extras
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#additional packages that extend functionality of existing packages
		[centosplus]
		name=CentOS-$releasever - Plus
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#contrib - packages by Centos Users
		[contrib]
		name=CentOS-$releasever - Contrib
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		


	**注意：**本指南的其他部分將假設您至少會使用 [openlogic] 儲存機制，該儲存機制稍後將用來安裝 Azure Linux 代理程式。

9.	執行下列命令，以清除目前的 yum 中繼資料並安裝任何更新：

		# sudo yum clean all
		# sudo yum -y update

10.	修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。若要執行這個動作，請在文字編輯器中開啟 "/etc/default/grub" 並編輯 `GRUB_CMDLINE_LINUX` 參數，例如：

		GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

	這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。除了上述以外，我們還建議您*移除*下列參數：

		rhgb quiet crashkernel=auto

	在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。

	如有需要，您可以保留 `crashkernel` 選項的設定，但請注意，此參數將會減少 VM 中約 128MB 或以上的可用記憶體數量，這在較小的 VM 中可能會是個問題。

11. 在您參照上述完成編輯 "/etc/default/grub" 之後，請執行下列命令以重建 grub 組態：

		# sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12.	確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。這通常是預設值。

13. 執行以下命令來安裝 Azure Linux 代理程式：

		# sudo yum install WALinuxAgent

14.	請不要在 OS 磁碟上建立交換空間

	Azure Linux 代理程式可在 VM 佈建於 Azure 後，使用附加至 VM 的本機資源磁碟自動設定交換空間。請注意，資源磁碟是*暫存*磁碟，可能會在 VM 取消佈建時清空。安裝 Azure Linux 代理程式 (請參閱上一個步驟) 後，請在 /etc/waagent.conf 中適當修改下列參數：

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

16. 在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。您現在可以將 Linux VHD 上傳至 Azure。


 

<!---HONumber=July15_HO1-->