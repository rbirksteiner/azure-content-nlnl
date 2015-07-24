<properties
   pageTitle="如何使用記錄檔管理"
   description="透過 Microsoft Azure Operational Insights 中的記錄檔管理，您可以檢視從受監視伺服器收集的記錄檔事件"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="operational-insights"
   ms.date="07/02/2015"
   ms.author="banders"/>

# 使用記錄檔管理

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

在 Microsoft Azure Operational Insights 中使用記錄檔管理之前，您必須安裝解決方案。因為必須設定這個套件，且預設不會收集任何項目，因此預設會在每個新工作區中啟用它。如果您已移除它，您可以從解決方案資源庫中重新加入它，請參閱[使用解決方案資源庫來新增或移除解決方案](operational-insights-add-solution.md)。

您可以加入新的記錄檔來收集事件，並選擇要針對記錄檔收集哪些事件層級或嚴重性。設定記錄檔管理後，它接著會往下推送收集原則並開始收集事件。您也可以在 Operational Insights 的 [**概觀**] 上使用 [**記錄檔管理**] 磚，存取從受監視伺服器收集的記錄檔事件的一些初始細項。

![記錄檔管理磚的影像](./media/operational-insights-log-collection/overview-log-mgt.png)

此磚會開啟 [**記錄檔管理**] 頁面，您可在其中檢視記錄檔包含的事件摘要。或者，您可以直接使用記錄搜尋，就像對任何其他資料類型所做的一樣。


此頁面詳細說明下列類別：

- Windows 事件記錄檔
- IIS 記錄檔
- 您已新增的其他記錄檔

![記錄檔管理儀表板的影像](./media/operational-insights-log-collection/gallery-logmgt-01.png)

![記錄檔管理儀表板的影像](./media/operational-insights-log-collection/gallery-logmgt-02.png)

## IIS 記錄檔格式

目前支援的唯一 IIS 記錄檔格式是 W3C。別擔心，它是最常見的格式，而且是 IIS 7 和 IIS 8 中的預設格式。因此，如果您登入 NCSA 或 IIS 原生格式，Operational Insights 完全不會收取這些記錄檔。即使是 W3C 格式，預設也不會記錄所有欄位。您可以在 [選取要記錄的 W3C 欄位至記錄檔 (IIS 7)] 閱讀更多有關格式的資訊 (https://technet.microsoft.com/library/cc754702(v=WS.10).aspx)。


> [AZURE.TIP]如需最佳的記錄搜尋經驗，建議您使用 IIS 中的 [**記錄**]，為每個網站選取所有記錄欄位。我們也建議您將新記錄檔的 [**記錄檔變換**] 排程變更為 [**每小時**]，讓較小的檔案可上傳至雲端來節省頻寬。


### 從 Operations Manager 或直接連接的代理程式收集 Windows 事件記錄檔

1. 在 [**概觀**] 頁面上，按一下 [**記錄檔管理**] 磚。

2. 在 [**記錄檔管理**] 儀表板，按一下 [**設定**] 磚。

3. 輸入您想要從中收集資訊之事件記錄檔的名稱。如果您不確定要使用的名稱，請在 [**事件檢視器**] 中選取 Windows 事件記錄檔的屬性，在 [**全名**] 欄位中複製名稱，並在 [**從下列事件記錄檔收集事件**] 方塊中貼上名稱。

4. 按一下 [**+**] 加入記錄檔。

5. 選取要針對記錄檔收集哪些事件層級或嚴重性。這個版本不支援 [**稽核成功**] 和 [**稽核失敗**] 事件。

6. 針對您要從中收集資訊的每個記錄檔重複上述步驟，然後按一下 [**儲存**]。

7. Operational Insights 應該在幾分鐘內顯示事件，接著您就可以搜尋資料。



### 從 Operations Manager 或直接連接的代理程式收集 IIS 記錄檔

1. 在 [**概觀**] 頁面上，按一下 [**記錄檔管理**] 磚。

2. 在 [**記錄檔管理**] 儀表板，按一下 [**設定**] 磚。

3. 在 [**IIS 記錄檔**] 下，選取 [**從 Operations Manager 收集記錄檔**]。


### 從 Azure 診斷收集 IIS 記錄檔和/或 Windows 事件
這可從 Azure 管理入口網站中設定，而不必在 Operational Insights 入口網站中設定，方法是在工作區下，移至 [**儲存體**] 索引標籤，即可從該儲存體帳戶啟用記錄檔收集。


### 使用記錄檔

1. 在 [**概觀**] 頁面上，按一下 [**記錄檔管理**] 磚。

2. 在 [**記錄檔管理**] 儀表板，檢視事件記錄檔，並選擇要使用的其中一個記錄檔。

3. 在 [**搜尋**] 頁面中，按一下任何項目來檢視其詳細資訊。

4. 您可以從初始結果執行其他記錄搜尋，然後分析並相互關聯記錄檔。


## 其他資源
- [Azure Operational Insights 中的 IIS 記錄檔格式需求](http://blogs.technet.com/b/momteam/archive/2014/09/19/iis-log-format-requirements-in-system-center-advisor.aspx)
- 請在[意見反應論壇](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy)中，參閱社群要求我們實作的記錄檔的其他資料來源和類型

<!---HONumber=July15_HO2-->