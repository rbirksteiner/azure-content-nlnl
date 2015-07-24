<properties title="Configuring Oracle Data Guard for Azure" pageTitle="設定適用於 Azure 的 Oracle Data Guard" description="在 Azure 虛擬機器上逐步執行設定和實作高可用性和嚴重損壞修復之 Oracle Data Guard 的教學課程。" services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#設定適用於 Azure 的 Oracle Data Guard
本教學課程示範如何在 Azure 虛擬機器環境中設定和實作 Oracle Data Guard，以取得高可用性並進行嚴重損壞修復 。本教學課程著重於非 RAC Oracle 資料庫的單向複寫。

Oracle Data Guard 支援 Oracle 資料庫的資料保護和嚴重損壞修復。它是簡單、高效能且直接的解決方案，適用於整個 Oracle 資料庫的嚴重損壞修復、資料保護和高可用性。

本教學課程假設您已經具備 Oracle 資料庫高可用性和嚴重損壞修復概念的理論和實務知識。如需相關資訊，請參閱 [Oracle 網站](http://www.oracle.com/technetwork/database/features/availability/index.html)和 [Oracle Data Guard 概念與管理指南](http://docs.oracle.com/cd/E11882_01/server.112/e17022/create_ps.htm)。

此外，本教學課程假設您已經實作下列必要條件：

- 您已經檢閱過 [Oracle 虛擬機器映像 - 其他考量](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)主題中的＜高可用性和嚴重損壞修復考量＞一節。請注意，Azure 支援獨立的 Oracle 資料庫執行個體，但目前不支援 Oracle Real Application Cluster (Oracle RAC)。

- 您已經使用 Windows Server 上提供 Oracle Enterprise Edition 映像的相同平台，在 Azure 中建立兩部虛擬機器 (VM)。如需相關資訊，請參閱[在 Azure 中建立 Oracle Database 12c 虛擬機器](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md)和 [Azure 虛擬機器](http://azure.microsoft.com/documentation/services/virtual-machines/)。請確定虛擬機器都位於[相同的雲端服務](virtual-machines-load-balance.md)和相同的[虛擬網路](azure.microsoft.com/documentation/services/virtual-network/)中，以確保它們可以透過永續的私人 IP 位址互相存取。此外，建議您將 VM 放在相同的[可用性設定組](virtual-machines-manage-availability.md)中，讓 Azure 可將其放置於不同的容錯網域和升級網域。請注意，Oracle Data Guard 僅適用於 Oracle Database Enterprise Edition。每部機器必須至少有 2 GB 的記憶體和 5 GB 的磁碟空間。如需平台上所提供 VM 大小的最新資訊，請參閱[適用於 Azure 的虛擬機器大小](http://msdn.microsoft.com/library/dn197896.aspx)。如果您的 VM 需要額外的磁碟區，則可連接其他磁碟。如需相關資訊，請參閱[如何將資料磁碟連接至虛擬機器](storage-windows-attach-disk.md)。

- 您已在 Azure 入口網站上，針對主要 VM 將虛擬機器名稱設定為 “Machine1”，並針對待命 VM 設定為 “Machine2”。

- 您已經設定 **ORACLE_HOME** 環境變數，以指向主要和待命虛擬機器中的同一個 oracle 根安裝路徑，例如 `C:\OracleDatabase\product\11.2.0\dbhome_1\database`。

- 您使用 **Administrators** 群組的成員身分或 **ORA_DBA** 群組的成員身分來登入 Windows Server。

在本教學課程中，您將：

實作實體的待命資料庫環境

1. 建立主要資料庫

2. 準備主要資料庫以用於建立待命資料庫

	1. 啟用強制記錄

	2. 建立密碼檔案

	3. 設定待命重做記錄

	4. 啟用封存

	5. 設定主要資料庫的初始化參數

建立實體的待命資料庫

1. 準備待命資料庫的初始設定參數檔案

2. 設定接聽程式和 tnsnames，以支援主要和待命機器上的資料庫

	1. 在這兩部伺服器上設定 listener.ora，以保存這兩個資料庫的項目

	2. 在主要和待命虛擬機器上設定 tnsnames.ora，以保存主要和待命資料庫的項目

	3. 啟動接聽程式，並檢查已在這兩部虛擬機器上對這兩個服務進行 tnsping。

3. 以 NoMount 狀態啟動待命執行個體

4. 使用 RMAN 來複製資料庫複製和建立待命資料庫

5. 在受管理的復原模式中啟動實體的待命資料庫

6. 驗證實體的待命資料庫

> [AZURE.IMPORTANT]本教學課程已根據下列硬體和軟體設定進行設定和測試：
>
>| | **主要資料庫** | **待命資料庫** |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Oracle 版本** | Oracle11g 企業版 (11.2.0.4.0) | Oracle11g 企業版 (11.2.0.4.0) |
>| **機器名稱** | Machine1 | Machine2 |
>| **作業系統** | Windows 2008 R2 | Windows 2008 R2 |
>| **Oracle SID** | TEST | TEST_STBY |
>| **記憶體** | 最少 2 GB | 最少 2 GB |
>| **磁碟空間** | 最少 5 GB | 最少 5 GB |

針對 Oracle 資料庫和 Oracle Data Guard 的後續版本，可能會有一些您需要實作的其他變更。如需最新的版本特定資訊，請參閱 Oracle 網站上的[資料防護](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html)和 [Oracle 資料庫](http://www.oracle.com/us/corporate/features/database-12c/index.html)文件。

##實作實體的待命資料庫環境
### 1.建立主要資料庫

- 在主要虛擬機器中建立主要資料庫 "TEST"。如需相關資訊，請參閱＜建立和設定 Oracle 資料庫＞。
- 在 SQL*Plus 命令提示字元中，使用 SYSDBA 角色，以 SYS 使用者身分來連接您的資料庫，並執行下列陳述式來查看您的資料庫名稱：

		SQL> select name from v$database;
		
		The result will display like the following:
		
		NAME
		---------
		TEST
- 接下來，從 dba_data_files 系統檢視中查詢資料庫檔案的名稱：

		SQL> select file_name from dba_data_files; 
		FILE_NAME 
		------------------------------------------------------------------------------- 
		C:\ <YourLocalFolder>\TEST\USERS01.DBF
		C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
		C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
		C:<YourLocalFolder>\TEST\SYSTEM01.DBF
		C:<YourLocalFolder>\TEST\EXAMPLE01.DBF

### 2.準備主要資料庫以用於建立待命資料庫

建立待命資料庫之前，建議您先確認已正確設定主要資料庫。以下是您需要執行的步驟清單：

1. 啟用強制記錄
2. 建立密碼檔案
3. 設定待命重做記錄
4. 啟用封存
5. 設定主要資料庫的初始化參數

#### 啟用強制記錄

為了實作待命資料庫，我們必須在主要資料庫中啟用「強制記錄」。此選項可確保即使是在已完成 'nologging' 操作的事件中，強制記錄都具有最高的優先順序，而所有的操作都會記錄到重做記錄中。因此，我們確定主要資料庫中的每項操作都會記錄下來，而且複寫到待命資料庫時會包含主要資料庫中的所有操作。執行 alter database 陳述式來啟用強制記錄：

	SQL> ALTER DATABASE FORCE LOGGING;

	Database altered.

#### 建立密碼檔案

為了能夠傳送主要伺服器的封存記錄並套用到待命伺服器，主要和待命伺服器上的 sys 密碼必須完全相同。這就是為什麼您要在主要資料庫上建立密碼檔案，並將它複製到待命伺服器的原因。

>[AZURE.IMPORTANT]使用 Oracle Database 12c 時，會有一個新的使用者 **SYSDG**，可用來管理 Oracle Data Guard。如需詳細資訊，請參閱 [Oracle Database 12c 版本中的變更](http://docs.oracle.com/cd/E16655_01/server.121/e10638/release_changes.htm)。

此外，請確定已經在 Machine1 中定義 ORACLE_HOME 環境。如果沒有，可使用 [環境變數] 對話方塊，將其定義為環境變數。若要存取此對話方塊，可在 [控制台] 中按兩下 [系統] 圖示，來啟動 [系統] 公用程式；然後按一下 [進階] 索引標籤並選擇 [環境變數]。按一下 [系統變數] 底下的 [新增] 按鈕來設定環境變數。設定環境變數之後，關閉現有的 Windows 命令提示字元，然後開啟新的命令提示字元。

執行下列陳述式以切換到 Oracle_Home 目錄，例如 C:\\OracleDatabase\\product\\11.2.0\\dbhome_1\\database。

	cd %ORACLE_HOME%\database

接著，使用密碼檔案建立公用程式 [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm) 來建立密碼檔案。在 Machine1 上的同一個 Windows 命令提示字元中，透過將密碼值設定為 **SYS** 的密碼來執行下列命令：

	ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

此命令會在 ORACLE_HOME\\database 目錄中，建立名為 PWDTEST.ora 的密碼檔案。您應該手動將這個檔案複製到 Machine2 中的 %ORACLE_HOME%\\database 目錄。

#### 設定待命重做記錄

接著，您需要設定待命重做記錄，如此一來，當主要機器成為待命機器時便能正確接收重做記錄。在此處預先建立它們，也能夠在待命機器上自動建立待命重做記錄。請務必將待命重做記錄 (SRL) 的大小設定為與線上重做記錄的大小相同。目前待命重做記錄檔的大小必須完全符合目前主要資料庫線上重做記錄檔的大小。

在 Machine1 上的 SQL*PLUS 命令提示字元中執行下列陳述式。v$logfile 是包含重做記錄檔相關資訊的系統檢視。

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE    MEMBER                                                       IS_
	---------- ------- ------- ------------------------------------------------------------ ---
	3         ONLINE  C:<YourLocalFolder>\TEST\REDO03.LOG               NO
	2         ONLINE  C:<YourLocalFolder>\TEST\REDO02.LOG               NO
	1         ONLINE  C:<YourLocalFolder>\TEST\REDO01.LOG               NO
	Next, query the v$log system view, displays log file information from the control file. 
	SQL> select bytes from v$log; 
	BYTES
	----------
	52428800
	52428800
	52428800


請注意，52428800 為 50 MB。

接著，在 SQL*Plus 視窗中執行下列陳述式，將新的待命重做記錄檔群組新增到待命資料庫，並指定可使用 GROUP 子句來識別該群組的編號。使用群組編號，能夠更容易管理待命重做記錄檔群組：

	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
	Database altered.

然後執行下列系統檢視，以列出重做記錄檔的相關資訊。此操作也會驗證已建立待命重做記錄檔群組：

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE MEMBER IS_
	---------- ------- ------- --------------------------------------------- ---
	3         ONLINE C:<YourLocalFolder>\TEST\REDO03.LOG NO
	2         ONLINE C:<YourLocalFolder>\TEST\REDO02.LOG NO
	1         ONLINE C:<YourLocalFolder>\TEST\REDO01.LOG NO
	4         STANDBY C:<YourLocalFolder>\TEST\REDO04.LOG
	5         STANDBY C:<YourLocalFolder>\TEST\REDO05.LOG NO
	6         STANDBY C:<YourLocalFolder>\TEST\REDO06.LOG NO
	6 rows selected.

#### 啟用封存

接著，執行下列陳述式、讓主要資料庫處於 ARCHIVELOG 模式，然後啟用自動封存，藉以啟用封存。您可以藉由掛接資料庫，然後執行 archivelog 命令，來啟用封存記錄模式。

首先，以 sysdba 身分登入。在 Windows 命令提示字元中，執行：

	sqlplus /nolog
	
	connect / as sysdba

然後，在 SQL*Plus 命令提示字元中關閉資料庫：

	SQL> shutdown immediate;
	Database closed.
	Database dismounted.
	ORACLE instance shut down.

接著，執行 startup mount 命令來掛接資料庫。這可確保 Oracle 會將執行個體關聯至指定的資料庫。

	SQL> startup mount;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.

然後，執行：

	SQL> alter database archivelog; 
	Database altered.

接下來，執行 Alter database 陳述式搭配 Open 子句，使資料庫可用於一般用途：

	SQL> alter database open;
	
	Database altered.

#### 設定主要資料庫的初始化參數

若要設定 Data Guard，您需要先在一般的 pfile (文字初始化參數檔案) 上建立並設定待命參數。當 pfile 就緒時，您需要將它轉換成伺服器參數檔案 (SPFILE)。

您可以使用 INIT 中的參數來控制 Data Guard 環境。遵循此教學課程時，您需要更新主要資料庫 INIT.ORA，讓它能夠保有這兩個角色：主要或待命。

	SQL> create pfile from spfile;
	File created.

接著，您需要編輯 pfile 來新增待命參數。若要這樣做，請開啟 %ORACLE_HOME%\\database 位置中的 INITTEST.ORA 檔案。接著，將下列陳述式附加至 INITTEST.ora 檔案。請注意，INIT.ORA 檔案的命名慣例是 INIT<YourDatabaseName>.ORA。
	
	db_name='TEST' 
	db_unique_name='TEST' 
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
	LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
	LOG_ARCHIVE_DEST_STATE_1=ENABLE
	LOG_ARCHIVE_DEST_STATE_2=ENABLE 
	REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE 
	LOG_ARCHIVE_FORMAT=%t_%s_%r.arc 
	LOG_ARCHIVE_MAX_PROCESSES=30
	# Standby role parameters --------------------------------------------------------------------
	fal_server=TEST_STBY
	fal_client=TEST
	standby_file_management=auto
	db_file_name_convert='TEST_STBY','TEST'
	log_file_name_convert='TEST_STBY','TEST'
	# ---------------------------------------------------------------------------------------------


先前的陳述式區塊包含三個重要的設定項目：- **LOG_ARCHIVE_CONFIG...：**您可以使用這個陳述式來定義唯一的資料庫識別碼。 - **LOG_ARCHIVE_DEST_1...：**您可以使用這個陳述式來定義本機封存資料夾位置。我們建議您針對資料庫封存需求建立新的目錄，並使用這個陳述式明確指定本機封存位置，而不是使用 Oracle 的預設資料夾 %ORACLE_HOME%\\database\\archive。- **LOG_ARCHIVE_DEST_2 ....LGWR ASYNC...：**您可以定義非同步的記錄寫入器程序 (LGWR)，來收集交易重做資料，並將它傳輸到待命目的地。DB_UNIQUE_NAME 會在此處指定目的地待命伺服器上資料庫的唯一名稱。

當新的參數檔案就緒之後，您需要從中建立 spfile。

首先，關閉資料庫：

	SQL> shutdown immediate;
	
	Database closed.
	
	Database dismounted.
	
	ORACLE instance shut down.

接下來，執行 startup nomount 命令，如下所示：

	SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes

現在建立 spfile：

	SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';

	File created.

然後，關閉資料庫：

	SQL> shutdown immediate;
	
	ORA-01507: database not mounted

接著，使用 startup 命令來啟動執行個體：

	SQL> startup;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.
	Database opened.

##建立實體的待命資料庫
本節著重於您必須在 Machine2 中執行以準備實體待命資料庫的步驟。

首先，您需要透過 Azure 入口網站，使用遠端桌面功能連至 Machine2。

然後，在待命伺服器 (Machine2) 上，建立待命資料庫所需的所有資料夾，例如 C:\\<YourLocalFolder>\\TEST。遵循此教學課程的同時，請確定資料夾結構符合 Machine1 上的資料夾結構，以保留所有必要的檔案，例如 controlfile、datafiles、redologfiles、udump、bdump 和 cdump 檔案。此外，在 Machine2 中定義 ORACLE_HOME 和 ORACLE_BASE 環境變數。如果沒有，可使用 [環境變數] 對話方塊，將其定義為環境變數。若要存取此對話方塊，可在 [控制台] 中按兩下 [系統] 圖示，來啟動 [系統] 公用程式；然後按一下 [進階] 索引標籤並選擇 [環境變數]。按一下 [系統變數] 底下的 [新增] 按鈕來設定環境變數。設定環境變數之後，您需要關閉現有的 Windows 命令提示字元，然後開啟新的命令提示字元來查看變更。

接著，遵循下列步驟：

1. 準備待命資料庫的初始設定參數檔案

2. 設定接聽程式和 tnsnames，以支援主要和待命機器上的資料庫

	1. 在這兩部伺服器上設定 listener.ora，以保存這兩個資料庫的項目

	2. 在主要和待命虛擬機器上設定 tnsnames.ora，以保存主要和待命資料庫的項目

	3. 啟動接聽程式，並檢查已在這兩部虛擬機器上對這兩個服務進行 tnsping。

3. 以 NoMount 狀態啟動待命執行個體

4. 使用 RMAN 來複製資料庫複製和建立待命資料庫

5. 在受管理的復原模式中啟動實體的待命資料庫

6. 驗證實體的待命資料庫

### 1.準備待命資料庫的初始設定參數檔案

本節示範如何準備待命資料庫的初始化參數檔案。若要這樣做，請先手動將 INITTEST.ORA 檔案從 Machine1 複製到 Machine2。您應該可以在這兩部機器的 %ORACLE_HOME%\\database 資料夾中看到 INITTEST.ORA 檔案。然後，修改 Machine2 中的 INITTEST.ora 檔案，以下列指定的方式來設定它，使其適用於待命角色：
	
	db_name='TEST'
	db_unique_name='TEST_STBY'
	db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
	db_file_name_convert=’TEST’,’TEST_STBY’
	log_file_name_convert='TEST','TEST_STBY'
	
	
	job_queue_processes=10
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
	LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) 
	LOG_ARCHIVE_DEST_STATE_1='ENABLE'
	LOG_ARCHIVE_DEST_STATE_2='ENABLE'
	LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
	LOG_ARCHIVE_MAX_PROCESSES=30


先前的陳述式區塊包含兩個重要的設定項目：

-	***.LOG_ARCHIVE_DEST_1：**您需要在 Machine2 中手動建立 c:\\OracleDatabase\\TEST_STBY\\archives 資料夾。
-	***.LOG_ARCHIVE_DEST_2：** 這是選擇性步驟。當主要機器處於維護模式且待命機器成為主要資料庫時，您可以視需要來設定這個步驟。

接著，需要啟動待命執行個體。在待命資料庫伺服器的 Windows 命令提示字元中，輸入下列命令，藉由建立新的 Windows 服務來建立 Oracle 執行個體：

	oradim -NEW -SID TEST_STBY -STARTMODE MANUAL

請注意，**Oradim** 命令會建立 Oracle 執行個體，但不會啟動它。您可以在 C:\\OracleDatabase\\product\\11.2.0\\dbhome_1\\BIN 目錄中找到它。

##設定接聽程式和 tnsnames，以支援主要和待命機器上的資料庫
建立待命資料庫之前，您必須確定在您的設定中主要和待命資料庫可以彼此通訊。若要這樣做，您需要手動或使用網路設定公用程式 NETCA 來設定接聽程式和 TNSNames。當您使用復原管理員公用程式 (RMAN) 時，這是必要的工作。

### 在這兩部伺服器上設定 listener.ora，以保存這兩個資料庫的項目

以下列指定的方式，使用遠端桌面功能連至 Machine1 並編輯 listener.ora 檔案。當您編輯 listener.ora 檔案時，請務必在同一個資料行中對齊左右括號。您可以在下列資料夾中找到 listener.ora 檔案：c:\\OracleDatabase\\product\\11.2.0\\dbhome_1\\NETWORK\\ADMIN\\。

	# listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora
	
	# Generated by Oracle configuration tools.
	
	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )
	
	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )

接下來，使用遠端桌面功能連至 Machine2，並編輯 listener.ora 檔案，如下所示：# listener.ora Network Configuration File: C:\\OracleDatabase\\product\\11.2.0\\dbhome_1\\network\\admin\\listener.ora
	
	# Generated by Oracle configuration tools.
	
	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test_stby)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )
	
	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )


