<properties
   pageTitle="Cloud Cruiser 和 Microsoft Azure 計費 API 整合"
   description="提供 Microsoft Azure 計費合作夥伴 Cloud Cruiser 將 Azure 計費 API 整合至其產品的經驗所得來的獨特觀點。這特別適用於有興趣使用/嘗試將 Cloud Cruiser 用於 Microsoft Azure Pack 的客戶。"
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
   ms.date="06/17/2015"
   ms.author="mobandyo;sirishap;bryanla"/>

# Cloud Cruiser 和 Microsoft Azure 計費 API 整合 

本文描述從新的 Microsoft Azure 計費 API 所收集來的資訊如何用來在 Cloud Cruiser 中進行工作流程成本模擬與分析。

## Azure RateCard API
RateCard API 提供來自 Azure 的費率資訊。以適當的認證進行驗證之後，您可以查詢 API 以收集 Azure 上可用服務的中繼資料，以及與優惠識別碼相關聯的費率。

以下是來自 API 的範例回應，顯示 A0 (Windows) 執行個體的價格：

    {       
		"MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",       
		"MeterName": "Compute Hours",       
		"MeterCategory": "Virtual Machines",       
		"MeterSubCategory": "A0 VM (Windows)",       
		"Unit": "Hours",       
		"MeterRates": 
		{         
			"0": 0.029       
		},       
		"EffectiveDate": "2014-08-01T00:00:00Z",       
		"IncludedQuantity": 0.0     
	}, 

## Azure RateCard API 的 Cloud Cruiser 介面
Cloud Cruiser 可以用不同的方式運用 RateCard API 資訊。在這篇文章中，我們將說明如何使用它進行 IaaS 工作負載成本模擬及分析。

為了示範這個使用案例，請想像執行於 Microsoft Azure Pack (WAP) 之數個執行個體的工作負載。目標是要在 Azure 上模擬相同的工作負載，並評估這類移轉的成本。若要建立這個模擬，有兩個主要的工作要執行：

1. **匯入和處理從 RateCard API 收集的服務資訊** - 這項工作也會在活頁簿上執行，其中從 RateCard API 擷取的內容會轉換並發佈為新的費率方案。新的費率方案將在模擬中用來評估 Azure 價格。

2. **標準化 WAP 服務和 IaaS 的 Azure 服務** - 根據預設，WAP 服務以個別資源 (CPU、記憶體大小、磁碟大小等) 為基礎，而 Azure 服務以執行個體大小 (A0、A1、A2 等等) 為基礎。第一個工作可以由 Cloud Cruiser 的 ETL 引擎執行，稱為活頁簿，其中資源整合為執行個體大小，類似 Azure 執行個體服務。

## 從 RateCard API 匯入資料

Cloud Cruiser 活頁簿提供自動化的方式收集和處理來自 RateCard API 的資訊。ETL (擷取-轉換-載入) 活頁簿可讓您設定資料的集合、轉換和發佈至 Cloud Cruiser 資料庫。

每個活頁簿可以有一個或多個集合。這可讓您將來自不同資源的資訊相互關聯，以補充或強化使用狀況資料。在下方的兩個螢幕擷取畫面中，顯示在現有活頁簿中建立新的*集合*，並將資訊從 RateCard API 匯入到*集合*：

![圖 1 - 建立新的集合][1]

![圖 2 - 從新集合匯入資料][2]

將資料匯入活頁簿之後，就可以建立多個步驟及轉換程序，以修改資料與建立資料模型。以這個範例而言，因為我們只對基礎結構即為服務 (IaaS) 感興趣，所以我們可以使用轉換步驟移除不必要的資料列或記錄，它們和服務相關，而不是 IaaS。

下方螢幕擷取畫面顯示轉換步驟，用來處理從 RateCard API 所收集的資料：

![圖 3 - 處理收集自 RateCard API 之資料的轉換步驟][3]

## 定義新的服務和費率方案

有不同的方式可定義 Cloud Cruiser 上的服務。其中一個選項是從使用情況資料匯入服務。使用公用雲端，其中的服務已由提供者定義時，通常會使用這個方法。

費率方案是一組費率或價格，可根據生效日期、客戶群組或其他選項套用至不同服務。費率方案也可以在 Cloud Cruiser 上用來建立模擬或「假設」案例，以了解服務中的變更如何影響工作負載的總成本。

