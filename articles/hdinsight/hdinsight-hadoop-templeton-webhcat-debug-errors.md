<properties
 pageTitle="了解並解決 HDInsight 上的 WebHCat 錯誤"
 description="了解 WebHCat 在 HDInsight 上傳回的常見錯誤以及如何解決這些問題。"
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
 ms.date="06/04/2015"
 ms.author="larryfr"/>

#了解並解決 HDInsight 上從 WebHCat (Templeton) 收到的錯誤

透過 WebHCat (之前稱為 Templeton) 使用 HDInsight 時，可能會收到錯誤。本文件提供常見錯誤的指引 – 發生的原因，以及解決這些問題的方法。

##什麼是 WebHCat？

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) 是適用於 [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog) 的 REST API，這是 Hadoop 的資料表和儲存體管理層。WebHCat 預設會在 HDInsight 叢集上啟用，並由各種工具用來提交工作、取得工作狀態等，而不需登入叢集。

##修改組態

> [AZURE.IMPORTANT]因為已超過設定的上限，而發生本文件中所列的幾個錯誤。當解決步驟提到您可以變更值時，您必須使用下列其中一項來執行變更：

* 針對 **Windows** 叢集：使用指令碼動作在叢集建立期間設定此值。如需詳細資訊，請參閱[開發指令碼動作](hdinsight-hadoop-script-actions.md)。

* 針對 **Linux** 叢集：使用 Ambari (Web 或 REST API) 來修改此值。如需詳細資訊，請參閱[使用 Ambari 管理 HDInsight](hdinsight-hadoop-manage-ambari.md)

###預設組態

下列預設組態值會影響 WebHCat 效能，而如果超過這些值則會造成錯誤：

| 設定 | 作用 | 預設值 |
| ------- | ------------ | ------------- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] | 可以同時為作用中 (擱置或執行中) 的工作數目上限 | 10,000 |
| [templeton.exec.max-procs][max-procs] | 可以同時提供服務的要求數目上限 | 20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] | 將保留作業歷程記錄的天數 | 7 天 |

##要求太多

**HTTP 狀態碼**：429

| 原因 | 解決方案 |
| ----- | ---------- |
| 您已超出 WebHCat 每分鐘提供服務的並行要求上限 (預設值為 20) | 減少您的工作負載以確保不會提交超過並行要求數目上限，或藉由修改 `templeton.exec.max-procs` 來提高並行要求限制。如需詳細資訊，請參閱[修改組態](#modifying-configuration)。 |

##無法使用伺服器

**HTTP 狀態碼**：503

| 原因 | 解決方案 |
| ---------------- | ------------------- |
| 這通常發生於叢集的主要和次要 HeadNode 間的容錯移轉期間 | 等候兩分鐘，然後重試作業 |

##不正確的要求內容：找不到工作

**HTTP 狀態碼**：400

| 原因 | 解決方案 |
| ---------------- | ------------------- |
| 工作歷程記錄清除器已清除工作詳細資料 | 工作歷程記錄的預設保留期間為 7 天。修改 `mapreduce.jobhistory.max-age-ms` 即可變更此值。如需詳細資訊，請參閱[修改組態](#modifying-configuration)。 |
| 工作因為容錯移轉而遭到刪除 | 最多重試工作提交兩分鐘 |
| 使用的工作識別碼無效 | 檢查工作識別碼是否正確 |

##閘道不正確

**HTTP 狀態碼**：502

| 原因 | 解決方案 |
| ---------------- | ------------------- |
| 內部記憶體回收會發生於 WebHCat 程序中 | 等候記憶體回收完成，或重新啟動 WebHCat 服務 |
| 等候 ResourceManager 服務回應時逾時。這發生於作用中應用程式的數目達到設定的最大值時 (預設值為 10,000) | 等候目前執行中的工作完成，或修改 `yarn.scheduler.capacity.maximum-applications` 以提高並行工作限制。如需詳細資訊，請參閱[修改組態](#modifying-configuration)。 |
| 嘗試在 `Fields` 設為 `*` 時透過 [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) 呼叫擷取所有工作時 | 請勿擷取*所有* 工作詳細資料，改用 `jobid` 僅只擷取大於特定工作識別碼的工作詳細資料。或者，不要使用 `Fields` |
| WebHCat 服務在 HeadNode 容錯移轉期間關閉 | 等候兩分鐘，然後重試作業 |
| 有 500 個以上透過 WebHCat 提交的擱置工作 | 等到目前擱置的工作完成，再送出更多工作 |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
 

<!---HONumber=July15_HO2-->