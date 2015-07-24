<properties 
	title="Elastic database query – previewing May 2015" 
	pageTitle="彈性資料庫查詢：2015 年 5 月預覽功能" 
	description="發表彈性查詢功能" 
	metaKeywords="azure sql database elastic global queries" 
	services="sql-database" 
	documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="sidneyh" />

# 彈性資料庫查詢：2015 年 6 月預覽功能 

彈性資料庫查詢是 Azure SQL Database 在 2015 年 6 月底推出的新功能預覽。此功能導入的能力是，可以藉由與一個 Azure SQL Database 的單一連接，對多個資料庫執行 T-SQL 查詢。這可讓分區資料集 (定義使用彈性資料庫分區對應) 以單一整合式資料存放區的形式呈現。所有用於報告和資料整合用途的分散式查詢處理，均會在幕後進行。使用任何能夠與 SQL Database 通訊的驅動程式，包括 ADO.Net、ODBC、JDBC、Node.js、PHP 等，均可支援連接。

## 彈性資料庫查詢案例

彈性資料庫查詢的目標是要促進跨分區化資料層的報告案例；在此層中，多個資料庫或分區會提供資料列到某個 T-SQL 查詢的單一整體結果中。T-SQL 查詢可以由使用者或由應用程式直接撰寫，或透過連接到全域查詢彈性查詢資料庫的工具來間接撰寫。此方法對商業情報、報告或資料整合工具 (封裝無法使用彈性調整程式庫直接擴充的軟體) 而言特別實用。此方法也可讓您透過 SQL Server Management Studio 或 Visual Studio 發出的查詢，輕鬆存取分區化資料的整個集合，並支援從 Entity Framework 或其他 ORM 環境進行透明的多分區資料存取。圖 1 說明了此案例，其中全域查詢彈性資料庫的查詢會提供第二個不同的途徑，來將查詢提交到除了現有雲端應用程式 (可能會使用彈性調整程式庫) 以外的分區化資料層。

![彈性資料庫查詢][1]

案例可以依下列拓撲區分特性：

-	分區化資料層 (拓撲 1)：資料層已設計為適合分區化。會使用 (1) 彈性資料庫用戶端程式庫或 (2) 自行分區化，來執行和管理分區化。它的報告情況是，跨許多分區計算報表，而且對功能和連線能力的需求，遠遠超出彈性資料庫用戶端程式庫中的多分區查詢所能達到的。 

-	多資料庫設計 (拓撲 2)：在這裡，資料層已被垂直分割，讓不同類型的資料可位於不同的資料庫。它的報告情況是，根據分佈在多個資料庫的資料計算報表，但必須整合到一個整體的報表結果中。

彈性資料庫查詢的預覽版將會涵蓋這兩種情況。如果使用拓撲 1 的客戶是使用彈性資料庫用戶端程式庫來管理分區，就可以仰賴他們現有的分區對應。自行分區化的客戶必須使用其資料層適用的彈性資料庫工具來建立分區對應，才能使用這項預覽功能。使用拓撲 2 的客戶必須為他們的各個資料庫建立不同的分區對應。這些分區對應只會將每個點指向單一分區，然後該單一分區就可以針對跨分區查詢公開自己的資料表。

## 後續步驟
彈性資料庫查詢功能預計在 2015 年 6 月底推出預覽版屆時請回來查看本網頁以取得詳細資料，以及如何使用此功能的逐步指引。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
<!--anchors-->

<!---HONumber=58_postMigration-->