<properties 
	pageTitle="DocumentDB 限制與配額 | Azure" 
	description="了解 DocumentDB 強制執行的限制和配額。" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="mimig"/>


#DocumentDB 限制與配額

下表說明 DocumentDB 強制執行的限制和配額。以星號 (*) 列出的配額[可以透過聯絡 Azure 支援人員來進行調整](documentdb-increase-limits.md)。

|實體 |配額 (標準提供項目)|
|-------|--------|
|資料庫帳戶* |5
|每個資料庫帳戶的資料庫數目 |100
|每個資料庫帳戶的使用者數目 - 跨所有資料庫 |500,000
|每個資料庫帳戶的權限數目 - 跨所有資料庫 |2,000,000
|每個資料庫帳戶的附件儲存體 (預覽功能) |2 GB
|每個集合的每秒要求單位上限 |2500
|每個集合的預存程序、觸發程序和 UDF 數目* |各 25 個
|預存程序和觸發程序的執行時間上限 |5 seconds
|佈建的文件儲存體/集合 |10 GB
|每個資料庫帳戶的集合上限* |100
|每個資料庫的文件儲存體上限 (100 個集合)* |1 TB
|Id 屬性的長度上限 |255 字元
|每個頁面的項目數上限 |1000
|文件和附件的要求大小上限 |512 KB
|預存程序、觸發程序和 UDF 的要求大小上限 |512 KB
|回應大小上限 |1MB
|String |所有字串都必須符合 UTF-8 編碼。因為 UTF-8 是寬度不一定的編碼，所以字串大小是使用 UTF-8 位元組來決定。
|屬性或值的長度上限 |無實際限制
|每個查詢的 UDF 數目上限* |1
|每個查詢的內建函數數目上限 |無實際限制
|每個查詢的 JOIN 數目上限* |2
|每個查詢的 AND 子句數目上限* |5
|每個查詢的 OR 子句數目上限* |5
|每個 IN 運算式的值數目上限* |100
|每分鐘建立集合的數目上限* |5
|每分鐘縮放作業的數目上限* |5
 

<!---HONumber=July15_HO1-->