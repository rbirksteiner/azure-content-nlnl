1. 在 Azure [管理入口網站](http://manage.windowsazure.com)中，按一下 [**虛擬機器**]，然後選取剛剛建立的虛擬機器 (**testlinuxvm**)。

2. 在命令列上，按一下 [連接]，然後按一下 [連接空的磁碟]。

	[連接空的磁碟] 對話方塊隨即出現。


3. 系統已為您定義 [虛擬機器名稱]、[儲存位置] 和 [檔案名稱]。您只需要輸入想要的磁碟大小。在 [大小] 欄位中輸入 **5**。

	![連接空的磁碟][Image2]

	**注意：**所有磁碟都是從 Azure 儲存體中的 VHD 檔案建立。您可以為新增至儲存體的 VHD 檔案提供名稱，但是 Azure 會自動產生磁碟的名稱。

4. 按一下核取記號將資料磁碟連接至虛擬機器。

5. 按一下虛擬機器名稱來顯示儀表板；您便可確認資料磁碟已順利連接至虛擬機器。您連接的磁碟會列在 [磁碟] 表格中。

	當您連接資料磁碟時，其將在您登入並完成設定後才可使用。

##使用 SSH 或 PuTTY 連接至虛擬機器並完成安裝
您必須登入虛擬機器並完成磁碟的設定，才能使用磁碟來儲存資料。

1. 佈建虛擬機器之後，請使用 SSH 或 PuTTY 連接，並以 **newuser** 身分登入 (如前面步驟所述)。	


2. 在 SSH 或 PuTTY 視窗中，輸入下列命令，然後輸入帳戶密碼：

	`$ sudo grep SCSI /var/log/messages`

	您可以在所顯示的訊息中，找到最後一個新增之資料磁碟的識別碼 (在此範例中為 **sdc**)。

	![GREP][Image4]


3. 在 SSH 或 PuTTY 視窗中，輸入下列命令，以將磁碟 **/dev/sdc** 進行分割：

	`$ sudo fdisk /dev/sdc`


4. 輸入 **n** 建立新的磁碟分割。

	![FDISK][Image5]


5. 輸入 **p** 將磁碟分割設為主要磁碟分割、輸入 **1** 將它設為第一個磁碟分割，然後按 Enter 鍵接受磁柱的預設值 (1)。

	![FDISK][Image6]


6. 輸入 **p** 查看目前分割之磁碟的詳細資料。

	![FDISK][Image7]


7. 輸入 **w** 寫入磁碟的設定。

	![FDISK][Image8]


8. 使用 **mkfs** 命令，將新磁碟格式化：

	`$ sudo mkfs -t ext4 /dev/sdc1`

9. 接下來您必須有可供掛接新檔案系統的目錄。做為範例，請輸入下列命令建立新目錄來掛接磁碟機，然後輸入帳戶密碼：

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

	如果還有建立其他資料磁碟機或磁碟分割，同樣也需要分別在 /etc/fstab 中輸入。

	現在，您可以測試檔案系統是否適當掛接，方法是取消掛接檔案系統，再重新掛接，例如使用先前步驟中建立的範例掛接點 `/datadrive`：

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	如果第二個命令會產生錯誤，請檢查 /etc/fstab 檔案的正確語法。


	>[AZURE.NOTE]後續移除資料磁碟而不編輯 fstab，可能會造成 VM 無法開機。如果這是常見情況，那麼多數散發套件會提供 `nofail` 和/或 `nobootwait` fstab 選項，使得即使沒有磁碟，也能讓系統開機。請查閱散發套件的文件，以取得這些參數的相關資訊。


[Image2]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM2.png
[Image4]: ./media/attach-data-disk-centos-vm-in-portal/GrepScsiMessages.png
[Image5]: ./media/attach-data-disk-centos-vm-in-portal/fdisk1.png
[Image6]: ./media/attach-data-disk-centos-vm-in-portal/fdisk2.png
[Image7]: ./media/attach-data-disk-centos-vm-in-portal/fdisk3.png
[Image8]: ./media/attach-data-disk-centos-vm-in-portal/fdisk4.png
[Image9]: ./media/attach-data-disk-centos-vm-in-portal/mkfs.png

<!---HONumber=62-->