<properties 
	pageTitle="在入口網站中建立 Azure 搜尋服務索引" 
	description="在管理入口網站中填入欄位定義，即可為 Azure 搜尋服務新增索引。" 
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

# 在入口網站中建立 Azure 搜尋服務索引

只需在 Azure 管理入口網站中建立一個索引，即可快速地在 Azure 搜尋中建立一個索引原型。入口網站十分適合用來進行概念證明測試，對於已部署到您服務中的任何索引，您也可以透過入口網站檢視任一索引的結構描述定義和資源使用狀況。

若要完成此工作，請先確定您已擁有可供使用的 Azure 搜尋服務。如果您需要設定 Azure 搜尋服務的協助，請參閱[在入口網站中建立 Azure 搜尋服務](search-create-service-portal.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 開啟 Azure 搜尋服務的服務儀表板。以下提供一些尋找儀表板的方法。
	- 在 Jumpbar 中，按一下 [首頁]****。首頁上有您訂用帳戶中每個服務的磚。按一下磚即可開啟服務儀表板。
	- 只需在 Jumpbar 中，依序按一下 [瀏覽]**** | [篩選依據]**** | [搜尋服務]****，即可在清單中找到您的「搜尋」服務。 

3. 您將會在服務儀表板的頂端看見命令列，其中包括 [新增索引]****。
	
	查看定價層。如果您使用免費版，您最多可以有 3 個索引。您可能需要刪除一個索引以釋放空間。

     ![][1]

4. 若要刪除索引，請按一下索引即可滑動開啟刀鋒視窗。按一下 [刪除]****。

     ![][2]

5. 若要在入口網站中建立新的索引，請按一下 [新增索引]**** 並為其命名，例如 *hotels*。

	建立索引可能需要一些時間，一旦可供使用時，就會出現在索引清單上。

6. 按一下 *hotels* 即可開啟索引定義刀鋒視窗。

	當您在入口網站中建立索引時，將會為您建立所需欄位 (id)。此為索引鍵欄位，用於唯一識別各個文件。每個索引鍵 (不包括複合索引鍵) 只有一個欄位，而且一律為字串。

	如果您想在建立索引期間重新命名索引鍵欄位，那麼務必立即執行此步驟。索引建立之後，您就無法重新命名該欄位。

	![][3]

7. 若要編輯欄位名稱，請按一下欄位清單中的向右箭號。

8. 將 *id* 取代為 *hotelId*。

9. 按一下各個刀鋒視窗 (欄位和索引) 上的 [確定]****，即可建立索引。

##新增欄位

在 Azure 搜尋中，預設會啟用索引屬性，像是可搜尋的、可建立 Facet 屬性的，以及可篩選的。當您設定這些屬性時，通常會關閉不合理的搜尋行為 (例如對說明排序或建立 Facet 屬性)。

入口網站則不同。在入口網站中，預設會關閉搜尋行為，以便您可以逐項為不同欄位選取所有適用的行為。

1. 按一下 [新增/編輯欄位]**** 即可新增更多欄位。在此練習中，我們將重新建立 *hotels* 索引 (在[如何搭配 Azure 搜尋服務使用 Fiddler](search-fiddler.md) 一文中提及)。 

	![][4]

2. 新增並設定欄位以完成結構描述。

	![][5]

	如需欄位名稱和類型的參考資訊，請參閱[命名規則](https://msdn.microsoft.com/library/azure/dn857353.aspx)和[支援的資料類型](https://msdn.microsoft.com/library/azure/dn798938.aspx)。

3. 按一下頁面頂端的 [儲存]****。

  	![][6]

##後續步驟

雖然已定義索引，但仍需事先載入文件才能供人使用。若要輕鬆執行此操作，請繼續執行[如何搭配 Azure 搜尋服務使用 Fiddler](search-fiddler.md) 一文中的**載入文件**部分。接著您可以按照該篇文章中的剩餘步驟執行一些查詢。

一旦您熟悉基本的索引，請考慮新增語言分析器或建議工具，以新增多語言支援或預先輸入建議。索引結構描述中提供這兩項功能的詳細說明。如需詳細資訊，請參閱[語言支援](https://msdn.microsoft.com/elibrary/azure/dn879793.aspx)和[建立索引](https://msdn.microsoft.com/library/azure/dn798941.aspx)。

<!--Image references-->
[1]: ./media/search-create-index-portal/AzureSearch-PortalIndex-1.PNG
[2]: ./media/search-create-index-portal/AzureSearch-PortalIndex-2.PNG
[3]: ./media/search-create-index-portal/AzureSearch-PortalIndex-3.PNG
[4]: ./media/search-create-index-portal/AzureSearch-PortalIndex-4.PNG
[5]: ./media/search-create-index-portal/AzureSearch-PortalIndex-5.PNG
[6]: ./media/search-create-index-portal/AzureSearch-PortalIndex-6.PNG
<!--HONumber=54--> 