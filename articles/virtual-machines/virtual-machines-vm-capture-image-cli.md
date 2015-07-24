<properties
	pageTitle="使用 CLI 擷取執行 Linux 之虛擬機器的映像"
	description="了解如何對執行 Linux 的 Azure 虛擬機器 (VM) 擷取映像。"
	services="virtual-machines"
	documentationCenter=""
	authors="karthmut"
	manager="madhana"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="karthmut"/>




# 如何使用 CLI 擷取 Linux 虛擬機器以做為範本##



本文說明如何擷取執行 Linux 的 Azure 虛擬機器，以便用它做為範本建立其他虛擬機器。此範本包括 OS 磁碟和任何連接虛擬機器的資料磁碟。它不包含網路組態，因此您將需要在建立使用該範本的其他虛擬機器時加以設定。



Azure 會將此範本視為映像，並將其儲存在您的映像清單中。這也是您已上傳的任何映像儲存所在之處。如需映像的詳細資訊，請參閱[關於 Azure 中的虛擬機器映像][]。



##開始之前##



這些步驟假設您已建立 Azure 虛擬機器且設定好作業系統，包括連接任何資料磁碟。如果您還沒這麼做，請參閱下列指示：



- [如何建立自訂虛擬機器][]

- [如何將資料磁碟連接至虛擬機器][]



##擷取虛擬機器##



1. 連接至虛擬機器。如需詳細資訊，請參閱[如何登入執行 Linux 的虛擬機器][]。



2. 只有當 VM 的狀態為 **StoppedDeallocated** 時，才能擷取它。在 SSH 視窗中，輸入下列命令以關閉您的 VM：



        vm shutdown [options] <vm-name>



    請注意，`vm shutdown` 的其中一個選項是 `-p`，其將會在關機時保留計算資源。**請勿**啟用這個選項，因為 VM 必須解除佈建，才能擷取它。



3. 執行下列命令，以擷取 VM 映像：



        vm capture <vm-name> <target-image-name> --deleted



    必須刪除 VM，才能擷取映像。您可以使用 `--deleted` 或 `-t` 來執行這個動作。



4. 您可以藉由檢查 VM 映像清單來確認已擷取映像：



        vm image list | grep <target-image-name>



    `| grep <target-image-name>` 部分是選擇性的，但將協助您更容易在清單中尋找您的映像。



以下是擷取虛擬機器的逐步解說範例 (包含終端機輸出)：


    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status            Location  DNS Name                  IP Address

    data:    -----------  ----------------  --------  ------------------------  ------------

    data:    kmorig       RoleStateUnknown  West US   kmorig.cloudapp.net       100.92.56.16

    info:    vm list command OK

    ~$ azure vm shutdown kmorig

    info:    Executing command vm shutdown

    + Getting virtual machines

    + Shutting down VM

    info:    vm shutdown command OK

    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status              Location  DNS Name                  IP Address

    data:    -----------  ------------------  --------  ------------------------  ----------

    data:    kmorig       StoppedDeallocated  West US   kmorig.cloudapp.net

    info:    vm list command OK

    ~$ azure vm capture kmorig kmcaptured --deleted

    info:    Executing command vm capture

    + Getting virtual machines

    + Checking image with name kmcaptured exists

    + Capturing VM

    info:    vm capture command OK

    ~$ azure vm image list | grep kmcaptured

    data:    kmcaptured



如需更多詳細資料和其他命令，請瀏覽 [Azure CLI 文件頁面][]。


[Azure CLI 文件頁面]: ../virtual-machines-command-line-tools.md

[如何登入執行 Linux 的虛擬機器]: virtual-machines-linux-how-to-log-on.md

[關於 Azure 中的虛擬機器映像]: http://msdn.microsoft.com/library/azure/dn790290.aspx

[如何建立自訂虛擬機器]: virtual-machines-create-custom.md

[如何將資料磁碟連接至虛擬機器]: storage-windows-attach-disk.md
 

<!---HONumber=July15_HO2-->