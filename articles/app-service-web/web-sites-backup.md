<properties 
	pageTitle="在 Azure App Service 中備份 Web 應用程式" 
	description="了解如何在 Azure App Service 中建立 Web 應用程式的備份" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# 在 Azure App Service 中備份 Web 應用程式


[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 中的備份和還原功能可讓您以手動或自動方式輕鬆建立 Web 應用程式備份。您可以將 Web 應用程式還原至先前的狀態，或根據原始應用程式的其中一個備份來建立新的 Web 應用程式。

如需從備份還原 Azure Web 應用程式的相關資訊，請參閱[還原 Web 應用程式](web-sites-restore.md)。

<a name="whatsbackedup"></a>
## 備份什麼項目 
Web Apps 可以備份下列資訊：

* Web 應用程式設定
* Web 應用程式檔案內容
* 任何連接到您應用程式的 SQL Server 或 MySQL 資料庫 (您可以選擇要將哪些資料庫納入備份)

這項資訊會備份至您指定的 Azure 儲存體帳戶和容器。

> [AZURE.NOTE]每個備份都是應用程式的完整離線複本，而不是增量更新。

<a name="requirements"></a>
## 需求和限制

* 使用備份和還原功能時，網站必須處於標準模式中。如需調整 Web 應用程式規模以使用標準模式的詳細資訊，請參閱[在 Azure App Service 中調整 Web 應用程式規模](web-sites-scale.md)。請注意，高階模式能夠執行的每日備份數量比標準模式更多。

* 使用備份和還原功能時，Azure 儲存體帳戶和容器必須與您即將備份的 Web 應用程式隸屬於相同的訂用帳戶。如果您還沒有儲存體帳戶，可以建立一個帳戶，做法是按一下 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)上 [**備份**] 刀鋒視窗中的 [**儲存體帳戶**]，然後從 [**目的地**] 刀鋒視窗中選擇 [**儲存體帳戶**] 和 [**容器**]。如需 Azure 儲存體帳戶的詳細資訊，請參閱本文結尾處的[連結](#moreaboutstorage)。

* 備份和還原功能支援最多 10GB 的網站和資料庫內容。如果備份功能由於裝載超過限制而無法繼續，作業記錄裡將會顯示錯誤。

<a name="manualbackup"></a>
## 建立手動備份

1. 在 Azure 入口網站中，從 Web Apps 刀鋒視窗中選擇您的 Web 應用程式。這將會在新的刀鋒視窗中顯示您的 Web 應用程式詳細資料。
2. 選取 [**設定**] 選項。[**設定**] 刀鋒視窗隨即顯示，讓您能夠修改 Web 應用程式。
	
	![Backups page][ChooseBackupsPage]

3. 在 [**設定**] 刀鋒視窗中選擇 [**備份**]。[**備份**] 刀鋒視窗隨即顯示。
	
4. 從 [**備份**] 刀鋒視窗中，選取 [**儲存體帳戶**] 和 [**容器**]，藉以選擇您的備份目的地。此儲存體帳戶必須與您即將備份的 Web 應用程式隸屬於相同的訂用帳戶。
	
	![Choose storage account][ChooseStorageAccount]
	
5. 在 [**備份**] 刀鋒視窗的 [**包含的資料庫**] 選項中，選取與您要備份的 Web 應用程式連線的資料庫 (SQL Server 或 MySQL)。

	> [AZURE.NOTE]資料庫若要出現在此清單中，其連接字串必須存在於入口網站之 [**Web 應用程式設定**] 刀鋒視窗的 [**連接字串**] 區段中。
	
6. 在 [**備份**] 刀鋒視窗中，選取 [**備份目的地**]。您必須選擇現有的儲存體帳戶和容器。
7. 在命令列上，按一下 [立即備份]。
	
	![BackUpNow button][BackUpNow]
	
	在備份過程中，您將會看見進度訊息。
	

您可以隨時進行手動備份。

<a name="automatedbackups"></a>
## 設定自動備份

1. 在 [**備份**] 刀鋒視窗上，將 [**已排程備份**] 設為 [開啟]。
	
	![Enable automated backups][SetAutomatedBackupOn]
	
2. 選取您要備份 Web 應用程式的目的地儲存體帳戶。此儲存體帳戶必須與您即將備份的 Web 應用程式隸屬於相同的訂用帳戶。
	
	![Choose storage account][ChooseStorageAccount]
	
3. 在 [頻率] 方塊中，指定您要進行自動備份的頻率。天數必須介於 1 與 90 之間 (包含此二數，即一天一次到每 90 天一次)。
	
4. 使用 [**開始**] 選項，來指定您想要自動備份開始執行的日期和時間。
	
	> [AZURE.NOTE]Azure 會以 UTC 格式儲存備份時間，但在顯示時，則會根據您用來顯示入口網站之電腦的系統時間。
	
5. 在 [**包含的資料庫**] 區段中，選取與您要備份的 Web 應用程式連接的資料庫 (SQL Server 或 MySQL)。資料庫若要出現在此清單中，其連接字串必須存在於入口網站之 [**Web 應用程式設定**] 刀鋒視窗的 [**連接字串**] 區段中。
	
	> [AZURE.NOTE]如果您選擇在備份中納入一或多個資料庫，並且指定了少於 7 天的頻率，則會出現警告，指出過於頻繁的備份可能會增加您的資料庫成本。
	
6. 此外，將 [**保留 (天數)**] 值設為您想要保留備份的天數。
7. 在命令列中按一下 [儲存] 按鈕，以儲存您的組態變更 (如果您決定不儲存，則選擇 [捨棄])。
	
	![儲存按鈕][SaveIcon]

<a name="notes"></a>
## 注意事項

* 請確定會在 Web 應用程式 [**設定**] 中的 [**Web 應用程式設定**] 刀鋒視窗上，適當地設定每個資料庫的連接字串，使備份和還原功能可納入您的資料庫。
* 雖然您可以將多個 Web 應用程式備份至相同的儲存體帳戶，但為了方便維護，建議您為每個 Web 應用程式建立個別的儲存體帳戶。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

<a name="partialbackups"></a>
## 只備份您網站的一部分

有時候您不想要備份您的網站上的所有內容，特別是如果您定期備份您的網站，或者如果您的網站有超過 10 GB 的內容 (亦即您可以一次備份的最大數量)。

例如，您可能不想要備份記錄檔。或者，如果您[設定每週備份](https://azure.microsoft.com/zh-tw/documentation/articles/web-sites-backup/#configure-automated-backups)，您不會想要儲存體帳戶充滿從不變更的靜態內容，像是舊的部落格文章或影像。

部分備份可讓您選擇只針對想要備份的檔案進行備份。

###指定您不想要備份的檔案
您可以建立一份要從備份中排除的檔案和資料夾清單。

將清單以文字檔儲存在您網站的 wwwroot 資料夾，並命名為 _backup.filter。透過 `http://{yoursite}.scm.azurewebsites.net/DebugConsole` 中的 [Kudu 主控台](https://github.com/projectkudu/kudu/wiki/Kudu-console)即可輕鬆存取此檔案。

下列指示將使用 Kudu 主控台建立 _backup.filter 檔案，但您可以使用您最愛的部署方法，將檔案放置在那裡。

###怎麼辦
我有一個網站，其中包含過去幾年的記錄檔和靜態影像，且以後不再變更。

我已完整備份網站，包括舊的影像。現在我想要每天備份網站，但我不想付費儲存從未變更的記錄檔或靜態影像檔案。

![Logs 資料夾][LogsFolder] ![Images 資料夾][ImagesFolder]
	
下列步驟顯示我如何從備份中排除那些檔案。

####識別您不想要備份的檔案和資料夾
這很容易。我已經知道我不想要備份任何記錄檔，所以我想要排除 `D:\home\site\wwwroot\Logs`。

在 `D:\home\LogFiles` 還有一個所有 Azure Web Apps 都具有的記錄檔資料夾。讓我們也排除該資料夾。

我也不想要一再重複備份過去幾年的影像。因此，也讓我們新增 `D:\home\site\wwwroot\Images\2013` 和 `D:\home\site\wwwroot\Images\2014` 至清單。

最後，讓我們不備份 Images 資料夾的 brand.png 檔案，以示範也可以將個別檔案列入黑名單。它位在 `D:\home\site\wwwroot\Images\brand.png`

以下是我們不想要備份的資料夾：

* D:\home\site\wwwroot\Logs
* D:\home\LogFiles
* D:\home\site\wwwroot\Images\2013
* D:\home\site\wwwroot\Images\2014
* D:\home\site\wwwroot\Images\brand.png

#### Create the exclusion list
您在稱為 _backup.filter 的特殊檔案中儲存不想要備份之檔案和資料夾的黑名單。建立檔案並放在 `D:\home\site\wwwroot_backup.filter`。

在 _backup.filter 檔案中列出所有您不想要備份的檔案和資料夾。將您想要從備份中排除的資料夾及檔案，以相對於 D:\home 的完整路徑新增至檔案 (一行一個路徑)。

以我的網站為例，`D:\home\site\wwwroot\Logs` 變成 `\site\wwwroot\Logs`、`D:\home\LogFiles` 變成 `\LogFiles`，依此類推，因此我的 _backup.filter 包含下列內容：

    \site\wwwroot\Logs
    \LogFiles
    \site\wwwroot\Images\2013
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\brand.png

請注意，每一行開頭的 ``。這很重要。

###執行備份
現在您可以使用平常的備份方式進行備份。[手動](https://azure.microsoft.com/zh-tw/documentation/articles/web-sites-backup/#create-a-manual-backup)或[自動](https://azure.microsoft.com/zh-tw/documentation/articles/web-sites-backup/#configure-automated-backups)都可以。

_backup.filter 中所列出之篩選條件下的任何檔案和資料夾將從備份中排除。這表示現在將不再備份記錄檔以及 2013 年和 2014年影像檔案。

###還原您已備份的網站
以您[還原定期備份](https://azure.microsoft.com/zh-tw/documentation/articles/web-sites-restore/)的相同方式還原您網站的部分備份。其將正確地執行。

####技術詳細資料
透過完整 (非部分) 備份，通常備份裡的任何項目會取代網站上的所有內容。如果檔案在網站上，但不在備份裡，即會遭到刪除。

但是，當還原部分備份時，位於其中一個黑名單資料夾 (像是我網站的 `D:\home\site\wwwroot\images\2014`) 的任何內容將保持原狀。因此，如果個別檔案已列入黑名單，則在還原期間也不受影響。

<a name="aboutbackups"></a>
## 備份的儲存方式

在進行一個或多個備份之後，備份將顯示於您的 [**儲存體帳戶**] 以及 Web 應用程式的 [**容器**] 刀鋒視窗中。從 [**儲存體帳**戶]，每個備份都會有一個包含備份資料的 .zip 檔案，以及一個包含 .zip 檔案內容資訊清單的 .xml 檔案。

.zip 和 .xml 備份檔案名稱，會以您的 Web 應用程式名稱、底線，以及指出備份製作時間的時間戳記依序組成。時間戳記會包含 YYYYMMDD 格式的日期 (無空格的數字) 和 UTC 格式的 24 小時制時間 (例如 fabrikam_201402152300.zip)。如果您要存取備份而不實際執行 Web 應用程式還原，您可以將這些檔案的內容解壓縮並加以瀏覽。

隨 zip 檔案儲存的 XML 檔案會在 *backupdescription* > *databases* > *databasebackupdescription* > *filename* 之下指出資料庫檔案名稱。

資料庫備份檔案本身則儲存在 .zip 檔案的根目錄中。若是 SQL 資料庫，這會是 BACPAC 檔案 (無副檔名)，而且可以匯入。若要根據 BACPAC 匯出建立新的 SQL 資料庫，您可以遵循＜[匯入 BACPAC 檔案以建立新的使用者資料庫](http://technet.microsoft.com/library/hh710052.aspx)＞一文中的步驟。

如需使用 Azure 入口網站還原 Web 應用程式 (包括資料庫) 的相關資訊，請參閱[在 Azure App Service 中還原 Web 應用程式](web-sites-restore.md)。

> [AZURE.NOTE]對 **websitebackups** 容器中的檔案進行任何變更，都可能導致備份失效，進而無法還原。

<a name="bestpractices"></a>
##最佳作法
當災難來襲，而您必須還原網站時該怎麼辨？ 確定您已事先準備妥當。

是，您可以具有部分備份，但至少先完整備份網站一次，以便備份您網站的所有內容 (這是為最壞的情況做打算)。接著當您還原備份時，可以先還原網站的完整備份，然後再還原最新的部分備份將其覆蓋。

原因如下：它可讓您使用[部署位置](https://azure.microsoft.com/zh-tw/documentation/articles/web-sites-staged-publishing/)來測試您已還原的網站。您甚至根本不需要接觸您的生產網站，即可測試還原程序。測試還原程序是一件[好事](http://axcient.com/blog/one-thing-can-derail-disaster-recovery-plan/)。您永遠都不會知道何時可能會惡運臨身，像是當我嘗試還原我的部落格，但最後卻是遺失去一半內容。

###一個恐怖的故事

我的部落格採用 [Ghost](https://ghost.org/) 部落格平台。像一個負責的開發人員，我已經為我的網站建立備份，一切都很好。然後有一天我收到訊息，指出有新的 Ghost 版本可用，而且我可以將我的部落格升級到該版本。太棒了 ！

我多建立了一個網站備份，以備份最新的部落格文章，並繼續升級 Ghost。

在我的生產網站上。

糟糕的錯誤。

升級出錯，我的首頁只顯示空白畫面。「沒有問題」我以為，「我只需還原剛建立的備份」。

我還原升級，看起來一切回復原狀... 但部落格文章除外。

出了什麼事？？？

其實，在 [Ghost 升級注意事項](http://support.ghost.org/how-to-upgrade/)中，有這則警告：

![您可以從 content/data 取得資料庫的複本，但是不應該在 Ghost 執行時這樣做。請先將它停止。][GhostUpgradeWarning]

如果您嘗試在 Ghost 執行時備份資料... 實際上並不會備份資料。

真倒楣。

如果我曾經先在測試位置上嘗試還原，我就會知道這個問題，而不會遺失我的所有文章。

人生就是如此。[任何人](http://blog.codinghorror.com/international-backup-awareness-day/)都可能碰到這樣的事。

測試您的備份。

<a name="nextsteps"></a>
## 後續步驟
如需從備份還原 Azure Web 應用程式的相關資訊，請參閱[在 Azure App Service 中還原 Web 應用程式](web-sites-restore.md)。

若要開始使用 Azure，請參閱 [Microsoft Azure 免費試用](/pricing/free-trial/)。


<a name="moreaboutstorage"></a>
### 儲存體帳戶的相關資訊

[什麼是儲存體帳戶？](../storage-whatis-account.md)

[如何建立儲存體帳戶](../storage-create-storage-account/)

[如何監視儲存體帳戶](../storage-monitor-storage-account.md)

[了解 Azure 儲存體計費](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
 

<!---HONumber=62-->