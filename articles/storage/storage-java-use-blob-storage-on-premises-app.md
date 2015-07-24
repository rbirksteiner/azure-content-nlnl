<properties 
	pageTitle="搭配 Blob 儲存體 (Java) 的內部部署應用程式 | Microsoft Azure" 
	description="了解如何建立可將影像上傳至 Azure，然後在瀏覽器中顯示此影像的主控台應用程式。程式碼範例以 Java 撰寫。" 
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="robmcm"/>

# 搭配 Blob 儲存體的內部部署應用程式

## 概觀

下列範例說明如何使用 Azure 儲存體，在 Azure 中儲存影像。以下程式碼適用於主控台應用程式，可將影像上傳至 Azure，然後建立可在瀏覽器中顯示此影像的 HTML 檔案。

## 必要條件

1.  已安裝 Java Developer Kit (JDK) 1.6 版或更新版本。
2.  已安裝 Azure SDK。
3.  已安裝 Azure Libraries for Java 的 JAR 和任何適用的相依性 JAR，且位於 Java 編輯器所使用的組建路徑中。如需有關安裝 Azure Libraries for Java 的詳細資訊，請參閱 [下載 Azure SDK for Java] (英文)。
4.  已設定 Azure 儲存體帳戶。以下程式碼將會使用此儲存體帳戶的帳戶名稱和帳戶金鑰。如需建立儲存體帳戶的詳細資訊，請參閱[如何建立儲存體帳戶] (英文)，如需擷取帳戶金鑰的詳細資訊，請參閱[如何管理儲存體帳戶] (英文)。
5.  您已建立一個已命名並儲存於路徑 c:\myimages\image1.jpg 中的本機影像檔案。或者，修改範例中的 **FileInputStream** 建構函式以使用其他影像路徑和檔案名稱。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 使用 Azure Blob 儲存體上傳檔案

我們將在此提供逐步程序；如果想要跳過這些步驟，我們將在本主題後面提供完整程式碼。

透過包含匯入 Azure 核心儲存體類別、Azure Blob 用戶端類別、Java IO 類別和 **URISyntaxException** 類別來開始程式碼：

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

