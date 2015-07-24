<properties 
	pageTitle="將資料移至 Azure 上的 SQL Server | Microsoft Azure" 
	description="將資料移至 Azure 上的 SQL Server" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="fashah;garye;mohabib;bradsev" />

#將資料移至 Azure 上的 SQL Server

本文件說明如何從一般檔案 (csv/tsv) 或內部部署的 SQL Server，將資料移至 Azure 上的 SQL Server。這項工作是 Azure Machine Learning 提供的進階分析程序和技術 (ADAPT) 的一部分。


<table>

<tr>
<td><b>來源</b></td>
<td colspan="2" align="center"><b>目的地</b></td>
</tr>

<tr>
  <td></td>
  <td><b>Azure 上的 SQL Server VM</b></td>
</tr>

<tr>
  <td><b>一般檔案</b></td>  
  <td>
    1.<a href="#insert-tables-bcp">命令列大量複製公用程式 (BCP)</a><br>
    2.<a href="#insert-tables-bulkquery">大量插入 SQL 查詢</a><br>
    3.<a href="#sql-builtin-utilities">SQL Server 中的圖形化內建公用程式</a>
  </td>
</tr>
<tr>
  <td><b>內部部署的 SQL Server</b></td>
  <td>
    1.<a href="#export-flat-file">匯出至一般檔案</a><br>
    2.<a href="#sql-migration">SQL Database 移轉精靈</a> <br>    
    3.<a href="#sql-backup">資料庫備份和還原</a> <br>
  </td>
</tr>
</table>

請注意，本文件假設 SQL 命令是從 SQL Server Management Studio 或 Visual Studio 資料庫總管中執行。

