<properties 
   pageTitle="變更資料庫服務層和效能等級" 
   description="了解如何使用 Azure SQL Database 的服務層，動態地擴大或縮小雲端資料庫的規模，以藉此在不使應用程式停機的情況下，依據業務和成本需求來調高或調低效能。" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="04/02/2015"
   ms.author="sstein"/>


# 變更資料庫服務層和效能等級 

本主題說明在服務層和效能等級之間移動 Azure SQL Database 所需的步驟。

## 變更服務層 

使用[將 SQL Database Web/Business 資料庫升級至新的服務層](sql-database-upgrade-new-service-tiers.md)和 [Azure SQL Database 服務層和效能等級](https://msdn.microsoft.com/library/azure/dn741336.aspx)中的資訊，來判斷適合您 Azure SQL Database 的服務層和效能等級。

您可以使用 Azure 管理入口網站、[PowerShell](https://msdn.microsoft.com/library/azure/dn546726.aspx) 或 [REST API](https://msdn.microsoft.com/library/dn505719.aspx) 輕鬆地在任何服務層之間移動。

在服務層之間移動時，請考慮以下情況：- 升級服務層或效能等級前，請先確定您在伺服器上有可用的配額。如果您需要額外配額，請連絡客戶支援。- 同盟資料庫無法升級至基本、標準或高階服務層。

- 若要將資料庫降級，資料庫應該小於目標服務層允許的大小上限。如需每個服務層允許大小的詳細資訊，請參閱本節稍後的服務層與資料庫大小表格。

- 升級資料庫時若將[標準地理複寫](https://msdn.microsoft.com/library/azure/dn758204.aspx)或[作用中地理複寫](https://msdn.microsoft.com/library/azure/dn741339.aspx)啟用，您必須先將其次要資料庫升級為所需的效能層次，然後再升級主要資料庫。

- 從高階服務層降級時，您必須先終止所有的「異地複寫」關聯性。您可以遵循[終止連續複製關聯性](https://msdn.microsoft.com/library/azure/dn741323.aspx)主題所述的步驟，停止主要資料庫與作用中次要資料庫之間的複寫程序。

- 還原服務會針對各種服務層提供不同的選項。降級後您可能會無法還原至某個時間點，或具有較短的備份保留期限。如需詳細資訊，請參閱 [Azure SQL Database 備份和還原](https://msdn.microsoft.com/library/azure/jj650016.aspx)。

- 您可以在 24 小時期間內，變更最多四個個別的資料庫 (服務層或效能等級)。

- 完成變更之前，不會將新屬性套用至資料庫。

請注意以下事項：- 2015 年 9 月將會淘汰 Business 和 Web 服務層。如需詳細資訊，請參閱 [Web 和 Business Edition 終止常見問題集](https://msdn.microsoft.com/library/azure/dn741330.aspx)。

<note included> - 請務必注意，目前實作的[「同盟」將與 Web 和 Business 服務層一起被淘汰](https://msdn.microsoft.com/library/azure/dn741330.aspx)。建議您使用 [Elastic Scale for Azure SQL Database](sql-database-elastic-scale-get-started.md)，在 Azure SQL Database 上建置分區化的向外延展解決方案。若要試用，請參閱「開始使用 Azure SQL Database Elastic Scale 預覽」。

## 升級至較高的服務層
使用下列其中一種方法升級資料庫。這些步驟是針對升級至高階服務層，但也適用於所有的升級情形。

### 使用 Azure 管理入口網站
1. 使用您的 Microsoft 帳戶登入 Azure 管理入口網站。
2. 瀏覽至 [SQL DATABASE] 索引標籤。
3. 選取 [資料庫] 清單中的資料庫。這會在 [資料庫儀表板] 或 [快速入門] 頁面中開啟資料庫。
4. 選取資料庫的 [調整規模] 索引標籤。
5. 在 [一般] 區段中，選取 [高階] 服務層。
6. 針對 [效能等級]，請選取 **P1**、**P2** 或 **P3**。推動每個效能等級的資源會以 DTU 來表示。如需效能等級和 DTU 的詳細資訊，請參閱「Azure SQL Database 服務層和效能等級」
8. 在畫面底部的命令列中，按一下 [儲存] 按鈕。
9. 隨即會看到 [確認] 頁面。請閱讀提供的資訊，並選取核取方塊以確認。


### 使用 Azure PowerShell
1. 使用 Set-AzureSqlDatabase 指定資料庫的效能等級、資料庫大小上限和服務層。如需不同服務層所支援的資料庫大小清單，請參閱「Azure SQL Database 服務層 (版本)」。
2. 使用 New-AzureSqlDatabaseServerContext Cmdlet 設定伺服器內容。＜使用 Azure PowerShell 命令＞一節中有範例語法。
3. 取得資料庫的控制代碼，然後鎖定效能等級。使用 Set-AzureSqlDatabase –ServiceObjective 指定效能等級

使用範例 在此範例中：- 此範例示範如何升級至高階服務層。- 建立指向 "somedb" 資料庫名稱的 $db 控制代碼。- 建立指向高階效能等級 1 的 $P1 控制代碼。- 將資料庫 $db 的效能等級設定為 $P1。

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName "somedb"

		$P1= Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P1"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P1 –Edition Premium



## 降級至較低的服務層
使用下列其中一種方法將資料庫降級至較低的服務層：

### 使用 Azure 管理入口網站
1. 使用您的 Microsoft 帳戶登入 Azure 管理入口網站。
2. 瀏覽至 [SQL DATABASE] 索引標籤。
3. 選取所需資料庫的 [調整規模] 索引標籤。
4. 在 [一般] 區段中，選取您想要降級為該層的服務層。
5. 在畫面底部的命令列中，按一下 [儲存] 按鈕。
6. 視情況閱讀 [確認] 頁面中提供的資訊，然後選取核取方塊以確認變更。

### 使用 Azure PowerShell
1. 使用 Set-AzureSqlDatabase 指定資料庫的服務層、效能等級和大小上限。
2. 透過＜使用 Azure PowerShell 命令＞一節中提供的範例語法，使用 New-AzureSqlDatabaseServerContext 設定伺服器內容。
3. 執行下列動作：
 - 取得資料庫的控制代碼。
 - 取得效能等級的控制代碼。
 - 使用 Set-AzureSqlDatabase –ServiceObjective 指定資料庫的服務層、效能等級和大小上限。

**使用範例**

此範例示範如何從高階服務層資料庫降級至標準服務層資料庫：

- 建立指向 "somedb" 資料庫名稱的 $db 控制代碼。

- 建立指向標準效能等級 2 的 $S2 變數。

- 將資料庫 $db 的效能等級設定為 $S2。

- 使用 –Edition 和 –MaxSizeGB 參數，指定資料庫服務層和資料庫的大小上限。–MaxSizeGB 參數的指定值必須對目標服務層而言為有效。本主題前面有每個服務層的 MaxSize 值表格。

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$S2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "S2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $S2 –Edition Standard –MaxSizeGB 40

##變更效能等級
您可以使用下列其中一種方法，提高或降低標準或高階資料庫的效能等級。變更資料庫的效能等級可能需要一段時間。如需詳細資訊，請參閱相關的[高階資料庫變更的影響](https://msdn.microsoft.com/library/azure/dn369872.aspx#Impact)一節。

如果您變更效能等級的高階資料庫已設定「主動式異地複寫關聯性」，請針對主要資料庫和作用中次要資料庫，依照以下順序執行：

這是因為作用中次要資料庫必須等於或高於主要資料庫的效能等級。- 如果您要從較高的效能等級變更為較低的效能等級，請先從主要資料庫開始變更，再接著變更一或多個作用中次要資料庫。

- 如果您要從較低的效能等級變更為較高的效能等級，請先從作用中次要資料庫開始變更，最後才變更主要資料庫。

###使用 Azure 管理入口網站
1. 使用您的 Microsoft 帳戶登入 Azure 管理入口網站。
2. 瀏覽至 [SQL DATABASE] 索引標籤。
3. 針對帳戶或特定伺服器，選取 [資料庫] 清單中的資料庫。這會在 [資料庫儀表板] 或 [快速入門] 頁面中開啟資料庫。
5. 選取資料庫的 [調整規模] 索引標籤。
6. 選取 [效能等級] 選項中的效能等級。
7. 在畫面底部的命令列中，按一下 [儲存] 按鈕

###使用 Azure PowerShell
1. 使用 Set-AzureSqlDatabase 指定資料庫的效能等級。
2. 使用 New-AzureSqlDatabaseServerContext Cmdlet 設定伺服器內容。＜使用 Azure PowerShell 命令＞一節中有範例語法。
3. 執行下列動作：
- 取得資料庫的控制代碼。
- 取得效能等級的控制代碼。
- 使用 Set-AzureSqlDatabase –ServiceObjective 指定效能等級。

**使用範例**

在此範例中：

- 建立指向 "somedb" 資料庫名稱的 $db 控制代碼。

- 建立指向高階版效能等級 2 的 $P2 控制代碼。

- 將資料庫 $db 的效能等級設定為 $P2。

		Windows PowerShell
		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$P2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P2

##資料庫變更的影響
本節提供的資訊，是關於升級至標準或高階服務層，或對資料庫的效能等級進行變更後的效果。

###處理資料庫變更期間的應用程式連接
當效能等級變更或升級/降級完成後，與資料庫的連接可能會暫時中斷，而重新建立連接或許需要幾秒鐘的時間。SQL Database 應用程式的程式碼應該設計為能彈性因應連接中斷情形，因為當資料中心的電腦故障，且 SQL Database 服務將資料庫容錯移轉時，SQL Database 的連接隨時都可能會中斷。應用程式不需要任何實作變更，也不需要變更高階資料庫的效能等級，即可使用高階資料庫。

###了解及估計資料庫變更的延遲
資料庫的 SLO 變更通常需要移動資料，因此可能需要數小時的時間，變更要求才會完成，且相關聯的計費變更也會生效。升級資料庫或將其降級，或變更資料庫的效能等級時，就可能會移動資料。

**涉及資料移動之 SLO 變更的延遲**

決定資料庫的儲存體大小後，便可以透過以下算式來估計 SLO 變更要求的延遲：

3 x (5 分鐘 + 資料庫大小 / 150 MB/分鐘)

例如，如果資料庫大小為 50 GB，可依以下算式估計 SLO 變更要求的延遲：

3 x (5 分鐘 + 50 GB x 1024 MB/GB / 150 MB/分鐘) ≈17 小時

使用這個算式估計的下限與上限會視情況而有所不同，空的資料庫為 15 分鐘，150 GB 資料庫則約為 2 天。估計結果可以根據資料中心內的條件再進一步改變。

**從較高效能等級變更為較低效能等級的延遲**

一般而言，如果資料庫效能等級從較高變更為較低，並不會移動資料。在這種情況下，SLO 變更的延遲會快得多，通常只要數秒即可完成。

警告：上述說明僅適用於高階和標準服務層之間的降級。降級至 Web、Business 或 Basic 服務層時需要移動資料。

###檢查資料庫變更的狀態
您可以在服務層升級或降級期間檢查資料庫的狀態，或使用下列其中一種方法變更效能等級。

####使用 Azure 管理入口網站
1. 使用您的 Microsoft 帳戶登入 Azure 管理入口網站。
2. 選取 [資料庫] 清單中的資料庫。這會在 [資料庫儀表板] 或 [快速入門] 頁面中開啟資料庫。
3. 在 [資料庫儀表板] 中，查看 [快速概覽] 區域的 [版本] 區段即可了解狀態資訊。
4. 服務等級目標 (SLO) 代表服務層內的效能等級。


##使用 Azure PowerShell 命令
本節提供使用 Azure PowerShell 命令的必要條件。

**必要條件**

若要使用本主題中所述的 Azure PowerShell Cmdlet，您必須在執行 PowerShell 的電腦上安裝以下軟體。1.從 http://www.microsoft.com/zh-tw/download/details.aspx?id=34595 下載至少為 3.0 版的 Windows PowerShell。

2. 從 [Azure SDK 和工具下載](http://azure.microsoft.com/downloads/)的＜命令列工具＞一節下載 Azure PowerShell。

執行下列動作：從 [開始] 畫面或 [開始] 功能表，瀏覽至 Azure PowerShell 並加以啟動。

輸入伺服器的使用者名稱和密碼。

使用 **New-AzureSqlDatabaseServerContext** 建立伺服器內容。

**範例**

		Windows PowerShell
		$subId = <Subscription ID>
		$thumbprint = <Certificate Thumbprint>
		$myCert = Get-Item Cert:\CurrentUser\My\$thumbprint
		Set-AzureSubscription -SubscriptionName "mySubscription" -SubscriptionId $subId -Certificate $myCert
		Select-AzureSubscription -SubscriptionName "mySubscription"
		$serverContext = New-AzureSqlDatabaseServerContext -ServerName "myserver" -UseSubscription


Azure PowerShell 參考 若要查看本主題中使用之 Azure PowerShell Cmdlet 的詳細資訊，請參閱 [Azure SQL Database Cmdlet](https://msdn.microsoft.com/library/dn546726.aspx)。

[New-AzureSqlDatabaseServerContext](http://go.microsoft.com/fwlink/?LinkId=391026)

[New-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391027)

[Set-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391412)
 

<!---HONumber=62-->