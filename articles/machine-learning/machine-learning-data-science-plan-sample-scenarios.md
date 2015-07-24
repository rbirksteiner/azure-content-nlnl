<properties
	pageTitle="Azure Machine Learning 中進階分析程序和技術的案例 | Microsoft Azure"
	description="針對 Azure Machine Learning 中的進階預測性分析程序選取適當的案例。"
	services="machine-learning"
	documentationCenter=""
	authors="msolhab"
	manager="paulettm"
	editor="" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na" 
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/29/2015"
	ms.author="msolhab;bradsev" />


# Azure Machine Learning 中進階分析程序和技術的案例

本文概述可以運用 Azure Machine Learning 中的進階分析程序和技術 (ADAPT) 來加以處理的各種範例資料來源和目標案例。本文將根據資料特性、來源位置和在 Azure 中的目標儲存機制，來說明處理順序中可用的選項。

最後一節提供**決策樹**，讓您在選取適合您資料和目標的範例案例時可以使用。

>[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]


下列各節均提供一個範例案例。在每個案例中，都會列出可能的資料科學或進階分析流程，以及支援的 Azure 資源。

>[AZURE.NOTE]**對於所有下列案例，您必須：**

*   [建立儲存體帳戶](storage-whatis-account.md)
*   [建立 Azure ML 工作區](machine-learning/machine-learning-create-workspace.md)




## <a name="smalllocal"></a>案例 #1：本機檔案中的中小型表格式資料集

![中小型本機檔案][1]

#### 其他 Azure 資源：無

1.  登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。

2.  上傳資料集。

3.  建置從上傳的資料集開始的 Azure 機器學習實驗流程。

## <a name="smalllocalprocess"></a>案例 #2：本機檔案中需要處理的中小型資料集

![需要處理的中小型本機檔案][2]

#### 其他 Azure 資源：Azure 虛擬機器 (IPython Notebook 伺服器)

1.  建立執行 IPython Notebook 的 Azure 虛擬機器。

2.  將資料上傳至 Azure 儲存體容器。

3.  前置處理和清除 IPython Notebook 中的資料 (從 Azure 儲存體容器存取資料)。

4.  將資料轉換為已清理的表格式格式。

5.  將轉換的資料儲存在 Azure Blob 中。

6.  登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。

7.  使用[讀取器][reader]模組讀取 Azure Blob 的資料。

8. 建置從內嵌的資料集開始的 Azure 機器學習實驗流程。

## <a name="largelocal"></a>案例 #3：本機檔案的大型資料集，以 Azure Blob 為目標

![大型本機檔案][3]

#### 其他 Azure 資源：Azure 虛擬機器 (IPython Notebook 伺服器)

1.  建立執行 IPython Notebook 的 Azure 虛擬機器。

2.  將資料上傳至 Azure 儲存體容器。

3.  前置處理和清除 IPython Notebook 中的資料 (從 Azure Blob 存取資料)。

4.  若有需要，請將資料轉換為已清理的表格式格式。

5.  視需要瀏覽資料並建立功能。

6.  擷取中小型資料範例。

7.  將取樣資料儲存在 Azure Blob 中。

8. 登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。

9. 使用[讀取器][reader]模組讀取 Azure Blob 的資料。

10. 建置從內嵌的資料集開始的 Azure ML 實驗流程。


## <a name="smalllocaltodb"></a>案例 #4：本機檔案的中小型資料集，以 Azure 虛擬機器中的 SQL Server 為目標

![中小型本機檔案至 Azure 中的 SQL DB][4]

#### 其他 Azure 資源：Azure 虛擬機器 (SQL Server / IPython Notebook 伺服器)

1.  建立執行 SQL Server + IPython Notebook 的 Azure 虛擬機器。

2.  將資料上傳至 Azure 儲存體容器。

3.  使用 IPython Notebook 前置處理和清除 Azure 儲存體容器中的資料。

4.  若有需要，請將資料轉換為已清理的表格式格式。

5.  將資料儲存至 VM-local 檔案 (IPython Notebook 會在 VM 上執行，本機磁碟是指 VM 磁碟機)。

6.  將資料載入執行於 Azure VM 的 SQL Server 資料庫。

    a.選項 #1：使用 SQL Server Management Studio。

		i.  Login to SQL Server VM
        ii. Run SQL Server Management Studio.
        iii. Create database and target tables.
        iv. Use one of the bulk import methods to load the data from VM-local files.

    b.選項 #2：使用 IPython Notebook – 不建議用於中型和大型資料集

        i.  Use ODBC connection string to access SQL Server on VM.
        ii. Create database and target tables.
        iii. Use one of the bulk import methods to load the data from VM-local files.

