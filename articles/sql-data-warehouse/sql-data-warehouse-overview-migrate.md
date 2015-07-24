<properties
   pageTitle="將您的解決方案移轉至 SQL 資料倉儲 | Microsoft Azure"
   description="將您的解決方案帶入 Azure SQL 資料倉儲平台的移轉指導。"
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

# 將您的解決方案移轉至 SQL 資料倉儲

SQL 資料倉儲是一個能夠彈性調整以符合您需求的分散式資料庫系統。為了維護效能與延展性起見，不會在 SQL 資料倉儲內實作 SQL Server 的所有功能。下列移轉主題介紹的是將解決方案移轉到 SQL 資料倉儲的一些重要因素。因為針對延展性設計資料倉儲帶來了不同的設計模式，因此傳統的方法不一定最適合。您可能會發現您需調整解決方案，以確保能夠充分利用 SQL 資料倉儲所提供的分散式平台。

另外也請務必記得，SQL 資料倉儲是以 Microsoft Azure 為基礎的平台。因此，移轉過程很有可能需將您的資料傳輸至雲端。資料傳輸應另行討論且必須謹慎考量，尤其是在磁碟區增加時。傳輸資料也不應與載入資料混淆，後者又是另外一個主題。

## 移轉指導
開始移轉之前，請確定您已詳讀這些文章，以確保了解一些產品的差異之處和基本概念。

- [遷移結構描述][]
- [遷移資料][]
- [遷移程式碼][]
 
## 後續步驟
如需其他開發祕訣，請參閱[開發概觀][]。

您也可以檢視 [Transact-SQL 參考資料][]，以取得更多資訊。

最後，請查看[載入概觀][]，其中討論各種資料載入選項並提供逐步指導。

<!--Image references-->

<!--Article references-->
[遷移結構描述]: sql-data-warehouse-migrate-schema.md
[遷移資料]: sql-data-warehouse-migrate-data.md
[遷移程式碼]: sql-data-warehouse-migrate-code.md

[開發概觀]: sql-data-warehouse-overview-develop.md
[載入概觀]: sql-data-warehouse-overview-load.md
[Transact-SQL 參考資料]: sql-data-warehouse-overview-migrate.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=July15_HO1-->