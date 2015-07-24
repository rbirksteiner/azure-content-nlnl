<properties 
	pageTitle="建立 Blob 的快照集" 
	description="建立 Azure 儲存體 Blob 快照集的做法指南" 
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
	ms.date="05/27/2015" 
	ms.author="tamram"/>

# 建立 Blob 快照集

## 概觀

快照集是在某個點時間取得的唯讀 Blob 版本。快照集對於備份 Blob 非常有用。一旦建立快照集後，您便可以加以讀取、複製或刪除，但您無法修改快照集。

Blob 的快照集名稱與從中擷取該快照集的基底 Blob 名稱相同，其中並附加了 [日期時間] 值，以表示擷取快照當時的時間。例如，如果分頁 Blob URI 為 `http://storagesample.core.blob.windows.net/mydrives/myvhd`，則快照集 URI 將會類似 `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`。Blob 的所有快照集會共用其 URI，而且只能透過附加的 [日期時間] 值進行區分。

Blob 可包含任意數目的快照集。系統會保存快照集，直到您將它們明確刪除為止。請注意，快照集必須要有其來源 Blob 才能存在。您可以列舉與 Blob 相關聯的快照集，以追蹤目前的快照集。

當您建立 Blob 的快照集時，Blob 的系統屬性都會使用相同值複製到快照集中。

任何與基底 Blob 相關聯的租用不會影響快照集。您無法取得快照集上的租用。

## 複製快照集 

涉及 Blob 和快照集的複製作業會遵循下列規則：

- 您可以將快照集複製到其基底 Blob 之上。藉由將快照集升級到基底 Blob 的位置，您可以還原舊版的 Blob。快照集會被保留，但基底 Blob 會遭到快照集的可寫入複本覆寫。

- 您可以將快照集複製到不同名稱的目的地 Blob。所產生的目的地 Blob 會是一個可寫入的 Blob，而不是快照集。

- 複製來源 Blob 時，不會將來源 Blob 的任何快照集複製到目的地。使用某個複本覆寫目的地 Blob 時，任何與目的地 Blob 覆寫期間相關聯的快照集都會讓名稱保持不變。

- 當您建立區塊 Blob 的快照集時，該 Blob 的認可區塊清單也會複製到快照集。系統不會複製任何未認可的區塊。

## 指定存取條件 

您可以指定存取條件，如此一來，只有在符合條件時，才會建立快照集。若要指定存取條件，請使用 AccessCondition 屬性。如果不符合指定的條件，就不會建立快照集，而且 Blob 服務會傳回狀態碼 HTTPStatusCode.PreconditionFailed。

## 刪除快照集 

除非快照集也會一併刪除，否則無法刪除具有這些快照集的 Blob。您可以個別刪除快照集，或告知儲存體服務在刪除來源 Blob 時刪除所有快照集。如果您嘗試刪除仍具有快照集的 Blob，您將會收到錯誤訊息。

## 快照集與高階儲存體
搭配使用快照集與高階儲存體需遵循下列規則：

- 高階儲存體帳戶中每個分頁 Blob 的快照集數目限制為 100 個。如果超出該限制，此快照集 Blob 作業就會傳回錯誤碼 409 (**SnapshotCountExceeded**)。

- 高階儲存體帳戶中分頁 Blob 的快照集可能每十分鐘擷取一次。如果超出該頻率，此快照集 Blob 作業就會傳回錯誤碼 409 (**SnaphotOperationRateExceeded**)。

- 不支援透過取得 Blob 來讀取高階儲存體帳戶中分頁 Blob 的快照集。在高階儲存體帳戶的快照集上呼叫 [取得 Blob] 會傳回錯誤碼 400 (**InvalidOperation**)。不過，支援針對快照集呼叫取得 Blob 屬性和取得 Blob 中繼資料。

- 若要讀取快照集，您可以使用複製 Blob 作業，將快照集複製到帳戶中的其他分頁 Blob。複製作業的目的地 Blob 不可以包含任何現有的快照集。如果目的地 Blob 具有快照集，則複製 Blob 會傳回錯誤碼 409 (**SnapshotsPresent**)。

