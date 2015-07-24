<properties
	pageTitle="開始使用事件中心"
	description="遵循此教學課程，開始使用 Azure Event Hubs 以 Java 傳送事件並且在 Apache Storm 叢集中接收這些事件。"
	services="event-hubs,service-bus"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="core"
	ms.tgt_pltfrm="java"
	ms.devlang="java"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="sethm"/>

# 開始使用事件中心

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## 簡介

事件中心是可高度擴充的擷取系統，每秒可以吸收數以百萬計的事件，讓應用程式能處理和分析已連線裝置和應用程式所產生的大量資料。收集到事件中樞後，您可以使用任何即時分析提供者或儲存體叢集轉換和儲存資料。

如需詳細資訊，請參閱[事件中心概觀]。

在本教學課程中，您將學習如何使用以 Java 撰寫的主控台應用程式將訊息收集到事件中心，以及如何使用 Apache Storm 平行擷取它們。

若要完成本教學課程，您需要下列項目：

+ 為了執行 [Maven](http://maven.apache.org/) 所設定的 Java 開發環境。在本教學課程中，我們將採用 [Eclipse](https://www.eclipse.org/)。

+ 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

## 建立事件中心

1. 登入 [Azure 管理入口網站]，然後按一下畫面底部的 [新增]。

2. 依序按一下 [應用程式服務]、[服務匯流排]、[事件中心]、[快速建立]。

   	![][1]

3. 為您的事件中心輸入名稱、選取所需的區域，然後按一下 [建立新的事件中心]。

   	![][2]

4. 按一下您剛才建立的命名空間 (通常是 ***event hub name*-ns**)。

   	![][3]

5. 按一下頁面頂端的 [事件中心] 索引標籤，然後按一下您剛建立的事件中心。

   	![][4]

6. 按一下頁面頂端的 [設定] 索引標籤，新增名為 **SendRule** 且具有 *Send* 權限的規則，並新增另一個名為 **ReceiveRule** 且具有 *Listen* 權限的規則，然後按一下 [儲存]。

   	![][5]

7. 在相同頁面上，記下為 **SendRule** 和 **ReceiveRule** 產生的金鑰。

   	![][6c]

現已建立事件中心，並具有傳送與接收事件所需的連接字串。

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.	從 Eclipse 執行 **LogTopology** 類別，然後等它啟動所有資料分割的接收器。

2.	執行 **Sender** 專案，按下主控台視窗中的 **Enter** 鍵，並查看出現在接收器視窗中的事件。

   	![][22]

> [AZURE.NOTE]本教學課程中只使用本機模式的 Storm 進行開發。如需 Storm 部署和模式的詳細資訊，請參閱 [HDInsight Storm 概觀]和 [Apache Storm] 官方文件。

## 後續步驟

下列資源可供開發整合事件中心和 Storm 的應用程式。

- [使用 Storm 和 HDInsight 分析感應器資料]是一個完整案例教學課程，該課程使用事件中心、Storm 和 HBase 擷取 Hadoop 叢集中的感應器資料。
- [在 Storm 和 HDInsight 上使用 SCP.NET 和 C# 開發串流資料處理應用程式]教學課程說明如何使用 C# 撰寫 Storm 管線。

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub6c.png

[22]: ./media/service-bus-event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Azure 管理入口網站]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[事件中心概觀]: http://msdn.microsoft.com/library/azure/dn836025.aspx

[Apache Storm]: https://storm.incubator.apache.org
[HDInsight Storm 概觀]: http://azure.microsoft.com/documentation/articles/hdinsight-storm-overview/
[使用 Storm 和 HDInsight 分析感應器資料]: http://azure.microsoft.com/documentation/articles/hdinsight-storm-sensor-data-analysis/
[在 Storm 和 HDInsight 上使用 SCP.NET 和 C# 開發串流資料處理應用程式]: http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/
 

<!---HONumber=July15_HO1-->