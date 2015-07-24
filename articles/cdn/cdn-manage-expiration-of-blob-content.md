<properties 
 pageTitle="如何管理 Azure 內容傳遞網路 (CDN) 中 Blob 內容的到期" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="zhangmanling" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.workload="media" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="04/25/2015" 
 ms.author="mazha"/>


#如何管理 Azure 內容傳遞網路 (CDN) 中 Blob 內容的到期  

充分利用 Azure CDN 快取中獲益的 Blob 是在其存留時間 (TTL) 期限期間經常存取。Blob 在 TTL 期限內存留於快取中，然後 Blob 服務會在經過該時間之後進行重新整理。然後重複執行此程序。

您有兩個選項可控制 TTL。

1.	請不要設定快取值，因此會使用預設 TTL：7 天。 
2.	在 [**放置 Blob**]、[**放置封鎖清單**] 或 [**設定 Blob 屬性**] 要求上明確地設定 *x-ms-blob-cache-control* 屬性，或使用 Azure 受管理程式庫來設定 [BlobProperties.CacheControl](http://msdn.microsoft.com/library/microsoft.windowsazure.storageclient.blobproperties.cachecontrol.aspx) 屬性。設定此屬性會設定 Blob 之 *Cache-Control* 標頭的值。標頭或屬性的值應該指定適當的值 (秒)。例如，若要將快取期間上限設為一年，您可以將要求標頭指定為 `x-ms-blob-cache-control: public, max-age=31556926`。如需有關設定快取標頭的詳細資料，請參閱 [HTTP/1.1 規格](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)。  

您想要透過 CDN 快取的任何內容都必須以公開存取的 Blob 形式儲存在 Azure 儲存體帳戶中。如需 Azure Blob 服務的詳細資料，請參閱 **Blob 服務概念**。

有數種不同的方式可使用 Blob 服務中的內容：

-	使用 **Azure 受管理程式庫參考**所提供的受管理 API。
-	使用協力廠商儲存體管理工具。
-	使用 Azure 儲存體服務 REST API。  

下列程式碼範例是一個主控台應用程式，可使用 Azure 受管理程式庫建立容器、設定其公開存取的權限，以及在容器內建立 Blob。它也會在 Blob 上設定 Cache-Control 標頭，以明確地指定想要的重新整理間隔。

假設您已啟用 CDN (如上所示)，則 CDN 將會快取所建立的 Blob。請務必使用您自己的儲存體帳戶和存取金鑰來指定帳戶認證：

	using System;
	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.StorageClient;
	
	namespace BlobsInCDN
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            //Specify storage credentials.
	            StorageCredentialsAccountAndKey credentials = new StorageCredentialsAccountAndKey("storagesample",
	                "m4AHAkXjfhlt2rE2BN/hcUR4U2lkGdCmj2/1ISutZKl+OqlrZN98Mhzq/U2AHYJT992tLmrkFW+mQgw9loIVCg==");
	            
	            //Create a reference to your storage account, passing in your credentials.
	            CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);
	            
	            //Create a new client object, which will provide access to Blob service resources.
	            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
	
	            //Create a new container.
	            CloudBlobContainer container = blobClient.GetContainerReference("cdncontent");
	            container.CreateIfNotExist();
	
	            //Specify that the container is publicly accessible.
	            BlobContainerPermissions containerAccess = new BlobContainerPermissions();
	            containerAccess.PublicAccess = BlobContainerPublicAccessType.Container;
	            container.SetPermissions(containerAccess);
	
	            //Create a new blob and write some text to it.
	            CloudBlob blob = blobClient.GetBlobReference("cdncontent/testblob.txt");
	            blob.UploadText("This is a test blob.");
	
	            //Set the Cache-Control header on the blob to specify your desired refresh interval.
	            blob.SetCacheControl("public, max-age=31536000");
	        }
	    }
	
	    public static class BlobExtensions
	    {
	        //A convenience method to set the Cache-Control header.
	        public static void SetCacheControl(this CloudBlob blob, string value)
	        {
	            blob.Properties.CacheControl = value;
	            blob.SetProperties();
	        }
	    }
	}

測試可透過 CDN 特有的 URL 來提供 Blob。針對上面顯示的 Blob，URL 會與下面類似：

	http://az1234.vo.msecnd.net/cdncontent/testblob.txt  

如有需要，您可以使用 **wget** 或 Fiddler 這類工具，檢查要求和回應的詳細資料。

##另請參閱

[如何管理 Azure 內容傳遞網路 (CDN) 中雲端服務內容的到期](./cdn-manage-expiration-of-cloud-service-content.md)

<!---HONumber=62-->