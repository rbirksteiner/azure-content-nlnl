<properties 
	title="Elastic database jobs overview" 
	pageTitle="彈性資料庫工作概觀" 
	description="說明彈性資料庫工作服務" 
	metaKeywords="azure sql database elastic databases" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="sidneyh" />

# 彈性資料庫工作概觀

**彈性資料庫工作** (預覽) 可讓您對[彈性資料庫集區 (預覽)](sql-database-elastic-pool.md) 中的所有資料庫執行 T-SQL 指令碼 (工作)。例如，您可以輕鬆地更新每個資料庫中的結構描述，以包含新的資料表。通常，您必須個別連接到每個資料庫，以執行 T-SQL 陳述式或執行其他管理工作。**彈性資料庫工作**會處理登入工作，並為您可靠地執行指令碼，同時也會記錄每個資料庫的執行狀態。如需安裝預覽的指示，請移至[安裝彈性資料庫工作元件](sql-database-elastic-jobs-service-installation.md)。

![彈性資料庫工作服務][1]

## 優點

* 定義、維護及保存要在彈性資料庫集區中執行的 T-SQL 指令碼
* 使用自動重試大規模且可靠地執行 T-SQL 指令碼
* 追蹤工作執行狀態

## 案例

* 效能管理工作，例如部署新的結構描述
* 更新參考資料，例如所有資料庫通用的產品資訊
* 重建索引以提升查詢效能

## 工作的運作方式

1.	安裝彈性資料庫工作所使用的服務。請參閱[安裝彈性資料庫工作](sql-database-elastic-jobs-service-installation.md)。如果安裝失敗，請參閱[如何解除安裝](sql-database-elastic-jobs-uninstall.md)。
2.	[將使用者加入每個資料庫](sql-database-elastic-jobs-add-logins-to-dbs.md)，以設定要執行工作的彈性資料庫集區。
3.	從彈性資料庫工作集區檢視，按一下 [建立工作]。
4.	輸入工作控制資料庫 (工作的中繼資料儲存體) 的使用者名稱和密碼。(當您安裝彈性資料庫工作時，會建立使用者名稱和密碼)。
5.	在**建立工作**刀鋒視窗中，將工作名稱、使用者名稱和密碼輸入到目標資料庫 (具備讓指令碼成功執行的足夠權限)，並貼上或輸入 T-SQL 指令碼。
6.	按一下 [執行]，這項工作隨即會對每個資料庫執行指令碼。
7.	[管理工作] 檢視可讓您查看所有執行中的工作，或已執行和處於最新執行狀態的工作。
8.	按一下任何工作，以查看工作執行詳細資料和每個資料庫的工作執行狀態。
9.	如果工作失敗，請按一下其名稱以查看錯誤記錄檔。

## 元件和定價 

下列元件共同建立允許臨機執行管理工作的 Azure 雲端服務。這些元件會於安裝期間在您的訂用帳戶中自動安裝和設定。您可以識別服務，因為這些服務都有自動產生的相同名稱。這個名稱是唯一的，包含前置詞 "edj"，後面接著隨機產生的 21 個字元。

* **Azure 雲端服務**：彈性資料庫工作 (預覽) 會以客戶託管的 Azure 雲端服務來傳遞，以執行所要求的工作。您可以從入口網站，在 Microsoft Azure 訂用帳戶中部署及託管服務。預設至少會使用兩個背景工作角色來執行已部署的服務，以取得高可用性。每個背景工作角色 (ElasticDatabaseJobWorker) 都會以預設大小在 A0 執行個體上執行。如需定價，請參閱[雲端服務定價](http://azure.microsoft.com/pricing/details/cloud-services/)。 
* **Azure SQL Database**：服務會使用稱為**控制資料庫**的 Azure SQL Database 來儲存所有的工作中繼資料。預設服務層為 S0。如需定價，請參閱 [SQL Database 定價](http://azure.microsoft.com/pricing/details/sql-database/)。
* **Azure 服務匯流排**：Azure 服務匯流排可協調 Azure 雲端服務內的工作。請參閱[服務匯流排定價](http://azure.microsoft.com/pricing/details/service-bus/)。
* **Azure 儲存體**：Azure 儲存體帳戶可用來儲存事件中需要進一步偵錯之問題的診斷輸出記錄 ([Azure 診斷](../cloud-services-dotnet-diagnostics.md)的常見作法)。如需定價，請參閱 [Azure 儲存體定價](http://azure.microsoft.com/pricing/details/storage/)。

## 後續步驟
[安裝元件](sql-database-elastic-jobs-service-installation.md)，然後[建立記錄檔並加入集區中的每個資料庫](sql-database-elastic-jobs-add-logins-to-dbs.md)。若要進一步了解工作的建立和管理，請參閱[建立及管理彈性資料庫工作](sql-database-elastic-jobs-create-and-manage.md)。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=58_postMigration-->