<properties 
	pageTitle="處理 SQL Azure 的資料 |Microsoft Azure" 
	description="處理 SQL Azure 的資料" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="fashah" 
	manager="paulettm" 
	editor="" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="fashah;garye" />

#<a name="heading"></a>在 Azure 上處理 SQL Server 虛擬機器中的資料

本文件涵蓋探索資料，並針對儲存於 Azure 上 SQL Server VM 中的資料產生功能。您可以使用下列方式來完成此動作：

1. [使用 SQL](#sql)
2. [使用類似 Python 的程式設計語言](#python) 


**注意**
>本文件中的 SQL 陳述式範例假設資料位於 SQL Server 中。如果不是，請參閱雲端資料科學程序圖，以了解如何將資料移至 SQL Server 中。

###<a name="SQL"></a>使用 SQL

我們將在本節中使用 SQL，來說明下列資料有爭議的工作：

1. [資料探索](#sql-dataexploration)
2. [功能產生](#sql-featuregen)

####<a name="sql-dataexploration"></a>資料探索
以下是數個 SQL 指令碼範例，可用來探索儲存於 SQL Server 中的資料。

**注意**
> 如需實用範例，您可以使用 [NYC 計程車資料集](http://www.andresmh.com/nyctaxitrips/)，並參考標題為[使用 IPython Notebook 和 SQL Server 來處理有爭議的 NYC 資料](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb)的 IPNB，以進行端對端逐步解說。

1. 取得每天的觀察計數

	`SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)`

2. 取得類別資料行中的層級

	`select  distinct <column_name> from <databasename>`

3. 取得兩個類別資料行組合中的層級數目

	`select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. 取得數值資料行的分佈

	`select <column_name>, count(*) from <tablename> group by <column_name>`


####<a name="sql-featuregen"></a>功能產生

在本節中，我們將說明使用 SQL 產生功能的方式：

1. [以計數為基礎的功能產生](#sql-countfeature)
2. [分類收納功能產生](#sql-binningfeature)
3. [從單一資料行衍生功能](#sql-featurerollout)

**注意**
>一旦產生額外功能之後，就可以將它們當成資料行新增至現有的資料表，或是建立具有其他功能和主索引鍵的新資料表 (可與原始資料表聯結)。

####<a name="sql-countfeature"></a>以計數為基礎的功能產生

本文件示範兩種產生計數功能的方法。第一種方法會使用條件式加總，而第二種方法會使用 'where' 子句。這些接著可與原始資料表聯結 (使用主索引鍵資料行)，以具備計數功能及原始資料。

	select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

	select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
	where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

####<a name="sql-binningfeature"></a>分類收納功能產生

下列範例將示範如何藉由分類收納 (使用 5 個分類收納組) 可改用來做為功能的數值資料行，來產生分類收納功能：

	`SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


####<a name="sql-featurerollout"></a>從單一資料行衍生功能

本節示範如何在資料表中衍生單一資料行來產生額外功能。此範例假設您正嘗試從中產生功能的資料表中具有緯度或經度資料行。

以下是有關經緯度位置資料的簡短入門指南 (源自 stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`)。這有助於您在將功能化位置欄位之前先行了解：

- 正負號告訴我們是否位於地球的北方或南方、東方或西方。
- 非零的數百個位數告訴我們使用的是經度，而不是緯度！
- 數十個位數可提供大約 1,000 公里的位置。它會為我們提供身處哪個大陸或海洋的實用資訊。
- 單位數 (一個十進位度數) 提供一個最多可達 111 公里 (60 海浬，大約 69 英哩) 的位置。它會告知我們大致上位於哪一個大的州或國家/地區中。
- 第一個小數點最多可達 11.1 km：它能夠分辨某一個大型縣 (市) 的位置與鄰近的大型縣 (市)。
- 第二個小數位數最多可達 1.1 km：它可以將某一個村莊與下一個村莊分隔開來。
- 第三個小數位數最多可達 110 m：它可以識別大型農場或學術機構校區。
- 第四個小數位數最多可達 11 m：它可以識別某一塊土地。它相當於未修正的 GPS 單位且無干擾的一般精確度。
- 第五個小數位數最多可達 1.1 m：它會分辨彼此的樹狀結構。您只能使用微分校正來達到此層級利用商業 GPS 單位所達到的精確度。
- 第六個小數位數最多可達 0.11 m：您可以使用此項目來詳細配置結構，其適用於設計環境和建置道路。比起足以追蹤冰河和河流的移動，這應該是更好的方式。您可以採用含有 GPS 的精心度量 (例如，微分校正的 GPS) 來達成此項目。

您可以使用下列方式來將位置資訊功能化，以分隔出區域、位置及縣 (市) 資訊。請注意，一次也可以呼叫 REST 端點，例如，可在 `https://msdn.microsoft.com/library/ff701710.aspx` 上取得的 Bing Maps API，以取得區域或學區資訊。

	select 
		<location_columnname>
		,round(<location_columnname>,0) as l1		
		,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end 	
		,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end 	
		,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end 	
		,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end 	
		,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end 	
		,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end 	
	from <tablename>

上述以位置為基礎的功能可進一步用來產生其他計數功能，如先前所述。


**秘訣**

> 1. 您可以使用所選擇的語言，利用程式設計方式插入記錄。您可能需要插入區塊中的資料以改善寫入效率[在此處看看如何使用 pyodbc 來執行此動作的範例](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)。 
> 2. 另一個替代方式是使用 [BCP 公用程式](https://msdn.microsoft.com/library/ms162802.aspx)在資料庫中插入資料

####<a name="sql-aml"></a>連接到 Azure Machine Learning

新產生的功能可當成資料行新增至現有資料表或儲存於新的資料表中，並與原始資料表加以聯結以進行機器學習服務。如果已經建立功能，就可以使用 Azure ML 中的[讀取器][reader]模組來產生或存取功能，如下所示：

![azureml 讀取器][1]

###<a name="python"></a>使用類似 Python 的程式設計語言

當資料位於 SQL Server 時，使用 Python 來探索資料和產生功能，類似於使用 Python 來處理 Azure Blob 中的資料，如[此處](machine-learning-data-science-process-data-blob.md)所述。資料必須從資料庫載入 Pandas 資料框架，然後就能進一步處理。我們將在本節中說明連接到資料庫以及將資料載入資料框架的程序。

下列連接字串格式可用來使用 pyodbc (使用您的特定值來取代伺服器名稱、dbname、使用者名稱和密碼)，從 Python 連接到 SQL Server 資料庫：

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Python 中的 [Pandas 程式庫](http://pandas.pydata.org/)提供一組豐富的資料結構和資料分析工具，可用來對 Python 程式設計進行資料操作。下列程式碼會將從 SQL Server 資料庫傳回的結果讀取至 Pandas 資料框架：

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

現在您可以利用[在資料科學環境中處理 Azure Blob 資料](machine-learning-data-science-process-data-blob.md)主題中說明的方式來使用 Pandas 資料框架。

### 作用中的 Azure 資料科學範例

如需使用公用資料集之 Azure 資料科學程序的端對端逐步解說範例，請參閱[作用中的 Azure 資料科學範例](machine-learning-data-science-process-sql-walkthrough.md)。

[1]: ./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=July15_HO1-->