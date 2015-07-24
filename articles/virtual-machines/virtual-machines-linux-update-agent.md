<properties
	pageTitle="如何從 Github 升級至最新版的 Azure Linux 代理程式"
	description="了解如何從 Github，為 Azure 中的 Linux VM 更新 Azure Linux 代理程式。"
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="mingzhan"/>


# 如何從 Github 升級至最新版的 Azure Linux 代理程式

若要更新 [Azure Linux 代理程式](https://github.com/Azure/WALinuxAgent) ，您必須準備好：

1. 在 Azure 中執行的 Linux VM
2. 您已使用 SSH 連線到 Linux VM

> [AZURE.NOTE]如果您會從 Windows 電腦執行這項工作，可以使用 Putty 來 SSH 到 Linux 機器。如需詳細資訊，請參閱[如何登入執行 Linux 的虛擬機器](virtual-machines-linux-how-to-log-on.md)。

支援 Azure 的 Linux 散發版本有將 Azure Linux 代理程式套件放在其儲存機制，因此請先從這個 Distro 儲存機制檢查並安裝最新版本。

對於 Ubuntu，只要輸入：
     
    #sudo apt-get install waagent

在 CentOS 上請輸入：

    #sudo yum install waagent

對於 Oracle Linux，請確定已在檔案 `/etc/yum.repo.d/public-yum-ol6.repo` 或 `/etc/yum.repo.d/public-yum-ol7.repo` 中啟用附加元件儲存機制，然後輸入：

    #sudo yum install WALinuxAgent

通常您要做就是這幾個步驟了，但如果因為其他原因造成您必須直接從 https://github.com 安裝，請執行以下步驟。


## 安裝 wget

使用 SSH 登入 VM。

在命令列輸入 `#sudo yum install wget` 來安裝 wget (有一些散發版本基本上是不會安裝的，例如 Redhat、CentOS、Oracle Linux 6.4 和 6.5 版)。


## 下載最新版本

在在網頁上開啟 [Github 中的 Azure Linux 代理程式版本](https://github.com/Azure/WALinuxAgent/releases) ，然後查明最新的版本號碼，例如：2.0.12。(您可以輸入 `#waagent --version`，即可找到目前的版本 )。

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

下列一行使用 2.0.12 版做為範例：

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.12/waagent  

## 讓 waagent 變成可執行檔

    #chmod +x waagent

## 將新的可執行檔複製到 /usr/sbin /
    
    #sudo cp waagent /usr/sbin

針對 CoreOS，請使用：

    #sudo cp waagent /usr/share/oem/bin/
 
## 重新啟動 waagent 服務

針對大多數的 Linux 散發版本：

    #sudo service waagent restart

針對 Ubuntu，使用：

    #sudo service walinuxagent restart

針對 CoreOS，請使用：

    #sudo systemctl restart waagent 

## 確認 Azure Linux 代理程式版本
   
    #waagent -version

針對 CoreOS，上述命令可能無效。

您會看到 Linux 代理程式版本已更新為新的版本。

如需有關 Azure Linux 代理程式的詳細資訊，請參閱 [Azure Linux 代理程式讀我檔案](https://github.com/Azure/WALinuxAgent)。



 

<!---HONumber=July15_HO2-->