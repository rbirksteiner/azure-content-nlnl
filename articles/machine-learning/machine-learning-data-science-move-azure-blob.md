<properties 
	pageTitle="從 Azure Blob 儲存體來回移動資料 | Microsoft Azure" 
	description="從 Azure Blob 儲存體來回移動資料" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="sunliangms;sachouks;mohabib;bradsev" />

# 從 Azure Blob 儲存體來回移動資料

[Azure Machine Learning 中進階分析程序和技術 (ADAPT) 的案例](../machine-learning-data-science-plan-sample-scenarios.md)文章可協助您判斷進階分析程序中各種資料科學工作流程所需的資源。如果您需要根據自己的案例從 Azure Blob 儲存體來回移動資料，請使用下列其中一個方法：

- [使用 Azure 儲存體總管](#explorer)
- [使用 AzCopy 命令列公用程式](#AzCopy)
- [在 Python 中使用 Azure SDK](#PythonSDK)


> [AZURE.TIP]或者，您可以使用 [Azure Data Factory](https://azure.microsoft.com/zh-tw/services/data-factory/) 建立並排程管線，以從 Azure Blob 儲存體下載資料，並將資料傳遞至已發行的 Azure Machine Learning Web 服務，再接收預測性分析的結果，然後將結果上傳至儲存體。如需詳細資訊，請參閱[使用 Azure Data Factory 和 Azure Machine Learning 來建立預測管線](../data-factory/data-factory-create-predictive-pipelines.md)。

<para></para>

> [AZURE.NOTE]如需 Azure Blob 儲存體的完整介紹，請參閱 [Azure Blob 基本概念](../storage-dotnet-how-to-use-blobs.md)和 [Azure Blob 服務](https://msdn.microsoft.com/library/azure/dd179376.aspx)。

上傳/下載資料之前，您必須知道 Azure 儲存體帳戶名稱和帳戶金鑰。如需關於取得此資訊的指示，請參閱[管理儲存體帳戶](../storage-create-storage-account.md)的＜作法：檢視、複製及重新產生儲存體存取金鑰＞一節。本文件假設您擁有 Azure 儲存體帳戶和對應的儲存體金鑰。


<a id="explorer"></a>
## 使用 Azure 儲存體總管 

Azure 儲存體總管是一個免費的 Windows 型工具，可用以檢查及更改 Azure 儲存體帳戶中的資料。您可以從 [Azure 儲存體總管](http://azurestorageexplorer.codeplex.com/)加以下載。下列步驟說明如何使用 Azure 儲存體總管上傳/下載資料。

1.  啟動 Azure 儲存體總管 
2.  如果您想要存取的儲存體帳戶尚未新增至 Azure 儲存體總管，按一下 [新增帳戶] 按鈕來新增帳戶。如果已經新增，可從 [--選取儲存體帳戶--] 下拉式清單中選取帳戶。![建立工作區][1] <br>
3. 輸入儲存體帳戶名稱和儲存體帳戶金鑰，然後按一下 [新增儲存體帳戶]。您可以新增多個儲存體帳戶，各個帳戶將會顯示於個別的索引標籤上。此儲存體帳戶下方的容器會顯示於左面板中。選取容器，以在右面板查看容器中的 Blob。
![建立工作區][2]
<br>
![建立工作區][3]
<br>
4. 按一下 [上傳] 按鈕來上傳資料。從檔案系統中選取一或多個要上傳的檔案，然後按一下 [開啟]，開始上傳檔案。
5. 選取對應容器中的 Blob，然後按一下 [下載] 按鈕來下載資料。

<a id="AzCopy"></a>
## 使用 AzCopy

AzCopy 是上傳和下載資料的命令列公用程式。

**警告**：如果您使用的電腦不是稍早在進階分析程序中設定的 VM，請使用以下安裝指示來安裝 AzCopy：[下載並安裝 AzCopy](../storage-use-azcopy.md#install)。

###將檔案上傳至 Blob或從 Blob 下載檔案的範例：

	# Uploading from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

> [AZURE.TIP]1.上傳檔案時，/S 將以遞迴方式上傳檔案。如果沒有這個參數，將不會上傳子目錄中的任何檔案。2.下載檔案時，/S 將以遞迴方式搜尋容器，直到下載了指定目錄及其子目錄中的所有檔案，或指定目錄及其子目錄中所有符合指定模式的所有檔案為止。3.您無法使用 /Source 參數來指定要下載的特定 Blob 檔案。若要下載特定檔案，請使用 /Pattern 參數指定要下載的 Blob 檔案名稱。/S 參數可用來讓 AzCopy 以遞迴方式尋找檔案名稱模式。若未提供模式參數，AzCopy 將下載該目錄中的所有檔案。

如需 AzCopy 的詳細使用方式，請參閱[開始使用 AzCopy 命令列公用程式](../storage-use-azcopy.md#install)。


<a id="PythonSDK"></a>
## 使用 Python

使用 Azure SDK 中提供的 Python API，您可以

- 建立容器
- 將 Blob 上傳至容器
- 下載 Blob
- 列出容器中的 Blob
- 刪除 Blob

本節說明如何列出、上傳和下載 Blob。如需 Python API 使用方式的詳細資料，請參閱[如何從 Python 使用 Blob 儲存體服務](../storage-python-how-to-use-blob-storage.md)。

> [AZURE.NOTE]如果您使用的電腦不是稍早在進階分析程序中設定的 VM，就需要先安裝 [Python Azure SDK](../python-how-to-install.md)，才能使用以下範例程式碼。

### 將資料上傳至 Blob

將下列程式碼片段新增至您希望以程式設計方式存取 Azure 儲存體的任何 Python 程式碼頂端附近：

	from azure.storage import BlobService

**BlobService** 物件讓您能使用容器及 blob。下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 BlobService 物件。使用您真正的帳戶和金鑰來取代帳戶名稱和帳戶金鑰。
	
	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

使用下列方法，將資料上傳至 Blob：
 
1. put_block_blob_from_path (從指定路徑上傳檔案的內容)
2. put_block_blob_from_file (從任何已經開啟的檔案/資料流上傳內容)
3. put_block_blob_from_bytes (上傳位元組陣列)
4. put_block_blob_from_text (使用指定的編碼方式上傳指定的文字值)
 
下列程式碼範例會將本機檔案上傳至容器：
	
	blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

下列程式碼範例會將本機目錄中的所有檔案 (不含目錄) 上傳至 Blob 儲存體：

	from azure.storage import BlobService
	from os import listdir
	from os.path import isfile, join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	# find all files in the LOCAL_DIRECT (excluding directory)
	local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]
	
	file_num = len(local_file_list)
	for i in range(file_num):
	    local_file = join(LOCAL_DIRECT, local_file_list[i])
	    blob_name = local_file_list[i]
	    try:
	        blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
	    except:
	        print "something wrong happened when uploading the data %s"%blob_name

### 從 Blob 下載資料

使用下列方法從 Blob 下載資料：1. get_blob_to_path 2. get_blob_to_file 3. get_blob_to_bytes 4. get_blob_to_text

這些方法可以在資料大小超過 64 MB 時執行必要的區塊化動作。

下列程式碼範例會將容器中 Blob 的內容下載至本機檔案：

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

下列程式碼範例會從容器下載所有 Blob。它會使用 list_blobs 來取得容器中可用的 Blob 清單，並將它們下載至本機目錄。

	from azure.storage import BlobService
	from os.path import join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	
	# List all blobs and download them one by one
	blobs = blob_service.list_blobs(CONTAINER_NAME)
	for blob in blobs:
	    local_file = join(LOCAL_DIRECT, blob.name)
	    try:
	        blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
	    except:
	        print "something wrong happened when downloading the data %s"%blob.name

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png
 

<!---HONumber=July15_HO1-->