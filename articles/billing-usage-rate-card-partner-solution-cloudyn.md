<properties
   pageTitle="Microsoft Azure 使用情況和 RateCard API 可讓 Cloudyn 提供 ITFM 給客戶"
   description="提供 Microsoft Azure 計費合作夥伴 Cloudyn 將 Azure 計費 API 整合至其產品的經驗所得來的獨特觀點。這特別適用於有興趣使用/嘗試將 Cloudyn 用於 Azure 服務的客戶。"
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="06/14/2015"
   ms.author="mobandyo;bryanla"/>

# Microsoft Azure 使用情況和 RateCard API 可讓 Cloudyn 提供 ITFM 給客戶 

Cloudyn (Microsoft 開發夥伴和雲端管理功能的領導提供者) 是為一項新 Microsoft Azure 資源使用情況與 RateCard API 的私人預覽所選取。使用情況 API 可為訂用帳戶提供預估的 Azure 耗用量資料。RateCard API 提供非企業合約 (EA) 客戶所有 Azure 服務的完整定價資訊。當這些 API 整合在一起時，會提供完整的資訊基礎給 IT 財務管理 (ITFM) 工具的輸入，例如 Cloudyn 所提供的工具。

## 簡介 

使用情況 API 的資料和 RateCard API 的資料之間所謂的「乘法」(使用情況 [單位] 價格 [$unit] = 詳細使用情況和成本) 會建立最細微、精確且可靠的計費資訊，可供目前的 Azure 使用。

![ITFM 概觀][1]

使用這些 API 可提供與客戶的使用情況和成本相關的重要資訊，讓 Cloudyn 可利用簡單的程式設計方式分析客戶帳戶，並為其客戶執行各種 ITFM 工作。

## 整合 Cloudyn 和 RateCard 使用情況 API
RateCard API 需要數個輸入參數 -- 例如區域資訊、貨幣及區域設定 --，但最重要的一種是 OfferDurableID，它會指定客戶正在使用的 Azure 優惠類型 (隨收隨付、舊版 6 及 12 個月的承諾方案、MSDN 優惠、MPN 優惠、促銷優惠和其他類型)。OfferDurableID 位於指定訂用帳戶之「優惠識別碼」下的 [Azure 使用情況和計費入口網站](https://account.windowsazure.com/Subscriptions)。

在註冊 [Cloudyn for Azure](https://www.cloudyn.com/microsoft-azure/) 服務時，客戶可以加入其 OfferDurableID 程式碼，可讓 Cloudyn 透過 RateCard API 提取其相關定價資訊。如需各種類型的優惠資訊，請參閱 [Microsoft Azure 優惠詳細資料](http://azure.microsoft.com/en-gb/support/legal/offer-details/)頁面。

![Cloudyn ITFM 引擎概觀][2]

除了 Azure 效能 API 之外，Cloudyn 還使用了使用情況和 RateCard API，建立其他層的視覺化、分析、警示、報告、成本管理和可行的建議，提供可靠的企業雲端 ITFM 工具給 Azure 客戶。

## Cloudyn ITFM 使用由使用情況和 RateCard API 整合啟用的案例 
一般 Cloudyn ITFM 所使用的案例都由使用情況和 RateCard API 啟用，包括：

+ **成本分析** - 可讓雲端成本細分成任何原生識別維度 (提供者、服務、帳戶、區域等)。藉由提供每個帳戶最細微的使用情況和成本資料分解，然後由 Cloudyn 將其分組和篩選，並以圖型或表格的形式呈現給使用者，Azure 使用情況和 RateCard API 可讓這個工作變簡單。

![成本分析圓形圖][3]

+ **成本配置 360** - 可讓財務與 IT 管理者發現其雲端部署的實際成本分解、驅動程式和趨勢。它還可以進一步讓管理者輕鬆地建立部署費用與商務單位、部門、區域之間的關聯、針對雲端成本提供前所未有的見解，並且促進企業計費和回報。Azure 使用情況和 RateCard API 可做為 Cloudyn 成本配置引擎的輸入，藉由定義方法和商務邏輯以配置未標記或無法標記的資源，進而輔助 API。

![成本配置 360 圖表][4]

+ **具有成本效益的大小** - 提供正確的大小建議給使用量過低的虛擬機器，進而減少客戶對於過大或過度佈建機器的費用。它是藉由檢查虛擬機器 CPU 和 RAM 計量 (透過效能 API)、執行階段的時數 (透過使用情況 API) 和成本 (透過 RateCard API) 來達成效果。然後 Cloudyn 會根據使用量過低的 CPU 或 RAM 資源 (效能) 提供正確大小的建議，並且將 VM 之間的價格差異 (RateCard) 乘以使用量過低機器的實際使用時間 (使用情況)，以計算出預估的節約效益。 

![符合成本效益的大小][5]

+ **雲端移植建議** - 提供雲端移植的財務建議。它會檢查使用者目前部署在主要雲端廠商的雲端資源成本，並且和其在 Azure 上對等的部署成本進行比較。然後它會提供細微的、每個資源的、財務型的移植建議給 Azure。評估 Azure 上所需之對等部署 (根據效能計量和使用者喜好設定) 之後，Cloudyn 會使用 RateCard API 評估 Azure 上對等部署的成本。

+ **效能報告** - 由 Azure 的效能 API 啟用，這些報告提供 CPU 和 RAM 使用量的陣列功能給最佳化建議。以下是執行個體使用量報告的範例，根據平均 CPU 使用量來呈現執行個體分解。

![效能報告][6]

+ **類別管理員** - 為缺乏組織的雲端資源帶來秩序的強大 Cloudyn 功能。它可讓使用者自由建立自己的唯一類別 (標記) 以進行符合商業實務的有效測量和報告。此外，使用者可以輕鬆地管理和分類不一致的標記 (也就是錯字和其他不一致)，並且自動偵測未標記的資源以達成精確的成本屬性。

![類別管理員][7]

## 影片 

以下短片顯示 Azure 客戶如何使用 Azure 和 Azure 計費 API 的 Cloudyn，從 Azure 耗用量資料取得見解。
 
> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## 後續步驟

+ 開始免費 [Cloudyn for Azure](https://www.cloudyn.com/microsoft-azure/) 試用版，查看如何利用 Microsoft Azure 雲端部署的自訂報告和分析取得成本透明度。
+ 請參閱[深入了解 Microsoft Azure 資源耗用量](billing-usage-rate-card-overview.md)以取得 Azure 資源使用情況和 RateCard API 的概觀。 
+ 查看 [Azure 計費 REST API 參考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)以取得屬於 Azure 資源管理員所提供之 API 集合的兩個 API 之詳細資訊。
+ 如果您想要探究範例程式碼，請查看 [Github 上的 Microsoft Azure 計費 API 程式碼範例](https://github.com/Azure/BillingCodeSamples)。

## 詳細資訊
+ 若要深入了解 Microsoft Azure 企業合約 (EA) 優惠，請瀏覽[授權企業用 Azure](http://azure.microsoft.com/pricing/enterprise-agreement/)
+ 請參閱 [Azure 資源管理員概觀](resource-group-overview.md)一文，以深入了解 Azure 資源管理員。
+ 如需協助了解雲端花費之必要工具套件的其他資訊，請參閱 Gartner 文章 [IT 財務管理 (ITFM) 工具的市場指南](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb)。

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png

<!---HONumber=July15_HO1-->