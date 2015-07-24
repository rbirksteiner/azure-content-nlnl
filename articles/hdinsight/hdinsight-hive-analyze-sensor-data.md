<properties
	pageTitle="使用 Hive 及 Hadoop 分析感應器資料 | Microsoft Azure"
	description="了解如何使用 Hive 查詢主控台搭配 HDInsight (Hadoop) 分析感應器資料，然後在 Microsoft Excel 中使用 Power View 將資料視覺化。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2015" 
	ms.author="larryfr"/>

#在 HDInsight 的 Hadoop 上使用 Hive 查詢主控台分析感應器資料

了解如何使用 Hive 查詢主控台搭配 HDInsight (Hadoop) 分析感應器資料，然後在 Microsoft Excel 中使用 Power View 將資料視覺化。

> [AZURE.NOTE]本文件的步驟只適用於 Windows HDInsight 叢集。

在此範例中，您將使用 Hive 來處理暖氣、通風和空調 (HVAC) 系統所產生的歷史資料，以識別無法可靠地維持規定溫度的系統。您將了解如何：

- 建立 Hive 資料表來查詢逗點分隔值 (CSV) 檔案中儲存的資料。
- 建立 Hive 查詢以分析資料。
- 使用 Microsoft Excel 連接到 HDInsight (使用開放式資料庫連接，ODBC) 來擷取已分析的資料。
- 使用 Power View 將資料視覺化。

![A diagram of the solution architecture](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

##必要條件

* HDInsight (Hadoop) 叢集：如需建立叢集的相關資訊，請參閱[在 HDInsight 中佈建 Hadoop 叢集](hdinsight-provision-clusters.md)。

* Microsoft Excel 2013

	> [AZURE.NOTE]Microsoft Excel 用於搭配 [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US) 進行資料視覺化。

* [Microsoft Hive ODBC 驅動程式](http://www.microsoft.com/download/details.aspx?id=40886)

##執行範例

1. 從 Azure 入口網站，按一下您要執行範例的叢集，然後按一下底部的 [查詢主控台]。或者，您也可以使用下列 URL 直接開啟查詢主控台：

	 	https://<clustername>.azurehdinsight.net

	出現提示時，使用您佈建此叢集時所用的系統管理員使用者名稱和密碼來進行驗證。

2. 從開啟的網頁中，按一下 [Getting Started Gallery] 索引標籤，然後在 [搭配範例資料的方案] 類別下，按一下 [感應器資料分析] 範例。

3. 依照網頁上提供的指示完成範例。

<!---HONumber=July15_HO2-->