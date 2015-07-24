<properties
	pageTitle="建立媒體服務帳戶 - Azure"
	description="說明如何在 Azure 中建立新的媒體服務帳戶。"
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="04/20/2015"
	ms.author="juliako"/>


#如何建立媒體服務帳戶

> [AZURE.SELECTOR]
- [Portal](media-services-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](https://msdn.microsoft.com/library/azure/dn167014.aspx)

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)和[媒體服務即時資料流工作流程](media-services-live-streaming-workflow.md)系列的一部分。

Azure 管理入口網站提供一種方法來快速建立 Azure 媒體服務帳戶。您可以使用自己的帳戶，來存取讓您在 Azure 中儲存、加密、編碼、管理和串流播放媒體內容的媒體服務。當您建立媒體服務帳戶時，您也會在與媒體服務帳戶相同的地理區域中建立相關聯的儲存體帳戶 (或使用現有儲存體帳戶)。

本主題說明如何使用「快速建立」方法建立新的媒體服務帳戶，然後建立它與儲存體帳戶的關聯。

##<a id="concepts"></a>概念
存取媒體服務時需要有兩個相關聯的帳戶：

-   **媒體服務帳戶**。您的帳戶可讓您存取 Azure 中提供的一組雲端型媒體服務。媒體服務帳戶並不會儲存實際媒體內容。而是在您的帳戶中儲存媒體內容和媒體處理工作的中繼資料。當您建立帳戶時，您會選取一個可用的媒體服務區域。所選取的區域會是儲存您帳戶之中繼資料記錄的資料中心。

    > [AZURE.NOTE]**北歐****西歐****美國西部****美國東部****東南亞****東亞****日本西部****日本東部**
-   **相關聯的儲存體帳戶**。您的儲存體帳戶就是與媒體服務帳戶相關聯的 Azure 儲存體帳戶。儲存體帳戶提供媒體檔案的 Blob 儲存體，而且必須與媒體服務帳戶位於相同的地理區域中。建立媒體服務帳戶時，可以選擇相同區域中的現有儲存體帳戶，也可以在相同區域中建立新的儲存體帳戶。如果您刪除媒體服務帳戶，並不會刪除相關儲存體帳戶中的 Blob。

##<a id="quick"></a>如何：使用「快速建立」建立媒體服務帳戶

1. 在[管理入口網站][]中，依序按一下 [新增]、[媒體服務] 和 [快速建立]。

	![Media Services Quick Create](./media/media-services-create-account/wams-QuickCreate.png)

2. 在 [名稱] 中，輸入新帳戶的名稱。媒體服務帳戶名稱是全部小寫且不含空格的數字或字母，而且長度是 3 到 24 個字元。

3. 在 [區域] 中，選取將用來儲存您媒體服務帳戶之中繼資料記錄的地理區域。只有可用的媒體服務區域才會出現在下拉式清單中。

4. 在 [儲存體帳戶] 中，選取儲存體帳戶以從媒體服務帳戶提供媒體內容的 Blob 儲存體。您可以選取與媒體服務帳戶相同地理區域中的現有儲存體帳戶，也可以建立新的儲存體帳戶。新的儲存體帳戶會建立於相同的區域中。

5. 如果您已建立新的儲存體帳戶，請在 [NEW STORAGE ACCOUNT NAME] 中輸入儲存體帳戶的名稱。儲存體帳戶名稱的規則會與媒體服務帳戶相同。

6. 按一下表單底部的 [快速建立]。

	您可以在視窗底端的訊息區域監視程序的狀態。

	當順利建立帳戶時，狀態會變更為 [使用中]。[媒體服務] 頁面隨即開啟，其中顯示新的帳戶。

	在頁面底部會顯示 [管理金鑰] 按鈕。當您按一下此按鈕時，會顯示對話方塊，其中具有媒體服務帳戶名稱和主要和次要金鑰。您將需要帳戶名稱和主要金鑰資訊，以便以程式設計方式存取媒體服務帳戶。


	![Media Services Page](./media/media-services-create-account/wams-mediaservices-page.png)

	當您按兩下帳戶名稱時，預設會顯示 [快速入門] 頁面。此頁面可讓您執行一些在入口網站的其他頁面也可以執行的管理工作。例如，您可以從此頁面上傳視訊檔案，也可以從 [內容] 頁面上傳視訊檔案。

	此外，您還可以檢視使用 Azure Media Services SDK 來完成下列工作的程式碼：上傳、編碼和發行影片。您可以按一下 [WRITE SOME CODE] 區段下的其中一個連結，並複製程式碼，然後將該程式碼用在您的應用程式中。


<!-- Reusable paths. -->

<!-- Anchors. -->
  [Concepts]: #concepts
  [Before you begin]: #begin
  [How to: Create a Media Services account using Quick Create]: #quick

<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386

  [管理入口網站]: http://manage.windowsazure.com/
 

<!---HONumber=July15_HO2-->