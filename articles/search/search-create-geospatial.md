<properties 
	pageTitle="使用 Azure 搜尋服務建立地理空間搜尋應用程式" 
	description="使用 Bing 和 Azure 搜尋服務建立地理空間搜尋應用程式" 
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

# 使用 Azure 搜尋服務建立地理空間搜尋應用程式

本教學課程示範如何使用 Azure 搜尋服務與 Bing 地圖將地理空間搜尋新增至 Web 應用程式。透過地理搜尋功能，您可以找到某個地點特定距離範圍內的搜尋目標 (例如，尋找距離我目前位置 5 公理範圍內的所有餐廳)。Azure 搜尋服務中的地理空間功能支援常用的地圖技術。舉例來說，當您想要在不動產應用程式中使用多邊形來展示多邊形邊界內的待售屋，您可以透過 OData 或是我們的簡易搜尋語法輕鬆地辦到。

如需更多概觀，請觀賞本頻道 9 有關 [AAzure 搜尋服務與地理空間資料](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)的影片介紹

![][7]

為了建立應用程式，我們將使用 Bing 地圖服務來對應從 CSV 檔案載入的地理編碼地址，然後將得出的資料儲存到搜尋索引中。

本教學課程將以 [AAzure 搜尋服務 – Adventure Works 示範](http://azuresearchadventureworksdemo.codeplex.com)為基礎來編定。如果您尚未看過這項示範，請從該示範開始了解如何建立索引並從 Web 應用程式呼叫 Azure 搜尋服務 API。

<a id="sub-1"></a>
## 必要條件

+	Visual Studio 2012 或更高版本，並安裝 ASP.NET MVC 4 與 SQL Server。如果您尚未安裝此軟體，可以下載免費的 Express 版：[Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) 和 [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx)。
+	Azure 搜尋服務。您需要搜尋服務名稱，以及系統管理金鑰。如需詳細資料，請參閱[在入口網站中建立 Azure 搜尋服務](search-create-service-portal.md)。
+	Bing 地圖服務與其存取金鑰。下一節會提供說明
+	[CodePlex 上的 Azure 搜尋服務 GeoSearch 範例](https://azuresearchgeospatial.codeplex.com/)。在 [來源] 索引標籤上，按一下 [下載 ]取得解決方案的壓縮檔。 

    ![][12]

此解決方案內含兩個專案：

+	**StoreIndexer** 會建立 Azure 搜尋服務索引並載入資料。
+	**AdventureWorksWebGeo** 是一項 MVC4 應用程式，它會查詢 Azure 搜尋服務索引並在 Bing 地圖上顯示商店位置。

[AZURE.INCLUDE [您需要有 Azure 帳戶，才能完成本教學課程：](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## Bing 地圖

我們將使用 Bing 地圖 API 來做兩件事情。

+ **地理編碼地址：**資料裡已經包含一些地址資訊 (城市、州/省、郵遞區號)，但是我們還要取得該地址的經緯度以便進行地理空間搜尋。為取得座標，我們將使用 Bing 地圖 DataFlow API 來傳送一批地址以利地址編碼。透過 Bing 試用帳戶，我們一次只能使用 50 組地址，但是這已經足夠本教學課程使用。

+ **Bing 地圖：**當該應用程式執行時，我們將使用 Bing 地圖，在 Bing 地圖上顯示商店位置。

### 建立 Bing 地圖帳戶

1. 前往 [Bing 地圖入口網站](https://www.bingmapsportal.com/) (英文) 建立新的帳戶。輸入詳細資料以建立帳戶。

2. 帳戶建立完畢後，請選擇 [Create or view keys] 並輸入詳細資料以建立金鑰。為方便本示範進行，您可以選擇 [Trial Key]。

3. 按一下 [提交]，這時應會顯示您的 Bing 地圖應用程式的金鑰詳細資料。請記下此金鑰，以便稍後使用。

<a id="sub-3"></a>
## C# 中使用 Bing 地圖 DataFlow API 的地理編碼地址

在此步驟中，我們會針對全球各地的不同自行車商店，使用 Bing 地圖 DataFlow API 來建立一些地理編碼地址。

此項資料來自名為 store_locations.csv (位於您剛下載的來源) 的 CSV 檔案。如果您在文字編輯器或是 Excel 中開啟此檔案，會看到每一間商店、商店名稱及其地址都會有對應的 ID 欄位。

讓我們逐一檢視這些代碼，了解其運作原理。

1. 在 Visual Studio 中開啟 AdventureWorksGeo 方案，展開 [方案總管] 中的專案 **StoreIndexer**，然後開啟 Program.cs。因為已在 [Azure 搜尋服務 – Adventure Works 示範](http://azuresearchadventureworksdemo.codeplex.com/)中涵蓋了索引建立方式，所以我們將略過如何在 Program.cs 建立索引的討論。

2. 前往 [主要] 函數，請注意它會呼叫 **ApplyStoreData**。移至此函數，並詳細了解此程式碼。

3. **ApplyStoreData** 會將名為 "store_locations.csv" 裡的 CSV 檔案資料載入 System.Data.DataTable。

    此檔案內含所有商店，包括我們想要載入 Azure 搜尋服務的地址。在此檔案的每一列重複此動作，就能建立一組 **indexOperations** 以便稍後插入 Azure 搜尋服務索引 (先前透過 **CreateStoresIndex()** 函數來建立)。

    如果您之後仔細查看該索引，會發現用來包含每一間商店之縱向與橫向座標的 **GeoPt** 欄位都是空的。這時我們會進入 [主要] 函數的下一個步驟。

5. 移至函數 **ExtractAddressInfoToXML()**。此函數會從 store_locations.csv 檔案擷取地址資訊，並使用以 Bing 地圖可以接受並進行地理編碼的格式來載入 XML 檔案。檔案建立完畢後，就會呼叫函數 **GeoCoding.CreateJob**，傳送出去供 Bing 地圖 DataFlow 進行處理。

6. 由於地理編碼程序需要一點時間，每 10 秒鐘會產生一個呼叫 **GeoCoding.CheckStatus** 的迴圈來查看該工作是否已經完成。一旦完成，便可將 **GeoCoding.DownloadResults** 呼叫至地址類別來下載結果。

7. 最後步驟是將這些地理編碼的地址傳送至 Azure 搜尋服務。讓我們開啟 **UpdateStoreData** 函數，仔細查看其運作原理。

  **UpdateStoreData** 會使用動作 **@search.action: merge**，搭配剛從 Bing 地圖下載的縱向與橫向座標來更新 Edm.GeographyPoint 類型的位置欄位。此函數會查詢 "stores" 索引中屬於該文件唯一金鑰的 StoreId，然後將此新資料與現有文件合併。

8. 在執行應用程式之前，請開啟 App.config 並針對您的 Azure 搜尋服務與 Bing 地圖 API 更新其 "SearchServiceName"、"SearchServiceApiKey" 與 "BingMapsAPI" 的值，以新增您的 Azure 搜尋服務與 Bing 地圖 API 資訊。在搜尋服務名稱上，如果您的服務是 "mysearch.search.windows.net"，則您需要輸入 "mysearch"。

9. 以滑鼠右鍵按一下 **StoreIndexer** 專案，並選擇 [偵錯 | Start New Instance] 加以執行。

<a id="sub-4"></a>
## 使用 Azure 搜尋服務與 Bing 地圖為 MVC4 應用程式新增地圖

在此步驟中，您將在網頁瀏覽器中建立並執行搜尋應用程式，以便載入商店搜尋並繪製在 Bing 地圖上。

1.	在 Visual Studio 中，開啟名為 AdventureWorksGeo.sln 的 Azure 搜尋服務示範解決方案。 
	
2.	以滑鼠右鍵按一下 [方案總管] 的 **AdventureWorksWebGeo**，然後選取 [設定為啟始專案]。
	
3.	開啟此專案中的 Web.config，並更新您的 Azure 搜尋服務與 Bing 地圖 API 裡的 "SearchServiceName"、"SearchServiceApiKey" 與 "BingMapsAPI" 值。在搜尋服務名稱上，如果您的服務是 "mysearch.search.windows.net"，則您需要輸入 "mysearch"。

4.	儲存 Web.config。
	
5.	按 **F5** 鍵啟動專案。如果出現組建錯誤，請遵循以下[疑難排解](#err-mvc)步驟。

請注意，這些商店會以點狀方式呈現在地圖上。按一下其中一間商店，會看到快顯視窗，內含該商店的詳細資料。這裡的所有資訊都來自先前步驟中建立，名為 "stores" 的 Azure 搜尋服務索引。

<a id="sub-5"></a>
## 探索 AdventureWorksWebGeo

專案 **AdventureWorksWebGeo** 示範如何使用 ASP.NET MVC 4 與 Azure 搜尋服務進行互動以便建立可使用地理搜尋功能的地圖應用程式。在本小節裡，我們將檢視個別的應用程式碼部分，了解他們做了哪些事情。

1.	在 [方案總管] 中，展開 **AdventureWorksWebGeo** | **Controller**，然後開啟 HomeController.cs。應用程式啟動和索引頁面載入時，會呼叫 **Index ()** 函數。在此函數中，Bing 地圖 API 會從 Web.config 載入並傳遞至「索引」檢視作為 ViewBag.BingAPI 使用。

2.	開啟 [檢視 | 首頁] 裡的 Index.cshtml。

3.	此檔案會遵循您通常用來將 Bing 地圖新增至 Web 應用程式的方式進行，不過幾個重點如下：

+	控制器裡的 ViewBag 會用來搭配以下項目載入地圖認證：'@ViewBag.BingAPI' 

+	地圖一旦載入，JQuery $.post 會參考以下項目以便加入 HomeController **Search** 函數：/home/search

+	**Search** 函數會擷取商店位置，然後 Bing 地圖會接收此位置並將之新增為 PushPins。

4.	開啟 [控制器] 下方的 HomeController.cs，然後查看 **Search** 函數。請注意，此函數如何呼叫 _storeSearch.Search(lat, lon, 10000)。此舉會導致執行查詢，以尋找距離指定的橫向 (lat) 與縱向 (lon) 座標 10,000 公里以內的所有商店。查詢的結果會進行處理，然後傳回「索引」檢視以便處理為 PushPins 並顯示在 Bing 地圖上。
示範就到此為止。現在您已經逐一檢視過所有需要知道的主要作業流程，可以開始依據 ASP.NET MVC4 應用程式並搭配 Azure 搜尋服務來打造出自己的地圖。


<a id="err-mvc"></a>
## 如何解決「無法載入檔案或是組件 'System.Web.Mvc」

在打造 AdventureWorksWeb 時，如果出現「無法載入檔案或是組件 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 或其中一項相依性」的訊息時，請嘗試以下步驟來解決這個錯誤。

1. 開啟 [封裝管理員主控台]：[**工具**] | [**NuGet 封裝管理員**] | [**封裝管理員主控台**]
2. 在 PM> 提示字元底下，輸入「Update-package -reinstall Microsoft.AspNet.Mvc」
3. 當系統詢問您是否要重新載入檔案，選擇 [全部都是]。
4. 重新打造此解決方案，並試著再按一次 **F5**。

<a id="next-steps"></a>
## 後續步驟

為力求精進，請考慮在此地圖應用程式加入更多功能。例如，您可以新增：

+ 搜尋方塊，以便讓使用者搜尋特定商店。

+ Facet 類別，以讓使用者依國家/地區或州/省篩選。

+ 使用者繪製的選擇區域，讓使用者藉由拖曳地圖上的區域來指定要搜尋的區域。該區域可以接著由 Azure 搜尋服務透過地理互動 API 進行篩選，然後繪製在地圖上。


<!--Anchors-->
[Prerequisites]: #sub-1
[Bing Maps]: #sub-2
[Geocode Addresses in C# using Bing Maps DataFlow API]: #sub-3
[Add Mapping to an MVC4 Application using Azure Search and Bing Maps]: #sub-4
[Explore AdventureWorksWebGeo]: #sub-5
[Next steps]: #next-steps


<!--Image references-->
[7]: ./media/search-create-geospatial/AzureSearch-geo1-App.PNG
[12]: ./media/search-create-geospatial/AzureSearch_Create2_CodeplexDownload.PNG

<!---HONumber=July15_HO2-->