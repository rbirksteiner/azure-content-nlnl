<properties
   pageTitle="在 Microsoft Azure 儲存體中使用 Azure 金鑰保存庫加密和解密 Blob"
   description="本教學課程將告訴您如何使用 Microsoft Azure 儲存體的用戶端加密並搭配 Azure 金鑰保存庫來加密和解密 blog"
   services="storage"
   documentationCenter=""
   authors="adhurwit"
   manager=""
   editor=""/>

<tags
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="06/17/2015"
   ms.author="adhurwit"/>

# 在 Microsoft Azure 儲存體中使用 Azure 金鑰保存庫加密和解密 blob

## 簡介
 
本教學課程涵蓋如何搭配使用用戶端儲存體加密 (目前為預覽狀態) 和 Azure 金鑰保存庫 (目前也是預覽狀態)。其中告訴您如何在主控台應用程式中使用這些技術加密和解密 blob。

**預估完成時間：**20 分鐘

如需 Azure 金鑰保存庫的概觀資訊，請參閱[什麼是 Azure 金鑰保存庫？](key-vault/key-vault-whatis.md)

如需 Azure 儲存體用戶端加密的概觀資訊，請參閱 [Microsoft Azure 儲存體用戶端加密 - 預覽](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview.aspx)


## 必要條件

若要完成本教學課程，您必須具備下列項目：

- Azure 儲存體帳戶
- Visual Studio 2013 或更新版本
- Azure PowerShell 


## 用戶端加密程序概觀

如需 Microsoft Azure 儲存體用戶端加密的概觀，請參閱 [http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx "開始使用 Microsoft Azure 儲存體的用戶端加密")

以下是該部落格文章中所述的程序：

1. Azure 儲存體用戶端 SDK 會產生內容加密金鑰 (CEK)，這是使用一次的對稱金鑰。
2. 使用此 CEK 加密使用者資料。
3. 然後使用金鑰加密金鑰 KEK 包裝 (加密) CEK。KEK 由金鑰識別碼所識別，可以是非對稱金鑰組或對稱金鑰，且可以在本機管理或儲存在 Azure 金鑰保存庫中。儲存體用戶端本身永遠沒有 KEK 的存取權。它只是叫用金鑰保存庫所提供的金鑰包裝演算法。如有需要，使用者可以選擇使用自訂提供者來包裝/取消包裝金鑰。
4. 然後，將加密的資料上傳至 Azure 儲存體服務。


## 設定 Azure 金鑰保存庫
若要繼續進行本教學課程，您必須執行以下在教學課程[開始使用 Azure 金鑰保存庫](key-vault/key-vault-get-started.md)中所述的步驟。

- 建立金鑰保存庫
- 將金鑰或密碼加入至金鑰保存庫
- 向 Azure Active Directory 註冊應用程式
- 授權應用程式使用金鑰或密碼

請記下向 Azure Active Directory 註冊應用程式時所產生的 ClientID 和 ClientSecret。

在金鑰保存庫中建立這兩個金鑰。我們在教學課程的其餘部分假設您已使用下列名稱：ContosoKeyVault 和 TestRSAKey1。


## 使用封裝與 AppSettings 建立主控台應用程式

在 Visual Studio 中，建立新的主控台應用程式。

在封裝管理員主控台中加入必要的 nuget 封裝：

	// Note that this is the preview version for Azure Storage
	Install-Package WindowsAzure.Storage -Pre

	// This is the latest stable release for ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	// These are currently only available in preview
	Install-Package Microsoft.Azure.KeyVault -Pre
	Install-Package Microsoft.Azure.KeyVault.Extensions -Pre


將 AppSettings 加入至 App.Config。

	<appSettings>
	    <add key="accountName" value="myaccount"/>
	    <add key="accountKey" value="theaccountkey"/>
	    <add key="clientId" value="theclientid"/>
	    <add key="clientSecret" value="theclientsecret"/>
    	<add key="container" value="stuff"/>
	</appSettings>

新增下列 using 陳述式，並確定將 System.Configuration 的參考加入至專案。

	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Configuration;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.Azure.KeyVault;
	using System.Threading;		
	using System.IO;


## 新增方法以取得權杖給主控台應用程式

需要驗證以存取您的金鑰保存庫的金鑰保存庫類別會使用下列方法。

	private async static Task<string> GetToken(string authority, string resource, string scope)
	{
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(
	        ConfigurationManager.AppSettings["clientId"], 
	        ConfigurationManager.AppSettings["clientSecret"]);
		AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	
	    if (result == null)
	        throw new InvalidOperationException("Failed to obtain the JWT token");
	
	    return result.AccessToken;
	}

## 在您的程式中存取儲存體和金鑰保存庫 

在 Main() 函式中，加入下列程式碼：

	// This is standard code to interact with Blob Storage
	StorageCredentials creds = new StorageCredentials(
		ConfigurationManager.AppSettings["accountName"],
       	ConfigurationManager.AppSettings["accountKey"]);
	CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
	CloudBlobClient client = account.CreateCloudBlobClient();
	CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
	contain.CreateIfNotExists();

	// The Resolver object is used to interact with Key Vault for Azure Storage
	// This is where the GetToken method from above is used
	KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE]金鑰保存庫物件模型
