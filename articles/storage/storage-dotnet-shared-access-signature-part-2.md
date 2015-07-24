<properties 
	pageTitle="透過 Blob 服務來建立與使用 SAS | Microsoft Azure" 
	description="探討如何透過 Blob 服務來產生及使用共用存取簽章" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="tamram"/>


# 共用存取簽章，第 2 部分：透過 Blob 服務來建立與使用 SAS

## 概觀

本教學課程的[第 1 部分](storage-dotnet-shared-access-signature-part-1.md)探討了共用存取簽章 (SAS)，並說明使用它們的最佳作法。第 2 部分顯示如何產生然後使用共用存取簽章以搭配 Azure Blob 服務。這些範例均以 C# 撰寫，並使用 Azure Storage Client Library for .NET。所涵蓋的案例包括使用共用存取簽章的以下層面：

- 在容器上產生共用存取簽章
- 在 blob 上產生共用存取簽章
- 建立預存存取原則，以管理容器資源上的簽章
- 從用戶端應用程式測試共用存取簽章

## 關於本教學課程
在本教學課程中，我們將專注於藉由建立兩個主控台應用程式，以為容器和 blob 建立共用存取簽章。第一個主控台應用程式會在容器及 blob 上產生共用存取簽章。這個應用程式知曉儲存體帳戶金鑰。第二個主控台應用程式將擔任用戶端應用程式，它會使用第一個應用程式所建立的共用存取簽章，來存取容器及 blob 資源。這個應用程式只會使用共用存取簽章來向容器及 blob 資源驗證其存取 - 它不知曉帳戶金鑰。

## 第 1 部份：建立主控台應用程式以產生共用存取簽章

首先，請確定您已安裝 Azure Storage Client Library for .NET。您可以安裝包含最新用戶端程式庫組件的 [NuGet 封裝](http://nuget.org/packages/WindowsAzure.Storage/ "NuGet 封裝")，這是確定您具有最新修正程式的建議方法。您也可以在最新版 [Azure SDK for .NET](http://azure.microsoft.com/downloads/) 中一起下載用戶端程式庫。

在 Visual Studio 中，建立新的 Windows 主控台應用程式，並將它命名為 **GenerateSharedAccessSignatures**。使用下列任一種方法，新增對 **Microsoft.WindowsAzure.Configuration.dll** 及 **Microsoft.WindowsAzure.Storage.dll** 的參照：

- 	如果要安裝 NuGet 封裝，請先安裝 [NuGet Package Manager Extension for Visual Studio](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)。在 Visual Studio 中，選取 [Project | Manage NuGet Packages]、在線上搜尋 [Azure 儲存體]，並遵循安裝指示。
- 	或者，在您的 Azure SDK 安裝中找到組件，並新增對它們的參照。
 
在 Program.cs 檔的頂端，新增下列 **using** 陳述式：

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

編輯 app.config 檔，使它包含的組態設定具有指向您儲存體帳戶的連接字串。您的 app.config 檔案應該看起來類似如下：

    <configuration>
      <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings> 
    </configuration>

### 為容器產生共用存取簽章 URI

一開始，我們將新增方法以在新容器上產生共用存取簽章。在此情況中，簽章不與預存存取原則相關，因此它在 URI 上攜帶了資訊，以指出其到期時間與授與權限。

首先，將程式碼新增至 **Main()** 方法，以驗證對儲存體帳戶的存取，並建立新的容器：

    static void Main(string[] args)
    {
	    //Parse the connection string and return a reference to the storage account.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
	    
	    //Create the blob client object.
	    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
	    
	    //Get a reference to a container to use for the sample code, and create it if it does not exist.
	    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
	    container.CreateIfNotExists();
	    
	    //Insert calls to the methods created below here...
	    
	    //Require user input before closing the console window.
	    Console.ReadLine();
    }

接下來，新增方法以產生容器的共用存取簽章，並傳回簽章 URI：

    static string GetContainerSasUri(CloudBlobContainer container)
    {
	    //Set the expiry time and permissions for the container.
	    //In this case no start time is specified, so the shared access signature becomes valid immediately.
	    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
	    sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;
	    
	    //Generate the shared access signature on the container, setting the constraints directly on the signature.
	    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
	    
	    //Return the URI string for the container, including the SAS token.
	    return container.Uri + sasContainerToken;
    }

在 **Main()** 方法的底部，對 **Console.ReadLine()** 進行呼叫之前，新增下列幾行，以呼叫 **GetContainerSasUri()** 並將簽章 URI 寫到主控台視窗：

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

編譯並執行以輸出新容器的共用存取簽章 URI。URI 將類似如下 URI：

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

執行程式碼之後，您在容器上建立的共用存取簽章將在接下來的 24 小時內有效。簽章會授與用戶端權限以列出容器中的 blob，以及將新的 blob 寫入容器。

### 產生 Blob 的共用存取簽章 URI

接下來，我們將撰寫類似的程式碼以在容器內建立新的 blob，並為它產生共用存取簽章。此共用存取簽章不與預存存取原則相關，因此它在 URI 上包含了開始時間、到期時間及權限資訊。

新增新方法以建立新的 blob 並在其中寫入一些文字，然後產生共用存取簽章並傳回簽章 URI：

    static string GetBlobSasUri(CloudBlobContainer container)
    {
	    //Get a reference to a blob within the container.
	    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");
	    
	    //Upload text to the blob. If the blob does not yet exist, it will be created. 
	    //If the blob does exist, its existing content will be overwritten.
	    string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
	    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	    ms.Position = 0;
	    using (ms)
	    {
		    blob.UploadFromStream(ms);
	    }
	    
	    //Set the expiry time and permissions for the blob.
	    //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
	    //The shared access signature will be valid immediately.
	    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
	    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
	    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
	    
	    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
	    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);
	    
	    //Return the URI string for the container, including the SAS token.
	    return blob.Uri + sasBlobToken;
    }

