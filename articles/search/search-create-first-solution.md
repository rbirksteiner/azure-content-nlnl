<properties 
	pageTitle="使用 Azure 搜尋服務建立您的第一個搜尋解決方案" 
	description="使用 Azure 搜尋服務建立您的第一個搜尋解決方案" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="07/08/2015" 
	ms.author="heidist"/>

# 使用 Azure 搜尋建立您的第一個搜尋解決方案

此範例將說明 Adventure Works 資源回收公司的搜尋應用程式。完成本教學課程後，您將取得附有豐富搜尋使用性的線上產品目錄，其中包括 Facet 導覽、多個可用於搜尋結果的排序選項，以及預先輸入查詢建議。

   ![][7]

此示範將引導您完成下列練習，以開始使用 Azure Search：

+	建立 Azure Search 索引。
+	從 SQL Server Database 將文件 (資料) 載入 Azure Search 索引中。
+	建置使用 Azure Search 的 ASP.NET MVC4 應用程式，以：
	+	搜尋及顯示 Azure 搜尋服務索引的結果
	+	在輸入搜尋詞彙時，在 [搜尋] 方塊中顯示預先輸入建議
	+	使用 Facet 篩選搜尋結果


<a id="sub-1"></a>
## 必要條件

+	[Azure 訂用帳戶](../includes/free-trial-note.md)。如果尚未準備好註冊試用版訂用帳戶，則您可以略過本教學課程，並改為選擇 [試用 Azure App Service](https://tryappservice.azure.com/)。這個替代選項免費提供您 Azure 搜尋服務與 ASP.NET Web 應用程式 - 每個工作階段一小時 - 不需有訂用帳戶。
+	Visual Studio 2012 或更高版本，並安裝 ASP.NET MVC 4 與 SQL Server。如果您尚未安裝此軟體，可以下載免費的 Express 版：[Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) 和 [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx)。
+	Azure 搜尋服務。您需要搜尋服務名稱，以及系統管理金鑰。如需詳細資料，請參閱[在入口網站中建立 Azure 搜尋服務](search-create-service-portal.md)。
+	[CodePlex 上的 Adventure Works Azure Search 示範專案](http://go.microsoft.com/fwlink/p/?LinkID=510972)。在 [來源] 索引標籤上，按一下 [下載 ]取得解決方案的壓縮檔。 

    ![][12]


此解決方案內含兩個專案：

+	**CatalogIndex** 會建立 Azure Search 索引，並從專案包含的 SQL Server Database 中載入資料。
+	**AdventureWorksWeb** 是一項會查詢 Azure Search 索引的 MVC4 應用程式。本教學課程假設您已具備 ASP.NET MVC 的使用知識。

<a id="sub-2"></a>
## 建立 Azure Search 索引

在此步驟中，您將根據 AdventureWorks SQL Server Database 中的資料，使用 **CatalogIndex** 新建名為 "catalog" 的 Azure Search 索引。

1.	在 Visual Studio 中，開啟名為 **AdventureWorksCatalog.sln** 的 Azure 搜尋服務示範方案。  
2.	在 [方案總管] 中以滑鼠右鍵按一下 **CatalogIndexer**，然後選取 [設為起始專案]，使您在按 **F5** 時會執行此應用程式，而不是 **AdventureWorksWeb** 專案。
3.	開啟此專案中的 **App.config**，然後將 "SearchServiceName" 和 "SearchServiceApiKey" 的值更新為您 Azure Search 服務的值。在搜尋服務名稱上，如果您的服務是 "mysearch.search.windows.net"，則您需要輸入 "mysearch"。
4.	App.config 會選擇性地包含採用 SQL Server 2014 Express LocalDB (Server=(LocalDB)\\v11.0) 的 "SourceSqlConnectionString" 項目。如果您使用不同版本的 SQL Server，請據以更新伺服器名稱。例如，如果您具有本機預設執行個體，您可以使用 local 或 localhost。
5.	儲存 **App.config**。
6.	按 **F5** 鍵啟動專案。

命令提示字元應會開啟並顯示下列文字：「正在建立索引...」

一段時間後，作業應會完成並顯示文字：「完成。按 <enter> 繼續作業：」

   ![][8]

按 **Enter** 以關閉應用程式。至此，您已成功建立 Azure Search 索引。

> [AZURE.NOTE]如果出現包含「金鑰 'attachdbfilename' 的值無效」的錯誤，或是其他資料庫附加錯誤，表示可能有 UAC 衝突發生。根據此示範的用途，請執行下列動作來因應這些錯誤：將方案複製到可存取已驗證使用者的新資料夾或現有資料夾 (例如 Temp)。使用 [**以系統管理員身分執行**] 啟動 Visual Studio。開啟方案、建置方案，然後按 **F5** 來建立索引。

若要驗證索引建立和文件上傳是否成功，請移至 [Azure 管理入口網站](https://portal.azure.com)中的搜尋服務儀表板。在 [Usage] 中，索引計數應該會增加一個，而您應有 294 個文件，資料庫中的每項產品各一個。

按一下 [索引] 磚以顯示索引清單。索引清單會列出新索引和文件計數。請注意，您最多可在免費定價層具有三個索引。如果已有三個索引，您必須刪除一個，以釋出空間給任何新的索引。

   ![][9]


<a id="sub-3"></a>
## 探索 CatalogIndexer

現在我們要仔細觀察 **CatalogIndexer** 專案，以了解其運作方式。

1.	從 [方案總管] 中開啟 **Program.cs**，然後移至 `Main(string[] args)` 函數。

    請注意，此函數如何根據您的 Azure 搜尋服務建置名為 `_serviceURI` 的 URI，然後建立名為 `_httpClient` 的 HttpClient，而使用您的 API 金鑰向此搜尋服務進行驗證。

2.	`ChangeEnumeratorSql` 和 `ChangeSet` 可用來從您的 SQL Server AdventureWorks Database 中的 Products 資料表列舉資料。

3.	接著將會根據從這個資料表收集到的資料呼叫 `ApplyChanges`，以將這項資料套用至您的 Azure Search 索引。

4.	移至相同檔案中的 `ApplyChanges`。請注意，此函數如何刪除已存在的索引 (`DeleteCatalogIndex`)，然後建立名為 "catalog" 的新索引 (`CreateCatalogIndex`)。

5.	移至 `CreateCatalogIndex` 函數，然後觀察如何使用與 SQL Server 中的 Products 資料表資料行相符的結構描述建立索引。每個欄位都有一個類型 (即 `Edm.String` 或 `Edm.Double`，以及定義這些欄位之用途的屬性。如需這些屬性的詳細資訊，請參閱 [Azure Search REST API 文件](http://msdn.microsoft.com/library/azure/dn798935.aspx)。

6.	返回 `ApplyChanges` 函數。請留意此函數如何對列舉的變更 `ChangeSet` 中的所有資料進行迴圈。變更不會逐一套用，而會分成以 1000 個為單位的群組，再套用至搜尋服務。這會比逐一套用文件有效率得多。

您已了解如何使用 SQL Server 中的關聯式資料來建立及填入索引，現在我們將討論如何建置使用搜尋資料的產品目錄。

<a id="sub-4"></a>
## 使用 Azure Search 建置 MVC4 應用程式

在此步驟中，您將在網頁瀏覽器中建置及執行搜尋應用程式。

1.	在 Visual Studio 中，開啟名為 **AdventureWorksCatalog.sln** 的 Azure 搜尋示範解決方案。  

2.	以滑鼠右鍵按一下 [方案總管] 的 **AdventureWorksWeb**，然後選取 [設定為啟始專案]。

3.	開啟此專案中的 **Web.config**，然後將 "SearchServiceName" 和 "SearchServiceApiKey" 的值更新為您 Azure Search 服務的值。在搜尋服務名稱上，如果您的服務是 "mysearch.search.windows.net"，則您需要輸入 "mysearch"。

4.	儲存 **Web.config**。

5.	按 **F5** 鍵啟動專案。如果出現組建錯誤，請遵循以下[疑難排解](#err-mvc)步驟。

    ![][10]

6.	將搜尋方塊保留為空白，然後按一下 [搜尋] 以全數傳回 294 項產品。

7.	請留意位於左側的 Facet 清單。嘗試按一下其中一個 Facet。搜尋會重新執行，但這次會新增您所選擇的 Facet 以篩選結果。您可以在 **HomeController.cs**`Search` 函數的第一行加入中斷點，以逐步執行 (F11) 每一行。

7.	輸入搜尋詞彙，例如 "mountain bikes"。當您輸入時，請留意到建議查詢的下拉式清單。

    ![][11]

此示範剛開始時的螢幕擷取畫面會在下方再次出現，以提醒您找到了哪些項目。在前幾節中，我們討論了 Facet 導覽 (位於左側)，位於頁面頂端的文件計數、建議，以及依關聯性、清單或價格排序的排序選項。

   ![][7]


<a id="sub-5"></a>
## 探索 AdventureWorksWeb

專案 AdventureWorksWeb 將說明如何使用 ASP.NET MVC 4 與 Web 應用程式中的 Azure Search 互動。在本節裡，我們將檢視個別的應用程式碼部分，了解它們執行了哪些作業。

1.	在 [方案總管] 中展開 **AdventureWorksWeb** | **Controller**，然後開啟 **HomeController.cs**

    這是 MVC 控制器，可管理從 [索引] 檢視進行的互動。在此控制器的頂端，您會看見 `CatalogSearch _catalogSearch` 的參考，此參考可建立對 Azure Search 服務的 HttpClient 連線物件。此`CatalogSearch` 的程式碼位於 **CatalogSeach.cs** 中

2. **HomeController.cs** 內有兩個主要函數：

	**搜尋**：當使用者按一下搜尋按鈕或選擇 Facet 時，即會呼叫此函數，以擷取結果並將其傳回至要顯示的 [索引] 檢視。

	**建議**：當使用者在 [搜尋] 方塊中輸入時，即會呼叫此函數，以根據您的Azure 搜尋服務索引內容傳回建議清單。

我們將深入詳細探索這兩個函數。

3.	在**HomeController.cs**`Search` 函數中，您會看見 `_catalogSearch.Search` 的呼叫，其中包含您 Azure Search 服務的連線物件。當您呼叫位於 **CatalogSearch.cs** 中的搜尋函數時，您可以看見此函數使用這些參數建置 Azure 搜尋服務查詢。查詢的結果會儲存在名為 `result` 的 JSON 物件中，然後傳回至會在網頁中剖析及顯示結果的 `Index` 檢視。

4.	在 [檢視 | 首頁] 下開啟 **Index.cshtml**，您會看見用來剖析這些結果的程式碼。

5.	如果應用程式仍在執行中，請加以停止，然後在 [檢視 | 首頁] 下開啟 **Index.cshtml** 檔案。在此檔案的結尾處，您會看見使用 `JQuery $(function ())` 的 JavaScript 函數。在頁面載入時，將會呼叫此函數。它會使用 JQuery 自動完成函數，並將此函數連結作為來自文字方塊的回呼 (識別為 "q")。只要有人在文字方塊中輸入即會呼叫此自動建議函數，進而使用輸入的內容呼叫 /home/suggest。`/home/suggest` 是 **HomeController.cs** 中名為 `Suggest` 之函數的參考。

6.	開啟 **HomeController.cs**，然後移至建議函數。此程式碼與搜尋函數非常類似，會使用 `_catalogSearch` 物件呼叫 **CatalogSearch.cs** 中名為 `Suggest` 的函數。`Suggest` 函數並不會執行搜尋查詢，而是會呼叫[建議 API](http://msdn.microsoft.com/library/azure/dn798936.aspx)。此 API 會使用在文字方塊中輸入的詞彙，並建置可能建議的清單。值會傳回至 **Index.cshtml** 檔案，並自動列在 [搜尋] 方塊中作為預先輸入選項。

至此，您可能會好奇 Azure Search 如何得知應為哪些欄位建置建議。此問題的答案就在您先前建立索引的過程中。在 **CatalogIndexer** 專案之 Program.cs 檔案內的 `CreateCatalogIndex` 函數中，有一個名為 `Suggestions` 的屬性。只要此屬性設為 `True`，即表示 Azure Search 可使用此屬性作為擷取建議的欄位。

我們就來試試看。

7.	再次建置應用程式，然後按 **F5** 以執行應用程式。

8.	在搜尋方塊中，輸入 "Road" 這個字。您應可隨即看見項目清單顯示在文字方塊下方，並附有可供使用者選擇的建議。

您可以在 **HomeController.cs** 內的 `Suggest` 中加入中斷點，以逐步執行 (F11) 為了建置建議清單而發出的每個呼叫。

示範就到此為止。現在您已經逐一檢視過所有需要知道的主要作業流程，而可以開始建置使用 Azure Search 的 ASP.NET MVC4 應用程式。


<a id="err-mvc"></a>
## 如何解決「無法載入檔案或是組件 'System.Web.Mvc」

在打造 AdventureWorksWeb 時，如果出現「無法載入檔案或是組件 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 或其中一項相依性」的訊息時，請嘗試以下步驟來解決這個錯誤。

1. 開啟 [封裝管理員主控台]：[**工具**] | [**NuGet 封裝管理員**] | [**封裝管理員主控台**]
2. 在 PM> 提示字元底下，輸入「Update-package -reinstall Microsoft.AspNet.Mvc」
3. 當系統詢問您是否要重新載入檔案，選擇 [全部都是]。
4. 重新打造此解決方案，並試著再按一次 **F5**。


<a id="next-steps"></a>
## 後續步驟

如需其他自我學習，請考慮新增會在使用按一下其中一項搜尋結果時開啟的 [詳細資料] 頁面。您可以執行下列動作加以準備：

+	研究[查閱 API](http://msdn.microsoft.com/library/azure/dn798929.aspx)，此 API 可讓您查詢 Azure Search，以傳回特定文件 (例如，您可以傳送 productID)。
+	嘗試在 **HomeController.cs** 檔案中新增名為 Details 的新函數。新增對應的 **Details.cshtml** 檢視，以接收此查詢的結果並顯示結果。
+	查看下列有關地理空間搜尋的附加程式碼範例和視訊：[頻道 9 - Azure 搜尋服務和地理空間資料](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)和 [CodePlex：Azure 搜尋服務 GeoSearch 範例](http://azuresearchgeospatial.codeplex.com)

您也可以檢閱 MSDN 上的 [Azure Search REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx)。


<!--Anchors-->
[Prerequisites]: #sub-1
[Create an Azure Search index]: #sub-2
[Explore CatalogIndexer]: #sub-3
[Build an MVC4 Application using Azure Search]: #sub-4
[Explore AdventureWorksWeb]: #sub-5
[Next steps]: #next-steps


<!--Image references-->
[7]: ./media/search-create-first-solution/AzureSearch_Create1_WebApp.PNG
[8]: ./media/search-create-first-solution/AzureSearch_Create1_ConsoleMsg.PNG
[9]: ./media/search-create-first-solution/AzureSearch_Create1_DashboardIndexes.PNG
[10]: ./media/search-create-first-solution/AzureSearch_Create1_WebAppEmpty.PNG
[11]: ./media/search-create-first-solution/AzureSearch_Create1_Suggestions.PNG
[12]: ./media/search-create-first-solution/AzureSearch_Create1_CodeplexDownload.PNG

<!---HONumber=July15_HO2-->