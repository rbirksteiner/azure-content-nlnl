<properties 
	pageTitle="在 Windows 市集應用程式中使用 Azure 儲存體 | Azure" 
	description="了解如何使用 Azure Blob、佇列和資料表來儲存 Windows 市集應用程式的資料。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="tamram"/>





# 如何在 Windows 市集應用程式中使用 Azure 儲存體

## 概觀

本指南說明如何開始著手開發採用 Azure 儲存體的 Windows 市集應用程式。

## 下載所需工具 ##

- [Visual Studio 2012](http://msdn.microsoft.com/library/windows/apps/br211384) 可讓您輕鬆地建置、偵錯、當地語系化、封裝及部署 Windows 市集應用程式。
- [Azure Storage Client Library for Windows Runtime](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx) 提供可處理 Azure 儲存體的類別庫。
- [適用於 Windows 市集應用程式的 WCF 資料服務工具](http://www.microsoft.com/download/details.aspx?id=30714)利用 Visual Studio 2012 和更新版本中 Windows 市集應用程式的用戶端 OData 支援，擴充了新增服務參照體驗。

## 開發應用程式 ##

### 準備就緒

在 Visual Studio 2012 或更新版本中建立新的 Windows 市集應用程式專案：

![store-apps-storage-vs-project][store-apps-storage-vs-project]

接著，新增 Azure Storage Client Library 的參考，方法是以滑鼠右鍵按一下 [參考]，然後選擇 [加入參考]，並瀏覽到已下載的 Storage Client Library for Windows Runtime：

![store-apps-storage-choose-library][store-apps-storage-choose-library]

### 使用搭配 Blob 和佇列服務的程式庫

此時您的應用程式已準備好可呼叫 Blob 和佇列服務。新增下列 **using** 陳述式，以方便直接參考 Azure 儲存體類型：

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    
接著，新增頁面按鈕。將下列程式碼新增至其 **Click** 事件，並使用 [async 關鍵字](http://msdn.microsoft.com/library/vstudio/hh156513.aspx) (英文) 來修改事件處理常式方法。
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();
    
此程式碼假設您有兩個字串變數 *accountName* 和 *accountKey*，代表您儲存體帳戶的名稱和與該帳戶相關聯的帳戶金鑰。

建置並執行應用程式。按一下按鈕將會首先檢查您的帳戶中是否有名為 *container1* 的容器存在，如果沒有的話則建立容器。

### 使用搭配資料表服務的程式庫

用來與資料表服務通訊的類型會視適用於 Windows 市集應用程式程式庫的 WCF 資料服務而定。接著，透過使用 [Package Manager Console] 加入所需的 WCF 程式庫參考：

![store-apps-storage-package-manager][store-apps-storage-package-manager]

使用下列命令將 [Package Manager] 指向您機器上的位置：
    
    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

此命令會自動將所有所需的參考加入您的專案。如果您不想使用 [Package Manager Console]，您也可以將本機機器上的 WCF Data Services NuGet 資料夾新增至 [套件來源] 清單，然後透過 UI 加入參考，如[使用對話方塊管理 NuGet 套件](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog) (英文) 中所述。

當您參考 WCF 資料服務 NuGet 套件時，請變更按鈕 **Click** 事件中的程式碼：
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();
    
此程式碼會檢查您的帳戶中是否有名為 *table1* 的資料表存在，如果不存在，則請建立。

您也可以加入 Microsoft.WindowsAzure.Storage.Table.dll (您可以在下載的相同套件中找到) 的參考。此程式庫包含其他功能，例如反映式序列化和一般查詢。請注意，此程式庫不支援 JavaScript。



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png
 

<!---HONumber=July15_HO1-->