## 傳回快照集的絕對 URI 

這個 C# 程式碼範例會建立新的快照集，並寫出主要位置的絕對 URI。

    //Create the blob service client object.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## 了解快照集產生費用的方式

建立快照集 (即 Blob 的唯讀複本) 可能會為您的帳戶產生額外的資料儲存體費用。設計您的應用程式時，請務必留意可能產生這些費用的方式，以便將不必要的成本降至最低。

### 重要的計費考量

下列清單包含建立快照集時要考量的重點。

- 無論唯一的區塊或分頁是在 Blob 或快照集中，都會產生費用。在您更新快照集所依據的 Blob 之前，您的帳戶不會針對與該 Blob 相關聯的快照集產生額外費用。一旦您更新基底 Blob 之後，它就會與其快照集分離，而系統將針對每個 Blob 或快照集中唯一的區塊或分頁向您收費。

- 當您取代區塊 Blob 內的某個區塊時，後續即會將該區塊視為唯一區塊進行收費。即使該區塊的區塊識別碼和資料與其在快照集中擁有的相同，也是如此。一旦再次認可該區塊之後，其就會與它在任何快照集的對應項目分離，而您將需支付其資料的費用。這同樣適用分頁 Blob 中以相同資料更新的頁面。

- 藉由呼叫 UploadFile、UploadText、UploadStream 或 UploadByteArray 方法來取代區塊 Blob，會取代該 Blob 中的所有區塊。如果您的快照集與該 Blob 相關聯，基底 Blob 和快照集中的所有區塊現在都會分離出來，而您將針對這兩個 Blob 的所有區塊支付費用。即使基底 Blob 和快照集中的資料保持一致，也是如此。

- Azure Blob 服務未提供方法來判斷兩個區塊是否包含相同資料。已上傳且認可的每個區塊都會被視為唯一，即使它具有相同資料和相同的區塊識別碼也一樣。由於費用是針對唯一區塊而產生，因此，請務必考量更新含有快照集的 Blob 將產生額外的唯一區塊及額外費用。

> [AZURE.NOTE]最佳做法是要求您謹慎管理快照集，以避免產生額外費用。建議您以下列方式管理快照集：

> - 每當您更新 Blob 時，刪除並重新建立與該 Blob 相關聯的快照集，除非您的應用程式設計為需要維護快照集，否則即使您正以相同資料進行更新也一樣。藉由刪除並重新建立 Blob 的快照集，讓您可以確保該 Blob 和快照集不會分離開來。

> - 如果您正在維護 Blob 的快照集，請避免呼叫 UploadFile、UploadText、UploadStream 或 UploadByteArray 來更新 Blob，因為這些方法會取代 Blob 中的所有區塊。相反地，請使用 PutBlock 和 PutBlockList 方法來更新最少的可能區塊數目。


### 快照集計費案例


下列案例示範如何針對區塊 Blob 及其快照集產生費用。

在案例 1 中，基底 Blob 在擷取快照之後尚未更新，因此只會針對唯一區塊 1、2 和 3 產生費用。

![Azure 儲存體資源](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

在案例 2 中，基底 Blob 已更新，但快照集並未更新。區塊 3 已更新，而且即使它包含相同資料及相同的識別碼，它還是與快照集中的區塊 3 不一樣。因此，此帳戶必須支付四個區塊的費用。

![Azure 儲存體資源](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

在案例 3 中，基底 Blob 已更新，但快照集並未更新。區塊 3 已使用基底 Blob 中的區塊 4 來取代，但快照集仍然反映區塊 3。因此，此帳戶必須支付四個區塊的費用。
 
![Azure 儲存體資源](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

在案例 4 中，基底 Blob 已完全更新，且未包含它的任何原始區塊。因此，此帳戶必須支付所有八個唯一區塊的費用。如果您使用像是 UploadFile、UploadText、UploadFromStream 或 UploadByteArray 的更新方法，就會發生這個案例，因為這些方法會取代 Blob 的所有內容。

![Azure 儲存體資源](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

<!---HONumber=July15_HO1-->