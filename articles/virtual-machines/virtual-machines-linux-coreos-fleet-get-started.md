<properties
	pageTitle="開始在 Azure 上的 CoreOS 使用 Fleet"
	description="提供在 Azure 上的 CoreOS Linux 虛擬機器使用 Fleet 和 Docker 的基本範例。"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="madhana"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/17/2015"
	ms.author="danlep"/>

# 開始在 Azure 上的 CoreOS 使用 Fleet

本文提供您兩個快速的範例，有關使用 [Fleet](https://github.com/coreos/fleet) 和 [Docker](https://www.docker.com/) 在 [ CoreOS] 虛擬機器的叢集上執行應用程式。

若要使用這些範例，請先設定三個節點的 CoreOS 叢集，如[如何在 Azure 上使用 CoreOS] 中所述。完成後，您將了解 CoreOS 部署的最基本項目，然後具備工作叢集和用戶端電腦。我們將使用和這些範例中完全相同的叢集名稱。此外，這些範例假定您正使用本機 Linux 主機來執行 **Fleet** 命令。




## <a id='simple'>範例 1：Docker 與 Hello World 搭配使用</a>

此為在單一 Docker 容器上執行的簡單 "Hello World" 應用程式。這會使用 [busybox Docker 中樞映像]。

在 Linux 用戶端電腦上，使用您最愛的文字編輯器來建立下列 **systemd** 單位檔案，並將其命名為 `helloworld.service`。(如需此語法的詳細資訊，請參閱[單位檔案]。)

```
[Unit]
Description=HelloWorld
After=docker.service
Requires=docker.service

[Service]

TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill busybox1
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

```

現在請連接 CoreOS 叢集，然後執行下列 **fleetctl** 命令來啟動這個單位。此輸出顯示此單位已啟動，並顯示其位置。


```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start helloworld.service


Unit helloworld.service launched on 62f0f66e.../100.79.86.62
```

>[AZURE.NOTE]若要執行遠端 **fleetctl** 命令，而不使用 **--tunnel** 參數，可選擇設定 FLEETCTL_TUNNEL 環境變數，以通道傳送要求。例如：`export FLEETCTL_TUNNEL=coreos-cluster.cloudapp.net:22`。


您可連接到容器以查看此服務的輸出：

```
fleetctl --tunnel coreos-cloudapp.cluster.net:22 journal helloworld.service

Mar 04 21:29:26 node-1 docker[57876]: Hello World
Mar 04 21:29:27 node-1 docker[57876]: Hello World
Mar 04 21:29:28 node-1 docker[57876]: Hello World
Mar 04 21:29:29 node-1 docker[57876]: Hello World
Mar 04 21:29:30 node-1 docker[57876]: Hello World
Mar 04 21:29:31 node-1 docker[57876]: Hello World
Mar 04 21:29:32 node-1 docker[57876]: Hello World
Mar 04 21:29:33 node-1 docker[57876]: Hello World
Mar 04 21:29:34 node-1 docker[57876]: Hello World
Mar 04 21:29:35 node-1 docker[57876]: Hello World
```

若要清除，請停止並卸載此單位。

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop helloworld.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload helloworld.service
```


## <a id='highavail'>範例 2：高可用性的 Apache 伺服器</a>

使用 CoreOS、Docker 和 **Fleet** 的優勢在於，這很容易以高可用性的方式執行服務。在此範例中，您將部署服務，這包含在 Apache Web 伺服器中的三個完全相同的容器。這些容器將在此叢集的三個 VM 中執行。此範例和[以 Fleet 啟動容器]的範例類似，並且使用 [CoreOS Apache Docker 中樞映像]。

>[AZURE.NOTE]若要執行高可用性的 Apache 伺服器，您將需要在虛擬機器上設定負載平衡的 HTTP 端點 (公用連接埠 80、私用連接埠 80)。您可以在建立 CoreOS 叢集後使用 Azure 管理入口網站或 **azure vm endpoint** 命令進行這項動作。如需詳細資訊，請參閱[設定負載平衡集]。

在用戶端電腦上，請使用您最愛的文字編輯器來建立 **systemd** 範本單位檔案，名為 apache@.service。您將使用該範本來啟動三個個別的執行個體，名為 apache@1.service、apache@2.service 和 apache@3.service：

```
[Unit]
Description=High Availability Apache
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill apache1
ExecStartPre=-/usr/bin/docker rm apache1
ExecStartPre=/usr/bin/docker pull coreos/apache
ExecStart=/usr/bin/docker run -rm --name apache1 -p 80:80 coreos/apache /usr/sbin/apache2ctl -D FOREGROUND
ExecStop=/usr/bin/docker stop apache1

[X-Fleet]
X-Conflicts=apache@*.service
```

>[AZURE.NOTE]`X-Conflicts` 屬性會告知 CoreOS，此容器只有一個執行個體可以在指定的 CoreOS 主機上執行。如需詳細資訊，請參閱[單位檔案]。

現在請在 CoreOS 叢集上啟動單位執行個體。您應該會發現它們在三個不同的機器上執行：

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start apache@{1,2,3}.service

unit apache@3.service launched on 00c927e4.../100.79.62.16
unit apache@1.service launched on 62f0f66e.../100.79.86.62
unit apache@2.service launched on df85f2d1.../100.78.126.15

```
若要與其中一個單位上執行的 Apache 伺服器連線，請傳送一個簡單的要求到裝載此 CoreOS 叢集的雲端服務。

`curl http://coreos-cluster.cloudapp.net`

您會看到從 Apache 伺服器傳回的預設文字，類似於：

```
<htm\l><body><h1>It works!</h1>
<p>This is the default web page for this server.</p>
<p>The web server software is running but no content has been added, yet.</p>
</body></html>
```

您可以嘗試關閉叢集內的一部或多部虛擬機器，以確認 Apache 服務仍繼續執行。

完成後，請停止並卸載單位。

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop apache@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload apache@{1,2,3}.service

```

## 後續步驟

您可以在 Azure 上三個節點的 CoreOS 叢集嘗試更多動作。透過閱讀 [Tim Park 的 CoreOS 教學課程]、[Patrick Chanezon 的 CoreOS 教學課程]、[Docker] 文件和 [CoreOS 概觀]，瀏覽如何建立更複雜的叢集、如何使用 Docker，以及如何建立更有趣的應用程式。

如需在 Azure 上的 Linux VM 使用開放原始碼環境的詳細資訊，另請參閱 [Azure 上的 Linux 和開放原始碼運算]。

<!--Link references-->
[Azure Command-Line Interface (Azure)]: ../xplat-cli.md
[ CoreOS]: https://coreos.com/
[CoreOS 概觀]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Tim Park 的 CoreOS 教學課程]: https://github.com/timfpark/coreos-azure
[Patrick Chanezon 的 CoreOS 教學課程]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[如何在 Azure 上使用 CoreOS]: virtual-machines-linux-coreos-how-to.md
[設定負載平衡集]: http://msdn.microsoft.com/library/azure/dn655055.aspx
[以 Fleet 啟動容器]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[單位檔案]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[busybox Docker 中樞映像]: https://registry.hub.docker.com/_/busybox/
[CoreOS Apache Docker 中樞映像]: https://registry.hub.docker.com/u/coreos/apache/
[Azure 上的 Linux 和開放原始碼運算]: virtual-machines-linux-opensource.md
 

<!---HONumber=July15_HO2-->