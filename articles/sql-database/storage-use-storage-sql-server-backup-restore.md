<properties 
	pageTitle="如何使用 Azure 儲存體進行 SQL Server 備份與還原 | Azure" 
	description="將 SQL Server 和 SQL Database 備份到 Azure 儲存體。說明將 SQL 資料庫備份到 Azure 儲存體的優點，以及需要哪些 SQL Server 和 Azure 儲存體元件" 
	services="sql-database, virtual-machines" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor="tysonn"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="jeffreyg"/>



# 如何使用 Azure 儲存體進行 SQL Server 備份與還原

## 概觀

在 SQL Server 2012 SP1 CU2 中發表了能夠將 SQL Server 備份寫入 Azure Blob 儲存體服務的功能。您可以使用此功能，從內部部署 SQL Server 資料庫或 Azure 虛擬機器中的 SQL Server 資料庫，備份及還原 Azure Blob 服務。備份至雲端提供的好處包括可用性、無限制的地理區域備援異地儲存體，以及輕鬆地與雲端之間來回移轉資料。在此版本中，您可以使用 T-SQL 或 SMO 發出 BACKUP 或 RESTORE 陳述式。

## 使用 Azure Blob 服務進行 SQL Server 備份的好處

儲存體管理、儲存體故障的風險、異地儲存體的存取，以及設定裝置等，是一些一般性的備份挑戰。針對在 Azure 虛擬機器中執行的 SQL Server，還有其他的挑戰如設定及備份 VHD，或設定連接的磁碟機。以下列出使用 Azure Blob 儲存體服務的儲存體進行 SQL Server 備份的部分重要好處：

