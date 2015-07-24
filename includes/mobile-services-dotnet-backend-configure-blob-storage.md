
##<a name="storage-client-server"></a>在行動服務專案中安裝儲存體用戶端

若要產生可將映像上傳至 Blob 儲存體的 SAS，您必須首先新增可在行動服務專案中安裝儲存體用戶端程式庫的 NuGet 封裝。

1. 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下行動服務專案，然後選取 [管理 NuGet 封裝]。

2. 在左側窗格中，依序選取 [線上] 類別及 [Stabile Only]，搜尋 **WindowsAzure.Storage**，並按一下 [Azure 儲存體] 封裝上的 [安裝]，然後接受授權協定。

  	![](./media/mobile-services-configure-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	這會將 Azure 儲存體服務的用戶端程式庫新增至行動服務專案。

##<a name="update-data-model"></a>更新資料模型中的 TodoItem 定義

TodoItem 類別可定義資料物件，您必須將相同屬性新增至此類別，就像在用戶端上一樣。

1. 在 Visual Studio 2013，開啟行動服務專案，展開 DataObjects 資料夾，然後開啟 TodoItem.cs project 檔案。
	
2. 將下列新屬性新增至 **TodoItem** 類別：

        public string containerName { get; set; }
		public string resourceName { get; set; }
		public string sasQueryString { get; set; }
		public string imageUri { get; set; } 

	這些屬性可用來產生 SAS 及儲存映像資訊。請注意，這些屬性上的大小寫符合 JavaScript 後端版本。

	>[AZURE.NOTE]使用預設資料庫初始設定式時，當 Entity Framework 在 Code First 定義中偵測到資料模型變更時，就會捨棄並重新建立資料庫。若要進行此資料模型變更，並保有資料庫的現有資料，必須使用 Code First Migrations。無法針對 Azure 中的 SQL Database 使用預設的初始設定式。如需詳細資訊，請參閱[如何使用 Code First Migrations 更新資料模型](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md) (英文)。

##<a name="update-scripts"></a>更新 TodoItem 控制器以產生共用存取簽章 

已更新現有的 **TodoItemController**，因此 **PostTodoItem** 方法會在插入新的 TodoItem 時產生 SAS。您也可以

0. 如果您尚未建立儲存體帳戶，請參閱[如何建立儲存體帳戶]。

1. 在管理入口網站中，按一下 [儲存體]、儲存體帳戶和 [管理金鑰]。

2. 請記下 [儲存體帳戶名稱] 和 [存取金鑰]。
 
3. 在行動服務中，按一下 [設定] 索引標籤，向下捲動至 [應用程式設定]，並輸入您從儲存體帳戶取得之下列各項的 [名稱] 和 [值] 組，然後按一下 [儲存]。

	+ `STORAGE_ACCOUNT_NAME`
	+ `STORAGE_ACCOUNT_ACCESS_KEY`

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

	儲存體帳戶存取金鑰會以加密方式儲存在應用程式設定中。您可以在執行期間從任何伺服器指令碼存取此金鑰。如需詳細資訊，請參閱[應用程式設定]。

4. 在 Visual Studio 的 [方案總管] 中，為行動服務專案開啟 Web.config 檔案，並新增下列新應用程式設定，以您剛在入口網站中設定的儲存體帳戶名稱和存取金鑰來取代預留位置：

		<add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
		<add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

	在本機電腦上執行行動服務時，該服務會使用這些儲存的設定，讓您先測試程式碼再發佈。在 Azure 中執行時，行動服務會改用入口網站中設定的應用程式設定值，並忽略這些專案設定。

7.  在 Controllers 資料夾中，開啟 TodoItemController.cs 檔案並新增下列 **using** 指示詞：

		using System;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
  
8.  使用下列程式碼來取代現有的 **PostTodoItem** 方法：

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            string storageAccountName;
            string storageAccountKey;

            // Try to get the Azure storage account token from app settings.  
            if (!(Services.Settings.TryGetValue("STORAGE_ACCOUNT_NAME", out storageAccountName) |
            Services.Settings.TryGetValue("STORAGE_ACCOUNT_ACCESS_KEY", out storageAccountKey)))
            {
                Services.Log.Error("Could not retrieve storage account settings.");
            }

            // Set the URI for the Blob Storage service.
            Uri blobEndpoint = new Uri(string.Format("https://{0}.blob.core.windows.net", storageAccountName));

            // Create the BLOB service client.
            CloudBlobClient blobClient = new CloudBlobClient(blobEndpoint, 
                new StorageCredentials(storageAccountName, storageAccountKey));

            if (item.containerName != null)
            {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.ToLower();

                // Create a container, if it doesn't already exist.
                CloudBlobContainer container = blobClient.GetContainerReference(item.containerName);
                await container.CreateIfNotExistsAsync();

                // Create a shared access permission policy. 
                BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

                // Enable anonymous read access to BLOBs.
                containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
                container.SetPermissions(containerPermissions);

                // Define a policy that gives write access to the container for 5 minutes.                                   
                SharedAccessBlobPolicy sasPolicy = new SharedAccessBlobPolicy()
                {
                    SharedAccessStartTime = DateTime.UtcNow,
                    SharedAccessExpiryTime = DateTime.UtcNow.AddMinutes(5),
                    Permissions = SharedAccessBlobPermissions.Write
                };

                // Get the SAS as a string.
                item.sasQueryString = container.GetSharedAccessSignature(sasPolicy); 

                // Set the URL used to store the image.
                item.imageUri = string.Format("{0}{1}/{2}", blobEndpoint.ToString(), 
                    item.containerName, item.resourceName);
            }

            // Complete the insert operation.
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

   	現在，這個 POST 方法會對插入的項目產生新的 SAS (有效期間為 5 分鐘)，並將產生的 SAS 值指派給所傳回項目的 `sasQueryString` 屬性。`imageUri` 屬性也會設定為新 BLOB 的資源路徑，以便於繫結期間在用戶端 UI 中顯示映像。

	>[AZURE.NOTE]此程式碼會為個別 BLOB 建立 SAS。如果您需要使用相同的 SAS 將多個 Blob 上傳至容器，您可以改用空白的 Blob 資源名稱呼叫 <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">generateSharedAccessSignature 方法</a>，如下所示：<pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

接著，您將會更新快速入門應用程式，以使用插入時產生的 SAS 來新增映像上傳功能。
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[如何建立儲存體帳戶]: ../articles/storage/storage-create-storage-account.md
[應用程式設定]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=July15_HO2-->