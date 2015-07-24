<properties 
	pageTitle="如何使用 Python 中的 Blob 儲存體 | Microsoft Azure" 
	description="了解如何使用 Python 的 Azure Blob 服務上傳、列出、下載及刪除 blob。" 
	services="storage" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="huvalo"/>

# 如何使用 Python 的 Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概觀

本指南將示範如何使用 Azure Blob 儲存服務執行一般案例。相關範例是以 Python 所撰寫，並使用 [Python Azure 封裝][]。所涵蓋的案例包括「上傳」、「列出」、「下載」及「刪除」Blob。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 作法：建立容器

> [AZURE.NOTE]如果您需要安裝 Python 或 [Python Azure 封裝][]，請參閱 [Python 安裝指南](../python-how-to-install.md)。

**BlobService** 物件讓您能使用容器及 blob。下列程式碼會建立 **BlobService** 物件。將下列內容新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 Python 檔案內的頂端附近：

	from azure.storage import BlobService

下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 **BlobService** 物件：將 'myaccount' 和 'mykey' 取代為真實的帳戶和金鑰。

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

如果容器不存在，您可以使用 **BlobService** 物件建立容器：

	blob_service.create_container('mycontainer')

根據預設，新容器屬私人性質，您必須指定儲存體存取金鑰 (如上面所做過的) 才能從此容器下載 blob。若要讓所有人都能使用容器中的檔案，您可以使用下列程式碼建立容器，並傳遞公用存取等級：

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

或者，您也可以在建立容器之後使用下列程式碼修改它：

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

做此變更之後，網際網路上的任何人都可以看到公用容器中的 Blob，但只有您能修改或刪除它們。

## 作法：將 Blob 上傳到容器中

若要將資料上傳至 Blob，請使用 **put_block_blob_from_path**、**put_block_blob_from_file**、**put_block_blob_from_bytes** 或 **put_block_blob_from_text** 方法。這些是高階方法，可在資料大小超過 64 MB 時執行必要的區塊化動作。

**put_block_blob_from_path** 會從指定的路徑上傳檔案的內容，**put_block_blob_from_file** 會從已開啟的檔案/串流上傳內容，**put_block_blob_from_bytes** 會上傳位元組陣列，**put_block_blob_from_text** 會使用指定的編碼 (預設為 UTF-8) 上傳指定的文字值。

下列範例會將 **sunset.png** 檔案的內容上傳至 **myblob** blob 中。

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## 作法：列出容器中的 Blob

若要列出容器中的 Blob，請搭配使用 **list_blobs** 方法與 **for** 迴圈，顯示容器中每個 Blob 的名稱。下列程式碼將容器中每個 Blob 的 **name** 和 **url** 輸出到主控台。

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## 作法：下載 Blob

若要從 Blob 下載資料，請使用 **get_blob_to_path**、**get_blob_to_file**、**get_blob_to_bytes** 或 **get_blob_to_text**。這些是高階方法，可在資料大小超過 64 MB 時執行必要的區塊化動作。

下列範例示範如何使用 **get_blob_to_path** 下載 **myblob** blob 的內容，並將其儲存至 **out-sunset.png** 檔案：

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## 作法：刪除 Blob

最後，若要刪除 Blob，請呼叫 **delete_blob**。

	blob_service.delete_blob('mycontainer', 'myblob') 

## 後續步驟

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

-   請參閱 MSDN 參考資料：[儲存和存取在 Azure 中的資料][]
-   造訪 [Azure 儲存體團隊部落格][] (英文)。

[儲存和存取在 Azure 中的資料]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
[Python Azure 封裝]: https://pypi.python.org/pypi/azure
 

<!---HONumber=July15_HO2-->