7.  視需要瀏覽資料並建立功能。請注意，功能在資料庫資料表中無需具體化。僅注意建立這些功能的必要查詢。

8. 若有需要，請決定資料範例大小。

9. 登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。

10. 使用[讀取器][reader]模組，直接從 SQL Server 讀取資料。若有需要，將可擷取欄位、建立功能和取樣資料的必要查詢，直接貼入[讀取器][reader]查詢。

11. 建置從內嵌的資料集開始的 Azure ML 實驗流程。

## <a name="largelocaltodb"></a>案例 #5：本機資料中的大型資料集，目標 Azure VM 中的 SQL Server

![大型本機檔案至 Azure 中的 SQL DB][5]

#### 其他 Azure 資源：Azure 虛擬機器 (SQL Server / IPython Notebook 伺服器)

1.  建立執行 SQL Server 和 IPython Notebook 伺服器的 Azure 虛擬機器。

2.  將資料上傳至 Azure 儲存體容器。

3.  (選擇性) 前置處理和清除資料。

    a.前置處理和清除 IPython Notebook 中的資料 (從 Azure Blob 存取資料)。

    b.若有需要，請將資料轉換為已清理的表格式格式。

    c.將資料儲存至 VM-local 檔案 (IPython Notebook 會在 VM 上執行，本機磁碟是指 VM 磁碟機)。

4.  將資料載入執行於 Azure VM 的 SQL Server 資料庫。

    a.登入 SQL Server VM。

    b.如果資料尚未儲存，請將資料檔案從 Azure 儲存體容器下載到 local-VM 資料夾。

    c.執行 SQL Server Management Studio。

    d.建立資料庫和目標資料表。

	e.使用其中一個大量匯入方法來載入資料。

    f.如果需要資料表聯結，請建立索引以加速聯結。

 >[AZURE.NOTE]如需加快大型資料的載入速度，建議您建立分割資料表並大量平行匯入資料。如需詳細資訊，請參閱[平行資料匯入至 SQL 分割資料表](machine-learning/machine-learning-data-science-parallel-load-sql-partitioned-tables.md)。

5.  視需要瀏覽資料並建立功能。請注意，功能在資料庫資料表中無需具體化。僅注意建立這些功能的必要查詢。

6.  若有需要，請決定資料範例大小。

7.  登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。

8. 使用[讀取器][reader]模組，直接從 SQL Server 讀取資料。若有需要，將可擷取欄位、建立功能和取樣資料的必要查詢，直接貼入[讀取器][reader]查詢。

9. 從上傳的資料集開始的簡易 Azure ML 實驗流程

## <a name="largedbtodb"></a>案例 #6：SQL Server 資料庫內部部署中的大型資料集，以 Azure 虛擬機器中的 SQL Server 為目標

![大型 SQL DB 內部部署至 Azure 中的 SQL DB][6]

#### 其他 Azure 資源：Azure 虛擬機器 (SQL Server / IPython Notebook 伺服器)

1.  建立執行 SQL Server 和 IPython Notebook 伺服器的 Azure 虛擬機器。

2.  使用其中一個資料匯出方法來將資料從 SQL Server 匯出成傾印檔案。

    a.注意：如果您決定從內部部署資料庫移動所有資料，一個替代 (較快速) 方法是將整個資料庫移到 Azure 中的 SQL Server 執行個體。略過匯出資料、建立資料庫，和將資料載入/匯入目標資料庫等步驟，並依照替代方法進行。

3.  將傾印檔案上傳至 Azure 儲存體容器。

4.  將資料載入執行於 Azure 虛擬機器的 SQL Server 資料庫。

    a.登入 SQL Server VM。

    b.將資料檔案從 Azure 儲存體容器下載到 local-VM 資料夾。

    c.執行 SQL Server Management Studio。

    d.建立資料庫和目標資料表。

	e.使用其中一個大量匯入方法來載入資料。

	f.如果需要資料表聯結，請建立索引以加速聯結。

> [AZURE.NOTE]如需加快大型資料的載入速度，請建立分割資料表並大量平行匯入資料。如需詳細資訊，請參閱[平行資料匯入至 SQL 分割資料表](machine-learning/machine-learning-data-science-parallel-load-sql-partitioned-tables.md)。

5.  視需要瀏覽資料並建立功能。請注意，功能在資料庫資料表中無需具體化。僅注意建立這些功能的必要查詢。

6.  若有需要，請決定資料範例大小。

7.  登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。

8. 使用[讀取器][reader]模組，直接從 SQL Server 讀取資料。若有需要，將可擷取欄位、建立功能和取樣資料的必要查詢，直接貼入[讀取器][reader]查詢。

9. 從上傳的資料集開始的簡易 Azure ML 實驗流程。

