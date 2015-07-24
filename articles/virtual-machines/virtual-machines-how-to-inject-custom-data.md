<properties
	pageTitle="將自訂資料插入 Azure 虛擬機器"
	description="本主題說明如何在建立執行個體時，將自訂資料插入 Azure 虛擬機器，以及如何在 Windows 或 Linux 上尋找自訂資料。"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/07/2015"
	ms.author="rasquill"/>


#將自訂資料插入 Azure 虛擬機器

不論作業系統是 Microsoft Windows 或 Linux 散發套件，將指令碼或其他資料插入正在佈建的 Azure 虛擬機器是很常見的案例。本主題將說明如何：

- 將資料插入正在佈建的 Azure 虛擬機器

- 針對 Windows 和 Linux 進行擷取，以及

- 使用某些系統提供的特殊工具來自動偵測與處理自訂資料。

> [AZURE.NOTE]本主題將以有關此功能的[這篇 Azure 部落格文章](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)為基礎加以擴充，並將隨著更多功能的出現而隨時更新。

## 將自訂資料插入 Azure 虛擬機器

目前僅在 [Microsoft Azure 命令列介面](https://github.com/Azure/azure-sdk-tools-xplat)中支援此功能。雖然您可能會在 `azure vm create` 命令中使用任何選項，但是下列將示範一個非常基本的方法。

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## 在虛擬機器中使用自訂資料

+ 如果您的 Azure 虛擬機器是 Windows 虛擬機器，則自訂資料檔案會被儲存到 `%SYSTEMDRIVE%\AzureData\CustomData.bin`，而且雖然從本機電腦傳送到新虛擬機器的資料是 base64 編碼，但是系統會自動將它解碼並立即開啟或使用。

   >[AZURE.NOTE]如果檔案已存在，則會被覆寫。目錄上的安全性會設為 [System:Full Control] 和 [Administrators:Full Control]。

+ 如果您的 Azure 虛擬機器是 Linux 虛擬機器，則自訂資料檔案會位於下列兩個地方，但因為資料會是 base64 編碼，您必須先將資料解碼。

    + 於 `/var/lib/waagent/ovf-env.xml`
    + 於 `/var/lib/waagent/CustomData`



## Azure 上的 Cloud-Init

如果您的 Azure 虛擬機器是來自 Ubuntu 或 CoreOS 映像，則您可以使用 CustomData 將 cloud-config 傳送到 cloud-init。或者，如果您的自訂資料檔案是指令碼，則 cloud-init 只能執行它。

### Ubuntu 雲端映像

您可以在大部分的 Azure Linux 映像中編輯 "/etc/waagent.conf" 來設定暫存資源磁碟和交換檔。如需詳細資訊，請參閱 [Azure Linux 代理程式使用者指南](virtual-machines-linux-agent-user-guide.md)。

不過，在 Ubuntu 雲端映像上，我們必須使用 cloud-init 設定資源磁碟 (也稱為「暫時」磁碟) 和交換資料分割。如需詳細資訊，請參閱 Ubuntu wiki 上的下列網頁：

 - [Ubuntu Wiki：設定交換資料分割](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟：使用 cloud-init

如需進一步資訊，請參閱 [Ubuntu 的 cloud-init 文件](https://help.ubuntu.com/community/CloudInit) (英文)。

<!--Link references-->
[加入角色服務管理 REST API 參考](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Azure 命令列介面](https://github.com/Azure/azure-sdk-tools-xplat)
 

<!---HONumber=July15_HO1-->