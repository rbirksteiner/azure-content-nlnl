<properties 
	pageTitle="在 Azure 中的 Linux 虛擬機器上使用根權限" 
	description="了解如何在 Azure 中的 Linux 虛擬機器上使用根權限。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="szark"/>




# 在 Azure 中的 Linux 虛擬機器上使用根權限

在 Azure 中的 Linux 虛擬機器上，依預設會停用 `root` 使用者。使用者可以使用 `sudo` 命令，以提高的權限來執行命令。不過，根據系統如何佈建而定，操作過程可能不同。

1. **SSH 金鑰和密碼，或僅密碼** - 虛擬機器是以憑證 (`.CER` 檔案) 或 SSH 金鑰和密碼來佈建，或只以使用者名稱和密碼來佈建。在此情況下，執行命令之前，`sudo` 會提示輸入使用者的密碼。

2. **僅 SSH 金鑰** - 虛擬機器是以憑證 (`.cer` 或 `.pem` 檔案) 或 SSH 金鑰來佈建，而未使用密碼。在此情況下，執行命令之前，`sudo` **不會**提示輸入使用者的密碼。


## SSH 金鑰和密碼，或僅密碼

使用 SSH 金鑰或密碼驗證來登入 Linux 虛擬機器，然後使用 `sudo` 執行命令，例如：

	# sudo <command>
	[sudo] password for azureuser:

在此例子中，將會提示使用者輸入密碼。輸入密碼之後，`sudo` 將會以 `root` 權限執行命令。

您也可以編輯 `/etc/sudoers.d/waagent` 檔案來啟用 passwordless sudo，例如：

	#/etc/sudoers.d/waagent
	azureuser ALL = (ALL) NOPASSWD: ALL

這項變更將允許 azureuser 使用者的 passwordless sudo。

## 僅 SSH 金鑰

使用 SSH 金鑰驗證來登入 Linux 虛擬機器，然後使用 `sudo` 執行命令，例如：

	# sudo <command>

在此例子中，將**不會**提示使用者輸入密碼。按 `<enter>` 鍵之後，`sudo` 將會以 `root` 權限執行命令。

 

<!---HONumber=July15_HO1-->