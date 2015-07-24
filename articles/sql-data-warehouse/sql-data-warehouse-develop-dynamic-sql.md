<properties
   pageTitle="SQL 資料倉儲中的動態 SQL | Microsoft Azure"
   description="使用 Azure SQL 資料倉儲中的動態 SQL 以開發解決方案的秘訣。"
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

# SQL 資料倉儲中的動態 SQL
開發 SQL 資料倉儲的應用程式程式碼時，您可能會發現自己必須使用動態 sql，協助提供彈性的、泛型的且模組化的解決方案。不過，SQL 資料倉儲此時不支援 blob 資料類型。這可能會限制字串的大小，因為 blob 類型包括 varchar(max) 和 nvarchar(max) 類型。您可能會發現，您在建置必須執行之動態 SQL 程式碼的大型字串時，您使用了應用程式程式碼中的這些類型。

在這些情況下，您必須將程式碼細分成區塊，並改為使用 EXEC 陳述式。

簡化的範例如下所示：

```
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字串不是特別長，則您可以一如往常使用 [sp_executesql][]。


## 後續步驟
如需更多開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/zh-tw/library/ms188001.aspx

<!--Other Web references-->

<!---HONumber=July15_HO1-->