<properties
	pageTitle="下載 Azure SDK for PHP"
	description="了解如何下載和安裝 Azure SDK for PHP。"
	documentationCenter="php"
	services=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="03/20/2015"
	ms.author="tomfitz"/>

#下載 Azure SDK for PHP

## 概觀

Azure SDK for PHP 內含的元件可讓您開發、部署及管理適用於 Azure 的 PHP 應用程式。尤其是 Azure SDK for PHP 包含下列各項：

* **適用於 Azure 的 PHP 用戶端程式庫**。這些類別庫所提供的介面可供存取 Azure 功能，例如資料管理服務和雲端服務。  
* **適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (Azure CLI)**這是一組命令，可用於部署和管理 Azure 服務，例如 Azure 網站和 Azure 虛擬機器。Azure CLI 可在任何平台上運作，包括 Mac、Linux 和 Windows。
* **Azure PowerShell (僅限 Windows)**。這是一組 PowerShell Cmdlet，可用於部署和管理 Azure 服務，例如雲端服務和虛擬機器。
* **Azure 模擬器 (僅限 Windows)**。計算和儲存體模擬器都是雲端服務和資料管理服務的本機模擬器，可讓您在本機測試應用程式。Azure 模擬器只能在 Windows 上執行。

下列各節說明如何下載和安裝上述元件。

本主題中的指示假設您已安裝 [PHP][install-php]。

> [AZURE.NOTE]您必須有 PHP 5.3 或更新版本，才能使用適用於 Azure 的 PHP 用戶端程式庫。

##適用於 Azure 的 PHP 用戶端程式庫

適用於 Azure 的 PHP 用戶端程式庫提供了一個介面，以便從任何作業系統存取 Azure 功能，例如資料管理服務和雲端服務。您可以透過編輯器或 PEAR 封裝管理員或以手動方式安裝這些程式庫。

如需有關如何使用適用於 Azure 的 PHP 用戶端程式庫的詳細資訊，請參閱[如何從 PHP 使用 Blob 服務][blob-service] (英文)、[如何從 PHP 使用資料表服務][table-service] (英文) 及 [如何從 PHP 使用佇列服務][queue-service] (英文)。

###透過編輯器安裝

1. [安裝 Git][install-git]。


	> [AZURE.NOTE]在 Windows 中，您也需要將 Git 可執行檔新增至 PATH 環境變數。

2. 在專案的根目錄中建立名為 **composer.json** 的檔案，並新增下列程式碼：

        {
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "require": {
                "pear-pear.php.net/mail_mime" : "*",
                "pear-pear.php.net/http_request2" : "*",
                "pear-pear.php.net/mail_mimedecode" : "*",
                "microsoft/windowsazure": "*"
            }
        }

3. 將 **[composer.phar][composer-phar]** 下載到專案根目錄中。

4. 開啟命令提示字元，在專案根目錄中執行此命令

		php composer.phar install

###當作 PEAR 封裝安裝

若要將適用於 Azure 的 PHP 用戶端程式庫當作 PEAR 封裝安裝，請遵循下列步驟：

1. [安裝 PEAR][install-pear]。
2. 設定 Azure PEAR 通道：

		pear channel-discover pear.windowsazure.com
3. 安裝 PEAR 封裝：

		pear install pear.windowsazure.com/WindowsAzure-0.4.0

安裝完成後，您即可從您的應用程式參考類別庫。

###手動安裝

若要手動下載和安裝 PHP Client Libraries for Azure，請依照下列步驟進行：

1. 從 [GitHub][php-sdk-github] 下載含有程式庫的 .zip 封存檔。或者，分岔儲存機制並複製到本機電腦(後面這個選項需要有 GitHub 帳戶並在本機安裝 Git)。

	> [AZURE.NOTE]PHP Client Libraries for Azure 相依於 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2)、[Mail_mime](http://pear.php.net/package/Mail_mime) 和 [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode) PEAR 封裝。建議使用 [PEAR 封裝管理員](http://pear.php.net/manual/en/installation.php)來安裝這些封裝，以解決這些相依性。

2. 將下載之封存檔的 `WindowsAzure` 目錄複製到應用程式目錄結構中，並從您的應用程式參考類別。

##Azure PowerShell 和 Azure 模擬器

Azure PowerShell 是一組 PowerShell Cmdlet，可用於部署和管理 Azure 服務 (例如雲端服務和虛擬機器)。Azure 模擬器是雲端服務和資料管理服務的模擬器，可讓您在本機測試應用程式。只有 Windows 支援這些元件。

安裝 Azure PowerShell 和 Azure 模擬器的建議方式就是使用 [Microsoft Web Platform Installer][download-wpi]。請注意，您也可以選擇安裝其他開發元件，例如 PHP、SQL Server、適用於 SQL Server for PHP 的 Microsoft 驅動程式和 WebMatrix。

如需有關如何使用 Azure PowerShell 的詳細資訊，請參閱[如何安裝及設定 Azure PowerShell][powershell-tools] (英文)。

##Azure CLI

Azure CLI 是一組命令，可用於部署和管理 Azure 服務，例如 Azure 網站和 Azure 虛擬機器。下列清單說明如何根據您的作業系統來安裝 Azure CLI：

* **Mac**：在此下載 Azure SDK 安裝程式：[http://go.microsoft.com/fwlink/?LinkId=252249][mac-installer]。開啟已下載的 .pkg 檔案，並依照提示完成安裝步驟。

* **Linux**：安裝最新版本的 [Node.js][nodejs-org] (請參閱[透過封裝管理員來安裝 Node.js][install-node-linux])，然後執行下列命令：

		npm install azure-cli -g

	> [AZURE.NOTE]您可能需要以提高的權限來執行此命令：`sudo npm install azure-cli -g`


如需如何使用 Azure CLI 的相關資訊，請參閱[如何使用適用於 Mac、Linux 和 Windows 的 Azure 命令列][azure cli]。

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[pear-net]: http://pear.php.net/
[http-request2-package]: http://pear.php.net/package/HTTP_Request2
[mail-mimedecode-package]: http://pear.php.net/package/Mail_mimeDecode
[mail-mime-package]: http://pear.php.net/package/Mail_mime
[install-pear]: http://pear.php.net/manual/en/installation.getting.php
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git

<!---HONumber=July15_HO1-->