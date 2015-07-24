<properties
   pageTitle="在 Azure 上部署 3 個節點的 Deis 叢集"
   description="本文說明如何使用 Azure 資源管理員，在 Azure 上建立 3 個節點的 Deis 叢集。"
   services="virtual-machines"
   documentationCenter=""
   authors="HaishiBai"
   manager="larar"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# 部署 3 個節點的 Deis 叢集

本文將指導您逐步完成在 Azure 上佈建 [Deis](http://deis.io/) 叢集。它涵蓋建立必要的憑證，以及在新佈建的叢集上部署及調整 **Go** 應用程式範例的所有步驟。

下圖顯示已部署的系統之架構。系統管理員使用 Deis 工具 (如 **deis** 和 **deisctl**) 來管理叢集。連線是透過會將連線轉送到叢集上其中一個節點的 Azure 負載平衡器而建立。用戶端也是透過負載平衡器存取部署的應用程式。在此情況下，負載平衡器將流量轉送到 Deis 路由器網狀結構，進一步將流量路由至裝載於叢集上且相對應的 Docker 容器。

  ![已部署之 Desis 叢集的架構圖](media/virtual-machines-deis-cluster/architecture-overview.png)

若要完成下列步驟，您需要：

 * 有效的 Azure 訂用帳戶。如果沒有，您可以在 [azure.com](https://azure.microsoft.com) 免費取得一個。
 * 用於 Azure 資源群組的工作或學校識別碼。如果您有個人帳戶且使用 Microsoft ID 登入，則您需要[以您的個人識別碼建立一個供作識別碼](resource-group-create-work-id-from-personal.md)。
 * 依您的用戶端作業系統而定 -- [Azure PowerShell](powershell-install-configure.md) 或 [適用於 Mac、Linux 和 Windows 的 Azure CLI](xplat-cli-install.md)。
 * [OpenSSL](https://www.openssl.org/)。OpenSSL 是用來產生必要的憑證。
 * Git 用戶端，例如 [Git Bash](https://git-scm.com/)。
 * 若要測試範例應用程式，您也需要 DNS 伺服器。您也可以使用任何 DNS 伺服器或 支援萬用字元 A 記錄的服務。
 * 執行 Deis 用戶端工具的電腦。您可以使用本機電腦或虛擬機器。您可以在幾乎所有 Linux 散發套件執行這些工具，但是以下指示使用的是 Ubuntu。

## 佈建叢集

在本節中，您將使用開放原始碼儲存機制 [azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) 中的 [Azure 資源管理員](../resource-group-overview.md)範本。首先，您會複製範本。然後，您會建立用於驗證的新 SSH 金鑰組。接下來，您會為叢集設定新的識別碼。最後，您會使用 Shell 指令碼或 PowerShell 指令碼佈建叢集。

1. 複製儲存機制：[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)。

        git clone https://github.com/Azure/azure-quickstart-templates

2. 移至範本資料夾：

        cd azure-quickstart-templates\deis-cluster-coreos

3. 使用 ssh-keygen 建立新的 SSH 金鑰組：

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. 使用上一步的私密金鑰產生憑證：

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. 移至 [https://discovery.etcd.io/new](https://discovery.etcd.io/new) 來產生新的叢集權杖，看起來如下：

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br /> 每個 CoreOS 叢集必須有這項免費服務提供的唯一權杖。詳細資訊請參閱 [CoreOS 文件](https://coreos.com/docs/cluster-management/setup/cluster-discovery/) (英文)。

6. 修改 **cloud-config.yaml**，以新權杖取代現有的 **discovery** 權杖：

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. 修改 **azuredeploy parameters.json** 檔案：開啟您在步驟 4 於文字編輯器中建立的憑證。將 `----BEGIN CERTIFICATE-----` 和 `-----END CERTIFICATE-----` 之間的所有文字複製到 **sshKeyData** 參數中 (您必須移除所有新行字元)。

8. 修改 **newStorageAccountName** 參數。這是 VM OS 磁碟的儲存體帳戶。此帳戶名稱必須是全域唯一的。

9. 修改 **publicDomainName** 參數。這會成為 DNS 名稱與負載平衡器公用 IP 相關聯的一部分。最終的 FQDN 格式會是 _[此參數的值]_._[區域]_.cloudapp.azure.com。例如，如果名稱指定為 deishbai32，且資源群組部署至美國西部地區，則您負載平衡器會最終的 FQDN 會是 deishbai32.westus.cloudapp.azure.com。

10. 儲存參數檔案。然後您可以使用 Azure PowerShell 佈建叢集：

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  或 Azure CLI：

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. 資源群組佈建之後，您可以在 Azure 入口網站上看到群組中的所有資源。如以下螢幕擷取畫面所示，資源群組內有包含三個 VM 的虛擬網路，這些 VM 加入同一個可用性設定組。該群組也包含負載平衡器，其具有關聯的公用 IP。

  ![在 Azure 入口網站佈建的資源群組](media/virtual-machines-deis-cluster/resource-group.png)

## 安裝用戶端

您需要 **deisctl** 來控制 Deis 叢集。雖然 deisctl 會自動安裝在所有叢集節點中，但較佳做法是在個別的系統管理電腦上使用 deisctl。此外，因為所有節點都只設定私用 IP 位址，所以您會需要使用 SSH 在負載平衡器 (有公用 IP) 建立通道，以連線到節點電腦。以下是在各別的 Ubuntu 實體或虛擬機器上設定 deisctl 的步驟。

1. 安裝 deisctl：mkdir deis

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. 將您的私用金鑰加入 SSH 代理程式：

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. 設定 deisctl：

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

範本會定義傳入的 NAT 規則，將 2223 對應到執行個體 1、2224 對應到執行個體 2、2225 對應到執行個體 3。這會提供使用 deisctl 工具的備援。您可以在 Azure 入口網站檢查這些規則：

![負載平衡器上的 NAT 規則](media/virtual-machines-deis-cluster/nat-rules.png)

> [AZURE.NOTE]範本目前僅支援 3 個節點的叢集。這是因為 Azure 資源管理員範本 NAT 規則定義不支援迴圈與法的限制。

## 安裝並啟動 Deis 平台

您現在可以使用 deisctl 來安裝和啟動 Deis 平台：

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE]啟動平台需要一些時間 (最多 10 分鐘)。尤其啟動建立器服務需要較長的時間。而且有時要嘗試幾次才會成功：若作業似乎無回應，請嘗試輸入 `ctrl+c` 來中斷命令的執行，然後再試一次。

您可以使用 `deisctl list` 確認是否所有服務都在執行：

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

恭喜！ 您現在有在 Azure 上執行的 Deis 了！ 接下來，讓我們部署一個簡單的 Go 應用程式，看看叢集實際運作。

## 部署及調整 Hello World 應用程式

以下步驟說明如何將 Go 應用程式「Hello World」部署到叢集。步驟是以 [Deis 文件](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles) (英文) 為基礎。

1. 為了讓路由網狀結構正確運作，您指向負載平衡器公用 IP 的網域必須有萬用字元 A 記錄。以下螢幕擷取畫面顯示在 GoDaddy 上註冊之範例網域的 A 記錄：

    ![Godaddy A 記錄](media/virtual-machines-deis-cluster/go-daddy.png)
<p />
2. 安裝 deis：

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. 建立新的 SSH 金鑰，然後將公用金鑰加入 GitHub (當然，您也可以使用您現有的金鑰)。若要建立新的 SSH 金鑰組，請使用：

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. 在 GitHub 加入 id_rsa.pub，或您自行選擇的公用金鑰。您可以使用 SSH 金鑰設定畫面上的 [Add SSH key] 按鈕來加入。

  ![Github 金鑰](media/virtual-machines-deis-cluster/github-key.png) <p /> 5.註冊新的使用者：

        deis register http://deis.[your domain]
<p />
6. 加入 SSH 金鑰：

        deis keys:add [path to your SSH public key]
  <p />
7. 建立應用程式。

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8. git push 會觸發建置及部署 Docker 映像，這需要幾分鐘來完成。根據我的經驗，第 10 步驟 (將映像推送到私用儲存機制) 通常會無回應。若發生此情況，您可以停止程序，並使用 `deis apps:destroy –a <application name>` 移除應用程式，然後再試一次。您也可以使用 `deis apps:list` 來尋找您應用程式的名稱。如果一切都正常運作，您會在命令輸出的結尾看到訊息如下：

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. 確認應用程式是否正在運作：

        curl -S http://[your application name].[your domain]
  您應該會看到：

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. 將應用程式調整為 3 個執行個體：

        deis scale cmd=3
<p />
11. 或者，您可以使用 deis info 來檢查應用程式的詳細資料。以下是我的應用程式部署的輸出結果：

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## 後續步驟

本文章會引導您使用 Azure 資源管理員範本，完成所有佈建新 Deis 叢集的步驟。範本支援工具連線的備援，以及已部署應用程式的負載平衡。該範本也避免在成員節點使用公用 IP，這可以節省寶貴的公用 IP 資源，並提供主機應用程式更安全的環境。若要深入了解，請參閱下列文章：

[Azure 資源管理員概觀][resource-group-overview] [如何使用 Azure CLI][azure-command-line-tools] [搭配 Azure 資源管理員使用 Azure PowerShell][powershell-azure-resource-manager]

[azure-command-line-tools]: ../xplat-cli.md
[resource-group-overview]: ../resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md

<!---HONumber=July15_HO2-->