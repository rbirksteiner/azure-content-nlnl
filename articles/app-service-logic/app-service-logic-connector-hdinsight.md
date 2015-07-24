<properties 
   pageTitle="HDInsight 連接器" 
   description="如何使用 HDInsight 連接器" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Microsoft HDInsight 連接器 #

在邏輯應用程式中，連接器可以在執行流程時用來擷取、處理或發送資料。HDInsight 連接器可讓您在 Azure 上建立 Hadoop 叢集，並提交各種 Hadoop 工作，例如 Hive、Pig、MapReduce 和 Streaming MapReduce 工作。Azure HDInsight 服務在雲端部署和佈建 Apache Hadoop 叢集，提供軟體架構來管理、分析和報告巨量資料。Hadoop 核心採用 Hadoop Distributed File System (HDFS) 來提供可靠的資料儲存，並提供簡單的 MapReduce 程式設計模型，可並行處理和分析這個分散式系統中儲存的資料。您可以使用 HDInsight 連接器建立或刪除叢集、提交工作並等候它完成。

###基本動作
		
- 建立叢集
- 等候建立叢集
- 提交 Pig 工作
- 提交 Hive 工作
- 提交 MapReduce 工作
- 等候工作完成
- 刪除叢集


## 建立 HDInsight 連接器 API 應用程式的執行個體 ##

若要使用 HDInsight 連接器，您必須建立 HDInsight 連接器 API 應用程式的執行個體。以下步驟可以達到此目的：

1. 使用 Azure 入口網站左邊的 [+新增] 選項並選取 [Marketplace]，開啟 Azure Marketplace。
2. 瀏覽至 [API Apps]，搜尋「HDInsight 連接器」，然後選取它並按一下 [建立]。
3. 在第一個刀鋒視窗中提供一般詳細資訊，例如名稱、App Service 方案等等。
4. 在 [封裝設定] 中，提供 HDInsight 叢集使用者名稱和密碼，並按一下 [確定]。
5. 按一下 [建立]。


 ![][1]

## 憑證設定 (選用) ##

注意：只有當您想要在邏輯應用程式中執行管理作業 (建立及刪除叢集)，才需要這個步驟。

瀏覽至剛才建立的 HDInsight 連接器 API 應用程式，您會看到 [安全性] 元件顯示 0 - 表示未上傳任何管理憑證。

![][2]

若要將管理憑證上傳至您的 API 應用程式，您必須執行下列步驟 1.按一下 [安全性] 元件。2.在 [安全性] 刀鋒視窗中，按一下 [上傳憑證]。3.在下一個刀鋒視窗中瀏覽並選取憑證檔。4.選取憑證檔之後，按一下 [確定]。

憑證上傳成功之後，憑證的詳細資料會顯示如下。

![][3]

注意：如果您想要變更憑證，您可以上傳另一個憑證來取代現有的憑證。

## 邏輯應用程式中的使用方式 ##

HDInsight 在邏輯應用程式中只能當動作使用。讓我們以一個簡單的邏輯應用程式為例，它會建立叢集，執行 Hive 工作，然後在工作結束時刪除叢集。


- 在 [啟動邏輯] 卡中，按一下 [手動執行此邏輯]。
- 選取從資源庫建立的 HDInsight 連接器 API 應用程式，將會顯示所有可用的動作。

![][5]


- 選取 [建立叢集]，提供所有必要的叢集參數，然後按一下 ✓。

![][6]



- 該動作現在會在邏輯應用程式中顯示為已設定。將會顯示動作輸出的結果，並可用於輸入後續動作。 

![][7]



- 從元件庫選取相同的 HDInsight 連接器做為動作。選取 [等候建立叢集] 動作，提供所有必要的參數，然後按一下 ✓。

![][8]



- 從元件庫選取相同的 HDInsight 連接器做為動作。選取 [提交 Hive 工作] 動作，提供所有必要的參數，然後按一下 ✓。

![][9]



- 從元件庫選取相同的 HDInsight 連接器做為動作。選取 [等候工作完成] 動作，提供所有必要的參數，然後按一下 ✓。

![][10]



- 從元件庫選取相同的 HDInsight 連接器做為動作。選取 [刪除叢集] 動作，提供所有必要的參數，然後按一下 ✓。

![][11]

- 使用設計工具介面頂端的儲存命令，儲存邏輯應用程式。

您可以按一下 [立即執行]，手動啟動邏輯應用程式來測試案例。

<!--Image references-->
[1]: ./media/app-service-logic-connector-hdinsight/Create.jpg
[2]: ./media/app-service-logic-connector-hdinsight/CertNotConfigured.jpg
[3]: ./media/app-service-logic-connector-hdinsight/CertConfigured.jpg
[5]: ./media/app-service-logic-connector-hdinsight/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-hdinsight/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-hdinsight/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-hdinsight/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-hdinsight/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-hdinsight/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-hdinsight/LogicApp7.jpg
 

<!---HONumber=62-->