<properties 
	pageTitle="Azure 搜尋開發人員案例研究：WhatToPedia 如何在 Microsoft Azure 建置資訊媒體入口網站" 
	description="了解如何在 Microsoft Azure 上使用搜尋服務建置資訊入口網站和中繼搜索引擎" 
	services="search, sql-database,  storage, web-sites" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe"/>

<tags 
	ms.service="search" 
	ms.devlang="NA" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="search" 
	ms.date="04/13/2015" 
	ms.author="heidist"/>

# Azure 搜尋開發人員案例研究

## [WhatToPedia.com](http://whattopedia.com/) 如何在 Microsoft Azure 建置資訊媒體入口網站

 ![][6]<font size="9">好主意</font>


我們的想法是建置資訊入口網站，藉由高相關、範圍內搜尋的體驗幫助購物者聯繫零售商，就像遊客在未知的領域時，旅遊入口網站幫忙匹配飯店、餐廳和娛樂項目一樣。

我們所設想的入口網站將針對給定市場中的零售商資料，提供傑出的高品質搜尋體驗，幫助購物者根據零售商所提供的地點及便利設施來找到商店。我們將在搜尋引擎中植入初始資料集，但會藉助零售商訂閱者張貼其企業資訊，而隨時間進展建置更深入的值。舉凡促銷、新商品、熱門品牌、內部特點服務等，全都是會提升我們網站價值的資料。一旦零售商註冊為訂閱者，這些資料會自己報告並整合到搜尋主體。廣告加上訂用帳戶模型，會替我們的新商務帶來收益錢潮。

搜尋服務將會是純雲端平台上最主要的使用者互動模型。基於規模和降低成本的目的，從入口網站體驗到原始檔控制等，幾乎每件事都將透過線上服務進行。使用能提供我們所需立即可用功能的搜尋引擎，可快速建立搜尋服務應用程式，而不需自己建置和管理搜尋引擎。

## 我們所建置