> [AZURE.TIP]或者，您可以使用 [Azure Data Factory](https://azure.microsoft.com/zh-tw/services/data-factory/) 建立並排程管線，以將資料移至 Azure 上的 SQL Server VM。如需詳細資訊，請參閱[使用 Azure Data Factory 複製資料 (複製活動)](../data-factory/data-factory-copy-activity.md)。


## <a name="sqlonazurevm"></a>將資料移至 Azure 上的 SQL Server VM

本節說明將資料移至 Azure 上 SQL Server VM 的程序。如果您尚未設定 SQL Server VM，請依照[將 Azure SQL Server 虛擬機器設定為 IPython Notebook 伺服器供進階分析使用](machine-learning-data-science-setup-sql-server-virtual-machine.md)中所述，針對進階分析佈建新的 SQL Server 虛擬機器。

本文將說明從下列資料來源移動資料的方式：
  
1. [從一般檔案](#filesource_to_sqlonazurevm) 
2. [從內部部署的 SQL Server](#sqlonprem_to_sqlonazurevm)


### <a name="filesource_to_sqlonazurevm"></a>檔案來源

如果您的資料位於一般檔案 (使用資料列或資料行格式排列) 中，可透過下列方法，將它移到 Azure 上的 SQL Server VM：

1. [命令列大量複製公用程式 (BCP)](#insert-tables-bcp) 
2. [大量插入 SQL 查詢](#insert-tables-bulkquery)
3. [SQL Server 中的圖形化內建公用程式 (匯入/匯出，SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>命令列大量複製公用程式 (BCP)

BCP 是與 SQL Server 一起安裝的命令列公用程式，是最快速移動資料的其中一種方式。它的運作方式可跨越這三個 SQL Server 版本 (內部部署的 SQL Server、SQL Azure，以及 Azure 上的 SQL Server VM)。

> [AZURE.NOTE]**我應該針對 BCP 將資料放置於何處？** 雖然並非必要，但是，擁有包含與目標 SQL Server 位於相同電腦上之來源資料的檔案可讓傳輸速度 (網路速度與本機磁碟 IO 速度) 變得更快。您可以使用像是 [AZCopy](../storage-use-azcopy.md)、[Azure 儲存體總管](https://azurestorageexplorer.codeplex.com/)或透過遠端桌面通訊協定 (RDP) 進行的 Windows 複製/貼上等各種檔案複製工具，將包含資料的一般檔案移到安裝 SQL Server 的電腦上。

1. 確定已在目標 SQL Server 資料庫上建立資料庫和資料表。以下是如何使用 `Create Database` 和 `Create Table` 命令來執行此作業的範例：

		CREATE DATABASE <database_name>
	
		CREATE TABLE <tablename>
		(
			<columnname1> <datatype> <constraint>,
			<columnname2> <datatype> <constraint>,
			<columnname3> <datatype> <constraint>
		) 
	
2. 從安裝 BCP 的電腦命令列中發出下列命令，以產生說明資料表結構描述的格式檔案。

	`bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

3. 使用 BCP 命令來將資料插入資料庫，如下所示。若假設 SQL Server 安裝於同一部電腦上，這應該就能從命令列順利運作：

	`bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **最佳化 BCP 插入**：請參閱[最佳化大量匯入的指導方針](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx)這篇文章，以將這類插入最佳化。

#### <a name="insert-tables-bulkquery-parallel"></a>平行插入以進行更快速的資料移動

如果您要移動的資料很大，就可以在 PowerShell 指令碼中同時平行執行多個 BCP 命令來加快運作速度。

> [AZURE.NOTE]**巨量資料擷取**：若要將大型和超大型資料集的資料載入予以最佳化，可以使用多個檔案群組和資料分割資料表來進行邏輯與實體資料庫資料表的資料分割。如需關於建立和載入資料至資料分割資料表的詳細資訊，請參閱[平行載入 SQL 資料分割資料表](machine-learning-data-science-parallel-load-sql-partitioned-tables.md)。


下列 PowerShell 指令碼範例將示範使用 BCP 平行插入：
	
	$NO_OF_PARALLEL_JOBS=2

	 Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
	 # Define what each job does
	   $ScriptBlock = {
		   param($partitionnumber)

		   #Explictly using SQL username password
		   bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

			#Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
			#bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
	  }
	

	# Background processing of all partitions
	for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
	{
	  Write-Debug "Submit loading partition # $i"
	  Start-Job $ScriptBlock -Arg $i	  
	}
	

	# Wait for it all to complete
	While (Get-Job -State "Running")
	{
	  Start-Sleep 10
	  Get-Job
	}
	
	# Getting the information back from the jobs
	Get-Job | Receive-Job
	Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>大量插入 SQL 查詢

[大量插入 SQL 查詢](https://msdn.microsoft.com/library/ms188365)可用來將資料從以資料列/資料行為基礎的檔案匯入資料庫 ([此處](https://msdn.microsoft.com/library/ms188609)說明支援的類型)。

此處提供一些大量插入的命令範例，如下所示：

1. 分析您的資料，並在匯入之前設定任何自訂選項，以確定 SQL Server 資料庫會針對任何特殊欄位 (例如日期) 假設相同的格式。以下範例示範如何將日期格式設為「年-月-日」 (如果您的資料包含「年-月-日」格式的日期)：

		SET DATEFORMAT ymd;	
	
2. 使用大量匯入陳述式來匯入資料：

    	BULK INSERT <tablename>
    	FROM	
    	'<datafilename>'
    	WITH 
    	(
		FirstRow=2,
    	FIELDTERMINATOR =',', --this should be column separator in your data
    	ROWTERMINATOR ='\n'   --this should be the row separator in your data
    	)
 	  

### <a name="sql-builtin-utilities"></a>SQL Server 中的內建公用程式

您可以使用 SQL Server Integration Services (SSIS)，將資料從一般檔案匯入 Azure 上的 SQL Server VM。SSIS 適用於兩種 Studio 環境。如需詳細資料，請參閱 [Integration Services (SSIS) 和 Studio 環境](https://technet.microsoft.com/library/ms140028.aspx)：

- 如需 SQL Server Data Tools 的詳細資料，請參閱 [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
- 如需匯入/匯出精靈的詳細資料，請參閱 [SQL Server 匯入和匯出精靈](https://msdn.microsoft.com/library/ms141209.aspx)

### <a name="sqlonprem_to_sqlonazurevm"></a>從內部部署的 SQL Server 移動資料

您可以從內部部署的 SQL Server 移動資料，如下所示：

1. [匯出至一般檔案](#export-flat-file) 
2. [SQL Database 移轉精靈](#sql-migration)
3. [資料庫備份和還原](#sql-backup)

我們將在下方說明這每一項內容：

#### <a name="export-flat-file"></a>匯出至一般檔案

有各種方法可用來從內部部署的 SQL Server 大量匯出資料，如[此處](https://msdn.microsoft.com/library/ms175937.aspx)所述。本文件將提供大量複製程式 (BCP) 做為範例。一旦將資料匯出至一般檔案之後，就可以使用大量匯入功能來將它匯入另一部 SQL Server。

1. 使用 BCP 公用程式，從內部部署的 SQL Server 將資料匯出至檔案，如下所示

	`bcp dbname..tablename out datafile.tsv -S	servername\sqlinstancename -T -t \t -t \n -c`

2. 針對步驟 1 中匯出的資料表結構描述使用 `create database` 和 `create table`，在 Azure 的 SQL Server VM 上建立資料庫和資料表。

3. 建立格式檔案，用以說明要匯出/匯入之資料的資料表結構描述。[此處](https://msdn.microsoft.com/library/ms191516.aspx)說明格式檔案的詳細資料。

	從 SQL Server 電腦執行 BCP 時產生格式檔案

		bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

	針對 SQL Server 遠端執行 BCP 時產生格式檔案

		bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
		
	
4. 使用[從檔案來源移動資料](#filesource_to_sqlonazurevm)一節中所述的任何方法，將一般檔案中的資料移至 SQL Server。

#### <a name="sql-migration"></a>SQL Database 移轉精靈

[SQL Server 資料庫移轉精靈](http://sqlazuremw.codeplex.com/)提供方便使用的方式，讓您在兩個 SQL Server 執行個體之間移動資料。它讓使用者能夠對應來源與目的地資料表之間的資料結構描述，選擇資料行類型和其他各種功能。它會在幕後使用大量複製 (BCP) 功能。SQL Database 移轉精靈歡迎畫面的螢幕擷取畫面如下所示。

![SQL Server 移轉精靈][2]

#### <a name="sql-backup"></a>資料庫備份和還原

SQL Server 支援：

1. [資料庫備份和還原功能](https://msdn.microsoft.com/library/ms187048.aspx) (兩者皆可為本機檔案或以 bacpac 匯出至 Blob) 和[資料層應用程式](https://msdn.microsoft.com/library/ee210546.aspx) (使用 bacpac)。 
2. 使用複製的資料庫直接在 Azure 上建立 SQL Server VM，或者複製到現有 SQL Azure 資料庫的能力。如需詳細資料，請參閱[使用複製資料庫精靈](https://msdn.microsoft.com/library/ms188664.aspx)。 

SQL Server Management Studio 的資料庫備份/還原選項的螢幕擷取畫面如下所示。

![SQL Server 匯入工具][1]


[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png

 

<!---HONumber=July15_HO1-->