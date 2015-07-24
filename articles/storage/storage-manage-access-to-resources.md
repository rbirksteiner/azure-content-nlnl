<properties 
	pageTitle="管理 Azure 儲存體資源的存取 | Microsoft Azure" 
	description="了解如何管理使用者存取 Azure 儲存體資源的方式。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="jdial" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="micurd;tamram"/>

# 管理 Azure 儲存體資源的存取

## 概觀

根據預設，只有儲存體帳戶的擁有者可以存取該帳戶內的儲存體資源。如果您的服務或應用程式需要將這些資源提供給其他用戶端使用，而不共用存取金鑰，則您有下列選項來允許存取：

- 您可以設定容器的權限，以允許對該容器及其 Blob 進行匿名讀取存取。這不適用於資料表或佇列。

- 您可以透過共用存取簽章公開資源，讓您藉由指定資源的可用時間間隔以及用戶端將擁有它的權限，為容器、Blob、資料表或佇列資源委派有限的存取權。

- 您可以使用預存的存取原則，來管理容器或其 Blob、佇列或資料表的共用存取簽章。預存的存取原則可提供您控制共用存取簽章的其他量值，而且也提供了直接撤銷它們的方式。

## 限制容器和 Blob 的存取權

根據預設，可能只有儲存體帳戶的擁有者能存取容器及其內部的任何 Blob。若要為匿名使用者授與容器及其 Blob 的讀取權限，您可以設定容器權限以允許公用存取。匿名使用者可以讀取可公開存取之容器內的 Blob，而不需驗證要求。

容器會提供下列選項來管理容器存取：

- **完整的公開讀取權限：**可以透過匿名要求讀取容器和 Blob 資料。用戶端可以透過匿名要求列舉容器內的 Blob，但無法列舉儲存體帳戶內的容器。

- **僅對 Blob 有公開讀取權限：**您可以透過匿名要求讀取此容器內的 Blob 資料，但您無法使用容器資料。用戶端無法透過匿名要求列舉容器內的 Blob。

- **沒有公開讀取權限：**只有帳戶擁有者可以讀取容器和 Blob 資料。

>[AZURE.NOTE]如果您的服務要求您對 Blob 資源執行更細微的控制，或者您想要提供讀取作業以外的作業權限，就可以使用共用存取簽章來讓使用者能夠存取資源。

### 為匿名使用者提供的功能
下表顯示當容器的 ACL 設為允許公用存取時，匿名使用者可能會呼叫哪些作業。

| REST 作業 | 具有完整公開讀取權限 | 僅對 Blob 有公開讀取權限 |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| 列出容器 | 只有擁有者 | 只有擁有者 |
| 建立容器 | 只有擁有者 | 只有擁有者 |
| 取得容器屬性 | 全部 | 只有擁有者 |
| 取得容器中繼資料 | 全部 | 只有擁有者 |
| 設定容器中繼資料 | 只有擁有者 | 只有擁有者 |
| 取得容器 ACL | 只有擁有者 | 只有擁有者 |
| 設定容器 ACL | 只有擁有者 | 只有擁有者 |
| 刪除容器 | 只有擁有者 | 只有擁有者 |
| 列出 Blob | 全部 | 只有擁有者 |
| 放置 Blob | 只有擁有者 | 只有擁有者 |
| 取得 Blob | 全部 | 全部 |
| 取得 Blob 屬性 | 全部 | 全部 |
| 設定 Blob 屬性 | 只有擁有者 | 只有擁有者 |
| 取得 Blob 中繼資料 | 全部 | 全部 |
| 設定 Blob 中繼資料 | 只有擁有者 | 只有擁有者 |
| 放置區塊 | 只有擁有者 | 只有擁有者 |
| 取得區塊清單 (僅限認可區塊) | 全部 | 全部 |
| 取得區塊清單 (僅限未認可的區塊或所有區塊) | 只有擁有者 | 只有擁有者 |
| 放置區塊清單 | 只有擁有者 | 只有擁有者 |
| 刪除 Blob | 只有擁有者 | 只有擁有者 |
| 複製 Blob | 只有擁有者 | 只有擁有者 |
| 快照 Blob | 只有擁有者 | 只有擁有者 |
| 租用 Blob | 只有擁有者 | 只有擁有者 |
| 放置頁面 | 只有擁有者 | 只有擁有者 |
| 取得頁面範圍 | 全部 | 全部 |

