<properties 
	pageTitle="開始使用 Microsoft Azure 儲存體的用戶端加密 (預覽) | Microsoft Azure" 
	description="適用於 .NET 的 Azure 儲存體用戶端程式庫預覽提供對於用戶端加密和與 Azure 金鑰保存庫整合的支援。用戶端加密為您的 Azure 儲存體應用程式提供最大安全性，因為您的存取金鑰永遠不會讓服務使用。用戶端加密適用於 Blob、佇列和資料表。" 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="tamram"/>


# 開始使用 Microsoft Azure 儲存體的用戶端加密 (預覽)

## 概觀

歡迎使用[適用於 .NET 的新 Azure 儲存體用戶端程式庫預覽](https://www.nuget.org/packages/WindowsAzure.Storage/4.4.1-preview)。此預覽程式庫包含新功能，可協助開發人員在將用戶端應用程式內的資料上傳至 Azure 儲存體之前，對資料進行加密，以及在下載時解密資料。預覽程式庫也針對儲存體帳戶金鑰管理，支援與 Azure [金鑰保存庫](http://azure.microsoft.com/services/key-vault/)的整合。

## 透過信封技術加密和解密

加密和解密的程序採用信封技術。

### 透過信封技術加密

透過信封技術加密的運作方式如下：

1. Azure 儲存體用戶端程式庫會產生內容加密金鑰 (CEK)，這是使用一次的對稱金鑰。
2. 使用此 CEK 加密使用者資料。
3. 然後使用金鑰加密金鑰 (KEK) 包裝 (加密) CEK。KEK 由金鑰識別碼所識別，可以是非對稱金鑰組或對稱金鑰，且可以在本機管理或儲存在 Azure 金鑰保存庫中。 
	
	儲存體用戶端程式庫本身永遠沒有 KEK 的存取權。程式庫會叫用金鑰保存庫所提供的金鑰包裝演算法。如有需要，使用者可以選擇使用自訂提供者來包裝/取消包裝金鑰。

4. 然後，將加密的資料上傳至 Azure 儲存體服務。包裝的金鑰及一些其他加密中繼資料會儲存為中繼資料 (在 blob 上)，或插補加密的資料 (訊息佇列和資料表實體)。

### 透過信封技術解密

透過信封技術解密的運作方式如下：

1. 用戶端程式庫假設使用者在本機或 Azure 金鑰保存庫中管理金鑰加密金鑰 (KEK)。使用者不必知道用於加密的特定金鑰。相反地，可以設定並使用金鑰解析程式，將不同的金鑰識別碼解析成金鑰。
2. 用戶端程式庫會下載加密的資料，以及儲存在服務上的任何加密資料。
3. 然後，使用金鑰加密金鑰 (KEK) 將已包裝的內容加密金鑰 (CEK) 解除包裝 (解密)。同樣地，用戶端程式庫在此沒有 KEK 的存取權。它只會叫用自訂或金鑰保存庫提供者的解除包裝演算法。
4. 然後，使用內容加密金鑰 (CEK) 解密已加密的使用者資料。

## 加密機制

儲存體用戶端程式庫會使用 [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 來加密使用者資料。具體來說，就是[加密區塊鏈結 (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) 模式搭配 AES。每個服務的運作方式稍有不同，我們將在這裡討論每個服務。

### Blob

在預覽版本中，用戶端程式庫僅支援加密整個 blob。明確地說，當使用者使用 **UploadFrom*** 方法或 **BlobWriteStream** 時，就支援加密。在下載方面，完整和範圍下載都支援。

在加密期間，用戶端程式庫會產生 16 位元組的隨機初始化向量 (IV)，以及 32 位元組的隨機內容加密金鑰 (CEK)，並使用這項資訊執行 blob 資料的信封加密。然後，已包裝的 CEK 和一些其他加密中繼資料會儲存為 blob 中繼資料，並連同加密的 blob 一起儲存在服務上。

> [AZURE.WARNING]如果您要為 blob 編輯或上傳您自己的中繼資料，則必須確定保留此中繼資料。如果您上傳新的中繼資料，但缺少此中繼資料，包裝的 CEK、IV 和其他中繼資料將會遺失，而且永遠無法再擷取 blob 內容。

下載已加密的 blob 牽涉到使用 **DownloadTo***/**BlobReadStream** 便利方法擷取整個 blob 的內容。包裝的 CEK 會解除包裝，並與 IV (在此情況下儲存為 blob 中繼資料) 一起用來傳回解密的資料給使用者。

從加密的 blob 中下載任意範圍 (**DownloadRange*** 方法) 牽涉到調整使用者提供的範圍，以取得少量的額外資料，此資料可用來成功解密所要求的範圍。

所有 blob 類型 (區塊 blob 和分頁 blob) 都可使用這個機制來加密/解密。

### 佇列

因為佇列訊息可以是任何格式，用戶端程式庫會定義自訂的格式，其中包含訊息文字中的初始化向量 (IV) 和加密的內容加密金鑰 (CEK)。

在加密期間，用戶端程式庫會產生 16 位元組的隨機 IV，以及 32 位元組的隨機 CEK，並使用這項資訊執行佇列訊息文字的信封加密。然後，已包裝的 CEK 和一些其他加密中繼資料會加入至已加密的佇列訊息。這個修改過的訊息 (如下所示) 會儲存在服務上。

	<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

在解密期間，從佇列訊息中擷取已包裝的金鑰，並解除包裝。IV 也會從佇列訊息中擷取，並與未包裝的金鑰一起用來解密佇列訊息資料。請注意，加密中繼資料很小 (小於 500 位元組)，雖然計入佇列訊息的 64KB 限制內，但影響仍在可掌控的範圍內。

### 資料表

在預覽版本中，用戶端程式庫支援在插入和取代作業中加密實體屬性。

>[AZURE.NOTE]目前不支援合併。因為屬性子集先前可能是使用不同的金鑰加密，直接合併新的屬性並更新中繼資料會導致資料遺失。合併可能需要額外的服務呼叫來從服務讀取預先存在的實體，或在每個屬性上都使用新的金鑰，兩者都不利用於效能。

資料表資料加密的運作方式如下：

1. 使用者指定應該加密的屬性。
2. 用戶端程式庫為每一個實體產生 16 位元組的隨機初始化向量 (IV)，以及 32 位元組的隨機內容加密金鑰 (CEK)，並在應該加密的個別屬性上，為每個屬性衍生新的 IV 來執行信封加密。
3. 然後，已包裝的 CEK 和一些其他加密中繼資料會儲存成額外兩個保留的屬性。第一個保留的屬性 (_ClientEncryptionMetadata1) 是字串屬性，保存 IV、版本和已包裝的金鑰的相關資訊。另一個保留的屬性 (_ClientEncryptionMetadata2) 是二進位屬性，保存已加密的屬性的相關資訊。
4. 由於加密需要這些額外保留的屬性，使用者現在可能只有 250 個自訂屬性，而不是 252 個。實體的總大小必須小於 1MB。

請注意，只有字串屬性可以加密。如果有其他類型的屬性需要加密，則必須轉換成字串。

對於資料表，除了加密原則，使用者還必須指定應該加密的屬性。作法是指定 [EncryptProperty] 屬性 (針對衍生自 TableEntity 的 POCO 實體)，或在要求選項中指定加密解析程式。加密解析程式是委派，接受資料分割索引鍵、資料列索引鍵和屬性名稱，然後傳回布林值，指出是否應該加密該屬性。在加密期間，用戶端程式庫會使用此資訊，決定將屬性在寫到網路時是否應該加密。委派也提供關於屬性如何加密的可能邏輯。(例如，如果 X，則加密屬性 A，否則加密屬性 A 和 B。) 請注意，讀取或查詢實體時不需要提供這項資訊。

### 批次作業

在批次作業中，批次作業中的所有資料列上會使用相同的 KEK，因為用戶端程式庫只允許每個批次作業有一個選項物件 (也就是一個原則/KEK)。不過，用戶端程式庫會在內部為批次中的每個資料列產生新的隨機 IV 和隨機 CEK。使用者也可以選擇為批次中的每個作業加密不同的屬性，作法是在加密解析程式中定義此行為。

### 查詢

若要執行查詢作業，您必須指定一個能夠解析結果集中的所有金鑰的金鑰解析程式。如果查詢結果中包含的實體無法解析成提供者，用戶端程式庫會擲回錯誤。對於執行伺服器端投射的任何查詢，依預設，用戶端程式庫會將特殊加密中繼資料屬性 (_ClientEncryptionMetadata1 和 _ClientEncryptionMetadata2) 加入至選取的資料行。

## Azure 金鑰保存庫

Azure 金鑰保存庫 (預覽) 可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。使用 Azure 金鑰保存庫時，使用者可以使用受硬體安全模組 (HSM) 保護的金鑰來加密金鑰和密碼 (例如驗證金鑰、儲存體帳戶金鑰、資料加密金鑰、.PFX 檔案和密碼)。如需詳細資訊，請參閱[什麼是 Azure 金鑰保存庫？](../articles/key-vault-whatis.md)。

儲存體用戶端程式庫會使用金鑰保存庫核心程式庫，以提供整個 Azure 的通用架構來管理金鑰。使用者也享有使用金鑰保存庫延伸模組程式庫的額外好處。延伸模組程式庫提供實用的功能，包括簡單又完善的對稱/RSA 本機和雲端金鑰提供者，以及彙總和快取。

### 介面和相依性

有三個金鑰保存庫封裝：

- Microsoft.Azure.KeyVault.Core 包含 IKey 和 IKeyResolver。這是不具有相依性的小型封裝。.NET 和 Windows Phone 的儲存體用戶端程式庫將它定義為相依性。
- Microsoft.Azure.KeyVault 包含金鑰保存庫 REST 用戶端。
- Microsoft.Azure.KeyVault.Extensions 包含延伸模組程式碼，其中包含密碼編譯演算法的實作及 RSAKey 和 SymmetricKey。它依賴 Core 和 KeyVault 命名空間，並提供功能來定義彙總解析程式 (當使用者想要使用多個金鑰提供者時) 和快取金鑰解析程式。儲存體用戶端程式庫不直接依賴這個封裝，如果使用者想要使用 Azure 金鑰保存庫來儲存其金鑰，或使用金鑰保存庫延伸模組來取用本機和雲端密碼編譯提供者，則需要此封裝。

金鑰保存庫是專為高價值的主要金鑰而設計，個別金鑰保存庫的節流限制都以此為設計重點。使用金鑰保存庫執行用戶端加密時，慣用的模型是使用在金鑰保存庫中儲存為密碼且在本機快取的對稱主要金鑰。使用者必須執行下列動作：

1. 離線建立密碼，再上載至金鑰保存庫。
2. 使用密碼的基底識別碼做為參數，解析用於加密的目前密碼版本，並在本機快取這項資訊。使用 CachingKeyResolver 進行快取。使用者不需要實作自己的快取邏輯。
3. 建立加密原則時，使用快取解析程式做為輸入。

如需金鑰保存庫用法的詳細資訊，請參閱[加密程式碼範例](https://github.com/Azure/azure-storage-net/tree/preview/Samples/GettingStarted/EncryptionSamples)。

### 最佳作法

只有 .NET 和 Windows Phone 的儲存體用戶端程式庫中提供加密支援。Windows 執行階段目前不支援加密。此外，金鑰保存庫延伸模組目前不支援 Windows Phone。如果您想要在電話上使用儲存體用戶端加密，您必須實作自己的金鑰提供者。此外，由於 Windows Phone .NET 平台的限制，Windows Phone 上目前不支援分頁 blob 加密。

>[AZURE.IMPORTANT]使用預覽程式庫時，請注意這些要點：
>
>- 請勿對實際執行資料使用預覽程式庫。未來對程式庫的變更會影響使用的結構描述。未來的版本並不保證可以使用預覽程式庫來解密已加密的資料。  
>- 當讀取或寫入加密的 Blob 時，使用完整的 Blob 上傳命令和範圍/完整 Blob 下載命令。避免使用通訊協定作業寫入加密的 Blob，例如放置區塊、放置區塊清單、撰寫頁面或清除頁面；否則您可能會損毀加密的 Blob，並使它無法讀取。
>- 對於資料表，存在類似的條件約束。請小心在未更新加密中繼資料時即更新加密的內容。
>- 如果您在加密 Blob 上設定中繼資料，您可能會覆寫解密所需的加密相關中繼資料，因為設定中繼資料不是附加的。快照集也是如此。在為加密的 Blob 建立快照集時，請避免指定中繼資料。


## 用戶端 API / 介面

使用者在建立 EncryptionPolicy 物件時，可以只提供金鑰 (實作 IKey)、只提供者解析程式 (實作 IKeyResolver)，或兩者都提供。IKey 是以金鑰識別碼來識別的基本金鑰類型，且提供包裝/解除包裝的邏輯。IKeyResolver 在解密程序期間用來解析金鑰。它定義 ResolveKey 方法，可根據金鑰識別碼傳回 IKey。這可讓使用者從多個位置中管理的多個金鑰之中選擇。

- 加密時一律使用金鑰，缺少金鑰將會導致錯誤。
- 解密：
	- 叫用金鑰解析程式 (如果指定) 以取得金鑰。如果已指定解析程式，但沒有金鑰識別碼的對應，則會擲回錯誤。
	- 如果未指定解析程式，但是指定金鑰，則金鑰的金鑰識別碼是以服務所儲存的內容為對象。

[加密範例](https://github.com/Azure/azure-storage-net/tree/preview/Samples/GettingStarted/EncryptionSamples)示範 blob、佇列和資料表更詳細的端對端案例，以及金鑰保存庫整合。

### Blob

使用者將會建立 **BlobEncryptionPolicy** 物件，並設定在要求選項中 (針對每個 API 或在用戶端層級上使用 **DefaultRequestOptions**)。其他一切由用戶端程式庫在內部處理。

	// Create the IKey used for encryption.
 	RsaKey key = new RsaKey("private:key1" /* key identifier */);
  
 	// Create the encryption policy to be used for upload and download.
 	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);
  
 	// Set the encryption policy on the request options.
 	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };
  
 	// Upload the encrypted contents to the blob.
 	blob.UploadFromStream(stream, size, null, options, null);
  
 	// Download and decrypt the encrypted contents from the blob.
 	MemoryStream outputStream = new MemoryStream();
 	blob.DownloadToStream(outputStream, null, options, null);

