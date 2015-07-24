<properties
	pageTitle="Operational Insights 功能指南"
	description="Operational Insights 是一項分析服務，可讓 IT 系統管理員取得跨內部部署和雲端環境的深入見解。它可讓您與即時和歷史電腦資料互動，以快速開發自訂見解，並提供 Microsoft 和社群開發的資料分析模式。"
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2015"
	ms.author="banders"/>

# Operational Insights 功能指南

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

本文協助您了解 Operational Insights 可協助您解決什麼問題、Operational Insights 環境組成項目，以及如何建立 Operational Insights 帳戶和登入服務。

## 轉換電腦資料

Operational Insights 是一項分析服務，可讓 IT 系統管理員取得跨內部部署和雲端環境的深入見解。它可讓您與即時和歷史電腦資料互動，以快速開發自訂見解，並提供 Microsoft 和社群開發的資料分析模式。

使用 Operational Insights，您可以使用下列功能將電腦資料轉換成營運情報。


|**圖示** | **要使用的項目** | **作用**|
|---|---|---|
|![](./media/operational-insights-feature-guide/cap-plan.png) | [容量規劃](operational-insights-capacity.md) | 您可以使用 Microsoft Azure Operational Insights 中的「容量規劃」解決方案，協助您瞭解伺服器基礎結構的容量。 |
| ![](./media/operational-insights-feature-guide/update.png) | [系統更新評估](operational-insights-updates.md) | 您可以使用 Microsoft Azure Operational Insights 中的系統更新解決方案，協助您將遺失的更新套用到基礎結構中的伺服器。 |
| ![](./media/operational-insights-feature-guide/log-mgt.png) | [記錄檔管理](operational-insights-search.md) | 您可以使用「記錄檔管理」解決方案來收集整個 Operational Insights 記錄搜尋的事件和 IIS 記錄檔。 |
| ![](./media/operational-insights-feature-guide/malware.png) | [惡意程式碼評估](operational-insights-antimalware.md) | 您可以使用 Microsoft Azure Operational Insights 中的「反惡意程式碼」解決方案來保護基礎結構中伺服器，避免遭受惡意程式碼侵襲。 |
| ![](./media/operational-insights-feature-guide/sec-audit.png) | [安全性和稽核](operational-insights-security-audit.md) | 您可以使用「安全性和稽核」解決方案，針對值得您注意的問題使用內建的搜尋查詢，對您組織的 IT 安全性狀況獲得全面性的檢視。 |
| ![](./media/operational-insights-feature-guide/assessment.png) | [Active Directory 和 SQL 評估](operational-insights-assessment.md) | 您可以使用「評估」解決方案定期評估伺服器環境的風險和健全狀況。 |
| ![](./media/operational-insights-feature-guide/alert.png) | [警示管理](operational-insights-alerts.md) | 您可以使用「警示管理」解決方案管理來自 Operations Manager 所監視伺服器的警示。 |


您也可以：

- **使用或不使用代理程式或搭配使用 System Center Operations Manager，以將電腦資料傳送至系統**。如需詳細資訊，請參閱：
	- [從 System Center Operations Manager 連接至 Operational Insights](operational-insights-connect-scom.md)
	- [將電腦直接連線到 Operational Insights](operational-insights-direct-agent.md)
	- [在 Microsoft Azure 中分析來自伺服器的資料](operational-insights-analyze-data-azure.md)
- **使用行動應用程式即時執行所有上述作業**
	- 如需 Windows Phone 應用程式的詳細資訊，請參閱 [Operational Insights 行動應用程式](http://www.windowsphone.com/zh-tw/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)。

## Operational Insights 環境

Operational Insights 環境組成項目包含：

- Microsoft Azure 代管的工作區，其為 Azure 帳戶的容器
- 裝載於雲端的 Operational Insights Web 服務
- 直接連接到 Web 服務的獨立代理程式
- 或是 System Center Operations 的附加服務，但不是必要服務


如果您使用的是稱為 System Center Advisor 的舊版 Operational Insights，您的本機環境中可能已安裝 Advisor 軟體。但是，Advisor 軟體無法搭配 Operational Insights 使用。

以 Operations Manager 服務形式使用 Operational Insights 軟體會包含一或多個管理群組，每一管理群組至少包含一個代理程式。Operations Manager 代理程式會使用解決方案 (類似 System Center Operations Manager 中的管理組件) 收集您伺服器中的資料並進行分析。資料會定期從 Operations Manager 傳送至 Operational Insights Web 服務 (必要時，透過 Proxy 伺服器傳遞)，而不會將任何項目儲存在任何 Operations Manager 資料庫，因此不會對它們造成額外負載。

同樣地，安裝在個別電腦上的代理程式可以直接連接到 Web 服務，以傳送收集的資料進行處理。

每個解決方案中的資料都會經過分析、編製索引，然後呈現在 Operational Insights 入口網站中。您可以檢視任何警示和相關的補救措施、組態評估、基礎結構容量問題、系統更新狀態、反惡意程式碼警告及記錄檔資料。您也可以執行詳細臨機操作記錄搜尋和瀏覽。

![Operational Insights 概觀圖表的影像](./media/operational-insights-feature-guide/environment.png)

### 可在何處取得 Operational Insights？
Microsoft Azure Operational Insights 位於美國。雖然 Operational Insights 的語言是英文，但是數個其他市場都提供此服務。如需相關資訊，請參閱[國際可用性](http://go.microsoft.com/fwlink/?LinkId=229842)。
 

<!---HONumber=July15_HO1-->