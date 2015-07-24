<properties
   pageTitle="從 Windows 搭配使用 SSH 金鑰與 Linux 架構叢集上的 Hadoop | Microsoft Azure"
   description="了解如何建立和使用 SSH 金鑰來驗證以 Linux 為基礎的 HDInsight 叢集。使用 PuTTY SSH 用戶端，從 Windows 用戶端連接叢集。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop (預覽)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

以 Linux 為基礎的 Azure HDInsight 叢集提供密碼或安全殼層 (SSH) 金鑰來使用 SSH 存取的選項。本文提供從以 Windows 為基礎的用戶端使用 PuTTY SSH 用戶端連接到 HDInsight 的資訊。

> [AZURE.NOTE]本文中的步驟假設您是使用以 Windows 為基礎的用戶端。如果您使用 Linux、Unix 或 OS X 用戶端，請參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)。

##必要條件

* 適用於以 Windows 為基礎的用戶端的 **PuTTY** 和 **PuTTYGen**您可從下列位置取得這些公用程式：[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

* 支援 HTML5 的新式網頁瀏覽器。

或

* [適用於 Mac、Linux 和 Windows 的 Azure CLI](../xplat-cli.md)。

##什麼是 SSH？

SSH 是用來登入遠端伺服器並在其中遠端執行命令的公用程式。SSH 會用以 Linux 為基礎的 HDInsight 來與叢集前端節點建立加密的連線，並提供命令列供您輸入命令。然後直接在該伺服器上執行命令。

##建立 SSH 金鑰 (選擇性)

在建立以 Linux 為基礎的 HDInsight 叢集時，您可以選擇使用密碼或 SSH 金鑰來驗證伺服器。SSH 金鑰比較安全，因為它們依據的是憑證。如果您計劃搭配使用 SSH 金鑰與叢集，請使用下列資訊。

1. 開啟 PuTTYGen。

2. 在 [**要產生的金鑰類型**] 中選取 [**SSH-2 RSA**]，然後按一下 [**產生**]。

	![PuTTYGen 介面](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. 在進度列下方的區域中移動滑鼠直到進度列填滿為止。移動滑鼠時會產生用來產生金鑰的隨機資料。

	![移動滑鼠](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

	在產生金鑰後，就會顯示公開金鑰。

4. 若要提高安全性，您可以在 [**金鑰複雜密碼**] 欄位中輸入複雜密碼，然後在 [**確認複雜密碼**] 欄位中輸入相同的值。

	![複雜密碼](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)

	> [AZURE.NOTE]強烈建議您對此金鑰使用安全的複雜密碼。不過如果您忘記此複雜密碼，將無法加以復原。

5. 按一下 [**儲存私密金鑰**] 將金鑰儲存到 **.ppk** 檔案。此金鑰會用來驗證以 Linux 為基礎的 HDInsight 叢集。

	> [AZURE.NOTE]此金鑰可用來存取以 Linux 為基礎的 HDInsight 叢集，因此請將它儲存在安全的位置。

6. 按一下 [**儲存公開金鑰**] 將金鑰儲存為 **.txt** 檔案。這可讓您在日後建立其他以 Linux 為基礎的 HDInsight 叢集時重複使用此公開金鑰。

	> [AZURE.NOTE]PuTTYGen 的頂端也會顯示此公開金鑰。您可以用滑鼠右鍵按一下此欄位，複製值並貼到表單中，例如 Azure 入口網站中的 HDInsight 精靈。

##建立以 Linux 為基礎的 HDInsight 叢集

在建立以 Linux 為基礎的 HDInsight 叢集時，您必須提供先前建立的公開金鑰。從以 Windows 為基礎的用戶端，有兩種方式可以建立以 Linux 為基礎的 HDInsight 叢集：

* **Azure 入口網站** - 使用網頁型入口網站來建立叢集。

* **適用於 Mac、Linux 和 Windows 的 Azure CLI** - 使用命令列命令建立叢集。

這兩種方法都需要公開金鑰。如需建立以 Linux 為基礎的 HDInsight 叢集的完整資訊，請參閱[佈建以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

###Azure 入口網站

使用入口網站來建立以 Linux 為基礎的 HDInsight 叢集時，您必須在下列表單中輸入使用者名稱和密碼或公開金鑰：

![要求公開金鑰的表單映像](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

這會建立指定使用者的登入資訊，並啟用密碼驗證或 SSH 金鑰驗證。

###適用於 Mac、Linux 和 Windows 的 Azure 命令列介面

您可以使用[適用於 Mac、Linux 和 Windows 的 Azure CLI](../xplat-cli.md)，使用 `azure hdinsight cluster create` 來建立新叢集。

如需使用這個命令的詳細資訊，請參閱[使用自訂選項在 HDInsight 中佈建 Hadoop Linux 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

##連線至以 Linux 為基礎的 HDInsight 叢集

1. 開啟 PuTTY。

	![putty 介面](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. 如果您在建立使用者帳戶時提供 SSH 金鑰，您必須執行下列步驟來選取要在驗證叢集時使用的私密金鑰：

	在 [**類別**] 中，依序展開 [**連接**] 和 [**SSH**]，然後選取 [**驗證**]。最後，按一下 [**瀏覽**]，然後選取內含私密金鑰的 .ppk 檔案。

	![putty 介面，選取私密金鑰](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. 在 [**類別**] 中，選取 [**工作階段**]。在 [**PuTTY 工作階段的基本選項**] 畫面上，將您的 HDInsight 伺服器的 SSH 位址輸入到 [**主機名稱 (或 IP 位址)**] 欄位。SSH 位址是叢集名稱加上 **-ssh.azurehdinsight.net**。例如，**mycluster-ssh.azurehdinsight.net**。

	![已輸入 ssh 位址的 putty 介面](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. 若要儲存連線資訊供日後使用，請在 [**儲存的工作階段**] 底下輸入此連線的名稱，然後按一下 [**儲存**]。連線便會加入已儲存的工作階段清單。

5. 按一下 [**開啟**] 來連線到叢集。

	> [AZURE.NOTE]如果這是您第一次連線到叢集，您會收到安全性警示。這是正常現象。請選取 [**是**] 快取伺服器的 RSA2 金鑰以繼續進行。

6. 出現提示時，輸入您建立叢集時所輸入的使用者。如果您對使用者提供密碼，系統會提示您一併輸入密碼。

###連接至背景工作節點

背景工作節點無法直接從 Azure 資料中心外部存取，但是可以透過 SSH 從叢集前端節點存取。

如果您在建立使用者帳戶時提供 SSH 金鑰，當您想要連接至背景工作節點時，您必須執行下列步驟來選取要在驗證叢集時使用的私密金鑰。

1. 從下列位置安裝 Pageant：[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。此公用程式是用於快取 PuTTY 的 SSH 金鑰。

2. 執行 Pageant。它會最小化為狀態列中的圖示。以滑鼠右鍵按一下圖示，然後選取 [**新增金鑰**]。

    ![新增金鑰](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. 當瀏覽對話方塊顯示時，請選取包含金鑰的 .ppk 檔案，然後按一下 [**開啟**]。這樣會將金鑰新增至 Pageant，在連接至叢集時將金鑰提供給 PuTTY。

    > [AZURE.IMPORTANT]如果您使用 SSH 金鑰來保護您的帳戶的安全，您必須先完成上述步驟，才能連接至背景工作節點。

4. 開啟 PuTTY。

5. 如果您使用 SSH 金鑰進行驗證，請在 [**類別**] 區段中，依序展開 [**連接**]、[**SSH**]，然後選取 [**驗證**]。

    在 [**驗證參數**] 區段中，啟用 [**允許代理程式轉送**]。這樣會允許 PuTTY 在連接至背景工作節點時，透過連接至叢集前端節點，自動通過憑證驗證。

    ![允許代理程式轉送](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. 如稍早所述連接到叢集。如果您使用 SSH 金鑰進行驗證，不需要選取金鑰 - 會對叢集使用新增至 Pageant 的 SSH 金鑰來進行驗證。

7. 建立連接之後，請使用下列項目以擷取您的叢集中的節點清單。將 *ADMINPASSWORD* 取代為您的叢集系統管理員帳戶的密碼。將 *CLUSTERNAME* 取代為您叢集的名稱。

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    這樣會以 JSON 格式傳回叢集中節點的資訊，包括 `host_name`，其中包含每個節點的完整網域名稱 (FQDN)。以下是 **curl** 命令傳回之 `host_name` 項目的範例：

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. 當您具有想要連接之背景工作節點的清單之後，請從 PuTTY 工作階段使用下列命令，以開啟與背景工作節點的連線：

        ssh USERNAME@FQDN

    將 *USERNAME* 取代為您的 SSH 使用者名稱，將 *FQDN* 取代為背景工作節點的 FQDN。例如，`workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`。

    > [AZURE.NOTE]如果您使用密碼以驗證您的 SSH 工作階段，則系統會提示您再次輸入密碼。如果您使用 SSH 金鑰，連線應該沒有任何提示即會完成。

9. 建立工作階段之後，針對 PuTTY 的提示會從 `username@headnode` 變更為 `username@workernode`，以指出您已連接至背景工作節點。目前您執行的任何命令會在背景工作節點上執行。

10. 完成在背景工作節點上執行動作之後，請使用 `exit` 命令以關閉背景工作節點的工作階段。這樣會帶您返回 `username@headnode` 提示字元。

##新增更多帳戶

如果您需要將更多帳戶新增至您的叢集，請執行下列步驟：

1. 如先前所述為新的使用者帳戶產生新的公開金鑰和私密金鑰。

2. 在連往叢集的 SSH 工作階段中，使用下列命令加入新使用者：

		sudo adduser --disabled-password <username>

	這會建立新的使用者帳戶，但會停用密碼驗證。

3. 使用下列命令建立用來保存金鑰的目錄和檔案：

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. 在 nano 編輯器開啟時，複製並貼上新使用者帳戶的公開金鑰內容。最後，使用 **Ctrl-X** 來儲存檔案並結束編輯器。

	![具有範例金鑰之 nano 編輯器的映像](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. 使用下列命令將 .ssh 資料夾和內容的擁有權變更為新的使用者帳戶：

		sudo chown -hR <username>:<username> /home/<username>/.ssh

6. 您現在應該就能使用新的使用者帳戶和私密金鑰驗證伺服器。

##<a id="tunnel"></a>SSH 通道

SSH 也可用來建立通道以將本機要求 (例如 Web 要求) 傳送到 HDInsight 叢集。要求便會路由至要求的資源，彷彿要求是在 HDInsight 叢集前端節點上產生。

在 HDInsight 叢集上所要存取的 Web 服務是使用叢集之前端節點或背景工作節點的內部網域名稱時，這種方式最為適用。例如，Ambari 網頁的某些區段使用內部網域名稱，例如 **headnode0.mycluster.d1.internal.cloudapp.net**。這些名稱無法從叢集外加以解析，但是透過 SSH 以通道傳送的要求會在叢集內產生，並且將會正確解析。

使用下列步驟來建立 SSH 通道，並設定瀏覽器用此通道來連接到叢集。

1. 開啟 PuTTY，並輸入[連線至以 Linux 為基礎的叢集](#connect-to-a-linux-based-hdinsight-cluster)區段中所記載的連線資訊。

2. 在對話方塊左側的 [**類別**] 區段中，依序展開 [**連接**] 和 [**SSH**]，最後選取 [**通道**]。

3. 在 [**控制 SSH 連接埠轉送的選項**] 表單中提供下列資訊：

	* **來源連接埠** - 您想要轉送之用戶端上的連接埠。例如，**9876**。

	* **目的地** - 以 Linux 為基礎的 HDInsight 叢集的 SSH 位址。例如，**mycluster-ssh.azurehdinsight.net**。

	* **動態** - 啟用動態 SOCKS Proxy 路由。

	![通道選項的映像](./media/hdinsight-hadoop-linux-use-ssh-windows/puttytunnel.png)

4. 按一下 [**新增**] 以新增設定，然後按一下 [**開啟**] 開啟 SSH 連線。

5. 出現提示時，登入伺服器。這會建立 SSH 工作階段，並啟用通道。

6. 設定用戶端程式 (例如 Firefox) 使用 **localhost:9876** 做為 **SOCKS v5** proxy。Firefox 的設定如下所示：

	![Firefox 設定的映像](./media/hdinsight-hadoop-linux-use-ssh-windows/socks.png)

	> [AZURE.NOTE]選取 [**遠端 DNS**] 會使用 HDInsight 叢集解析 DNS 要求。若未選取，則會在本機解析 DNS。

	您可以驗證是否透過通道路由傳送流量，方法是在 Firefox 中啟用和停用 Proxy 設定的情況下造訪網站，例如 [http://www.whatismyip.com/](http://www.whatismyip.com/)。在啟用時，是使用 Microsoft Azure 資料中心內之機器的 IP 位址。

###瀏覽器延伸模組

當設定瀏覽器使用通道的功能在運作時，您通常不會想透過通道傳送所有流量。瀏覽器延伸模組 (例如 [FoxyProxy](http://getfoxyproxy.org/)) 支援 URL 要求的模式比對 (僅限 FoxyProxy Standard 或 Plus)，以便只有特定 URL 的要求會透過通道傳送。

如果您已安裝 FoxyProxy Standard，請使用下列步驟將它設定為只透過通道轉送 HDInsight 的流量。

1. 在您的瀏覽器中開啟 FoxyProxy 延伸模組。比方說，在 Firefox 中選取 [位址] 欄位旁的 FoxyProxy 圖示。

	![foxyproxy 圖示](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxy.png)

2. 選取 [**加入新的 Proxy**]、選取 [**一般**] 索引標籤，然後輸入 **HDInsightProxy** 的 Proxy 名稱。

	![foxyproxy 一般](./media/hdinsight-hadoop-linux-use-ssh-windows/foxygeneral.png)

3. 選取 [**Proxy 詳細資料**] 索引標籤並填入下列欄位：

	* **主機或 IP 位址** - 這是 localhost，因為我們使用本機電腦上的 SSH 通道。

	* **連接埠** - 這是用於 SSH 通道的連接埠。

	* **SOCKS Proxy** - 選取此選項讓瀏覽器使用通道做為 Proxy。

	* **SOCKS v5** - 選取此選項以設定 Proxy 的要求版本。

	![foxyproxy proxy](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxyproxy.png)

4. 選取 [**URL 模式**] 索引標籤，然後選取 [**加入新的模式**]。使用下列欄位定義模式，然後按一下 [**確定**]：

	* **模式名稱** - **headnode** - 這是易記的模式名稱。

	* **URL 模式** - ***headnode*** - 這會定義模式來比對任何 URL 中是否有 **headnode** 一字。

	![foxyproxy 模式](./media/hdinsight-hadoop-linux-use-ssh-windows/foxypattern.png)

4. 選取 [**確定**] 以加入 Proxy 並關閉 [**Proxy 設定**]。

5. 在 FoxyProxy 對話方塊頂端，將 [**選取模式**] 變更為 [**根據預先定義的模式和優先順序使用 Proxy**]，然後按一下 [**關閉**]。

	![foxyproxy 選取模式](./media/hdinsight-hadoop-linux-use-ssh-windows/selectmode.png)

在執行這些步驟後，只有包含 **headnode** 字串之 URL 的要求會透過 SSL 通道路由傳送。

##後續步驟

您已經了解如何使用 SSH 金鑰進行驗證，接著請了解如何在 HDInsight 上搭配使用 MapReduce 和 Hadoop。

* [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)

* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)

* [搭配 HDInsight 使用 MapReduce 工作](hdinsight-use-mapreduce.md)

<!---HONumber=July15_HO2-->