在此範例中，我們將使用來自 RateCard API 的服務資訊定義 Cloud Cruiser 中的新服務。同樣地，我們可以使用和服務相關聯的費率，在 Cloud Cruiser 上建立新的費率方案。

在轉換程序結束時，就可以建立新的步驟，並將來自 RateCard API 的資料發佈做為新的服務和費率。

![圖 4- 發佈來自 RateCard API 的資料做為新的服務和費率][4]

## 確認 Azure 服務和費率

發佈服務及費率之後，您可以在 Cloud Cruiser 的 [*服務*] 索引標籤中確認匯入服務的清單：

![圖 5- 驗證新的服務][5]

在 [*費率方案*] 索引標籤上，您可以利用匯入自 RateCard API 的費率檢查名為 "AzureSimulation" 的費率方案。

![圖 6- 驗證新的費率方案及相關聯的費率][6]

## 標準化 WAP 和 Azure 服務

根據預設，WAP 會根據計算、記憶體和網路資源等使用情況提供使用情況資訊。在 Cloud Cruiser 中，您可以直接根據這些資源的配置或計量使用情況來定義服務。例如，您可以設定每小時 CPU 使用量的基本費率，或為配置給執行個體的 GB 記憶體收費。

以這個範例而言，為了比較 WAP 和 Azure 之間的成本，我們必須將 WAP 上的資源使用情況彙總套組，進而將其對應至 Azure 服務。此轉換可以在活頁簿中輕鬆地實作：

![圖 7 - 轉換 WAP 資料以正規化服務][7]

活頁簿的最後一個步驟是將資料發佈至 Cloud Cruiser 資料庫。在此步驟期間，使用情況資料現在整合為服務 (進而對應至 Azure 服務)，並繫結至預設的費率來建立費用。

完成活頁簿之後，您可以在排程器上加入新的工作，並指定活頁簿執行的頻率和時間，即可自動化資料的處理。

![圖 8 - 活頁簿排程][8]

## 建立工作負載成本模擬分析的報告

收集使用情況並將費用載入 Cloud Cruiser 資料庫之後，我們就可以利用 Cloud Cruiser Insights 模組 – 進階的分析報告工具 – 建立我們想要的工作負載成本模擬。

為了示範這種案例，我們建立了下列報告：

![成本比較][9]

上圖顯示由服務細分的成本比較，並比較在 WAP (深藍色) 與 Azure (淺藍色) 之間為每個特定服務執行工作負載的價格。

下圖顯示相同的資料，但由部門細分，示範每個部門在 WAP 和 Azure 上執行其工作負載的成本，以及兩者之間的差異 – 節約列 (綠色)。

## 後續步驟

+ 如需建立 Cloud Cruiser 活頁簿和報告的詳細指示，請參閱 Cloud Cruiser 的線上[文件](http://docs.cloudcruiser.com/) (需要有效的登入)。如需有關 Cloud Cruiser 的詳細資訊，請連絡 [info@cloudcruiser.com](mailto:info@cloudcruiser.com)。
+ 請參閱[深入了解 Microsoft Azure 資源耗用量](billing-usage-rate-card-overview.md)以取得 Azure 資源使用情況和 RateCard API 的概觀。 
+ 查看 [Azure 計費 REST API 參考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)以取得屬於 Azure 資源管理員所提供之 API 集合的兩個 API 之詳細資訊。
+ 如果您想要探究範例程式碼，請查看 [Github 上的 Microsoft Azure 計費 API 程式碼範例](https://github.com/Azure/BillingCodeSamples)。

## 詳細資訊
+ 請參閱 [Azure 資源管理員概觀](resource-group-overview.md)一文，以深入了解 Azure 資源管理員。

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "圖 1 - 建立新的集合"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "圖 2 - 從新集合匯入資料"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "圖 3 - 處理收集自 RateCard API 之資料的轉換步驟"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "圖 4- 發佈來自 RateCard API 的資料做為新的服務和費率"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "圖 5- 驗證新的服務"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "圖 6- 驗證新的費率方案及相關聯的費率"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "圖 7 - 轉換 WAP 資料以正規化服務"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "圖 8 - 活頁簿排程"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "圖 9 - 工作負載成本比較案例的範例報告"

<!---HONumber=July15_HO1-->