>
>務必了解，實際上有兩個金鑰保存庫物件模型需要注意：一個是根據 REST API (KeyVault 命名空間)，另一個是用戶端加密的延伸。

> 金鑰保存庫用戶端會與 REST API 互動，並了解金鑰保存庫中包含的兩種項目的 JSON Web 金鑰和密碼。

> 金鑰保存庫延伸模組似乎是特別為 Azure 儲存體中的用戶端加密而建立的類別。它們根據金鑰解析程式的概念，包含金鑰和類別的介面 (IKey)。您需要知道 IKey 的兩個實作：RSAKey 和 SymmetricKey。現在，它們剛好與金鑰保存庫中所包含的項目重疊，但目前是獨立的類別 (因此，金鑰保存庫用戶端所擷取的金鑰和密碼不實作 IKey)。


## 加密 blob 和上傳
加入下列程式碼來加密 Blob 並上傳至 Azure 儲存體帳戶。使用的 ResolveKeyAsync 方法會傳回 IKey。

	
	// Retrieve the key that you created previously
	// The IKey that is returned here is an RsaKey
	// Remember that we used the names contosokeyvault and testrsakey1
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


	// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy. 
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

	// Reference a block blob
	CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

	// Upload using the UploadFromStream method
	using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
		blob.UploadFromStream(stream, stream.Length, null, options, null);


以下是目前 Azure 管理入口網站的螢幕擷取畫面，其中使用用戶端加密與金鑰保存庫中儲存的金鑰來加密 blob。KeyId 屬性是金鑰保存庫中做為金鑰加密金鑰 (KEK) 的金鑰的 URI。EncryptedKey 屬性包含內容加密金鑰 (CEK) 的加密版本。

![此螢幕擷取畫面顯示包含加密中繼資料的 Blob 中繼資料][1]

> [AZURE.NOTE]如果您查看 BlobEncryptionPolicy 建構函式，您會看到它可接受金鑰和 (或) 解析程式。請注意，現在您無法使用解析程式進行加密，因為它目前不支援預設金鑰。



## 解密 blob 和下傳
解密其實就是解析程式類別存在的價值。加密金鑰的識別碼與它的中繼資料裡的 Blob 相關聯，因此，沒有理由讓您擷取金鑰，並記住金鑰與 blob 之間的關聯。您只需要確定金鑰仍在金鑰保存庫中。

RSA 金鑰的私密金鑰保留在保存庫金鑰中，為了進行解密，從包含 CEC (內容加密金鑰) 的 blob 中繼資料，加密的金鑰會傳送至金鑰保存庫進行解密。

加入下列內容以解密您剛才上傳的 blob。

	// In this case we will not pass a key and only pass the resolver because 
	// 	this policy will only be used for downloading / decrypting
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
	    blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE]有幾個其他種類的解析程式可簡化金鑰管理，包括：AggregateKeyResolver 和 CachingKeyResolver。


## 使用金鑰保存庫密碼
搭配用戶端加密來使用密碼的方式是透過 SymmetricKey 類別，因為密碼基本上是一種對稱金鑰。但是，如上所述，金鑰保存庫中的密碼未完全對應到 SymmetricKey。有幾件事必須了解：


- SymmetricKey 中的金鑰必須是固定的長度：128、192、256、384 或 512 位元
- SymmetricKey 中的金鑰應該為 Base64 編碼
- 用來做為 SymmetricKey 的金鑰保存庫密碼，在金鑰保存庫中必須具有 "application/octet-stream" 內容類型。

以下是在 PowerShell 中，在保存庫中建立密碼做為 SymmetricKey 的範例：

	// Here we are making a 128-bit key so we have 16 characters. 
	// 	The characters are in the ASCII range of UTF8 so they are
	//	each 1 byte. 16 x 8 = 128
	$key = "qwertyuiopasdfgh"
	$b = [System.Text.Encoding]::UTF8.GetBytes($key)
	$enc = [System.Convert]::ToBase64String($b)
	$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

	// substitute the VaultName and Name in this command
	$secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

在主控台應用程式中，您可以使用像以前一樣的呼叫來擷取這個密碼做為 SymmetricKey。

	SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    	"https://contosokeyvault.vault.azure.net/secrets/TestSecret2/", 
        CancellationToken.None).GetAwaiter().GetResult();

就這麼簡單。盡情享受！

## 後續步驟

如需有關搭配使用 C# 和 Microsoft Azure 儲存體的詳細資訊，請參閱[適用於 .NET 的 Microsoft Azure 儲存體用戶端程式庫](https://msdn.microsoft.com/library/azure/dn261237.aspx)

如需 Blob REST API 的詳細資訊，請參閱 [Blob 服務 REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

如需 Microsoft Azure 儲存體的最新資訊，請移至 [Microsoft Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/)


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png

<!---HONumber=July15_HO1-->