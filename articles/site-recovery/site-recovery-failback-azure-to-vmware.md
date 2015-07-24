<properties 
   pageTitle="從 Azure 到 VMware 的容錯回復步驟" 
   description="本文說明如何使用 Azure Site Recovery 和 vContinuum 工具，將虛擬機器容錯回復到 VMware。" 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="05/27/2015"
   ms.author="ruturajd@microsoft.com"/>

# 從 Azure 到 VMware 的容錯回復步驟

本文件將引導您逐步完成從 Azure 容錯回復到 VMware 站台所需的步驟。您必須已經依照 [VMware 到 Azure 的保護和復原](site-recovery-vmware-to-azure.md)教學課程中指定的步驟來執行。

成功容錯移轉到 Azure 之後，將可在 [虛擬機器] 索引標籤中取得虛擬機器。當您決定進行容錯回復之後，就需要依照下列步驟來執行。

請注意，當您從 Azure 容錯回復到 VMware 站台時，只能復原到虛擬機器。即使您在 VMware 上的初始來源是實體機器，在容錯移轉到 Azure 後緊接著容錯回復到 VMware，即會將它轉換為虛擬機器。

## 概觀

1.  安裝 vContinuum 伺服器內部部署

    a.將它設定為指向 CS

2.  在 Azure 上部署 PS

3.  安裝 MT 內部部署

4.  保護容錯移轉的 VM 回復到內部部署的步驟

    a.組態考量

5.  監視對於回復到內部部署之 VM 的保護

6.  將 VM 容錯移轉回到內部部署

以下是我們將使用下列步驟達成的安裝概觀。部分安裝已經在容錯移轉期間完成。

-   藍線是在容錯移轉期間使用的連線。

-   紅線是在容錯回復期間使用的連線。

-   帶有箭號的線條表示會透過網際網路。

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## 安裝 vContinuum 內部部署

