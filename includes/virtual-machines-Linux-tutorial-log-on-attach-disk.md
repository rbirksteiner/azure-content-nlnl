

## <a id="logon"> </a>如何在建立虛擬機器之後登入 ##

您可以使用 SSH 用戶端來管理虛擬機器的設定及機器上執行的應用程式。若要這樣做，必須在您要用來存取虛擬機器的電腦上安裝 SSH 用戶端。有許多 SSH 用戶端程式可供選擇。下面是一些可能的選項：

- 如果您使用的是執行 Windows 作業系統的電腦，可能會想要使用 PuTTY 之類的 SSH 用戶端。如需詳細資訊，請參閱 [PuTTY 下載](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。
- 如果您使用的是執行 Linux 作業系統的電腦，可能會想要使用 OpenSSH 之類的 SSH 用戶端。如需詳細資訊，請參閱 [OpenSSH](http://www.openssh.org/)。

此教學課程會說明如何使用 PuTTY 程式來存取虛擬機器。

1. 在管理入口網站中尋找 [主機名稱] 和 [連接埠資訊]。您可以從虛擬機器的儀表板中找到您需要的資訊。請按一下虛擬機器名稱，然後在儀表板的 [Quick Glance] 區段中尋找 [SSH Details]。

	![尋找 SSH 詳細資料](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/SSHdetails.png)

2. 開啟 PuTTY 程式。

3. 輸入您從儀表板收集的 [主機名稱] 和 [連接埠資訊]，然後按一下 [開啟]。

	![輸入主機名稱和連接埠資訊](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/putty.png)

4. 使用您在建立虛擬機器時所新增的 NewUser1 帳戶，登入虛擬機器。

	![登入新的虛擬機器](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/sshlogin.png)

	您現在可以開始使用虛擬機器，就如同操作任何其他伺服器一樣。


## <a id="attachdisk"> </a>如何將資料磁碟連接至新的虛擬機器 ##

應用程式可能需要儲存資料。若要進行此設定，請將資料磁碟連接至先前建立的虛擬機器。最簡單的方法是將空的資料磁碟連接至機器。

在 Linux 上，資源磁碟通常由 Azure Linux 代理程式管理，並自動掛接到 **/mnt/resource** (或 Ubuntu 映像中的 **/mnt**)。換句話說，在 Linux 上，核心有可能將資料磁碟命名為 `/dev/sdc`，且使用者必須分割、格式化及掛接該資源。如需詳細資訊，請參閱＜[Azure Linux 代理程式使用者指南](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md)＞(英文)。

>[AZURE.NOTE]請勿在資源磁碟上儲存資料。此磁碟提供暫存儲存空間以供應用程式和程序使用，並可用來儲存您不需要保留的資料 (例如交換檔)。位於 Azure 儲存體中的資料磁碟如同分頁 Blob 中的 .vhd 檔案，可提供儲存體備援以保護您的資料安全。如需詳細資訊，請參閱[關於 Azure 中的磁碟和映像](http://msdn.microsoft.com/library/jj672979.aspx)。

1. 如果您尚未登入，請登入 Azure 管理入口網站。

2. 按一下 [虛擬機器]，然後選取先前建立的 [MyTestVM1] 虛擬機器。

3. 在命令列上，按一下 [連接]，然後按一下 [連接空的磁碟]。
	
	[連接空的磁碟] 對話方塊隨即出現。

	![定義磁碟詳細資料](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/attachnewdisklinux.png)

4. 系統已為您定義 [虛擬機器名稱]、[儲存位置] 和 [檔案名稱]。您只需要輸入想要的磁碟大小。在 [大小] 欄位中輸入 **5**。

	**注意：**所有磁碟都是從 Azure 儲存體中的 VHD 檔案建立。您可以為加入儲存體的 VHD 檔案命名，但磁碟的名稱是自動產生的。

5. 按一下核取記號將資料磁碟連接至虛擬機器。

6. 您可以查看儀表板，以確認資料磁碟已成功連接至虛擬機器。請按一下虛擬機器的名稱來顯示儀表板。

	虛擬機器的磁碟數目現在是 2，且您連接的磁碟會列在 [磁碟] 表格中。

	![成功連接磁碟](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/attachemptysuccess.png)


您剛連接至虛擬機器的資料磁碟為離線狀態，且在新增之後尚未初始化。您必須先登入機器並將磁碟初始化，才能使用該磁碟來儲存資料。

1. 使用「如何在建立虛擬機器之後登入」中列出的步驟，連線至虛擬機器。


2. 在 SSH 視窗中，輸入下列命令，然後輸入帳戶密碼：

	`sudo grep SCSI /var/log/messages`

	在出現的訊息中，您可以找到最後一個新增之資料磁碟的識別碼。

	![識別磁碟](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskmessages.png)


3. 在 SSH 視窗中，輸入下列命令來建立新的裝置，然後輸入帳戶密碼：

	`sudo fdisk /dev/sdc`

	>[AZURE.NOTE]在此範例中，如果 /sbin 或 /usr/sbin 不在您的 `$PATH` 中，您可能需要在部份散發套件上使用 `sudo -i`。


4. 輸入 **n** 建立新的磁碟分割。

	![建立新的裝置](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskpartition.png)


5. 輸入 **p** 將磁碟分割設為主要磁碟分割，輸入 **1** 設為第一個磁碟分割，然後按 Enter 鍵接受預設的磁柱值。

	![建立磁碟分割](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskcylinder.png)


6. 輸入 **p** 查看目前分割之磁碟的詳細資料。

	![列出磁碟資訊](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskinfo.png)


7. 輸入 **w** 寫入磁碟的設定。

	![寫入磁碟變更](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskwrite.png)


8. 您必須在新的磁碟分割上建立檔案系統。做為範例，請輸入下列命令來建立檔案系統，然後輸入帳戶密碼：

	`sudo mkfs -t ext4 /dev/sdc1`

	![建立檔案系統](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskfilesystem.png)

	>[AZURE.NOTE]請注意，SUSE Linux Enterprise 11 系統僅對 ext4 檔案系統提供唯讀存取。針對這些系統，建議您將新檔案系統格式化為 ext3，而非 ext4。


9. 建立可掛接新檔案系統的目錄。例如，請輸入下列命令，然後輸入帳戶密碼：

	`sudo mkdir /datadrive`


10. 輸入下列命令來掛接磁碟機：

	`sudo mount /dev/sdc1 /datadrive`

	資料磁碟現在可以當做 **/datadrive** 來使用。


11. 將新的磁碟機新增至 /etc/fstab：

	為了確保重新開機之後自動重新掛接磁碟機，必須將磁碟機新增至 /etc/fstab 檔案。此外，強烈建議在 /et/fstab 中使用全域唯一識別碼 (Universally Unique IDentifier, UUID) 來參考磁碟機，而不只是裝置名稱 (例如，/dev/sdc1)。若要尋找新磁碟機的 UUID，您可以使用 **blkid** 公用程式：
	
		`sudo -i blkid`

	輸出類似如下範例：

		`/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
		`/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
		`/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

	>[AZURE.NOTE]blkid 並不要求一定要使用 sudo 存取權，只是在某些散發套件上，如果 /sbin 或 /usr/sbin 不在 `$PATH` 中，則使用 `sudo -i` 來執行會比較簡單。

	**注意：**不當編輯 /etc/fstab 檔案會導致系統無法開機。如果不確定，請參閱散發套件的文件，以取得如何適當編輯此檔案的相關資訊。在編輯之前，也建議先備份 /etc/fstab 檔案。

	請使用文字編輯器，在 /etc/fstab 檔案的結尾輸入新檔案系統的相關資訊。在此範例中，我們使用先前步驟所建立之新的 **/dev/sdc1** 裝置的 UUID 值，並使用掛接點 **/datadrive**：

		`UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

	或者，在基於 SUSE Linux 的系統上，您可能需要使用稍微不同的格式：

		`/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`

	如果還有建立其他資料磁碟機或磁碟分割，同樣也需要分別在 /etc/fstab 中輸入。

	現在，您可以測試檔案系統是否適當掛接，方法是取消掛接檔案系統，再重新掛接，例如使用先前步驟中建立的範例掛接點 `/datadrive`：

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	如果第二個命令會產生錯誤，請檢查 /etc/fstab 檔案的正確語法。


	>[AZURE.NOTE]後續移除資料磁碟而不編輯 fstab，可能會造成 VM 無法開機。如果這是常見情況，那麼多數散發套件會提供 `nofail` 和/或 `nobootwait` fstab 選項，使得即使沒有磁碟，也能讓系統開機。請查閱散發套件的文件，以取得這些參數的相關資訊。

<!---HONumber=62-->