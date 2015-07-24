<properties 
	pageTitle="版本資訊：Microsoft HDInsight Emulator for Azure | Microsoft Azure" 
	description="取得最新版 HDInsight Hadoop Emulator (Hadoop 沙箱環境) 的最新資訊。" 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="jgao"/>




# 版本資訊：Microsoft HDInsight Emulator for Hadoop 



> [AZURE.NOTE]要確認版本號碼，最簡單的方式是在 [新增/移除程式] 中查看 **Microsoft HDInsight Emulator for Azure** (1.0.0.0 或更新版本) 或 **Microsoft HDInsight 開發人員預覽** (1.0.0.0 之前的版本) 的項目。

## 2.0.0.0 版，2014 年 8 月 29 日發行

* 此版本將 HDInsight Emulator 更新為以 3.1 版的服務中目前存在的同一組 Hadoop 專案為目標。    

* 與此產品的預覽版本相同，此版本仍然以開發人員使用為主，因此僅支援單一節點部署。

### 新功能 
 
* [HDInsight 所提供叢集版本的新功能](hdinsight-component-versioning.md)，對應至 3.1 版的服務。其中包括 Hive 0.13 和 Tez 支援。

## 1.0.0.0 版，2013 年 10 月 28 日發行

* 這是 Microsoft HDInsight Emulator for Azure (先前稱為 Microsoft HDInsight Developer Preview) 的正式發行版 (GA)。 

* 與此產品的預覽版本相同，此版本仍然以開發人員使用為主，因此僅支援單一節點部署。

### 新功能 
 
* 已新增指令碼，以簡化將所有 Apache Hadoop 服務設為自動或手動啟動的設定。預設值仍將是自動啟動，但所有服務現在都已可透過 C:\\Hadoop 中安裝的 set-onebox-manualstart.cmd 或 set-onebox-autostart.cmd 指令碼進行變更。 

* 所需的安裝相依性數目已大幅降低，使安裝更為快速。

* 此版本包含用以在安裝於 GettingStarted 資料夾中的 RunSamples.ps1 指令碼中執行 Pig 範例時所使用的命令的錯誤修正。

* 此版本包含與 Azure HDInsight 叢集 1.6 版提供的 Hortonworks Data Platform 服務相符的 Hortonworks Data Platform (HDP) 1.1 版的更新。

## 0.11.0.0 版，2013 年 9 月 30 日發行

### 新功能 
		
* 已移除 HDInsight 儀表板。 

* 此版本包含與 Azure HDInsight 上的 Hortonworks Data Platform Preview 相符的 Hortonworks Data Platform Developer Preview。

## 0.10.0.0 版，2013 年 8 月 9 日發行

### 新功能 
	
* 此版本包含與 Azure HDInsight 上的 Hortonworks Data Platform Preview 相符的 Hortonworks Data Platform Developer Preview。

## 0.8.0.0 版，2013 年 6 月 7 日發行

### 新功能 

* 此版本包含與 Azure HDInsight 上的 Hortonworks Data Platform Preview 相符的 Hortonworks Data Platform Developer Preview。

## 0.6.0.0 版，2013 年 5 月 13 日發行

### 新功能 

* 現在已會安裝 Hive Server 2。與此更新同時間發行的 0.9.2.0 版 Microsoft Hive ODBC 驅動程式必須要有此伺服器。 

* 所有服務都會設為自動啟動，因此在機器重新開機後無須再手動啟動各項服務。

## 0.4.0.0 版，2013 年 3 月 25 日發行

### 新功能 

* 新版的 Microsoft HDInsight Developer Preview 和 Hortonworks Data Platform for Windows Developer Preview。 

* 包含 Apache Hadoop、Hive、Pig、Sqoop、Oozie、HCatalog 和 Templeton。

