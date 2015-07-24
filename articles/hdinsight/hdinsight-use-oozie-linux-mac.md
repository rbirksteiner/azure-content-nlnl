<properties
	pageTitle="在 HDInsight 上使用 Hadoop Oozie | Microsoft Azure"
	description="在 HDInsight 上使用 Hadoop Oozie：一項巨量資料服務。了解如何定義 Oozie 工作流程，以及提交 Oozie 工作。"
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
	ms.date="06/25/2015"
	ms.author="larryfr"/>


# 在以 Linux 為基礎的 HDInsight 上搭配 Hadoop 使用 Oozie 來定義並執行工作流程 (預覽)

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

了解如何用 Apache Oozie 定義使用 Hive 和 Sqoop 的工作流程，然後在以 Linux 為基礎的 HDInsight 叢集上執行該工作流程。

Apache Oozie 是可管理 Hadoop 工作的工作流程/協調系統。它可與 Hadoop 堆疊相整合，並支援 Apache MapReduce、Apache Pig、Apache Hive 和 Apache Sqoop 的 Hadoop 工作。它也可用來排程系統的特定工作，例如 Java 程式或 Shell 指令碼。

> [AZURE.NOTE]還有另一個選項可以定義與 HDInsight 搭配的工作流程，那就是 Azure Data Factory。若要進一步了解 Azure Data Factory，請參閱[搭配 Data Factory 使用 Pig 和 Hive][azure-data-factory-pig-hive]。

##必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**：請參閱[取得 Azure 免費試用](get-azure-free-trial-for-testing-hadoop-in-hdinsight.md)。

- **Azure CLI**：請參閱[安裝和設定 Azure CLI](xplat-cli.md)

- **HDInsight 叢集**：請參閱[開始使用 Linux 上的 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)

- **Azure SQL Database**：建立此資料庫時會用到本文件中的步驟

##範例工作流程

依照本文件的指示所實作的工作流程含有兩個動作：動作就是工作的定義，例如執行 Hive、Sqoop、MapReduce 或其他處理序：

![Workflow diagram][img-workflow-diagram]

1. Hive 動作會執行 HiveQL 指令碼，以從 HDInsight 隨附的 **hivesampletable** 擷取記錄。每個資料列均代表來自特定行動裝置的瀏覽。顯示的記錄格式類似如下：

		8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
		23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
		23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

	本文件中使用的 Hive 指令碼會計算每個平台 (例如 Android 或 iPhone) 的總瀏覽次數，並將計數儲存到新的 Hive 資料表。

	如需 Hive 的詳細資訊，請參閱[在 HDInsight 上使用 Hive][hdinsight-use-hive]。

2.  Sqoop 動作會將新的 Hive 資料表內容匯出至 Azure SQL Database 中的資料表。如需 Sqoop 的詳細資訊，請參閱[搭配 HDInsight 使用 Hadoop Sqoop][hdinsight-use-sqoop]。

> [AZURE.NOTE]如需 HDInsight 叢集支援的 Oozie 版本，請參閱 [HDInsight 所提供 Hadoop 叢集版本的新功能][hdinsight-versions]。

##建立工作目錄

Oozie 的工作所需資源必須儲存在同一個目錄中。此範例使用 **wasb:///tutorials/useoozie**。請使用以下命令建立此目錄，以及建立資料目錄，以保存此工作流程所建立的新 Hive 資料表：

	hadoop fs -mkdir -p /tutorials/useoozie/data

> [AZURE.NOTE]`-p` 參數會使系統建立路徑中的所有目錄 (如果這些目錄尚未存在)。**data** 目錄將用來保存 **useooziewf.hql** 指令碼所使用的資料。

此外，也請執行以下命令，確保 Oozie 在執行 Hive 和 Sqoop 工作時可以模擬您的使用者帳戶。將 **USERNAME** 替換為您的登入名稱：

	sudo adduser USERNAME users

如果您收到錯誤，告訴您該使用者已是使用者成員，可以忽略它。

##新增資料庫驅動程式

由於此工作流程會使用 Sqoop 將資料匯出至 SQL Database，因此您必須提供一份要用來與 SQL Database 溝通的 JDBC 驅動程式複本。請使用以下命令將該複本複製到工作目錄：

	hadoop fs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /tutorials/useoozie/sqljdbc4.jar