在 **Main()** 方法的底部，對 **Console.ReadLine()** 的呼叫之前，新增下列幾行，以呼叫 **GetBlobSasUri()** 並將共用存取簽章 URI 寫到主控台視窗：
    
    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();
    

編譯並執行以輸出新 blob 的共用存取簽章 URI。URI 將類似如下 URI：

	https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

### 在容器上建立預存存取原則

現在，讓我們在容器上建立預存存取原則，這將為與它相關的任何共用存取簽章定義限制。

在前面的範例中，我們指定了開始時間 (隱含或明確)、到期時間，以及對共用存取簽章 URI 本身的權限。在接下來的範例中，我們將在預存存取原則上指定這些，而不在共用存取簽章上指定。如此做讓我們能變更這些限制，而不必重新發出共用存取簽章。

在共用的存取簽章上可以有一或多個限制，其餘的則在儲存原則上。不過，您只能在其中一處指定開始時間、到期時間與權限；例如，您不能在共用存取簽章上指定權限，然後又在預存存取原則上指定。

請注意，當您新增容器的存取原則時，您必須取得容器的現有權限、新增新的存取原則，然後設定容器的權限。

新增可在容器上建立新預存存取原則的新方法，並傳回原則的名稱：

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container, 
		string policyName)
    {
        //Create a new shared access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
        };

        //Get the container's existing permissions.
        BlobContainerPermissions permissions = container.GetPermissions();

        //Add the new policy to the container's permissions, and set the container's permissions.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

在呼叫 **Console.ReadLine()** 之前，於 **Main()** 方法底部新增下列各行，以先清除任何現有的存取原則，然後再呼叫 **CreateSharedAccessPolicy()** 方法：

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for 
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

請注意，當您清除容器的存取原則時，您必須先取得容器的現有權限、清除權限，然後再次設定權限。

### 在使用存取原則的容器上產生共用存取簽章 URI

接下來，我們將在稍早建立的容器上建立另一個共用存取簽章，但這次我們將會把簽章與我們在前面範例中建立的存取原則相關聯。

新增新方法以在容器上產生另一個共用存取簽章：

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
	    //Generate the shared access signature on the container. In this case, all of the constraints for the 
	    //shared access signature are specified on the stored access policy.
	    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);
	    
	    //Return the URI string for the container, including the SAS token.
	    return container.Uri + sasContainerToken;
    }
    
在 **Main()** 方法的底部，對 **Console.ReadLine()** 的呼叫之前，新增下列幾行，以呼叫 **GetContainerSasUriWithPolicy()** 方法：

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

### 在使用存取原則的 Blob 上產生共用存取簽章 URI

最後，我們將新增類似的方法建立另一個 blob，並產生與存取原則相關的共用存取簽章。

新增新方法以建立 blob 並產生共用存取簽章：

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
	    //Get a reference to a blob within the container.
	    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");
	    
	    //Upload text to the blob. If the blob does not yet exist, it will be created. 
	    //If the blob does exist, its existing content will be overwritten.
	    string blobContent = "This blob will be accessible to clients via a shared access signature. " + 
	    "A stored access policy defines the constraints for the signature.";
	    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	    ms.Position = 0;
	    using (ms)
	    {
		    blob.UploadFromStream(ms);
	    }
	    
	    //Generate the shared access signature on the blob.
	    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);
	    
	    //Return the URI string for the container, including the SAS token.
	    return blob.Uri + sasBlobToken;
    }

在 **Main()** 方法的底部，對 **Console.ReadLine()** 的呼叫之前，新增下列幾行，以呼叫 **GetBlobSasUriWithPolicy** 方法：

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

