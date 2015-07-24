
<properties
   pageTitle="開始在 Azure 上搭配 swarm 使用 docker"
   description="描述如何使用 Docker VM 延伸模組建立一組 VM，以及使用 swarm 來建立 Docker 叢集。"
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/24/2015"
   ms.author="rasquill"/>

# 如何搭配swarm 使用 docker

本主題將示範一個非常簡單的方式來搭配使用 [docker](https://www.docker.com/) 和 [swarm](https://github.com/docker/swarm)，以便在 Azure 上建立由 swarm 管理的叢集。它會在 Azure 中建立四個虛擬機器，一個用來做為 swarm 管理員，其餘三個則做為 docker 主機叢集的一部分。當您完成時，可以使用 swarm 查看叢集，然後開始在其上使用 docker。此外，本主題中的 Azure CLI 呼叫會使用服務管理 (asm) 模式。

> [AZURE.NOTE]這是舊版軟體，因此，請查看以往的更新記錄，以取得在 Azure 上使用此功能，為 Docker 容器建立平衡且受控制的大型叢集相關資訊，以及檢查 docker swarm 文件來探索它的所有功能。<!-- -->此外，本主題會搭配 swarm 使用 docker，並且*不搭配* **docker-machine** 使用 Azure CLI，以示範不同工具如何一同運作但仍保持獨立。**docker-machine** 具備 **--swarm** 參數，可讓您使用 **docker-machine** 直接將節點新增到 swarm。如需範例，請參閱 [docker-machine](https://github.com/docker/machine) 文件。如果您錯過了在 Azure VM 上執行的 **docker-machine**，請參閱[如何搭配 Azure 使用 docker-machine](virtual-machines-docker-machine.md)。

## 使用 Azure 虛擬機器建立 docker 主機

本主題會建立四個 VM，但您可以使用任何您想要的數目。使用 *&lt;password&gt;* (使用您選擇的密碼來取代) 呼叫下列命令。

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

當您完成時，應該能夠使用 **azure vm list** 來查看您的 Azure VM：

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## 在 swarm 主要 VM 上安裝 swarm

本主題使用[來自 docker swarm 文件的安裝容器模型](https://github.com/docker/swarm#1---docker-image) -- 但是，您也可以 SSH 到 **swarm-master**。在此模型中，會下載 **swarm** 以做為執行 swarm 的 docker 容器。在後續內容中，我們會*從膝上型電腦使用 docker 遠端*執行此步驟以連接到 **swarm-master** VM，並告知它使用叢集識別碼建立命令 **swarm create**。叢集識別碼是 **swarm** 探索 swarm 群組成員的方式。(您也可以複製儲存機制並自行建置它，這樣做可讓您擁有完整控制權且能夠進行偵錯)。

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

最後一行是叢集識別碼；請將它複製到某處，因為當您將節點 VM 加入 swarm 主機以建立 "swarm" 時將再次用到它。在此範例中，叢集識別碼是 **36731c17189fd8f450c395db8437befd**。

> [AZURE.NOTE]只是要先聲明，我們正使用本機 docker 安裝來連接到 Azure 中的 **swarm-master** VM，並指示 **swarm-master** 下載、安裝及執行 **create** 命令，其會傳回我們稍後要基於探索目的而使用的叢集識別碼。<!-- -->若要確認這一點，請執行 `docker -H tcp://`*&lt;hostname&gt;* ` images` 以列出 **swarm-master** 機器和其他節點上的容器處理程序來進行比較 (因為我們搭配 **--rm** 參數執行舊版 swarm 命令，所以會在命令完成時移除容器，因此，使用 **docker ps -a** 將不會傳回任何內容)：


        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />
> 如果您熟悉 **docker**，就知道其他節點不會包含任何項目，因為並未下載並執行任何映像。

## 將節點 VM 加入我們的 docker 叢集

針對每個節點，使用 Azure CLI 列出端點資訊。我們將在以下針對 **swarm-node-1** docker 主機執行此動作，以取得該節點的 docker 連接埠。

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


使用 **docker** 和 `-H` 選項來指向節點 VM 上的 docker 用戶端，藉由傳遞叢集識別碼和節點的 docker 連接埠，將該節點加入您建立的 swarm (後者使用的是 **--addr**)：

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

看起來不錯。若要確認 **swarm** 正在 **swarm-node-1** 上執行，可輸入：

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

針對叢集中的所有其他節點重複執行。在本例中，我們會針對 **swarm-node-2** 和 **swarm-node-3** 執行此動作。

## 開始管理 swarm 叢集

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

然後您可以列出叢集中的節點：

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

在您的 swarm 上執行動作。若要尋找靈感，請參閱 [https://github.com/docker/swarm/](https://github.com/docker/swarm/)，或者觀看[影片](https://www.youtube.com/watch?v=EC25ARhZ5bI)。

<!-- links -->

[docker-machine-azure]: virtual-machines-docker-machine.md
 

<!---HONumber=July15_HO2-->