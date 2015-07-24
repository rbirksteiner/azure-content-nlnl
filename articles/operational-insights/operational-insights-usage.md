<properties
   pageTitle="管理伺服器和使用量資料"
   description="了解從您的伺服器傳送至 Operational Insights 服務的資料量"
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

# 管理伺服器和使用量資料

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operational Insights 會收集資料，並定期將資料傳送給 Operational Insights 服務。您可以使用 [**使用量**] 儀表板，檢視正在傳送給 Operational Insights 服務的資料量。[**使用量**] 儀表板也會顯示解決方案每日正在傳送的資料量，及您的管理群組正在傳送資料的頻率。

>[AZURE.NOTE]如果您有一個免費帳戶，您每天只能將 500 MB 的資料傳送給 Operational Insights 服務。如果達到每日限制，資料分析會停止並在隔天開頭繼續分析。

您可以在 Operational Insights 的 [**概觀**] 儀表板，使用 [**伺服器和使用量**] 磚來檢視您的使用量。

![伺服器和使用量磚的影像](./media/operational-insights-usage/overview-servers-usage.png)

如果您已超過每日使用量上限，或如果接近限制，您可以選擇性地移除解決方案來減少傳送至 Operational Insights 服務的資料量。如需移除解決方案的詳細資訊，請參閱[使用解決方案資源庫來新增或移除解決方案](operational-insights-add-solution.md)。

如果 Operations Manager 管理群組將資料傳送至 Operational Insights 服務時發生問題，您可以疑難排解問題，或在需要時從 Operational Insights 移除群組。

![使用儀表板的影像](./media/operational-insights-usage/usage-dash.png)

[**使用量**] 儀表板會顯示下列資訊：

- 每日平均使用量

- 今天的每個解決方案的資料使用量

- 每個管理群組的伺服器將資料傳送至 Operational Insights 服務的頻率為何

## 處理使用量資料

1. 在 [**概觀**] 頁面上，按一下 [**伺服器和使用量**] 磚。

2. 在 [**使用量**] 儀表板上檢視使用量類別，其顯示您關注的領域。

3. 如果得知有不必要使用大量配額的解決方案，您可以考慮移除該解決方案。

## 疑難排解或移除管理群組

1. 在 [**概觀**] 頁面上，按一下 [**伺服器和使用量**] 磚。

2. 在 [**使用量**] 儀表板上，檢視未傳送資料的管理群組相關資訊。

3. 如果目前有未傳送資料的管理群組，您可以按一下 [**疑難排解**] 以取得詳細的疑難排解資訊。如果您不想再保留管理群組及向他報告的所有代理程式，請按一下 [**移除**]。

<!---HONumber=July15_HO2-->