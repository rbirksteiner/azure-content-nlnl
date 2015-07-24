#如何使用適用於 Mac 和 Linux 的 Azure 命令列工具

本指南說明如何使用適用於 Mac 和 Linux 的 Azure 命令列工具，在 Azure 中建立和管理服務。涵蓋的案例包括**安裝工具**、**匯入發佈設定**、**建立和管理 Azure 網站**和**建立和管理 Azure 虛擬機器**。如需完整的參考文件，請參閱＜[適用於 Mac 和 Linux 的 Azure 命令列工具文件][reference-docs]＞。

##目錄
* [什麼是適用於 Mac 和 Linux 的 Azure 命令列工具](#Overview)
* [如何安裝適用於 Mac 和 Linux 的 Azure 命令列工具](#Download)
* [如何建立 Azure 帳戶](#CreateAccount)
* [如何下載和匯入發佈設定](#Account)
* [如何建立和管理 Azure 網站](#WebSites)
* [如何建立和管理 Azure 虛擬機器](#VMs)


<h2><a id="Overview"></a>什麼是適用於 Mac 和 Linux 的 Azure 命令列工具</h2>

適用於 Mac 和 Linux 的 Azure 命令列工具是一組用來部署和管理 Azure 服務的命令列工具。
 
支援的工作包括：

* 匯入發佈設定。
* 建立和管理 Azure 網站。
* 建立和管理 Azure 虛擬機器。

如需完整的支援命令清單，請於安裝工具之後在命令列輸入 `azure -help`，或參閱[參考文件][reference-docs]。

<h2><a id="Download">如何安裝適用於 Mac 和 Linux 的 Azure 命令列工具</a></h2>

下列清單包含安裝命令列工具的相關資訊，視作業系統而定：

* **Mac**：下載 [Azure SDK 安裝程式][mac-installer]。開啟已下載的 .pkg 檔案，並依照提示完成安裝步驟。

* **Linux**：安裝最新版本的 [Node.js][nodejs-org] (請參閱[透過封裝管理員來安裝 Node.js][install-node-linux])，然後執行下列命令：

		npm install azure-cli -g

	**注意**：您可能需要以提高的權限來執行此命令：

		sudo npm install azure-cli -g

* **Windows**：執行 Winows 安裝程式 (.msi 檔案)，該檔案可於以下位置取得：[Azure 命令列工具][windows-installer]。


若要測試安裝，請在命令提示字元中輸入 `azure`。如果安裝成功，您會看到所有可用的 `azure` 命令的清單。

<h2><a id="CreateAccount"></a>如何建立 Azure 帳戶</h2>

您需要 Azure 帳戶，才能使用適用於 Mac 和 Linux 的 Azure 命令列工具。

請開啟網頁瀏覽器，瀏覽到 [http://www.windowsazure.com][windowsazuredotcom]，然後按一下右上角的 [免費試用]。

![Azure 網站][Azure Web Site]

依照指示來建立帳戶。

<h2><a id="Account"></a>如何下載和匯入發佈設定</h2>

若要開始進行，您需要先下載並匯入發佈設定。這可讓您使用工具來建立和管理 Azure 服務。若要下載發佈設定，請使用 `account download` 命令：

	azure account download

這樣會開啟預設瀏覽器，並提示您登入管理入口網站。登入之後，將會下載 `.publishsettings` 檔案。請記下此檔案的儲存位置。

接下來，執行下列命令，並將 `{path to .publishsettings file}` 改為 `.publishsettings` 檔案的路徑，以匯入 `.publishsettings` 檔案：

	azure account import {path to .publishsettings file}

您可以使用 <code>account clear</code> 命令，以移除 <code>import</code> 命令儲存的所有資訊：

	azure account clear

若要查看 `account` 命令的選項清單，請使用 `-help` 選項：

	azure account -help

匯入發佈設定之後，基於安全的考量，請刪除 `.publishsettings` 檔案。

> [AZURE.NOTE]匯入發佈設定時，用來存取 Azure 訂用帳戶的認證會儲存在 `user` 資料夾內。`user` 資料夾已受到作業系統的保護。不過，建議您採取額外的步驟來加密 `user` 資料夾。做法如下：
> 
> - 在 Windows 中，修改資料夾屬性或使用 BitLocker。
> - 在 Mac 中，開啟資料夾的 FileVault。
> - 在 Ubuntu 中，使用「加密主目錄」功能。其他 Linux 散發套件提供同等的功能。

現在，您可以開始建立和管理 Azure 網站及 Azure 虛擬機器。

<h2><a id="WebSites"></a>如何建立和管理 Azure 網站</h2>

###建立網站

若要建立 Azure 網站，請先建立名為 `MySite` 的空目錄，並進入該目錄中。

然後，執行下列命令：

	azure site create MySite --git

此命令的輸出包含新建立之網站的預設 URL。`--git` 選項可讓您在本機應用程式目錄和網站的資料中心建立 git 儲存機制，以利用 git 來發佈至網站。請注意，如果本機資料夾已經是 git 儲存機制，此命令會將新的遠端新增至現有的儲存機制，並指向網站資料中心裡的儲存機制。

請注意，您可以搭配下列任何選項來執行 `azure site create` 命令：

* `--location [location name]`。此選項可讓您指定要建立網站的資料中心位置 (例如，[美國西部])。如果省略此選項，此命令會提示您選擇位置。
* `--hostname [custom host name]`。此選項可讓您指定網站的自訂主機名稱。

接著，您可以將內容加入網站目錄中。請使用一般 git 流程 (`git add`、`git commit`) 來認可內容。請使用下列 git 命令將網站內容發送至 Azure：

	git push azure master

###設定從 GitHub 發佈

若要設定從 GitHub 儲存機制持續發佈，請在建立網站時使用 `--GitHub` 選項：

	auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

如果您有 GitHub 儲存機制的本機副本，或儲存機制中具有 GitHub 儲存機制的單一遠端參考，則此命令會自動將 GitHub 儲存機制中的程式碼發佈至網站。此後，任何發送至 GitHub 儲存機制的變更會自動發佈至網站。

設定從 GitHub 發佈時，使用的預設分支為主要分支。若要指定不同的分支，請從本機儲存機制執行下列命令：

	azure site repository <branch name>

###進行應用程式設定

應用程式設定是應用程式在執行階段可用的機碼值組。針對 Azure 網站來設定時，應用程式設定值會覆寫網站的 Web.config 檔案中以相同機碼定義的設定。若為 Node.js 和 PHP 應用程式，應用程式設定是以環境變數的形式存在。下列範例顯示如何設定機碼值組：

	azure site config add <key>=<value> 

若要查看所有機碼值組的清單，請使用下列命令：

	azure site config list 

或者，如果知道機碼，您可以使用下列命令來查看此機碼的值：

	azure site config get <key> 

如果要變更現有機碼的值，必須先清除現有機碼，再重新加入機碼。清除命令為：

	azure site config clear <key> 

###列出和顯示網站

若要列出網站，請使用下列命令：

	azure site list

若要取得網站的詳細資訊，請使用 `site show` 命令。下列範例會詳細說明 `MySite`：

	azure site show MySite

###停止、啟動或重新啟動網站

您可以使用 `site stop`、`site start` 或 `site restart` 命令來停止、啟動或重新啟動網站：

	azure site stop MySite
	azure site start MySite
	azure site restart MySite

###刪除網站

最後，您可以使用 `site delete` 命令刪除網站：

	azure site delete MySite

請注意，如果您從原本執行 `site create` 的資料夾內執行上述任何命令，則不需要指定網站名稱 `MySite` 當做最後一個參數。

若要查看完整的 `site` 命令清單，請使用 `-help` 選項：

	azure site -help 

<h2><a id="VMs"></a>如何建立和管理 Azure 虛擬機器</h2>

Azure 虛擬機器是使用您提供的映像或映像庫中的虛擬機器映像 (.vhd 檔案) 來建立。若要查看可用的映像，請使用 `vm image list` 命令：

	azure vm image list

您可以使用 `vm create` 命令從可用的映象檔之一佈建並啟動虛擬機器。下列範例顯示如何從映像庫中的映像 (CentOS 6.2) 建立 Linux 虛擬機器 (名為 `myVM`)。虛擬機器的根使用者名稱和密碼分別是 `myusername` 和 `Mypassw0rd`。(請注意，`--location` 參數指定要建立虛擬機器的資料中心。如果省略 `--location` 參數，此命令會提示您選擇位置)。

	azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-zh-tw-30GB.vhd myusername --location "West US"

您可以選擇將 `--ssh` 旗標 (Linux) 或 `--rdp` 旗標 (Windows) 傳給 `vm create`，以允許從遠端連線至新建立的虛擬機器。

如果想要從自訂映像佈建虛擬機器，您可以使用 `vm image create` 命令從 .vhd 檔案建立映像，然後使用 `vm create` 命令來建立虛擬機器。下列範例顯示如何從本機 .vhd 檔案建立 Linux 映像 (名為 `myImage`)(`--location` 參數指定儲存映像的資料)。

	azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

除了從本機 .vhd 建立映像，您也可以從 Azure 二進位大型物件儲存體中儲存的 .vhd 建立映像。您若要這麼做，可透過 `blob-url` 參數：

	azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

建立映像之後，若要要從映像佈建虛擬機器，可以使用 `vm create`。下列命令會從以上建立的映像 (`myImage`) 建立名為 `myVM` 的虛擬機器。

	azure vm create myVM myImage myusername --location "West US"

佈建虛擬機器之後，建議您建立端點，以允許從遠端存取虛擬機器 (舉例來說)。下列範例使用 `vm create endpoint` 命令，開啟 `myVM` 上的外部連接埠 22 和本機連接埠 22：

	azure vm endpoint create myVM 22 22

若要取得虛擬機器的詳細資訊 (包括 IP 位址、DNS 名稱和端點資訊)，您可以使用 `vm show` 命令：

	azure vm show myVM

若要關閉、啟動或重新啟動虛擬機器，請使用下列其中一個命令：

	azure vm shutdown myVM
	azure vm start myVM
	azure vm restart myVM

最後，若要刪除 VM，請使用 `vm delete` 命令：

	azure vm delete myVM

如需用來建立和管理虛擬機器的完整命令清單，請使用 `-h` 選項：

	azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com

<!---HONumber=62-->