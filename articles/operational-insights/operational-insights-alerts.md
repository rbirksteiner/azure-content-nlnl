<properties
   pageTitle="檢視 Operations Manager 的警示"
   description="了解如何針對基礎結構中受監視的伺服器管理 Operations Manager 的警示"
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



# 檢視 Operations Manager 警示

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

在 Microsoft Azure Operational Insights 中使用警示管理之前，您必須安裝解決方案。如需閱讀安裝解決方案的更多資訊，請參閱[使用解決方案資源庫來新增或移除解決方案](operational-insights-add-solution.md)。唯有當您的伺服器受 Operations Manager 代理程式監視時，解決方案才能發揮作用。如需有關搭配使用 Operations Manager 與 Operational Insights 的資訊，請參閱[從System Center Operations Manager 連接到 Operational Insights](operational-insights-connect-scom.md)。

安裝解決方案後，您可以在 Operational Insights 中使用 [**概觀**] 儀表板上的 [**警示管理**] 磚檢視受監視伺服器的警示。

![image of Alert Management tile](./media/operational-insights-alerts/overview-alert.png)


您可以從 [**警示**] 儀表板來檢視及管理 Microsoft Azure Operational Insights 警示。顯示於儀表板中的警示資訊分為以下類別：

- 作用中警示
	- 重大警示
	- 警告警示
	- 警示來源
- 所有作用中警示
	- 顯示重大、警告及資訊等警示的百分比。
- 常用警示查詢
	- 這個區域包含預先建置的查詢，這些查詢是 Operational Insights 軟體開發團隊為協助您開始使用警示而建立的。


警示能通知您偵測到問題、受警示影響的伺服器、優先順序及警示名稱。

![image of Alert dashboard](./media/operational-insights-alerts/alert-drilldown1.png)

![image of Alert dashboard](./media/operational-insights-alerts/alert-drilldown2.png)


在 [**警示管理**] 儀表板中，您可以檢視 Microsoft Azure Operational Insights 發現的所有警示。

## 檢視 Operational Insights 警示

1. 在 [**概觀**] 頁面上，按一下 [**警示管理**] 磚。
2. 在 [**警示管理**] 儀表板中檢視警示類別，然後選擇要操作的警示。
3. 在 [**搜尋**] 頁面中，按一下磚或任何項目來檢視其詳細資訊。
4. 透過發現的資訊，您可以調查警示的成因，以及判斷解決警示需要採取的額外措施。

<!---HONumber=July15_HO2-->