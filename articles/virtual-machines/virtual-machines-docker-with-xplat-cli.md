<properties
	pageTitle="在 Azure 上使用 Linux 的 Docker VM 擴充程式"
	description="說明 Docker 和 Azure 虛擬機器延伸模組，並示範如何以程式設計方式使用 Azure CLI，從命令列在 Azure 上建立 Docker 主機的虛擬機器。"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="05/22/2015"
	ms.author="rasquill"/>

# 透過 Azure 命令列介面 (Azure CL) 使用 Docker VM 延伸模組

本主題說明如何透過 Azure CLI 中的服務管理 (asm) 模式，在任何平台上建立包含 Docker VM 延伸模組的 VM。[Docker](https://www.docker.com/) 是最常用的虛擬化方式之一，它不使用虛擬機器，而是使用 [Linux 容器](http://en.wikipedia.org/wiki/LXC)作為在共用資源上獨立資料和執行計算的方法。您可以將 Docker VM 擴充程式應用在 [Azure Linux 代理程式](virtual-machines-linux-agent-user-guide.md)上，如此可在 Azure 上建立 Docker VM 來託管任何數量的應用程式容器。若要查看容器及其優點的高層級討論，請參閱 [Docker 高層級白板](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard) (英文)。

+ [如何搭配使用 Docker VM 延伸模組與 Azure]
+ [Linux 及 Windows 的虛擬機器延伸模組]
+ [Azure 容器及容器管理資源]
+ [後續步驟]

## <a id='How to use the Docker VM Extension with Azure'>如何搭配使用 Docker VM 延伸模組與 Azure</a>
若要將 Docker VM 延伸模組與 Azure 搭配使用，您必須安裝 0.8.6 版本以上的 [Azure 命令列介面](https://github.com/Azure/azure-sdk-tools-xplat) (Azure CLI) (本文截稿當時的最新版本為 0.8.10)。您可以在 Mac、Linux 及 Windows 上安裝 Azure CLI。



在 Azure 上使用 Docker 的完整程序相當簡單：

+ 在您要控管 Azure 的電腦上安裝 Azure CLI 和其相依性 (若在 Windows 上，就是指如同虛擬機器一樣執行的 Linux 散發套件)
+ 使用 Azure CLI Docker 命令在 Azure 中建立 VM Docker 主機
+ 使用本機 Docker 命令在 Azure 中的 Docker VM 內管理 Docker 容器。


### 安裝 Azure 命令列介面 (Azure CLI)

若要安裝和設定 Azure CLI，請參閱[如何安裝 Azure 命令列介面](../xplat-cli-install.md)。若要確認安裝狀況，請在命令提示字元中輸入 `azure`，您應該很快就能看到 Azure CLI ASCII 作品，其中會列出您可以使用的基本命令。如果安裝正確執行，您應該可以輸入 `azure help vm` 並且可以看到列出的其中一個命令是 "docker"。

> [AZURE.NOTE]Docker 具備適用於 Windows 的安裝程式 [Boot2Docker](https://docs.docker.com/installation/windows/)，您也可以使用該安裝程式，自動建立您可以使用的 docker 用戶端，以便搭配做為 docker 主機的 Azure VM 使用。

### 將 Azure CLI 連接至您的 Azure 帳戶
在使用 Azure CLI 前，必須讓您的 Azure 帳戶認證與您平台上的 Azure CLI 產生關聯。[如何連接到您的 Azure 訂用帳戶](../xplat-cli-connect.md)一節說明如何下載及匯入您的 **.publishsettings** 檔案，或為 Azure CLI 與組織識別碼建立關聯。

> [AZURE.NOTE]因為使用一個或其他多個驗證方法的行為會有些許差異，因此請確實閱讀上述文件以了解不同的功能。

### 安裝 Docker 及使用適合 Azure 的 Docker VM 擴充程式
請依循 [Docker 安裝指示](https://docs.docker.com/installation/#installation)在您的電腦本機上安裝 Docker。

若要搭配使用 Docker 與 Azure 虛擬機器，VM 所使用的 Linux 映像必須安裝 [Azure Linux VM 代理程式](virtual-machines-linux-agent-user-guide.md)。目前來說，只有兩種映像類型提供此安裝：

+ Azure 映像庫中的 Ubuntu 映像，或

+ 使用已安裝並設定的 Azure Linux VM 代理程式所建立的自訂 Linux 映像。如需如何使用 Azure VM 代理程式來建置自訂 Linux VM 的詳細資訊，請參閱 [Azure Linux VM 代理程式](virtual-machines-linux-agent-user-guide.md)。

### 使用 Azure 映像庫

若要從 Bash 或終端機工作階段中，使用下列 Azure CLI 命令尋找 VM 映像庫中最新的 Ubuntu 映像，請輸入

`azure vm image list | grep Ubuntu-14_04`

並選取其中一個映像名稱 (例如 `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-zh-tw-30GB`)，然後使用下列命令建立使用該映像的新 VM。

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-zh-tw-30GB" <username> <password>
```

其中：

+ *&lt;vm-cloudservice name&gt;* 是將成為 Azure 中 Docker 容器主機電腦的 VM 名稱。

+  *&lt;username&gt;* 是VM 預設根目錄使用者的使用者名稱

+ *&lt;password&gt;* 是 *username* 帳戶的密碼，需符合 Azure 的複雜性標準

> [AZURE.NOTE]目前來說，密碼必須至少要有 8 個字元，包含一個小寫字母和一個大寫字母、一個數字以及如下的其中一個特殊字元：`!@#$%^&+=`。不，上一個句子中的句號並不是一個特殊字元。

如果命令執行成功，根據您使用的精準引數和選項，您應該可以看到如下所示的畫面：

![](./media/virtual-machines-docker-with-xplat-cli/dockercreateresults.png)

> [AZURE.NOTE]建立虛擬機器需要幾分鐘的時間，但當其佈建完成後，Docker 精靈 (Docker 服務) 便會啟動，您即可與 Docker 容器主機連線。

若要測試您已在 Azure 中建立的 Docker VM，請輸入

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

其中 *<vm-name-you-used>* 是虛擬機器的名稱，您會將其用在呼叫 `azure vm docker create`。您應該可看到如下所示的內容，這代表您的 Docker 主機 VM 已在 Azure 中啟用和執行，並且正等待您的命令。

![](./media/virtual-machines-docker-with-xplat-cli/connectingtodockerhost.png)

### Docker 主機 VM 驗證
除了建立 Docker VM 之外，`azure vm docker create` 命令也會自動建立所需的憑證，以允許您的 Docker 用戶端電腦使用 HTTPS 與 Azure 容器主機連線，而且憑證會適當地儲存在用戶端和主機機器中。在後續執行上，現有的認證會被重新使用並且與新的主機共用。

依預設，憑證會放在 `~/.docker`，而 Docker 將設定為在連接埠 **2376** 上執行。如果您要使用不同的連接埠或目錄，則可以使用下列其中一個 `azure vm docker create` 命令列選項來設定您的 Docker 容器主機 VM，藉此使用不同連接埠或不同憑證來連接用戶端：

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

主機上的 Docker 精靈會設定為使用由 `azure vm docker create` 命令產生的憑證接聽，並驗證指定之連接埠上的用戶端連線。用戶端機器必須使用這些認證來取得 Docker 主機的存取權。

> [AZURE.NOTE]在沒有這些憑證下運作的網路主機，將很容易受到任何可連線到此機器的使用者攻擊。在您修改預設設定之前，請確保您已了解存在您電腦和應用程式中的風險。




## 後續步驟

您已準備好開始移至 [Docker 使用者指南]，並使用 Docker VM。若要在新入口網站中建立具有 Docker 功能的 VM，請參閱[如何搭配使用 Docker VM 擴充程式與入口網站]。

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[後續步驟]: #next-steps

[如何搭配使用 Docker VM 延伸模組與 Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Linux 及 Windows 的虛擬機器延伸模組]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Azure 容器及容器管理資源]: #Container-and-Container-Management-Resources-for-Azure

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[如何搭配使用 Docker VM 擴充程式與入口網站]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Docker 使用者指南]: https://docs.docker.com/userguide/
 

<!---HONumber=July15_HO1-->