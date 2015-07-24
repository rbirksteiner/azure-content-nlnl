<properties
   pageTitle="SQL 資料倉儲中的預存程序 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中實作預存程序以便開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL 資料倉儲中的預存程序 

SQL 資料倉儲支援許多 SQL Server 中具備的 TRANSACT-SQL 功能。更重要的是，我們會想要運用相應放大特定功能，將解決方案的效能最大化。

不過，為了維護 SQL 資料倉儲的規模和效能，還有一些具有行為差異的功能以及其他不支援的功能。

本文說明如何實作 SQL 資料倉儲中的預存程序。

## 預存程序簡介
預存程序很適合用來封裝您的 SQL 程式碼；將它儲存在資料倉儲中您的資料附近。藉由將程式碼封裝成可管理的單位，預存程序協助開發人員將其解決方案模組化；促使程式碼有更大的可重複使用性。每個預存程序也可接受參數，使其更具彈性。

SQL 資料倉儲提供簡化且更簡化的預存程序實作。相較於 SQL Server，最大差異是預存程序不是預先編譯的程式碼。在資料倉儲中，我們通常比較不在乎編譯時間。比較重要的是在對大型資料磁碟區操作時，正確地最佳化預存程序程式碼。目標是要節省時數、分鐘數和秒數，而不是毫秒數。因此，將預存程序視為 SQL 邏輯的容器更有幫助。
 
當 SQL 資料倉儲執行預存程序時，SQL 陳述式會在執行階段進行剖析、轉譯和最佳化。在此過程中，每個陳述式都會轉換為分散式查詢。實際針對資料執行的 SQL 程式碼與提交的查詢不同。

## 巢狀預存程序
當預存程序呼叫其他預存程序或執行動態 sql 時，內部預存程序或程式碼叫用據稱就是巢狀。

SQL 資料倉儲最多支援 8 個巢狀層級。這與 SQL Server 稍有不同。SQL Server 中的巢狀層級為 32。

最上層預存程序呼叫等同於巢狀層級 1

```
EXEC prc_nesting
``` 
如果預存程序也進行另一個 EXEC 呼叫，這會使巢狀層級提升為 2 ```
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
``` 如果第二個程序接著執行動態 sql，這會使巢狀層級提升為 3 ```
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

請注意，SQL 資料倉儲目前不支援 @@NESTLEVEL。您必須自行追蹤自己的巢狀層級。您不太可能會達到 8 個巢狀層級的限制，但如果達到，您必須重新處理您的程式碼並將其「壓平合併」，使其符合這項限制。

## INSERT..EXECUTE
SQL 資料倉儲不允許您透過 INSERT 陳述式取用預存程序的結果集。不過，您可以使用另一個方法。

如需如何這麼做的範例，請參閱有關[暫存資料表]的文章。

## 限制

在 SQL 資料倉儲中不會實作 TRANSACT-SQL 預存程序的有些層面。

如下：

- 暫存預存程序
- 編號預存程序
- 擴充預存程序
- CLR 預存程序
- 加密選項
- 複寫選項
- 資料表值參數
- 唯讀參數
- 預設參數
- 執行內容
- return 陳述式

## 後續步驟
如需更多開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[暫存資料表]: sql-data-warehouse-develop-temporary-tables.md
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/zh-tw/library/ms187371.aspx

<!--Other Web references-->

<!---HONumber=July15_HO1-->