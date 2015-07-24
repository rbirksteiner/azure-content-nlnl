**注意**：本文所建立的虛擬機器並未連線到虛擬網路。如果您想要讓虛擬機器使用虛擬網路 (以便您日後能根據主機名稱來直接連線至虛擬機器) 或設定跨部署連線，請改用 [從藝廊] 方法，在建立虛擬機器時指定虛擬網路。如需虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](http://go.microsoft.com/fwlink/p/?LinkID=294063)。

1. 使用您的 Azure 帳戶，登入 Azure 管理入口網站。
2. 在管理入口網站中，依序按一下網頁左下角的 [+新增]、[虛擬機器] 和 [從藝廊]。

	![建立新的虛擬機器][Image1]

3. 從 [平台映像] 中選取 CentOS 虛擬機器映像，然後按一下頁面右下角的 [下一步] 箭頭。
	
4. 在 [虛擬機器組態] 頁面，提供下列資訊：
	- 提供 [虛擬機器名稱] (如 "testlinuxvm")。
	- 指定 [新使用者名稱] (如 "newuser")，此名稱將新增至 Sudoers 清單檔案。
	- 在 [新密碼] 方塊中，輸入[強式密碼](http://msdn.microsoft.com/library/ms161962.aspx)。
	- 在 [確認密碼] 方塊中，重新輸入密碼。
	- 從 [大小] 下拉式清單中選取適當的大小。

	按 [下一步] 箭頭以繼續。
	
5. 在 [虛擬機器模式] 頁面，提供下列資訊：
	- 選取 [獨立虛擬機器]。
	- 在 [DNS 名稱] 方塊中，輸入有效的 DNS 位址。例如，"testlinuxvm"
	- 在 [儲存體帳戶] 方塊中，選取 [使用自動產生的儲存體帳戶]。
	- 在 [區域/同質群組/虛擬網路] 方塊中，選取這個虛擬映像將託管於的區域。

	按 [下一步] 箭頭以繼續。

6. 在 [虛擬機器選項] 頁面的 [可用性設定組] 方塊中，選取 [(無)]。

	按一下打勾記號繼續。
	
7. 等待 Azure 準備好您的虛擬機器。

##設定端點
建立虛擬機器之後，請設定端點以便進行遠端連線。

1. 在管理入口網站中，按一下 [虛擬機器]，按一下新虛擬機器的名稱，然後按一下 [端點]。

2. 按一下頁面底部的 [編輯端點]，並編輯 SSH 端點，使其 [公用連接埠] 為 22。

##連線至虛擬機器
在佈建好虛擬機器並設定好端點之後，您可以使用 SSH 或 PuTTY 連線到該虛擬機器。

###使用 SSH 進行連線
如果您是使用 Linux，請使用 SSH 連線至虛擬機器。在命令提示字元下，執行：

	$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

輸入使用者的密碼。

###使用 PuTTY 進行連線
如果您是使用 Windows 電腦，請使用 PuTTY 連線至 VM。PuTTY 可自 [PuTTY 下載頁面][PuTTYDownLoad]下載取得。

1. 下載 **putty.exe** 並將其儲存至您電腦上的目錄中。開啟命令提示字元，瀏覽至該資料夾，然後執行 **putty.exe**。

2. 在 [**Host Name**] 中輸入 "testlinuxvm.cloudapp.net"，並在 [**Port**] 中輸入 "22"。![PuTTY 畫面][Image6]

##更新虛擬機器 (選用)
連線至虛擬機器之後，您可以選擇性地安裝更新。請執行：

	$ sudo yum update

再輸入一次密碼。等待更新安裝完畢。


[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image1]: ./media/create-and-configure-centos-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-centos-vm-in-portal/putty.png

<!---HONumber=62-->