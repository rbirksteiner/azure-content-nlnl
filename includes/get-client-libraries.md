### 透過編輯器安裝

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

4. 開啟命令提示字元，在專案根目錄中執行下列命令

		php composer.phar install

### 手動安裝

若要手動下載和安裝 PHP Client Libraries for Azure，請依照下列步驟進行：

1. 從 [GitHub][php-sdk-github] 下載含有程式庫的 .zip 封存檔。或者，分岔儲存機制並複製到本機電腦後面這個選項需要有 GitHub 帳戶並在本機安裝 Git。

	
	> [AZURE.NOTE]PHP Client Libraries for Azure 相依於 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2)、[Mail_mime](http://pear.php.net/package/Mail_mime) 和 [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode) PEAR 封裝。建議使用 [PEAR 封裝管理員](http://pear.php.net/manual/en/installation.php)來安裝這些封裝，以解決這些相依性。


2. 將已下載之封存檔的 `WindowsAzure` 目錄複製到應用程式目錄結構中。

如需關於安裝 PHP Client Libraries for Azure 的詳細資訊 (包括關於以 PEAR 封裝來安裝的詳細資訊)，請參閱＜[下載 Azure SDK for PHP][download-SDK-PHP]＞(英文)。


[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar

<!---HONumber=July15_HO2-->