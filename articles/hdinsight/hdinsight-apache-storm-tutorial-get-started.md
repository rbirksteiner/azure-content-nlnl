<properties
	pageTitle="Apache Storm 教學課程：開始使用 Storm | Microsoft Azure"
	description="在 HDInsight 上使用 Apache Storm 和 Storm Starter 範例，開始分析巨量資料。了解如何使用 Storm 即時處理資料。"
	keywords="apache storm,apache storm tutorial,big data analytics,storm starter"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>


# Apache Storm 教學課程：在 HDInsight 上使用 Storm Starter 範例開始分析巨量資料

Apache Storm 是一個可處理資料串流的分散式、容錯、即時的運算系統。在 Storm on Azure HDInsight 中，您可以建立雲端式 Storm 叢集，以執行即時的巨量資料分析。

## 開始之前

您必須具備下列先決條件，才能順利完成本 Apache Storm 教學課程：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

## 建立 Azure 儲存體帳戶

Storm on HDInsight 使用 Azure Blob 儲存體來儲存提交給叢集的記錄檔和拓撲。請使用以下步驟建立和您的叢集搭配使用的 Azure 儲存體帳戶：

1. 登入 [Azure 入口網站](http://manage.windowsazure.com/)。

2. 按一下左下角的 [新增]，並指向 [資料服務]，接著指向 [儲存體]，然後按一下 [快速建立]。

	![Azure 入口網站，您可以在此入口網站中使用「快速建立」設定新的儲存體帳戶。](./media/hdinsight-apache-storm-tutorial-get-started/HDI.StorageAccount.QuickCreate.png)

3. 輸入 [URL]、[位置] 和 [複寫] 資訊，然後按一下 [建立儲存體帳戶]。為 HDInsight 建立儲存體時，請勿選取同質群組。您會在儲存體清單中看見新的儲存體帳戶。

	>[AZURE.NOTE]佈建 HDInsight 叢集的快速建立選項 (如在本教學課程中所使用的選項) 在佈建叢集時並不會詢問位置。相反地，它會依預設將叢集並存於與儲存體帳戶相同的資料中心內。因此請務必在叢集支援的位置中建立儲存體帳戶，這些位置包括：**東亞**、**東南亞**、**北歐****西歐**、**美國東部**、**美國西部**、**美國中北部**、**美國中南部**。

4. 等候新儲存體帳戶的 [**狀態**] 變更為 [**線上**]。

如需建立儲存體帳戶的詳細資訊，請參閱[如何建立儲存體帳戶](../storage/storage-create-storage-account.md)。

##在 Azure 入口網站上佈建 Storm 叢集

佈建 HDInsight 叢集時，可以佈建包含 Apache Storm 及相關應用程式的 Azure 運算資源。您也可以使用 Azure 入口網站、HDInsight 的 Azure PowerShell Cmdlet 或 HDInsight .NET SDK 來建立其他版本的 Hadoop 叢集。如需相關指示，請參閱[使用自訂選項佈建 HDInsight 叢集][hdinsight-provision]。如需不同 HDInsight 版本及其服務等級協定 (SLA) 的相關資訊，請參閱 [HDInsight 元件版本設定](hdinsight-component-versioning.md)頁面。

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

1. 登入 [Azure 入口網站][azureportal]。

2. 按一下左邊的 [HDInsight]，然後按一下頁面左下角的 [+新增]。

3. 按一下第二欄的 HDInsight 圖示，然後選取 [STORM]。

	![quick create](./media/hdinsight-apache-storm-tutorial-get-started/quickcreate.png)

4. 輸入唯一的叢集名稱，然後為輸入管理帳戶的唯一密碼。在 [儲存體帳戶] 中，選取先前建立的儲存體帳戶。

	在 [叢集大小] 中，選取 [1 個資料節點] 的大小以用於此叢集。這麼做可以將與叢集相關的成本減至最低。若要用於生產環境，可以建立更大的叢集。

	> [AZURE.NOTE]叢集的管理員帳戶已命名為 **admin**。您輸入的密碼就是此帳戶的密碼。您將需要此資訊來對叢集執行動作，例如提交或管理 Storm 拓撲。

5. 最後，選取 [建立 HDInsight 叢集] 旁邊的核取記號以建立叢集。

> [AZURE.NOTE]佈建叢集需要花一點時間 (通常約 15 分鐘以內) 來建立叢集、設定軟體和安裝範例資料與拓撲。

##在 HDInsight 上執行 Storm Starter 範例

本 Apache Storm 教學課程會向您介紹在 GitHub 上使用 Storm Starter 範例的巨量資料分析。

每個 Storm on HDInsight 叢集都會隨附一個 Storm 儀表板，讓您將 Storm 拓撲上傳到叢集並在其中執行 Storm 拓撲。每個叢集也會隨附可直接從 Storm 儀表板執行的範例拓撲。

###<a id="connect"></a>連接至儀表板

儀表板位於 **https://&lt;clustername>.azurehdinsight.net//**，其中 **clustername** 是叢集的名稱。您也可以在 Azure 入口網站頁面底部，找到適用於叢集的儀表板連結。

![含 Storm 儀表板連結的 Azure 入口網站](./media/hdinsight-apache-storm-tutorial-get-started/dashboard-link.png)

> [AZURE.NOTE]連接至儀表板時，系統會提示您輸入使用者名稱和密碼。這是您建立叢集時使用的管理員名稱 (**admin**) 和密碼。

載入 Storm 儀表板後，您會看到 [提交拓撲] (Submit Topology) 表單。

![利用 Storm 儀表板提交 Storm Starter 拓撲。](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

[提交拓撲] (Submit Topology) 表單可用於上傳含有 Storm 拓撲的 .jar 檔案並執行。它也包含數個與叢集一併提供的基本範例。

###<a id="run">從 GitHub 的 Storm Starter 專案執行 word-count 範例

與叢集一併提供的範例包含 word-counting 拓撲的數個變體。這些範例包含會隨機產生句子的 **spout**，以及會將每個句子拆成個別單字，然後計算每個單字的出現次數的 **bolts**。這些範例均來自 [Storm Starter 範例](https://github.com/apache/storm/tree/master/examples/storm-starter) (Apache Storm 的一部分)。

請使用以下步驟執行 Storm Starter 範例：

1. 選取 [Jar 檔案] 下拉式清單中的 [StormStarter - WordCount]。此時應該會使用此範例的參數填入 [類別名稱] 和 [其他參數] 欄位。

	![在 Storm 儀表板上選取的 Storm Starter WordCount。](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

	* **類別名稱**：提交拓撲的 .jar 檔案中的類別。
	* **其他參數**：拓撲需要的所有參數。在此範例中，此欄位用於讓提交的拓撲有易記名稱。

2. 按一下 [提交] 按鈕。稍後 [結果] 欄位會顯示用於提交工作的命令，以及命令的結果。[錯誤] 欄位會顯示提交拓撲時發生的所有錯誤。

	![Storm Starter WordCount 的提交按鈕和結果。](./media/hdinsight-apache-storm-tutorial-get-started/submit-results.png)

	> [AZURE.NOTE]顯示結果並不表示拓撲已完成，**Storm 拓撲一旦啟動，就會持續執行，直到您將拓撲停止為止。** Word-count 拓撲會產生隨機的句子，並持續計算每個單字出現的次數，直到您將拓撲停止為止。

###<a id="monitor"></a>監視拓撲

您可以使用 Storm UI 監視拓撲。

1. 選取 Storm 儀表板頂端的 [Storm UI]。這會顯示叢集和所有執行中拓撲的摘要資訊。

	![顯示 Storm Starter WordCount 拓樸摘要的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started/stormui.png)

	在上方的頁面中，您會看到拓撲作用中的時間，以及使用的背景工作數、執行程式數和工作數。

	> [AZURE.NOTE][名稱] 欄含有稍早透過 [其他參數] 欄位提供的易記名稱。

4. 在 [拓撲摘要] 下，選取 [名稱] 欄中的 [wordcount] 項目。這麼做會顯示拓撲的詳細資訊。

	![包含 Storm Starter WordCount 拓樸資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started/topology-summary.png)

	此頁面提供以下資訊：

	* **拓撲統計資料 (Topology stats)**：拓撲效能的基本資訊，已整理為時間範圍。

		> [AZURE.NOTE]選取特定的時間範圍，可以變更頁面中其他區段所顯示之資訊的時間範圍。

	* **Spouts**：spout 的基本資訊，包括每個 spout 傳回的最後一個錯誤。

	* **Bolts**：bolt 的基本資訊。

	* **拓撲組態 (Topology configuration)**：拓撲組態的詳細資訊。

	此頁面也提供可對拓撲採取的動作：

	* **啟用**：繼續處理已停用的拓撲。

	* **停用**：暫停執行中拓撲。

	* **重新平衡**：調整拓撲的平行處理原則。變更叢集中的節點數目之後，您應該重新平衡執行中拓撲。這可讓拓撲調整平行處理原則，以彌補叢集中增加/減少的節點數目。如需詳細資訊，請參閱[了解 Storm 拓撲的平行處理原則](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

	* **終止 (Kill)**：在指定的逾時之後終止 Storm 拓撲。

5. 在此頁面中，選取 [Spouts] 或 [Bolts] 區段中的一個項目。如此會顯示所選元件的相關資訊。

	![包含所選元件相關資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started/component-summary.png)

	此頁面會顯示以下資訊：

	* **Spout/Bolt 統計資料 (Spout/Bolt stats)**：元件效能的基本資訊，已整理為時間範圍。

		> [AZURE.NOTE]選取特定的時間範圍，可以變更頁面中其他區段所顯示之資訊的時間範圍。

	* **輸入統計資料 (Input stats)** (僅 bolt)：提供的資訊是關於產生 bolt 所使用之資料的元件。

	* **輸出統計資料 (Output stats)**：此 bolt 發出之資料的資訊。

	* **執行程式**：此元件之執行個體的資訊。

	* **錯誤**：此元件產生的錯誤。

5. 檢視 spout 或 bolt 的詳細資料時，請在 [執行程式] 區段的 [連接埠] 欄中選取一個項目，以檢視特定元件執行個體的詳細資料。

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	您可以在此資料中看到 **seven** 一字已出現 1,493,957 次。這就是啟動拓撲後，該字所出現的次數。

###停止拓撲

返回 word-count 拓撲的 [拓撲摘要] (Topology summary) 頁面，然後選取 [拓撲動作] (Topology actions) 區段中的 [終止] (Kill) 按鈕。出現提示時，請先輸入要等候 10 秒，再停止拓撲。逾時期限過後，當您瀏覽儀表板的 [Storm UI] 區段時，便不會再顯示拓撲。

##摘要

您已在本 Apache Storm 教學課程中藉由 Storm Starter 了解如何建立 Storm on HDInsight 叢集，以及如何使用 Storm 儀表板部署、監視和管理 Storm 拓撲。

##<a id="next"></a>接續步驟

* **HDInsight Tools for Visual Studio**：類似稍早提到的 Storm 儀表板，HDInsight Tools 可讓您運用 Visual Studio 提交、監視及管理 Storm 拓撲。HDInsight Tools 也能讓您建立 C# Storm 拓撲，並提供可讓您在叢集上部署和執行的範例拓撲。

	如需詳細資訊，請參閱[開始使用 HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)。

* **範例檔案**：HDInsight Storm 叢集在 **%STORM_HOME%\\contrib** 目錄中提供數個範例。每個範例應該會包含下列項目：

	* 原始程式碼 - 例如，storm-starter-0.9.1.2.1.5.0-2057-sources.jar

	* Java 文件 - 例如，storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

	* 範例 - 例如，storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

	請使用 'jar' 命令來解壓縮原始程式碼或 Java 文件。例如，'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'。

	> [AZURE.NOTE]Java 文件是由網頁組成。解壓縮之後，請使用瀏覽器來檢視 **index.html** 檔案。

	若要存取這些範例，必須為 Storm on HDInsight 叢集啟用「遠端桌面」，然後從 **%STORM_HOME%\\contrib** 複製檔案。

* 以下文件含有可和 Storm on HDInsight 搭配使用的其他範例清單：

	* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md

<!---HONumber=July15_HO2-->