## 建立和使用共用存取簽章
共用存取簽章是一個 URI，可針對特定的時間間隔，授與容器、Blob、佇列和資料表有限的存取權限。藉由為用戶端提供共用存取簽章，您可以讓他們存取您儲存體帳戶中的資源，而不需與他們共用您的帳戶金鑰。

>[AZURE.NOTE]如需共用存取簽章的深入概念性概觀和教學課程，請參閱[共用存取簽章](storage-dotnet-shared-access-signature-part-1.md)。

支援使用共用存取簽章的作業包含：

- 讀取和寫入分頁或區塊 Blob 內容、區塊清單、屬性和中繼資料

- 刪除、租用及建立 Blob 的快照集

- 列出容器中的 Blob

- 加入、移除、更新和刪除佇列訊息 (在儲存體用戶端程式庫 2.0 版及更新版本中)

- 取得佇列中繼資料，包括訊息計數 (在儲存體用戶端程式庫 2.0 版及更新版本中)

- 查詢、加入、更新、刪除和更新插入資料表實體 (在儲存體用戶端程式庫 2.0 版及更新版本中)

共用存取簽章 URI 查詢參數可合併所需的所有資訊，為儲存體資源授與控制的存取權。URI 查詢參數會指定共用存取簽章的有效時間間隔、其授與的權限、可供使用的資源，以及儲存體服務應該用來驗證要求的簽章。

此外，共用存取簽章 URI 可參考預存的存取原則，以便在一組簽章之外額外提供一層控制，包括視需要修改或撤銷資源存取權限的能力。

如需共用存取簽章的 URI 格式相關資訊，請參閱[使用共用存取簽章委派存取](https://msdn.microsoft.com/library/ee395415.aspx)。

### 安全使用共用存取簽章
共用存取簽章會為 URI 之授與權限所指定的資源授與存取權限。您應該一律使用 HTTPS 來建構共用存取簽章 URI。使用 HTTP 搭配共用存取簽章，會讓您的儲存體帳戶遭到惡意使用。

如果共用存取簽章所授與的存取權限不是供一般大眾使用，則應該使用最少可能的權限來建構它。此外，共用存取簽章應該透過安全連線安全地散佈給用戶端、應該基於撤銷因素而與預存的存取原則產生關聯，而且應該為簽章指定最短的可能存留時間。

>[AZURE.NOTE]共用存取簽章 URI 會與用來建立簽章的帳戶金鑰，以及相關聯的預存的存取原則 (如果有的話) 產生關聯。如果未指定任何預存的存取原則，則撤銷共用存取簽章的唯一方式是變更帳戶金鑰。

### 建立共用存取簽章
下列程式碼範例會在容器上建立存取原則，然後產生容器的共用存取簽章。然後就能將此共用存取簽章提供給用戶端：

    // The connection string for the storage account.  Modify for your account.
    string storageConnectionString =
       "DefaultEndpointsProtocol=https;" +
       "AccountName=myaccount;" +
       "AccountKey=<account-key>";
    
    // As an alternative, you can retrieve storage account information from an app.config file. 
    // This is one way to store and retrieve a connection string if you are 
    // writing an application that will run locally, rather than in Microsoft Azure.
    
    // string storageConnectionString = ConfigurationManager.AppSettings["StorageAccountConnectionString"];
    
    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Create blob container permissions, consisting of a shared access policy 
    // and a public access setting. 
    BlobContainerPermissions blobPermissions = new BlobContainerPermissions();
    
    // The shared access policy provides 
    // read/write access to the container for 10 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don’t set start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
       Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
    });
    
    // The public access setting explicitly specifies that 
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Set the permission policy on the container.
    container.SetPermissions(blobPermissions);
    
    // Get the shared access signature to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