### 在主要和待命虛擬機器上設定 tnsnames.ora，以保存主要和待命資料庫的項目

以下列指定的方式，使用遠端桌面功能連至 Machine1 並編輯 tnsnames.ora 檔案。您可以在下列資料夾中找到 tnsnames.ora 檔案：c:\\OracleDatabase\\product\\11.2.0\\dbhome_1\\NETWORK\\ADMIN\\。

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )
	
	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )

使用遠端桌面功能連至 Machine2 並編輯 tnsnames.ora 檔案，如下所示：
	
	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )
	
	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )


### 啟動接聽程式，並檢查已在這兩部虛擬機器上對這兩個服務進行 tnsping。

在主要和待命的虛擬機器中開啟新的 Windows 命令提示字元，並執行下列陳述式：

	C:\Users\DBAdmin>tnsping test
	
	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test)))
	OK (0 msec)
	

	C:\Users\DBAdmin>tnsping test_stby
	
	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test_stby)))
	OK (260 msec)


##以 NoMount 狀態啟動待命執行個體
您需要設定環境，以支援待命虛擬機器 (MACHINE2) 上的待命資料庫。

首先，手動將密碼檔案從主要機器 (Machine1) 複製到待命機器 (Machine2)。請務必讓這兩部機器上的 **sys** 密碼完全相同。

