<properties 
	pageTitle="如何使用拼音的資料表儲存體 | Microsoft Azure" 
	description="如何在 Azure 中使用資料表儲存體服務。程式碼範例以 Ruby API 撰寫。" 
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
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# 如何使用 Ruby 的資料表儲存體

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## 概觀

本指南示範如何使用 Microsoft Azure 資料表服務執行一般案例。這些範例使用 Ruby API 撰寫。所涵蓋的案例包括「建立和刪除資料表」、「在資料表中插入及查詢實體」。

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 Ruby 應用程式

建立 Ruby 應用程式。如需指示，請參閱[在 Azure 上建立 Ruby 應用程式](/develop/ruby/tutorials/web-app-with-linux-vm/) (英文)。

## 設定您的應用程式以存取儲存體

若要使用 Azure 儲存體，您需要下載並使用 Ruby azure 套件，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### 使用 RubyGems 來取得套件

1. 使用命令列介面，例如 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix)。

2. 在命令視窗中鍵入 **gem install azure** 以安裝 Gem 和相依性。

### 匯入封裝

使用您偏好的文字編輯器，將以下內容新增至您打算使用儲存體的 Ruby 檔案頂端：

	require "azure"

## 設定 Azure 儲存體連接

azure 模組會讀取環境變數 **AZURE_STORAGE_ACCOUNT** 及 **AZURE_STORAGE_ACCESS_KEY**，以取得連接 Azure 儲存體帳戶所需的資訊。如果尚未設定這些環境變數，您必須使用下列程式碼，在使用 **Azure::TableService** 之前指定帳戶資訊：

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"

若要取得這些值，請執行下列動作：

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com/)。

2. 瀏覽到您要使用的儲存體帳戶。

3. 按一下導覽窗格底部的 [管理金鑰]。

4. 在快顯對話方塊中，您將會看到儲存體帳戶名稱、主要存取金鑰和次要存取金鑰。如需存取金鑰，您可使用主要存取金鑰或次要存取金鑰。

## 如何建立資料表

**Azure::TableService** 物件可讓您操作資料表及實體。若要建立資料表，請使用 **create_table()** 方法。下列範例將建立資料表或列印錯誤訊息 (若有的話)。

	azure_table_service = Azure::TableService.new
	begin
	  azure_table_service.create_table("testtable")
	rescue
	  puts $!
	end

## 如何將實體新增至資料表

若要新增實體，請先建立定義實體屬性的雜湊物件。請注意，您必須對每個實體指定 **PartitionKey** 和 **RowKey**。這些是實體的唯一識別碼，且其值的查詢速度比其他屬性快上許多。Azure 儲存體服務使用 **PartitionKey** 自動將資料表的實體分散在許多儲存體節點上。具有相同 **PartitionKey** 的實體會儲存在相同節點上。**RowKey** 是實體在其所屬資料分割內的唯一識別碼。

	entity = { "content" => "test entity", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.insert_entity("testtable", entity)

## 作法：更新實體

有多種方法可以用來更新現有的實體：

* **update_entity()：**透過取代現有實體來進行更新。
* **merge_entity()：**透過將新的屬性值合併到現有實體來更新現有實體。
* **insert_or_merge_entity()：**透過取代現有實體來進行更新。如果實體不存在，將會插入新的實體：
* **insert_or_replace_entity()：**透過將新的屬性值合併到現有實體來更新現有實體。如果實體不存在，將會插入新的實體。

下列範例示範使用 **update_entity()** 來更新實體：

	entity = { "content" => "test entity with updated content", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.update_entity("testtable", entity)

使用 **update_entity()** 及 **merge_entity()** 時，如果正在更新的實體不存在，則更新操作會失敗。因此，如果您要儲存一個實體，而不管它是否已存在，您應該改用 **insert_or_replace_entity()** 或 **insert_or_merge_entity()**。

## 作法：使用實體群組

有時候批次提交多個操作是有意義的，可以確保伺服器會進行不可部分完成的處理。若要達到此目的，您首先必須建立 **Batch** 物件，然後在 **TableService** 上使用 **execute_batch()** 方法。下列範例示範在一個批次中，提交具備 RowKey 2 和 3 的兩個實體。請注意，它僅適用於具備相同 PartitionKey 的實體。

	azure_table_service = Azure::TableService.new
	batch = Azure::Storage::Table::Batch.new("testtable", 
	  "test-partition-key") do
	  insert "2", { "content" => "new content 2" }
	  insert "3", { "content" => "new content 3" }
	end
	results = azure_table_service.execute_batch(batch)

## 作法：查詢實體

若要查詢資料表中的實體，請使用 **get_entity()** 方法，傳遞資料表名稱、**PartitionKey** 和 **RowKey**。

	result = azure_table_service.get_entity("testtable", "test-partition-key", 
	  "1")

## 作法：查詢一組實體

若要查詢資料表中的實體集合，請建立查詢雜湊物件並使用 **query_entities()** 方法。下列範例示範取得具備相同 **PartitionKey** 的所有實體：

	query = { :filter => "PartitionKey eq 'test-partition-key'" }
	result, token = azure_table_service.query_entities("testtable", query)

**請注意**，如果單一查詢的結果集因為太大而無法傳回，則會傳回接續權杖，方便您用來擷取後續頁面。

## 作法：查詢實體屬性的子集

一項資料表查詢可以只擷取實體的少數屬性。這項稱為「投射」的技術可減少頻寬並提高查詢效能 (尤其是對大型實體而言)。使用 select 子句並傳遞您要帶到用戶端的屬性名稱。

	query = { :filter => "PartitionKey eq 'test-partition-key'", 
	  :select => ["content"] }
	result, token = azure_table_service.query_entities("testtable", query)

## 作法：刪除實體

若要刪除實體，請使用 **delete_entity()** 方法。您必須傳入資料表名稱，其中包含實體、實體的 PartitionKey 和 RowKey。

		azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## 作法：刪除資料表

若要刪除資料表，請使用 **delete_table()** 方法並傳入要刪除的資料表名稱。

		azure_table_service.delete_table("testtable")

## 後續步驟

了解資料表儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

- 請參閱 MSDN 參考資料：[Azure 儲存體](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- 造訪 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/) (英文)。
- 請造訪 GitHub 上的 [Azure SDK for Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) 儲存機制 (英文)。
 

<!---HONumber=July15_HO2-->