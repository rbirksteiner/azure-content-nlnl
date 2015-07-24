<properties 
	pageTitle="開始使用 SQL Database 稽核 | Azure" 
	description="開始使用 SQL Database 稽核" 
	services="sql-database" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="jeffreyg"/>
 
# 開始使用 SQL Database 稽核 
<p> Azure SQL Database 稽核會追蹤資料庫事件，並將稽核事件寫入 Azure 儲存體帳戶中的稽核記錄。Basic、Standard 和 Premium 服務層皆提供稽核功能。

稽核可協助您保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。

稽核工具啟用及推動遵循法規標準，但不保證符合法規。如需有關支援標準法規的 Azure 程式詳細資訊，請參閱 <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure 信任中心</a>。

+ [Azure SQL Database 稽核基本概念] 
+ [設定資料庫的稽核]
+ [分析稽核記錄和報告]

##<a id="subheading-1"></a>Azure SQL Database 稽核基本概念

下列章節描述使用 Azure Preview 入口網站進行稽核的設定。您也可以[使用傳統的 Azure 入口網站設定資料庫的稽核]。

SQL Database 稽核可讓您：

- **保留**所選事件的稽核記錄。您可以定義要稽核的資料庫動作類別。
- **報告**資料庫活動。您可以使用預先設定的報告和儀表板，以便快速開始使用活動和事件報告。
- **分析**報告。您可以尋找可疑事件、異常活動及趨勢。

您可以設定下列事件類別的稽核：

**一般 SQL** 和**參數化 SQL**，且為其所收集的稽核記錄歸類為

- **資料存取**
- **結構描述變更 (DDL)**
- **資料變更 (DML)**
- **帳戶、角色和權限 (DCL)**

**預存程序**，**登入**以及**交易管理**。

針對每個事件類別，分別設定**成功**和**失敗**作業的稽核。

如需已稽核活動和事件的進一步資訊，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">稽核記錄格式參考 (doc 檔案下載)</a>。

稽核記錄會儲存在 Azure 儲存體帳戶。您可以定義稽核記錄保留期間。

可以針對特定資料庫定義稽核原則，或將稽核原則定義為預設伺服器原則。預設伺服器稽核原則會套用至伺服器上的所有資料庫，其中該伺服器並沒有定義特定覆寫的資料庫稽核原則。