然後，在 Machine2 中開啟 Windows 命令提示字元，並設定環境變數以指向待命資料庫，如下所示：

	SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
	SET ORACLE_SID=TEST_STBY

接下來，在 nomount 狀態中啟動待命資料庫，然後產生 spfile。

啟動資料庫：

	SQL>shutdown immediate;
	
	SQL>startup nomount
	ORACLE instance started.
	
	Total System Global Area  747417600 bytes
	Fixed Size                  2179496 bytes
	Variable Size             473960024 bytes
	Database Buffers          264241152 bytes
	Redo Buffers                7036928 bytes


##使用 RMAN 來複製資料庫複製和建立待命資料庫
您可以使用復原管理員公用程式 (RMAN)，來取得主要資料庫的所有備份複本以建立實體待命資料庫。

使用遠端桌面功能連至待命虛擬機器 (MACHINE2)，然後透過為 TARGET (主要資料庫，Machine1) 和 AUXILLARY (待命資料庫，Machine2) 執行個體指定完整的連線字串來執行 RMAN 公用程式。

>[AZURE.IMPORTANT]請勿使用作業系統驗證，因為待命伺服器機器中還沒有任何資料庫。

	C:> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY
	
	RMAN>DUPLICATE TARGET DATABASE
	  FOR STANDBY
	  FROM ACTIVE DATABASE
	  DORECOVER
	    NOFILENAMECHECK;

