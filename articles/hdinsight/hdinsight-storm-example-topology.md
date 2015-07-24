<properties
 pageTitle="HDInsight 上的 Apache Storm 拓撲 | Microsoft Azure"
 description="利用 Apache Storm on HDInsight 建立和測試的範例 Storm 拓撲清單 (包含基本 basic C# 和 Java 拓撲)，以及使用事件中樞。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="06/05/2015"
 ms.author="larryfr"/>

# Apache Storm on HDInsight 的範例 Storm 拓撲

以下是 Microsoft 所建立和維護的範例清單，可搭配 Apache Storm on HDInsight 使用。這些範例涵蓋各種主題，從建立基本 C# 和 Java 拓撲以使用 Azure 服務，例如事件中心、DocumentDB、Power BI、SQL Database、HBase on HDInsight 和 Azure 儲存體。一些範例也會示範如何使用非 Azure 或甚至非 Microsoft 的技術，例如 SignalR 和 Socket.IO

| 說明 | 示範 | 語言/架構 |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [開發 Apache Storm on HDInsight 的 Java 型拓撲][5797064f] | Maven | Java |
| [使用 Visual Studio 開發 Apache Storm on HDInsight 的 C# 拓撲][16fce2d1] | HDInsight Tools for Visual Studio | C#，Java |
| [在 C# Storm 拓樸中建立多個資料流][ec5a4064] | 多個資料流 | C# |
| [決定含 Apache Storm on HDInsight 的 Twitter 趨勢主題][3c86c7c8] | Trident | Java，Trident |
| [利用 Storm on HDInsight 處理 Azure 事件中心的事件][844d1d81] | 事件中心 | C# 和 Java |
| [使用 Power Bi (預覽) 來視覺化 Storm 拓撲的資料][94d15238] | Power BI | C# |
| [在 HDInsight 中使用 Storm 和 HBase 分析感應器資料][ab894747] | 事件中心、HBase、Socket.IO、Web 儀表板 | C#、Java、JavaScript、HTML |
| [使用 Storm on HDInsight 處理事件中心的車輛感應器資料][246ee964] | 事件中心、DocumentDb、Azure 儲存體 Blob (WASB) | C#，Java |
| [使用 Storm on HDInsight，從 Azure 事件中心擷取、轉換及載入 (ETL) 至 HBase][b4b68194] | 事件中心，HBase | C# |
| [使用 Storm on HDInsight 之 Azure 服務的範本 C# Storm 拓樸專案][ce0c02a2] | 事件中心、DocumentDb、SQL Database、HBase、SignalR | C#，Java |
| [使用 Storm on HDInsight 從 Azure 事件中心讀取的延展性效能評比][d6c540e3] | 訊息輸送量、事件中心、SQL Database | C#，Java |
| [在 HDInsight 上使用 Storm 和 HBase 讓事件相互關聯](hdinsight-storm-correlation-topology.md) | HBase | C# |

## 後續步驟

* [開始使用 Apache Storm on HDInsight][2b8c3488]

* [了解如何利用 Storm on HDInsight 部署和管理 Storm 拓撲][6eb0d3b8]

  [2b8c3488]: ../hdinsight-storm-getting-started.md "了解如何在 HDInsight 叢集上建立 Storm 並使用 Storm 儀表板來部署範例拓撲。"
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "了解如何使用 web 型 Storm 儀表板和 Storm UI 或 HDInsight Tools for Visual Studio 部署和管理拓撲。"
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "了解如何使用 HDInsight Tools for Visual Studio 建立 C# Storm 拓撲。"
  [5797064f]: hdinsight-storm-develop-java-topology.md "了解如何建立基本字數統計拓撲，以使用 Maven 在 Java 中建立 Storm 拓撲。"
  [94d15238]: hdinsight-storm-power-bi-topology.md "示範如何從 C# 拓撲，將資料寫入 Power BI，然後從資料建立圖表和儀表板。"
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "示範執行在 C# 中實作之字數統計的基本 Storm 拓樸。這也會示範如何建立 C# 拓撲中的多個資料流。"
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "了解如何利用 Storm on HDInsight 從 Azure 事件中心讀取和寫入資料。"
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "了解如何使用 Apache Storm on HDInsight 處理 Azure 事件中心的感應器資料，使用 D3.js 將其視覺化，並將其 (選擇性) 儲存至 HBase。"
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "了解如何使用 Trident 建立 Storm 拓撲，以根據雜湊標記來決定 Twitter 上的趨勢主題。"
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "了解如何使用 Storm 拓撲讀取 Azure 事件中心的訊息，閱讀 Azure DocumentDB 的文件做為資料參考，並將資料儲存至 Azure 儲存體。"
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "使用 Apache Storm on HDInsight 從 Azure 事件中心讀取並儲存至 SQL Database 時示範輸送量的幾個拓樸。"
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "了解如何讀取 Azure 事件中心的資料、彙總與轉換資料，然後將它儲存至 HBase on HDInsight。"
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "此專案包含 spout、bolt 和拓撲的範本，藉此和各種 Azure 服務進行互動，例如事件中心、DocumentDB 和 SQL Database。"
 

<!---HONumber=July15_HO2-->