在設定稽核之前，請檢查您是否正在使用[「下層用戶端」](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。


##<a id="subheading-2"></a>設定資料庫的稽核

1. 啟動 <a href="https://portal.azure.com" target="_blank">Azure Preview 入口網站</a>，位址是 https://portal.azure.com。或者，您也可以啟動<a href= "https://manage.windowsazure.com/" target="_bank">傳統的 Azure 入口網站</a>，位址是 https://manage.windowsazure.com/。請參閱下列詳細資訊。

2. 巡覽至您要稽核的 SQL Database / SQL Server 組態刀鋒視窗。在最上層按一下 [設定] 按鈕，然後在 [設定] 刀鋒視窗選取 [稽核]。

	![][1]

3. 在 [稽核組態] 刀鋒視窗中，選取 [儲存體詳細資料]，以開啟 [稽核記錄儲存體] 刀鋒視窗。選取將儲存記錄的 Azure 儲存體帳戶以及保留期間。**秘訣：**在所有稽核的資料庫中使用相同的儲存體帳戶，以充分利用預先設定的報告範本。

	![][2]

4. 在 [依事件記錄] 下，按一下 [成功] 和 [失敗] 以記錄所有事件，或選擇個別的事件類別。


5. 如果您要設定 SQL Database 的稽核，請按一下 [若要強制執行稽核，按一下這裡...]，並且在 [啟用安全性的存取] 中選取 [必要項]。如果您要設定 SQL Server 的稽核，您有兩個選項：(1) 在步驟 #6 之後，巡覽此伺服器上的每一個 SQL Database，並套用此步驟，或 (2) [修改連接字串中的伺服器 FDQN](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。


	![][5]

6. 按一下 [確定]。



##<a id="subheading-3">分析稽核記錄和報告</a>

在安裝期間，會在所選擇的 Azure 儲存體帳戶之具有 **SQLDBAuditLogs** 首碼的 [儲存資料表] 集合中彙總稽核記錄。您可以使用工具 (例如 <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure 儲存體總管</a>) 來檢視記錄檔。

預先設定的儀表板報告範本會以<a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">可下載的 Excel 試算表</a>形式提供，以協助您快速分析記錄資料。若要在稽核記錄上使用範本，您需要 Excel 2013 (或更新版本) 和 Power Query (您可從<a href="http://www.microsoft.com/download/details.aspx?id=39379">此處</a>下載)。

範本中包含虛構的範例資料，您可以設定 Power Query 直接從 Azure 儲存體帳戶匯入稽核記錄。

如需有關使用報告範本的詳細指示，請閱讀<a href="http://go.microsoft.com/fwlink/?LinkId=506731">如何 (doc 下載)</a>。

![][6]


##<a id="subheading-4"></a>使用傳統的 Azure 入口網站設定資料庫的稽核

1. 啟動<a href= "https://manage.windowsazure.com/" target="_bank">傳統的 Azure 入口網站</a>，位址是 https://manage.windowsazure.com/。
 
2.   按一下要稽核的 SQL Database / SQL Server，然後按一下 [稽核與安全性] 索引標籤。

3.   如果您要設定 SQL Database 的稽核，請在 [啟用安全性的存取] 中選取 [必要項]。如果您要設定 SQL Server 的稽核，您有兩個選項：(1) 在步驟 #7 之後，巡覽此伺服器上的每一個 SQL Database，並套用此步驟，或 (2) [修改連接字串中的伺服器 FDQN](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。

4. 在稽核區段中，按一下 [已啟用]。


	![][7]

5. 視需要編輯 [依事件記錄]。

	![][8]

6. 選取 [儲存體帳戶] 和設定 [保留]。

	![][11]

7. 按一下 [儲存]。




##<a id="subheading-3">生產環境中的使用方式作法</a>
本節的說明與以上的畫面截圖有關。可以使用 <a href="https://portal.azure.com" target="_blank">Azure Preview 入口網站</a>或<a href= "https://manage.windowsazure.com/" target="_bank">傳統的 Azure 入口網站</a>。
 

##<a id="subheading-4"></a>儲存體金鑰重新產生

在生產中，您可能會定期重新整理儲存體金鑰。重新整理金鑰時，您需要重新儲存該原則。程序如下：


1. 在 [稽核組態] 刀鋒視窗中 (如以上的設定稽核一節所述)，將 [儲存體存取金鑰] 的 [主要] 切換為 [次要]，並按下 [儲存]。![][10]
2. 進入 [儲存體組態] 刀鋒視窗，並**重新產生「主要存取金鑰」** 。

3. 返回 [稽核組態] 刀鋒視窗，並且將 [儲存體存取金鑰] 的 [次要] 切換為 [主要]，然後按下 [儲存]。

4. 返回儲存體 UI 並**重新產生** 「次要存取金鑰」 (為了下一個金鑰重新整理週期做準備)。
  
##<a id="subheading-4"></a>自動化
有數個 PowerShell 指令程式可用來設定 Azure SQL Database 中的稽核。若要存取稽核 Cmdlet，您必須在 [Azure 資源管理員] 模式中執行 PowerShell。

> [AZURE.NOTE][Azure 資源管理員](https://msdn.microsoft.com/library/dn654592.aspx)模組目前為預覽版。它可能沒有如 Azure 模組所提供的相同管理功能。

在 [Azure 資源管理員] 模式中，您可以執行 `Get-Command *AzureSql*` 來列出可用的 Cmdlet。







<!--Anchors-->
[Azure SQL Database 稽核基本概念]: #subheading-1
[設定資料庫的稽核]: #subheading-2
[分析稽核記錄和報告]: #subheading-3
[使用傳統的 Azure 入口網站設定資料庫的稽核]: #subheading-4


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
[2]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
[3]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
[5]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
[6]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
[7]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-enable.png
[8]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure.png
[9]: ./media/sql-database-auditing-get-started/sql-database-auditing-security-enabled-access.png
[10]: ./media/sql-database-auditing-get-started/sql-database-auditing-storage-account.png
[11]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure-storage.png






<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

 

<!---HONumber=July15_HO1-->