### 佇列

使用者將會建立 **QueueEncryptionPolicy** 物件，並設定在要求選項中 (針對每個 API 或在用戶端層級上使用 **DefaultRequestOptions**)。其他一切由用戶端程式庫在內部處理。


	// Create the IKey used for encryption.
 	RsaKey key = new RsaKey("private:key1" /* key identifier */);
  
 	// Create the encryption policy to be used for upload and download.
 	QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);
  
 	// Add message
 	QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 	queue.AddMessage(message, null, null, options, null);
  
 	// Retrieve message
 	CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);

### 資料表

除了建立加密原則並設定在要求選項中，使用者還必須在 **TableRequestOptions** 中指定 **EncryptionResolver**，或在實體上設定屬性。

#### 使用解析程式


	// Create the IKey used for encryption.
 	RsaKey key = new RsaKey("private:key1" /* key identifier */);
  
 	// Create the encryption policy to be used for upload and download.
 	TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);
  
 	TableRequestOptions options = new TableRequestOptions() 
 	{ 
    	EncryptionResolver = (pk, rk, propName) =>
     	{
        	if (propName == "foo")
         	{
            	return true;
         	}
         	return false;
     	},
     	EncryptionPolicy = policy
 	};
  
 	// Insert Entity
 	currentTable.Execute(TableOperation.Insert(ent), options, null);
  
 	// Retrieve Entity
 	// No need to specify an encryption resolver for retrieve
 	TableRequestOptions retrieveOptions = new TableRequestOptions() 
 	{
    	EncryptionPolicy = policy
 	};
  
 	TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 	TableResult result = currentTable.Execute(operation, retrieveOptions, null);

#### 使用屬性

如上所述，如果實體實作 TableEntity，則可以使用 [EncryptProperty] 屬性來裝飾屬性，而不需要指定 EncryptionResolver。

	[EncryptProperty]
 	public string EncryptedProperty1 { get; set; }

## 後續步驟

[Microsoft Azure 儲存體的用戶端加密 – 預覽 ](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview.aspx) 下載 [Azure Storage Client Library for .NET NuGet 封裝](http://www.nuget.org/packages/WindowsAzure.Storage/4.4.0-preview) 從 GitHub 下載 [Azure Storage Client Library for .NET 原始程式碼](https://github.com/Azure/azure-storage-net/tree/preview) 下載 Azure 金鑰保存庫 NuGet [核心](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/)、[用戶端](http://www.nuget.org/packages/Microsoft.Azure.KeyVault/)和[延伸模組](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/)封裝。請造訪 [Azure 金鑰保存庫文件](../articles/key-vault-whatis.md)

<!---HONumber=July15_HO2-->