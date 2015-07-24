<properties
   pageTitle="搭配使用 Azure Data Factory 與 SQL 資料倉儲 | Microsoft Azure"
   description="搭配使用 Azure Data Factory (ADF) 與 SQL 資料倉儲以便開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="lodipalm"/>

# 搭配使用 Azure Data Factory 與 SQL 資料倉儲

Azure Data Factory 提供完全受管理的方法，可供協調資料的傳輸和 SQL 資料倉儲上預存程序的執行。這可讓您更輕鬆地設定及排程 SQL 資料倉儲的複雜擷取、轉換和載入 (ETL) 程序。如需 Azure Data Factory 的更完整概觀，請參閱 [Azure Data Factory 文件][]。

## 資料移動 

Azure Data Factory 可讓資料在內部部署來源與不同的 Azure 服務之間移動。整體而言，目前與 Azure Data Factory 的整合可支援下列位置的雙向資料移動：

+ Azure Blob
+ Azure SQL Database
+ 內部部署 SQL Server
+ IaaS 上的 SQL Server

如需有關如何設定資料複製活動的資訊，請參閱[使用 Azure Data Factory 複製資料][]。

> [AZURE.NOTE]在這個階段，Azure Data Factory 無法用於將資料傳輸到 SQL 資料倉儲中的非 null 資料行。

## 預存程序
 如同可用來排程資料傳輸，Azure Data Factory 也可以用來協調預存程序的執行。這允許建立更多複雜的管線並擴充 Azure Data Factory 的功能，以利用 SQL 資料倉儲的運算能力。

## 後續步驟
如需整合概觀，請參閱 [SQL 資料倉儲整合概觀][]。如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀][]。

<!--Image references-->

<!--Article references-->

[使用 Azure Data Factory 複製資料]: ./data-factory-copy-activity/
[SQL 資料倉儲開發概觀]: ./sql-data-warehouse-overview-develop/
[SQL 資料倉儲整合概觀]: ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory 文件]: https://azure.microsoft.com/documentation/services/data-factory/

<!---HONumber=July15_HO1-->