##在受管理的復原模式中啟動實體的待命資料庫
本教學課程示範如何建立實體的待命資料庫。如需建立邏輯待命資料庫的相關資訊，請參閱 Oracle 文件。

開啟 SQL*Plus 命令提示字元，並在待命虛擬機器或伺服器 (MACHINE2) 上啟用 Data Guard，如下所示：

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

當您在**掛接**模式中開啟待命資料庫時，封存記錄傳送會繼續，而受管理的復原程序會繼續在待命資料庫上套用記錄。這確保待命資料庫仍會與主要資料庫維持最新狀態。請注意，待命資料庫在這段期間無法針對報告用途進行存取。

當您在**唯讀**模式中開啟待命資料庫時，封存記錄傳送會繼續。但是，受管理的復原程序會停止。這會使待命資料庫變得越來越過時，直到受管理的復原程序繼續執行為止。您可以在這段期間基於報告目的來存取待命資料庫，但是資料可能不會反映最新的變更。

通常，我們會建議您讓待命資料庫保持為**掛接**模式，以便在主要資料庫失敗的情況下，使待命資料庫中的資料維持最新狀態。不過，您可以視應用程式需求而定，基於報告目的將待命資料庫保留為**唯讀**模式。下列步驟示範如何使用 SQL*Plus，在唯讀模式中啟用 Data Guard：

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE OPEN READ ONLY;


