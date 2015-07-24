<properties
	pageTitle="將 Azure PowerShell 與 Azure 儲存體搭配使用 | Microsoft Azure"
	description="了解如何使用適用於 Azure 儲存體的 Azure PowerShell Cmdlet 建立和管理儲存體帳戶；使用 Blob、資料表、佇列和檔案；設定和查詢儲存體分析，並建立共用存取簽章。"
	services="storage"
	documentationCenter="na"
	authors="tamram" 
	manager="adinah"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2015"
	ms.author="selcint"/>

# 搭配使用 Azure PowerShell 與 Azure 儲存體

## 概觀

在本指南中，我們將探討如何透過[適用於儲存體的 Azure 服務管理 Cmdlet](https://msdn.microsoft.com/library/azure/dn806401.aspx)，使用 Azure 儲存體執行各種開發和管理工作。

Azure PowerShell 是個模組，其提供了各種 Cmdlet 來透過 Windows PowerShell 管理 Azure。它是以工作為基礎的命令列殼層和指令碼語言，特別為系統管理所設計。使用 PowerShell，您可以輕鬆控制和自動執行 Azure 服務和應用程式的管理。例如，您可透過 Azure 管理入口網站執行的工作，大多也可使用 Cmdlet 來執行。

本指南假設您過去有使用 [Azure 儲存體](http://azure.microsoft.com/documentation/services/storage/)和 [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx) 的經驗。本指南提供的一些指令碼示範如何搭配使用 PowerShell 與 Azure 儲存體。您應該在執行每個指令碼之前，先根據您的組態更新指令碼變數。

本指南的第一節提供 Azure 儲存體和 PowerShell 的快速概覽。如需詳細資訊和指示，請從[搭配使用 Azure PowerShell 與 Azure 儲存體的先決條件](#prerequisites-for-using-azure-powershell-with-azure-storage)開始閱讀。


## 在 5 分鐘內開始使用 Azure 儲存體和 PowerShell

本節說明如何在 5 分鐘內透過 PowerShell 存取 Azure 儲存體。

**Azure 新手：**取得 Microsoft Azure 訂用帳戶和與該訂用帳戶相關聯的 Microsoft 帳戶。如需 Azure 購買選項的資訊，請參閱[免費試用](http://azure.microsoft.com/pricing/free-trial/)、[購買選項](http://azure.microsoft.com/pricing/purchase-options/)和[會員優惠](http://azure.microsoft.com/pricing/member-offers/) (適用於 MSDN、Microsoft 合作夥伴網路、BizSpark 和其他 Microsoft 方案的成員)。

如需有關 Azure 帳戶的詳細資訊，請參閱[管理帳戶、訂用帳戶和系統管理角色](https://msdn.microsoft.com/library/azure/hh531793.aspx)。

**建立 Microsoft Azure 訂用帳戶和帳戶之後：**

1.	下載並安裝 [Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409)。
2.	啟動 Windows PowerShell 整合式指令碼環境 (ISE)：在本機電腦中移至 **開始**功能表。鍵入**系統管理工具**，然後按一下以執行。在 [**系統管理工具**] 視窗中，以滑鼠右鍵按一下 [**Windows PowerShell ISE**]，然後按一下 [**以系統管理員身分執行**]。
3.	在 **Windows PowerShell ISE** 中，按一下 [**檔案**] > [**新增**]，建立新的指令碼檔。
4.	現在，我們將提供簡單的指令碼，顯示用以存取 Azure 儲存體的基本 PowerShell 命令。此指令碼會先要求您的 Azure 帳戶認證，以將您的 Azure 帳戶新增到本機 PowerShell 環境。然後，指令碼會設定預設 Azure 訂用帳戶，並在 Azure 中建立新的儲存體帳戶。接著，指令碼將在這個新的儲存體帳戶中建立新容器，並將現有的映像檔案 (Blob) 上傳至該容器。指令碼列出該容器中的所有 Blob 之後，它會在本機電腦中建立新的目的地目錄並下載映像檔。
5.	在下列程式碼區段中，選取 **#begin** 和 **#end** 備註之間的指令碼。按 CTRL+C 將它複製到剪貼簿。

    	#begin
    	# Update with the name of your subscription.
    	$SubscriptionName="YourSubscriptionName"

    	# Give a name to your new storage account. It must be lowercase!
    	$StorageAccountName="yourstorageaccountname"

    	# Choose "West US" as an example.
    	$Location = "West US"

    	# Give a name to your new container.
    	$ContainerName = "imagecontainer"

    	# Have an image file and a source directory in your local computer.
    	$ImageToUpload = "C:\Images\HelloWorld.png"

    	# A destination directory in your local computer.
    	$DestinationFolder = "C:\DownloadImages"

    	# Add your Azure account to the local PowerShell environment.
    	Add-AzureAccount

    	# Set a default Azure subscription.
    	Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

    	# Create a new storage account.
    	New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location

    	# Set a default storage account.
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

    	# Create a new container.
    	New-AzureStorageContainer -Name $ContainerName -Permission Off

    	# Upload a blob into a container.
    	Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

    	# List all blobs in a container.
    	Get-AzureStorageBlob -Container $ContainerName

    	# Download blobs from the container:
    	# Get a reference to a list of all blobs in a container.
    	$blobs = Get-AzureStorageBlob -Container $ContainerName

    	# Create the destination directory.
    	New-Item -Path $DestinationFolder -ItemType Directory -Force  

    	# Download blobs into the local destination directory.
    	$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
    	#end

5.	在 **Windows PowerShell ISE** 中，按 CTRL+V 以複製指令碼。按一下 [**檔案**] > [**儲存**]。在 [**另存新檔**] 對話方塊視窗中，鍵入指令碼檔的名稱，例如「mystoragescript」。按一下 [儲存]。

6.	現在，您需要根據您的組態設定更新指令碼變數。您必須使用自己的訂用帳戶更新 **$SubscriptionName** 變數。您可以保留指令碼中指定的其他變數或視需要予以更新。

	- **$SubscriptionName：**您必須使用自己的訂用帳戶名稱更新此變數。依照下列其中一個方式執行，即可找出您的訂用帳戶名稱：

		a.在 **Windows PowerShell ISE** 中，按一下 [**檔案**] > [**新增**]，建立新的指令碼檔。將下列指令碼複製到新的指令碼檔，然後按一下 [**偵錯**] > [**執行**]。下列指令碼會先要求您的 Azure 帳戶認證，以將您的 Azure 帳戶新增到本機 PowerShell 環境，然後顯示連接到本機 PowerShell 工作階段的所有訂用帳戶。請記下遵循此教學課程時，您所要使用的訂用帳戶名稱：

    		Add-AzureAccount
       		Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName


		b.Azure 目前支援兩個入口網站：[Azure 管理入口網站](https://manage.windowsazure.com/)和 [Azure Preview 入口網站](https://portal.azure.com/)。如果您已登入目前的 [Azure 管理入口網站](https://portal.azure.com/)，請向下捲動並按一下入口網站左側的 [**設定**]。按一下 [**訂用帳戶**]。複製您想要在執行本指南中提供的指令碼時使用的訂用帳戶名稱。請參閱下列螢幕擷取畫面作為範例：

		![Azure 管理入口網站][Image1]

		c.如果您已登入 [Azure Preview 入口網站](https://portal.azure.com/)，請按一下左側 [中樞] 功能表中的 [**瀏覽**]。然後按一下 [**全部**]，再按一下 [**訂用帳戶**]。複製您想要在執行本指南中的指令碼時使用的訂用帳戶名稱。請參閱下列螢幕擷取畫面作為範例：

		![Azure Preview 入口網站][Image2]


	- **$StorageAccountName：**使用指令碼中的指定名稱，或是為儲存體帳戶輸入新名稱。**重要事項：**儲存體帳戶的名稱在 Azure 中必須是唯一的名稱。而且必須是小寫字母！

	- **$Location：**使用指令碼中指定的「美國西部」，或選擇其他的 Azure 地點，例如美國東部、北歐等。

	- **$ContainerName：**使用指令碼中的指定名稱，或是為容器的輸入新名稱。

	- **$ImageToUpload：**輸入位於本機電腦上的圖片的路徑，例如：「C:\\Images\\HelloWorld.png」。

	- **$DestinationFolder：**輸入本機目錄的路徑，以儲存從 Azure 儲存體下載的檔案，例如：「C:\\DownloadImages」。

7.	更新「mystoragescript.ps1」檔案中的指令碼變數之後，請按一下 [**檔案**] > [**儲存**]。然後按一下 [**偵錯**] > [**執行**]，或按 **F5** 以執行指令碼。

在指令碼執行之後，您應該有包含下載的映像檔案的本機目的資料夾。以下螢幕擷取畫面顯示範例輸出︰

![下載 Blob][Image3]


> [AZURE.NOTE]「在 5 分鐘內開始使用 Azure 儲存體和 PowerShell」一節提供有關如何搭配使用 Azure PowerShell 與 Azure 儲存體的快速簡介。如需詳細資訊和指示，我們鼓勵您閱讀下列各節。

## 搭配使用 Azure PowerShell 與 Azure 儲存體的先決條件
您需要有 Azure 訂用帳戶和帳戶，才能如上面說明的方法執行本指南提供的 PowerShell Cmdlet。

Azure PowerShell 是個模組，其提供了各種 Cmdlet 來透過 Windows PowerShell 管理 Azure。如需安裝和設定 Azure PowerShell 的資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。建議您在使用本指南之前，先下載並安裝或升級至最新的 Azure PowerShell 模組。

您可以在 Azure PowerShell 主控台、標準 Windows PowerShell 主控台或 Windows PowerShell 整合式指令碼環境 (ISE) 中執行這些 Cmdlet。例如，若要開啟 **Azure PowerShell 主控台**，請移至 [開始] 功能表、鍵入 Microsoft Azure PowerShell，以滑鼠右鍵按一下，然後按一下 [以系統管理員身分執行]。若要開啟 **Windows PowerShell ISE**，請移至 [開始] 功能表、鍵入系統管理工具，然後按一下加以執行。在 [系統管理工具] 視窗中，以滑鼠右鍵按一下 [Windows PowerShell ISE]，按一下 [以系統管理員身分執行]。

## 如何在 Azure 中管理儲存體帳戶

### 如何設定預設 Azure 訂用帳戶
若要使用 Azure PowerShell 管理 Azure 儲存體，您需要透過 Azure Active Directory 驗證或憑證型驗證向 Azure 驗證用戶端環境。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 教學課程。本指南使用 Azure Active Directory 驗證。

1.	在 Azure PowerShell 主控台或 Windows PowerShell ISE 中，輸入下列命令，將您的 Azure 帳戶新增到本機的 PowerShell 環境：

    `Add-AzureAccount`

2.	在 [登入 Microsoft Azure] 視窗中，輸入與您的帳戶相關聯的電子郵件地址和密碼。Azure 會驗證並儲存認證資訊，然後關閉視窗。

3.	接著，執行下列命令以檢視本機 PowerShell 環境的 Azure 帳戶，並確認您的帳戶已列出：

	`Get-AzureAccount`

4.	然後，執行下列 Cmdlet 可檢視已連接到本機 PowerShell 工作階段的所有訂用帳戶，並確認您的訂用帳戶已列出：

	`Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`

5.	若要設定預設 Azure 訂用帳戶，請執行 Select-azuresubscription Cmdlet：

	    $SubscriptionName = 'Your subscription Name'
    	Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

6.	執行 Get-azuresubscription Cmdlet 來確認預設訂用帳戶的名稱：

	`Get-AzureSubscription -Default`

7.	若要查看 Azure 儲存體的所有可用 PowerShell Cmdlet，請執行：

	`Get-Command -Module Azure -Noun *Storage*`

### 如何建立新的 Azure 儲存體帳戶
若要使用 Azure 儲存體，您將需要儲存體帳戶。設定電腦以連接至您的訂用帳戶之後，您可以建立新的 Azure 儲存體帳戶。

1.	執行 Get-azurelocation Cmdlet 來尋找所有可用的資料中心位置：

    `Get-AzureLocation | format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.	接著，執行 New-AzureStorageAccount Cmdlet 來建立新的儲存體帳戶。下列範例會在「美國西部」資料中心建立新的儲存體帳戶。

    	$location = "West US"
	    $StorageAccountName = "yourstorageaccount"
	    New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location

> [AZURE.IMPORTANT]儲存體帳戶的名稱在 Azure 中必須是獨一無二的且必須小寫。如需命名慣例與限制，請參閱[關於 Azure 儲存體帳戶](storage-create-storage-account.md)和[命名和參考容器、Blob 及中繼資料](http://msdn.microsoft.com/library/azure/dd135715.aspx)。

### 如何設定預設 Azure 儲存體帳戶
您可以在訂用帳戶中有多個儲存體帳戶。您可以選擇其中一個儲存體帳戶，並將它設為相同 PowerShell 工作階段中所有儲存體命令的預設儲存體帳戶。這可讓您執行 Azure PowerShell 儲存體命令，而不需明確指定儲存體內容。

1.	若要設定您的訂用帳戶的預設儲存體帳戶，您可以執行 Set-azuresubscription Cmdlet。

		$SubscriptionName = "Your subscription name"
     	$StorageAccountName = "yourstorageaccount"  
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

2.	接著，執行 Get-azuresubscription Cmdlet 來確認儲存體帳戶與預設訂用帳戶相關聯。這個命令會傳回目前訂用帳戶的訂用帳戶屬性，包括其目前的儲存體帳戶。

	    Get-AzureSubscription –Current

### 如何列出訂用帳戶中的所有 Azure 儲存體帳戶
每個 Azure 訂用帳戶可以擁有高達 100 個儲存體帳戶。如需最新的限制資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。

執行下列 Cmdlet 來找出目前訂用帳戶中儲存體帳戶的名稱和狀態：

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### 如何建立 Azure 儲存體內容
Azure 儲存體內容是 PowerShell 中用以封裝儲存體認證的物件。在執行任何後續 Cmdlet 時使用儲存體內容，可讓您驗證您的要求，而不需明確指定儲存體帳戶和其存取金鑰。您可以用很多方式建立儲存體內容，例如使用儲存體帳戶名稱和存取金鑰、共用存取簽章 (SAS) 權杖、連接字串或匿名。如需詳細資訊，請參閱 [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx)。

使用下列三種方式之一來建立儲存體內容：

- 執行 [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) Cmdlet 可找出 Azure 儲存體帳戶的主要儲存體存取金鑰。接著，呼叫 [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) Cmdlet，以建立儲存體內容：

    	$StorageAccountName = "yourstorageaccount"
    	$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    	$Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- 產生 Azure 儲存體容器的共用存取簽章權杖，並用來它建立儲存體內容：

    	$sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
    	$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

	如需詳細資訊，請參閱 [New-AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx) 和[共用存取簽章，第 1 部分：了解 SAS 模型](storage-dotnet-shared-access-signature-part-1.md)。

- 在某些情況下，您可能想要在建立新的儲存體內容時指定服務端點。當您向 Blob 服務註冊儲存體帳戶的自訂網域名稱，或想要使用共用存取簽章存取儲存體資源時，這可能是必要作業。在連接字串中設定服務端點，並用來建立新的儲存體內容，如下所示：

    	$ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
    	$Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

如需如何設定儲存體連接字串的詳細資訊，請參閱[設定連接字串](storage-configure-connection-string.md)。

您現已設定您的電腦並學會如何使用 Azure PowerShell 管理訂用帳戶和儲存體帳戶。請移至下一節，以了解如何管理 Azure Blob 和 Blob 快照集。

## 如何管理 Azure Blob 和 Blob 快照集
Azure Blob 儲存體是一項儲存大量非結構化資料的服務 (例如文字或二進位資料)，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。本節假設您已熟悉 Azure Blob 儲存體服務概念。如需詳細資訊，請參閱[如何使用 .NET 的 Blob 儲存體](storage-dotnet-how-to-use-blobs.md)和 [Blob 服務概念](http://msdn.microsoft.com/library/azure/dd179376.aspx)。

### 如何建立容器
Azure 儲存體中的每個 Blob 必須位於一個容器中。您可以使用 New-AzureStorageContainer Cmdlet 建立私用容器：

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE]匿名讀取權限有三個層級：**Off**、**Blob** 和 **Container**。若要防止匿名存取 Blob，請將 Permission 參數設定為 **Off**。新容器預設為私人，且只能由帳戶擁有者存取。若要允許 Blob 資源的匿名公開讀取權限，但不允許容器中繼資料或容器中 Blob 清單的匿名公開讀取權限，請將 Permission 參數設定為 **Blob**。若要允許 Blob 資源、容器中繼資料或容器中 Blob 清單的完整公開讀取權限，請將 Permission 參數設定為 **Container**。如需詳細資訊，請參閱[管理 Azure 儲存體資源的存取](storage-manage-access-to-resources.md)。

### 如何將 Blob 上傳到容器中
Azure Blob 儲存體支援區塊 Blob 和頁面 Blob。如需詳細資訊，請參閱[了解區塊 Blob 和分頁 Blob](http://msdn.microsoft.com/library/azure/ee691964.aspx)。

若要將 Blob 上傳至容器，您可以使用 [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) Cmdlet。根據預設，此命令會將本機檔案上傳至區塊 Blob。若要指定 Blob 的類型，您可以使用 -BlobType 參數。

下列範例會執行 [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) Cmdlet 以取得指定資料夾中的所有檔案，然後使用管線運算子將這些檔案傳遞至下一個 Cmdlet。[Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) Cmdlet 會將本機檔案上傳至您的容器：

    Get-ChildItem –Path C:\Images* | Set-AzureStorageBlobContent -Container "yourcontainername"

### 如何從容器下載 Blob
下列範例示範如何從容器下載 Blob。此範例會先使用儲存體帳戶內容建立 Azure 儲存體的連線，其中包含儲存體帳戶名稱及其主要存取金鑰 。然後使用 [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) Cmdlet 擷取 Blob 參照。接著再使用 [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) Cmdlet 將 Blob 下載到本機目的地資料夾中。

    #Define the variables.
    $ContainerName = "yourcontainername"
    $DestinationFolder = "C:\DownloadImages"

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Download blobs from a container.
    New-Item -Path $DestinationFolder -ItemType Directory -Force
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### 如何在儲存體容器之間複製 Blob
您可以跨儲存體帳戶和區域以非同步方式複製 Blob。下列範例示範如何在兩個不同的儲存體帳戶中將 Blob 從一個儲存體容器複製到另一個儲存體容器。此範例會先設定來源和目的地儲存體帳戶的變數，然後建立每個帳戶的儲存體內容。接著再使用 [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) Cmdlet 將 Blob 從來源容器複製到目的地容器。此範例假設來源和目的地儲存體帳戶和容器已經存在。

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

請注意，此範例會執行非同步複製。您可以透過執行 [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx) Cmdlet，監視每個副本的狀態。

### 如何從次要位置複製 Blob
您可以從支援 RA-GRS 之帳戶的次要位置複製 Blob。

    #define secondary storage context using a connection string constructed from secondary endpoints. 
    $SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
    Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext

### 如何刪除 Blob
若要刪除 Blob，請先取得 Blob 參考，然後在該參考上呼叫 Remove-AzureStorageBlob Cmdlet。下列範例會刪除指定的容器中的所有 Blob。此範例會先設定儲存體帳戶的變數，然後建立儲存體內容。接著再使用 [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) Cmdlet 擷取 Blob 參照，並執行 [Remove-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806399.aspx) Cmdlet，以從 Azure 儲存體中的容器移除 Blob。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob

## 如何管理 Azure Blob 快照集
Azure 可讓您建立 Blob 的快照集。快照集是在某個點時間取得的唯讀 Blob 版本。一旦建立快照集後，即可加以讀取、複製或刪除，但不能修改。快照集提供在某個時間點備份 Blob 的方法。如需詳細資訊，請參閱[建立 Blob 的快照集](http://msdn.microsoft.com/library/azure/hh488361.aspx)。

### 如何建立 Blob 快照集
若要建立 Blob 的快照，請先取得 Blob 參照，然後在該參考上呼叫 `ICloudBlob.CreateSnapshot` 方法。下列範例會先設定儲存體帳戶的變數，然後建立儲存體內容。接著再使用 [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) Cmdlet 擷取 Blob 參考，並執行 [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) 方法建立快照。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

### 如何列出 Blob 的快照
您可以對 Blob 建立您所需數量的快照集。您可以列出與 Blob 相關聯的快照集，以追蹤目前的快照集。下列範例會使用預先定義的 Blob，並呼叫 [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) Cmdlet 以列出該 Blob 的快照。

    #Define the blob name.
    $BlobName = "yourblobname"

    #List the snapshots of a blob.
    Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### 如何複製 Blob 的快照集
您可以複製 Blob 的快照集以還原該快照集。如需詳細的資訊及限制，請參閱[建立 Blob 的快照集](http://msdn.microsoft.com/library/azure/hh488361.aspx)。下列範例會先設定儲存體帳戶的變數，然後建立儲存體內容。接著，此範例會定義容器和 Blob 名稱變數。然後使用 [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) 擷取 Blob 參考，並執行 [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) 方法建立快照。再執行 [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) Cmdlet，並使用來源 Blob 的 ICloudBlob 物件複製 Blob 的快照。執行此範例前，請務必根據您的組態更新變更。請注意，下列範例假設來源和目的地容器和來源 Blob 已經存在。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

您現已學會如何使用 Azure PowerShell 來管理 Azure Blob 和 Blob 快照集。請移至下一節，以了解如何管理資料表、佇列和檔案。

## 如何管理 Azure 資料表和資料表實體
Azure 資料表儲存體服務是 NoSQL 資料存放區，您可以用來儲存和查詢龐大的結構化、非關聯式資料集。服務的主要元件是資料表、實體和屬性。資料表是一組實體。實體是一組屬性。每個實體最多可有 252 個屬性，也就是所有的名稱/值組。本節假設您已熟悉 Azure 資料表儲存體服務概念。如需詳細資訊，請參閱[了解表格服務資料模型](http://msdn.microsoft.com/library/azure/dd179338.aspx)和[如何使用 .NET 的資料表儲存體](storage-dotnet-how-to-use-tables.md)。

在下列小節中，您將學習如何使用 Azure PowerShell 管理 Azure 資料表儲存體服務。涵蓋的狀況包括**建立**、**刪除**和**擷取****資料表**，以及**新增**、**查詢**和**刪除資料表實體**。

### 如何建立資料表
每個資料表必須位於 Azure 儲存體帳戶中。下列範例示範如何在 Azure 儲存體中建立資料表。此範例會先使用儲存體帳戶內容建立 Azure 儲存體的連線，其中包含儲存體帳戶名稱及其存取金鑰 。接著再使用 [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) Cmdlet 在 Azure 儲存體中建立資料表。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable –Name $tabName –Context $Ctx

### 如何擷取資料表
您可以查詢和擷取儲存體帳戶中的一個或所有資料表。下列範例示範如何使用 [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) 擷取指定的資料表。

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable –Name $tabName –Context $Ctx

如果您未使用任何參數呼叫 Get-AzureStorageTable Cmdlet，它會取得儲存體帳戶的所有儲存體資料表。

### 如何刪除資料表
您可以使用 [Remove-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx) Cmdlet 刪除儲存體帳戶中的資料表。

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable –Name $tabName –Context $Ctx

### 如何管理資料表實體
目前，Azure PowerShell 不會提供 Cmdlet 來直接管理資料表實體。若要在資料表實體上執行作業，您可以使用適用於 [.NET 的 Azure 儲存體用戶端程式庫](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)中提供的類別。

#### 如何新增資料表實體
若要將實體新增至資料表，請先建立一個可定義實體屬性的物件。實體最多可有 255 個屬性，包括 3 個系統屬性：**PartitionKey**、**RowKey** 和 **Timestamp**。您需負責插入及更新 **PartitionKey** 和 **RowKey** 的值。伺服器會管理 **Timestamp** 的值，該值無法予以修改。**PartitionKey** 和 **RowKey**可聯手唯一地識別資料表內的每個實體。

-	**PartitionKey**：決定儲存實體的資料分割。
-	**RowKey**：在資料分割內唯一地識別實體。

您可以為每個實體最多定義 252 個自訂屬性。如需詳細資訊，請參閱[瞭解表格服務資料模型](http://msdn.microsoft.com/library/azure/dd179338.aspx)。

下列範例示範如何將實體加入至資料表。此範例示範如何擷取員工資料表並在其中加入數個項實體。首先，它會先使用儲存體帳戶內容建立 Azure 儲存體的連線，其中包含儲存體帳戶名稱及其存取金鑰 。接著，再使用 [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) Cmdlet 擷取指定的資料表。如果資料表不存在，可使用 [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) Cmdlet 在 Azure 儲存體中建立資料表。接下來，此範例會定義 Add-Entity 自訂函數，經由指定每個實體的磁碟分割和資料列索引鍵，將實體加入至資料表。Add-Entity 函數會在 [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) 類別上呼叫 [New-Object](http://technet.microsoft.com/library/hh849885.aspx) Cmdlet，以建立實體物件。之後，此範例會在此實體物件上呼叫 [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) 方法，以將它加入至資料表。

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity()
    {
    param(
       $table,
       [String]$partitionKey,
       [String]$rowKey,
       [String]$name,
       [Int]$id
    )

      $entity = New-Object Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)

      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"

    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable –Name $TableName -Context $Ctx
    }

    #Add multiple entities to a table.
    Add-Entity $table "Partition1" "Row1" "Chris" 1
    Add-Entity $table "Partition1" "Row2" "Jessie" 2
    Add-Entity $table "Partition2" "Row1" "Christine" 3
    Add-Entity $table "Partition2" "Row2" "Steven" 4


#### 如何查詢資料表實體
若要查詢資料表，請使用 [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx) 類別。下列範例假設您已執行本指南的「如何新增實體」一節中提供的指令碼。此範例會先使用儲存體內容建立 Azure 儲存體的連線，其中包含儲存體帳戶名稱及其存取金鑰 。接著，再使用 [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) Cmdlet 嘗試擷取先前建立的「員工」資料表。在 Microsoft.WindowsAzure.Storage.Table.TableQuery 類別上呼叫 [New-Object](http://technet.microsoft.com/library/hh849885.aspx) Cmdlet，可建立新的查詢物件。此範例會尋找 'ID' 資料行的值為 1 (如字串篩選條件中指定) 的實體。如需詳細資訊，請參閱 [查詢資料表和實體](http://msdn.microsoft.com/library/azure/dd894031.aspx)。當您執行此查詢時，它會傳回所有符合篩選準則的所有實體。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"

    #Get a reference to a table.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx

    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

    #Define columns to select.
    $list = New-Object System.Collections.Generic.List[string]
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")

    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20

    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)

    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties[“ID”].Int32Value}} -AutoSize

#### 如何刪除資料表實體
您可以使用實體的資料分割和資料列索引鍵來刪除實體。下列範例假設您已執行本指南的「如何新增實體」一節中提供的指令碼。此範例會先使用儲存體內容建立 Azure 儲存體的連線，其中包含儲存體帳戶名稱及其存取金鑰 。接著，再使用 [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) Cmdlet 嘗試擷取先前建立的「員工」資料表。如果資料表已存在，則會呼叫 [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) 方法，根據分割及資料列索引鍵的值擷取實體。然後，將實體傳遞至 [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) 方法加以刪除。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null)
    {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve(“Partition2”, "Row1"))
       $entity = $tableResult.Result;
    if ($entity -ne $null)
    {
       #Delete the entity.$table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## 如何管理 Azure 佇列和佇列訊息
Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。本節假設您已熟悉 Azure 佇列儲存體服務概念。如需詳細資訊，請參閱[如何使用 .NET 的佇列儲存體](storage-dotnet-how-to-use-queues.md)。

本節將示範如何使用 Azure PowerShell 來管理 Azure 佇列儲存體服務。涵蓋的狀況包括**插入**和**刪除**佇列訊息，以及**建立**、**刪除**和**擷取佇列**。

### 如何建立佇列
下列範例會先使用儲存體帳戶內容建立 Azure 儲存體的連線，其中包含儲存體帳戶名稱及其存取金鑰 。接著再呼叫 [New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) Cmdlet 以建立名為「queuename」的佇列。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx

如需 Azure 佇列服務命名慣例的資訊，請參閱[為佇列和中繼資料命名](http://msdn.microsoft.com/library/azure/dd179349.aspx)。

### 如何擷取佇列
您可以查詢與擷取儲存體帳戶中的特定佇列或所有佇列清單。下列範例示範如何使用 [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) Cmdlet 擷取指定的佇列。

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx

如果您未使用任何參數，直接呼叫 [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx)，會取得包含所有佇列的清單。

### 如何刪除佇列
若要刪除佇列及其內含的所有訊息，請呼叫 Remove-AzureStorageQueue Cmdlet。下列範例示範如何使用 Remove-AzureStorageQueue Cmdlet 刪除指定的佇列。

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue –Name $QueueName –Context $Ctx

### 如何管理佇列訊息
目前，Azure PowerShell 不會提供 Cmdlet 來直接管理佇列實體。若要在佇列訊息上執行作業，您可以使用[適用於 .NET 的 Azure 儲存體用戶端程式庫](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)中提供的類別。

#### 如何將訊息插入佇列中
若要將訊息插入現有佇列中，請先建立 [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) 類別的新執行個體。接著，呼叫 [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) 方法。您可以從字串 (採用 UTF-8 格式) 或位元組陣列建立 CloudQueueMessage。

下列範例示範如何將訊息加入佇列中。此範例會先使用儲存體帳戶內容建立 Azure 儲存體的連線，其中包含儲存體帳戶名稱及其存取金鑰 。接著，再使用 [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx) Cmdlet 擷取指定的佇列。如果佇列存在，可使用 [New-Object](http://technet.microsoft.com/library/hh849885.aspx) Cmdlet 建立 [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) 類別的執行個體。之後，此範例會在此訊息物件上呼叫 [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) 方法，以將其加入佇列。以下是可擷取佇列及插入訊息「MessageInfo」的程式碼：

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    #If the queue exists, add a new message.
    if ($Queue -ne $null)
    {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object "Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage" "MessageInfo"

       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    }


#### 如何在下一個訊息中清除佇列
您的程式碼可以使用兩個步驟將訊息自佇列中清除佇列。呼叫 [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) 方法，會取得佇列中的下一個訊息。從 **GetMessage** 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言將會是不可見的。若要完成從佇列移除訊息的動作，您還必須呼叫 [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) 方法。這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。您的程式碼會在處理完訊息之後立即呼叫 **DeleteMessage**。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage)

## 如何管理 Azure 檔案共用和檔案
Azure 檔案儲存體為使用標準 SMB 2.1 通訊協定的應用程式提供共用儲存體。Microsoft Azure 虛擬機器和雲端服務可以透過掛接的共用，在應用程式元件之間共用檔案資料，而內部部署應用程式可以透過檔案儲存體 API 或 Azure PowerShell，存取共用中的檔案資料。

如需 Azure 檔案儲存體的詳細資訊，請參閱[如何使用 Azure 檔案儲存體](storage-dotnet-how-to-use-files.md)和[檔案服務 REST API](http://msdn.microsoft.com/library/azure/dn167006.aspx)。

## 如何設定及查詢儲存體分析
您可以使用 [Azure 儲存體分析](storage-analytics.md)收集 Azure 儲存體帳戶的計量，以及關於傳送至儲存體帳戶之要求的記錄資料。也可以使用儲存體計量監視儲存體帳戶的健康狀態，並使用儲存體記錄診斷和疑難排解儲存體帳戶的問題。根據預設，不會針對您的儲存體服務啟用儲存體計量功能。您可以使用 Azure 管理入口網站、Windows PowerShell，或以程式設計方式透過儲存體 API，來啟用監視。系統會在伺服器端執行儲存體記錄，這可讓您在儲存體帳戶中記錄成功和失敗要求的詳細資料。這些記錄檔可讓您查看資料表、佇列和 Blob 的讀取、寫入和刪除作業詳細資料，以及失敗要求的原因。

若要了解如何使用 PowerShell 啟用和檢視儲存體計量的資料，請參閱[如何使用 PowerShell 啟用儲存體度量](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell)。

若要了解如何使用 PowerShell 啟用和擷取儲存體記錄的資料，請參閱 [如何使用 PowerShell 啟用儲存體記錄](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell)和[尋找儲存體記錄的記錄資料](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata)。如需使用儲存體計量和儲存體記錄疑難排解儲存體問題的詳細資訊，請參閱[監控、診斷和疑難排解 Microsoft Azure 儲存體](storage-monitoring-diagnosing-troubleshooting.md)。

## 如何管理共用存取簽章 (SAS) 和預存的存取原則
對於任何使用 Azure 儲存體的應用程式而言，共用存取簽章是安全性模型不可或缺的一部分。若要提供您儲存體帳戶的有限權限給沒有帳戶金鑰的用戶端，它們是非常有用的方式。根據預設，只有儲存體帳戶的擁有者可以存取該帳戶內的 Blob、資料表和佇列。如果您的服務或應用程式需要將這些資源提供給其他用戶端使用，而不共用存取金鑰，您會有下列三個選項：

- 設定容器的權限，以允許對容器及其 Blob 進行匿名的讀取存取。這不適用於資料表或佇列。
- 使用共用存取簽章，可針對特定的時間間隔，授與容器、Blob、佇列和資料表有限的存取權限。
- 使用預存的存取原則，針對容器或其 Blob、佇列或資料表的共用存取簽章取得一層額外控制。預存的存取原則讓您能夠變更開始時間、到期時間或簽章的權限，或者在發出之後將它撤銷。

共用存取簽章可以是下列其中一種格式：

- **臨機操作 SAS**：當您建立臨機操作的 SAS 時，SAS 的開始時間、到期時間和權限全都標示在 SAS URI 上。您可以在容器、Blob、資料表或佇列上建立此類型的 SAS，而且無法撤銷它。
- **具有預存存取原則的 SAS**：預存存取原則是在資源容器、Blob 容器、資料表或佇列中定義，且可用來管理一或多個共用存取簽章的條件約束。當您將 SAS 與預存存取原則建立關聯時，SAS 會繼承為該預存存取原則所定義的限制 (開始時間、過期時間和權限)。這種類型的 SAS 是可撤銷的。

如需詳細資訊，請參閱[共用存取簽章教學課程](storage-dotnet-shared-access-signature-part-1.md)和[管理 Azure 儲存體資源的存取](storage-manage-access-to-resources.md)。

在下一節中，您將了解如何為 Azure 資料表建立共用存取簽章權杖和預存的存取原則。Azure PowerShell 也會為容器、Blob 和佇列提供類似的 Cmdlet。若要執行本節中的指令碼，請下載 [Azure PowerShell 0.8.14 版](http://go.microsoft.com/?linkid=9811175&clcid=0x409)或更新版本。

### 如何建立原則式共用存取簽章權杖
使用 New-AzureStorageTableStoredAccessPolicy Cmdlet 來建立新的預存存取原則。然後呼叫 [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) Cmdlet ，為 Azure 儲存體資料表建立新的原則式共用存取簽章權杖。

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### 如何建立臨機操作 (非可撤銷) 的共用存取簽章權杖
[New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) Cmdlet 可為 Azure 儲存體資料表建立新的臨機操作 (無法撤銷) 的共用存取簽章權杖：

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### 如何建立預存的存取原則
使用 New-AzureStorageTableStoredAccessPolicy Cmdlet，為 Azure 儲存體資料表建立新的預存存取原則：

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### 如何更新預存的存取原則
使用 Set-AzureStorageTableStoredAccessPolicy Cmdlet，為 Azure 儲存體資料表更新現有的預存存取原則：

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### 如何刪除預存的存取原則
使用 Remove-AzureStorageTableStoredAccessPolicy Cmdlet，在 Azure 儲存體資料表上刪除預存的存取原則：

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## 如何使用適用於美國政府和 Azure China 的 Azure 儲存體
Azure 環境是 Microsoft Azure 的獨立部署，例如[適用於美國政府的 Azure Government](http://azure.microsoft.com/features/gov/)、[適用於全球 Azure 的 AzureCloud](https://manage.windowsazure.com) 和[由中國世紀互聯運作的AzureChinaCloud for Azure](http://www.windowsazure.cn/)。您可以針對美國政府與 Azure 中國部署新的 Azure 環境。

若要搭配使用 Azure 儲存體與 AzureChinaCloud，您需要建立與 AzureChinaCloud 相關聯的儲存體內容。遵循下列步驟，以便開始使用產品：

1.	執行 [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) Cmdlet，查看可用的 Azure 環境：

    `Get-AzureEnvironment`

2.	將 Azure China 帳戶新增至 Windows PowerShell：

    `Add-AzureAccount –Environment AzureChinaCloud`

3.	建立 AzureChinaCloud 帳戶的儲存體內容：

    	$Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

若要將 Azure 儲存體與[適用於美國政府的 Azure Government ](http://azure.microsoft.com/features/gov/) 搭配使用，請定義一個新環境，然後在此環境中建立新的儲存體內容：

1. 呼叫 [Add-AzureEnvironment](http://msdn.microsoft.com/library/azure/dn790364.aspx) Cmdlet，以為您的私人資料中心建立新的 Azure 環境。

    	Add-AzureEnvironment -Name $EnvironmentName -PublishSettingsFileUrl $publishSettingsFileUrl -ServiceEndpoint $serviceEndpoint -ManagementPortalUrl $managementPortalUrl -StorageEndpoint $storageEndpoint -ActiveDirectoryEndpoint $activeDirectoryEndpoint -ResourceManagerEndpoint $resourceManagerEndpoint -GalleryEndpoint $galleryEndpoint -ActiveDirectoryServiceEndpointResourceId $activeDirectoryServiceEndpointResourceId -GraphEndpoint $graphEndpoint -SubscriptionDataFile $subscriptionDataFile

2. 依照下方所示執行 [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) Cmdlet，為這個新環境建立新的儲存體內容，方法如下所示。

	    $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment $EnvironmentName

如需詳細資訊，請參閱：

- [Microsoft Azure Government 開發人員指南](../azure-government-developer-guide.md)。
- [適用於全域 Azure 的 AzureCloud 與中國 21Vianet 操作的AzureChinaCloud for Azure之間的差異](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## 後續步驟
在本指南，您已了解如何使用 Azure PowerShell 管理 Azure 儲存體。以下是有助於您深入了解的一些相關文章和資源。

- [Azure 儲存體文件](http://azure.microsoft.com/documentation/services/storage/)
- [Azure 儲存體 MSDN 參考](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- [Azure 儲存體 PowerShell Cmdlet](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Windows PowerShell 參考](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next
 

<!---HONumber=July15_HO2-->