如果工作流程使用了其他資源，例如含有 MapReduce 應用程式的 jar，則您也必須新增這些資源。

##定義 Hive 查詢

使用下列步驟建立 HiveQL 指令碼，以定義本文件之後的 Oozie 工作流程中要使用的查詢。

1. 使用 SSH 連接至以 Linux 為基礎的 HDInsight 叢集：

    * **Linux、Unix 或 OS X 用戶端**：請參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Windows 用戶端**：請參閱[從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 使用以下命令建立新的檔案：

        nano useooziewf.hql

1. 開啟 nano 編輯器後，請使用下列項目做為檔案的內容：

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
		FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;

	指令碼中使用了兩個變數：

	- **${hiveTableName}**：將包含要建立的資料表名稱
	- **${hiveDataFolder}**：將包含儲存資料表之資料檔案的位置

	工作流程定義檔 (在本教學課程中為 workflow.xml) 會在執行階段將這些值傳遞至此 HiveQL 指令碼。

2. 按 Ctrl-X，以結束編輯器。出現提示時，請選取 [Y] 儲存檔案，然後按 **Enter** 鍵以使用 **useooziewf.hql** 檔案名稱。

3. 執行以下命令將 **useooziewf.hql** 複製到 **wasb:///tutorials/useoozie/useooziewf.hql**：

		hadoop fs -copyFromLocal useooziewf.hql /tutorials/useoozie/useooziewf.hql

	這些命令會將 **useooziewf.hql** 檔案儲存在與此叢集相關聯的 Azure 儲存體帳戶上，即使刪除叢集，此帳戶也仍會保留該檔案。這讓您得以刪除未使用的叢集以節省成本，同時又能維護工作和工作流程。

##定義工作流程

Oozie 工作流程定義會以 hPDL 撰寫 (一種 XML 程序定義語言)。使用下列步驟定義工作流程：

1. 使用以下陳述式建立和編輯新的檔案：

        nano workflow.xml

1. 開啟 nano 編輯器後，請輸入下列項目做為檔案內容：

		<workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
		  <start to = "RunHiveScript"/>
		  <action name="RunHiveScript">
		    <hive xmlns="uri:oozie:hive-action:0.2">
		      <job-tracker>${jobTracker}</job-tracker>
		      <name-node>${nameNode}</name-node>
		      <configuration>
		        <property>
		          <name>mapred.job.queue.name</name>
		          <value>${queueName}</value>
		        </property>
		      </configuration>
		      <script>${hiveScript}</script>
		      <param>hiveTableName=${hiveTableName}</param>
		      <param>hiveDataFolder=${hiveDataFolder}</param>
		    </hive>
		    <ok to="RunSqoopExport"/>
		    <error to="fail"/>
		  </action>
		  <action name="RunSqoopExport">
		    <sqoop xmlns="uri:oozie:sqoop-action:0.2">
		      <job-tracker>${jobTracker}</job-tracker>
		      <name-node>${nameNode}</name-node>
		      <configuration>
		        <property>
		          <name>mapred.compress.map.output</name>
		          <value>true</value>
		        </property>
		      </configuration>
		      <arg>export</arg>
		      <arg>--connect</arg>
		      <arg>${sqlDatabaseConnectionString}</arg>
		      <arg>--table</arg>
		      <arg>${sqlDatabaseTableName}</arg>
		      <arg>--export-dir</arg>
		      <arg>${hiveDataFolder}</arg>
		      <arg>-m</arg>
		      <arg>1</arg>
		      <arg>--input-fields-terminated-by</arg>
		      <arg>"\t"</arg>
		      <archive>sqljdbc4.jar</archive>
		      </sqoop>
		    <ok to="end"/>
		    <error to="fail"/>
		  </action>
		  <kill name="fail">
		    <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
		  </kill>
		  <end name="end"/>
		</workflow-app>

	此工作流程中定義了兩個動作：

	- **RunHiveScript**：這是起始動作，且會執行 **useooziewf.hql** Hive 指令碼

	- **RunSqoopExport**：此動作會使用 Sqoop 將建立的資料從 Hive 指令碼匯出到 SQL Database。只有 **RunHiveScript** 動作成功時才會執行此動作。

		> [AZURE.NOTE]如需關於 Oozie 工作流程和使用工作流程動作的詳細資訊，請參閱 [Apache Oozie 4.0 文件][apache-oozie-400] (英文，適用於 HDInsight 3.0 版) 或 [Apache Oozie 3.3.2 文件][apache-oozie-332] (英文，適用於 HDInsight 2.1 版)。

	請注意，工作流程有數個項目，例如 `${jobTracker}` 會替換為您在本文件稍後的工作定義中所使用的值。

	此外也請注意 Sqoop 區段中的 `<archive>sqljdbc4.jar</arcive>` 項目。它會指示 Oozie 當此動作執行時，讓 Sqoop 可取得此封存。

2. 依序按 Ctrl-X、**Y** 和 **Enter** 鍵以儲存檔案。

3. 使用以下命令，將 **workflow.xml** 檔案複製到 **wasb:///tutorials/useoozie/workflow.xml**：

		hadoop fs -copyFromLocal workflow.xml wasb:///tutorials/useoozie/workflow.xml

##建立資料庫

下列步驟會建立要接收匯出資料的 Azure SQL Database。

> [AZURE.IMPORTANT]您必須先[安裝和設定 Azure CLI](xplat-cli.md)，才能執行這些步驟。您可以在 HDInsight 叢集，或在本機工作站中，執行 CLI 安裝作業並依照下列步驟建立資料庫。

1. 使用以下命令建立新的 Azure SQL Database 伺服器：

        azure sql server create <adminLogin> <adminPassword> <region>

    例如，`azure sql server create admin password "West US"`

    命令完成時，您應該會收到類似這樣的回應：

        info:    Executing command sql server create
        + Creating SQL Server
        data:    Server Name i1qwc540ts
        info:    sql server create command OK

    > [AZURE.IMPORTANT]請記下此命令傳回的伺服器名稱 (在上述範例中為 **i1qwc540ts**)。 這是所建立的 SQL Database 伺服器的簡短名稱。完整的網域名稱 (FQDN) 為 **&lt;shortname&gt;.database.windows.net**。上述範例中，FQDN 會是 **i1qwc540ts.database.windows.net**。

2. 使用以下命令，在 SQL Database 伺服器上建立名為 **oozietest** 的資料庫：

        azure sql db create [options] <serverName> oozietest <adminLogin> <adminPassword>

    完成時會傳回 [確定] 訊息。

	> [AZURE.NOTE]如果收到的錯誤指出您沒有存取權，您可能需要使用以下命令，將系統的 IP 位址加入至 SQL Database 防火牆：
    >
    > `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

###建立資料表

> [AZURE.NOTE]連接至 SQL Database 建立資料表的方法有很多種。下列步驟會從 HDInsight 叢集使用 [FreeTDS](http://www.freetds.org/)。

3. 使用以下命令在 HDInsight 叢集上安裝 FreeTDS：

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. 安裝 FreeTDS 後，請使用下列命令來連接至先前建立的 SQL Database 伺服器：

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest

    您將收到類似以下的輸出：

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

5. 在 `1>` 提示字元輸入下列幾行：

		CREATE TABLE [dbo].[mobiledata](
		[deviceplatform] [nvarchar](50),
		[count] [bigint])
		GO
		CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
		GO

    輸入 `GO` 陳述式後，將評估先前的陳述式。這麼做會建立名為 **mobiledata** 的新資料表，Sqoop 將會寫入此資料表。

    使用下列命令來確認已建立資料表：

        SELECT * FROM information_schema.tables
        GO

    您應該會看到如下所示的輸出：

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

8. 在 `1>` 提示字元輸入 `exit` 以結束 tsql 公用程式。

##建立工作定義

工作定義會描述可在哪裡找到 workflow.xml 以及工作流程所使用的其他檔案 (例如 useooziewf.hql)。 它也會定義工作流程中所使用的屬性值，以及相關聯的檔案。

1. 使用以下命令取得預設儲存體的完整 WASB 位址。這稍後會在組態檔中用到：

		sed -n '/<name>fs.default/,/</value>/p' /etc/hadoop/conf/core-site.xml

	應該會傳回類似以下的資訊：

		<name>fs.defaultFS</name>
		<value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>

	儲存 **wasb://mycontainer@mystorageaccount.blob.core.windows.net** 值，後續的步驟中會使用該值。

2. 使用以下命令取得叢集前端節點的 FQDN。該項目將用於叢集的 JobTracker 位址。這稍後會在組態檔中用到：

		hostname -f

	此命令傳回的資訊類似如下：

		headnode0.CLUSTERNAME-ssh.j7.internal.cloudapp.net

	JobTracker 使用的連接埠是 8050，因此要用於 JobTracker 的完整位址便是 **headnode0.CLUSTERNAME-ssh.j7.internal.cloudapp.net:8050**。

1. 使用以下命令建立 Oozie 工作定義組態：

		nano job.xml

2. 開啟 nano 編輯器後，請使用下列項目做為檔案的內容：

		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>

		  <property>
		    <name>nameNode</name>
		    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
		  </property>

		  <property>
		    <name>jobTracker</name>
		    <value>JOBTRACKERADDRESS</value>
		  </property>

		  <property>
		    <name>queueName</name>
		    <value>default</value>
		  </property>

		  <property>
		    <name>oozie.use.system.libpath</name>
		    <value>true</value>
		  </property>

		  <property>
		    <name>hiveScript</name>
		    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
		  </property>

		  <property>
		    <name>hiveTableName</name>
		    <value>mobilecount</value>
		  </property>

		  <property>
		    <name>hiveDataFolder</name>
		    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
		  </property>

		  <property>
		    <name>sqlDatabaseConnectionString</name>
		    <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
		  </property>

		  <property>
		    <name>sqlDatabaseTableName</name>
		    <value>mobiledata</value>
		  </property>

		  <property>
		    <name>user.name</name>
		    <value>YourName</value>
		  </property>

		  <property>
		    <name>oozie.wf.application.path</name>
		    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
		  </property>
		</configuration>

	* 將 **wasb://mycontainer@mystorageaccount.blob.core.windows.net** 的所有執行個體替換為您之前收到的值。
	> [AZURE.WARNING]您必須使用完整的 WASB 路徑，其中包含容器和儲存體帳戶做為路徑的一部分。使用簡短格式 (wasb:///) 會在工作開始時導致 RunHiveScript 動作失敗。

	* 將 **JOBTRACKERADDRESS** 替換為您之前收到的 JobTracker/ResourceManager 位址。

	* 用您 HDInsight 叢集的登入名稱取代 **YourName**。

	* 用您 Azure SQL Database 的資訊取代 **serverName**、**adminLogin** 和 **adminPassword**。

	此檔案中大部分的資訊都會用來填入 workflow.xml 或 ooziewf.hql 檔案中所使用的值 (例如 ${nameNode})。

	> [AZURE.NOTE]**oozie.wf.application.path** 項目會定義要在何處尋找 workflow.xml 檔案，該檔案包含此工作執行過的工作流程。

2. 依序按 Ctrl-X、**Y** 和 **Enter** 鍵以儲存檔案。

##提交和管理工作

下列步驟使用 Oozie 命令提交及管理叢集上的 Oozie 工作流程。Oozie 命令是 [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html) 上的易記介面。

> [AZURE.IMPORTANT]使用 Oozie 命令時，您必須使用 HDInsight 前端節點的 FQDN。只有從叢集才能存取此 FQDN，或者，如果叢集位於 Azure 虛擬網路，就必須從同一個網路中的其他電腦來存取。

1. 使用以下命令取得 Oozie 服務的 URL：

		sed -n '/<name>oozie.base.url/,/</value>/p' /etc/oozie/conf/oozie-site.xml

	此命令會傳回類似以下的值：

		<name>oozie.base.url</name>
		<value>http://headnode0.CLUSTERNAME-ssh.j7.internal.cloudapp.net:11000/oozie</value>

	**http://headnode0.CLUSTERNAME-ssh.j7.internal.cloudapp.net:11000/oozie** 部分是要搭配 Oozie 命令使用的 URL。

2. 使用以下命令建立 URL 的環境變數，讓您不必為每個命令輸入該 URL：

		export OOZIE_URL=http://headnode0.CLUSTERNAME-ssh.j7.internal.cloudapp.net:11000/oozie

	將 URL 替換為您稍早收到的 URL。

3. 使用以下命令提交工作：

		oozie job -config job.xml -submit

	這麼做會從 **job.xml** 載入工作資訊，並將工作資訊提交至 Oozie，但不會執行該工作。

	命令完成時，會傳回工作的 ID。例如，`0000005-150622124850154-oozie-oozi-W`。這將用來管理工作。

4. 使用以下命令檢視工作的狀態。輸入上一個命令所傳回的工作 ID：

		oozie job -info <JOBID>

	這會傳回類似以下的資訊。

		Job ID : 0000005-150622124850154-oozie-oozi-W
		------------------------------------------------------------------------------------------------------------------------------------
		Workflow Name : useooziewf
		App Path      : wasb:///tutorials/useoozie
		Status        : PREP
		Run           : 0
		User          : USERNAME
		Group         : -
		Created       : 2015-06-22 15:06 GMT
		Started       : -
		Last Modified : 2015-06-22 15:06 GMT
		Ended         : -
		CoordAction ID: -
		------------------------------------------------------------------------------------------------------------------------------------

	這項工作的狀態為 `PREP`，這表示該工作已提交，但尚未開始。

4. 使用下列命令來啟動工作：

		oozie job -start JOBID

	如果您在使用此命令後檢查狀態，會發現工作為執行中狀態，並傳回工作內的動作資訊。

5. 工作順利完成後，您可以使用以下命令，確認是否已產生資料並匯出至 SQL Database 資料表：

		TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest

	在 `1>` 提示中輸入下列內容：

		SELECT * FROM mobiledata
		GO

	您應該會收到類似以下的資訊：

		deviceplatform  count
		Android 31591
		iPhone OS       22731
		proprietary development 3
		RIM OS  3464
		Unknown 213
		Windows Phone   1791
		(6 rows affected)

如需 Oozie 命令的詳細資訊，請參閱 [Oozie 命令列工具](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html)。

##Oozie REST API

Oozie REST API 可讓您建置自己的工具來搭配 Oozie 使用。以下為使用 Oozie REST API 時 HDInsight 的特定資訊：

* **URI**：可從叢集的外部 (位於 `https://CLUSTERNAME.azurehdinsight.net/oozie`) 存取 REST API

* **驗證**：您必須使用叢集的 HTTP 帳戶 (admin) 和密碼來驗證 API。例如：

		curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions

如需使用 Oozie REST API 的詳細資訊，請參閱 [Oozie Web 服務 API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)。

##Oozie Web UI

Oozie Web UI 可讓您用網頁檢視叢集上 Oozie 工作的狀態。它可讓您檢視工作狀態、工作定義、設定、工作中的動作圖表以及工作的記錄。您也可以檢視工作內的動作詳細資料。

若要存取 Oozie Web UI，請依照下列步驟進行：

1. 建立 HDInsight 叢集的 SSH 通道。如需執行這項作業的說明，請參閱下列其中一項：

	* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md#tunnel)

	* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel)

