<properties 
	pageTitle="使用 Azure 儲存體模擬器進行開發和測試 | Microsoft Azure" 
	description="Azure 儲存體模擬器提供免費的本機開發環境，針對 Azure 儲存體進行開發和測試。了解儲存體模擬器，包括如何驗證要求、如何從應用程式連接到模擬器，以及如何使用命令列工具。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>
<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="tamram"/>

# 使用 Azure 儲存體模擬器進行開發和測試

## 概觀

Microsoft Azure 儲存體模擬器提供了模擬 Azure Blob、佇列和資料表服務的本機環境，以供進行開發。您可以使用儲存體模擬器，針對儲存體服務在本機測試您的應用程式，而不需建立 Azure 訂用帳戶，也不會產生任何費用。如果您滿意應用程式在模擬器中的運作方式，就可以切換成使用雲端的 Azure 儲存體帳戶。

> [AZURE.NOTE]儲存體模擬器隨 [Microsoft Azure SDK](https://azure.microsoft.com/downloads/) 提供。您也可以將儲存體模擬器安裝為獨立的封裝。若要設定儲存體模擬器，您必須在電腦上具有系統管理權限。
>  
> 請注意，在某個儲存體模擬器版本中建立的資料不保證可在使用不同版本時加以存取。如果您需要長期保存資料，建議您將資料儲存於 Azure 儲存體帳戶中 (而不是儲存體模擬器中)。

## 儲存體模擬器的運作方式
 
儲存體模擬器會使用本機 Microsoft SQL Server 執行個體以及本機檔案系統來模擬 Azure 儲存體服務。儲存體模擬器預設會使用 Microsoft SQL Server 2012 Express LocalDB 中的資料庫。您可以選擇設定儲存體模擬器存取 SQL Server 本機執行個體，而非 LocalDB 執行個體。如需詳細資訊，請參閱下方的[啟動及初始化儲存體模擬器](#start-and-initialize-the-storage-emulator)。

您可以安裝 SQL Server Management Studio Express 來管理您的 LocalDB 安裝。儲存體模擬器會使用 Windows 驗證，連接到 SQL Server 或 LocalDB。

儲存體模擬器與 Azure 儲存體服務的功能有所差異。如需有關這些差異的詳細資訊，請參閱[儲存體模擬器和 Azure 儲存體之間的差異](#differences-between-the-storage-emulator-and-azure-storage)。

## 對儲存體模擬器的驗證要求

就像雲端的 Azure 儲存體一樣，您傳送給儲存體模擬器的每個要求都必須經過驗證，除非它是匿名的要求。您可以使用共用金鑰驗證或共用存取簽章 (SAS)，驗證傳送給儲存體模擬器的要求。

### 使用共用金鑰認證進行驗證

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

如需有關連接字串的詳細資訊，請參閱[設定 Azure 儲存體的連接字串](storage-configure-connection-string.md)。

### 使用共用存取簽章進行驗證 

某些 Azure 儲存體用戶端程式庫 (例如 Xamarin 程式庫)，僅支援使用共用存取簽章 (SAS) 權杖進行的驗證。您必須使用可支援共用金鑰驗證的工具或應用程式，來建立此 SAS 權杖。有個簡單的方法可產生 SAS 權杖，也就是透過 Azure PowerShell：

1. 如果尚未安裝 Azure PowerShell，請先安裝。建議使用最新版的 Azure PowerShell Cmdlet。如需安裝指示，請參閱[如何安裝和設定 Azure PowerShell](../articles/powershell-install-configure.md#Install)。

2. 開啟 Azure PowerShell 並執行下列命令。請務必以您自己的認證取代 *ACCOUNT_NAME* 和 *ACCOUNT_KEY = =*。以您選擇的名稱取代 *CONTAINER_NAME*。

		$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
		
		New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
		
		$now = Get-Date 
		
		New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

針對新容器產生的共用存取簽章 URI 應該類似下列項目：

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

使用此範例建立的共用存取簽章的效期為一天。簽章會將完整存取權限 (也就是讀取、寫入、刪除和列出) 授與容器內的 Blob。

如需共用存取簽章的詳細資訊，請參閱[共用存取簽章：了解 SAS 模型](storage-dotnet-shared-access-signature-part-1.md)。


## 啟動及初始化儲存體模擬器

若要啟動 Azure 儲存體模擬器，請選取 [開始] 按鈕或按下 Windows 鍵。先輸入 **Azure 儲存體模擬器**，再從應用程式清單選取模擬器。

如果模擬器正在執行，您就會在 Windows 工作列通知區域看到圖示。

儲存體模擬器啟動時，會出現命令列視窗。您可以使用此命令列視窗來啟動和停止儲存體模擬器、清除資料、取得目前狀態，以及初始化模擬器。如需詳細資訊，請參閱[儲存體模擬器命令列工具參考](#storage-emulator-command-line-tool-reference)。

關閉命令列視窗時，儲存體模擬器會繼續執行。若要再次顯示命令列，請遵循上述步驟，如同啟動儲存體模擬器一樣。

當您第一次執行儲存體模擬器時，系統會為您初始化本機儲存體環境。初始化處理程序會在 LocalDB 中建立資料庫，並為每個本機儲存體服務保留 HTTP 連接埠。

儲存體模擬器預設會安裝至 C:\\Program Files (x86)\\Microsoft SDKs\\Azure\\Storage Emulator 目錄。

### 初始化儲存體模擬器以使用不同的 SQL 資料庫

您可以使用儲存體模擬器命令列工具來初始化儲存體模擬器，以指向預設 LocalDB 執行個體以外的 SQL 資料庫執行個體。請注意，您必須以系統管理權限執行命令列工具，才能初始化儲存體模擬器的後端資料庫：

1. 按一下 [**開始**] 按鈕或按下 [**Windows**] 鍵。先輸入 `Azure Storage Emulator`，並在開始帶出儲存體模擬器命令列工具時加以選取。
2. 在命令提示字元視窗中，輸入下列命令，其中 `<SQLServerInstance>` 是 SQL Server 執行個體的名稱。若要使用 LocalDb，請指定 `(localdb)\v11.0` 做為 SQL Server 執行個體。

		AzureStorageEmulator init /sqlInstance <SQLServerInstance> 
    
	您也可以使用下列命令，引導模擬器使用預設的 SQL Server 執行個體：

    	AzureStorageEmulator init /server .\\ 

	或者，您也可以使用下列命令，將資料庫重新初始化至預設 LocalDB 執行個體：

    	AzureStorageEmulator init /forceCreate 

如需有關這些命令的詳細資訊，請參閱[儲存體模擬器命令列工具參考](#storage-emulator-command-line-tool-reference)。

## 在儲存體模擬器中為資源定址

儲存體模擬器的服務端點與 Azure 儲存體帳戶的服務端點不同。這項差別的原因是，本機電腦不會執行網域名稱解析，所以儲存體模擬器端點需要本機位址，而不是網域名稱。

當您在 Azure 儲存體帳戶中為資源定址時，會使用下列配置；其中的帳戶名稱是 URI 主機名稱的一部分，而要定址的資源是 URI 路徑的一部分：

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

例如，下列 URI 是 Azure 儲存體帳戶中的有效 blob 位址：

	https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

在儲存體模擬器中，因為本機電腦不會執行網域名稱解析，所以帳戶名稱是 URI 路徑的一部分，而不是主機名稱的一部分。您可以針對儲存體模擬器中執行的資源使用下列配置：

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

例如，下列位址可能會用於存取儲存體模擬器中的 Blob：

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

儲存體模擬器的服務端點包括：

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

> [AZURE.NOTE]雖然建議使用 HTTPS 通訊協定來存取 Azure 儲存體中的資源，但是您無法將 HTTPS 與儲存體模擬器搭配使用。
 
### 使用 RA-GRS 為帳戶次要位址定址

從 3.1 版開始，儲存體模擬器帳戶就支援讀取存取地理備援複寫 (RA-GRS)。針對同時位於雲端和本機模擬器中的儲存體資源，您可以藉由將 -secondary 附加到帳戶名稱來存取次要位置。例如，下列位址可能會用於在儲存體模擬器中使用唯讀的次要位置來存取 Blob：

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE]如需以程式設計方式使用儲存體模擬器來存取次要位置，請使用 Storage Client Library for.NET 3.2 版或更新版本。如需詳細資訊，請參閱[儲存體用戶端程式庫參考](https://msdn.microsoft.com/library/azure/dn261237.aspx)。

## 儲存體模擬器命令列工具參考

從 3.0 版開始，在您啟動儲存體模擬器時，命令列視窗隨即出現。使用命令列視窗啟動和停止模擬器，以及查詢狀態和執行其他作業。

> [AZURE.NOTE]如果您已安裝 Microsoft Azure 計算模擬器，當您啟動儲存體模擬器時，就會出現系統匣圖示。使用滑鼠右鍵按一下圖示即可顯示功能表，提供圖形化方式來啟動和停止儲存體模擬器。

### 命令列語法

	AzureStorageEmulator [/start] [/stop] [/status] [/clear] [/init] [/help]

### 選項

若要檢視選項清單，請在命令提示字元輸入 `/help`。

| 選項 | 說明 | 命令 | 引數 |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **Start** | 啟動儲存體模擬器。 | `AzureStorageEmulator start [-inprocess]` | *-inprocess*：在目前的處理序中啟動模擬器，而不是建立新的處理序。 |
| **Stop** | 停止儲存體模擬器。 | `AzureStorageEmulator stop` | |
| **狀態** | 列印儲存體模擬器的狀態。 | `AzureStorageEmulator status` | |
| **Clear** | 清除命令列上指定的所有服務中的資料。 | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *blob*：清除 blob 資料。<br/>*queue*：清除佇列資料。<br/>*table*：清除資料表資料。<br/>*all*：清除所有服務中的所有資料。 |
| **Init** | 執行一次初始化以設定模擬器。 | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-server serverName*：指定裝載 SQL 執行個體的伺服器。<br/>*-sqlinstance instanceName*：指定要使用的 SQL 執行個體的名稱。<br/>*-forcecreate*：強制建立 SQL 資料庫，即使它已經存在。<br/>*-inprocess*：在目前的處理序中執行初始化，而不是繁衍新的處理序。必須以較高權限啟動目前的處理序，才能進行此動作。 |
                                                                                                                  
## 儲存體模擬器和 Azure 儲存體之間的差異

因為儲存體模擬器是在本機 SQL 執行個體中執行的模擬環境，所以模擬器和雲端 Azure 儲存體帳戶之間會有一些功能上的差異：

- 儲存體模擬器僅支援單一固定帳戶及已知的驗證金鑰。

- 儲存體模擬器不是可擴充的儲存體服務，也不支援大量並行用戶端。

- 如[在儲存體模擬器中定址資源](#addressing-resources-in-the-storage-emulator)中所述，資源在儲存體模擬器與在 Azure 儲存體帳戶中的定址方式不同。這項差別的原因是，雲端提供網域名稱解析，而本機電腦沒有提供。

- 從 3.1 版開始，儲存體模擬器帳戶就支援讀取存取地理備援複寫 (RA-GRS)。在模擬器中，所有帳戶均已啟用 RA-GRS，而且主要和次要複本之間絕對不會有延遲。次要帳戶支援 Get Blob Service Stats、Get Queue Service Stats 和 Get Table Service Stats 作業，且這些作業一律會根據基礎 SQL 資料庫傳回 `LastSyncTime` 回應元素的值當成目前的時間。

	如需以程式設計方式使用儲存體模擬器來存取次要位置，請使用 Storage Client Library for.NET 3.2 版或更新版本。如需詳細資訊，請參閱[儲存體用戶端程式庫參考](https://msdn.microsoft.com/library/azure/dn261237.aspx)。

- 儲存體模擬器目前不支援檔案服務和 SMB 通訊協定服務端點。

### Blob 儲存體的差異 

下列差異適用於模擬器中的 Blob 儲存體：

- 儲存體模擬器最多只支援 2 GB 的 blob 大小。

- 針對存在於儲存體模擬器中的 blob 進行的 Put Blob 作業會成功，並擁有作用中的租用 (即使要求中並未指定租用識別碼)。

### 資料表儲存體的差異 

下列差異適用於模擬器中的資料表儲存體：

- 在儲存體模擬器中，資料表服務的日期屬性只支援 SQL Server 2005 所支援的範圍 (也就是必須晚於 1753 年 1 月 1 日)。1753 年 1 月 1 日之前的所有日期都會變成此值。日期的精確度受限於 SQL Server 2005 的精確度，也就是會精確度達到 1/300 秒。

- 儲存體模擬器支援屬性值小於 512 個位元組的資料分割索引鍵與資料列索引鍵。此外，帳戶名稱、資料表名稱和索引鍵屬性名稱的大小總計不得超過 900 個位元組。

- 在儲存體模擬器中，資料表的資料列大小總計限制為小於 1 MB。

- 在儲存體模擬器中，資料類型 `Edm.Guid` 或 `Edm.Binary` 的屬性只支援查詢篩選字串中的 `Equal (eq)` 與 `NotEqual (ne)` 比較運算子。

### 佇列儲存體的差異

模擬器中的佇列儲存體沒有特定差異。

## 儲存體模擬器版本資訊

### 4.0 版

儲存體模擬器可執行檔已重新命名為 *AzureStorageEmulator.exe*。

### 3.2 版
- 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上的 2014-02-14 版儲存體服務。請注意，儲存體模擬器目前不支援檔案服務端點。如需有關 2014-02-14 版的詳細資訊，請參閱 [為 Windows Azure 中的 Blob、佇列和表格服務進行版本設定](https://msdn.microsoft.com/library/azure/dd894041.aspx)。

### 3.1 版
- 儲存體模擬器現在支援讀取權限異地備援儲存體 (RA-GRS)。針對次要帳戶支援 Get Blob Service Stats、Get Queue Service Stats 和 Get Table Service Stats API，且這些 API 一律會根據基礎 SQL Database 傳回 LastSyncTime 回應元素的值當成目前的時間。如需以程式設計方式使用儲存體模擬器來存取次要位置，請使用 Storage Client Library for.NET 3.2 版或更新版本。如需詳細資訊，請參閱＜儲存體用戶端程式庫參考＞。

### 3.0 版
- Azure 儲存體模擬器不再隨附於計算模擬器封裝。

- 儲存體模擬器圖形化使用者介面已由可編寫指令碼的命令列介面取代。如需有關命令列介面的詳細資訊，請參閱儲存體模擬器命令列工具參考。3.0 版仍將提供圖形化介面，除非在系統匣圖示按一下滑鼠右鍵並選取 [顯示儲存體模擬器 UI] 的方式安裝計算模擬器，否則無法存取圖形化介面。

- 現在完全支援 2013-08-15 版的 Azure 儲存體服務。(先前只有儲存體模擬器 2.2.1 版預覽才支援此版本)。



 

<!---HONumber=July15_HO2-->