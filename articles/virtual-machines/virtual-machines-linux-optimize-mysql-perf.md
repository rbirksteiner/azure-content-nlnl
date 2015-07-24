<properties 
	pageTitle="在 Azure Linux VM 上最佳化 MySQL 效能" 
	description="了解如何最佳化在執行 Linux 之 Azure 虛擬機器 (VM) 上執行的 MySQL。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="NingKuang" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/21/2015" 
	ms.author="ningk"/>

#在 Azure Linux VM 上最佳化 MySQL 效能 

有許多因素會影響 Azure 上的 MySQL 效能，均與虛擬硬體選取和軟體設定有關。本文著重於透過儲存體、系統和資料庫設定最佳化效能。

##在 Azure 虛擬機器上利用 RAID 
儲存體是影響雲端環境中的資料庫效能的關鍵因素。相較於單一磁碟，RAID 可透過並行提供更快速的存取。如需詳細資訊，請參閱[標準 RAID 層級](http://en.wikipedia.org/wiki/Standard_RAID_levels)。

Azure 中的磁碟 I/O 輸送量和 I/O 回應時間可透過 RAID 大幅改進。我們的實驗室測試顯示︰當 RAID 磁碟數目增加一倍 (從 2 到 4、4 到 8 等) 時，磁碟 I/O 輸送量可以增加一倍，而平均 I/O 回應時間則可減少一半。如需詳細資訊，請參閱[附錄 A](#AppendixA)。

除了磁碟 I/O 以外，MySQL 效能會在您提高 RAID 層級時改善。如需詳細資訊，請參閱[附錄 B](#AppendixB)。

您也可能要考慮區塊大小。通常當您有較大的區塊大小時，您的額外負荷會比較低，尤其是大量寫入時。不過，當區塊大小太大時，可能會增加額外的負荷，而且無法利用 RAID。目前的預設大小為 512 KB，經證明是大多數一般實際執行環境的最佳大小。如需詳細資訊，請參閱[附錄 C](#AppendixC)。

請注意，您可針對不同虛擬機器類型新增的磁碟數目會有所限制。[Azure 的虛擬機器和雲端服務大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)中有這些限制的詳細說明。雖然您可以選擇設定具有較少磁碟的 RAID，但您需要連接 4 個資料磁碟，才能遵循本文中的 RAID 4 範例。

本文假設您已經建立 Linux 虛擬機器並已安裝及設定 MYSQL 。如需開始使用的詳細資訊，請參閱「如何在 Azure 上安裝 MySQL」。
  
###在 Azure 上設定 RAID
下列步驟說明如何使用 Windows Azure 管理入口網站，在 Azure 上建立 RAID。您也可以使用 Windows PowerShell 指令碼設定 RAID。在此範例中，我們將設定具有 4 個磁碟的 RAID 0。

####步驟 1：將資料磁碟新增至您的虛擬機器  

在 Azure 管理入口網站的 [虛擬機器] 頁面上，按一下您要新增資料磁碟的虛擬機器。在此範例中，虛擬機器是 mysqlnode1。

![][1]

在虛擬機器頁面上，按一下 [儀表板]。

![][2]
 

在工作列上，按一下 [連接]。
 
![][3]

然後按一下 [連接空的磁碟]。

![][4]
 
資料磁碟的 [主機快取偏好設定] 應設定為 [無]。

這會將一個空的磁碟新增到虛擬機器中。再重複執行此步驟三次，您的 RAID 就有 4 個資料磁碟。

查看核心訊息記錄檔，您可以看到虛擬機器中新增的磁碟機。例如，若要在 Ubuntu 上查看此資料，請使用下列命令：

	sudo grep SCSI /var/log/dmesg

####步驟 2：建立具有額外磁碟的 RAID
遵循這份文件中詳細的 RAID 安裝步驟：

[http://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-RAID/](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-RAID/)

>[AZURE.NOTE]如果您使用 XFS 檔案系統，請在建立 RAID 後遵循下列步驟。

若要在 Debian、Ubuntu 或 Linux Mint 上安裝 XFS，請使用下列命令：

	apt-get -y install xfsprogs  

若要在 Fedora 、CentOS 或 RHEL 上安裝 XFS，請使用下列命令：

	yum -y install xfsprogs  xfsdump 


####步驟 3：設定新的儲存體路徑
使用下列命令：

	root@mysqlnode1:~# mkdir -p /RAID0/mysql

####步驟 4：將原始資料複製到新的儲存體路徑
使用下列命令：

	root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####步驟 5：修改權限，讓 MySQL 可以存取 (讀取和寫入) 資料磁碟
使用下列命令：

	root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##調整磁碟 I/O 排程演算法
Linux 會實作四種類型的 I/O 排程演算法：

-	NOOP 演算法 (沒有作業)
-	期限演算法 (期限)
-	完全公平佇列演算法 (CFQ)
-	預算週期演算法 (預期)  

您可以在不同的狀況下選取不同的 I/O 排程器，讓效能達到最佳化。在完全隨機存取環境中，CFQ 與期限演算法的效能沒有太大差別。通常建議將 MySQL 資料庫環境設為 [期限]，以求穩定性。如果有大量循序 I/O，CFQ 可能會降低磁碟 I/O 效能。

對於 SSD 和其他設備，使用 NOOP 或 [期限] 可達到比預設排程器更佳的效能。

從核心 2.5 起，預設 I/O 排程演算法為 [期限]。從核心 2.6.18 開始，CFQ 成為預設 I/O 排程演算法。您可以在核心開機時指定此設定，或在系統執行時動態修改此設定。

下列範例示範如何檢查預設排程器並將其設定為 NOOP 演算法。

若為 Debian 散發版本系列：

###步驟 1. 檢視目前的 I/O 排程器
使用下列命令：

	root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler 

您會看到下列輸出，其表示目前的排程器。

	noop [deadline] cfq 


###步驟 2.變更 I/O 排程演算法的目前裝置 (/dev/sda)
使用下列命令：

	azureuser@mysqlnode1:~$ sudo su -
	root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
	root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
	root@mysqlnode1:~# update-grub

>[AZURE.NOTE]單獨針對 /dev/sda 設定此項並不是很有用。必須在資料庫所在的所有資料磁碟上進行設定。

您應會看到下列輸出，表示 grub.cfg 已成功重建且預設排程器已更新為 NOOP。

	Generating grub configuration file ...
	Found linux image: /boot/vmlinuz-3.13.0-34-generic
	Found initrd image: /boot/initrd.img-3.13.0-34-generic
	Found linux image: /boot/vmlinuz-3.13.0-32-generic
	Found initrd image: /boot/initrd.img-3.13.0-32-generic
	Found memtest86+ image: /memtest86+.elf
	Found memtest86+ image: /memtest86+.bin
	done

若為 Redhat 散發版本系列，您只需要使用下列命令：

	echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##設定系統檔案作業設定
最佳作法是停用檔案系統上的 atime 記錄功能。Atime 是上次檔案存取時間。每當存取檔案時，檔案系統就會在記錄檔中記錄時間戳記。不過，很少使用這項資訊。如果您不需要它，可予以停用，將會減少整體的磁碟存取時間。
 
若要停用 atime 記錄，您必須修改檔案系統組態檔案 /etc/ fstab，並新增 **noatime** 選項。

例如，編輯 vim /etc/fstab 檔案，新增 noatime，如下所示。

	# CLOUD_IMG: This file was created/modified by the Cloud Image build process
	UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
	#Add the “noatime” option below to disable atime logging
	UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
	/dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

然後，使用下列命令重新掛接檔案系統：

	mount -o remount /RAID0

測試修改過的結果。請注意當您修改測試檔案時，存取時間不會更新。

修改前範例：

![][5]
 
修改後範例：

![][6]

##增加高並行存取的系統控制代碼數目上限
MySQL 是高並行存取資料庫。Linux 的並行控制代碼預設數目為 1024，這並不足夠。**使用下列步驟來增加系統的並行控制代碼上限，以支援 MySQL 的高並行存取**。

###步驟 1：修改 limits.conf 檔案
在 /etc/security/limits.conf 檔案中加入下列四行，以增加允許的並行控制代碼上限。請注意 65536 是系統可支援的數目上限。

	* soft nofile 65536
	* hard nofile 65536
	* soft nproc 65536
	* hard nproc 65536

###步驟 2：更新系統以取得新的限制
執行以下命令：

	ulimit -SHn 65536
	ulimit -SHu 65536 

###步驟 3：確定會在開機時更新限制
將下列啟動命令放入 /etc/rc.local 檔案中，以便在每次開機時生效。

	echo “ulimit -SHn 65536” >>/etc/rc.local
	echo “ulimit -SHu 65536” >>/etc/rc.local

##MySQL 資料庫最佳化 
您可以使用相同的效能微調策略，將 Azure 上的 MySQL 設定為內部部署電腦。

主要的 I/O 最佳化規則如下：

-	增加快取的大小。
-	減少 I/O 回應時間。  

若要最佳化 MySQL 伺服器設定，您可以更新 my.cnf 檔案，該檔案是伺服器和用戶端電腦的預設組態檔。

下列組態項目是影響 MySQL 效能的主要因素：

-	**innodb_buffer_pool_size**：緩衝集區包含經過緩衝處理的資料和索引。這通常設定為 70% 的實體記憶體。
-	**innodb_log_file_size**：這是重做記錄檔的大小。您可以使用重做記錄檔來確保寫入作業快速、可靠並可在當機後復原。這會設定為 512 MB，將提供大量空間給您記錄寫入作業。
-	**max_connections**：應用程式有時不會正確關閉連線。較大的值讓伺服器有更多的時間來回收閒置的連線。連線數目上限為 10000，但建議的上限為 5000。
-	**Innodb_file_per_table**：此設定可啟用或停用 InnoDB 在個別檔案中儲存資料表的功能。開啟此選項將確保可有效地套用數個進階管理作業。從效能觀點來看，它可以加速資料表空間傳輸，並將 debris 管理效能最佳化。因此這個選項的建議設定為 ON。</br> 從 MySQL 5.6 開始，預設設定為 ON。因此，不需要採取任何動作。若為其他版本，也就是 5.6 以前的版本，預設設定為 OFF。必須將此選項設為 ON。而且應在載入資料之前套用，因為只有新建的資料表會受影響。
-	**innodb_flush_log_at_trx_commit**：預設值為 1，其範圍設為 0~2。對獨立 MySQL DB 而言，預設值是最適合的選項。設定為 2 可達到最大資料完整性，適合於 MySQL 叢集中的主機。設定為 0 會讓資料遺失，這可能會影響可靠性，在某些情況下，效能會更佳，適合於 MySQL 叢集中的從屬。
-	**Innodb_log_buffer_size**：記錄緩衝區允許交易執行，而不需在交易認可前將記錄檔排清到磁碟。不過，如果有大型二進位物件或文字欄位，將會非常快速地耗用快取，並將觸發頻繁的磁碟 I/O。如果 Innodb_log_waits 狀態變數不是 0，最好能增加緩衝區大小。
-	**query_cache_size**：最佳選項是一開始就將它停用。將 query_cache_size 設為 0 (這現在是 MySQL 5.6 中的預設值)，並使用其他方法來加速查詢。  
  
請參閱[附錄 D](#AppendixD) 來比較最佳化之後的效能。


##開啟 MySQL 緩慢查詢記錄，以便分析效能瓶頸
MySQL 緩慢查詢記錄檔可協助您識別 MySQL 的較慢查詢。啟用 MySQL 緩慢查詢記錄檔之後，您可以使用 **mysqldumpslow** 之類的 MySQL 工具來識別效能瓶頸。

請注意，預設不會啟用此選項。開啟緩慢查詢記錄檔可能會耗用一些 CPU 資源。因此，建議您暫時啟用這個選項，以便排解效能瓶頸。

###步驟 1：將下列幾行新增至 my.cnf 檔案結尾，以修改該檔案   

	long_query_time = 2
	slow_query_log = 1
	slow_query_log_file = /RAID0/mysql/mysql-slow.log

###步驟 2：重新啟動 mysql 伺服器
	service  mysql  restart

###步驟 3：使用 “show” 命令來檢查設定是否生效
 
![][7]
   
![][8]
 
在此範例中，您可以看到緩慢查詢功能已開啟。您可以接著使用 **mysqldumpslow** 工具，判斷效能瓶頸並將效能最佳化，例如，新增索引。





##附錄

以下是在目標實驗室環境上產生的範例效能測試資料，可提供採用不同效能調整方法的效能資料趨勢的一般背景，不過，在不同的環境或產品版本之下結果可能會有所不同。

<a name="AppendixA"></a>附錄 A：**不同 RAID 層級的磁碟效能 (IOPS)**


![][9]
 
**測試命令：**

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE.NOTE：這項測試的工作負載會使用 64 個執行緒，並嘗試達到 RAID 的上限。

<a name="AppendixB"></a>附錄 B：**不同 RAID 層級的 MySQL 效能 (輸送量) 比較** (XFS檔案系統)

 
![][10] ![][11]

**測試命令：**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**不同 RAID 層級的 MySQL 效能 (OLTP) 比較** ![][12]

**測試命令：**

	time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>附錄 C：**不同區塊大小的磁碟效能 (IOPS) 比較** (XFS 檔案系統)

 
![][13]

**測試命令：**

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

請注意，這項測試所使用的檔案大小分別為 30 GB 和 1 GB，搭配 RAID 0 (4 磁碟) XFS fie 系統。


<a name="AppendixD"></a>附錄 D：**最佳化之前和之後的 MySQL 效能 (輸送量) 比較** (XFS檔案系統)

  
![][14]

**測試命令：**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**預設值與最佳化的組態設定如下所示：**

|參數 |預設值 |optmization
|-----------|-----------|-----------
|**innodb_buffer_pool_size** |None |7G
|**innodb_log_file_size** |5M |512M
|**max_connections** |100 |5000
|**innodb_file_per_table** |0 |1
|**innodb_flush_log_at_trx_commit** |1 |2
|**innodb_log_buffer_size** |8M |128M
|**query_cache_size** |16M |0


如需更詳細的最佳化設定參數，請參閱 mysql 官方指示。

[http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)

[http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**測試環境**

|硬體 |詳細資料
|-----------|-------
|CPU |AMD Opteron(tm) 處理器 4171 HE/4 核心
|記憶體 |14G
|disk |10G/磁碟
|作業系統 |Ubuntu 14.04.1 LTS



[1]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-14.png
 

<!---HONumber=July15_HO1-->