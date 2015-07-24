<properties
   pageTitle="使用系統更新來更新伺服器"
   description="了解如何使用 Microsoft Azure Operational Insights 中的系統更新解決方案，協助您將遺失的更新套用到基礎結構中的伺服器"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# 使用系統更新來更新伺服器

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

您可以使用 Microsoft Azure Operational Insights 中的系統更新解決方案，協助您將遺失的更新套用到基礎結構中的伺服器。您需要安裝解決方案以更新 Operational Insights 的 Operations Manager 代理程式和基底組態模組。更新資訊是在受監視伺服器上讀取，然後將更新資料傳送至雲端中的 Operational Insights 服務進行處理。會將邏輯套用至更新資料，且雲端服務會記錄資料。如果找到遺失的更新，它們會顯示在 [**更新**] 儀表板。您可以使用 [**更新**] 儀表板處理遺失的更新及開發計劃，將它們套用至所需要的伺服器。

## 使用系統更新來更新伺服器

在 Microsoft Azure Operational Insights 中使用系統更新之前，您必須安裝解決方案。如需閱讀安裝解決方案的更多資訊，請參閱[使用解決方案資源庫來新增或移除解決方案](operational-insights-add-solution.md)。安裝後，您可以檢視受監視伺服器缺少的更新，方法是在 Operational Insights 的 [**概觀**] 頁面上使用 [**系統更新評估**] 磚。

### 使用更新

1. 在 [**概觀**] 頁面上，按一下 [**系統更新評估**] 磚。![概觀頁面的影像](./media/operational-insights-updates/updates01.png)
2. 在 [**更新**] 儀表板上，檢視更新類別。![更新頁面的影像](./media/operational-insights-updates/updates02.png)
3. 捲動到頁面的右邊，以檢視 [**遺失的更新類型**] 刀鋒視窗，然後按一下 [**安全性更新**]。![更新頁面的影像](./media/operational-insights-updates/updates03.png)
4. 在 [搜尋] 頁面上，會顯示在基礎結構的伺服器中遺失的安全性更新清單。按一下知識庫文章識別碼 (KBID) 以檢視遺失更新的詳細資訊。在此範例中，*KBID 3032323*。![更新頁面的影像](./media/operational-insights-updates/updates04.png)
5. 網頁瀏覽器會開啟描述更新的知識庫文章。![更新頁面的影像](./media/operational-insights-updates/updates05.png)
6. 使用您已經找到的資訊，您可以建立計畫以套用遺失的更新。

<!---HONumber=July15_HO2-->