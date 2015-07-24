<properties
   pageTitle="在 SQL 資料倉儲中重新命名 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中重新命名物件和資料庫以開發解決方案的秘訣。"
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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# 在 SQL 資料倉儲中重新命名
SQL Server 支援分別透過預存程序 sp_rename 和 sp_renamedb 重新命名物件和資料庫。

SQL 資料倉儲使用 DDL 語法達成相同的目標。DDL 命令為 RENAME OBJECT 和 RENAME DATABASE。

## 重新命名物件

請務必了解，名稱只會影響重新命名的物件 - 沒有任何名稱變更的傳播。因此，使用物件舊名稱的任何檢視將會無效，直到物件的舊名稱建立為止。因此，您常常會看到 `RENAME OBJECT` 成對出現。

```
RENAME OBJECT product.item to item_old;
RENAME OBJECT product.item_new to item;
```

## 重新命名資料庫

重新命名資料庫和重新命名物件非常類似。

```
RENAME DATABASE AdventureWorks TO Contoso;
```

請務必記得，如果其他使用者已連線到物件或資料庫，或是正在使用，您就無法重新命名物件或資料庫。您可能必須先終止開啟的工作階段。若要終止工作階段，您必須使用 [KILL][] 命令。請小心使用 KILL。一旦執行了目標工作階段，此工作階段就會終止，而且所有未認可的工作都將復原。

> [AZURE.NOTE]SQL 資料倉儲中的工作階段會加上 'SID' 前置詞，您在叫用 KILL 命令時必須包含此前置詞和這個工作階段號碼。例如，KILL 'SID1234' 會終止工作階段 1234 - 假設您有正確的權限來執行它。

## 終止工作階段
若要重新命名資料庫，您可能必須終止連線到 SQL 資料倉儲的工作階段。下列查詢會產生不同的 KILL 命令清單，以清除連線 (儲存目前的工作階段)。

```
SELECT 'KILL '''+session_id+''''
FROM	sys.dm_pdw_exec_requests r
WHERE r.session_id <> SESSION_ID()
AND EXISTS
(	SELECT 	*
	FROM 	sys.dm_pdw_lock_waits w
	WHERE 	r.session_id = w.session_id
)
GROUP BY session_id
;
```

## 切換結構描述
如果目的是要變更物件所屬的結構描述，可以透過 `ALTER SCHEMA` 達成：

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```


## 後續步驟
如需更多開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/zh-tw/library/ms173730.aspx

<!--Other Web references-->
[Azure management portal]: http://portal.azure.com/

<!---HONumber=July15_HO1-->