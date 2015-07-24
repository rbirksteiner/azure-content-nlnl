<properties
   pageTitle="在事件中樞內使用 Storm on HDInsight 來處理事件 | Azure"
   description="了解如何利用在 Visual Studio 中使用 HDInsight Tools for Visual Studio 所建立之 C# Storm 拓撲來處理事件中樞資料。"
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/29/2015"
   ms.author="larryfr"/>

# 利用 Storm on HDInsight 處理 Azure 事件中樞的事件 (C#)

Azure 事件中樞可讓您從網站、應用程式和裝置處理巨量資料。事件中樞 Spout 可讓您輕鬆地使用 Apache Storm on HDInsight 來即時分析資料。您也可以使用事件中樞 Bolt 將資料從 Storm 寫入事件中樞。

在本教學課程中，您將了解如何使用 HDInsight Tools for Visual Studio 及事件中樞 Spout 和 Bolt 來建立兩個混合式 C#/Java 拓撲：

* **EventHubWriter**：隨機產生資料並將資料寫入事件中樞

* **EventHubReader**：從事件中樞讀取資料，並將資料儲存在 Azure 資料表儲存體

## 必要條件

* <a href="../hdinsight-storm-getting-started/" target="_blank">Apache Storm on HDInsight 叢集</a>

* <a href="../service-bus-event-hubs-csharp-ephcs-getstarted/" target="_blank">Azure 事件中樞</a>

* <a href="http://azure.microsoft.com/downloads/" target="_blank">Azure .NET SDK</a>

* <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">HDInsight Tools for Visual Studio</a> (2015/2/17 版本或更新版本)

## 已完成的專案

您可以從 GitHub 下載本教學課程中所建立之專案的完整版本：[eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid)。不過，您仍需要遵循本教學課程中的步驟，提供組態設定。

> [AZURE.NOTE]使用已完成的專案時，您必須使用 [NuGet 封裝管理員] 來還原此方案所需的封裝。

## 事件中樞 Spout 和 Bolt

事件中樞 Spout 和 Bolt 是可讓您輕鬆地從 Apache Storm 使用事件中樞的 Java 元件。雖然這些元件是採用 Java 撰寫而成的，但 HDInsight Tools for Visual Studio 可讓您建立混合 C# 和 Java 元件的混合式拓撲。

Spout 和 Bolt 會以名為 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 的單一 Java 封存 (.jar) 檔案形式散發。

### 下載 .jar 檔

最新版的 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 檔案包含在 **lib** 資料夾下的 <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight Storm 範例</a>專案。若要下載檔案，請使用下列其中一種方法。

> [AZURE.NOTE]已提交 Spout 和 Bolt，以將其納入 Apache Storm 專案中。如需詳細資訊，請參閱 GitHub 中的 <a href="https://github.com/apache/storm/pull/336/files">STORM-583：Storm 事件中樞的初始簽入</a>

