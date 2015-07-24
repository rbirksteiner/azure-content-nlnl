<properties
   pageTitle="使用 bcp 將資料載入 SQL 資料倉儲 | Microsoft Azure"
   description="瞭解 bcp 是什麼及如何用於資料倉儲案例。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/23/2015"
   ms.author="mausher;barbkess"/>


# 使用 bcp 載入資料
**[bcp][]** 是命令列大量載入公用程式，可讓您在 SQL Server、資料檔和 SQL 資料倉儲之間複製資料。使用 bcp 公用程式將大量資料列匯入 SQL 資料倉儲資料表，或將 SQL Server 資料表中的資料匯出成資料檔案。除非與 queryout 選項一起使用，否則 bcp 不需 TRANSACT-SQL 方面的知識。

bcp 是將較小的資料集移入和移出 SQL 資料倉儲資料庫的一種快速又輕鬆的方式。透過 bcp 載入/擷取時，建議的確切資料量取決於您對 Azure 資料中心的網路連線。一般而言，可載入和擷取維度資料表，但載入或擷取相當大的事實資料表時可能需要很長的時間。

bcp 可讓您：- 使用簡單的命令列公用程式，將資料載入 SQL 資料倉儲。- 使用簡單的命令列公用程式，從 SQL 資料倉儲擷取資料。

本教學課程將示範如何：- 使用 bcp in 命令將資料匯入資料表 - 使用 bcp out 命令從資料表匯出資料

## 必要條件
若要逐步執行本教學課程，您需要：- SQL 資料倉儲資料庫 - 已安裝 bcp 命令列公用程式 - 已安裝 SQLCMD 命令 列公用程式

>[AZURE.NOTE]您可以從 [Microsoft 下載中心][]下載 bcp 和 sqlcmd 公用程式。

##將資料匯入 SQL 資料倉儲
在本教學課程中，您將在 Azure SQL 資料倉儲中建立資料表，並將資料匯入資料表。

### 步驟 1：在 Azure SQL 資料倉儲中建立資料表
從命令提示字元中，使用下列命令連接到您的執行個體 (請適當地取代其中的值)：

```
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I
```
一旦連線之後，在 sqlcmd 提示字元上複製下列資料表指令碼，然後按 Enter 鍵：

```
CREATE TABLE DimDate2 (DateId INT NOT NULL, CalendarQuarter TINYINT NOT NULL, FiscalQuarter TINYINT NOT NULL);
```

在下一行中，輸入 GO 批次結束字元，然後按 Enter 鍵來執行陳述式：

```
GO
```

### 步驟 2：建立來源資料檔

開啟 [記事本]，並將下列幾行資料複製到新的檔案。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

將此檔案儲存到本機暫存目錄，C:\Temp\DimDate2.txt。

### 步驟 3：連接並匯入資料
在 bcp 中，您可以使用下列命令來連接並匯入資料 (請適當地取代其中的值)：

```
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -w -t
```

您像先前一樣使用 sqlcmd 來連接，並執行下列 TSQL 命令，以確認已載入資料：

```
SELECT * FROM DimDate2 ORDER BY 1;
GO
```

這應該會傳回下列結果：

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

## 從 SQL 資料倉儲匯出資料
在本教學課程中，您將從 Azure SQL 資料倉儲中的資料表建立資料檔案。我們會將上面建立的資料匯出至新的資料檔案，稱為 DimDate2_export.txt。

### 步驟 1：匯出資料

在 bcp 公用程式中，您可以使用下列命令來連接並匯出資料 (請適當地取代其中的值)：

```
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -w -t
```
您可以開啟新的檔案來確認資料已正確匯出。檔案中的資料應該符合以下文字：

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

>[AZURE.NOTE]由於分散式系統的本質，資料順序在不同 SQL 資料倉儲資料庫中可能不相同。您可以選擇性地使用 queryout 參數，指定要執行的 Transact-SQL 查詢。

## 後續步驟
如需載入的概觀，請參閱[將資料載入 SQL 資料倉儲][]。如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀][]。

<!--Image references-->

<!--Article references-->

[將資料載入 SQL 資料倉儲]: ./sql-data-warehouse-overview-load/
[SQL 資料倉儲開發概觀]: ./sql-data-warehouse-overview-develop/

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx


<!--Other Web references-->
[Microsoft 下載中心]: http://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=July15_HO1-->