### 將整個資料庫從內部部署 SQL Server 複製到 Azure SQL Database 的替代方法

![卸離本機 DB 和連結至 Azure 中的 SQL DB][7]

#### 其他 Azure 資源：Azure 虛擬機器 (SQL Server / IPython Notebook 伺服器)

若要在您的 SQL Server VM 中複寫整個 SQL Server 資料庫，您應將資料庫從一個位置/伺服器複製到另一個位置/伺服器 (假設資料庫可以暫時設定離線)。您可以在 SQL Server Management Studio Object Explorer GUI 中，或使用對等的 Transact-SQL 命令來執行此作業。

1. 在來源位置卸離資料庫。如需詳細資訊，請參閱 [卸離資料庫](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx)。
2. 在 Windows 檔案總管或 Windows 命令提示字元視窗中，將已卸離的資料庫檔案和記錄檔複製到位於 Azure 中 SQL Server VM 上的目標位置。
3. 將複製檔案連結至目標 SQL Server 執行個體。如需詳細資訊，請參閱 [連結資料庫](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx)。

[使用卸離和連結來移動資料庫 (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>案例 #7：本機檔案中的巨量資料，目標 Azure HDInsight Hadoop 叢集中的 Hive 資料庫

![本機目標 Hive 中的巨量資料][9]

#### 其他 Azure 資源：Azure HDInsight Hadoop 叢集和 Azure 虛擬機器 (IPython Notebook 伺服器)

1.  建立執行 IPython Notebook 伺服器的 Azure 虛擬機器。

2.  建立 Azure HDInsight Hadoop 叢集。

3.  (選擇性) 前置處理和清除資料。

    a.前置處理和清除 IPython Notebook 中的資料 (從 Azure Blob 存取資料)。

    b.若有需要，請將資料轉換為已清理的表格式格式。

    c.將資料儲存至 VM-local 檔案 (IPython Notebook 會在 VM 上執行，本機磁碟是指 VM 磁碟機)。

4.  將資料上傳至步驟 2 中已選取 Hadoop 叢集的預設容器。

5.  將資料載入 Azure HDInsight Hadoop 叢集中的 Hive 資料庫。

    a.登入 Hadoop 叢集的前端節點

    b.開啟 Hadoop 命令列。

    c.透過 Hadoop 命令列中的 `cd %hive_home%\bin` 命令進入 Hive 根目錄。

    d.執行 Hive 查詢以建立資料庫和資料表，並將資料從 Blob 儲存體載入 Hive 資料表。

 	> [AZURE.NOTE]如果資料太大，使用者可以建立包含分割區的 Hive 資料表接著，使用者可以在前端節點上的 Hadoop 命令列中使用 `for` 迴圈，以將資料載入使用資料分割的 Hive 分割資料表。

6.  在 Hadoop 命令列中，視需要瀏覽資料並建立功能。請注意，功能在資料庫資料表中無需具體化。僅注意建立這些功能的必要查詢。

	a.登入 Hadoop 叢集的前端節點

    b.開啟 Hadoop 命令列。

    c.透過 Hadoop 命令列中的 `cd %hive_home%\bin` 命令進入 Hive 根目錄。

	d.在 Hadoop 叢集前端節點的 Hadoop 命令列中執行 Hive 查詢，以瀏覽資料並視需要建立功能。

7.  若有需要，取樣資料以符合 Azure Machine Learning Studio 需求。

8.  登入 [Azure 機器學習 Studio](https://studio.azureml.net/)。

9. 使用[讀取器][reader]模組，直接從 `Hive Queries` 讀取資料。若有需要，將可擷取欄位、建立功能和取樣資料的必要查詢，直接貼入[讀取器][reader]查詢。

10. 從上傳的資料集開始的簡易 Azure ML 實驗流程。

## <a name="decisiontree"></a>用於案例選擇的決策樹
------------------------

下圖摘要說明上述案例，以及讓您連結到每個分項案例的「進階分析程序和技術」選擇。請注意，資料處理、資料探索、功能工程和取樣可能會出現在一或多個方法/環境中 (在來源、中繼和/或目標環境中)，且可以視需要反覆處理。圖表的目的只是說明一些可能的流程，並不提供詳盡的列舉。

![範例 DS 程序逐步解說案例][8]

### 進階分析實務範例

如需透過公用資料集運用進階分析程序和技術的端對端 Azure Machine Learning 逐步解說，請參閱：


* [進階分析程序和技術實務：使用 SQL Server](machine-learning/machine-learning-data-science-process-sql-walkthrough.md)。
* [進階分析程序和技術實務：使用 HDInsight Hadoop 叢集](machine-learning/machine-learning-data-science-process-hive-walkthrough.md)。


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=July15_HO1-->