<properties
	pageTitle="在 HDInsight 上啟用 Hadoop 服務的堆積傾印 | Microsoft Azure"
	description="在以 Linux 為基礎的 HDInsight 叢集上啟用 Hadoop 服務的堆積傾印，以進行偵錯和分析。"
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
	ms.date="06/19/2015"
	ms.author="larryfr"/>


#在以 Linux 為基礎的 HDInsight 上啟用 Hadoop 服務的堆積傾印 (預覽)

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

堆積傾印含有應用程式記憶體的快照，其中包括建立傾印時的變數值。因此堆積傾印非常有助於診斷在執行階段發生的問題。

> [AZURE.NOTE]本文的資訊僅適用於以 Linux 為基礎的 HDInsight。如需以 Windows 為基礎的 HDInsight 資訊，請參閱[在以 Windows 為基礎的 HDInsight 上啟用 Hadoop 服務的堆積傾印](hdinsight-hadoop-collect-debug-heap-dumps.md)。

## <a name="whichServices"></a>服務

您可以啟用下列服務的堆積傾印：

*  **hcatalog** - tempelton
*  **hive** - hiveserver2、metastore、derbyserver
*  **mapreduce** - jobhistoryserver
*  **yarn** - resourcemanager、nodemanager、timelineserver
*  **hdfs** - datanode、secondarynamenode、namenode

您也可以針對 HDInsight 所執行的 map 和 reduce 處理序來啟用堆積傾印。

## <a name="configuration"></a>了解堆積傾印組態

堆積傾印的啟用方式，是在服務啟動時將選項 (有時稱為參數) 傳遞至 JVM。就大部分的 Hadoop 服務而言，修改啟動服務所使用的 Shell 指令碼即可完成這項作業。

在每個指令碼中，有一項 ***_OPTS** 匯出，其中含有傳遞至 JVM 的選項。例如，在 **hadoop-env.sh** 指令碼中，以 `export HADOOP_NAMENODE_OPTS=` 為開頭的那一行即含有 NameNode 服務的選項。

map 和 reduce 處理序會稍有不同，因為它們是 MapReduce 服務的子處理序。每個 map 或 reduce 處理序都會在一個子容器中執行，且有兩個含有這些 JVM 選項的項目。兩者均包含在 **mapred-site.xml** 中：

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [AZURE.NOTE]我們建議使用 Ambari 來修改指令碼和 mapred-site.xml 設定，因為 Ambari 會處理叢集中跨節點的複寫變更。如需特定的步驟，請參閱[使用 Ambari](#using-ambari) 一節。

###啟用堆積傾印

以下選項可在發生 OutOfMemoryError 時啟用堆積傾印：

    -XX:+HeapDumpOnOutOfMemoryError

**+** 表示已啟用此選項。預設值為停用。

> [AZURE.WARNING]根據預設不會在 HDInsight 上啟用 Hadoop 服務的堆積傾印，因為傾印檔案可能會很龐大。如果您為了進行疑難排解而啟用它們，請記得在重現問題並收集傾印檔案之後將其停用。

###傾印位置

傾印檔案的預設位置是目前的工作目錄。您可以使用以下選項，控制檔案的儲存位置：

    -XX:HeapDumpPath=/path

例如，使用 `-XX:HeapDumpPath=/tmp` 會使傾印儲存在 /tmp 目錄中。

###指令碼

您也可以在發生 **OutOfMemoryError** 時觸發指令碼。例如觸發通知，讓您知道發生了錯誤。這可由以下選項來控制：

    -XX:OnOutOfMemoryError=/path/to/script

> [AZURE.NOTE]由於 Hadoop 是分散式系統，因此所有使用的指令碼都必須放置在服務所在叢集中的所有節點上。
>
> 指令碼也必須位於服務所屬的帳戶可以存取的位置中，而且必須提供執行權限。例如，您可能想要在 `/usr/local/bin` 中存放指令碼，並用 `chmod go+rx /usr/local/bin/filename.sh` 授與讀取和執行權限。

##使用 Ambari

若要修改服務的組態，請依照下列步驟進行：

1. 開啟叢集的 Ambari Web UI。URL 會是 https://YOURCLUSTERNAME.azurehdinsight.net。

    出現提示時，請使用您叢集的 HTTP 帳戶名稱 (預設值：admin) 和密碼來向網站驗證。

    > [AZURE.NOTE]Ambari 可能會再次提示您輸入使用者名稱和密碼。此時只要重新輸入同一組帳戶名稱和密碼即可。

2. 使用左側的清單，選取您想要修改的服務區域。例如 **HDFS**。在中間區域內，選取 [設定] 索引標籤。

    ![Ambari Web 圖片 (已選取 HDFS 設定索引標籤)](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. 在 [篩選...] 項目中輸入 **opts**。這麼做不但可以將設定項目的清單篩選為只包含此文字的項目，同時也是能夠快速找到 Shell 指令碼或**範本** (可以用來設定這些選項) 的好方法。

    ![篩選的清單](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. 針對您想要啟用堆積傾印的服務尋找 ***_OPTS** 項目，並新增您想要啟用的選項。在下圖中，我將 `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` 新增至 **HADOOP_NAMENODE_OPTS** 項目：

    ![含有 -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/ 的 HADOOP_NAMENODE_OPTS](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

	> [AZURE.NOTE]啟用 map 或 reduce 子處理序的堆積傾印時，會改為尋找標示為 **mapreduce.admin.map.child.java.opts** 和 **mapreduce.admin.reduce.child.java.opts** 的欄位。

    按 [儲存] 按鈕以儲存變更。系統會允許您輸入簡短的附註，說明所做的變更。

5. 套用變更後，一或多個服務旁邊就會出現**必須重新啟動**的圖示。

    ![必須重新啟動圖示和重新啟動按鈕](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. 選取每個需要重新啟動的服務，並使用 [服務動作] 按鈕**開啟維護模式**。這可避免您在重新啟動此服務時，從中產生警示。

    ![開啟維護模式功能表](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. 啟用維護模式後，請使用服務的 [重新啟動] 按鈕來**重新啟動所有受影響的項目**。

    ![重新啟動所有受影響的項目](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

    > [AZURE.NOTE]其他服務的 [重新啟動] 按鈕項目可能會有所不同。

8. 重新啟動服務後，請使用 [服務動作] 按鈕**關閉維護模式**。這麼做可讓 Ambari 繼續監視服務是否有警示。

<!---HONumber=July15_HO2-->