* **下載 ZIP 檔案**：從 <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight Storm 範例</a>網站選取右窗格中的 [下載 ZIP] 來下載包含專案的 .zip 檔案。

	![下載 zip 按鈕](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	下載檔案後，您可以解壓縮此封存，則檔案便會出現在 **lib** 目錄中。

* **複製專案**：如果您已安裝 <a href="http://git-scm.com/" target="_blank">Git</a>，請使用下列命令在本機複製儲存機制，然後在 **lib** 目錄中尋找檔案。

		git clone https://github.com/hdinsight/hdinsight-storm-examples

## 設定事件中心

事件中樞是此範例的資料來源。請使用下列步驟建立新的事件中心。

1. 從 [Azure 入口網站](https://manage.windowsazure.com)選取 [新增] > [服務匯流排] > [事件中樞] > [自訂建立]。

2. 在 [加入新的事件中樞] 畫面中輸入 [事件中樞名稱]、選取要建立中樞的 [區域]，然後建立新的命名空間或選取現有的命名空間。按一下 [箭頭] 以繼續。

	![精靈頁面 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE]您應該選取與 Storm on HDInsight 伺服器相同的 [位置]，以便降低延遲和成本。

2. 在 [設定事件中樞] 畫面中，輸入 [資料分割計數] 及 [訊息保留] 值。在此範例中，資料分割計數使用 10，訊息保留使用 1。請記下資料分割計數，因為您稍後會用到這個值。

	![精靈頁面 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. 建立事件中樞之後，依序選取命名空間、[事件中樞]，然後選取您先前建立的事件中樞。

4. 選取 [設定]，然後使用下列資訊建立兩個新的存取原則。

	<table>
<tr><th>名稱</th><th>權限</th></tr>
<tr><td>寫入器</td><td>傳送</td></tr>
<tr><td>讀取者</td><td>接聽</td></tr>
</table>建立權限之後，在頁面底部選取**儲存**圖示。這樣會建立共用存取原則，可用來傳送 (寫入器) 和接聽 (讀取器) 此事件中樞的訊息。

	![原則](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. 儲存原則之後，請使用頁面底部的 [共用存取金鑰產生器] 來擷取 [寫入器] 和 [讀取器] 原則的金鑰。請儲存這些金鑰，因為稍後會用到。

## 設定資料表儲存體

因為您可以透過**伺服器總管**輕鬆地從 Visual Studio 內部檢視資料表儲存體，所以會使用資料表儲存體來保存從事件中樞讀取的值。請使用下列步驟建立新的資料表儲存體：

1. 從 [Azure 入口網站](https://manage.windowsazure.com)依序選取 [新增] > [資料服務] > [儲存體] > [快速建立]。

	![快速建立儲存體](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. 輸入儲存體帳戶的 [名稱]、選取 [位置]，然後按一下 [核取記號] 以建立儲存體帳戶。

	> [AZURE.NOTE]您應該選取與事件中樞和 Storm on HDInsight 伺服器相同的 [位置]，以便降低延遲和成本。

3. 佈建新的儲存體帳戶之後，請選取此帳戶，然後使用頁面底部的 [管理存取金鑰] 連結來擷取 [儲存體帳戶名稱] 和 [主要存取金鑰]。請儲存此資訊，因為稍後會用到。

	![access keys](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

## 建立 EventHubWriter

在本節中，您將建立使用事件中樞 Bolt 將資料寫入事件中樞的拓樸。

1. 如果您尚未安裝最新版本的 HDInsight Tools for Visual Studio，請參閱<a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">開始使用 HDInsight Tools for Visual Studio</a>。

2. 開啟 Visual Studio，並依序選取 [檔案] > [新增] 和 [專案]。

3. 從 [新增專案] 畫面，展開 [已安裝] > [範本]，然後選取 [HDInsight]。從範本清單中，選取 [Storm 應用程式]。在畫面底部輸入 **EventHubWriter** 做為應用程式名稱。

	![image](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. 建立專案之後，您應該會有下列檔案：

	* **Program.cs**：這會定義您專案的拓撲。請注意，預設會建立含有一個 Spout 和一個 Bolt 的預設拓撲。

	* **Spout.cs**：Spout 範例。

	* **Bolt.cs**：Bolt 範例。因為您將會使用事件中樞 Bolt 寫入事件中樞，所以將會刪除此項目。

### 組態

1. 在**方案總管**中，以滑鼠右鍵按一下 **EventHubWriter**，然後選取 [屬性]。

2. 在專案屬性中，依序選取 [設定] 和 [這個專案未包含預設的設定檔]**。請按這裡建立設定檔。**

3. 輸入下列設定。使用您稍早在 [值] 資料行中所建立的事件中樞資訊。

	<table>
<tr><th style="text-align:left">名稱</th><th style="text-align:left">類型</th><th style="text-align:left">Scope</th></tr>
<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">字串</td><td style="text-align:left">應用程式</td></tr>
<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">字串</td><td style="text-align:left">應用程式</td></tr>
<tr><td style="text-align:left">EventHubNamespace</td><td style="text-align:left">字串</td><td style="text-align:left">應用程式</td></tr>
<tr><td style="text-align:left">EventHubName</td><td style="text-align:left">字串</td><td style="text-align:left">應用程式</td></tr>
<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">應用程式</td></tr>
</table>

4. 儲存並關閉 [屬性] 頁面。

### 定義拓撲

1. 在**方案總管**中，以滑鼠右鍵按一下 **Bolt.cs**，然後選取 [刪除]。您正在使用 Java 事件中樞 Bolt，因此不需要此檔案。

2. 開啟 **Program.cs** 檔案，並在 `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");` 行後面緊接著加入下列程式碼。

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	第一行會從稍早定義的屬性中讀取資料分割計數。第二行會定義一個還原序列化程式，可用來將 Spout 所產生的 JSON 資料還原序列化成 `java.lang.String`，以便 Java 元件可以使用該資料。

4. 尋找下列程式碼：

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	取代為下列內容：

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	這會建立 Spout，並使用事件中樞資料分割計數做為此元件的平行處理原則提示。這應該會為每個資料分割建立 Spout 的執行個體。

	這也會讓先前所建立的還原序列化程式與此元件的輸出資料流建立關聯。這樣可允許下游 EventHubSpout 元件使用 C# Spout 所產生的資料。

5. 在先前的程式碼後面緊接著加入下列程式碼：

		JavaComponentConstructor constructor =
            JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.bolt.EventHubBolt. (com.microsoft.eventhubs.bolt.EventHubBoltConfig. " +
            @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
            Properties.Settings.Default.EventHubPolicyName,
            Properties.Settings.Default.EventHubPolicyKey,
            Properties.Settings.Default.EventHubNamespace,
            "servicebus.windows.net", //suffix for servicebus fqdn
            Properties.Settings.Default.EventHubName,
			"true"));

	這會為 Java Bolt 建立新的建構函式，在執行階段時可用來設定 Bolt 的新執行個體。在此案例中，您會使用稍早加入的事件中樞組態資訊，透過 <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> 來設定 Spout。更具體來說，HDInsight 會在執行階段使用此程式碼執行下列動作：

	* 使用您所提供的事件中樞資訊，建立 **com.microsoft.eventhubs.bolt.EventHubBoltConfig** 的新執行個體。
	* 建立 **com.microsoft.eventhubs.bolt.EventHubBolt** 的新執行個體，並傳入 **EventHubBoltConfig** 執行個體。

6. 尋找下列程式碼：

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	取代為下列內容：

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	這會指示拓樸使用先前步驟的 **JavaComponentConstructor** 做為 Bolt。在此拓撲中，可將此元件稱為 "EventHubBolt" 的易記名稱。 將平行處理原則提示設定為事件中樞的資料分割數目，並訂閱 Spout ("Spout") 所產生的資料。

此時，您已完成使用 **Program.cs**。拓撲已定義完成，但現在您必須修改 **Spout.cs**，以便所產生的資料格式會是事件中樞 Bolt 可以使用的格式。

> [AZURE.NOTE]此拓撲將預設為建立一個背景工作處理序，這對於示範目的已足夠。如果您要針對生產叢集採用此做法，您應可加入下列內容來變更背景工作數目：

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### 修改 Spout

事件中樞 Bolt 預期會收到路由傳送至事件中樞的單一字串值。在下列範例中，您將修改預設的 **Spout.cs** 檔案以產生 JSON 字串。

1. 在**方案總管**中，以滑鼠右鍵按一下 **EventHubWriter** 專案，然後選取 [管理 NuGet 封裝]。搜尋 **Json.NET** 封裝，然後將它加入方案。您便可以輕鬆地建立透過 Bolt 傳送到事件中樞的 JSON 資料。

1. 開啟 **Spout.cs**，並在檔案頂端加入下列內容：

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	讓您可以更輕鬆地使用 JSON 資料。

3. 尋找下列程式碼：

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	取代為下列內容：

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	這會將 Spout 建立的資料定義變更為使用 **string** 資料，以及稍早在拓撲宣告的 **CustomizedInteropJSONSerializer** (在 program.cs 中)。

2. 使用下列內容取代 **NextTuple** 方法：

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	這將會隨機產生裝置識別碼和一個值，然後透過 Json.NET 發出使用這些值的 JSON 物件。

3. 儲存 **Spout.cs** 檔案。

此時，您會有一個基本拓撲，其將使用事件中樞 Bolt 來產生隨機資料並將資料儲存到事件中樞。接下來，您將建立讀取器。

## 建立 EventHubReader

在本節中，您將使用事件中樞 Spout 來建立從事件中樞讀取資料的拓樸。

2. 開啟 Visual Studio，選取 [檔案] > [新增] > [專案]。

3. 從 [新增專案] 畫面，展開 [已安裝] > [範本]，然後選取 [HDInsight]。從範本清單中，選取 [Storm 應用程式]。在畫面底部輸入 **EventHubReader** 做為應用程式名稱。

### 組態

1. 在**方案總管**中，以滑鼠右鍵按一下 [EventHubReader]，然後選取 [屬性]。

2. 在專案屬性中，依序選取 [設定] 和 [這個專案未包含預設的設定檔]**。請按這裡建立設定檔。**

3. 輸入下列設定。使用您稍早在 [值] 資料行中所建立的事件中樞和儲存體帳戶資訊。

	<table>
<tr><th style="text-align:left">名稱</th><th style="text-align:left">類型</th><th style="text-align:left">Scope</th></tr>
<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">字串</th><th style="text-align:left">應用程式</th></tr>
<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">字串</th><th style="text-align:left">應用程式</th></tr>
<tr><th style="text-align:left">EventHubNamespace</th><th style="text-align:left">字串</th><th style="text-align:left">應用程式</th></tr>
<tr><th style="text-align:left">EventHubName</th><th style="text-align:left">字串</th><th style="text-align:left">應用程式</th></tr>
<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">應用程式</th></tr>
<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(連接字串)</th><th style="text-align:left">應用程式</th></tr>
<tr><th style="text-align:left">TableName</th><th style="text-align:left">字串</th><th style="text-align:left">應用程式</th></tr>
</table>在 **TableName** 中，輸入您想要用來儲存事件的資料表名稱。

    在 **StorageConnection** 中輸入 `DefaultEndpointsProtocol=https;AccountName=myAccount;AccountKey=myKey;` 值。將 **myAccount** 和 **myKey** 取代為稍早取得的儲存體帳戶名稱和金鑰。

	拓撲就會使用這些值，與事件中心和資料表儲存體進行通訊。

4. 儲存並關閉 [屬性] 頁面。

### 定義拓撲

1. 在**方案總管**中，以滑鼠右鍵按一下 [Spout.cs]，然後選取 [刪除]。您正在使用 Java 事件中樞 Spout，因此不需要此檔案。

2. 開啟 **Program.cs** 檔案，並在 `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");` 行後面緊接著加入下列程式碼：

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		EventHubSpoutConfig ehConfig = new EventHubSpoutConfig(
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount);

	會讀取並指派資料分割計數給本機變數。它將使用多次。

	`EventHubSpoutConfig` 定義事件中樞 spout 的組態。在此例中是您先前加入的事件中樞組態資訊。這會在幕後使用 Java 事件中樞 spout，並將使用事件中樞資訊來建立 **com.microsoft.eventhubs.spout.EventHubSpoutConfig** 的新執行個體。

5. 尋找下列程式碼：

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	取代為下列內容：

        topologyBuilder.SetEventHubSpout(
            "EventHubSpout", 
            ehConfig, 
            partitionCount); 

	這會指示拓撲建立新的事件中樞 spout 並使用前一個步驟的 `EventHubSpoutConfig` 做為組態。"EventHubSpout" 可設定 spout 的易記名稱，而 `partitionCount` 用於設定平行處理原則的提示。這會在幕後使用提供的組態資訊來建立 **com.microsoft.eventhubs.spout.EventHubSpout** Java 元件的新執行個體。

2. 在先前的程式碼後面緊接著加入下列內容：

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	這會建立自訂序列化程式，它將用來把 Java 元件 (例如 EventHubSpout) 產生的資訊序列化成為下游 C# 元件可以使用的 JSON 格式。

3. 尋找下列程式碼：

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	取代為下列內容：

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount,
            true).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	此程式碼會指示拓撲使用 Bolt (於 Bolt.cs 中定義)。稍早定義的自訂序列化程式用在此處，以便此 Bolt 能使用上游 Java 元件所產生的資料。在此案例中為 EventHubSpout。

    > [AZURE.IMPORTANT]SetBolt 的最後一個參數 (具有值 `true`) 能啟用此 Bolt 的 ACK 功能。這是必要的，因為 EventHubSpout 元件針對它發出的資料預期會有 ACK。如果下游元件未傳回 ACK，Spout 在處理大約 1000 則訊息之後將停止接收。

此時，您已完成使用 **Program.cs**。拓撲已定義完成，但您現在必須建立協助程式類別來將資料寫入資料表儲存體，然後您必須修改 **Bolt.cs**，讓其可了解 Spout 所產生的資料。

> [AZURE.NOTE]此拓撲將預設為建立一個背景工作處理序，這對於示範目的已足夠。如果您要針對生產叢集採用此做法，您應可加入下列內容來變更背景工作數目：

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### 建立協助程式類別

將資料寫入資料表儲存體時，您必須建立類別來說明要寫入的資料。

1. 在**方案總管**中，以滑鼠右鍵按一下 [EventHubReader] 專案，然後依序選取 [新增]、[新增類別]。將新類別命名為 **Devices.cs**。

2. 開啟 **Devices.cs** 並以下列內容取代預設的程式碼：

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.Storage.Table;

		namespace EventHubReader
		{
		    class Device : TableEntity
		    {
		        public int value { get; set; }

		        public Device() { }
		        public Device(int id)
		        {
		            this.PartitionKey = id.ToString();
		            this.RowKey = System.Guid.NewGuid().ToString();
		        }
		    }
		}

	這會在資料表儲存體中建立實體，該實體包含資料分割索引鍵 (這將會設定為讀取自事件中樞的裝置識別碼)、唯一資料列索引鍵，和一個讀取自事件中樞的值。每個實體也會有時間戳記，其會在實體插入資料表時自動建立。

### 修改 Bolt

1. 在**方案總管**中，以滑鼠右鍵按一下 [EventHubReader] 專案，然後選取 [管理 NuGet 封裝]。搜尋 **Json.Net** 封裝，然後將它加入方案。我們便可以輕鬆地處理從 Spout 收到的 JSON 資料。並加入 **WindowsAzure.Storage** 封裝，以讓我們寫入資料表儲存體。

1. 開啟 **Bolt.cs**，並在檔案頂端加入下列內容：

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	您可以更輕鬆地使用來自 Bolt 的 JSON 資料，並將資料寫入資料表儲存體。

2. 找出 `private int count;` 陳述式並取代為下列內容：

        private CloudTable table;

	您會在連接至資料表時用到此內容。

4. 尋找下列程式碼：

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	取代為下列內容：

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	這會指示 Bolt 它將會收到 **string** 值 (而不是 **int**)，且應使用稍早在拓撲宣告的 **CustomizedInteropJSONDeserialzer** (在 program.cs 檔中) 來還原序列化資料。

3. 在先前的程式碼後面緊接著加入下列程式碼：

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	這將會使用先前設定的連接字串來連接到 **events** 資料表。建立資料表 (如果不存在)。

2. 找出 **Execute** 方法並取代為下列內容：

		public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Processing events");
            string eventValue = (string)tuple.GetValue(0);
            if (eventValue != null)
            {
                JObject eventData = JObject.Parse(eventValue);

                Device device = new Device((int)eventData["deviceId"]);
                device.value = (int)eventData["deviceValue"];

                TableOperation insertOperation = TableOperation.Insert(device);

                table.Execute(insertOperation);
                this.ctx.Ack(tuple);
            }
        }

	這會使用 Json.NET 來解析來自 Spout 的 JSON 資料，然後挑選出 **deviceId** 和 **deviceValue** 欄位。接著會使用初始設定過程中的 **deviceId** 設定資料表的資料分割索引鍵，建立新的 **Device** 物件。值會設為 **deviceValue**，最後再將實體插入資料表。

    實體插入資料表之後，會呼叫 Tuple 的 `Ack()`，來通知 Spout 我們已成功處理資料。

    > [AZURE.IMPORTANT]EventHubSpout 元件預期每個來自下游元件 (例如此 Bolt) 的 Tuple 都會有 ACK。如果未收到 ACK，EventHubSpout 將假設 Tuple 的處理失敗。

此時，您便會有一個完整拓撲，此拓撲會從事件中樞讀取資料，並將資料儲存在資料表儲存體內名為 **events** 的資料表中。

## 部署拓撲

1. 在**方案總管**中，以滑鼠右鍵按一下 [EventHubReader] 專案，然後選取 [提交到 Storm on HDInsight]。

	![提交到 Storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. 在 [提交拓撲] 畫面中，選取您的 [Storm 叢集]。展開 [其他組態]，依序選取 [Java 檔案路徑]、[...]，然後選取包含您稍早下載之 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 檔的目錄。最後，按一下 [提交]。

	![提交對話方塊的影像](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. 提交拓撲之後，[Storm 拓撲檢視器] 便會隨即出現。選取左窗格的 [EventHubReader] 拓撲，以檢視拓撲的統計資料。目前，因為事件中樞尚未有事件寫入，所以應該什麼都不會發生。

	![範例儲存體檢視](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. 在**方案總管**中，以滑鼠右鍵按一下 [EventHubWriter] 專案，然後選取 [提交到 Storm on HDInsight]。

2. 在 [提交拓撲] 畫面中，選取您的 [Storm 叢集]。展開 [其他組態]，依序選取 [Java 檔案路徑]、[...]，然後選取包含您稍早下載之 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 檔的目錄。最後，按一下 [提交]。

5. 提交拓撲之後，請重新整理 [Storm 拓撲檢視器] 中的拓撲清單，以確認兩個拓撲皆在叢集上執行。

6. 兩個拓撲都在執行時，選取**伺服器總管**，然後展開 [Azure] > [儲存體]，再選取您稍早建立的儲存體帳戶。展開儲存體帳戶底下的 [資料表]。最後，按兩下 **events** 資料表以開啟資料表。您應該會看到來自 **EventHubReader** 拓撲並已儲存在資料表中的資料。

	* **EventHubWriter** 拓撲會產生事件，並將這些事件寫入事件中樞。

	* **EventHubReader** 會接著從事件中樞讀取事件，並將事件儲存到資料表儲存體的 **events** 資料表中。

## 停止拓撲

若要停止拓撲，請在 [Storm 拓撲檢視器] 中選取每個拓撲，然後選取 [終止]。

![終止拓撲的影像](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## 注意事項

### 檢查點

EventHubSpout 會定期將其狀態設定檢查點到 Zookeeper 節點，這會儲存目前從佇列讀取之訊息的位移。如此可允許元件在下列狀況中，從儲存的位移處開始接收訊息：

* 元件執行個體失敗，且已重新啟動。

* 您可藉由加入或移除節點來增大或壓縮叢集。

* 拓撲遭終止並**使用相同名稱**重新啟動。

您也可以匯出及匯入保存的檢查點到 WASB (您的 HDInsight 叢集所使用的 Azure 儲存體)。 要執行此步驟的指令碼位於 Storm on HDInsight 叢集的 **c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin**。

>[AZURE.NOTE]路徑中的版本號碼可能不同，因為叢集上安裝的 Storm 版本可能會在未來變更。

此目錄中的指令碼有：

* **stormmeta_import.cmd**：將所有 Storm 中繼資料從叢集預設儲存體容器匯入到 Zookeeper。

* **stormmeta_export.cmd**：將所有 Storm 中繼資料從 Zookeeper 匯出到叢集預設儲存體容器。

* **stormmeta_delete.cmd**：從 Zookeeper 刪除所有 Storm 中繼資料。

匯出匯入可讓您在需要刪除叢集，但又想要在讓新的叢集回到線上時從中樞的目前位移繼續處理時，保存檢查點資料。

> [AZURE.NOTE]由於資料已保存到預設儲存體容器，新的叢集**必須**使用與先前叢集相同的儲存體帳戶和容器。

## 摘要

在本文件中，您已經了解如何使用 Java 事件中樞 Spout 和 Bolt，以利用 C# 拓撲來使用 Azure 事件中樞的資料。若要深入了解如何建立 C# 拓撲，請參閱下列內容。

* [使用 Visual Studio 開發 Apache Storm on HDInsight 的 C# 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)
 

<!---HONumber=July15_HO2-->