VContinuum 安裝程式將可在[下載位置](http://go.microsoft.com/fwlink/?linkid=526305)取得。

此外，在 vConinuum 上安裝此處指定的修補程式 (可在[下載位置](http://go.microsoft.com/fwlink/?LinkID=533813)取得)。

1.  啟動安裝程式以開始安裝 vContinuum。在歡迎畫面中，按 [下一步] 開始指定設定之後

![](./media/site-recovery-failback-azure-to-vmware/image2.png)

2.  指定 CX 伺服器 IP 位址和 CX 伺服器連接埠。確定勾選 [使用 HTTPS] 核取方塊。

![](./media/site-recovery-failback-azure-to-vmware/image3.png)

    a.  To discover the CX IP go to the CS deployment on Azure and view
        its dashboard. The public IP address will be displayed under
        Public Virtual IP address.

![](./media/site-recovery-failback-azure-to-vmware/image4.png)

    b.  To discover the CX public port go to the endpoints tab in the VM
        page and identify the HTTPs endpoints public port

![](./media/site-recovery-failback-azure-to-vmware/image5.png)

3.  指定 CS 複雜密碼。您必須在 CS 註冊期間記下複雜密碼。您也必須在 MT 和 PS 部署期間使用複雜密碼。萬一您不記得複雜密碼，可以前往 Azure 上的 CS 伺服器，然後尋找儲存在 C:\\Program Files (x86)\\InMage Systems\\private\\connection.passphrase 下方的複雜密碼

    ![](./media/site-recovery-failback-azure-to-vmware/image6.png)

4.  指定要安裝 vContinuum 伺服器的位置，然後開始安裝

    ![](./media/site-recovery-failback-azure-to-vmware/image7.png)

5.  安裝完成之後，您就可以啟動 vContinuum 以查看它的運作方式。

    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)

## 在 Azure 上安裝 PS 伺服器

處理序伺服器必須安裝在 Azure 上，讓 Azure 中的 VM 可以將資料傳回內部部署的 MT。您需要在 Azure 上組態伺服器所在的相同網路中部署 PS。

1.  在 Azure 的 [組態伺服器] 頁面中，選取要加入新的處理序伺服器 ![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  在處理序伺服器上設定下列設定來部署新的伺服器

    a.指定處理序伺服器的名稱

    b.輸入要以系統管理員身分連接到虛擬機器的使用者名稱

    c.輸入要用來登入的密碼

    d.選取處理序伺服器必須向其註冊的組態伺服器。確定您選取了正確的組態伺服器。這是您用來保護和容錯移轉虛擬機器的同一部伺服器。

    e.指定您需要將處理序伺服器部署到其中的 Azure 網路。確定您選取了與組態伺服器網路相同的網路。

    f.從選取的子網路指定唯一的 IP 位址。

    g.開始部署處理序伺服器。

![](./media/site-recovery-failback-azure-to-vmware/image10.png)

1.  將觸發部署處理序伺服器的工作

![](./media/site-recovery-failback-azure-to-vmware/image11.png)

在 Azure 上部署處理序伺服器之後，您就能使用指定的認證來登入該伺服器。使用您在正向保護期間所使用的相同步驟來註冊 PS。

![](./media/site-recovery-failback-azure-to-vmware/image12.png)

在容錯回復期間註冊的伺服器將不會顯示於 VM 屬性下方。它們將會只顯示於它們已註冊之組態伺服器下方的 [伺服器] 索引標籤中。

大約需要 10-15 分鐘，PS 才會列於 CS 下方。

## 安裝 MT 伺服器內部部署

根據來源端虛擬機器而定，您可能需要安裝 Linux 或 Windows 主要目標伺服器內部部署。

### 部署 Windows MT

Windows MT 已經隨附於 vContinuum 安裝程式中。當您安裝 vContinuum 時，MT 也會部署於同一部機器上，並向組態伺服器註冊。

1.  若要開始部署，請在 ESX 主機上建立空的機器內部部署，該主機是您想要從 Auzre 將 VM 復原到其中的主機。

2.  確定至少已將兩個磁碟連接到 VM - 一個用於作業系統，另一個則用於保留磁碟機。

3.  安裝作業系統。

4.  在伺服器上安裝 vContinuum。這也會完成 MT 的安裝。

### 部署 Linux MT

1.  若要開始部署，請在 ESX 主機上建立空的機器內部部署，該主機是您想要從 Auzre 將 VM 復原到其中的主機。

2.  確定至少已將兩個磁碟連接到 VM - 一個用於作業系統，另一個則用於保留磁碟機。

3.  安裝 Linux 作業系統。

    a.注意：Linux 主要目標 (MT) 系統不應針對根或保留儲存空間使用 LVM。預設會設定 Linux MT，以避免 LVM 磁碟分割/磁碟探索。

    b.您可以建立的磁碟分割為 ![](./media/site-recovery-failback-azure-to-vmware/image13.png)

4.  開始安裝 MT 之前，請執行下列的安裝前步驟。

#### 作業系統的安裝前步驟

若要取得 Linux 虛擬機器中每個 SCSI 硬碟的 SCSI 識別碼，您應該啟用參數 “disk.EnableUUID = TRUE”。

若要啟用此參數，請依照以下指定的步驟執行：

a.關閉虛擬機器。

b.以滑鼠右鍵按一下左面板中的 VM 項目，然後選取 [編輯設定]。

c.按一下 [選項] 索引標籤。

d.選取左方的 [進階] > [一般項目]，然後按一下右方顯示的 [組態參數]。

![](./media/site-recovery-failback-azure-to-vmware/image14.png)

當機器正在執行時，[組態參數] 選項將處於停用狀態。若要讓此索引標籤變成作用中狀態，請關閉機器。

e.查看含有 **disk.EnableUUID** 的資料列是否已經存在？

如果存在且已將值設為 False，即會使用 True 來覆寫值 (True 和 False 值是不區分大小寫的)。

如果存在且已設為 True，可按一下 [取消]，然後在其開機之後，於客體作業系統內部測試 SCSI 命令。

f.如果不存在，可按一下 [加入資料列]。

在 [名稱] 欄中加入 disk.EnableUUID。

將其值儲存為 TRUE

注意：請勿為上述值加上雙引號。

![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### 下載並安裝其他封裝

注意：請先確定系統具有網際網路連線，然後再下載並安裝其他封裝。

# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

上述命令將從 CentOS 6.6 儲存機制下載如下所述的 15 個封裝並進行安裝。

bc-1.06.95-1.el6.x86_64.rpm

busybox-1.15.1-20.el6.x86_64.rpm

elfutils-libs-0.158-3.2.el6.x86_64.rpm

kexec-tools-2.0.0-280.el6.x86_64.rpm

lsscsi-0.23-2.el6.x86_64.rpm

lzo-2.03-3.1.el6_5.1.x86_64.rpm

perl-5.10.1-136.el6_6.1.x86_64.rpm

perl-Module-Pluggable-3.90-136.el6_6.1.x86_64.rpm

perl-Pod-Escapes-1.04-136.el6_6.1.x86_64.rpm

perl-Pod-Simple-3.13-136.el6_6.1.x86_64.rpm

perl-libs-5.10.1-136.el6_6.1.x86_64.rpm

perl-version-0.77-136.el6_6.1.x86_64.rpm

rsync-3.0.6-12.el6.x86_64.rpm

snappy-1.1.0-1.el6.x86_64.rpm

wget-1.12-5.el6_6.1.x86_64.rpm

注意：如果來源機器會針對根目錄或開機裝置使用 Reiser 或 XFS 檔案系統，則下列封裝應該下載並安裝於 Linux 主要目標上，才能提供保護。

# cd /usr/local

# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm

# wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### 套用自訂組態變更

套用自訂組態變更之前，請確定您已經完成後續安裝步驟

若要套用自訂組態變更，請依照如下所述的步驟執行：

1. 將 RHEL 6-64 整合代理程式二進位檔複製到新建立的作業系統。

2. 執行下列命令來解壓縮二進位檔。

**tar zxvf <檔案名稱>**

3. 執行下列命令來授與權限。

# **chmod 755 ./ApplyCustomChanges.sh**

4. 執行下列命令來執行指令碼。

**# ./ApplyCustomChanges.sh**

注意：只需在伺服器上執行指令碼一次。順利執行上述指令碼之後，請將伺服器**重新開機**。

### 開始 MT 安裝

[下載](http://go.microsoft.com/fwlink/?LinkID=529757) Linux 主要目標伺服器安裝檔案。

使用您選擇的 sftp 用戶端公用程式，將下載的 Linux 主要目標伺服器安裝程式複製到 Linux 主要目標虛擬機器。或者，您可以登入 Linux 主要目標虛擬機器，並使用 wget 從提供的連結下載安裝套件。

使用您選擇的 ssh 用戶端，來登入 Linux 主要目標伺服器虛擬機器。

如果您已連接到 Azure 網路 (您已在其上透過 VPN 連線部署 Linux 主要目標伺服器)，則可使用從虛擬機器儀表板取得之 Linux 主要目標伺服器的內部 IP 位址，以及使用安全殼層連接到 Linux 主要目標伺服器的連接埠 22。

如果您是透過公用網際網路連線連接到 Linux 主要目標伺服器，則可使用 Linux 主要目標伺服器的公用虛擬 IP 位址 (從虛擬機器儀表板頁面)，以及針對 ssh 建立的公用端點來登入 Linux 主要目標伺服器。

從壓縮的 Linux 主要目標伺服器安裝程式 tar 封存中解壓縮檔案，方法是從您已將 Linux 主要目標伺服器安裝程式複製到其中的目錄執行

*"tar – xvzf Microsoft-ASR_UA_8.2.0.0_RHEL6-64 *"*。

![](./media/site-recovery-failback-azure-to-vmware/image16.png)

如果您將安裝程式檔案解壓縮到不同的目錄，請將目錄變更為要解壓縮 tar 封存內容的目錄。從這個目錄路徑執行 “sudo ./install.sh”

![](./media/site-recovery-failback-azure-to-vmware/image17.png)

當系統提示您選擇這個代理程式的主要角色時，請選取 2 (主要目標)

保留其他互動式安裝選項的預設值。

在進行剩餘的安裝期間請等待，直到主機組態介面出現為止。適用於 Linux 主要目標伺服器的主機組態公用程式是一個命令列公用程式。請勿調整 ssh 用戶端公用程式視窗的大小。使用方向鍵來選取 [全域] 選項，然後按鍵盤上的 Enter 鍵。

![](./media/site-recovery-failback-azure-to-vmware/image18.png)

![](./media/site-recovery-failback-azure-to-vmware/image19.png)

1.  針對 [IP] 欄位，輸入從虛擬機器儀表板頁面取得的組態伺服器內部 IP 位址，然後按 Enter 鍵。

2.  針對 [連接埠] 欄位輸入 22，然後按 Enter 鍵。

3.  讓 [使用 HTTPS：] 保持 [是]。按 Enter 鍵一次以上。

4.  輸入組態伺服器上產生的複雜密碼。如果您是從 Windows 機器使用 PuTTY 用戶端來 ssh 到 Linux 主要目標伺服器虛擬機器，您可以使用 Shift+Insert 鍵來貼上剪貼簿的內容。使用 Ctrl+C 鍵，將複雜密碼複製到本機剪貼簿，然後使用 Shift+Insert 鍵來貼上它。按 Enter 鍵

5.  使用向右鍵瀏覽到結束並按 Enter 鍵。

等待安裝完成

![](./media/site-recovery-failback-azure-to-vmware/image20.png)

如果您基於某些原因而無法向 Linux 主要目標伺服器註冊您的組態伺服器，則您應該從 /usr/local/ASR/Vx/bin/hostconfigcli 執行主機組態公用程式，再次執行這個動作 (您必須先以進階使用者身分執行 chmod，來設定此目錄的存取權限)


#### 向組態伺服器驗證主要目標伺服器的註冊。

您可以瀏覽 Azure Site Recovery 保存庫中 [組態伺服器] 頁面下方的 [伺服器詳細資料] 頁面，來驗證主要目標伺服器已成功向組態伺服器註冊


## 開始保護回復為內部部署的虛擬機器

將 VM 容錯回復為內部部署之前，您必須先保護回復為內部部署的虛擬機器。請遵循以下步驟來保護應用程式的 VM。

### 暫存磁碟機的注意事項

將 VM 容錯移轉到 Azure 時，它會新增額外的暫存磁碟機以供分頁檔使用。這通常是您已容錯移轉的 VM 不需要的額外磁碟機，因為它可能已經有分頁檔專用的磁碟機。

在開始反向保護虛擬機器之前，您需要確定磁碟機已離線，使它不會受到保護。

若要這樣做，

1.  開啟 [電腦管理] (透過控制台系統管理工具，或者使用滑鼠右鍵按一下總管視窗中的 [這部電腦]，然後選取 [管理])。

2.  選取 [儲存管理]，即會列出磁碟已上線且已連接到機器。

3.  選取連接到機器的暫存磁碟，並選擇使其離線。

4.  成功將其離線之後，您可以繼續以相反方向來保護虛擬機器。

### VM 的保護方案

在 Azure 入口網站上，查看虛擬機器的狀態，並確定它們已容錯移轉。

![](./media/site-recovery-failback-azure-to-vmware/image21.png)

注意：從 Azure 容錯移轉回到內部部署期間，Azure VM 會被視為類似於實體 VM。容錯移轉到內部部署是回復到虛擬機器。

1.  啟動機器上的 vContinuum

![](./media/site-recovery-failback-azure-to-vmware/image8.png)

1.  在 [選擇應用程式] 設定中，選取 [P2V]

2.  按一下 [新增保護] 選項來開始

3.  在開啟的新視窗中，您將可開始保護回復到內部部署的虛擬機器。

    a.根據您想要容錯回復和 [取得詳細資料] 的 VM 選取 [作業系統類型]

    b.在 [主要伺服器詳細資料] 中，您需要識別想要保護的虛擬機器。

    c.虛擬機器會依照它們的電腦主機名稱列出，而不是它們是否會顯示於 vCenter 或 Azure 上

    d.虛擬機器在容錯移轉之前會列於虛擬機器所在的 vCenter 主機名稱下方。

    e.一旦識別出要保護的 VM 之後，請逐一選取它們。

4.  當您選取要保護的虛擬機器 (而且已經將它容錯移轉到 Azure) 時，即會出現快顯視窗來提供兩個適用於虛擬機器的項目。這是因為 CS 偵測到兩個已向其註冊的虛擬機器執行個體。您需要移除內部部署 VM 的項目，如此就能保護正確的 VM。請注意，您將會依項目的電腦主機名稱看到這些項目。若要在此處識別正確的 Azure VM 項目，您可以登入 Azure VM，然後移至 C:\\Program Files (x86)\\Microsoft Azure Site Recovery\\Application Data\\etc。在 drscout.conf 檔案中，識別主機識別碼。在 vContinuum 對話方塊中，保留可在 VM 中找到 hostID 的項目。刪除所有其他項目。

![](./media/site-recovery-failback-azure-to-vmware/image22.png)

    a.  To select the correct VM – you can refer to its IP address. The
        IP address range on-premises will be the on-premises VM.

    b.  Click **Remove** to delete the entry

![](./media/site-recovery-failback-azure-to-vmware/image23.png)

    c.  Go to the vCenter and stop the virtual machine on the vCenter

    d.  Next you can also delete the virtual machines on-premises

5.  接下來，您必須指定要保護 VM 的 MT 內部部署伺服器。

    a.連接到您想要容錯回復的目標 vCenter

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

a.根據您要復原虛擬機器的目標主機來選取 MT 伺服器

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

1.  接著提供每個虛擬機器的複寫選項

![](./media/site-recovery-failback-azure-to-vmware/image25.png)

a.您需要選取復原端 [資料存放區] - 這是要將 VM 還原到其中的資料存放區

您必須針對每個 VM 提供不同選項，如下

<table>
<tr><td>選項</td><td>選項建議值</td></tr>
<tr><td>處理序伺服器 IP</td><td>選取您已部署於 Azure 上的 PS</td></tr>
<tr><td>保留大小 (以 MB 為單位)</td><td></td></tr>
<tr><td>保留值</td><td>1</td></tr>
<tr><td>天/小時</td><td>星期幾</td></tr>
<tr><td>一致性間隔</td><td>1</td></tr>
<tr><td>選取目標資料存放區</td><td>可在復原端使用的資料存放區。這個資料存放區應具備足夠空間，而且也可供您要在其上具限化虛擬機器的 ESX 主機使用。</td></tr>
</table>


1.  接下來，您可以設定虛擬機器將在容錯移轉到內部部署站台之後取得的屬性。您可以設定不同的屬性，如下所示

![](./media/site-recovery-failback-azure-to-vmware/image26.png)


  <table>
<tr><td>屬性</td><td>如何設定</td></tr>
<tr><td>網路組態</td><td>針對每個偵測到的 NIC，設定虛擬機器的容錯回復 IP 位址。選取 NIC，然後按一下 [變更]**** 來指定 IP 位址詳細資料。

</td></tr>
<tr><td>硬體組態</td><td>您可以指定 VM 的 CPU 和記憶體值。此設定可套用到您嘗試保護的所有 VM。若要識別 CPU 和記憶體的正確值，您可以參考 IAAS VM 角色大小，並查看核心數及指派的記憶體。
</td></tr>
<tr><td>顯示名稱</td><td>容錯移轉回到內部部署之後，您可以選擇重新命名虛擬機器，如同它在 vCenter 詳細目錄中所示。請注意，此處所示的預設值是虛擬機器的電腦主機名稱。若要識別 VM 名稱，您可以參考保護群組中的 VM 清單。</td></tr>
<tr><td>NAT 組態</td><td>以下將詳細討論</td></tr>
</table>

![](./media/site-recovery-failback-azure-to-vmware/image27.png)



> **PS NAT 設定選項**
>
> 若要啟用虛擬機器的保護，需要建立兩個通訊通道。
>
> 第一個通道位於虛擬機器和處理序伺服器之間。這個通道會從 VM 收集資料，並將它傳送到 PS。然後 PS 會將此資料傳送到主要目標。如果處理序伺服器和受保護的虛擬機器位於相同的 Azure vNet 上，則您不需使用 NAT 設定。如果 PS 和受保護的虛擬機器位於兩個不同的 vNet 上，則您需要指定適用於 PS 的 NAT 設定，並檢查第一個選項。
>
> ![](./media/site-recovery-failback-azure-to-vmware/image28.png)
>
> 若要識別處理序伺服器公用 IP，您可以前往 Azure 中的 PS 部署，並查看其公用 IP 位址。
>
> 第二個通道介於處理序伺服器的主要目標之間。是否使用 NAT 的選項取決於您是根據 MT 與 PS 之間的連線使用 VPN 連線，還是透過網際網路進行保護。如果 PS 是透過 VPN 來與 MT 通訊，則您不應選取此選項。如果主要目標需要透過網際網路來與處理序伺服器通訊，則請指定適用於 PS 的 NAT 設定。
>
> ![](./media/site-recovery-failback-azure-to-vmware/image29.png)
>
> 若要識別處理序伺服器公用 IP，您可以前往 Azure 中的 PS 部署，並查看其公用 IP 位址。
>
> ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

1.  如果您尚未刪除步驟 5.d 中指定的內部部署虛擬機器，而且如果您在步驟 7.a 中選取之要容錯回復到其中的資料存放區仍包含舊的 VMDK，則您也必須確定容錯回復的 VM 會建立於新的位置上。基於此因素，您可以選取 [進階設定]，並且在 [進階設定] 的 [資料夾名稱設定] 區段中指定要還原到其中的替代資料夾。

![](./media/site-recovery-failback-azure-to-vmware/image31.png)

[進階設定] 中的其他選項可保留為預設值。請確定您會將資料夾名稱設定套用到所有伺服器。

1.  接下來移至保護的最後一個階段。您需要在此處執行整備檢查，以確保虛擬機器已準備好回復到內部部署接受保護。

![](./media/site-recovery-failback-azure-to-vmware/image32.png)

	a.  Click on the readiness check and wait for it to complete.

	b.  The Readiness Report tab will give the information if all the
    virtual machines are ready.

	c.  If the Readiness Report is successful on all the virtual machines it
    will allow you to specify a name to the Protection plan

![](./media/site-recovery-failback-azure-to-vmware/image33.png)

	d.  Give the plan a new Name and begin Protect by clicking the button
    below.


1.  系統將立即開始保護。

    a.您可以在 vContinuum 上看到保護的進度

![](./media/site-recovery-failback-azure-to-vmware/image34.png)

	b.  Once the step **Activating Protection Plan** is completed, you can
    monitor the protection of the virtual machines via the ASR Portal.

	c.  You can also monitor the protection via Azure Site Recovery.

![](./media/site-recovery-failback-azure-to-vmware/image35.png)

您也可以按一下虛擬機器並在磁碟區複寫區段下監視其進度，藉以查看該配對的精確狀態。

![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## 準備容錯回復方案

您可以使用 vContinuum 來準備容錯回復方案，讓應用程式隨時都能容錯移轉回到內部部署。這些復原方案非常類似 ASR 復原方案。

1.  啟動 vContinuum，然後選取 [管理方案] 的選項。

2.  使用者即會看見子選項，請選取 [復原]。

![](./media/site-recovery-failback-azure-to-vmware/image37.png)

1.  您可以看到已用來保護虛擬機器的所有方案清單。這些方案與您可用來復原的方案相同。

2.  選取 [保護方案]，然後選取要在其中復原的所有 VM。

    a.選取每個 VM，您可以看到更多關於來源 VM、要將 VM 復原到其中的目標 ESX 伺服器及來源 VM 磁碟的詳細資料

3.  按 [下一步] 開始 [復原] 精靈

4.  選取要復原的虛擬機器

    a.查看您可以復原的所有虛擬機器清單

![](./media/site-recovery-failback-azure-to-vmware/image38.png)

	b.  You can **recover based on** multiple options however we recommend
    the **Latest Tag.** This will ensure that the latest data from the
    virtual machine will be used.

	c.  Select **Apply for All VMs** to ensure that the latest tag will be
    chosen for all the virtual machines.


1.  執行 [整備檢查]。 這將會通知是否已設定正確的參數，來啟用虛擬機器的最新標記復原。如果所有檢查都成功，請按 [下一步]，否則，請檢查記錄檔並解決錯誤。

![](./media/site-recovery-failback-azure-to-vmware/image39.png)

2.  在精靈的 VM 組態步驟中，確定已正確設定復原設定。萬一您需要不同的 VM 設定，您可以選擇變更它們。因為我們已經在保護期間完成此動作，所以您這次可選擇略過。

![](./media/site-recovery-failback-azure-to-vmware/image40.png)

1.  最後，檢閱將復原的虛擬機器清單。

    a.指定虛擬機器的復原順序。

注意：虛擬機器是使用電腦主機名稱來列出。它可能很難將電腦主機名稱對應到虛擬機器。若要對應名稱，您可以移至 Azure IAAS 中的虛擬機器儀表板，並查看虛擬機器的主機名稱。

![](./media/site-recovery-failback-azure-to-vmware/image41.png)

1.  提供**復原方案名稱**，然後選取 [復原選項] 中的 [稍後復原]。

    a.如果您想要立即復原，可以選擇 [修復選項] 中的 [立即復原]。

    b.建議您選取 [稍後復原]，因為保護初始複寫可能尚未完成

    c.最後按一下 [復原] 按鈕來儲存方案，或根據您的 [修復選項] 來觸發復原。

2.  您可以查看 [復原狀態]，並查看該方案是否已成功儲存。

![](./media/site-recovery-failback-azure-to-vmware/image42.png)

1.  如果您選擇稍後復原，將會收到方案已建立的通知，而您可在稍後進行復原。

![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## 復原虛擬機器

建立方案之後，您可以選擇復原虛擬機器。由於是必要條件，因此，您需要確定虛擬機器已完成同步處理。

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

如果 [複寫狀態] 顯示 [正常]，則表示保護已完成且已符合 RPO 閾值。若要確認複寫組的健康情況，您可以移至虛擬機器屬性，並查看複寫的健康情況。

**起始復原之前，請關閉 Azure 虛擬機器。如此將可確保不會產生核心分裂，而您的客戶將會由某一個應用程式複本來提供服務。只有在您成功確定 Azure VM 已關閉時，才能繼續以下步驟。**

若要開始將虛擬機器復原回到內部部署，您需要啟動儲存的方案。

1.  在 vContinuum 上，選取 [監視] 方案。

2.  精靈將會列出到目前為止已執行的方案。

![](./media/site-recovery-failback-azure-to-vmware/image45.png)

1.  選取 [復原] 節點，然後選取要復原的方案。

    a.它將通知您此方案尚未開始。

2.  按一下 [開始] 以開始復原。

3.  您可以監視虛擬機器的復原


![](./media/site-recovery-failback-azure-to-vmware/image46.png)

4. 一旦開啟 VM 的電源之後，您就可以連接到 vCenter 上的虛擬機器。

## 在容錯回復後重新受到 Azure 保護

完成容錯回復之後，您可能要再一次保護虛擬機器。下列步驟將協助您重新設定保護

1.  檢查虛擬機器內部部署正在運作，而且應用程式能夠與您的客戶聯繫。

2.  在 Azure Site Recovery 入口網站中，選取虛擬機器，並將它們刪除。

    a.選取停用虛擬機器的保護。如此將可確保 VM 沒有其他保護。

3.  移至 Azure IAAS 虛擬機器，並刪除已容錯移轉的 VM。

4.  刪除 vSpehere 上舊的 VM - 這些是您先前容錯移轉到 Azure 的 VM。

5.  在 ASR 入口網站中，選取保護最近容錯移轉的虛擬機器。

6.  一旦 VM 受到保護之後，您就可以將它們加入復原方案，持續進行保護。


 

<!---HONumber=July15_HO2-->