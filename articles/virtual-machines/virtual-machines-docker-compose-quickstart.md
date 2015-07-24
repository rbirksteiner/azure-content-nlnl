<properties
   pageTitle="開始使用 Azure 虛擬機器上的 Docker 和 Compose"
   description="在 Azure 上使用 Compose 和 Docker 的快速簡介"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/07/2015"
   ms.author="danlep"/>

# 開始使用 Azure 虛擬機器上的 Docker 和 Compose


您可以在 Azure 的 Linux 虛擬機器上使用 Docker，快速地開始使用 [Compose](http://github.com/docker/compose) (*Fig* 的後續版本) 定義並執行複雜的應用程式。您可以使用 Compose 定義單一檔案中的多容器應用程式，然後透過可進行所有操作以便在 VM 上執行的單一命令，啟動您的應用程式。




## 使用 Docker VM 延伸模組建立 Azure VM，並安裝 Compose

您可以使用 Azure Markeplace 中的各種 Azure 程序和可用的映像建立 Azure VM，並在其上安裝 Docker 和 Compose。例如，如需使用適用於 Mac、 Linux 和 Windows (Azure CLI) 的 Azure 命令列介面建立包含 Docker VM 延伸模組的 Ubuntu VM 的快速程序，請參閱[透過 Azure 命令列介面使用 Docker VM 延伸模組](virtual-machines-docker-with-xplat-cli)。該文章中的範例使用 [服務管理] (** asm **) 模式中的 CLI。


透過 Docker 執行 Ubuntu VM 之後，使用 SSH 進行連線，然後執行兩個命令來安裝 [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)：

```
curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]如果出現「使用權限遭拒」錯誤，表示您的 /usr/local/bin 目錄可能無法寫入，因此您必須以超級使用者的身分安裝 Compose。依序執行 `sudo -i`、以上兩個命令，然後再執行 `exit`。

若要測試 Compose 的安裝，請執行

```
docker-compose --version
```

您將看到類似 ```
docker-compose 1.2.0
``` 的輸出


## 建立 docker-compose.yml 組態檔

Compose 使用稱為 `docker-compose.yml` 的文字組態檔定義將在 VM 上執行的容器。此檔案會指定要在每個容器上執行的映像 (或可能是來自 Dockerfile 的組建)、所需的環境變數和相依性、連接埠，容器之間的連結等等。如需有關 yml 檔案語法的詳細資訊，請參閱 [docker compose.yml 參考](http://docs.docker.com/compose/yml/)。

在 VM 上建立工作目錄，並使用您慣用的文字編輯器建立 `docker-compose.yml`。若要嘗試簡單的範例，請將下列文字複製到檔案。此設定會使用來自 [DockerHub 登錄](https://registry.hub.docker.com/_/wordpress/)的映像，安裝 WordPress (開放原始碼部落格和內容管理系統) 和連結的後端 MariaDB SQL 資料庫。

 ``` wordpress: image: wordpress links: - db:mysql ports: - 8080:80

db: image: mariadb environment: MYSQL_ROOT_PASSWORD: <your password>

```

## Start the containers with Compose

In your working directory simply run

```
docker-compose up -d

```

to start the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress_db_1...Creating wordpress_wordpress_1... ```

>[AZURE.NOTE]請務必在啟動時使用 **-d** 選項，讓容器在背景持續執行。

若要確認容器是否已啟動，請輸入 `docker-compose ps`。您應該會看到如下的內容：

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:8080->80
ess_1              apache2-for ...                       /tcp
```

您現在可以在 VM 上瀏覽至 `http://localhost:8080`，以直接連線到 WordPress。如果您想要透過網際網路連線至 VM，請先在將公用連接埠 80 對應至私用連接埠 8080 的 VM 上設定 HTTP 端點。例如，執行下列 Azure CLI 命令：

```
azure vm endpoint create <machine-name> 80 8080

```

您現在應該會看到 WordPress 起始畫面，您可以在其中完成安裝。

![WordPress 起始畫面][wordpress_start]




## 後續步驟

* 如需建置和部署多容器應用程式的其他範例，請參閱 [Compose 命令參考](http://docs.docker.com/compose/cli/)和[使用者指南](http://docs.docker.com/compose/)。
* 嘗試整合 Docker Compose 與 [Docker Swarm](virtual-machines-docker-swarm.md) 叢集。如需案例，請參閱 [Docker Compose/Swarm 整合](https://github.com/docker/compose/blob/master/SWARM.md)。
* 使用 Azure 資源管理員範本 (您自己的範本或[社群](http://azure.microsoft.com/documentation/templates/)提供的範本) 部署包含 Docker 的 Azure VM，以及使用 Compose 設定的應用程式。例如，[包含 Docker 與三個容器範本的 Ubuntu VM](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/) 可協助您從 [DockerHub 登錄](https://registry.hub.docker.com/)中的映像，快速部署三個服務。

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png
 

<!---HONumber=July15_HO2-->