##驗證實體的待命資料庫
本節示範如何以系統管理員身分驗證高可用性設定。

開啟 SQL*Plus 命令提示字元視窗，並檢查待命虛擬機器 (Machine2) 上的封存重做記錄：

	SQL> show parameters db_unique_name;
	
	NAME                                TYPE       VALUE
	------------------------------------ ----------- ------------------------------
	db_unique_name                      string     TEST_STBY
	
	SQL> SELECT NAME FROM V$DATABASE
	
	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;
	
	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	45                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   NO
	47                    23-FEB-14   23-FEB-14   NO

開啟 SQL*Plus 命令提示字元視窗，並切換主要機器 (Machine1) 上的 logfile：

	SQL> alter system switch logfile; 
	System altered.
	
	SQL> archive log list
	Database log mode              Archive Mode
	Automatic archival             Enabled
	Archive destination            C:\OracleDatabase\archive
	Oldest online log sequence     69
	Next log sequence to archive   71
	Current log sequence           71

檢查待命虛擬機器 (Machine2) 上的封存重做記錄：

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;
	
	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   YES
	48                    23-FEB-14   23-FEB-14   YES
	
	49                    23-FEB-14   23-FEB-14   YES
	50                    23-FEB-14   23-FEB-14   IN-MEMORY

檢查待命虛擬機器 (Machine2) 上的任何間距：

	SQL> SELECT * FROM V$ARCHIVE_GAP;
	no rows selected.

另一種驗證方法是可以容錯移轉至待命資料庫，然後測試它是否能夠容錯回復到主要資料庫。若要啟動待命資料庫以做為主要資料庫，請使用下列陳述式：

	SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
	SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

如果您未在原始的主要資料庫上啟用快閃回復功能，建議您卸除原始的主要資料庫，並重新建立為待命資料庫。

我們建議您啟用在主要資料庫與待命資料庫上啟用快閃回復資料庫的功能。發生容錯移轉時，主要資料庫可以快閃回復到容錯移轉之前的時間，並快速轉換為待命資料庫。

##其他資源
[適用於 Azure 的 Oracle 虛擬機器映像](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=July15_HO2-->