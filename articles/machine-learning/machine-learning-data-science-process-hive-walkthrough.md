<properties 
	pageTitle="作用中的 Azure 資料科學程序：使用 HDInsight Hadoop 叢集 | Azure" 
	description="使用 HDInsight Hadoop 叢集的端對端 Azure 資料科學程序，利用可公開使用的資料集來建置和部署模型。" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="hangzh;bradsev" /> 

                
# 作用中的 Azure 資料科學程序：使用 HDInsight Hadoop 叢集

在此教學課程中，您將遵循 Azure 資料科學程序圖，端對端使用 Azure HDInsight Hadoop 叢集，來建置和部署使用可公開使用之資料集 ([NYC 計程車車程](http://www.andresmh.com/nyctaxitrips/)資料集) 的模型。 


## <a name="dataset"></a>NYC 計程車車程資料集說明

「NYC 計程車車程」資料大約是 20GB 以逗號分隔值 (CSV) 的壓縮檔 (未壓縮時可達 48GB)，其中包含超過 1 億 7300 萬筆個別車程及針對每趟車程支付的費用。每趟車程記錄包括上車和下車的位置與時間、匿名的計程車司機駕照號碼，以及圓形徽章 (計程車的唯一識別碼) 號碼。資料涵蓋 2013 年的所有車程，並且每月會在下列兩個資料集中加以提供：

1.  'trip_data' CSV 檔案包含車程詳細資訊，例如，乘客數、上車和下車地點、車程持續時間，以及車程長度。以下是一些範例記錄：

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2.  'trip_fare' CSV 檔案包含針對每趟車程所支付的費用詳細資料，例如，付款類型、費用金額、銷售稅和稅金、小費和服務費，以及支付的總金額。以下是一些範例記錄：

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

聯結 trip_data and trip_fare 的唯一索引鍵是由下列欄位所組成：medallion、hack_licence 和 pickup_datetime。

分別有 12 個車程 'trip_data' 和 'trip_fare' 檔案。在檔案名稱中，數字 1-12 表示產生車程的月份。 

## <a name="mltasks"></a>預測工作的範例
處理資料時，根據分析來決定您想要進行的預測種類，有助於釐清您必須在程序中包含的工作。
以下是三個預測問題的範例，我們將在此逐步解說中說明哪一個的構想是以 *tip_amount* 為基礎：

1. **二進位分類**：預測是否已針對某趟車程支付小費，例如，大於 $0 的 *tip_amount* 為正面範例，而等於 $0 的 *tip_amount* 為負面範例。

2. **多類別分類**：預測已針對該趟車程支付的小費金額範圍。我們會將 *tip_amount* 區分為五個分類收納組或類別：
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **迴歸工作**：預測已針對某趟車程支付的小費金額。  


## <a name="setup"></a>設定 Azure 資料科學環境

誠如您在[規劃您的環境](machine-learning-data-science-plan-your-environment.md)指南中所見，在 Azure 中使用「NYC 計程車車程」資料集時，有數種方法可以採用：

- 使用 Azure Blob 中的資料，然後在 Azure Machine Learning 中模型化
- 將資料載入 SQL Server 資料庫，然後在 Azure Machine Learning 中模型化
- 將資料載入 HDInsight Hive 資料表，然後在 Azure Machine Learning 中模型化

根據資料集大小、資料來源位置，以及選取的 Azure 目標環境，此案例類似[案例 #7：本機檔案中的大型資料集，Azure HDInsight Hadoop 叢集中的目標 Hive](../machine-learning-data-science-plan-sample-scenarios.md#largedbtohive)。

若要針對這個使用 Azure HDInsight Hadoop 叢集的方法來設定您的 Azure 資料科學環境，更具體的說法是使用 Hive 資料表和查詢來儲存及處理資料，請完成下列步驟。

1. [建立儲存體帳戶](../storage-whatis-account.md)

2. [建立 Azure ML 工作區](machine-learning-create-workspace.md)

3. [佈建資料科學 **Windows** 虛擬機器](machine-learning-data-science-setup-virtual-machine.md).

	> [AZURE.NOTE] 指令碼範例將會在虛擬機器安裝程序期間，下載到您的資料科學虛擬機器。當 VM 後續安裝指令碼完成之後，範例將會在您位於 *C:\Users<user_name>\Documents\Data Science Scripts* 上的 VM 文件庫中。  此範例資料夾在後續內容中稱為**指令碼範例**。Hive 查詢範例會包含於 [**指令碼範例**] 資料夾內副檔名為 .hql 的檔案中。請注意，此資料夾路徑中所參考的 *<user_name>* 是 VM 的 Windows 登入名稱，而不是您的 Azure 使用者名稱。

4. [自訂適用於資料科學的 Azure HDInsight Hadoop 叢集](machine-learning-data-science-customize-hadoop-cluster.md)。這個步驟將會建立已在所有節點上安裝 64 位元 Anaconda Python 2.7 的 Azure HDInsight Hadoop 叢集。建立自訂的 Hadoop 叢集之後，請使用此自訂主題中概述的程序，在 Azure 入口網站中啟用 Hadoop 叢集前端節點的遠端存取。


## <a name="getdata"></a>從公用來源取得資料

若要從公用位置取得 [NYC 計程車車程](http://www.andresmh.com/nyctaxitrips/)資料集，您可以使用[從 Azure Blob 儲存體來回移動資料](machine-learning-data-science-move-azure-blob.md)中所述的任何一種方法，將資料複製到新的虛擬機器。

使用 AzCopy 複製資料：

1. 登入您的虛擬機器 (VM)

2. 在 VM 的資料磁碟中建立新目錄 (注意：請勿使用 VM 隨附的暫存磁碟做為資料磁碟)。

3. 從 [命令提示字元] 視窗中，執行下列 AzCopy 命令，使用您在 (2) 中建立的資料資料夾路徑來取代 <path_to_data_folder>：

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxitrip /Dest:<path_to_data_folder> /S

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxifare /Dest:<path_to_data_folder> /S

	When the AzCopy completes, a total of 24 zipped CSV files (12 for trip_data and 12 for trip_fare) should be in the data folder.

	>[AZURE.NOTE] *Source:https://getgoing.blob.core.windows.net/nyctaxitrip* 和 *Source:https://getgoing.blob.core.windows.net/nyctaxifare* 會指定兩個公用的 Azure 容器，以用來和使用者共用已經解壓縮的 NYC 計程車資料。從這兩個公用的 Azure 容器中進行讀取並不需要存取金鑰。 

## <a name="upload"></a>將資料上傳至 Azure HDInsight Hadoop 叢集的預設容器

從虛擬機器中的命令提示字元或 Windows PowerShell 視窗，執行下列 AzCopy 命令，使用您在建立 Hadoop 叢集時所指定的值來取代下列參數。因此取代：

* ***&#60;path_to_data_folder>*** (使用您在上一節建立的資料) 
* ***&#60; Hadoop 叢集的儲存體帳戶名稱>*** (使用您的叢集所使用的儲存體帳戶)
* ***&#60;Hadoop 叢集的預設容器>*** (使用您的叢集所使用的預設容器)
* ***&#60;儲存體帳戶金鑰>*** (使用您的叢集所使用的儲存體帳戶金鑰)

請注意，如果此命令中參考的 ***nyctaxitripraw*** 或 ***nyctaxifareraw*** 不存在，就會在容器中建立它們。 

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
		
		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

## <a name="#hive-db-tables"></a>建立依月份進行資料分割的 Hive 資料庫和資料表

若要存取和執行此逐步解說中所使用的 Hive 查詢，請登入 Hadoop 叢集的前端節點、在前端節點的桌面上開啟 Hadoop 命令列，然後藉由輸入下列命令來進入 Hive 目錄：

    cd %hive_home%\bin

如果您需要這些程序或替代程序的任何額外協助，請參閱[從 Hadoop 命令列直接提交 Hive 查詢](machine-learning-data-science-process-hive-tables.md#submit)一節。 

如果您已經依照[設定 Azure 資料科學環境](#setup)中的指示來建立 Azure 虛擬機器，則 Hive 查詢範例應該已經下載至您虛擬機器上的 [**指令碼範例**] 資料夾中。或者，使用者可以從 [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) 簽出 .hql 檔案。 

若要提交 .hql 檔案中的 Hive 查詢，您必須將 .hql 檔案轉移到 Hadoop 叢集前端節點的本機目錄中。在下列範例中，我們假設 .hql 檔案已經轉移到前端節點的 ***C:\temp&#92;*** 目錄中。

以下是 ***C:\temp\sample_hive_create_db_and_tables.hql*** 檔案的內容，其會建立 Hive 資料庫 ***nyctaxidb*** 及資料表 ***trip*** 和 ***fare***。

	create database if not exists nyctaxidb;

	create external table if not exists nyctaxidb.trip
	( 
	    medallion string, 
	    hack_license string,
	    vendor_id string, 
	    rate_code string, 
	    store_and_fwd_flag string, 
	    pickup_datetime string, 
	    dropoff_datetime string, 
	    passenger_count int, 
	    trip_time_in_secs double, 
	    trip_distance double, 
	    pickup_longitude double, 
	    pickup_latitude double, 
	    dropoff_longitude double, 
	    dropoff_latitude double)  
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

	create external table if not exists nyctaxidb.fare 
	( 
	    medallion string, 
	    hack_license string, 
	    vendor_id string, 
	    pickup_datetime string, 
	    payment_type string, 
	    fare_amount double, 
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double)
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

在前端節點的 Hadoop 命令列中輸入下列命令，來提交這些 Hive 查詢：
	
	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"


## <a name="#load-data"></a>依資料分割將資料載入資料表
在 Hadoop 命令列中執行下列 PowerShell 命令，依據月份和資料分割，將資料載入 Hive 資料表：

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

以下為要進行檢查的 *sample_hive_load_data_by_partitions.hql* 檔案內容。

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/fare_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

### <a name="#show-db"></a>顯示 HDInsight Hadoop 叢集中的資料庫

若要在 Hadoop 命令列視窗內顯示在 HDInsight Hadoop 叢集中建立的資料庫，可在 Hadoop 命令列中執行下列命令：

	hive -e "show databases;"

### <a name="#show-tables"></a>顯示 nyctaxidb 資料庫中的資料表 
	
若要顯示 nyctaxidb 資料庫中的資料表，可在 Hadoop 命令列中執行下列命令：
	
	hive -e "show tables in nyctaxidb;"
   
## <a name="#explore-hive"></a>Hive 中的資料探索和功能工程
適用於已載入 Hive 資料表之資料的資料探索和功能工程工作可以使用 Hive 查詢來完成。以下是我們想要在本節中逐步引導您執行這類工作的範例：

- 檢視這兩個資料表中的前 10 筆記錄。
- 在變動的時間範圍中探索數個欄位的資料分佈。
- 調查經度和緯度欄位的資料品質。
- 根據 **tip_amount** 來產生二進位和多類別分類標籤。
- 藉由計算車程的直線距離來產生功能。
- 聯結這兩個資料表，並擷取將用來建置模型的隨機取樣。

若要進行預測，當您已在 Hive 中完成這些資料探索和功能工程之後，就已經準備好繼續前往 Azure Machine Learning。您可以儲存最後一個 Hive 查詢來擷取和取樣資料，然後複製查詢並直接貼至 Azure Machine Learning 中的讀取程式模組。然後就可以準備建置此資料的模型。

### 探索：檢視 trip 資料表中的前 10 筆記錄

若要掌握資料結構描述的本質以及資料的外觀，使用者可以從每個資料表中擷取前 10 筆記錄。從 Hadoop 命令列主控台個別執行下列兩個查詢來檢查記錄。

若要取得 *table _trip_* 中的前 10 筆記錄：

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
    
若要取得 *table _fare_* 中的前 10 筆記錄：
	
	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

### 探索：檢視 12 個資料分割中每一個資料分割的記錄數目。

在 Hadoop 命令列主控台中執行下列命令，來檢視 12 個月份資料分割中每一個資料分割的記錄數目。
	
	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

### 探索：依據 medallion 的車程分佈

此範例會識別在特定期間內超過 100 趟車程的圓形徽章 (計程車數目)。此查詢可以從資料分割資料表存取中獲益，因為它的條件是以資料分割變數 **month** 為依據。 

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql"

以下是要進行檢查的 *sample_hive_trip_count_by_medallion.hql* 檔案內容。

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100 
	ORDER BY med_count desc;

### 探索：依據 medallion 和 hack_license 的車程分佈

以下範例會依資料表的多個資料行來分組，在此案例中是依據 medallion 和 hack_license 資料行來分組。從 Hadoop 命令列主控台執行下列命令：

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql"

以下是要進行檢查的 *sample_hive_trip_count_by_medallion_license.hql* 檔案內容。
	
    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

### 資料品質評估：確認含有無效經度和 (或) 緯度的記錄

此範例會檢查是否有任何經度和 (或) 緯度欄位包含無效值 (弧度角度應介於 -90 和 90 之間)，或是具有 (0，0) 座標。

從 Hadoop 命令列主控台執行下列命令：

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

此命令中包含的 *-S* 引數會隱藏 Hive Map/Reduce 工作的狀態畫面顯示。這非常有用，因為它讓 Hive 查詢輸出的螢幕顯示更容易閱讀。 

以下是要進行檢查的 *sample_hive_quality_assessment.hql* 檔案內容。

    	SELECT COUNT(*) FROM nyctaxidb.trip
    	WHERE month=1
    	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'));

### 探索：收到小費和未收到小費之車程的頻率

此範例會尋找在指定期間內 (或者，如果涵蓋一整年，則是在整個資料庫中)，已收到小費的車程數目，以及未收到小費的車程數目。此分佈會反映二進位標籤分佈，以便稍後用來將二進位分類模型化。

從 Hadoop 命令列主控台執行下列命令：

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"

以下是要進行檢查的 *sample_hive_tipped_frequencies.hql* 檔案內容。

    SELECT tipped, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

### 探索：小費範圍的頻率

此範例會計算在指定期間內 (或者，如果涵蓋一整年，則是在整個資料庫中) 小費範圍的分佈。這是標籤類別的分佈，會在稍後用來將多類別分類模型化。

從 Hadoop 命令列主控台執行下列命令：

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

以下是要進行檢查的 *sample_hive_tip_range_frequencies.hql* 內容。

	SELECT tip_class, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount=0, 0, 
            if(tip_amount>0 and tip_amount<=5, 1, 
            if(tip_amount>5 and tip_amount<=10, 2, 
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

### 探索：計算直接距離並與車程距離進行比較

此範例會計算車程的直線距離 (以英哩計算)。此範例只會使用稍早所提供的資料品質評估查詢，將結果限制為有效座標。

從 Hadoop 命令列主控台執行下列命令：

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"

以下是要進行檢查的 *sample_hive_trip_direct_distance.hql* 檔案內容。

    set R=3959;
    set pi=radians(180);
	
	insert overwrite directory 'wasb:///queryoutputdir'
    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
        pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
    from nyctaxidb.trip 
    where month=1 
        and pickup_longitude between -90 and -30
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and -30
        and dropoff_latitude between 30 and 90;

查詢完成之後，會將結果寫入 Hadoop 叢集預設容器下的 Azure Blob ***queryoutputdir/000000_0***。如果您使用 Azure 儲存體總管，就應該能夠在 Hadoop 叢集預設容器中看見此 Blob，如下圖所示。

![Create workspace][2]

請注意，您可以套用篩選條件 (以紅色方塊反白顯示)，只擷取名稱中具有指定字母的 Blob。 

查詢完成之後，您可以使用 Azure SDK，將查詢輸出從 Azure Blob 讀取到 Pandas 資料框架，以進行進一步探索和處理。如需如何將 Azure Blob 讀取到 Pandas 資料框架的相關指示，請參閱[處理資料科學環境中的 Azure Blob 資料](machine-learning-data-science-process-data-blob.md)。 
	
### 準備資料以進行模型建置

本節中提供的查詢會聯結 **nyctaxidb.trip** 和 **nyctaxidb.fare** 資料表、產生二進位分類標籤 **tipped** 及多類別分類標籤 **tip_class**。您可以複製此查詢，然後直接貼至 [Azure Machine Learning Studio](https://studio.azureml.net) 讀取程式模組，以便從 Azure 中的 **Hive 查詢**直接擷取資料。此查詢也會排除含有不正確緯度和 (或) 經度座標的記錄。

此查詢會直接套用 Hive 內嵌的 UDF，以便從 Hive 記錄產生數個功能，包括小時、年中的週，以及欄位 _pickup_datetime_ 的工作日 (1 代表星期一，而 7 代表星期日)。使用者可以參考 [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)，以取得內嵌 Hive UDF 的完整清單。

此查詢也會對資料進行向下取樣，如此一來，查詢結果就能符合 Azure Machine Learning Studio。大約只有 1% 會匯入 Studio。

從 Hadoop 命令列主控台執行下列命令來提交查詢：

	hive -f "C:\temp\sample_hive_prepare_for_aml.hql"

以下是要進行檢查的 *sample_hive_prepare_for_aml.hql* 檔案內容。
	
    select 
        t.medallion, 
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        f.payment_type, 
        f.fare_amount, 
        f.surcharge, 
        f.mta_tax, 
        f.tip_amount, 
        f.tolls_amount, 
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
            if(tip_amount>0 and tip_amount<=5,1,
            if(tip_amount>5 and tip_amount<=10,2,
            if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
    from
    (
        select medallion, 
            hack_license,
            vendor_id,
            rate_code,
            store_and_fwd_flag,
            pickup_datetime,
            dropoff_datetime,
            passenger_count,
            trip_time_in_secs,
            trip_distance,
            pickup_longitude,
            pickup_latitude,
            dropoff_longitude,
            dropoff_latitude,
            rand() as sample_key 
        from nyctaxidb.trip
        where pickup_latitude between 30 and 60
            and pickup_longitude between -90 and -60
            and dropoff_latitude between 30 and 60
            and dropoff_longitude between -90 and -60
    )t
    join
    (
        select 
            medallion, 
            hack_license, 
            vendor_id, 
            pickup_datetime, 
            payment_type, 
            fare_amount, 
            surcharge, 
            mta_tax, 
            tip_amount, 
            tolls_amount, 
            total_amount
        from nyctaxidb.fare 
    )f
    on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
    where t.sample_key<=0.01

## <a name="mlmodel"></a>在 Azure Machine Learning 中建置模型

我們現在已準備好繼續在 [Azure Machine Learning](https://studio.azureml.net) 中進行模型建置和模型部署。資料現已就緒，可用來處理上述識別出的預測問題：

1. **二進位分類**：預測是否已針對某趟車程支付小費。

2. **多類別分類**：若要預測針對該趟車程支付的小費金額範圍，請使用先前定義的類別。 

3. **迴歸工作**：預測已針對某趟車程支付的小費金額。  

若要開始進行模型化練習，請登入 Azure Machine Learning 工作區。如果您尚未建立機器學習服務工作區，請參閱[建立 Azure ML 工作區](machine-learning-create-workspace.md)。

1. 若要開始使用 Azure Machine Learning，請參閱[什麼是 Azure Machine Learning Studio？](machine-learning-what-is-ml-studio.md)

2. 登入 [Azure Machine Learning Studio](https://studio.azureml.net)。

3. Studio 首頁會提供豐富的資訊、影片、教學課程、與模組參考的連結，以及其他資源。如需 Azure Machine Learning 的詳細資訊，請參閱 [Azure Machine Learning 文件中心](http://azure.microsoft.com/documentation/services/machine-learning/)。

典型的訓練體驗包含下列各項：

1. 建立 [**+NEW**] 實驗。
2. 取得 Azure ML 的資料。
3. 視需要前置處理、轉換和操作資料。
4. 視需要產生功能。
5. 將資料分割為訓練/驗證/測試資料集 (或讓每一個擁有個別的資料集)。
6. 根據要解決的學習問題，選取一或多個機器學習服務演算法。例如，二進位分類、多類別分類、迴歸。
7. 使用訓練資料集來訓練一或多個模型。
8. 使用訓練的模型，為驗證資料集計分。
9. 評估模型來計算適用於學習問題的相關度量。
10. 微調模型，並選取要部署的最佳模型。

在這個練習中，我們已經探索了 Hive 中的資料並進行工程 (步驟 1-4)，並且決定了要在 Azure ML 中擷取的取樣大小。建置一或多個預測模型：

1. 使用 [**讀取程式**] 模型來取得 Azure ML 的資料，您可以在＜**資料輸入和輸出**＞一節中取得該模型。如需詳細資訊，請參閱[讀取程式](http://msdn.microsoft.com/library/dn784775)模組參考頁面。

	![Create workspace][15]

2. 在 [**屬性**] 面板中，選取 [**Hive 查詢**] 做為 [**資料來源**]。

3. 輸入 HDInsight Hadoop 叢集資訊，如下所示。**Azure 儲存體帳戶名稱**必須是可用來建立 HDInsight Hadoop 叢集的儲存體帳戶，而 **Azure 容器名稱**必須是 Hadoop 叢集的預設容器。 

	![Create workspace][11]

4. 在 [**Hive 資料庫查詢**] 中編輯文字區域、貼上會擷取必要資料庫欄位的查詢 (包括任何計算得到的欄位，例如標籤)，以及向下取樣資料以達所需的取樣大小。

下圖顯示從 Hive 查詢直接讀取資料的二進位分類實驗範例。您可以針對多類別分類和迴歸問題建構類似的實驗。

![Create workspace][12]

> [AZURE.IMPORTANT] 在前幾節中提供的模型化資料擷取和取樣查詢範例中，**這三個模型化練習的所有標籤都包含於此查詢中**。每一個模型化練習的重要 (必要) 步驟都是針對其他兩個問題**排除**不需要的標籤，以及任何其他的**目標流失**。例如，使用二進位分類時，請使用 [**tipped**] 標籤，並排除 [**tip_class**]、[**tip_amount**] 和 [**total_amount**] 等欄位。後者為目標流失，因為它們意指支付的小費。

> 在此實驗中，第一個 [**中繼資料編輯器**] 模組會將資料行名稱新增至讀取程式模組的輸出資料。因為從 Hive 查詢讀取資料的讀取程式模組不會為輸出資料建立資料行名稱，所以需要此模組。 

> 若要排除不必要的資料行和 (或) 目標流失，您可能會使用 [**專案資料行**] 模組或 [**中繼資料編輯器**]。如需詳細資訊，請參閱[專案資料行](http://msdn.microsoft.com/library/dn784740)和[中繼資料編輯器](http://msdn.microsoft.com/library/dn784761)參考頁面。

## <a name="mldeploy"></a>在 Azure Machine Learning 中部署模型

當您的模型備妥時，可以直接從實驗中將它部署為 Web 服務。如需如何發佈 Azure ML Web 服務的詳細資訊，請參閱 [Azure Machine Learning API 服務操作](../machine-learning-overview-of-azure-ml-process.md)。

若要部署新的 Web 服務，您需要：

1. 建立計分實驗。
2. 發佈 Web 服務。

若要從「**已完成**」的訓練實驗建立計分實驗，請在下方動作列中按一下 [**建立計分實驗**]。

![Azure Scoring][13]

Azure Machine Learning 將根據訓練實驗的元件來建立計分實驗。特別是，它將：

1. 儲存訓練的模型，並移除模型訓練模組。
2. 識別邏輯的「**輸入連接埠**」，以代表預期的輸入資料結構描述。
3. 識別邏輯的「**輸出連接埠**」，以代表預期的 Web 服務輸出結構描述。

建立計分實驗時，請檢閱它並視需要進行調整。典型的調整是使用某一個會排除標籤欄位的輸入資料集和 (或) 查詢來取代它們，因為在呼叫服務時將無法使用這些欄位。若要將輸入資料集和 (或) 查詢的大小縮減為只有幾筆足以表示輸入結構描述的記錄，這也是個很好的練習。針對輸出連接埠，通常會使用 [**專案資料行**] 模組來排除所有輸入欄位，並且僅在輸出中包含 [**計分標籤**] 和 [**計分機率**]。

下圖顯示計分實驗範例。準備好要進行發佈時，請在下方動作列中按一下 [**發佈 WEB 服務**]。

![Create workspace][14]

總言之，在本逐步解說教學課程中，您已經建立適用於大型公用資料集的 Azure 資料科學環境。從資料擷取開始，繼續進行資料科學程序中的探索和功能工程工作，一直到將 Azure Machine Learning Web 服務的訓練和發佈模型化。

## 授權資訊

此範例逐步解說及其隨附的指令碼是在 MIT 授權下由 Microsoft 所共用。如需詳細資訊，請檢查 GitHub 上程式碼範例目錄中的 LICENSE.txt 檔案。

## 參考

*	[Andrés Monroy NYC 計程車車程下載頁面](http://www.andresmh.com/nyctaxitrips/)  
*	[FOILing NYC 的計程車車程資料 (作者為 Chris Whong)](http://chriswhong.com/open-data/foil_nyc_taxi/)   
*	[NYC 計程車和小型巴士委託研究和統計資料](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!--HONumber=49--> 