**Main()** 方法現在應該整體看來如此。執行它以將共用存取簽章 URI 寫到主控台視窗，然後將它們複製並貼入文字檔，以便用於本教學課程的第 2 部分。

    static void Main(string[] args)
    {
	    //Parse the connection string and return a reference to the storage account.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
	    
	    //Create the blob client object.
	    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
	    
	    //Get a reference to a container to use for the sample code, and create it if it does not exist.
	    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
	    container.CreateIfNotExists();
	    
	    //Generate a SAS URI for the container, without a stored access policy.
	    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
	    Console.WriteLine();
	    
	    //Generate a SAS URI for a blob within the container, without a stored access policy.
	    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
	    Console.WriteLine();
	    
        //Clear any existing access policies on container.
        BlobContainerPermissions perms = container.GetPermissions();
        perms.SharedAccessPolicies.Clear();
        container.SetPermissions(perms);

        //Create a new access policy on the container, which may be optionally used to provide constraints for 
        //shared access signatures on the container and the blob.
	    string sharedAccessPolicyName = "tutorialpolicy";
	    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
	    
	    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
	    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
	    Console.WriteLine();
	    
	    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
	    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
	    Console.WriteLine();
	    
	    Console.ReadLine();
    }

執行 GenerateSharedAccessSignatures 主控台應用程式時，您將在主控台視窗中看到類似如下的輸出。這些是您將在教學課程第 2 部分使用的共用存取簽章。

![sas-console-output-1][sas-console-output-1]

## 第 2 部份：建立主控台應用程式以測試共用存取簽章

為了測試前面範例中建立的共用存取簽章，我們將建立第二個主控台應用程式，使用簽章以在容器和 blob 上執行操作。

> [AZURE.NOTE]如果在您完成本教學課程的第一個部分之後已超過 24 小時，您所產生的簽章將不再有效。在此情況下，您應該在第一個主控台應用程式中執行程式碼以產生新的共用存取簽章，並在第二個部分的教學課程中使用。

在 Visual Studio 中，建立新的 Windows 主控台應用程式，並將它命名為 **ConsumeSharedAccessSignatures**。如您前面作法一樣，新增對 **Microsoft.WindowsAzure.Configuration.dll** 及 **Microsoft.WindowsAzure.Storage.dll** 的參照。

在 Program.cs 檔的頂端，新增下列 **using** 陳述式：

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
在 **Main()** 方法的本文中，新增下列限制，並將其值更新為您在教學課程第 1 部分中產生的共用存取簽章。

    static void Main(string[] args)
    {
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }
    
### 新增方法以嘗試使用共用存取簽章的容器操作

接下來，我們將新增使用容器上的共用存取簽章，測試部分代表性容器操作的方法。請注意，共用存取簽章用來傳回對容器的參照，並單獨根據簽章向容器驗證存取。

將下列方法新增至 Program.cs：

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<ICloudBlob> blobList = new List<ICloudBlob>();

        //Write operation: write a new blob to the container. 
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //List operation: List the blobs in the container.
        try
        {
            foreach (ICloudBlob blob in container.ListBlobs())
            {
                blobList.Add(blob);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Get a reference to one of the blobs in the container and read it. 
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        //Delete operation: Delete a blob in the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


更新 **Main()** 方法以呼叫 **UseContainerSAS()**，並使用您在容器上建立的兩個共用存取簽章：

	static void Main(string[] args)
	{
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
	
	    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
	    UseContainerSAS(containerSAS);
	    UseContainerSAS(containerSASWithAccessPolicy); 
	    
	    Console.ReadLine();
	}


### 新增方法以嘗試使用共用存取簽章的 Blob 操作

最後，我們將新增使用 blob 上的共用存取簽章，測試部分代表性 blob 操作的方法。在此案例中，我們使用建構函式 **CloudBlockBlob(String)**，並傳入共用存取簽章，以傳回 blob 的參照。不需要其他驗證；只有根據簽章。

將下列方法新增至 Program.cs：

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        //Write operation: Write a new blob to the container. 
        try
        {
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Read the contents of the blob.
        try
        {
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Delete operation: Delete the blob.
        try
        {
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


更新 **Main()** 方法以呼叫 **UseBlobSAS()**，並使用您在 blob 上建立的兩個共用存取簽章：

	static void Main(string[] args)
	{
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
	
	    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
	    UseContainerSAS(containerSAS);
	    UseContainerSAS(containerSASWithAccessPolicy); 
	    
	    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
	    UseBlobSAS(blobSAS);
	    UseBlobSAS(blobSASWithAccessPolicy);
	
	    Console.ReadLine();
	}

執行主控台應用程式，並觀察輸出，以查看哪些簽章允許哪些操作。主控台視窗中的輸出類似如下範例：

![sas-console-output-2][sas-console-output-2]

## 後續步驟

[共用存取簽章，第 1 部分：了解 SAS 模型](../storage-dotnet-shared-access-signature-part-1/)

[管理 Azure 儲存體資源的存取](http://msdn.microsoft.com/library/azure/ee393343.aspx)

[使用共用存取簽章 (REST API) 來委派存取權](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[資料表與佇列 SAS 簡介](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG

 

<!---HONumber=July15_HO1-->