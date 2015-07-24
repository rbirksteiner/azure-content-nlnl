
**重要事項**：如果您想要讓虛擬機器使用虛擬網路，請務必在建立虛擬機器時指定虛擬網路。只有在建立虛擬機器時，才能將虛擬機器設定為加入虛擬網路。如需虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](http://go.microsoft.com/fwlink/p/?LinkID=294063)。


1. 使用 Azure 帳戶，登入 [Azure 管理入口網站][AzurePreviewPortal]。

2. 在管理入口網站中，依序按一下網頁左下角的 [+新增]、[虛擬機器] 和 [從藝廊]。

	![建立新的虛擬機器][Image1]

3. 在 **SUSE** 群組之下，選取 OpenSUSE 虛擬機器映像，然後按一下頁面底部的向前箭號。


4. 在第一個 [**虛擬機器組態**] 頁面上，填入或驗證設定：

	- 輸入 [**虛擬機器名稱**] (如 "testlinuxvm")。
	- 驗證 [**階層**] 並挑選 [**大小**]。階層可決定您可以選擇的大小。
	- 輸入 [**新使用者名稱**] (如 "newuser")，此名稱將新增至 Sudoers 清單檔案。
	- 決定要使用的 [**驗證**] 類型。如需一般密碼指導方針，請參閱[字串密碼](http://msdn.microsoft.com/library/ms161962.aspx)。


5. 在下一個 [**虛擬機器組態**] 頁面上，填入或驗證設定：
	- 使用預設 [**建立新的雲端服務**]。
	- 在 [**DNS 名稱**] 方塊中，輸入有效的 DNS 名稱以作為位址的一部分 (如 "testlinuxvm")。
	- 在 [區域/同質群組/虛擬網路] 方塊中，選取這個虛擬映像將託管於的區域。

6.	按下一個箭頭完成，然後等待 Azure 準備您的虛擬機器而後予以啟動。

##連線至虛擬機器
視您的電腦執行的作業系統而定，您將使用 SSH 或 PuTTY 連線至虛擬機器：

- 如果您使用 Linux 連線至 VM，則使用 SSH。在命令提示字元下，執行： 

	`$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
	
	輸入使用者的密碼。

- 如果您使用 Windows 連線至 VM，則使用 PuTTY。您可以從 [PuTTY 下載頁面][PuTTYDownLoad]下載 PuTTY。

	下載 **putty.exe** 並將其儲存至您電腦上的目錄中。開啟命令提示字元，瀏覽至該資料夾，然後執行 **putty.exe**。

	輸入主機名稱 (例如 "testlinuxvm.cloudapp.net")，然後針對 [**連接埠**] 輸入 "22"。

	![PuTTY 畫面][Image6]

##更新虛擬機器 (選用)
1. 連線至虛擬機器之後，您可以選擇性地安裝系統更新和修補程式。若要執行更新，請輸入：

	`$ sudo zypper update`

2. 選取 [**軟體**]，然後選取 [**線上更新**] 以列出可用的更新。選取 [**接受**] 即可開始安裝並套用所有可用的新修補程式 (選用修補程式除外)。

3. 安裝完成之後，選取 [**完成**]。您的系統現在已是最新版本。

[PuTTYDownload]: http://www.puttyssh.org/download.html
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png

<!---HONumber=July15_HO1-->