<properties
    pageTitle="Operational Insights 解決方案"
    description="您可以使用解決方案將其他功能加入 Operational Insights"
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/02/2015"
    ms.author="banders"/>

# Operational Insights 解決方案

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure Operational Insights 含有基礎組態評估模組。然而，透過從 [概觀] 頁面加入解決方案，您可以取得額外的功能。

![解決方案圖示的影像](./media/operational-insights-add-solution/sol-gallery.png)

加入解決方案後，系統會從基礎結構中的伺服器收集資料，然後再傳送到 Operational Insights 服務。Operational Insights 服務的處理時間少則幾分鐘，多則達數小時之久。待服務處理完資料後，您可以在 Operational Insights 中予以檢視。

當您不再需要解決方案時，您可以輕易地將它移除。移除解決方案後，系統不會將資料傳送到 Operational Insights，因此可減少每日配額所使用的資料量。

## Microsoft Monitoring Agent 支援的解決方案

在現階段，使用 Microsoft Monitoring Agent 直接連接 Microsoft Azure Operational Insights 的伺服器可使用目前可用的大多數解決方案，包括：

- [系統更新](operational-insights-updates.md)

- [記錄檔管理](operational-insights-log-collection.md)

- [反惡意程式碼](operational-insights-antimalware.md)

- [變更追蹤](operational-insights-change-tracking.md)

- [SQL 和 Active Directory 評估](operational-insights-assessment.md)

然而，以下是 Microsoft Monitoring Agent *不*支援的解決方案，需要 System Center Operations Manager (SCOM)。

- [產能管理](operational-insights-capacity.md)

- [警示管理](operational-insights-alerts.md)

- [組態評估](operational-insights-solutions.md#configuration-assessment)

如需搭配使用這些解決方案和 Operations Manager 的指引，請參閱 [Operational Insights 的 Operations Manager 考量](operational-insights-operations-manager.md)。

安裝以下作業系統的電腦支援 IIS 記錄檔收集：

- Windows Server 2012

- Windows Server 2012 R2

### 若要新增解決方案


1. 在 Operational Insights 的 [概觀] 頁面中按一下 [**解決方案資源庫**] 磚。


2. 在 [Operational Insights 解決方案資源庫] 頁面中，您可以了解每個可用的解決方案。按一下要加入 Operational Insights 之解決方案的名稱。


3. 在所選解決方案的頁面中，該解決方案的詳細資料會顯示於此。按一下 [新增]。


4. 在確認頁面中，按一下 [**接受**] 以同意隱私權聲明和使用條款。


5. 加入解決方案而新建立的磚會出現在 Operational Insights 的 [概觀] 頁面中；待 Operational Insights 服務處理資料後，您便可以開始使用解決方案。




### 若要移除解決方案



1. 在 Operational Insights 的 [概觀] 頁面中按一下 [**解決方案資源庫**] 磚。


2. 在 [Operational Insights 解決方案資源庫] 頁面中，於要移除的解決方案下方按一下 [**移除**]。


3. 在確認頁面中按一下 [**是**] 以移除解決方案。

<!---HONumber=July15_HO2-->