* 具有下列功能的新 HDInsight 儀表板：
 
	* 連接到多個叢集，包括本機安裝，以及透過 Azure HDInsight 服務從遠端執行的叢集。如需 HDInsight 服務的詳細資訊，請參閱 [http://azure.microsoft.com/documentation/services/hdinsight/](http://azure.microsoft.com/documentation/services/hdinsight/)。
 
	* 在本機叢集上設定 Azure Blob 儲存體。請參閱下列詳細指示。

	* 在新的互動式 Hive 主控台中撰寫及編輯 Hive 查詢。

	* 檢視及下載工作歷程記錄和結果。

### 版本資訊 

* 對於相同的服務，可透過不同的連接埠號碼來存取本機 HDInsight 安裝和 Azure HDInsight 服務上的 REST API 端點： 

	本機：Oozie - http://localhost:11000/oozie/v1/admin/status Templeton - http://localhost:50111/templeton/v1/status ODBC - 在 DSN 組態或連接字串中使用連接埠 10000

	HDInsight 服務：Oozie - http://ServerFQDN:563/oozie/v1/admin/status Templeton - http://ServerFQDN:563/templeton/v1/status ODBC - 在 DSN 組態或連接字串中使用連接埠 563


* 在本機叢集上設定 Azure Blob 儲存體：

	在儀表板中，您會看見名為 "local (hdfs)" 的預設本機叢集。如果您要以 Azure Blob 儲存體作為本機安裝的儲存體，請執行下列動作：

	1. 在位於 C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf 中的 core-site.xml 內新增帳戶標籤：       

			<property>
        		<name>fs.azure.account.key.{AccountName}</name>
        		<value>{Key}</value>
     		</property>

			<property>
        		<name>fs.default.name</name>
       			<!-- cluster variant -->
       	 		<value>asv://ASVContainerName@ASVAccountName</value>
        		<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
        		<final>true</final>
      		</property>

     		<property>
        		<name>dfs.namenode.rpc-address</name>
        		<value>hdfs://localhost:8020</value>
        		<description>A base for other temporary directories.</description>
      		</property>
      
		範例：

			<property>
    			<name>fs.azure.account.key.MyHadoopOnAzureAccountName</name>
				<value>8T45df897/a5fSox1yMjYkX66CriiGgA5zptSXpPdG4o1Qw2mnbbWQN1E9i/i7kihk6FWNhvLlqe02NXEVw6rP==</value>
    		</property>

			<property>
				<name>fs.default.name</name>
				<!-- cluster variant -->
				<value>asv://MyASVContainer@MyASVAccount</value>
				<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
				<final>true</final>
			</property>
        

	2. 在桌面上以提高權限的模式開啟 Hadoop 命令 Shell，並執行下列命令：
	 
	 		%HADOOP_NODE%\stop-onebox.cmd && %HADOOP_NODE%\start-onebox.cmd

	3. 使用完整 URI 來存取該帳戶上的任何檔案：asv://{container}@{account}/{path} (或 asvs://，如果您要使用 HTTPS 來存取資料)。範例：
	 
	 		hadoop fs -lsr 
			asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

	4. 刪除目前註冊的本機叢集，然後以新的 Azure Blob 儲存體認證加以重新註冊。


## 0.3.0.0 版，2012 年 12 月 13 日發行 

* 儀表板網站已變更為匿名驗證，而不使用 Windows 認證。如此，就不會有舊版的版本資訊中提及的登入提示問題。 

* 已修改匯出和某些匯入類型的部分 Sqoop 錯誤。

### 版本問題 

* JavaScript 主控台無法載入。請參閱 0.2.0.0 版的版本資訊，以取得詳細資訊。 
* Sqoop 命令列會顯示下列警告。這些警告將在日後的更新中修正，您可以將其忽略。 
	
		c:\Hadoop\sqoop-1.4.2\bin>sqoop version 
		Setting HBASE_HOME to 
		Warning: HBASE_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\hbase-0.94.2] does not exist HBase imports will fail. 
		Please set HBASE_HOME to the root of your HBase installation. 
		Setting ZOOKEEPER_HOME to 
		Warning: ZOOKEEPER_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\zookeeper-3.4.3] does not exist 
		Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation. 
		Sqoop 1.4.2 
		git commit id 3befda0a456124684768348bd652b0542b002895 
		Compiled by  on Thu 11/29/2012- 3:26:26.10

