
如需有關磁碟的詳細資訊，請參閱[關於 Azure 中的虛擬機器磁碟](http://go.microsoft.com/fwlink/p/?LinkId=403697)。

##<a id="attachempty"></a>做法：連接空的磁碟
新增資料磁碟的一個較為簡易方式是連接空的磁碟，因為 Azure 會為您建立 .vhd 檔案並將它儲存在儲存體帳戶中。

1. 按一下 [虛擬機器]，然後選取適當的虛擬機器。

2. 在命令列上，按一下 [連接]，然後按一下 [連接空的磁碟]。


	![連接空的磁碟](./media/howto-attach-disk-window-linux/AttachEmptyDisk.png)

3.	[**連接空的磁碟**] 對話方塊隨即出現。


	![連接新的空磁碟](./media/howto-attach-disk-window-linux/AttachEmptyDetail.png)

 
	執行下列動作：

	- 在 [**檔案名稱**] 中，接受預設名稱，或為用於該磁碟的 .vhd 檔案輸入另一個名稱。資料磁碟會使用自動產生的名稱，即使您為 .vhd 檔案輸入另一個名稱亦然。

	- 輸入資料磁碟的 [**大小 (GB)**]。

	- 按一下核取記號完成工作。

4.	建立並連接資料磁碟之後，它會列在虛擬機器的儀表板。

	![成功連接空的資料磁碟](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

##<a id="attachexisting"></a>做法：連接現有磁碟

連接現有磁碟要求您在儲存體帳戶中需要有可用的 .vhd。請使用 [Add-AzureVhd](http://go.microsoft.com/FWLink/p/?LinkID=391684) Cmdlet，將 .vhd 檔案上傳至儲存體帳戶。在建立並上傳 .vhd 檔案之後，就可將它連接至虛擬機器。

1. 按一下 [虛擬機器]，然後選取適當的虛擬機器。

2. 在命令列上，按一下 [連接]，然後選取 [連接磁碟]。


	![連接資料磁碟](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

	[連接磁碟] 對話方塊隨即出現。



	![輸入資料磁碟詳細資料](./media/howto-attach-disk-window-linux/AttachExistingDetail.png)

3. 選取要連接至虛擬機器的資料磁碟。

4. 按一下核取記號將資料磁碟連接至虛擬機器。
 
5.	連接資料磁碟之後，它會列在虛擬機器的儀表板。


	![成功連接資料磁碟](./media/howto-attach-disk-window-linux/AttachExistingSuccess.png)

> [AZURE.NOTE]新增資料磁碟之後，您必須登入虛擬機器並初始化磁碟，這樣虛擬機器才能使用磁碟來儲存資料。

<!---HONumber=July15_HO2-->