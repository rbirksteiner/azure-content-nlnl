1. 如果您尚未登入 [Azure 管理入口網站](http://manage.windowsazure.com)，請先登入。

2. 按一下 [虛擬機器]，然後選取適當的虛擬機器。

3. 按一下命令列上的 [連接]。

	![登入虛擬機器](./media/virtual-machines-log-on-win-server/connectwindows.png)

4. 按一下 [開啟]，以使用系統自動為虛擬機器建立的遠端桌面通訊協定檔案。
	
5. 按一下 [連接] 以繼續。

	![繼續連接](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. 在虛擬機器上輸入系統管理帳戶的認證，然後按一下 [確定]。

 >[AZURE.TIP]在大部分情況下，您將使用建立虛擬機器時所指定的使用者名稱和密碼。請檢查使用者名稱以確定它有正確的網域資訊：

>- 如果 VM 屬於貴公司的網域，請確定使用者名稱包含該網域的名稱。
- 如果 VM 不屬於網域，請在該行開頭加上 '' 以移除任何網域資訊，或使用 VM 名稱做為網域名稱。例如，`\MyUserName` 或 `MyTestVM\MyUserName`。 
- 如果 VM 是網域控制站，請輸入該網域的網域系統管理員帳戶的使用者名稱和密碼。

按一下 [是] 以驗證虛擬機器的身分識別。

![驗證機器的身分識別](./media/virtual-machines-log-on-win-server/connectverify.png)

您現在可以從遠端使用虛擬機器。

<!---HONumber=62-->