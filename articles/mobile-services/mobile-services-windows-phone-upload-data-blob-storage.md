<properties 
	pageTitle="使用行動服務將影像上傳至 Blob 儲存體 (Windows Phone) | 行動服務" 
	description="了解如何使用行動服務將影像上傳至 Azure Blob 儲存體。" 
	documentationCenter="windows" 
	authors="ggailey777" 
	services="mobile-services" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# 使用行動服務將影像上傳到 Azure 儲存體

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##概觀

本主題說明如何使用 Azure 行動服務，讓您的應用程式能在 Azure 儲存體中上傳和存放使用者產生的影像。行動服務會使用 SQL Database 儲存資料。不過在 Azure Blob 儲存體服務中，二進位大型物件 (BLOB) 資料的儲存比較有效。

您無法使用用戶端應用程式安全地散佈認證，認證需要以安全的方式上傳資料至 Blob 儲存服務。相反地，您必須將認證儲存在行動服務中，並使用這些認證來產生用來上傳新影像的共用存取簽章 (SAS)。行動服務會安全地將 SAS (到期時間很短的認證，在此例中為 5 分鐘) 傳回給用戶端應用程式。應用程式會使用此暫存認證來上傳影像。在此範例中，來自 Blob 服務的下載是公開的。

在本教學課程中，您會在 [GetStartedWithData 範例應用程式專案](mobile-services-windows-phone-get-started-data.md)中新增功能，以便使用行動服務所產生的 SAS 來拍照並將影像上傳到 Azure。


##必要條件

本教學課程需要下列各項：

+ Microsoft Visual Studio 2012 Express for Windows 8 或更新版本
+ [Windows Phone SDK 8.0] (英文) 或更高版本
+ 已安裝適用於 Microsoft Visual Studio 的 Nuget Package Manager
+ [Azure 儲存體帳戶][How To Create a Storage Account]
+ 完成[將行動服務新增至現有的應用程式](mobile-services-windows-phone-get-started-data.md)教學課程  


##安裝適用於 Windows Phone 應用程式的儲存體用戶端

若要使用 SAS 將影像上傳至 Blob 儲存體，您必須先新增 NuGet 封裝，以安裝適用於 Windows Phone 應用程式的儲存體用戶端程式庫。

1. 在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下專案名稱，然後選取 [管理 NuGet 封裝]。

2. 在左側窗格中依序選取 [線上] 類別、[Include Prerelease]，搜尋 **WindowsAzure.Storage-Preview**，並按一下 [Azure Storage] 封裝上的 [安裝]，然後接受授權協定。

  	![新增 Azure 儲存體 NuGet](./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	這麼做會將 Azure 儲存體服務的用戶端程式庫新增至專案。

接著，您會更新快速入門應用程式以擷取和上傳影像。

##在管理入口網站中更新已註冊的插入指令碼


[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

>[AZURE.NOTE]如果要在 TodoItem 物件中新增屬性，您必須在行動服務中啟用動態結構描述。When Dynamic Schema is enabled, new columns are automatically added to the TodoItem table that map to these new properties.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]


##後續步驟

您現在已藉由整合行動服務與 Blob 服務，而能夠安全地上傳影像，請參閱其他後端服務和整合主題：

+ [使用 SendGrid 從行動服務傳送電子郵件]
 
  了解如何使用 SendGrid 電子郵件服務將電子郵件功能新增到行動服務本主題示範如何新增伺服器端指令碼，以使用 SendGrid 傳送電子郵件。

+ [在行動服務中排程後端工作]

  了解如何使用行動服務工作排程器功能，定義可依您定義的排程執行的伺服器指令碼。

##另請參閱

+ [行動服務伺服器指令碼參考]

  參考使用伺服器指令碼來執行伺服器端工作以及與其他 Azure 元件和外部資源整合的主題。
 
+ [行動服務 .NET 作法概念性參考]

  深入了解如何搭配使用行動服務與 .NET
  
<!-- Images. -->

<!-- URLs. -->
[使用 SendGrid 從行動服務傳送電子郵件]: store-sendgrid-mobile-services-send-email-scripts.md
[在行動服務中排程後端工作]: mobile-services-schedule-recurring-tasks.md
[行動服務伺服器指令碼參考]: mobile-services-how-to-use-server-scripts.md
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[行動服務 .NET 作法概念性參考]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Windows Phone SDK 8.0]: http://www.microsoft.com/download/details.aspx?id=35471


 

<!---HONumber=July15_HO1-->