### 使用共用存取簽章
收到共用存取簽章的用戶端可以從他們的程式碼使用它，來建構類型 [StorageCredentials](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.auth.storagecredentials.aspx) 的物件。這些認證接著可用來建構 [CloudStorageAccount](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.cloudstorageaccount.aspx) 或 [CloudBlobClient](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx) 物件以使用資源，如此範例所示：

    Uri blobUri = new Uri("https://myaccount.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Upload the blob. 
    // If the blob does not yet exist, it will be created. 
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Test\myblob.txt"))
    {
    blob.UploadFromStream(fileStream);
    }

## 使用預存的存取原則
預存的存取原則可為伺服器端上的共用存取簽章提供一層額外控制。建立預存的存取原則，可用來將共用存取簽章分組，並為受限於該原則的簽章提供額外的限制。您可以使用預存的存取原則來變更開始時間、到期時間或簽章的權限，或者在發出之後將它撤銷。

預存的存取原則可讓您更有效控制已發行的共用存取簽章。您可以在儲存於 Blob、容器、佇列或正在共用的資料表上的預存存取原則內指定這些參數，而不是在 URL 上指定簽章的存留期和權限。若要針對一或多個簽章變更這些參數，您可以修改預存的存取原則，而不是重新發出簽章。您也可以修改預存的存取原則，快速撤銷簽章。

例如，假設您已發出與預存的存取原則相關聯的共用存取簽章。如果您已在預存的存取原則內指定到期時間，則可修改存取原則來延長簽章的存留期，而不必重新發出新的簽章。

最佳做法建議針對您發出共用存取簽章的任何已簽署資源指定預存的存取原則，因為預存的原則可在發出簽章之後用來修改或撤銷該簽章。如果您未指定預存的原則，則建議您限制簽章的存留期，以減少對儲存體帳戶資源的任何風險。

### 將共用存取簽章關聯到預存的存取原則
預存存取原則的名稱長度最多包含 64 個字元，其在容器、佇列或資料表內是唯一的。若要將共用存取簽章關聯到預存的存取原則，您需要在建立共用存取簽章時指定這個識別項。在共用存取簽章 URI 上，*signedidentifier* 欄位會指定預存存取原則的識別碼。

容器、佇列或資料表最多可包含 5 個預存的存取原則。每個原則都可供任意數目的共用存取簽章使用。

>[AZURE.NOTE]當您在容器、佇列或資料表上建立預存的存取原則時，可能需要 30 秒的時間才會生效。在此間隔期間，與預存的存取原則相關聯的共用存取簽章將會失敗並顯示狀態碼 403 (禁止)，直到該存取原則變成作用中為止。

### 指定共用存取原則的存取原則參數
預存的存取原則可以為與其相關聯的簽章指定下列存取原則參數：

- 開始時間

- 過期時間

- 權限

根據您要如何控制儲存體資源的存取權而定，您可以在預存的存取原則內指定所有的這些參數，並在共用存取簽章的 URL 中省略它們。這樣做可讓您隨時修改相關聯的簽章行為，以及將它撤銷。或者，您可以在預存的存取原則內指定一或多個存取原則參數，並在 URL 上指定其他參數。最後，您可以在 URL 上指定所有參數。在此情況下，您可以使用預存的存取原則來撤銷簽章，但不是修改其行為。

共用存取簽章和預存的存取原則必須共同包含驗證簽章所需的所有欄位。如果遺漏任何必要欄位，要求將會失敗並顯示狀態碼 403 (禁止)。同樣地，如果同時在共用存取簽章 URL 和預存的存取原則中指定某個欄位，要求將會失敗並顯示狀態碼 403 (不正確的要求)。如需組成簽章的欄位詳細資訊，請參閱＜建立和使用共用存取簽章＞。

### 修改或撤銷預存的存取原則

若要撤銷使用相同預存存取原則之共用存取簽章的存取權，可使用不包含該原則名稱的新清單來覆寫預存的原則清單，從儲存體資源中移除預存的原則。若要變更預存存取原則的存取設定，可使用包含相同名稱且具有新存取控制詳細資料之原則的新清單來覆寫預存的原則清單。

## 另請參閱

- [Azure 儲存體服務的驗證](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [共用存取簽章：了解 SAS 模型](storage-dotnet-shared-access-signature-part-1.md)
- [使用共用存取簽章來委派存取權](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=July15_HO1-->