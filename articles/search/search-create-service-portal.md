<properties 
	pageTitle="在入口網站中建立 Azure 搜尋服務" 
	description="使用管理入口網站將免費版或標準版的 Azure 搜尋服務新增到現有的訂用帳戶中" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="04/27/2015" 
	ms.author="heidist"/>

# 在入口網站中建立 Azure 搜尋服務

Microsoft Azure 搜尋服務是全新推出的服務，可讓您將搜尋功能內嵌到自訂的應用程式中。此服務可為您的搜尋資料提供搜尋引擎和儲存體，您可以透過入口網站、.NET SDK 或 REST API 存取和管理這類資料。主要功能包括自動完成查詢、模糊比對、符合結果醒目提示、多面向導覽和多語言支援。如需有關搜尋服務的詳細資訊，請參閱 [Azure 搜尋服務簡介](../fundamentals-azure-search-chappell/)。

##將搜尋服務加到您的訂用帳戶中  

身為管理員，如果選擇使用共用的服務，您可以免費將搜尋服務加到現有的訂用帳戶中，如果選擇使用專用資源，則可以按照標準費率新增搜尋服務。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 Jumpbar 中依序按一下 [新增]**** | [資料 + 儲存體]**** | [搜尋]****。
 
     ![][1]

3. 設定服務名稱、定價層、資源群組、訂用帳戶及位置。這些設定都是必須設定的項目，且在佈建服務後就不能變更。

     ![][2]

	- **服務名稱**必須是唯一的、小寫、少於 15 個字元，且不得包含空格。此名稱會成為您 Azure 搜尋服務端點的一部分。如需命名慣例的詳細資訊，請參閱[命名規則](https://msdn.microsoft.com/library/azure/dn857353.aspx)。 
	
	- **定價層**決定可用容量和計費方式。以下兩個定價層提供相同的功能，但所處的資源層級不同。
	
		- **免費版**是在與其他訂戶共用的叢集上執行。此版本所提供的容量，已足夠用來試驗教學課程以及撰寫用於證明概念的程式碼，但並不適用於生產應用程式。部署免費版服務通常只需要幾分鐘時間。
		- **標準版**是在專用的資源上執行作業，且具備高度可擴充性。標準版服務一開始是以一個複本和一個資料分割進行佈建，不過您可以在建立服務後調整容量。部署標準版服務需要較長的時間，通常需要大約 15 分鐘時間。
	
	- **資源群組**是適用於一般用途之服務和資源的容器。例如，如果您以 Azure 搜尋、Azure 網站和 Azure BLOB 儲存體為基礎建立自訂搜尋應用程式，那麼您可以建立資源群組，來將這些服務一起放在入口網站的管理頁面中。
	
	- **訂用帳戶**可讓您在多個訂用帳戶間選擇 (如果您有多個訂用帳戶)。
	
	- **位置**是資料中心區域。目前，所有資源都必須在同一個資料中心內執行。不支援將資源分配到多個資料中心內。

4. 按一下 **** [建立] 即可佈建服務。

注意 Jumpbar 中的通知。服務一旦準備就緒可供使用時就會顯示通知。

<a id="sub-2"></a>
##尋找 Azure 搜尋服務的服務名稱和 API 金鑰

建立服務之後，可以返回入口網站取得 URL 或 `api-key`。如果想要連接至搜尋服務，您必須同時擁有 URL 和 `api-key` 才能驗證呼叫。

1. 在 Jumpbar 中按一下 [首頁]****，然後按一下搜尋服務以開啟服務儀表板。 

2. 您會在服務儀表板上看到基本資訊磚，以及存取系統管理金鑰的鑰匙圖示。

  	![][3]

3. 複製服務 URL 和系統管理金鑰，進到下一個工作 ([測試服務作業](#sub-4)) 時，您將會需要這些資料。

<a id="sub-3"></a>
##升級至標準版定價層

許多客戶一開始會使用免費版服務，但是當查詢效能或儲存體不足以支應所需執行的工作負載時，就會升級至標準版定價層。標準版定價層會在 Azure 資料中心內提供專屬於您的專用資源。搜尋作業同時需要儲存體複本和服務複本。當您註冊標準搜尋時，您可以將服務設定最佳化，以使用更多對您的狀況來說最重要的任何資源。

若要使用標準版定價層，請重複本文先前步驟，以建立新的搜尋服務，並選擇標準版定價層。請注意，設定專用資源可能需要一些時間，可能長達 15 分鐘或更長的時間。

免費版沒有就地升級選項。切換至標準版 (透過其調整規模的潛能) 需要用到新的服務。您將需要重新載入搜尋應用程式所使用的索引和文件。

標準版定價層中的搜尋服務會各以一個複本和資料分割建立，但可輕鬆地在更高的資源層級中重新調整規模。

1.	建立服務後，請返回服務儀表板。 

2.	按一下 [調整]**** 磚。

3.	使用滑桿新增副本和 (或) 資料分割。

額外的複本和資料分割會依搜尋單位計費。當您新增資源時，頁面上會顯示要支援任何特定資源設定所需要的搜尋單位總數。

您可以查看[定價詳細資料](http://go.microsoft.com/fwlink/p/?LinkID=509792)以取得每單位的計費資訊。有關如何設定資料分割和複本的組合，請參閱[限制和條件約束](http://msdn.microsoft.com/library/azure/dn798934.aspx)以協助您決定設定方式。

<a id="sub-4"></a>
##測試服務作業

設定搜尋服務的最後一個步驟是，確認您的服務可否從用戶端應用程式上操作和存取。您可以利用下方任一連結，以無程式碼方式驗證服務的可用性。

- [如何搭配 Azure 搜尋服務使用 Chrome Postman](../search-chrome-postman/)
- [如何搭配 Azure 搜尋服務使用 Telerik Fiddler](../search-fiddler/)

<!--Next steps and links -->
<a id="next-steps"></a>
##後續步驟

下列附加資料會顯示如何建置和管理使用 Azure 搜尋服務的搜尋應用程式。

- [如何以 .NET 使用 Azure 搜尋服務](../search-howto-dotnet-sdk/)

- [在 Microsoft Azure 中管理搜尋方案](../search-manage/)

- [在 MSDN 上的 Azure 搜尋服務](http://msdn.microsoft.com/library/dn798933.aspx)

- [第 9 頻道影片：Azure 搜尋服務簡介](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)


<!--Anchors-->
[Find the service name and api-keys of your Azure Search service]: #sub-2
[Upgrade to standard tier]: #sub-3
[Test service operations]: #sub-4
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG


<!--HONumber=54--> 