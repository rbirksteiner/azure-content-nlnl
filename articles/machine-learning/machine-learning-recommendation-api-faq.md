<properties 
	pageTitle="設定和使用 Machine Learning Recommendations API | Microsoft Azure" 
	description="以 Azure Machine Learning 建置之 Microsoft RECOMMENDATIONS API 的常見問題集" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="luisca"/>

#設定和使用 Machine Learning Recommendations API 的常見問題集


**什麼是 RECOMMENDATIONS？**

對於依賴建議對客戶進行交叉銷售和向上銷售產品和服務的組織和企業，Azure Machine Learning 的 RECOMMENDATIONS 可提供自助建議引擎。它是協同篩選的實作，其使用矩陣分解作為核心演算法。應用程式開發人員可以使用 REST API 來存取 RECOMMENDATIONS。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

**我可以用 RECOMMENDATIONS 來做什麼？**

RECOMMENDATIONS 將一個項目或一組項目作為輸入，並傳回相關建議清單。例如：線上零售商的客戶會按一下產品。線上零售商會將該產品當作輸入傳送至 RECOMMENDATIONS，取得回報的產品清單，並決定要向客戶顯示哪些產品。您可以使用 RECOMMENDATIONS 來最佳化您的線上商店，或甚至通知您的內部銷售部門或客服中心。

**是否有任何使用量限制？**

Recommendations 具有下列使用量限制：* 每一訂用帳戶的模型數上限：10 * 一個目錄可存放的項目數上限：100000 * 保留的使用點數上限是 ~5,000,000。如果將上傳或回報新的點，就會將最舊的點刪除。* 電子郵件 (例如，匯入目錄資料、匯入使用量資料) 中可以傳送的資料大小上限為 200 MB * 非作用中 Recommendations 模型組建的每秒交易數目 (TPS) 是 ~2 TPS。作用中 Recommendations 模型組建可以保留高達 20 TPS。

##購買和計費 


**在推出期間，Recommendations 的價格為何？**

Recommendations 是一項以訂用帳戶為基礎的服務。收費是根據每個月的交易量來進行。如需定價資訊，您可以在 Microsoft Azure Marketplace 中查看[提供項目頁面](https://datamarket.azure.com/dataset/amla/recommendations)。

**讓 Recommendations 為我追蹤和儲存使用者活動是否有任何相關聯的成本？**

目前沒有。

**Recommendations 是否有免費試用版？**

免費試用版限制在每個月 10000 筆交易。

**Recommendations 何時會計費？**

付費的訂閱就是任何按月計價的訂閱。當您購買付費的訂用帳戶時，我們會立即向您收取第一個月的使用費用。您需支付訂用帳戶頁面上提供項目旁邊顯示的相關金額 (加上適用稅額)。這筆每月費用會每個月在與您原先購買相同的行事曆日期收取，直到您取消訂閱為止。

**如何升級至較高層的服務？**

您可以在 Microsoft Azure Marketplace 上透過[提供項目頁面](https://datamarket.azure.com/dataset/amla/recommendations)頁面來購買或更新您的訂用帳戶。

當您升級訂閱時：

* 舊訂用帳戶剩餘的交易不會加至新的訂用帳戶。 
* 即使您的舊訂閱有未使用的交易，仍需支付新訂閱的完整價格。

升級訂用帳戶的程序：

* 導覽至[提供項目頁面](https://datamarket.azure.com/dataset/amla/recommendations)。
* 如果您還沒有登入，請登入 Marketplace。
* 在右窗格中，會列出所有可用的計畫。按一下您要升級至的計劃的選項按鈕。
* 如果您想要升級，請按一下 [**確定**]。如果您不想要升級，請按一下 [**取消**]。

**重要事項**：升級前，請先仔細閱讀對話方塊，因為其中有計費和使用暗示。

**我的 Recommendations 訂用帳戶何時會結束？**

您的訂閱會在您取消時結束。如果您想要取消您的訂用帳戶，請參閱下列指示。

**如何取消我的 Recommendations 訂用帳戶？**

若要取消您的訂用帳戶，請使用下列步驟。如果您目前的訂閱是付費的訂閱，則您的訂閱會持續有效，直到目前的計費期間結束為止。如果您需要取消立即生效，請透過 [Microsoft 支援](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn)與我們連絡。

**附註**：如果您在計費期間結束前取消，或對於計費期間內未使用的交易，均不會提供退款。

* 導覽至[提供項目頁面](https://datamarket.azure.com/dataset/amla/recommendations)。
* 如果您還沒有登入，請登入 Marketplace。
* 按一下資料集名稱和狀態右邊的 [**取消**]。您可以使用此訂用帳戶，直到目前的計費期間結束或達到您的交易限制為止 (以較早發生者為準)。

如果您想要立即取消訂用帳戶，以便購買新的訂用帳戶，請透過 [Microsoft 支援](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn)提出票證。

##開始使用 Recommendations

**Recommendations 是給我的嗎？**

機器學習服務中的 Recommendations 適用於依賴建議向客戶進行交叉銷售和向上銷售產品或服務的組織和企業。如果您有面向客戶的網站、銷售團隊、內部銷售團隊或客服中心，而且如果您提供的目錄有好幾十項產品或服務 – 您的盈餘即可受惠於使用 Recommendations。

實驗 Recommendations 的設計相當簡單。目前的 API 版本可執行必要的基本程式設計技能。如需協助，請連絡您網站的開發廠商。如果您有內部 IT 部門或內部的開發人員，他們應可為您取得 Recommendations。

**設定 Recommendations 的必要條件為何？**

Recommendations 要求您有與您的目錄相關的使用者選擇記錄檔。如果您沒有這類記錄檔，而且有面向客戶的網站，則 Recommendations 可為您收集使用者活動。

Recommendations 也需要您的產品或服務目錄。如果您沒有目錄，Recommendations 可以使用實際的客戶使用資料並摘錄成目錄。「隱含」目錄不包含未「回報」成為使用者交易一部分的項目。

**我如何在第一次設定 Recommendations？**

[訂用帳戶](https://datamarket.azure.com/dataset/amla/recommendations) Recommendations 之後，您應該使用 [Azure Machine Learning Recommendations 快速入門指南](machine-learning-recommendation-api-quick-start-guide.md)中的 API 文件來設定服務。

**哪裡可以找到 API 文件？**

API 文件是 [Azure Machine Learning Recommendations - 快速入門指南](machine-learning-recommendation-api-quick-start-guide.md)。

**有哪些選項可將目錄和使用資料上傳至 Recommendations？**

您有兩個選項可以上傳您的目錄和使用量資料：您可以從 CRM 系統或其他記錄檔匯出資料，並將它上傳至 Recommendations，或您可以將會追蹤使用者活動的標記加入至您的網站。如果您使用第二個方法，資料會儲存在 Azure 中。

##維護與支援

**我可以有多大的資料集？**

每個資料集可包含多達 100000 個目錄項目以及高達 2048 MB 的使用資料。此外，一個訂閱最多可包含 10 個資料集 (模型)。

**哪裡可以取得 Recommendations 的技術支援？**

技術支援可在 [Microsoft Azure 支援](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning)網站取得。

**哪裡可以找到使用條款？**

[Microsoft Azure Machine Learning Recommendations API 服務條款](https://datamarket.azure.com/dataset/amla/recommendations#terms)。



 

<!---HONumber=July15_HO1-->