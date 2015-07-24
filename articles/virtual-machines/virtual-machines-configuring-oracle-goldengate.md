<properties title="Configuring Oracle GoldenGate for Azure" pageTitle="設定適用於 Azure 的 Oracle GoldenGate" description="在 Azure 虛擬機器上逐步執行設定和實作高可用性和嚴重損壞修復之 Oracle GoldenGate 的教學課程。" services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#設定適用於 Azure 的 Oracle GoldenGate
本教學課程示範如何設定適用於 Azure 虛擬機器環境的 Oracle GoldenGate，以取得高可用性並進行嚴重損壞修復 。本教學課程著重於非 RAC Oracle 資料庫的[雙向複寫](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm)，而且要求這兩個站台必須是作用中的站台。

Oracle GoldenGate 支援資料分佈和資料整合。它讓您能夠透過 Oracle 設定資料分佈和資料同步處理解決方案 (Oracle 複寫設定)，並提供彈性的高可用性解決方案。Oracle GoldenGate 利用它的複寫功能來補充 Oracle Data Guard，讓資訊可分佈於整個企業，並進行零停機時間的升級和移轉。如需詳細資訊，請參閱[搭配使用 Oracle GoldenGate 與 Oracle Data Guard](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm)。

Oracle GoldenGate 包含下列主要元件：擷取、資料幫浦、複寫、軌跡或解壓縮檔案、檢查點、管理員及收集器。若要在兩個站台之間進行雙向複寫，您需要在這兩個站台上建立並啟動所有元件。如需 Oracle GoldenGate 架構的詳細資訊，請參閱 [Oracle GoldenGate 指南](http://docs.oracle.com/goldengate/1212/gg-winux/index.html)。

本教學課程假設您已經具備 Oracle 資料庫高可用性和嚴重損壞修復概念的理論和實務知識，以及 [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html)。如需詳細資訊，請參閱 [Oracle 網站](http://www.oracle.com/technetwork/database/features/availability/index.html)。

此外，本教學課程假設您已經實作下列必要條件：

- 您已經檢閱過 [Oracle 虛擬機器映像 - 其他考量](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)主題中的＜高可用性和嚴重損壞修復考量＞一節。請注意，Azure 支援獨立的 Oracle 資料庫執行個體，但目前不支援 Oracle Real Application Cluster (Oracle RAC)。

- 您已經從 [Oracle 下載](http://www.oracle.com/us/downloads/index.html)網站下載 Oracle GoldenGate 軟體。您已選取 Oracle Fusion Middleware 產品套件 - 資料整合。然後，您針對 Oracle 11g 資料庫，在適用於 Microsoft Windows x64 (64 位元) 的 Oracle v11.2.1 媒體套件上選取了 Oracle GoldenGate。接著，在 Windows 2008 (64 位元) 上下載適用於 Oracle 11g (64 位元) 的 Oracle GoldenGate V11.2.1.0.3。

- 您已經使用 Windows Server 上提供 Oracle Enterprise Edition 映像的平台，在 Azure 中建立兩部虛擬機器 (VM)。如需相關資訊，請參閱[在 Azure 中建立 Oracle Database 12c 虛擬機器](#z3dc8d3c097cf414e9048f7a89c026f80)和 [Azure 虛擬機器](http://azure.microsoft.com/documentation/services/virtual-machines/)。請確定虛擬機器都位於[相同的雲端服務](virtual-machines-load-balance.md)和相同的[虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/)中，以確保它們可以透過永續的私人 IP 位址互相存取。

- 您已經在 Azure 入口網站上，針對站台 A 將虛擬機器名稱設定為 “MachineGG1”，並針對站台 B 設定為 “MachineGG2”。

- 您已經在站台 A 上建立測試資料庫 “TestGG1”，並在站台 B 上建立 “TestGG2”。

- 您使用 Administrators 群組的成員身分或 **ORA_DBA** 群組的成員身分來登入 Windows Server。

在本教學課程中，您將：

1. 在站台 A 和站台 B 上設定資料庫  

	1. 執行初始資料載入
	
2. 準備站台 A 和站台 B 以進行資料庫複寫

3. 建立所有必要的物件來支援 DDL 複寫

4. 在站台 A 和站台 B 上設定 GoldenGate 管理員

5. 在站台 A 和站台 B 上建立擷取群組及資料幫浦程序

	1. 在站台 A 上建立擷取及資料幫浦程序

	2. 在站台 B 上建立 GoldenGate 檢查點資料表

	3. 在站台 B 上加入 REPLICAT

	4. 在站台 B 上建立擷取及資料幫浦程序

	5. 在站台 A 上建立 GoldenGate 檢查點資料表

	6. 在站台 A 上加入 REPLICAT

	7. 在站台 A 和站台 B 上加入 trandata

	8. 在站台 A 上啟動擷取及資料幫浦程序

	9. 在站台 B 上啟動擷取及資料幫浦程序

	10. 在站台 A 上啟動 REPLICAT 程序

	11. 在站台 B 上啟動 REPLICAT 程序

6. 確認雙向複寫程序

>[AZURE.IMPORTANT]本教學課程已根據下列軟體設定進行設定和測試：
>
>| | **站台 A 資料庫** | **站台 B 資料庫** |
>|------------------------|----------------------------------|----------------------------------|
>| **Oracle 版本** | Oracle11g 版本 2 - (11.2.0.1) | Oracle11g 版本 2 - (11.2.0.1) |
>| **機器名稱** | MachineGG1 | MachineGG2 |
>| **作業系統** | Windows 2008 R2 | Windows 2008 R2 |
>| **Oracle SID** | TESTGG1 | TESTGG2 |
>| **複寫結構描述** | SCOTT | SCOTT |

針對 Oracle 資料庫和 Oracle GoldenGate 的後續版本，可能會有一些您需要實作的其他變更。如需最新的版本特定資訊，請參閱 Oracle 網站上的 [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) 和 [Oracle 資料庫](http://www.oracle.com/us/corporate/features/database-12c/index.html)文件。例如，針對版本 11.2.0.4 來源資料庫及更新版本，擷取 DDL 是透過記錄採礦伺服器以非同步方式來執行，並且不需要安裝任何特定的觸發程序、資料表或其他資料庫物件。您可以執行 Oracle GoldenGate 升級，而不需停止使用者應用程式。若擷取是處於與 Oracle 11g 來源資料庫 (版本早於 11.2.0.4) 整合的模式中，就需要使用 DDL 觸發程序和支援物件。如需詳細指引，請參閱[安裝和設定適用於 Oracle 資料庫的 Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf)。

##1.在站台 A 和站台 B 上設定資料庫
本節說明如何在站台 A 和站台 B 上執行資料庫的必要條件。您必須在下列兩個站台上執行本節的所有步驟：站台 A 和站台 B。

首先，透過管理入口網站，使用遠端桌面功能連至站台 A 和站台 B。開啟 Windows 命令提示字元，並建立適用於 Oracle GoldenGate 安裝檔案的主目錄：

	mkdir C:\OracleGG

接著解壓縮，並將 Oracle GoldenGate 軟體安裝於此資料夾。完成此步驟之後，您可以執行下列命令來起始 GoldenGate 軟體命令直譯器 (GGSCI)：

	C:\OracleGG.\ggsci

您可以使用 [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) 來執行數個命令，以設定、控制和監視 Oracle GoldenGate。

接下來，執行下列命令，從安裝套件建立所有子資料夾：

	GGSCI (Hostname) 1> CREATE SUBDIRS

執行下列命令以結束 GGSCI 命令提示字元：

	GGSCI (Hostname) 1> EXIT

然後，您需要建立資料庫使用者，以供 Oracle GoldenGate 管理員、擷取和複寫程序使用。請注意，您可以針對每個程序建立個別的使用者，或只設定一個常見的使用者。在本教學課程中，我們會建立一個名為 ggate 的使用者。然後，授與該使用者必要的權限。請注意，您必須在站台 A 和站台 B上執行下列操作。

在站台 A 和站台 B 上，使用 **SYSDBA** 來開啟 SQL*Plus 命令視窗，以具備資料庫管理員權限，例如：

	Enter username: / as sysdba

然後執行：

	SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata<DBNAME><DBNAME>ggs_data01.dbf' size 200m; 
	SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
	      grant connect, resource to ggate;
	      grant select any dictionary, select any table to ggate;
	      grant create table to ggate;
	      grant flashback any table to ggate;
	      grant execute on dbms_flashback to ggate;
	      grant execute on utl_file to ggate;
	      grant create any table to ggate;
	      grant insert any table to ggate;
	      grant update any table to ggate;
	      grant delete any table to ggate;
	      grant drop any table to ggate;

接著，在站台 A 和站台 B 上的 %ORACLE_HOME%\\database 資料夾中找到 INIT<DatabaseSID>.ORA 檔案，並將下列資料庫參數附加到 INITTEST.ora：

	UNDO_MANAGEMENT=AUTO
	UNDO_RETENTION=86400

如需所有 Oracle GoldenGate GGSCI 命令的完整清單，請參閱[適用於 Windows 的 Oracle GoldenGate 參考資訊 ](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm)。

### 執行初始資料載入

您可以依照下列幾種方法，在資料庫中執行初始資料載入。例如，您可以使用 [Oracle GoldenGate 直接載入](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm)或一般的匯出和匯入公用程式，將資料表資料從站台 A 匯出至站台 B。

為了示範 Oracle GoldenGate 複寫程序，本教學課程將示範如何使用下列命令，在站台 A 和站台 B 上建立資料表。

首先，開啟 SQL*Plus 命令視窗，並執行下列命令，在站台 A 和站台 B 資料庫上建立一個庫存資料表：
	
	create table scott.inventory
	(prod_id number,
	prod_category varchar2(20),
	qty_in_stock number,
	last_dml timestamp default systimestamp);

接下來，將條件約束加入站台 A 和站台 B 資料庫上新建立的資料表：

	alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

然後，授與使用者 ggate 在站台 A 和站台 B 上新庫存資料表的所有權限：

	grant all on scott.inventory to ggate;

接下來，在新建立的資料表上建立並啟用資料庫觸發程序 INVENTORY_CDR_TRG，藉此確定如果使用者不是 ggate，就會記錄新資料表的所有交易。在站台 A 和站台 B 上執行此操作。

	CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
	BEFORE UPDATE
	ON SCOTT.INVENTORY
	REFERENCING NEW AS New OLD AS Old
	FOR EACH ROW
	BEGIN
	IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
	THEN
	:NEW.LAST_DML := SYSTIMESTAMP;
	END IF;
	END;
	/ 


##2.準備站台 A 和站台 B 以進行資料庫複寫
本節說明如何準備站台 A 和站台 B 以進行資料庫複寫。您必須在下列兩個站台上執行本節的所有步驟：站台 A 和站台 B。

首先，透過 Azure 入口網站，使用遠端桌面功能連至站台 A 和站台 B。使用 SQL*Plus 命令視窗，將資料庫切換到 archivelog 模式：
	
	sql>shutdown immediate 
	sql>startup mount 
	sql>alter database archivelog; 
	sql>alter database open;


然後，啟用最小的[補充記錄](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm)，如下所示：

	SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

接下來，準備資料庫以支援 DDL (資料定義語言) 複寫：

	SQL> alter system set recyclebin=off scope=spfile;

然後，關閉並重新啟動資料庫：

	sql>shutdown immediate 
	sql>startup


##3.建立所有必要的物件來支援 DDL 複寫
本節列出您用來建立所有必要物件以支援 DDL 複寫所需的指令碼。您需要在站台 A 和站台 B 上執行本節中指定的指令碼。

開啟 Windows 命令提示字元並巡覽至 Oracle GoldenGate 資料夾，例如 C:\\OracleGG。在站台 A 和站台 B 上，使用資料庫管理員權限來啟動 SQL*Plus 命令提示字元，例如使用 **SYSDBA**：

然後，執行下列指令碼：
	
	SQL> @marker_setup.sql  
	Enter GoldenGate schema name: ggate
	SQL> @ddl_setup.sql  
	Enter GoldenGate schema name: ggate
	SQL> @role_setup.sql 
	Enter GoldenGate schema name: ggate
	SQL> grant ggs_ggsuser_role to ggate;
	 Grant succeeded.
	 SQL> @ddl_enable
	 Trigger altered.
	 SQL> @ddl_pin ggate


Oracle GoldenGate 工具需要資料表層級的登入，以支援 DDL (資料定義語言)。這就是為什麼要使用 ADD TRANDATA 命令來啟用資料表層級的補充記錄。開啟 Oracle GoldenGate 命令直譯器視窗、登入資料庫，然後執行 ADD TRANDATA 命令：

	GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

	GGSCI(Hostname) 6> add trandata scott.inventory

##4.在站台 A 和站台 B 上設定 GoldenGate 管理員
Oracle GoldenGate 管理員會執行一些像是啟動其他 GoldenGate 程序、追蹤記錄檔案管理和報告等功能。

您必須在站台 A 和站台 B 上設定 Oracle GoldenGate 管理員程序。若要這樣做，請在站台 A 和站台 B 上執行下列步驟。

開啟 Windows 命令視窗，並起始 Oracle GoldenGate 命令直譯器：

	cd C:\OracleGG\
	c:\OracleGG>ggsci
	Oracle GoldenGate Command Interpreter for Oracle
	Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
	Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
	Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


將 GGSCI 工作階段記錄到資料庫，如此您就能執行會影響資料庫的命令：

	GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

顯示狀態，以及延遲 (如果適當) 系統上所有的管理員、擷取及複寫程序：

	GGSCI (HostName) 2> info all
	Program     Status      Group       Lag           Time Since Chkpt
	MANAGER     STOPPED

使用 EDIT PARAMS 命令來開啟參數檔案，然後附加下列資訊：

	GGSCI (HostName) 3> edit params mgr
	PORT 7809
	USERID ggate, PASSWORD ggate
	PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

顯示狀態，以及延遲 (如果適當) 系統上所有的管理員、擷取及複寫程序：

	GGSCI (HostName) 46> info all
	Program     Status      Group       Lag           Time Since Chkpt
	MANAGER     STOPPED

將 GGSCI 工作階段記錄到資料庫，如此您就能執行會影響資料庫的命令：

	GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

	Successfully logged into database.

啟動管理員程序：

	GGSCI (HostName) 48> start manager
	Manager started.

##5.在站台 A 和站台 B 上建立擷取群組及資料幫浦程序

###在站台 A 上建立擷取及資料幫浦程序

您需要在站台 A 和站台 B 上建立擷取和資料幫浦程序。透過管理入口網站，使用遠端桌面功能連至站台 A 和站台 B。開啟 GGSCI 命令直譯器視窗。在站台 A 上執行下列命令：

	GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
	EXTRACT added.
	GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
	EXTTRAIL added.
	GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
	EXTRACT added.
	GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
	RMTTRAIL added.

使用 EDIT PARAMS 命令來開啟參數檔案，然後附加下列資訊：GGSCI (MachineGG1) 18> edit params ext1 EXTRACT ext1 USERID ggate, PASSWORD ggate EXTTRAIL C:\\OracleGG\\dirdat\\aa TRANLOGOPTIONS EXCLUDEUSER ggate TABLE scott.inventory, GETBEFORECOLS ( ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml), ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

使用 EDIT PARAMS 命令來開啟參數檔案，然後附加下列資訊：

	GGSCI (MachineGG1) 15> edit params dpump1
	EXTRACT dpump1
	 USERID ggate, PASSWORD ggate
	 RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
	 RMTTRAIL C:\OracleGG\dirdat\ab
	 PASSTHRU
	 TABLE scott.inventory;

###在站台 B 上建立 GoldenGate 檢查點資料表

接下來，您必須在站台 B 上加入檢查點資料表。若要這樣做，您必須開啟 GoldenGate 命令直譯器視窗，然後執行：

	C:\OracleGG\ggsci
	GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

然後，將檢查點資料表加入資料庫，其中 ggate 為擁有者：
	
	GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
	Successfully created checkpoint table ggate.checkpointtable.

在目標伺服器上，將檢查點資料表的名稱加入 GLOBALS 檔案，即此步驟中的站台 B。在站台 B上編輯 GLOBALS 檔案：

	GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

然後，將 CHECKPOINTTABLE 參數附加至現有的 GLOBALS 檔案：

	GGSCHEMA ggate
	CHECKPOINTTABLE ggate.checkpointtable

最後一個步驟是儲存並關閉 GLOBALS 參數檔案。


###在站台 B 上加入 REPLICAT
本節說明如何在站台 B 上加入 REPLICAT 程序 "REP2"。
 
使用 ADD REPLICAT 命令，在站台 B 上建立複寫群組：

	GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

使用 EDIT PARAMS 命令來開啟參數檔案，然後附加下列資訊：

	GGSCI (MachineGG2) 10> edit params rep2
	REPLICAT rep2
	ASSUMETARGETDEFS
	USERID ggate, PASSWORD ggate
	DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
	MAP scott.inventory, TARGET scott.inventory;

###在站台 B 上建立擷取及資料幫浦程序

本節說明如何在站台 B上建立新的擷取程序 “EXT2” 和新的資料幫浦程序 “DPUMP2”：

	GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
	 EXTRACT added.
	GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
	 EXTTRAIL added.
	GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
	 EXTRACT added.
	GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
	 RMTTRAIL added.

使用 EDIT PARAMS 命令來開啟參數檔案，然後附加下列資訊：

	GGSCI (MachineGG2) 31> edit params ext2
	EXTRACT ext2
	USERID ggate, PASSWORD ggate
	EXTTRAIL C:\OracleGG\dirdat\ac
	TRANLOGOPTIONS EXCLUDEUSER ggate
	TABLE scott.inventory,
	GETBEFORECOLS (
	ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
	ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

使用 EDIT PARAMS 命令來開啟參數檔案，然後附加下列資訊：

	GGSCI (MachineGG2) 32> edit params dpump2
	EXTRACT dpump2
	USERID ggate, PASSWORD ggate
	RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
	RMTTRAIL C:\OracleGG\dirdat\ad
	PASSTHRU
	TABLE scott.inventory;

###在站台 A 上建立 GoldenGate 檢查點資料表

開啟 Oracle GoldenGate 命令直譯器視窗，並建立檢查點資料表：

	GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

	GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

	Successfully created checkpoint table ggate.checkpointtable.

您也需要將檢查點資料表的名稱加入站台 A 上的 GLOBALS 檔案。

開啟 Oracle GoldenGate 命令直譯器視窗，並在站台 A 上編輯 GLOBALS 檔案：

	GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
	Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
	GGSCHEMA ggate
	CHECKPOINTTABLE ggate.checkpointtable

儲存並關閉 GLOBALS 參數檔案。

###在站台 A 上加入 REPLICAT

本節說明如何在站台 A 上加入 REPLICAT 程序 "REP1"。

下列命令會使用軌跡的名稱來建立複寫群組 rep1，以及相關聯的 checkpointtable。

	GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
	 REPLICAT added.

使用 EDIT PARAMS 命令來開啟參數檔案，然後附加下列資訊：

	GGSCI (MachineGG1) 10> edit params rep1
	REPLICAT rep1
	ASSUMETARGETDEFS
	USERID ggate, PASSWORD ggate
	DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
	MAP scott.inventory, TARGET scott.inventory;

### 在站台 A 和站台 B 上加入 trandata

使用 ADD TRANDATA 命令來啟用資料表層級的補充記錄。開啟 Oracle GoldenGate 命令直譯器視窗、登入資料庫，然後執行 ADD TRANDATA 命令。

使用遠端桌面功能連至 MachineGG1、開啟 Oracle GoldenGate 命令直譯器，然後執行：

	GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	GGSCI (MachineGG1) 13> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.
		
使用遠端桌面功能連至 MachineGG2、開啟 Oracle GoldenGate 命令直譯器，然後執行：

	GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

顯示資料表層級補充記錄狀態的相關資訊：

	GGSCI (MachineGG2) 15> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###在站台 A 和站台 B 上加入 trandata

使用 ADD TRANDATA 命令來啟用資料表層級的補充記錄。開啟 Oracle GoldenGate 命令直譯器視窗、登入資料庫，然後執行 ADD TRANDATA 命令。

使用遠端桌面功能連至 MachineGG1、開啟 Oracle GoldenGate 命令直譯器，然後執行：

	GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	GGSCI (MachineGG1) 13> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

使用遠端桌面功能連至 MachineGG2、開啟 Oracle GoldenGate 命令直譯器，然後執行：

	GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
	Display information about the state of table-level supplemental logging:
	GGSCI (MachineGG2) 15> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###在站台 A 上啟動擷取及資料幫浦程序

在站台 A 上啟動擷取程序 ext1：

	GGSCI (MachineGG1) 31> start extract ext1
	Sending START request to MANAGER …
	EXTRACT EXT1 starting

在站台 A 上啟動資料幫浦程序 dpump1：

	GGSCI (MachineGG1) 23> start extract dpump1
	Sending START request to MANAGER …
	EXTRACT DPUMP1 starting
顯示擷取群組 ext1 的相關資訊：GGSCI (MachineGG1) 32> info extract ext1 EXTRACT EXT1 Last Started 2013-11-25 08:03 Status RUNNING Checkpoint Lag 00:00:00 (updated 00:00:02 ago) Log Read Checkpoint Oracle Redo Logs 2013-11-25 08:03:18 Seqno 6, RBA 3230720 SCN 0.1074371 (1074371)

顯示狀態，以及延遲 (如果適當) 系統上所有的管理員、擷取及複寫程序：

	GGSCI (MachineGG1) 16> info all
	Program     Status      Group       Lag at Chkpt  Time Since Chkpt
	
	MANAGER     RUNNING
	EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
	EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###在站台 B 上啟動擷取及資料幫浦程序

在站台 B上啟動擷取程序 ext2：

	GGSCI (MachineGG2) 22> start extract ext2
	Sending START request to MANAGER …
	EXTRACT EXT2 starting

在站台 B上啟動資料幫浦程序 dpump2：

	GGSCI (MachineGG2) 23> start extract dpump2
	Sending START request to MANAGER …
	EXTRACT DPUMP2 starting

顯示狀態，以及延遲 (如果適當) 系統上所有的管理員、擷取及複寫程序：

	GGSCI (ActiveGG2orcldb) 6> info all
	Program     Status      Group       Lag at Chkpt  Time Since Chkpt
	
	MANAGER     RUNNING
	EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
	EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### 在站台 A 上啟動 REPLICAT 程序

本節說明如何在站台 A 上啟動 REPLICAT 程序 "REP1"。

在站台 A 上啟動複寫程序：

	GGSCI (MachineGG1) 38> start replicat rep1
	Sending START request to MANAGER …
	REPLICAT REP1 starting

顯示複寫群組的狀態：

	GGSCI (MachineGG1) 39> status replicat rep1
	 REPLICAT REP1: RUNNING

###在站台 B 上啟動 REPLICAT 程序

本節說明如何在站台 B 上啟動 REPLICAT 程序 "REP2"。

在站台 B 上啟動複寫程序：

	GGSCI (MachineGG2) 26> start replicat rep2
	Sending START request to MANAGER …
	REPLICAT REP2 starting

顯示複寫群組的狀態：

	GGSCI (MachineGG2) 27> status replicat rep2
	REPLICAT REP2: RUNNING

##6.確認雙向複寫程序

若要確認 Oracle GoldenGate 設定，請在站台 A上的資料庫中插入資料列。使用遠端桌面功能連至站台 A。開啟 SQL*Plus 命令視窗，然後執行：SQL> select name from v$database;
	
	NAME
	———
	TESTGG
	
	SQL> insert into inventory values  (100,’TV’,100,sysdate);
	
	1 row created.
	
	SQL> commit;
	
	Commit complete.

接著，檢查是否已在站台 B上複寫該資料列。若要這樣做，請使用遠端桌面功能連至站台 B。開啟 SQL Plus 視窗，然後執行：

	SQL> select name from v$database;
	
	NAME
	———
	TESTGG
	
	SQL> select * from inventory;
	
	PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
	———- ——————– ———— ———
	100 TV 100 22-MAR-13

在站台 B 上插入新記錄：

	SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
	1 row created.
	
	SQL> commit;
	
	Commit complete.

使用遠端桌面功能連至站台 A，並檢查複寫是否已經執行：

	SQL> select * from inventory;
	
	PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
	———- ——————– ———— ———
	100 TV 100 22-MAR-13
	101 DVD 10 22-MAR-13

##其他資源
[適用於 Azure 的 Oracle 虛擬機器映像](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=July15_HO2-->