宣告名為 **StorageSample** 的類別，並加上左括號 **{**。

    public class StorageSample {

在 **StorageSample** 類別中，宣告將包含預設端點通訊協定、儲存體帳戶名稱和儲存體存取金鑰 (如您 Azure 儲存體帳戶中所指定) 的字串變數。分別以您自己的帳戶名稱和帳戶金鑰取代預留位置值 **your_account_name** 和 **your_account_key**。

    public static final String storageConnectionString = 
           "DefaultEndpointsProtocol=http;" + 
               "AccountName=your_account_name;" + 
               "AccountKey=your_account_name"; 

將您的宣告加入 **main** 中，包括 **try** 區塊，以及加上必要的左括號 **{**。

    public static void main(String[] args) 
    {
        try
        {

宣告下列類型的變數 (以下說明乃是針對他們在此範例中的用途)：

-   **CloudStorageAccount**：可用來以 Azure 儲存體帳戶名稱和金鑰初始化帳戶物件，並可用來建立 Blob 用戶端物件。
-   **CloudBlobClient**：可用來存取 Blob 服務。
-   **CloudBlobContainer**：可用來建立 Blob 容器、列出容器中的 Blob，和刪除容器。
-   **CloudBlockBlob**：可用來將本機影像檔案上傳至容器。

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

對 **account** 變數指定值。

    account = CloudStorageAccount.parse(storageConnectionString);

對 **serviceClient** 變數指定值。

    serviceClient = account.createCloudBlobClient();

對 **container** 變數指定值。我們將取得名為 **gettingstarted** 的容器參照。

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

// 建立容器。如果容器不存在，此方法將會建立容器 (並傳回 **true**)。如果容器存在，則會傳回 **false**。**createIfNotExists** 的替代方式是 **create** 方法 (如果容器已存在，將傳回錯誤訊息)。

    container.createIfNotExists();

設定容器的匿名存取。

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

取得區塊 Blob 的參照，此參照將可代表 Azure 儲存體中的 Blob。

    blob = container.getBlockBlobReference("image1.jpg");

使用 **File** 建構函式，來取得在本機建立且即將上傳之檔案的參照。(執行此程式碼之前，請確定您已建立此檔案。)

    File fileReference = new File ("c:\myimages\image1.jpg");

透過呼叫 **CloudBlockBlob.upload** 方法來上傳本機檔案。**CloudBlockBlob.upload** 方法的第一個參數是 **FileInputStream** 物件，代表將上傳至 Azure 儲存體的本機檔案。第二個參數是檔案的大小 (位元組)。

    blob.upload(new FileInputStream(fileReference), fileReference.length());

呼叫名為 **MakeHTMLPage** 的協助程式函數來製作基本的 HTML 頁面，此頁面將包含 **&lt;image&gt;** 元素，且來源設定為目前位於您 Azure 儲存體帳戶中的 Blob。(我們將在本主題後面討論 **MakeHTMLPage** 的程式碼。)

    MakeHTMLPage(container);

列印關於已建立之 HTML 頁面的狀態訊息和相關資訊。

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

透過插入右括號 **}** 來結束 **try** 區塊

處理下列例外狀況：

-   **FileNotFoundException**：**FileInputStream** 或 **FileOutputStream** 建構函式可能擲回的例外狀況。
-   **StorageException**：Azure 用戶端儲存體程式庫可能擲回的例外狀況。
-   **URISyntaxException**：**ListBlobItem.getUri** 方法可能擲回的例外狀況。
-   **Exception**：一般例外狀況處理。

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

透過插入右括號 **}** 來結束 **main**

建立名為 **MakeHTMLPage** 的方法，藉此建立基本的 HTML 頁面。此方法包含參數類型 **CloudBlobContainer**，可用來逐一取得已上傳的 Blob 清單。此方法將擲回例外狀況類型 **FileNotFoundException** (**FileOutputStream** 建構函式可能擲回的例外狀況) 和 **URISyntaxException** (**ListBlobItem.getUri** 方法可能擲回的例外狀況)。加上左括號 **{**。

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

建立名為 **index.html** 的本機檔案。

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

寫入本機檔案，加入 **&lt;html&gt;**、**&lt;header&gt;** 和 **&lt;body&gt;** 元素。

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

逐一取得已上傳的 Blob 清單。在 HTML 頁面中，為每個 Blob 建立一個 **&lt;img&gt;** 元素，並將其 **src** 屬性傳送至 Azure 儲存體帳戶中之 Blob 的 URI。雖然您在此範例中僅新增一張影像，如果您要新增多張，此程式碼需要重複列舉全部影像。

為了方便起見，此範例假設每個已上傳的 Blob 是一張影像。如果您已更新除了影像以外的 Blob，或分頁 Blob 而非區塊 Blob，則請視需要調整程式碼。

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

結束 **&lt;body&gt;** 元素和 **&lt;html&gt;** 元素。

    stream.println("</body>");
    stream.println("</html>");

關閉本機檔案。

    stream.close();

透過插入右括號 **}** 來結束 **MakeHTMLPage**

透過插入右括號 **}** 來結束 **StorageSample**

下列是此範例的完整程式碼。請記得修改預留位置值 **your_account_name** 和 **your_account_key**，以便分別使用您的帳戶名稱和帳戶金鑰。

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor 
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" + 
                       "AccountKey=your_account_name"; 

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\myimages\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

除了將本機影像檔案上傳至 Azure 儲存體以外，此範例程式碼還會建立本機檔案 namedindex.html，您可在瀏覽器中開啟此檔案以查看已上傳影像。

因為此程式碼包含您的帳戶名稱和帳戶金鑰，請確定您的來源程式碼安全無虞。

## 刪除容器

由於您需支付儲存體的費用，因此，在您完成體驗此範例之後，您可能會想要刪除 **gettingstarted** 容器。若要刪除容器，請使用 **CloudBlobContainer.delete** 方法：

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

若要呼叫 **CloudBlobContainer.delete** 方法，初始化 **CloudStorageAccount**、**ClodBlobClient**、**CloudBlobContainer** 物件的程序與為 **createIfNotExist** 方法所顯示的程序相同。下列是刪除名為 **gettingstarted** 之容器的完整範例。

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString = 
                "DefaultEndpointsProtocol=http;" + 
                   "AccountName=your_account_name;" + 
                   "AccountKey=your_account_key"; 

        public static void main(String[] args) 
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                
                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();
                
                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

如需其他 Blob 儲存體類別和方法的概觀，請參閱[如何使用 Java 的 Blob 儲存體服務] (英文)。

## 後續步驟

請遵循下列連結以深入了解更複雜的儲存體工作。

- [Azure Storage SDK for Java]
- [Azure 儲存體用戶端 SDK 參考]
- [Azure 儲存體 REST API]
- [Azure 儲存體團隊部落格]

  [Download the Azure SDK for Java]: http://azure.microsoft.com/develop/java/
  [如何建立儲存體帳戶]: storage-create-storage-account.md#create-a-storage-account
  [如何管理儲存體帳戶]: storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys
  [如何使用 Java 的 Blob 儲存體服務]: storage-java-how-to-use-blob-storage.md
  [Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
  [Azure 儲存體用戶端 SDK 參考]: http://dl.windowsazure.com/storage/javadoc/
  [Azure 儲存體 REST API]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
 

<!---HONumber=July15_HO1-->