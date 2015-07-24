<properties title="Deploying Your Own Private Docker Registry on Azure"
  pageTitle="在 Azure 上部署您自己的私用 Docker Registry"
  description="描述如何使用 Docker Registry 裝載 Azure Blob 儲存體服務上的容器映像。"
  services="virtual-machines"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="" 
  tags="" />

<tags
  ms.service="virtual-machines"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="06/17/2015" 
  ms.author="ahmetb" />

# 在 Azure 上部署您自己的私用 Docker Registry

本文件描述何謂 Docker 私用登錄，並顯示如何使用 Azure Blob 儲存體將 Docker Registry 2.0 容器映像部署至 Microsoft Azure 上的 Docker 私用登錄。

本文件假設：

1. 您知道如何使用 Docker，並具備要儲存的 Docker 映像。(您不知道嗎？ [了解 Docker](https://www.docker.com))
2. 您具有已安裝 Docker 引擎的伺服器。(您沒有嗎？ [快速在 Azure 上執行。](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## 什麼是私用 Docker Registry？

若要將容器化的應用程式傳送至雲端，您需建構 Docker 容器映像，並將其儲存到某個位置，以便自己和其他人使用。

建立容器映像並將其傳送至雲端並不困難，但是要可靠地儲存產生的映像卻是個挑戰。基於這個理由，Docker 提供集中式的服務 (稱為 [Docker Hub][docker-hub])，可將容器映像儲存在雲端上，並可讓您隨時使用這些映像建立容器。

雖然 [Docker Hub][docker-hub] 是儲存私用應用程式容器映像的付費服務，Docker 仍會考量開發人員的需求並提供開放原始碼工具組，可在防火牆或內部部署後方您自己的私用 Docker 登錄中儲存您的映像，無需使用公用網際網路。Azure Blob 儲存體可輕鬆受到保護，所以您可以快速地使用它，在您自行控制的 Azure 中建立並使用私用 Docker 登錄。

## 為什麼應該在 Azure 上裝載 Docker Registry？

藉由在 Microsoft Azure 上裝載 Docker Registry 執行個體，並將您的映像儲存在 Azure Blob 儲存體上，您可以享有數個優點：

**安全性：**您的 Docker 映像不會離開 Azure 資料中心，因此它們不會跨越公用網際網路，如果您使用 Docker Hub 就有可能跨越。
  
**效能：**您的 Docker 影像會儲存在和應用程式相同的資料中心或區域內。這表示將會比 Docker Hub 更快速、更可靠地提取映像。

**可靠性：**藉由使用 Microsoft Azure Blob 儲存體，您可使用許多儲存體屬性，例如高可用性、備援、進階儲存體 (SSD) 等等。

## 將 Docker Registry 設定為使用 Azure Blob 儲存體

(建議您先閱讀 [Docker Registry 2.0 文件][registry-docs]再繼續進行。)

您可以利用兩種不同的方式[設定][registry-config] Docker Registry。您可以：

1. 使用 `config.yml` 檔案。在此情況下，您必須在 `registry` 映像頂端建立個別的 Docker 映像。
2. 透過環境變數覆寫預設組態檔：在不建立和維護個別 Docker 映像的情況下完成作業。

為了簡單起見，本主題會遵循選項 2，使用環境變數。

若要執行 Docker Registry 執行個體：* 使用您的 Azure 儲存體帳戶儲存映像 * 接聽虛擬機器的通訊埠 5000 * 未設定驗證 (不建議，請參閱下列附註)

您必須在 bash 終端機執行下列 Docker 命令 (以認證取代 `<storage-account>` 和 `<storage-key>`)：

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

一旦命令結束，您就可以看到容器藉由在您的主機上執行 `docker ps` 命令，裝載您的私用 Docker Registry 執行個體：

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT]本文件未涵蓋設定 Docker Registry 的安全性，如果您開啟連接至虛擬機器端點上登錄連接埠的連接埠，根據預設，任何未經驗證的人都可以存取您的登錄，如果您使用上述的部署命令，則可存取負載平衡器。
>
> 請參閱[設定 Docker Registry][registry-config] 文件，以了解如何保護登錄執行個體和您的映像。

## 後續步驟

一旦您設定了登錄，就可以使用它的其他功能。以 docker [registry-docs] 開頭。

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[registry-docs]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 

<!---HONumber=62-->