* 有彈性、可靠且無限制的異地儲存體：將您的備份存放在 Azure Blob 服務上可能是方便、有彈性而且容易存取的異地選項。為您的 SQL Server 備份建立異地儲存體只需要修改現有的指令碼/工作。異地儲存體應該通常要離生產資料庫位置夠遠，以避免單一災害同時影響異地和生產資料庫位置。藉由選擇地理區域備援複寫 Blob 儲存體，在發生可能影響整個區域的災害時，您會有額外的一層保護。此外，可以隨時隨地取得備份，並且可以輕鬆存取以進行還原。
* 備份封存：Azure Blob 儲存體服務提供比常用的磁帶選項更好的替代選項來封存備份。磁帶儲存體可能需要實體運輸到異地設施，以及保護媒體的措施。將備份存放在 Azure Blob 儲存體提供了立即、高度可用且持久的封存選項。
* 沒有硬體管理的額外負荷：Azure 服務沒有硬體管理的額外負荷。Azure 服務會管理硬體，並提供地埋區域備援複寫，以提供備援及硬體故障的防護。
* 目前對於在 Azure 虛擬機器中執行的 SQL Server 執行個體，可以藉由建立連接的磁碟來備份至 Azure Blob 儲存體服務。不過，您可以連接到 Azure 虛擬機器的磁碟數有限制。超大型執行個體的限制為 16 個磁碟，較小的執行個體則更少。藉由啟用對 Azure Blob 儲存體的直接備份，您可以略過 16 個磁碟的限制。
* 此外，現在存放在 Azure Blob 儲存體服務的備份檔，可以讓內部部署 SQL Server 或在 Azure 虛擬機器中執行的另一個 SQL Server 直接使用，而不需要資料庫連接/卸離或下載並連接 VHD。
* 成本效益：只需為使用的服務付費。可以作為具成本效益的異地及備份封存選項。如需詳細資訊，請參閱 [Azure 定價計算機](http://go.microsoft.com/fwlink/?LinkId=277060 "定價計算機")和 [Azure 定價文章](http://go.microsoft.com/fwlink/?LinkId=277059 "定價文章")。

如需詳細資訊，請參閱 [SQL Server 備份及還原與 Azure Blob 儲存體服務](http://go.microsoft.com/fwlink/?LinkId=271617)。

以下兩節介紹 Azure Blob 儲存體服務，以及在與 Azure Blob 儲存體服務之間備份或還原時使用的 SQL Server 元件。了解元件及其彼此間的互動，對於在與 Azure Blob 儲存體服務之間備份或還原很重要。

此程序的第一步是建立 Azure 帳戶。SQL Server 使用 Azure 儲存體帳戶名稱及其存取金鑰值，以驗證和讀寫 Blob 至儲存體服務。SQL Server 認證會存放此驗證資訊，並在備份或還原作業期間使用。

如需有關建立儲存體帳戶及執行簡單還原的完整逐步解說，請參閱[開始使用 Azure 儲存體服務進行 SQL Server 備份與還原](http://go.microsoft.com/fwlink/?LinkId=271615)

## Azure Blob 儲存體服務元件 

* 儲存體帳戶：儲存體帳戶是所有儲存體服務的起點。若要存取 Azure Blob 儲存體服務，請先建立 Azure 儲存體帳戶。必須要有儲存體帳戶名稱及其存取金鑰屬性才能向 Azure Blob 儲存體服務及其元件進行驗證。如需 Azure Blob 儲存體服務的詳細資訊，請參閱[如何使用 Azure Blob 儲存體服務](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) (英文)

* 容器：容器提供一組 Blob 的分組，且可以存放無限數目的 Blob。若要將 SQL Server 備份寫入 Azure Blob 服務，您必須至少建立根容器。

* Blob：任何類型和大小的檔案。Azure Blob 儲存體服務中可以儲存兩種 Blob：區塊和分頁 Blob。SQL Server 備份使用分頁 Blob 作為 Blob 類型。可利用下列 URL 格式來定址 Blob：`https://<storage account>.blob.core.windows.net/<container>/<blob>` 如需分頁 Blob 的詳細資訊，請參閱[了解區塊 Blob 和分頁 Blob](http://msdn.microsoft.com/library/azure/ee691964.aspx)

## SQL Server 元件

* URL：URL 指定唯一備份檔案的統一資源識別碼 (URI)。URL 用來提供 SQL Server 備份檔的位置和名稱。在此實作中，唯一有效的 URL 是指向 Azure 儲存體帳戶中分頁 Blob 的 URL。URL 必須指向實際的 Blob，而不只是指向容器。如果 Blob 不存在，便會建立它。如果指定現有的 Blob，則 BACKUP 會失敗，除非指定了 > WITH FORMAT 選項。以下是您會在 BACKUP 命令中指定的 URL 範例：**`http[s]://ACCOUNTNAME.Blob.core.windows.net/<CONTAINER>/<FILENAME.bak>` 
<b>注意：</b> HTTPS 不是必要，但建議使用。<b>重要事項</b> 如果您選擇複製及上傳備份檔到 Azure Blob 儲存體服務，則必須使用分頁 Blob 類型作為儲存體選項 (如果您計劃使用此檔案進行還原作業的話)。從區塊 Blob 類型進行 RESTORE 會失敗並發生錯誤。

* 認證：連接到 Azure Blob 儲存體服務進行驗證所需的資訊會儲存為認證。為了讓 SQL Server 將備份寫入 Azure Blob 或從 Azure Blob 還原，必須建立 SQL Server 認證。認證會存放儲存體帳戶的名稱以及儲存體帳戶存取金鑰。建立認證之後，發出 BACKUP/RESTORE 陳述式時，它必須指定在 WITH CREDENTIAL 選項中。如需如何建立 SQL Server 認證的逐步指示，請參閱 [SQL Server 備份及還原至 Azure Blob 儲存體服務使用者入門](http://go.microsoft.com/fwlink/?LinkId=271615)。

## 使用 Azure Blob 進行 SQL Server 資料庫備份與還原 - 概念與工作：

**概念、考量及程式碼範例：**

[SQL Server 備份及還原與 Azure Blob 儲存體服務](http://go.microsoft.com/fwlink/?LinkId=271617)

**使用者入門教學課程：**

[SQL Server 備份及還原至 Azure Blob 儲存體服務使用者入門](http://go.microsoft.com/fwlink/?LinkID=271615 "教學課程")

**最佳作法、疑難排解：**
	
[備份與還原最佳作法 (Azure Blob 儲存體服務)](http://go.microsoft.com/fwlink/?LinkId=272394)




	




 

<!---HONumber=July15_HO2-->