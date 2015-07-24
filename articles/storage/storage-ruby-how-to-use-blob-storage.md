<properties 
	pageTitle="如何使用拼音的 Blob 儲存體 | Microsoft Azure" 
	description="了解如何使用 Azure Blob 服務來上傳、下載、列出及刪除 Blob 內容。範例以 Ruby 撰寫。" 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="tomfitz"/>


# 如何使用 Ruby 的 Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概觀

本指南將示範如何使用 Azure Blob 服務執行一般案例。這些範例使用 Ruby API 撰寫。所涵蓋的案例包括**上傳、列出、下載**及**刪除** Blob。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 Ruby 應用程式

建立 Ruby 應用程式。如需指示，請參閱[在 Azure 上建立 Ruby 應用程式](/develop/ruby/tutorials/web-app-with-linux-vm/) (英文)。

## 設定您的應用程式以存取儲存體

若要使用 Azure 儲存體，您需要下載並使用 Ruby azure 套件，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### 使用 RubyGems 來取得套件

1. 使用命令列介面，例如 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix)。

2. 在命令視窗中鍵入 "gem install azure" 以安裝 Gem 和相依性。

### 匯入封裝

使用您偏好的文字編輯器，將以下內容新增至您打算使用儲存體的 Ruby 檔案頂端：

	require "azure"

## 設定 Azure 儲存體連接

azure 模組會讀取環境變數 **AZURE_STORAGE_ACCOUNT** 及 **AZURE_STORAGE_ACCESS_KEY**，以取得連接 Azure 儲存體帳戶所需的資訊。如果尚未設定這些環境變數，您必須使用下列程式碼，在使用 **Azure::BlobService** 之前指定帳戶資訊：

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


若要取得這些值，請執行下列動作：

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com/)。
2. 瀏覽到您要使用的儲存體帳戶
3. 按一下導覽窗格底部的 [管理金鑰]。
4. 在快顯對話方塊中，您將會看到儲存體帳戶名稱、主要存取金鑰和次要存取金鑰。如需存取金鑰，您可以使用主要存取金鑰或次要存取金鑰。

## 作法：建立容器

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

**Azure::BlobService** 物件可讓您操作容器及 Blob。若要建立容器，請使用 **create_container()** 方法。

下列範例將建立容器或列印錯誤訊息 (若有的話)。

	azure_blob_service = Azure::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

如果您想讓容器中的檔案成為公用性質，可以設定容器的權限。

您可以只修改 <strong>create_container() </strong> 呼叫以傳遞 **:public_access_level** 選項：

	container = azure_blob_service.create_container("test-container", 
	  :public_access_level => "<public access level>")


**:public_access_level** 選項的有效值包括：

* **blob：**可指定容器和 blob 資料的完整公用讀取權限。用戶端可以透過匿名要求列舉容器內的 Blob，但無法列舉儲存體帳戶內的容器。

* **容器：**可指定 Blob 的公用讀取權限。您可以透過匿名要求讀取此容器內的 Blob 資料，但您無法使用容器資料。用戶端無法透過匿名要求列舉容器內的 Blob。

或者，您可以使用 **set_container_acl()** 方法指定公用存取等級，藉此修改容器的公用存取等級。
 
下列範例會將公用存取等級變更為 **container**：

	azure_blob_service.set_container_acl('test-container', "container")

## 作法：將 Blob 上傳到容器中

若要將內容上傳至 Blob，請使用 **create_block_blob()** 方法建立 Blob，使用檔案或字串作為 Blob 的內容。

下列程式碼將上傳檔案 **test.png** 作為容器中的新 Blob (名為 "image-blob")。

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## 作法：列出容器中的 Blob

若要列出容器，請使用 **list_containers()** 方法。若要列出容器內的 Blob，請使用 **list_blobs()** 方法。

這會輸出該帳戶所有容器中所有 Blob 的 URL。

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## 作法：下載 Blob

若要下載 Blob，請使用 **get_blob()** 方法以擷取內容。

下列範例示範使用 **get_blob()** 來下載 "image-blob" 的內容，並將它寫入本機檔案。

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## 作法：刪除 Blob
最後，若要刪除 Blob，請使用 **delete_blob()** 方法。下列範例示範如何刪除 Blob。

	azure_blob_service.delete_blob(container.name, "image-blob")

## 後續步驟

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

- 請參閱 MSDN 參考資料：[Azure 儲存體](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- 造訪 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/) (英文)。
- 請造訪 GitHub 上的 [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) 儲存機制 (英文)。
 

<!---HONumber=July15_HO2-->