<properties
   pageTitle="如何搭配使用 docker-machine 和 Azure"
   description="示範如何使用 Ubuntu 上的 Docker Machine，在 Azure 上啟動並執行。"
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
   ms.date="05/25/2015"
   ms.author="rasquill"/>

# 如何搭配 Azure 使用 docker-machine

本主題說明如何搭配 [machine](https://github.com/docker/machine) 和 [Azure CLI](https://github.com/Azure/azure-xplat-cli) 使用 [Docker](https://www.docker.com/)，建立 Azure 虛擬機器，從執行 Ubuntu 的電腦上簡單快速地管理 Linux 容器。基於示範緣故，本教學課程將示範如何同時部署 [busybox Docker 中樞映像](https://registry.hub.docker.com/_/busybox/)和 [nginx Docker 中樞映像](https://registry.hub.docker.com/_/nginx/)，並設定容器以將 Web 路由要求傳送到 nginx 容器 (Docker **machine** 文件說明如何針對其他平台修改這些指示)。

您必須具備一些先決條件，才能完成本教學課程。您將需要安裝下列項目：

1. [npm](https://docs.npmjs.com/) 和 [Node.js](http://nodejs.org/)
2. [Azure CLI](https://github.com/Azure/azure-xplat-cli)
3. [Docker 用戶端](https://docs.docker.com/installation/)

如果您以此順序安裝這些項目，將可隨時於本教學課程中使用 Ubuntu 電腦。(本教學課程大致上應該與任何其他以 dpkg 為基礎的發行版相同，例如 Debian。如果您發現了額外的需求或步驟，請加上註解，好讓我們了解。)

## 取得 docker-machine -- 或建置它

開始使用 **docker-machine** 的最快速方式是直接從[版本共用](https://github.com/docker/machine/releases)中下載適當的版本。本教學課程中的用戶端電腦是在 x64 電腦上執行 Ubuntu，因此，會用到 **docker-machine_linux-amd64** 映像。

您也可以依照[參與 machine 建置](https://github.com/docker/machine#contributing)的步驟，自行建置 **docker-machine**。您應該已準備好下載最多可達 1 GB 或以上來執行組建，不過這樣做，您就可以精確自訂所需的方式。

> [AZURE.NOTE]您也可以建立指向其平台版本的[符號連結](http://en.wikipedia.org/wiki/Symbolic_link)，但本教學課程會直接使用二進位檔，非常清楚地示範該行為。因此，本教學課程會改用 `docker-machine_linux-amd64 env`，而不使用如 **docker-machine** 文件所示的命令 (例如 `docker-machine env`)。不論您是否要建立符號連結或只要直接使用二進位檔名稱，都可自行決定，但是，如果要變更您正在使用的名稱，請記得使用下列指示來修改名稱。

<br />

>  不論您選擇哪一個方法，都必須在命令列上直接呼叫二進位檔，或者放置二進位檔的路徑，例如 **/usr/local/bin**。請記得輸入 `chmod +x` &lt;*`binaryName`*&gt; 來確定已將它標記為可執行檔，其中 &lt;*`binaryName`*&gt; 是 Docker machine 可執行檔的名稱。本教學課程使用 **docker-machine_linux-amd64**。

## 建立適用於 docker、machine 及 Azure 的憑證和金鑰檔

現在您必須建立憑證和金鑰檔，Azure 需要它們來確認您的身分識別和權限，而那些 **docker-machine** 需要它們來與您的 Azure 虛擬機器進行通訊，以便從遠端建立和管理容器。如果您在目錄中已經擁有這些檔案 (或許是搭配 docker 使用)，則您可以重複使用它們。但是，測試 **docker-machine** 的最佳做法是在個別目錄中建立它們，然後將 docker-machine 指向它們。

> [AZURE.NOTE]如果再三嘗試 **docker-machine** 都沒有成功，請確定您重複使用憑證和金鑰檔。**docker-machine** 也會建立一組用戶端憑證 -- 其建立的所有項目都會在 `~/.docker/machine` 上進行檢查。如果您將這些憑證移至另一部電腦，也需要移動 **docker-machine** 憑證資料夾。這和您即將在另一個平台上使用 **docker-machine** 的情況不同，例如，只想查看它如何運作。

如果您有使用 Linux 發行版的經驗，或許已經可以在電腦的特定位置上使用這些檔案，而 [Docker HTTPS 文件也會說明這些步驟](https://docs.docker.com/articles/https/)。不過，以下是這個步驟的最簡單形式。

1. 建立本機資料夾，並在命令列上瀏覽到該資料夾，然後輸入：

		openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
		openssl pkcs12 -export -out mycert.pfx -in mycert.pem -name "My Certificate"

	請準備好在這裡輸入您憑證的匯出密碼，並擷取該密碼供之後使用。然後輸入：

		openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

2. 將憑證的 .cer 檔案上傳至 Azure。在 [Azure 入口網站](https://manage.windowsazure.com)中，按一下服務區域左下方的 [設定] (如下所示)

	![][portalsettingsitem]

	，然後按一下 [管理憑證]：

	![][managementcertificatesitem]

	接著按一下 [上傳] (位於頁面下方) ![][uploaditem]，以上傳您在上一個步驟中建立的 **mycert.cer** 檔案。

3. 在入口網站的同一個 [設定] 窗格中，按一下 [訂戶帳戶]，以擷取要在您建立 VM 時使用的訂用帳戶識別碼，因為您將在下一個步驟中用到它 (您也可以在命令列上，使用 Azure CLI 命令 `azure account list` 來尋找訂用帳戶識別碼，其會顯示該帳戶中您所擁有之每個訂用帳戶的訂用帳戶識別碼)。

4. 使用 **docker-machine create** 命令，在 Azure 上建立 docker 主機 VM。此命令需要您剛在上一個步驟中擷取的訂用帳戶識別碼，以及您在步驟 1 中建立的 **.pem** 檔案的路徑。本主題會使用 "machine-name" 做為 Azure 雲端服務 (和您的 VM) 名稱，但是您應該使用自己的選項來取代，並且記得在任何其他需要 VM 名稱的步驟中使用您的雲端服務名稱。(請記住，在此範例中，我們使用的是完整的二進位檔名稱，而非 **docker-machine** 符號連結)。

		docker-machine_linux-amd64 create \
	    -d azure \
	    --azure-subscription-id="<subscription ID acquired above>" \
	    --azure-subscription-cert="mycert.pem" \
	    machine-name

	由於前兩個步驟需要建立新的 VM 並載入 Linux Azure 代理程式，以及更新新的 VM，因此您應該會看到如下的內容。

		INFO[0001] Creating Azure machine...
	    INFO[0049] Waiting for SSH...
	    modprobe: FATAL: Module aufs not found.
	    + sudo -E sh -c sleep 3; apt-get update
	    + sudo -E sh -c sleep 3; apt-get install -y -q linux-image-extra-3.13.0-36-generic
	    E: Unable to correct problems, you have held broken packages.
	    modprobe: FATAL: Module aufs not found.
	    Warning: tried to install linux-image-extra-3.13.0-36-generic (for AUFS)
	     but we still have no AUFS.  Docker may not work. Proceeding anyways!
	    + sleep 10
	    + [ https://get.docker.com/ = https://get.docker.com/ ]
	    + sudo -E sh -c apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
	    gpg: requesting key A88D21E9 from hkp server keyserver.ubuntu.com
	    gpg: key A88D21E9: public key "Docker Release Tool (releasedocker) <docker@dotcloud.com>" imported
	    gpg: Total number processed: 1
	    gpg:               imported: 1  (RSA: 1)
	    + sudo -E sh -c echo deb https://get.docker.com/ubuntu docker main > /etc/apt/sources.list.d/docker.list
	    + sudo -E sh -c sleep 3; apt-get update; apt-get install -y -q lxc-docker
	    + sudo -E sh -c docker version
	    INFO[0368] "machine-name" has been created and is now the active machine.
	    INFO[0368] To point your Docker client at it, run this in your shell: $(docker-machine_linux-amd64 env machine-name)

    > [AZURE.NOTE]因為正在建立 VM，所以可能需要幾分鐘的時間才能處於就緒狀態。雖然您正處於等待狀態，但您可以使用 Azure CLI 輸入 `azure vm list` 來檢查新 Docker 主機的狀態，直到您看見 VM 呈現 **ReadyRole** 狀態為止。

5. 針對終端機工作階段設定 docker 和機器環境變數。傳回訊息的最後一行建議您立即執行 **env** 命令，以匯出將 docker 用戶端與特定機器直接搭配使用所需的環境變數。

		$(docker-machine_linux-amd64 env machine-name)

	一旦執行此動作之後，就不需要使用任何特殊命令，來使用本機 docker 用戶端連接到 Docker **machine** 所建立的 VM。

	    $ docker info
	    Containers: 0
	    Images: 0
	    Storage Driver: devicemapper
	     Pool Name: docker-8:1-131736-pool
	     Pool Blocksize: 65.54 kB
	     Backing Filesystem: extfs
	     Data file: /dev/loop0
	     Metadata file: /dev/loop1
	     Data Space Used: 305.7 MB
	     Data Space Total: 107.4 GB
	     Metadata Space Used: 729.1 kB
	     Metadata Space Total: 2.147 GB
	     Udev Sync Supported: false
	     Data loop file: /var/lib/docker/devicemapper/devicemapper/data
	     Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
	     Library Version: 1.02.82-git (2013-10-04)
	    Execution Driver: native-0.2
	    Kernel Version: 3.13.0-36-generic
	    Operating System: Ubuntu 14.04.1 LTS
	    CPUs: 1
	    Total Memory: 1.639 GiB
	    Name: machine-name
	    ID: W3FZ:BCZW:UX24:GDSV:FR4N:N3JW:XOC2:RI56:IWQX:LRTZ:3G4P:6KJK
	    WARNING: No swap limit support

> [AZURE.NOTE]本教學課程示範的 **docker-machine** 會建立一個 VM。不過，您可以重複執行這些步驟，來建立所需機器的數目。如果您執行此動作，則在 VM 和 docker 之間進行切換的最佳方式是使用內嵌的 **env** 命令，為每個個別命令設定 **docker** 環境變數。例如，若要搭配不同的 VM 使用 **docker info**，您可以輸入 `docker $(docker-machine env <VM name>) info`，而 **env** 命令會填入 docker 連線資訊，以便與該 VM 搭配使用。

## 我們已完成此動作。讓我們從遠端使用 docker，和來自 Docker 中樞的映像執行某些應用程式。

您現在可以正常方式使用 docker，在容器中建立應用程式。最簡單的示範是 [busybox](https://registry.hub.docker.com/_/busybox/)：

	    $  docker run busybox echo hello world
	    Unable to find image 'busybox:latest' locally
	    511136ea3c5a: Pull complete
	    df7546f9f060: Pull complete
	    ea13149945cb: Pull complete
	    4986bf8c1536: Pull complete
	    busybox:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
	    Status: Downloaded newer image for busybox:latest
	    hello world

但是，您可能想要從 [Docker 中樞](https://registry.hub.docker.com/)建立可在網際網路上立即看見的應用程式，例如 [nginx](https://registry.hub.docker.com/_/nginx/)。

> [AZURE.NOTE]請記得使用 **-P** 選項，讓 **docker** 可為映像指派隨機連接埠，並使用 **-d**，以確保該容器會在背景中持續執行。(如果您忘記，將會啟動 nginx，然後它將立即關機。所以請不要忘了！)

	$ docker run --name machinenginx -P -d nginx
    Unable to find image 'nginx:latest' locally
    30d39e59ffe2: Pull complete
    c90d655b99b2: Pull complete
    d9ee0b8eeda7: Pull complete
    3225d58a895a: Pull complete
    224fea58b6cc: Pull complete
    ef9d79968cc6: Pull complete
    f22d05624ebc: Pull complete
    117696d1464e: Pull complete
    2ebe3e67fb76: Pull complete
    ad82b43d6595: Pull complete
    e90c322c3a1c: Pull complete
    4b5657a3d162: Pull complete
    511136ea3c5a: Already exists
    nginx:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for nginx:latest
    5883e2ff55a4ba0aa55c5c9179cebb590ad86539ea1d4d367d83dc98a4976848

若要從網際網路查看它，請在連接埠 80 上為 Azure VM 建立公用端點，然後將該連接埠對應到 nginx 容器的連接埠。首先，使用 `docker ps -a` 來找到容器，並找出 **docker** 已將哪個連接埠指派給容器的連接埠 80。(以下顯示的資訊已經過編輯，以便只顯示連接埠資訊；您將會看見更多資訊)。

	$ docker ps -a
    IMAGE               PORTS
    nginx:latest        0.0.0.0:49153->80/tcp, 0.0.0.0:49154->443/tcp
    busybox:latest

我們可以看到 docker 已將容器的連接埠 80 指派給 VM 的連接埠 49153。我們現在可以使用 Azure CLI，將外部雲端服務的公用連接埠 80 對應到 VM 上的連接埠 49153。然後，Docker 可確保會將 VM 連接埠 49153 上的輸入 tcp 流量路由傳送到 nginx 容器。

	$ azure vm endpoint create machine-name 80 49153
    info:    Executing command vm endpoint create
    + Getting virtual machines
    + Reading network configuration
    + Updating network configuration
    info:    vm endpoint create command OK

開啟您最愛的瀏覽器，過目一下。

![][nginx]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
前往 [Docker 使用者指南](https://docs.docker.com/userguide/)，並在 Microsoft Azure 上建立一些應用程式。或者，在 Azure 上使用 [**docker** 和 swarm](https://github.com/docker/swarm) (virtual-machines-docker-swarm)，看看 swarm 如何與 docker 及 Azure 搭配使用。

<!--Image references-->
[nginx]: ./media/virtual-machines-docker-machine/nginxondocker.png
[portalsettingsitem]: ./media/virtual-machines-docker-machine/portalsettingsitem.png
[managementcertificatesitem]: ./media/virtual-machines-docker-machine/managementcertificatesitem.png
[uploaditem]: ./media/virtual-machines-docker-machine/uploaditem.png

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 

<!---HONumber=July15_HO2-->