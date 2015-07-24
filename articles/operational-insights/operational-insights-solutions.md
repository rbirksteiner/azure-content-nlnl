<properties
	pageTitle="Operational Insights 解決方案"
	description="Operational Insights 是一項分析服務，可讓 IT 系統管理員取得跨內部部署和雲端環境的深入見解。它可讓您與即時和歷史電腦資料互動，以快速開發自訂見解，並提供 Microsoft 和社群開發的資料分析模式。"
	services="operational-insights"
	documentationCenter="n/a"
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="operational-insights"
	ms.tgt_pltfrm="NA"
	ms.devlang="NA"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="alfran"/>

# Operational Insights 中的解決方案

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

解決方案是一個**邏輯**、**視覺效果**和**資料擷取規則**集合，可解決客戶現今面臨的重要挑戰。解決方案由 Operational Insights 記錄搜尋提供，可讓您度量特定問題領域的核心。它們允許更深入的探討，以協助調查並更快解決操作問題、收集並相互關聯各種類型的機器資料，並協助您主動出擊，例如容量規劃、修補狀態報告和安全性稽核。

本指南將引導您逐步執行各種可用的解決方案及其用途。

>[AZURE.NOTE]如需有關*加入*解決方案的詳細資訊，請參閱[加入解決方案](operational-insights-add-solution.md)

## SQL 評估

SQL 評估解決方案會定期評估 SQL Server 環境的風險和健全狀況。它預設會每週掃描 SQL 系統，並顯示每月彙總的資訊。它提供專門為您的部署所量身訂做的優先建議清單。這些建議是跨六個焦點區域分類，可讓您和您的小組：

- **快速**瞭解環境的**風險和健全狀況**
- 輕鬆地採取**動作**來降低風險並改善健全狀況
- 排定您工作的**優先順序**，而且變得更有**生產力**

SQL 評估需要在每個代理程式上執行 .NET 4。它支援 Standard、Developer 和 Enterprise 版本的 SQL Server 中，所有目前支援的版本。請參閱[利用評估解決方案最佳化環境](operational-insights-assessment.md)。


## 組態評估

組態評估將提供您的伺服器基礎結構的目前狀態的詳細資訊。

不同於解決方案，組態評估是您第一次開始使用 Operational Insights 與 System Center Operations Manager 所取得。如果您只使用直接連接的代理程式，則不使用它。

設定資料是從受監視伺服器中收集了，然後傳送至雲端中 Operational Insights 服務進行處理。會將邏輯套用至接收的資料，且雲端服務會記錄資料。會顯示下列各方面有關伺服器處理的資料：

- **警示：**顯示已經為受監視伺服器產生的組態相關、主動警示。這些是由 Microsoft 客戶和支援組織 (CSS) 與最佳現場實務所撰寫的規則所產生
- **知識建議事項：**為在您的基礎結構中發現的工作負載顯示建議的 Microsoft 知識庫文件；它們是透過機器學習，據據您的組態所提供的自動建議
- **分析的伺服器和工作負載：**顯示 Operational Insights 正在監視的伺服器和工作負載
- **目前的快照集：**顯示向 Operational Insights 服務報告資料的伺服器最新相關資訊
- **變更記錄：**顯示對您的受監視伺服器組態所做的變更清單

> [AZURE.IMPORTANT]組態評估只能為 **Operations Manager** 管理的電腦啟用。如需詳細資訊，請參閱[連接 Operations Manager](operational-insights-connect-scom.md)


## 惡意程式碼評估
如果發現防護不足，[**惡意程式碼**] 頁面會顯示具有作用中威脅的伺服器和防護不足的伺服器。藉由使用 [**惡意程式碼**] 頁面上的資訊，您可以開發計劃，向需要它的伺服器套用保護。

> [AZURE.IMPORTANT]惡意程式碼評估目前僅支援 Windows Defender 和 System Center Endpoint Protection (SCEP) 即時用戶端。如果找不到其中任何用戶端，它會使用惡意軟體移除工具的資料，並將伺服器標記為不具有即時保護。您可以在[這裡](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519202-support-other-antivirus-products-in-malware-assess)閱讀更多資訊。我們也追蹤 [Windows 7 和 Windows Server 2008 R2 電腦的問題](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519211-windows-server-2008-r2-sp1-servers-are-shown-as-n)





