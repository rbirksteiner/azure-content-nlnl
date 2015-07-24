<properties 
	pageTitle="如何使用 Java 的檔案儲存體 | Microsoft Azure" 
	description="了解如何使用 Azure 檔案服務來上傳、下載、列出及刪除檔案。範例以 Java 撰寫。" 
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe" />

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="v-dedomi"/>

# 如何使用 Java 的檔案儲存體

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

## 概觀

在本指南中，您將學習如何對 Microsoft Azure 檔案儲存體服務執行基本作業。透過以 Java 撰寫的範例，您將學習如何建立共用和目錄、上傳、清單，及刪除檔案。如果您是 Microsoft Azure 檔案儲存體服務的新手，透過下列各節中的概念對於了解範例很有幫助。

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 Java 應用程式

若要建置範例，您將需要 Java Development Kit (JDK) 和 [適用於 Java 的 Azure 儲存體 SDK][]。您也應該建立 Azure 儲存體帳戶。

## 設定您的應用程式以使用檔案儲存體

若要使用 Azure 儲存體 API，請將下列陳述式加入至您要從其存取儲存體服務的 Java 檔案頂端。

	// Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
	import com.microsoft.azure.storage.file.*;

## 設定 Azure 儲存體連接字串

若要使用檔案儲存體，您必須連接到您的 Azure 儲存體帳戶。第一個步驟是設定連接字串，我們會用來連接到您的儲存體帳戶。讓我們定義靜態變數以便進行。

	// Configure the connection-string with your values
	public static final String storageConnectionString = 
	    "DefaultEndpointsProtocol=http;" + 
	    "AccountName=your_storage_account_name;" + 
	    "AccountKey=your_storage_account_key";

> [AZURE.NOTE]將 your_storage_account_name 和 your_storage_account_key 取代為您的儲存體帳戶的實際值。

## 連接到 Azure 儲存體帳戶

若要連接到您的儲存體帳戶，您必須使用 **CloudStorageAccount** 物件，將連接字串傳遞至其**剖析**方法。

	// Use the CloudStorageAccount object to connect to your storage account
	try {
		CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
	} catch (InvalidKeyException invalidKey) {
		// Handle the exception
	}

**CloudStorageAccount.parse** 會擲回 InvalidKeyException，因此您必須將其放在 try/catch 區塊內。

## 如何：建立共用

檔案儲存體的所有檔案和目錄都位於名為 [**共用**] 的容器中。您的儲存體帳戶可以有帳戶容量允許數量的共用。若要取得共用及其內容的存取權，您必須使用檔案儲存體用戶端。

	// Create the file storage client.
	CloudFileClient fileClient = storageAccount.createCloudFileClient();

使用檔案儲存體用戶端，您可以取得共用的參考。

	// Get a reference to the file share
	CloudFileShare share = fileClient.getShareReference("sampleshare"); 

若要實際建立共用，請使用 CloudFileShare 物件的 **createIfNotExists** 方法。

	if (share.createIfNotExists()) {
		System.out.println("New share created");
	}

目前，**共用**會將參考保留至名為 **sampleshare** 的共用。

## 如何：上傳檔案

Azure 檔案儲存體共用至少包含根目錄，檔案可以放置其中。在本節中，您將學習如何從本機儲存體將檔案上傳至共用的根目錄。

上傳檔案的第一個步驟是取得檔案所在之目錄的參考。您可以藉由呼叫共用物件的 **getRootDirectoryReference** 方法來完成。

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

現在您具有共用之根目錄的參考，您可以使用下列程式碼上傳檔案。

	// Define the path to a local file.
	final String filePath = "C:\\temp\\Readme.txt";

	CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
	cloudFile.uploadFromFile(filePath);

## 如何：建立目錄

您也可以組織儲存體，方法是將檔案放在子目錄中，而不是將所有檔案都放在根目錄中。Azure 檔案儲存體服務可讓您建立您的帳戶允許數量的目錄。下列程式碼會在根目錄底下建立名為 **sampledir** 的子目錄。

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	//Get a reference to the sampledir directory 
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");
			    
	if (sampleDir.createIfNotExists()) {
		System.out.println("sampledir created");
	} else {
		System.out.println("sampledir already exists");
	}

## 如何：列出共用中的檔案和目錄

取得共用內檔案和目錄的清單很容易，只要在 CloudFileDirectory 參考上呼叫 **listFilesAndDirectories** 即可。方法會傳回您可以逐一查看的 ListFileItem 物件清單。例如，下列程式碼會列出根目錄內的檔案和目錄。

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();
		   
	for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
		System.out.println(fileItem.getUri());
	}


## 如何：下載檔案

您針對檔案儲存體執行的其中一個較頻繁作業是下載檔案。在下列範例中，程式碼會下載 SampleFile.txt，並顯示其內容。

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();
	
	//Get a reference to the directory that contains the file
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");
			    
	//Get a reference to the file you want to download
	CloudFile file = sampleDir.getFileReference("SampleFile.txt");

	//Write the contents of the file to the console.
	System.out.println(file.downloadText());

## 如何：刪除檔案

另一個常見的檔案儲存體作業是刪除檔案。下列程式碼會刪除儲存在名為 **sampledir** 之目錄內名為 SampleFile.txt 的檔案。


	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory where the file to be deleted is in
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");
	
	String filename = "SampleFile.txt"
	CloudFile file;

	file = containerDir.getFileReference(filename)
	if ( file.deleteIfExists() ) {
		System.out.println(filename + " was deleted");
	}


## 如何：刪除目錄

刪除目錄是相當簡單的工作，不過請注意您無法刪除仍然包含檔案或其他目錄的目錄。

	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory you want to delete
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");	

	// Delete the directory
	if ( containerDir.deleteIfExists() ) {
		System.out.println("Directory deleted");
	}


## 如何：刪除共用

刪除共用可以藉由在 CloudFileShare 物件呼叫 **deleteIfExists** 方法來完成。以下是執行該作業的範例程式碼。

	try
	{
	    // Retrieve storage account from connection-string.
	    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	    // Create the file client.
	   CloudFileClient fileClient = storageAccount.createCloudFileClient();

	   // Get a reference to the file share
	   CloudFileShare share = fileClient.getShareReference("sampleshare");
	   
	   if (share.deleteIfExists()) {
		   System.out.println("sampleshare deleted");
	   } 
	} catch (Exception e) {
		e.printStackTrace();
	}

## 後續步驟

如果您想要深入了解其他 Azure 儲存體 API，請參考下列連結。

- [Azure Storage SDK for Java]
- [Azure 儲存體用戶端 SDK 參考]
- [Azure 儲存體 REST API]
- [Azure 儲存體團隊部落格]

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[適用於 Java 的 Azure 儲存體 SDK]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure 儲存體用戶端 SDK 參考]: http://dl.windowsazure.com/storage/javadoc/
[Azure 儲存體 REST API]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
 

<!---HONumber=July15_HO2-->