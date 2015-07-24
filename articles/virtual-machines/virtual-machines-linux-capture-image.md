<properties
	pageTitle="對執行 Linux 的虛擬機器擷取映像"
	description="了解如何對執行 Linux 的 Azure 虛擬機器 (VM) 擷取映像。"
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
	ms.date="06/11/2015" 
	ms.author="kathydav"/>


# 如何擷取 Linux 虛擬機器作為範本使用##

本文說明如何擷取執行 Linux 的 Azure 虛擬機器，以便用它做為範本建立其他虛擬機器。此範本包括 OS 磁碟和任何連接虛擬機器的資料磁碟。它不包含網路組態，因此您將需要在建立使用該範本的其他虛擬機器時加以設定。

Azure 會將此範本視為映像，並將其儲存在 [我的映像] 下方。這也是您已上傳的任何映像儲存所在之處。如需有關映像的詳細資訊，請參閱[關於 Azure 中的虛擬機器映像][]。

##開始之前##

這些步驟假設您已建立 Azure 虛擬機器且設定好作業系統，包括連接任何資料磁碟。如果您還沒這麼做，請參閱下列指示：

- [如何建立自訂虛擬機器][]
- [如何將資料磁碟連接至虛擬機器][]

##擷取虛擬機器##

1. 按一下命令列上的 [連線]，連線到虛擬機器。如需詳細資訊，請參閱[如何登入執行 Linux 的虛擬機器][]。

2. 在 SSH 視窗中，輸入下列命令。請注意，不同版本的 `waagent` 公用程式可能會有稍微不同的輸出：

	`$ sudo waagent -deprovision+user`

	此命令會嘗試清除系統，使之適合重新佈建。這項作業會執行下列工作：

	- 移除 SSH 主機金鑰 (如果組態檔中的 Provisioning.RegenerateSshHostKeyPair 是 'y')
	- 清除 /etc/resolv.conf 中的名稱伺服器設定
	- 移除 /etc/shadow 中的 `root` 使用者密碼 (如果組態檔中的 Provisioning.DeleteRootPassword 是 'y')
	- 移除快取的 DHCP 用戶端租用
	- 將主機名稱重設為 localhost.localdomain
	- 刪除最後佈建的使用者帳戶 (取自於 /var/lib/waagent) **和相關聯的資料**。

	![Deprovision the virtual machine](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)

	>[AZURE.NOTE]解除佈建會刪除檔案與資料，以將映象「一般化」。請只在您意圖擷取做為新的映像範本的虛擬機器上執行這個命令。


3. 輸入 **y** 繼續。

	![Deprovision of virtual machine successful](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

	**請注意**，您可以新增 `-force` 參數以避免此確認步驟。

	>[AZURE.NOTE]解除佈建只會將 Linux 映像一般化，但不保證會清除映像的所有機密資訊，也不保證映象適合重新發佈給第三方。


4. 輸入 **Exit** 關閉 SSH 用戶端。

5. 在[管理入口網站](http://manage.windowsazure.com)中，選取虛擬機器，然後按一下 [關閉]。

6. 當虛擬機器停止時，按一下命令列上的 [擷取]，開啟 [擷取虛擬機器] 對話方塊。

7.	在 [映像名稱] 中輸入新映像的名稱。

8.	所有的 Linux 映像都必須已「取消佈建」，方法是執行加上 `-deprovision` 選項的 `waagent` 命令。按一下 [我已在虛擬機器上執行 de-provision 命令]，表示作業系統已準備好要成為映像。

9.	按一下打勾記號以擷取映像。

	新映像現在會出現在 [映像] 下。擷取此映像後，虛擬機器便會遭刪除。

	![Image capture successful](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)


##後續步驟##
映像已可用來做為範本，建立虛擬機器。若要這麼做，您將需要使用 [從組件庫] 方法並選取您剛建立的映像，建立自訂的虛擬機器。如需指示，請參閱[如何建立自訂虛擬機器][]。

**另請參閱：** [Azure Linux 代理程式使用者指南](virtual-machines-linux-agent-user-guide.md)

[如何登入執行 Linux 的虛擬機器]: virtual-machines-linux-how-to-log-on.md
[關於 Azure 中的虛擬機器映像]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[如何建立自訂虛擬機器]: virtual-machines-create-custom.md
[如何將資料磁碟連接至虛擬機器]: storage-windows-attach-disk.md

 

<!---HONumber=July15_HO1-->