<properties
   pageTitle="深入瞭解 Microsoft Azure 資源耗用量"
   description="提供 Azure 計費使用情況和 RateCard API 的概念性概觀，可用來提供 Azure 資源耗用量和趨勢的見解。"
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

# 深入瞭解 Microsoft Azure 資源耗用量 

客戶和合作夥伴必須能夠準確地預測並管理其 Azure 成本。當它們從 Capex 移至 Opex 模型時，它們也必須能夠進行回報 vs. 計費分析，並提供估計和計費的模式可靠性，特別是大型的雲端部署。

本文中討論的 Azure 資源使用情況和費率 Card API 可將新的見解應用於 Azure 資源的耗用量，藉以解決這些需求。

## Azure 資源使用情況與 RateCard API 簡介 

Azure 資源使用情況和 RateCard API 會實作為資源提供者，並由 Azure 資源管理員公開為 API 系列的一部分。

### Azure 資源使用情況 API (預覽)
客戶和合作夥伴可以使用 Azure 資源使用情況 API 來取得其預估的 Azure 消耗量資料。這些功能包括：
	
- **Azure 角色型存取控制** - 客戶和合作夥伴可以在 [Azure Preview 入口網站](https://portal.azure.com)上或透過 [Azure PowerShell cmdlet](https://azure.microsoft.com/zh-tw/documentation/articles/powershell-install-configure/) 設定其存取原則，以指定哪些使用者或應用程式可以存取訂用帳戶的使用情況資料。呼叫端必須使用標準的 Azure Active Directory 權杖進行驗證。呼叫端也必須加入至讀取者、擁有者或參與者等角色，才可存取特定 Azure 訂用帳戶的使用情況資料。

- **每小時或每日彙總** - 呼叫端可以指定要 Azure 使用情況資料的每小時值區或每日值區。預設值為每日值區。

- **提供的執行個體中繼資料 (包括資源標記)** – 執行個體層級詳細資料，例如完整的資源 uri (/subscriptions/{subscription-id}/..)，以及資源群組資訊與資源標記都會在回應中提供。這樣可以協助客戶以決定性及程式設計方式，根據標記為跨領域收費之類的使用案例配置使用量。

- **提供的資源中繼資料** - 資源詳細資料 (例如計量名稱、計量類別、計量子類別、單位和區域) 也會在回應中傳遞，讓呼叫端深入了解耗用的內容。我們也致力於跨 Azure 入口網站、Azure 使用情況 CSV、EA 計費 CSV 和其他向外公開的體驗統一資源中繼資料術語，使客戶能夠讓跨體驗的資料相互關聯。

- **所有優惠類型的使用情況** – 所有優惠類型 (包含隨收隨付、MSDN、貨幣承諾、貨幣信用額度和 EA 等等) 的使用情況資料。

### Azure 資源 RateCard API (預覽)
客戶和合作夥伴可以使用 Azure 資源 RateCard API 來取得可用 Azure 資源的清單，連同每個資源的估計定價資訊。這些功能包括：

- **Azure 角色型存取控制** - 客戶和合作夥伴可以在 [Azure 預覽入口網站](https://portal.azure.com)上或透過 [Azure PowerShell cmdlet](https://azure.microsoft.com/zh-tw/documentation/articles/powershell-install-configure/) 設定其存取原則，以指定哪些使用者或應用程式可以存取 RateCard 資料。呼叫端必須使用標準的 Azure Active Directory 權杖進行驗證。呼叫端也必須加入至讀取者、擁有者或參與者等角色，才可存取特定 Azure 訂用帳戶的使用情況資料。
	
- **支援隨收隨付、MSDN、貨幣承諾、貨幣信用額度優惠 (不支援 EA)** - 此 API 提供 Azure 優惠層級費率資訊與訂用帳戶層級資訊。此 API 的呼叫端必須輸入優惠資訊以取得資源詳細資料和費率。由於 EA 優惠已自訂每個註冊的費率，所以我們無法在此時提供 EA 費率。

## 案例

以下是一些案例，可產生使用情況和 RateCard API 的組合：

- **Azure 月份花費** - 客戶可以使用使用情況和 RateCard API 組合，藉由分析每小時和每天的使用情形及收費評估值區，取得當月份雲端花費的更佳見解。 

- **設定警示** – 客戶和合作夥伴可以藉由使用使用情況和 RateCard API 取得預估的耗用量和收費評估，針對雲端耗用量設定以資源為基礎或以貨幣為基礎的警示。

- **預測計費** – 客戶和合作夥伴可以取得預估的耗用量及雲端花費，並應用機器學習演算法來預測他們在計費週期結束時的計費情形。

- **耗用前成本分析** – 如果客戶要藉由提供所需的使用量，將其工作負載移至 Azure，他們也可以使用 RateCard API 預測計費。如果客戶有其他雲端或私用雲端中的現有工作負載，他們也可以對應其使用情況和 Azure 費率，取得已評估 Azure 花費的較佳評估。這樣可增強檢視透過 [Azure 定價計算機](http://azure.microsoft.com/pricing/calculator/)取得的內容，因為計費合作夥伴 (舉例而言) 會提供優惠的樞紐分析功能，並比較/對比超越隨收隨付的不同優惠類型，包含貨幣承諾和貨幣信用額度。API 也會根據區域提供成本評估變更的功能，使假設分析的類型成為部署決策的必要條件，因為在全世界的不同 DC 中部署資源可以對總成本產生直接的影響。

- **假設分析** -

	- 客戶和合作夥伴可以判斷，在另一個區域執行其工作負載，或在 Azure 資源的另一個組態上執行，是否會比較符合成本效益。Azure 資源成本可能會因為其執行所在的 Azure 區域而有所不同，而這可讓客戶和合作夥伴取得成本最佳化。

	- 客戶與合作夥伴也可以判斷另一個 Azure 優惠類型是否會在 Azure 資源上提供更好的費率。

## 合作夥伴解決方案

[Microsoft Azure 使用情況和 RateCard API 可讓 Cloudyn 為客戶提供 ITFM](billing-usage-rate-card-partner-solution-cloudyn.md) 描述 Azure 計費 API 合作夥伴 [Cloudyn](https://www.cloudyn.com/microsoft-azure/) 所提供的整合經驗。本文章提供的詳細範圍包含他們的體驗，包括顯示 Azure 客戶如何使用 Cloudyn，Azure 計費 API 如何從 Azure 耗用量資料取得見解的短片。

[Cloud Cruiser 和 Microsoft Azure 計費 API 整合](billing-usage-rate-card-partner-solution-cloudcruiser.md)描述 [Azure Pack 適用的 Cloud Cruiser Express](http://www.cloudcruiser.com/partners/microsoft/) 如何直接從 WAP 入口網站運作，讓客戶能夠順暢地從單一使用者介面管理其 Microsoft Azure 私人或裝載公用雲端的作業和財務等方面。

## 後續步驟
+ 查看 [Azure 計費 REST API 參考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)以取得兩個 API 的詳細資料，兩者皆屬於 Azure 資源管理員所提供的 API 集合。
+ 如果您想要探究範例程式碼，請查看 [Github 上的 Microsoft Azure 計費 API 程式碼範例](https://github.com/Azure/BillingCodeSamples)。

## 詳細資訊
+ 請參閱 [Azure 資源管理員概觀](resource-group-overview.md)一文，以深入了解 Azure 資源管理員。
+ 如需協助了解雲端花費之必要工具套件的其他資訊，請參閱 Gartner 文章 [IT 財務管理 (ITFM) 工具的市場指南](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb)。

<!---HONumber=July15_HO1-->