WhatToPedia 是剛成立的資訊媒體公司我們建置了 [WhatToPedia.com](http://whattopedia.com/) – 目前位於歐洲北部的測試市場中，啟用日期為 2015 年 2 月 2 日。我們的客戶群主要是實體商店，他們需要便於管理維護且價格實惠的線上空間。

我們的任務是藉由強大的線上搜尋體驗來吸引購物者，並根據城市或鄰近地區、品牌、商店名稱或是商店類型提升搜尋結果。吸引購物者後會帶來連鎖反應，鼓勵零售商訂用我們的入口網站。訂用帳戶是收費服務，費率實惠。

 ![][7]

註冊訂用帳戶之後，零售商會接手現有的設定檔 (最初由我們從購買的資料所建立)，以其他資料如促銷、精選品牌或公告等進行更新。可以自己報告商店內提供的服務，例如使用的語言、可接受的貨幣、免稅購物等，以更有效地吸引正在尋找這些便利設施的購物者。

## 簡介

我叫 Thomas Segato (Microsoft 諮詢服務)，我和 WhatToPedia 的首席開發人員 Jesper Boelling 一同設計解決方案。

WhatToPedia 剛成立不久，正在瑞典測試行銷新的入口網站商務，而瑞典 60,000 個零售商中多數都是實體 SME (中小型企業)。因為我們了解在歐洲有說各種語言且攜帶各種貨幣的消費者，所以我們建置了可配合說各種語言之購物者的解決方案。我們需要能支援多語需求的搜尋引擎，而我們在 Azure 搜尋服務上找到了。

Azure 搜尋服務為我們的專案帶來重大突破。在 Azure 搜尋服務可用之前，我們花了相當多精力在解決建置自身搜尋引擎的問題上。使用 Azure 搜尋服務做為線上服務，解決了我們方案中最大的技術和管理難題，這代表我們可以更快開始行銷，同時有更穩健的搜尋體驗。

## 我們是如何辦到的

我們的願景是僅根據雲端服務來建置完整的基礎結構。我們選擇 Microsoft 做為策略平台，因為其能提供必要服務 (合作和開發兩方面皆是)，也能依需求延展和提供實惠的定價。
 
### 高階元件

我們建置了商務，而非僅是網站。支援整個行動需完善的工具和應用程式。我們採用 Visual Studio 和 Visual Studio Online 進行開發、Team Foundation Service (TFS) 用以控制原始檔、Office 365 用來傳訊和共同作業，當然還有 Microsoft Azure，用於所有網站相關的作業和儲存。藉由 Visual Studio，此 IDE 提供對 Azure 的直接佈建，以及整合 TFS Online，可另外讓生產力強化。

下列圖表說明用於 WhatToPedia 基礎結構的高階元件。

   ![][8]

### 我們如何使用 Microsoft Azure

看看上一張圖表的綠色方塊，您會發現 WhatToPedia 解決方案是建置在這些服務上：

- [Azure 搜尋服務](http://azure.microsoft.com/services/search/)
- [使用 MVC 4 的 Azure 網站](http://azure.microsoft.com/services/websites/)
- [用來排程工作的 Azure WebJobs](../websites-webjobs-resources.md)
- [Azure SQL Database](http://azure.microsoft.com/services/sql-database/)
- [Azure Blob 儲存體](http://azure.microsoft.com/services/storage/)
- [SendGrid 電子郵件傳遞](http://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

此解決方案的核心是資料和搜尋。資料從轉銷商提供者流動到一般客戶的過程如下所示：

  ![][9]

主要資料儲存體是位於 Azure SQL 資料庫中的轉銷商及會計資料。這包含初始資料集，及隨時間進展所加入的零售商特定資料。我們正使用 Azure WebJob 從 SQL 資料庫中公布更新到 Azure 搜尋服務中的搜尋主體。

### 展示層

入口網站為 Azure 網站，在 MVC 4 和 [Twitter Bootstrap](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29) 中實作。我們選擇 MVC，因為與 ASP.NET 表單型開發相比，其可提供 HTML 更簡單明瞭的方式。為了不用替多種裝置建立應用程式和維護多種行動平台，我們選擇 Twitter Bootstrap 來支援所有裝置和平台。

### 驗證、權限和敏感資料

購物者以匿名瀏覽此網站。因此，購物者不需登入，我們也不會儲存任何消費者的資料。

零售商則是另外一回事了。在這裡，我們儲存對外公開的設定檔資訊、計費資訊及零售商想在網站上公開的媒體內容。每個訂用此網站的零售商會取得使用者登入資訊，在更新訂閱者設定檔之前用來驗證使用者。我們將所有訂閱者資料安全地儲存在 Azure SQL 資料庫和 Azure Blob 儲存體中。我們選擇基於 .NET 表單型驗證的驗證模型。為簡單起見，我們選擇這種方式；我們不需要其他方式所需的角色、UI 支援和其他無關的功能。

為了確保零售商只能看到屬於他們的資料，我們建立每個零售商的零售商 ID，隨後可用於所有與零售商特定資料相關的讀取和寫入作業。使用此方式後，我們發現並不需要授與資料庫權限給個別零售商。我們以零售商 ID 做為資料隔離技術，使所有零售商都以單一資料庫角色和系統互動。

因為我們的業務集中在下游效應 (替零售商帶來更多生意、提供廣告和訂用的誘因)，所以可以不用處理網路上的交易。因此，您不會在我們的網站上找到購物車，而這也簡化了我們的安全性需求。

我們所採用的另一項簡化方式為外包帳單和應付帳款作業。藉由將消費者付費資訊直接發送給協力廠商 ([SveaWebPay](http://www.sveawebpay.se/))，我們減少在資料存放區中儲存和保護敏感資料的相關風險。

### 搜尋引擎

我們解決方案的核心為建置在 Azure 搜尋服務上的搜尋引擎。最初我們建置了自訂的搜尋引擎，但在這過程中意識到其複雜性和工作量確實相當高，這也促使我們考慮其他替代方案。

對我們而言最重要的基本功能包含：

- 篩選器
- 多面向導覽
- 提升結果
- 透過 AJAX 分頁

有次在網路上看到下列影片而讓我們獲得啟發並試著用 Azure 搜尋服務：[深入了解 TechEd Europe](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410)。

觀看這部影片之後，我們準備好依據所見來建置原型。由於我們在 MVC 中早已有資料模型，所以建立原型會相當直接，因為這些資料包含可搜尋的詞彙，而且我們早已制定想要如何排序、使用 Facet 和篩選資料的需求。

這是我們建置原型的方式。

**設定 Azure 搜尋服務**

1. 登入 Azure 入口網站，加入搜尋服務到我們的訂用帳戶中。我們使用共用版本 (利用我們的訂用帳戶免費使用)。
2. 建立索引。關於原型，我們使用入口網站 UI 來定義搜尋欄位，並且建立評分設定檔。我們的評分設定檔以地區資料為基礎：國家 | 城市 | 地址 (請參閱：新增評分設定檔)。
3. 複製服務 URL 和系統管理 API 金鑰到我們的組態檔。這個金鑰位於入口網站的搜尋服務頁面上，且用於驗證服務。
	
**開發搜尋索引子工作 – Windows 主控台**

1. 從資料庫讀取所有轉銷商。
2. 呼叫 Azure 搜尋服務 API 來逐一上傳轉銷商 (請參閱：http://msdn.microsoft.com/library/azure/dn798930.aspx))。
3. 在編製索引轉銷商以增量索引的資料庫中設定屬性。我們加入「索引子」欄位，這會儲存每個設定檔的索引狀態 (無論是否已編製索引)，來完成這動作。 

請參閱建置索引子工作之程式碼片段的附錄。

**開發搜尋網路入口網站 – MVC**

1. 呼叫 Azure 搜尋服務以從搜尋取得所有文件 (請參閱：http://msdn.microsoft.com/library/azure/dn798927.aspx)。
2. 從搜尋服務回應中擷取下列項目 (使用 JSON.Net http://james.newtonking.com/json)。
   - 結果
   - Facet
   - 結果計數
   - 開發使用者介面用來顯示搜尋結果、Facet 和計數 (我們早已有這些了)。

這是我們用來從 Azure 搜尋服務取得結果的程式碼：

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**依位置提升**

在原型中要確認之最重要的需求可能包括將位置搜尋關鍵字加入到查詢。如果使用者在搜尋查詢中輸入城市名稱，則位於給定城市中的轉銷商排名會較描述中有此城市關鍵字的轉銷商更高，這一點對我們的入口網站相當重要。為了此需求，我們使用評分設定檔，讓城市欄位的排名比其他欄位更高。

**支援多種語言**

我們需要在正確的語言中顯示正確的搜尋結果，並且提供在不同語言中尋找相同結果的選項。此問題分為兩面：

- 在多種語言中搜尋詞彙。
- 在正確的語言中顯示搜尋結果。

我們對每種語言加入有當地語系化文字的文件和該語言的屬性，來解決展示的部分。當使用者輸入搜尋詞彙時，我們使用 `$filter` 運算式來篩選使用者選擇的語言。

每個文件都有稱為「城市」的隱藏屬性建置於集合類型上。此屬性以各種語言儲存城市名稱，讓使用者可以在多種語言中搜尋。

###資料儲存體

所有資料 (設定檔、訂用帳戶及會計) 都儲存在 SQL 資料庫中。所有媒體檔案都儲存在 Azure Blob 儲存體中，包含零售商提供的影像和影片。使用個別的 Blob 儲存體分隔上傳檔案的作用；檔案永遠不會和網站混合，所以我們不需要每次新增檔案就再重新建置網站。

我們的儲存設計還有一項重要的優點，那就是多個開發人員可以共用單一開發儲存體。WhatToPedia 專案的需求之一是要能在 15 分鐘內建立開發環境，包含轉銷商資料、影像和影片。藉由設定來自 TFS Online 的最新資料、執行 SQL 指令碼和執行匯入工作，可設立完整的環境而不需耗費任何時間。這種做法也改進了預備流程。

###WebJobs

我們使用 Azure WebJobs 來更新資料到索引中。藉由建立搜尋索引子工作，很輕易地就把索引部分整合到我們的解決方案中。我們所做的唯一程式碼變更是要配合索引子工作，加入 `Indexed` 欄位到我們的資料模型來表示索引狀態。每當新增或更新新的設定檔，`Indexed` 欄位會設為 false。這也適用於當零售商從入口網站變更其設定檔資料時。

此工作會尋找所有 `Indexed` 設為 false 的資料列。當它找到這個資料列，文件會發布到 Azure 搜尋服務，然後 `Indexed` 欄位會設為 true。我們無須規劃加入資料或更新資料，因為 Azure 搜尋服務實際上會負責這樣做。如果您加入早已存在的文件，該服務會自動進行更新。

所有網路工作都開發成主控台應用程式，其可做為壓縮檔上傳到 Azure 網站再解壓縮，然後進行排程。

該工作做為已排程的網路工作，排程為每 5 分鐘執行一次。我們計算過該服務上傳 3,000 份文件費時約 3 分鐘，這在我們的需求之內。

> [AZURE.NOTE]Azure 搜尋服務最近引入一個原型索引子功能。在我們第一個版本中，這個功能因為太晚出現而沒辦法讓我們使用，但它貌似可解決我們之前用索引子工作所解決的相同問題，也就是自動化資料載入作業。


###備份策略

我們設計了多層備份策略以用來從各種案例中復原，從災難性失敗到個人交易的復原等。要保護的資產包含三種資料 (網站、訂閱者資料和媒體檔案)。

首先，藉由在 TFS Online 中保管網站原始碼，我們了解如果網站故障，則可以從 TFS 重新發布來重新建置網站。

Azure SQL 資料庫中的訂閱者資料為最敏感的資產。我們用內建的功能予以備份 (請參閱[Azure SQL Database 備份和還原](http://msdn.microsoft.com/library/azure/jj650016.aspx))。備份排程為每週一次完整資料庫備份、每天一次差異資料庫備份，以及每 5 分鐘一次交易記錄備份。對於這些資料的大小來說，此解決方案對即時和預計的資料量而言再適合不過了。

第三，我們在 Azure Blob 儲存體中儲存影像和影片檔案。我們仍正在評估對這些資料的終極備份規劃，並考慮使用 Azure 的 Cloudberry 總管做為可能的解決方案。現在，我們使用 WebJob 來複製影像和影片到另一個位置。

##我們學到了什麼

因為我們早已有資料，所以很容易進行概念證明。在幾個小時之內，我們就有了具有 Facet 和計數器、分頁、排名設定檔和搜尋結果的原型。這個搜尋結果如此地精確，所以我們決定移除某些呈現給一般使用者的篩選器。

最大的驚喜是我們可以這麼快地學習 Azure 搜尋服務，以及我們得到了這麼多收穫。不誇張，我們在幾個小時內就建立了概念證明 (請參閱下列註解)、在前端應用程式的 500 行程式碼中取代 3 行 (加上新的 WebJob)，以及取得更好的結果。

以前，我們的程式碼會實作標準搜尋的分頁、計數及其他行為。使用 Azure 搜尋服務，我們取回的結果包含搜尋點擊數、Facet、分頁資料、計數 -- 我們所需的全部東西，而且是以前我們要自己提供的。這也包含提升和內建多面向導覽，這些是我們原先解決方案中所沒有的。

實作中最大的挑戰在於這是預覽版本，而且尋找資訊和共用經驗相當困難。一旦我們將這些經驗和想法連接起來，就會發現使用 Azure 搜尋服務相當簡單，因為它有 REST API 和 JSON 資料格式。我們可以直接從多數開放原始碼的外掛程式呼叫架構，像是 JQuery JSON.Net，而且可以使用一些工具，像是用於快速實驗和偵錯的 Fiddler。

> [AZURE.NOTE]除了將資料準備好之外，對於我們之中早已知道搜尋科技如何運作的那些人，這也有助於建置原型，讓我們更有生產力，也更讚賞內建功能。如果您需要加強搜尋查詢建構、多面向導覽和篩選器等，也許您要在原型階段花更多時間。

###在搜尋展示頁中控制 Facet

我們在概念證明過程中所學到的其中一點為提前謹慎規劃 Facet。載入大量資料到解決方案之後，我們發現 Facet 的完整容量太大，無法展示給使用者。

我們藉由限制 Facet 計數參數來解決此問題。計數參數會對傳回給使用者的 Facet 數目設有嚴格的限制。包含關於計數參數討論的連結可以在[這裡](search-faceted-navigation.md)找到。

###用於排程工作的 WebJobs

Azure 搜尋服務並不是唯一的驚喜。與我們先前的方式相比，我們發現使用 WebJobs 自動化 Azure 搜尋服務的資料載入作業極其優異，這需要使用執行 Windows 排程器專用的 VM，且排程工作為更新搜尋索引。WebJobs 的設定較為簡單，也更容易偵錯，當然比起付費使用專用 VM 更加便宜。

###用於更新影像的 Azure Blob 儲存體總管

我們發現使用 [Azure Blob 儲存體總管](https://azurestorageexplorer.codeplex.com/) (在 CodePlex 中可供使用) 對於管理更新到此網站的影像和影片相當有幫助。我們把它當成開發人員工具來手動更新主要網站中的影像和影片。我們發現這比起部署變更到入口網站還更有彈性，每當我們需要更新影像時，可免除完整測試逐一查看。

##最後幾句話

感謝在 WhatToPedia 傑出的各位讓我們分享他們的故事。

我們希望您覺得這個案例研究相當實用。如果您打算使用 Azure 搜尋服務，建議您使用一些資源來加速完成：

- [Azure 搜尋服務專用的 MSDN 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow 也有標籤](http://stackoverflow.com/questions/tagged/azure-search)
- [Azure.com 上的文件頁面](http://azure.microsoft.com/documentation/services/search/)
- [MSDN 上的 Azure 搜尋服務文件](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##附錄：搜尋索引子 WebJob

下列程式碼會建置本節所提到的建置原型用的索引子。

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 

<!---HONumber=62-->