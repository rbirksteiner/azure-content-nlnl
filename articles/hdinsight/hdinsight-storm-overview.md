<properties
	pageTitle="Apache Storm on HDInsight 簡介 | Microsoft Azure"
	description="取得 Apache Storm 的簡介，並了解如何使用 Storm on HDInsight 在雲端中建置即時資料分析解決方案。"
	keywords="apache storm,real time analytics"
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
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#Apache Storm on HDInsight 簡介：Hadoop 的即時分析

Apache Storm on HDInsight 可讓您使用 [Apache Hadoop](http://hadoop.apache.org)，在 Azure 環境中建立分散式的即時分析解決方案。

##什麼是 Apache Storm？

Apache Storm 是一個容錯的分散式開放原始碼計算系統，可讓您使用 Hadoop 即時處理資料。Storm 解決方案也能夠重播最初未成功處理的資料，保證一定會處理資料。

##為何使用 Storm on HDInsight？

Apache Storm on HDInsight 是已和 Azure 環境整合的受管理叢集。它具有以下主要優勢：

* 可做為受管理服務執行，且具有 99.9% 運作時間的 SLA

* 使用您選擇的語言：提供以 **Java**、**C#** 和 **Python** 撰寫的 Storm 元件支援

	* 支援混合使用程式設計語言：使用 Java 讀取資料，然後使用 C# 處理資料

	* 使用 **Trident** Java 介面建立 Storm 拓撲，藉此支援「只一次性」處理訊息、「交易式」資料存放區持續性和一組常用的串流分析作業

* 包含內建的調升及調降規模功能：可以調整 HDInsight 叢集的規模，且不影響執行 Storm 拓撲

* 與其他 Azure 服務整合，包括事件中樞 (Event Hub)、Azure 虛擬網路、SQL 資料庫、Blob 儲存體和 DocumentDB

	* 使用 Azure 虛擬網路結合多個 HDInsight 叢集的功能：建立使用 HDInsight、HBase 或 Hadoop 叢集的分析管線

如需使用 Apache Storm 做為即時分析解決方案的公司清單，請參閱[使用 Apache Storm 的公司](https://storm.apache.org/documentation/Powered-By.html) (英文)。

若要開始使用 Storm，請參閱[開始使用 Storm on HDInsight][gettingstarted]。

###輕鬆佈建

只要花數分鐘即可佈建新的 Storm on HDInsight 叢集。指定叢集名稱、大小、管理員帳戶和儲存體帳戶。Azure 即會建立叢集，包括範例拓撲和 Web 管理儀表板。

以下是使用 Azure 入口網站佈建 Storm on HDInsight 叢集的範例。您也可以使用 [Azure PowerShell](../install-configure-powershell.md) 佈建 Storm 叢集。

![入口網站中的快速建立叢集表單範例](./media/hdinsight-storm-overview/quick-create.png)

提交要求後，15 分鐘內您就會有一個新的執行中 Storm 叢集，並準備好讓您進行第一次即時分析管線。

###容易使用

如果使用 Visual Studio，則 HDInsight Tools for Visual Studio 可讓您建立 C# 和混合式 C#/Java 拓撲，然後將這些拓撲提交至 Storm on HDInsight 叢集。

![建立 Storm 專案](./media/hdinsight-storm-overview/createproject.png)

HDInsight Tools for Visual Studio 也提供了介面，讓您可以監視和管理叢集上的 Storm 拓撲。

![Storm 管理](./media/hdinsight-storm-overview/stormview.png)

如需使用 HDInsight Tools 建立 Storm 應用程式的範例，請參閱[使用 HDInsight Tools for Visual Studio 開發 C# Storm 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

如需關於 HDInsight Tools for Visual Studio 的詳細資訊，請參閱[開始使用 HDInsight Tools for Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)。

每個 Storm on HDInsight 叢集也會提供 網頁式 Storm 儀表板，讓您提交、監視和管理在叢集上執行的 Storm 拓撲。

![Storm 儀表板](./media/hdinsight-storm-overview/dashboard.png)

如需關於使用 Storm 儀表板的詳細資訊，請參閱[部署和管理 HDInsight 上的 Apache Storm 拓撲](hdinsight-storm-deploy-monitor-topology.md)。

Storm on HDInsight 也提供透過 **Event Hub Spout** 與 Azure 事件中樞輕鬆整合的功能。您可以在每個 Storm 叢集的 **%STORM_HOME%\\examples\\eventhubspout\\eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 位置中使用這項功能。如需在 Storm 拓撲中使用此 Spout 的範例，請參閱[開始使用事件中樞](service-bus-event-hubs-c-storm-getstarted.MD)和[使用 Storm 和 HBase 分析感應器資料](hdinsight-storm-sensor-data-analysis.MD)。

###可靠性

即使資料分析分散在數以百計的節點，Apache Storm 也能保證每個傳入訊息一律都會經過完整處理。

**Nimbus 節點**提供與 Hadoop JobTracker 類似的功能，並會透過 **Zookeeper** 指派工作給叢集中的其他節點。Zookeeper 節點可為叢集進行協調，並促進 Nimbus 與背景工作節點上**監督員**處理序之間的通訊。如果其中一個處理節點停止，Nimbus 節點會收到通知，然後將工作和相關資料指派給其他節點。

Apache Storm 的預設組態只有一個 Nimbus 節點。Storm on HDInsight 會執行兩個 Nimbus 節點。如果主要節點失敗，HDInsight 叢集會切換至次要節點，直到主要節點復原為止。

![Nimbus、Zookeeper 和監督員的圖表](./media/hdinsight-storm-overview/nimbus.png)

###調整

雖然您可以在建立時指定叢集的節點數，但您可以擴大或縮小叢集以配合工作負載。即使在處理資料期間，所有 HDInsight 叢集均可讓您變更叢集中的節點數。

![入口網站中的資料節點執行個體計數](./media/hdinsight-storm-overview/scale.png)

###支援

Storm on HDInsight 隨附完整的企業級全年無休支援。Storm on HDInsight 也有 99.9% 的 SLA。這表示我們保證叢集至少 99.9% 的時間具有外部連線能力。

##即時分析的常見使用案例

以下是一些 Apache storm on HDInsight 可能的常見使用案例。如需真實案例的相關資訊，請參閱[公司如何使用 Storm](https://storm.incubator.apache.org/documentation/Powered-By.html) (英文)。

* 物聯網 (IoT)
* 詐騙偵測
* 社交分析
* 擷取、轉換、載入 (ETL)
* 網路監視
* 搜尋
* Mobile Engagement

##如何處理 HDInsight Storm 中的資料？

Apache Storm 會執行**拓撲**，而不是您在 HDInsight 或 Hadoop 中可能熟悉的 MapReduce 工作。Storm on HDInsight 叢集包含兩種節點：執行 **Nimbus** 的前端節點和執行**監督員**的背景工作節點。

* **Nimbus**：類似 Hadoop 中的 JobTracker，負責將程式碼散發到整個叢集、指派工作給虛擬機器及監控失敗情況。HDInsight 提供兩個 Nimbus 節點，所以 Storm on HDInsight 不會有單一失敗點。

* **監督員**：每一個背景工作節點的監督員負責啟動和停止節點上的**背景工作處理序**。

* **背景工作處理序**：執行一部分的**拓撲**。執行中的拓撲分散在整個叢集的許多背景工作處理序上。

* **拓撲**：定義一份可處理資料**串流**的計算圖。與 MapReduce 工作不同之處在於，拓撲會一直執行到您將它們停止為止。

* **串流**：未繫結的 **Tuple** 集合。串流由 **Spout** 和 **Bolt** 產生，由 **Bolt** 取用。

* **Tuple**：動態類型值的具名清單。

* **Spout**：取用資料來源的資料，並發出一或多個**串流**。

	> [AZURE.NOTE]在許多情況下，會自 Kafka、Azure 服務匯流排佇列或事件中樞讀取資料。佇列可確保運作中斷時資料仍持續存在。

* **Bolt**：取用**串流**、在 **Tuple** 上執行處理，然後可能發出**串流**。Bolt 也負責將資料寫入外部儲存體，例如佇列、HDInsight、HBase、Blob 或其他資料存放區。

* **Apache Thrift**：一套軟體架構，支援可擴充的跨語言服務開發。它可讓您建置 C++、Java、Python、PHP、Ruby、Erlang、Perl、Haskell、C#、Cocoa、JavaScript、Node.js、Smalltalk 和其他語言之間通用的服務。

	* **Nimbus** 是一種 Thrift 服務，而**拓撲**是 Thrift 定義，因此可利用各種程式設計語言來開發拓撲。

如需關於 Storm 元件的詳細資訊，請參閱 apache.org 上的 [Storm 教學課程][apachetutorial] (英文)。


##我可以使用哪些程式設計語言？

Storm on HDInsight 叢集支援 C#、Java 和 Python。

### C&#35;

HDInsight Tools for Visual Studio 可讓 .NET 開發人員以 C# 設計和實作拓撲。您也可以建立使用 Java 和 C# 元件的混合式拓撲。

如需詳細資訊，請參閱[使用 Visual Studio 開發 Apache Storm on HDInsight 的 C# 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

###Java

您遇到的大多數 Java 範例都是純 Java 或 Trident。Trident 是一種高階抽象概念，可讓工作變得更簡單，例如聯結、彙總、分組和篩選。不過，Trident 是以 Tuple 批次為對象，而原始 Java 解決方案是以一次一個 Tuple 的方式來處理串流。

如需關於 Trident 的詳細資訊，請參閱 apache.org 上的 [Trident 教學課程](https://storm.incubator.apache.org/documentation/Trident-tutorial.html) (英文)。

如需原始 Java 與 Trident 拓撲的範例，請移至 HDInsight Storm 叢集的 **%storm_home%\\contrib\\storm-starter** 目錄。

##有哪些常見的開發模式？

###保證處理訊息

Storm 可以提供不同程度的訊息處理保證。例如，基本的 Storm 應用程式可以保證至少處理一次，而 Trident 可以保證只處理一次。

如需詳細資訊，請參閱 apache.org 上的[保證處理資料](https://storm.apache.org/about/guarantees-data-processing.html) (英文)。

###IBasicBolt

讀取輸入 Tuple、發出零個以上的 Tuple，然後在執行方法結束時立即認可輸入 Tuple，由於這種模式實在相當常見，所以 Storm 提供 [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) 介面將此模式自動化。

###聯結

聯結兩個資料串流的方式隨著應用程式而有所不同。例如，您可以將多個串流中的每個 Tuple 聯結成一個新的串流，或只聯結特定時間範圍的幾批 Tuple。無論何者，都可利用 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) 來完成聯結，這是一種定義 Tuple 如何路由傳送至 bolt 的方法。

在下列 Java 範例中，會使用 fieldsGrouping 將源自元件 "1"、"2" 和 "3" 的 Tuple 路由傳送至 **MyJoiner** bolt。

	builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

###批次處理

批次處理有多種方式。在基本的 Storm Java 拓撲中，可以先使用簡單的計數器來批次處理 X 個 Tuple，再將它們發出，或使用稱為「計時 Tuple」的內部計時機制，每 X 秒發出一批。

如需使用計時 Tuple 的範例，請參閱[使用 Storm 和 HBase on HDInsight 分析感應器資料](hdinsight-storm-sensor-data-analysis.md)。

如果您使用 Trident，則是以 Tuple 批次處理為基礎。

###快取

通常會使用記憶體內部快取當做加速處理的機制，因為此方式可以將常用的資產保留在記憶體內。由於拓撲分散於多個節點上，以及每個節點內的多個程序上，因此您應該考慮使用 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29)，以確保含有用於快取查閱欄位的 Tuple，一定會路由傳送至相同的程序。這樣可避免快取項目在程序之間重複。

###串流前 N 個

當拓撲取決於計算 "top N" 值時，例如 Twitter 的前 5 個趨勢，您應該平行地計算前 N 值，然後將這些計算的輸出合併成一個全域值。這可利用 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) 來進行，依欄位路由傳送至平行 bolt (此時會依欄位值分割資料)，然後路由傳送至 bolt 來整體決定前 N 值。

如需相關範例，請參閱 [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) 範例。

##後續步驟

深入了解使用 Apache Storm in HDInsight 的即時分析解決方案：

* [開始使用 Storm on HDInsight][gettingstarted]

* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[gettingstarted]: ../hdinsight-storm-getting-started.md

<!---HONumber=July15_HO2-->