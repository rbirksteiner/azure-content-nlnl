<properties 
	pageTitle="如何使用 Python 的資料表儲存體 | Microsoft Azure" 
	description="了解如何使用 Python 的資料表服務來建立和刪除資料表，以及插入、刪除和查詢資料表。" 
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
	ms.date="03/11/2015" 
	ms.author="huvalo"/>


# 如何使用 Python 的資料表儲存體

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## 概觀

本指南說明如何使用 Azure 資料表儲存體服務執行一般案例。相關範例是以 Python 撰寫，並使用 [Python Azure 封裝][]。所涵蓋的案例包括「建立和刪除資料表」、「在資料表中插入及查詢實體」。

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.NOTE]如果您需要安裝 Python 或 [Python Azure 封裝][]，請參閱 [Python 安裝指南](../python-how-to-install.md)。


## 如何建立資料表

**TableService** 物件可讓您操作資料表服務。下列程式碼將建立 **TableService** 物件。將下列內容新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 Python 檔案內的頂端附近：

	from azure.storage import TableService, Entity

下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 **TableService** 物件：將 'myaccount' 和 'mykey' 取代為真實的帳戶和金鑰。

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## 如何將實體新增至資料表

若要新增實體，請先建立定義實體屬性名稱和值的字典。請注意，對每個實體都必須指定 **PartitionKey** 及 **RowKey**。這些是實體的唯一識別碼，且其值的查詢速度比其他屬性快上許多。系統使用 **PartitionKey** 自動將資料表的實體分散在許多儲存體節點上。具有相同 **PartitionKey** 的實體會儲存在相同節點上。**RowKey** 是實體在其所屬資料分割內的唯一識別碼。

若要將實體新增至資料表，請將字典物件傳給 **insert_entity** 方法。

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

您也可以將 **Entity** 類別的執行個體傳給 **insert_entity** 方法。

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## 如何更新實體

此程式碼說明如何以更新版本取代舊版本的現有實體。

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

如果正在更新的實體不存在，則更新操作便會失敗。如果您想要儲存實體 (不論它是否已存在)，請使用 **insert_or_replace_entity**。在下列範例中，第一個呼叫將取代現有實體。第二個呼叫將插入新實體，因為資料表中沒有具有指定 **PartitionKey** 和 **RowKey** 的實體存在。

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## 如何變更一組實體

有時候批次提交多個操作是有意義的，可以確保伺服器會進行不可部分完成的處理。要達到此點，請在 **TableService** 上使用 **begin_batch** 方法，然後如常呼叫操作系列。當您真的想提交批次時，請呼叫 **commit_batch**。請注意，所有實體都必須位於相同的資料分割中，才能以批次方式進行變更。以下範例在一個批次中同時新增兩個實體。

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## 如何查詢實體

若要查詢資料表中的實體，請使用 **get_entity** 方法，傳遞 **PartitionKey** 和 **RowKey**。

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## 如何查詢一組實體

此範例會根據 **PartitionKey** 找到在西雅圖的所有工作。

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## 如何查詢實體屬性的子集

一項資料表查詢可以只擷取實體的少數屬性。這項稱為「投射」的技術可減少頻寬並提高查詢效能 (尤其是對大型實體而言)。使用 **select** 參數並傳遞您要帶到用戶端的屬性名稱。

下列程式碼中的查詢只會傳回資料表中各實體的說明。

*請注意，下列程式碼片段只適用於雲端儲存體服務，儲存體模擬器並不支援。*

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## 如何刪除實體

您可以使用實體的資料分割和資料列索引鍵來刪除實體。

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## 如何刪除資料表

下列程式碼會從儲存體帳戶刪除資料表。

	table_service.delete_table('tasktable')

## 後續步驟

了解資料表儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

-   請參閱 MSDN 參考資料：[儲存和存取在 Azure 中的資料][]
-   [請造訪 Azure 儲存體團隊部落格][]

[儲存和存取在 Azure 中的資料]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[請造訪 Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
[Python Azure 封裝]: https://pypi.python.org/pypi/azure
 

<!---HONumber=July15_HO2-->