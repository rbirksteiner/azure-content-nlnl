<properties 
	pageTitle="如何建立及設定 Azure SQL Database - Azure 教學課程" 
	description="如何建立及設定 Azure SQL Database。" 
	services="sql-database" 
	documentationCenter="" 
	authors="sidneyh" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="sidneyh"/>

# 如何建立及設定 Azure SQL Database

本主題中，您會使用 Azure 管理入口網站的 [快速建立] 選項來建立及設定新的 Azure SQL Database。此程序說明如何使用的現有伺服器建立 SQL 資料庫，以及如何視需要建立新的伺服器。

> [AZURE.NOTE]使用 [快速建立] 建立 SQL Database，會佈建標準 (S0) 資料庫。若要在非標準 (S0) 的服務層和效能層級上建立 SQL Database，請使用 [自訂建立]。如需使用 [自訂建立] 建立 Azure SQL Database 的詳細資訊，請參閱[開始使用 Microsoft Azure SQL Database](sql-database-get-started.md)。

## 作法：建立 Azure SQL Database

1. 登入[管理入口網站](https://portal.azure.com/)。

2. 按一下頁面底部的 [新增]。

	![按一下 [SQL Database]][1]

3. 依序按一下 [資料服務]、[SQL Database] 和 [快速建立]。

	![按一下 [新增]、[資料服務] 和 [快速建立]][2]
	 
5. 在 [快速建立] 中，輸入新資料庫的名稱、選取訂閱，然後從 [伺服器] 清單中選取伺服器 (或跳到下一個步驟建立新的伺服器)。

	![在現有的伺服器中建立新的 SQL Database][7]

	您可以選擇性地選取 [新增 SQL Database 伺服器] 以建立新的伺服器。![建立新的 SQL Database 和新的伺服器][8]

	1. 選擇區域。區域可決定伺服器的地理位置。您無法輕易地切換區域，所以請選擇一個適合此伺服器的區域。選擇一個最靠近您的位置。將 Azure 應用程式和資料庫放在相同區域，可節省對外頻寬的成本並縮短資料延遲。
	2. 以一個字且不含空格的方式輸入登入名稱。 

		SQL Database 會針對加密連線使用 SQL 驗證。系統將使用您提供的名稱，建立指派給系統管理員 (sysadmin) 固定伺服器角色的新 SQL Server 驗證登入。

		登入不能是電子郵件地址、Windows 使用者帳戶或 Windows Live ID。SQL Database 上不支援宣告或 Windows 驗證。 
	3. 提供八個字元以上，使用大小寫值和數字或符號組合的強式密碼。

	


9. 完成時，請按一下頁面底部的 [建立 SQL Database] 核取記號。

### 自動產生的伺服器名稱

請注意您是否建立了新的伺服器，但未指定伺服器名稱。SQL Database 會自動產生伺服器名稱，以確保沒有重複的 DNS 項目。伺服器名稱是十個字元的英數字元字串。您無法變更 SQL Database 伺服器的名稱。

在下一個步驟中，您將設定防火牆，以允許在網路上執行之應用程式的連線存取。

<a id="configFWLogical"></a>

## 作法：設定邏輯伺服器的防火牆

1. 在 [管理入口網站][](http://manage.windowsazure.com) 中，按一下 [SQL Database]，然後按一下 [伺服器]

	![按一下 [伺服器]][4]
2. 從清單中，按一下您剛建立的伺服器。

2. 按一下 [設定]。

	![按一下 [設定]][5]

3. 在 [允許的 IP 位址] 區段，按一下 [新增至允許的 IP 位址]。這是您的路由器或 Proxy 伺服器目前正在接聽的 IP 位址。SQL Database 會偵測目前連線所使用的 IP 位址並建立防火牆規則，以接受來自此裝置的連線要求。![按一下 [新增至允許的 IP 位址]][6]

	規則的預設名稱隨即產生。您可以編輯名稱。
	

4. 當您從另一部電腦連接到資料庫時，您必須建立新的規則，以允許連接其 IP 位址。使用 [開始] 和 [結束] 方塊來建立某個範圍的 IP 位址。

	如果用戶端電腦使用動態指派的 IP 位址，您指定的範圍可足以容納指派給網路中電腦的 IP 位址。一開始請使用較狹窄的範圍，待需要時再將範圍延伸。

7. 按一下頁面底部的 [儲存] 以完成此步驟。

您現在已有 SQL Database、邏輯伺服器、允許來自您的 IP 位址之輸入連線的防火牆規則，以及系統管理員登入資訊。

**注意：**您剛剛建立的邏輯伺服器是虛擬的，它會動態地託管於資料中心內的實體伺服器上。刪除伺服器是無法復原的動作。請務必備份後續上傳至伺服器的所有資料庫。


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png
[7]: ./media/sql-database-create-configure/quick-create-existing-server.png
[8]: ./media/sql-database-create-configure/quick-create-new-server.png



 

<!---HONumber=62-->