## 0.2.0.0 版，2012 年 12 月 3 日發行

* Windows Installer 的語法版本簡介 

* 修正 MSDN 論壇上回報的各種安裝問題，特別是安裝 HDInsight 儀表板的相關問題

* 為了提高復原能力而新增的**開始**功能表項目

* 修正 Hive 主控台多行輸入

* 入門內容的微幅更新

### 版本問題 

* JavaScript 主控台無法載入。 

	* 進行某些安裝時，JavaScript 主控台將會失效，且頁面上會出現 HTTP 404 錯誤。若要解決此問題，請直接導覽至 http://localhost:8080 使用主控台。 

* 瀏覽至 HDInsight 儀表板時會引發登入提示

	* 有使用者回報在瀏覽至 HDInsight 儀表板時會引發登入對話方塊。此時，您可以提供現行使用者的登入資訊，如此即應可瀏覽至儀表板。 


## 1.0.0.0 版，2012 年 10 月 23 日發行

* 初始版本 

### 版本問題 

* Hive 主控台 

	* 如果提交的 Hive 命令中包含新行字元，則會出現語法錯誤。請移除新行字元，如此查詢即應可正常執行。 



## 一般已知問題

* Hadoop 使用者密碼過期 

	Hadoop 使用者的密碼有可能會過期，視您推送至機器的 Active Directory 原則而定。下列 Windows PowerShell 指令碼會將密碼設為不會過期，此指令碼可從管理命令提示字元執行：

		$username = "hadoop"
		$ADS_UF_DONT_EXPIRE_PASSWD = 0x10000 # (65536, from ADS_USER_FLAG_ENUM enumeration)

		$computer = [ADSI]("WinNT://$ENV:COMPUTERNAME,computer")
		$users = $computer.psbase.children | where { $_.psbase.schemaclassname -eq "User" }

		foreach($user in $users)
		{
			if($user.Name -eq $username)
			{
				$user.UserFlags = $ADS_UF_DONT_EXPIRE_PASSWD
        		$user.SetInfo()
 
        		$user.PasswordExpired = 0
        		$user.SetInfo()
 
        		Write-Host "$username user maintenance completed. "
    		}
		}


* Temp 目錄
	
	hadoop.tmp.dir 檔案會指向錯誤的位置。它不會指向 C:\\hadoop\\hdfs，而是指向 c:\\hdfs。此錯誤將在下次更新 HDP 位元時修正。

* 作業系統限制

	HDInsight Server 必須安裝在 64 位元作業系統上。

* Web Platform Installer (WebPI) 搜尋結果

	在 WebPI 搜尋結果中找不到 HDInsight。這通常是因為作業系統限制所致。HDInsight 必須使用 64 位元作業系統，且最低版本必須是 Windows 7 Service Pack 1、Windows Server 2008 R2 Service Pack 1、Windows 8 或 Windows Server 2012。

* 管理命令提示字元文件

	* 若要執行 **hadoop mradmin** 或 **hadoop defadmin** 等命令，您必須以 Hadoop 使用者的身分執行。 

	* 若要以該使用者的身分輕鬆建立 Shell，請開啟 Hadoop 命令提示字元，並執行下列程式碼：
	 
	 		start-hadoopadminshell

	* 這將會開啟以 Hadoop 系統管理員權限執行的新命令 Shell。

##<a name="nextsteps"></a>後續步驟

- [開始使用 HDInsight Emulator][hdinsight-hadoop-emulator-get-started]




[hdinsight-hadoop-emulator-get-started]: ../hdinsight-get-started-emulator.md

 

<!---HONumber=July15_HO2-->