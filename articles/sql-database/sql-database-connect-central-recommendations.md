<properties 
	pageTitle="連接到 SQL Database：連結、最佳作法和設計方針" 
	description="針對從 ADO.NET 和 PHP 之類的技術連接到 Azure SQL Database 的用戶端程式，統合收集連結和建議的入門主題。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="genemi"/>


# 連接到 SQL Database：連結、最佳作法和設計方針


本主題是開始使用至 Azure SQL Database 的用戶端連線的好地方。它提供指向各種技術之程式碼範例的連結，可用來連接到 SQL Database 並與其互動。這些技術包括企業程式庫、JDBC 和 PHP 和更多技術。建議一般適用於所有特定連線技術或程式設計語言。


## 與技術無關的建議


本節中的資訊適用於您用來連接到 SQL Database 的所有特定技術。


- [以程式設計方式連接至 Azure SQL Database 的指導方針](http://msdn.microsoft.com/library/azure/ee336282.aspx) - 討論包括：
 - 連接埠
 - 防火牆
 - 連接字串
- [Azure SQL Database 資源管理](https://msdn.microsoft.com/library/azure/dn338083.aspx) - 討論包括：
 - 資源管理
 - 限制強制執行
 - 節流


不論您使用的連線技術，SQL Database 伺服器，甚或是個別資料庫的某些防火牆設定與下列有關：


- [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx)


## 建議：連接


- 在您的用戶端連線邏輯中，將預設的逾時覆寫為 30 秒。
 - 預設 15 秒對於依賴網際網路的連線而言太短。
- 如果您使用的是[連線集區](http://msdn.microsoft.com/library/8xx3tyca.aspx)，請在程式未主動使用時即時關閉連線，而不只是準備重複使用連線。
 - 除非您的程式將立即重複使用其他作業的連線，而不暫停，否則我們建議使用下列模式：<br/><br/>開啟連線。<br/>透過連線執行一項作業。<br/>關閉連接。<br/><br/>
- 使用重試邏輯搭配連線邏輯，但僅用於暫時性錯誤。使用 SQL Database 時，嘗試開啟連線或發出查詢都可能因為多種原因而失敗。
 - 失敗的其中一個持續性原因可能是您的連接字串格式錯誤。
 - 失敗的其中一個暫時性原因可能是 Azure SQL Database 系統需要平衡整體負載。暫時性原因會自己消失，這表示您的程式應該重試。
 - 重試查詢時，請先關閉連線，然後再開啟另一個連線。
- 確定您的 [Azure SQL Database 防火牆](http://msdn.microsoft.com/library/ee621782.aspx)允許連接埠 1433 上的傳出 TCP 通訊。
 - 您可以在 SQL Database 伺服器上或個別的資料庫中設定[防火牆](http://msdn.microsoft.com/library/azure/ee621782.aspx)設定。


## 建議：驗證


- 使用 SQL Database 驗證，而非 Windows 驗證。
- 指定一個特定的資料庫，而非預設為 *master* 資料庫。
- 有時候必須提供使用者名稱的後置詞 *@yourservername*，但有時候必須省略後置詞。這取決於撰寫工具或 API 的方式。
 - 檢查每個個別技術的詳細資料。
- 透過在[自主資料庫](http://msdn.microsoft.com/library/ff929071.aspx)中指定使用者來連線。
 - 此方法會因為不需要在 master 資料庫中登入，而提供較好的效能和延展性。
 - 您無法在 SQL Database 上使用 Transact-SQL **USE myDatabaseName;** 陳述式。


## 暫時性錯誤


雲端服務 (例如 Azure 及其 SQL Database 服務) 有無止盡的平衡工作負載和資源管理挑戰。如果正在從同一部電腦提供的兩個資料庫同時進行極繁重的處理，則管理系統可能會偵測是否需要將一個資料庫的工作負載，轉移有多餘容量的另一個資源。


在轉移期間，資料庫可能暫時無法使用。這可能會封鎖新連接，或可能會導致您的用戶端程式遺失其連接。但資源移轉是暫時的，而且它可能會在幾分鐘或幾秒鐘的時間內自行解決。完成移轉後，用戶端程式可重新建立連接，然後繼續執行其工作。與其讓用戶端程式發生可避免的失敗，不如在處理程序中暫停。


發生任何 SQL Database 錯誤時，會擲回 [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx)。SqlException 在其 **Number** 屬性中包含一個數字錯誤碼。如果錯誤碼識別暫時性錯誤，您的程式應該重試呼叫。


- [錯誤訊息 (Azure SQL Database)](http://msdn.microsoft.com/library/azure/ff394106.aspx) - 其 [**連線中斷錯誤**] 區段是一個有理由自動重試的暫時性錯誤清單。
 - 例如，如果出現錯誤編號 40613，表示發生類似如下的狀況，則重試：<br/>* 伺服器 'theserver' 上的資料庫 'mydatabase' 目前無法使用。*


暫時性*錯誤*有時也稱為暫時性*失敗*。本主題將這兩個詞彙視為同義字。


發生連接錯誤 (不論是否為暫時性) 時，如需進一步的協助，請參閱：


- [疑難排解 Azure SQL Database 的連線問題](http://support.microsoft.com/kb/2980233/)


## 技術


下列主題包含數個語言和驅動程式技術的程式碼範例連結，可讓您從用戶端程式連接到 Azure SQL Database。


會針對 Windows 和 Linux 上執行的用戶端提供各種程式碼範例。


**一般範例：**有各種不同的程式設計語言，包括 PHP、Python、Node.js 和 .NET CSharp 的程式碼範例。此外，也提供在 Windows 或 Linux 上執行的用戶端範例。


- [SQL Database 的用戶端開發和快速入門程式碼範例](sql-database-develop-quick-start-client-code-samples.md)
- [Azure SQL Database 開發：使用說明主題](http://msdn.microsoft.com/library/azure/ee621787.aspx)


**重試邏輯：**有一些針對多樣化需求所設計的程式碼範例，可使用自動重試邏輯來處理暫時性錯誤。


- [作法：可靠地連線到 Azure SQL Database](http://msdn.microsoft.com/library/azure/dn864744.aspx)
- [作法：使用 ADO.NET 與 Enterprise Library 連接到 Azure SQL Database](http://msdn.microsoft.com/library/azure/dn961167.aspx)
- [作法：使用 ADO.NET 連接到 Azure SQL Database](http://msdn.microsoft.com/library/azure/ee336243.aspx)


**彈性擴縮：**如需彈性擴縮資料庫的連線相關資訊，請參閱：


- [開始使用 Azure SQL Database Elastic Scale 預覽](sql-database-elastic-scale-get-started.md)
- [資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)


**驅動程式庫：**如需連接驅動程式庫的詳細資訊，包括建議的版本，請參閱：


- [SQL Database 和 SQL Server 的連接庫](sql-database-libraries.md)


## 另請參閱


- [建立您的第一個 Azure SQL Database](sql-database-get-started.md)

 

<!---HONumber=July15_HO2-->