## 警示管理

使用警示管理解決方案，您可以跨所有伺服器檢視 **Operations Manager** 警示。此工具可以幫助您輕鬆地分級警示，並以非常快速和流暢的方式，在您的環境中識別根本原因。

您可以深入了解重要的案例，包括：

- 指定的時間範圍內引發了多少警示？
- 在指定的時間範圍內，引發最多作用中警示的前幾個來源。
- 在指定的時間範圍內引發的前幾個作用中的重大和警告警示。
- 詳細搜尋所有警示和檢視每個警示的能力

> [AZURE.IMPORTANT]只有在搭配使用 Operational Insights 與 **System Center Operations Manager** 時，才可以啟用警示管理。這不會從代理程式傳送任何資料，但只會同步處理 Operations Manager 警示到雲端，可讓您在 Operational Insights 中分級它們並使用記錄搜尋。如需詳細資訊，請參閱[連接 Operations Manager](operational-insights-connect-scom.md)


## 容量規劃

您可以使用 Microsoft Azure Operational Insights 中的容量管理解決方案，協助您瞭解伺服器基礎結構的容量。解決方案會讀取受監視伺服器上的效能計數器，並將使用量資料傳送至雲端中的 Operational Insights 服務進行處理。會將邏輯套用至使用量資料，且雲端服務會記錄資料。經過一段時間，會根據目前的耗用量識別使用模式和規劃容量。

比方說，當個別伺服器會需要額外的處理器核心或額外的記憶體時，可能會識別投影。在此範例中，投影可能表示在 30 天內，伺服器將需要額外的記憶體。這可協助您規劃在伺服器的下一段維護期間升級記憶體，這可能會每隔兩週發生一次。

>[AZURE.IMPORTANT]只有在搭配使用 Operational Insights 與 **System Center Operations Manager**，而且啟用 Operations Manager 連接器與 Virtual Machine Manager (VMM) 時，才能啟用容量管理。如需連接系統的詳細資訊，請參閱[如何連接 VMM 與 Operations Manager](https://technet.microsoft.com/library/hh882396.aspx)。

如需有關使用容量管理解決方案的詳細資訊，請參閱：

- [如何使用計算頁面](operational-insights-capacity/#compute-page)
- [如何使用直接附加儲存體頁面](operational-insights-capacity/#direct-attached-storage-page)


## 變更追蹤

您可以使用組態變更追蹤解決方案來幫助您輕鬆地識別軟體，及您環境中發生的 Windows 服務變更 — 識別這些組態變更可以協助您找出操作問題。使用 [變更追蹤] 頁面上的資訊，您可以輕鬆地看到您的伺服器基礎結構中所做的變更。您可以檢視基礎結構的變更，然後向下鑽研下列類別的詳細資訊：

- 軟體和 Windows 服務的組態類型所做的變更
- 應用程式的軟體變更和個別伺服器的更新
- 每個應用程式的軟體變更總數
- 個別伺服器的 Windows 服務變更


## 系統更新評估

您可以使用 Microsoft Azure Operational Insights 中的系統更新解決方案，協助您將遺失的更新套用到基礎結構中的伺服器。如果找到遺失的更新，它們會顯示在 [**更新**] 頁面。您可以使用 [**更新**] 頁面處理遺失的更新及開發計劃，將它們套用至所需要的伺服器。

[更新] 頁面詳細說明下列類別：

- 遺失安全性更新的伺服器
- 最近尚未更新的伺服器
- 應套用到特定伺服器的更新
- 遺失的更新類型

您可以在 [**搜尋**] 頁面中按一下任何磚或項目以檢視其詳細資料，即可取得遺失更新的詳細資訊。搜尋結果包括：

- 伺服器
- 更新標題
- 知識庫識別碼
- 更新適用的產品
- 更新嚴重性
- 發行日期

伺服器搜尋結果包括：

- 伺服器名稱
- 作業系統版本名稱
- 自動更新啟用方法
- 上次更新後至今的天數
- Windows Update 代理程式版本

<!---HONumber=July15_HO2-->