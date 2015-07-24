<properties
   pageTitle="使用變更追蹤識別差異"
   description="使用 Microsoft Azure Operational Insights 中的組態變更追蹤解決方案，幫助您輕鬆地識別環境中的軟體和發生的 Windows 服務變更。"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# 使用變更追蹤識別差異

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

您可以使用 Microsoft Azure Operational Insights 中的組態變更追蹤解決方案來幫助您輕鬆地識別軟體，及您環境中發生的 Windows 服務變更 — 識別這些組態變更可以協助您找出操作問題。

您需要安裝解決方案以更新 Operational Insights 的 Operations Manager 代理程式和基底組態模組。系統會讀取受監視伺服器上安裝的軟體和 Windows 服務，然後將資料傳送至雲端中的 Operational Insights 服務進行處理。會將邏輯套用至接收的資料，且雲端服務會記錄資料。發現變更時，會在變更追蹤儀表板中顯示伺服器及其變更。使用 [變更追蹤] 儀表板上的資訊，您可以輕鬆地看到您的伺服器基礎結構中所做的變更。

## 使用變更追蹤

在 Operational Insights 中使用 [變更追蹤] 之前，您必須安裝解決方案。如需閱讀安裝解決方案的更多資訊，請參閱[使用解決方案資源庫來新增或移除解決方案](operational-insights-add-solution.md)。

安裝之後，您可以在 Operational Insights 的 [**概觀**] 頁面，使用 [**變更追蹤**] 磚來檢視受監視伺服器的變更摘要。

![變更追蹤磚的影像](./media/operational-insights-change-tracking/overview-change-track.png)

您可以檢視基礎結構的變更，然後向下鑽研下列類別的詳細資訊：

- 軟體和 Windows 服務的組態類型所做的變更

- 應用程式的軟體變更和個別伺服器的更新

- 每個應用程式的軟體變更總數

- 個別伺服器的 Windows 服務變更

![變更追蹤儀表板的影像](./media/operational-insights-change-tracking/gallery-changetracking-01.png) ![變更追蹤儀表板的影像](./media/operational-insights-change-tracking/gallery-changetracking-02.png)

### 檢視任何變更類型的變更

1. 在 [**概觀**] 頁面上，按一下 [**變更追蹤**] 磚。

2. 在 [**變更追蹤**] 儀表板，檢閱其中一種變更類型刀鋒的摘要資訊，然後在 [**記錄搜尋**] 頁面中，按一下其中一個刀鋒來檢視詳細資訊。

3. 您可以在任何 [記錄搜尋] 頁面上，按時間、詳細結果和您的記錄搜尋記錄來檢視結果。您也可以按 Facet 篩選以縮減結果。

4. 您可以在 [記錄搜尋] 的任何頁面上，將結果詳細資料**匯出**到 CSV 檔，接著使用 Excel 或其他應用程式開啟該檔案以檢視或修改。

<!---HONumber=July15_HO2-->