2. 建立通道後，請在網頁瀏覽器中開啟 Ambari Web UI。Ambari 網站的 URI 是 **https://CLUSTERNAME.azurehdinsight.net**。請將 **CLUSTERNAME** 替換為您以 Linux 為基礎的 HDInsight 叢集名稱。

3. 在頁面左邊選取 [Oozie]，然後依序選取 [Quick Links] (快速連結) 和 [Oozie Web UI]。

	![功能表的圖](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Oozie Web UI 預設會顯示執行中的工作流程工作。若要查看所有的工作流程工作，請選取 [All Jobs] (所有工作)。

	![顯示的所有工作](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. 選取一項工作，以檢視該工作的詳細資訊。

	![工作資訊](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. 您可以在 [Job Info] (工作資訊) 索引標籤中看到基本的工作資訊，以及工作內的個別動作。使用上方的索引標籤，即可檢視 Job Definition (工作定義)、Job Configuration (工作組態)，以及存取 Job Log (工作記錄)，或檢視工作的定向非循環圖 (DAG)。

	* **Job Log (工作記錄)**：選取 [GetLogs] 按鈕，以取得工作的所有記錄，或使用 [Enter Search Filter] (輸入搜尋篩選條件) 欄位來篩選記錄。

		![工作記錄](./media/hdinsight-use-oozie-linux-mac/joblog.png)

	* **JobDAG**：DAG 是將整個工作流程所採取的資料路徑予以圖形化的概觀

		![工作 DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. 在 [Job Info] (工作資訊) 索引標籤中選取其中一個動作，便會出現此動作的資訊。例如，選取 **RunHiveScript** 動作。

	![動作資訊](./media/hdinsight-use-oozie-linux-mac/action.png)

8. 您可以查看動作的詳細資料，包括 **Console URL (主控台 URL)** 的連結，此連結可用來檢視工作的 JobTracker 資訊。

##排程工作

協調器可讓您指定工作的開始時間、結束時間和發生頻率，如此便可將這些工作安排在特定的時間。

若要定義工作流程的排程，請依照下列步驟進行：

1. 使用以下命令建立名為 **coordinator.xml** 的新檔案：

		nano coordinator.xml

	使用下列項目做為檔案的內容：

		<coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
	      <action>
	        <workflow>
	          <app-path>${workflowPath}</app-path>
	        </workflow>
	      </action>
		</coordinator-app>

	請注意，其中使用的 `${...}` 變數將替換為工作定義中的值。這些變數分為別：

	* **${coordFrequency}**：執行工作執行個體所相隔的時間
	* **${coordStart}**：工作開始時間
	* **${coordEnd}**：工作結束時間
	* **${coordTimezone}**：協調器工作位在固定時區，不受日光節約時間影響 (通常使用 UTC 表示)。此時區稱為「Oozie 處理時區」
	* **${wfPath}**：workflow.xml 的路徑

2. 依序按 Ctrl-X、**Y** 和 **Enter** 鍵以儲存檔案。

3. 使用以下命令將該檔案複製到此工作的工作目錄：

		hadoop fs -copyFromLocal coordinator.xml /tutorials/useoozie/coordinator.xml

4. 使用以下命令修改 **job.xml** 檔案：

		nano job.xml

	進行下列變更：

	* 將 `<name>oozie.wf.application.path</name>` 變更為 `<name>oozie.coord.application.path</name>`。這麼做會指示 Oozie 執行協調器檔案，而不是工作流程檔案

	* 加入下列項目，這將會設定 coordinator.xml 中所使用的變數，以指向 workflow.xml 的位置：

		    <property>
		      <name>workflowPath</name>
		      <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
		    </property>

		將 **mycontainer** 和 **mystorageaccount** 的值替換為 job.xml 檔案中其他項目所使用的值。

	* 加入下列項目，以定義 coordinator.xml 檔案要使用的開始時間、結束時間和頻率：

		    <property>
		      <name>coordStart</name>
		      <value>2015-06-25T12:00Z</value>
		    </property>

		    <property>
		      <name>coordEnd</name>
		      <value>2015-06-27T12:00Z</value>
		    </property>

		    <property>
		      <name>coordFrequency</name>
		      <value>1440</value>
		    </property>

		    <property>
		      <name>coordTimezone</name>
		      <value>UTC</value>
		    </property>

		這會將開始時間設定為 2015 年 6 月 25 日中午 12:00、將結束時間設定為 2015 年 6 月 27 日，而執行此工作的間隔則設定為每日 (頻率是以分鐘為單位，因此 24 小時 x 60 分鐘 = 1440 分鐘)。 最後，時區會設定為 UTC。

5. 依序按 Ctrl-X、**Y** 和 **Enter** 鍵以儲存檔案。

6. 若要執行工作，請使用以下命令：

		oozie job -config job.xml -run

	這麼做會提交並啟動工作。

7. 如果您造訪 Oozie Web UI，並選取 [Coordinator Jobs] (協調器工作) 索引標籤，您應會看到類似以下的資訊：

	![協調器工作索引標籤](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

	請注意 **Next Materialization (下一次具體化)** 項目，這是下次執行工作的時間。

8. 與先前的工作流程工作類似，在 Web UI 中選取工作項目會顯示工作的資訊：

	![協調器工作資訊](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

	請注意，這只會顯示工作的成功執行項目，而不是排程工作流程內的個別動作。若要查看這些動作，請選取其中一個 **Action (動作)** 項目。這麼做會顯示類似先前針對工作流程工作所擷取的資訊。

	![動作資訊](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

##疑難排解

Oozie UI 對於疑難排解 Oozie 工作的問題很有幫助，因為它可讓您輕鬆檢視 Oozie 記錄以及 MapReduce 工作 (例如 Hive 查詢) 的 JobTracker 連結。一般而言，疑難排解的模式應該是：

1. 在 Oozie Web UI 中檢視工作。

2. 如果發生錯誤或特定動作失敗，請選取該動作，以查看 [Error Message] (錯誤訊息) 欄位是否有提供失敗的詳細資訊。

3. 如果有提供，請使用動作的 URL 以檢視動作的更多詳細資料 (例如 JobTracker 記錄)。

以下是您可能會遇到的特定錯誤，以及解決這些問題的方法。

###JA009：無法初始化叢集

**徵兆**：工作狀態會變更為 **SUSPENDED**。工作的詳細資料會將 RunHiveScript 狀態顯示為 **START_MANUAL**。選取該動作會顯示下列錯誤訊息：

	JA009: Cannot initialize Cluster. Please check your configuration for map

**原因**：**job.xml** 檔案中使用的 WASB 位址未包含儲存體容器或儲存體帳戶名稱。WASB 位址格式必須是 `wasb://containername@storageaccountname.blob.core.windows.net`。

**解決方法**：變更工作所使用的 WASB 位址。

###JA002：不允許 Oozie 模擬 &lt;USER>

**徵兆**：工作狀態會變更為 **SUSPENDED**。工作的詳細資料會將 RunHiveScript 狀態顯示為 **START_MANUAL**。選取該動作會顯示下列錯誤訊息：

	JA002: User: oozie is not allowed to impersonate <USER>

**原因**：目前的權限設定不允許 Oozie 模擬指定的使用者帳戶。

**解決方法**：Oozie 可以模擬**使用者**群組中的使用者。使用 `groups USERNAME` 查看使用者帳戶所屬的群組。如果使用者不是**使用者**群組的成員，請使用以下命令將使用者新增至群組：

	sudo adduser USERNAME users

> [AZURE.NOTE]這可能需要幾分鐘，HDInsight 才能辨識出使用者已新增至該群組。

###啟動器錯誤 (Sqoop)

**徵兆**：工作狀態會變更為 **KILLED**。工作的詳細資料會將 RunSqoopExport 狀態顯示為 **ERROR**。選取該動作會顯示下列錯誤訊息：

	Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**原因**：Sqoop 無法載入存取資料庫時所需的資料庫驅動程式。

**解決方法**：從 Oozie 工作使用 Sqoop 時，必須將資料庫驅動程式與工作所使用的其他資源 (例如 workflow.xml) 包含在一起。

您還必須從 workflow.xml 的 `<sqoop>...</sqoop>` 區段，參考含有資料庫驅動程式的封存。

例如，您可以針對本文件中的工作使用下列步驟：

1. 將 sqljdbc4.jar 檔複製到 /tutorials/useoozie 目錄：

		 hadoop fs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /tutorials/useoozie/sqljdbc4.jar

2. 修改 workflow.xml，在 `</sqoop>` 上方用新的一行加入下列內容：

		<archive>sqljdbc4.jar</archive>

##後續步驟
在本教學課程中，您已了解如何定義 Oozie 工作流程，以及如何執行 Oozie 工作。若要深入了解如何使用 HDInsight，請參閱下列文章：

- [搭配 HDInsight 使用以時間為基礎的 Hadoop Oozie 協調器][hdinsight-oozie-coordinator-time]
- [在 HDInsight 上將 Hadoop 工作的資料上傳][hdinsight-upload-data]
- [在 HDInsight 上搭配 Hadoop 使用 Sqoop][hdinsight-use-sqoop]
- [在 HDInsight 上搭配 Hadoop 使用 Hive][hdinsight-use-hive]
- [搭配使用 Pig 與 HDInsight 上的 Hadoop][hdinsight-use-pig]
- 〈[開發 HDInsight 的 Java MapReduce 程式][hdinsight-develop-mapreduce]〉


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: data-factory-pig-hive-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md

[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/zh-tw/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

<!---HONumber=July15_HO2-->