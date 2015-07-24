<properties
	pageTitle="在 HDInsight 上執行 Hadoop 範例 | Microsoft Azure"
	description="利用提供的範例開始使用 Azure HDInsight 服務。使用 PowerShell 指令碼在資料叢集上執行 MapReduce 程式。"
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2015"
	ms.author="bradsev"/>




#在 HDInsight 上執行 Hadoop 範例

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

我們提供了一組範例，協助您使用 Azure HDInsight 並開始在 Hadoop 叢集上執行 MapReduce 工作。這些範例可套用在您所建立的每個 HDInsight 受管理叢集上。執行這些範例可協助您熟悉使用 Azure PowerShell Cmdlet 在 Hadoop 叢集上執行工作。

MapReduce 程式也可透過使用 Microsoft .NET API for HDInsight 從應用程式以程式設計的方式執行。如需使用 HDInsight API 進行工作提交的詳細資訊，請參閱[在 HDInsight 提交 Hadoop 工作][hdinsight-submit-jobs]。

網路上有許多 Hadoop 相關技術 (例如 Java 型 MapReduce 程式設計和串流) 的文件可供參考，此外也有適用於 Windows PowerShell 指令碼之 Cmdlet 的相關文件。如需這些資源的詳細資訊，請參閱 [Azure HDInsight 簡介][hdinsight-introduction]的最後一節 **HDInsight 的資源**。

**這些範例的用途**

<p>這些範例旨在協助您快速了解如何部署 Hadoop 工作，並提供您可擴充的測試環境，讓您運用服務所使用的概念和指令碼程序。其中，諸如建立及匯入各種大小的資料集、循序執行和撰寫工作、以及檢查工作結果等一般作業，都有範例可加以說明。所使用的資料集可變更大小，以便您觀察不同大小的資料集對工作效能造成的影響。</p>


**必要條件**：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **HDInsight 叢集**。如需各種建立此類叢集方式的相關指示，請參閱[佈建 HDInsight 叢集](hdinsight-provision-clusters.md)。
- **具有 Azure PowerShell 的工作站**。請參閱[安裝和使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。



## 範例 ##

HDInsight 附有下列範例：

- [**Pi Estimator Hadoop 範例**][hdinsight-sample-pi-estimator]：說明如何使用 HDInsight 執行 MapReduce 程式，而其使用統計方法 (擬蒙特卡羅法) 測量 Pi 值。
- [**在 Hadoop 叢集上執行 MapReduce 字數範例**][hdinsight-sample-wordcount]：說明如何使用 HDInsight 叢集執行 MapReduce 程式，而其計算文字檔中的文字出現次數。
- [**10-GB Graysort Hadoop 範例**][hdinsight-sample-10gb-graysort]：說明如何使用 HDInsight 在 10 GB 的檔案上執行一般用途 GraySort。有三個工作可執行：Teragen、Terasort 和 Teravalidate，分別用來產生資料、排序資料，以及確認資料已適當排序。
- [**Hadoop 中的 C# 串流字數 MapReduce 範例**][hdinsight-sample-csharp-streaming]：說明如何使用 C# 撰寫採用 Hadoop 串流介面的 MapReduce 程式。


## 如何執行範例 ##

這些範例可使用 Azure PowerShell 來執行。提供上述各範例執行方式的指示。

##後續步驟 ##

透過本文和關於各範例的文章，您已了解如何使用 Azure PowerShell 執行 HDInsight 叢集隨附的範例。如需透過 HDInsight 使用 Pig、Hive 和 MapReduce 的教學課程，請參閱下列主題：

* [開始在 HDInsight 中搭配 Hive 使用 Hadoop 以分析行動電話使用][hdinsight-get-started]
* [搭配 HDInsight 上的 Hadoop 使用 Pig][hdinsight-use-pig]
* [搭配 HDInsight 上的 Hadoop 使用 Hive][hdinsight-use-hive]
* [在 HDInsight 中提交 Hadoop 工作][hdinsight-submit-jobs]
* [Azure HDInsight SDK 文件][hdinsight-sdk-documentation]
* [在 HDInsight 中偵錯 Hadoop：錯誤訊息][hdinsight-errors]


[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=July15_HO2-->