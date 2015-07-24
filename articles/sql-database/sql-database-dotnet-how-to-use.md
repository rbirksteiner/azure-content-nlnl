<properties 
	pageTitle="如何使用 SQL Database (.NET) - Azure 功能指南" 
	description="開始使用 SQL Database。了解如何建立 SQL Database 執行個體並使用 ADO.NET、ODBC 和 EntityClient Provider 進行連線。" 
	services="sql-database" 
	documentationCenter=".net" 
	authors="jeffreyg" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="jeffreyg"/>







# 如何在 .NET 應用程式中使用 Azure SQL Database

本指南將說明如何在 Azure SQL Database 上建立邏輯伺服器和資料庫執行個體，並使用下列 .NET Framework 資料提供者技術連接到資料庫：ADO.NET、ODBC 和 EntityClient 提供者。


## 什麼是 SQL Database

SQL Database 可為 Azure 提供關聯式資料庫管理系統，並以 SQL Server 技術為基礎。在 SQL Database 執行個體中，您可以輕鬆地將關聯式資料庫解決方案佈建及部署到雲端，並充分利用分散式資料中心，而該資料中心可提供企業級的可用性、延展性和安全性，以及內建資料防護和自我修復功能。



## 登入 Azure

SQL Database 可在 Azure 上提供關聯式資料儲存體、存取和管理服務。若要加以使用，您必須要有 Azure 訂閱。

1. 開啟網頁瀏覽器，並瀏覽到 [http://azure.microsoft.com/](http://azure.microsoft.com)。若要開始使用免費帳戶，請按一下右上角的 [免費試用]，並依照步驟進行。

2. 現在已建立您的帳戶。您可以隨時開始使用。


## 建立及設定 SQL Database

接著建立及設定資料庫和伺服器。在 Azure 管理入口網站中，經過修訂的工作流程可讓您先建立資料庫，然後再進行伺服器佈建。

<h3 name="createsrvr">建立資料庫執行個體和邏輯伺服器</h3>

1. 登入 [Azure 管理入口網站][]。

2. 按一下頁面底部的 [+新增]。

3. 按一下 [資料服務]。

4. 按一下 [SQL Database]。

5. 按一下 [自訂建立]。

6. 在 [名稱] 中，輸入資料庫名稱。

7. 選擇版本、大小上限和定序。根據本指南的用途，您可以使用預設值。

	SQL Database 提供三種資料庫版本，即 Basic、 Standard 和 Premium。

	MAXSIZE 會在第一次建立資料庫時指定，且後續可使用 ALTER DATABASE 加以變更。MAXSIZE 可用來限制資料庫的大小。

	Azure 上的每個 SQL Database 實際上有三個複本。這是為了要確保高可用性。容錯移轉是透明的機制，且屬於服務的一部分。[服務等級協定][]提供 99.9% 的 SQL Database 運作時間。

8. 在 [伺服器] 中，選取 [New SQL Database server]。

9. 按一下箭號以移至下一頁。

10. 在 [伺服器設定] 中，輸入 SQL Server 驗證登入名稱。

	SQL Database 會針對加密連線使用 SQL 驗證。系統將使用您提供的名稱，建立指派給系統管理員 (sysadmin) 固定伺服器角色的新 SQL Server 驗證登入。

	登入不能是電子郵件地址、Windows 使用者帳戶或 Windows Live ID。SQL Database 上不支援宣告或 Windows 驗證。

11. 提供八個字元以上，使用大小寫值和數字或符號組合的強式密碼。

12. 選擇區域。區域可決定伺服器的地理位置。您無法輕易地切換區域，所以請選擇一個適合此伺服器的區域。選擇一個最靠近您的位置。將 Azure 應用程式和資料庫放在相同區域，可節省對外頻寬的成本並縮短資料延遲。

13. 請務必將 [允許 Azure 服務存取伺服器] 選項保持為選取狀態，以便使用 SQL Database 適用的管理入口網站、儲存體服務和 Azure 上的其他服務連接此資料庫。

14. 完成時，請按一下頁面底部的核取記號。

請注意，您未指定伺服器名稱。SQL Database 會自動產生伺服器名稱，以確保沒有重複的 DNS 項目。伺服器名稱是十個字元的英數字元字串。您無法變更 SQL Database 伺服器的名稱。

資料庫建立後，請按一下資料庫以開啟其儀表板。儀表板會提供可讓您在應用程式程式碼中複製及使用的連接字串。它也會顯示您從 Management Studio 或其他管理工具連接到資料庫時所需指定的管理 URL。


![image](./media/sql-database-dotnet-how-to-use/SQLDbDashboard.PNG)


在下一個步驟中，您將設定防火牆，以允許在網路上執行之應用程式的連線存取。

<h3 name="configFWLogical">設定邏輯伺服器的防火牆</h3>

1. 依序按一下 [SQL Database]、位於頁面頂端的 [伺服器]，以及您剛剛建立的伺服器。

	![Image2](./media/sql-database-dotnet-how-to-use/SQLDBFirewall.PNG)

2. 按一下 [設定]。

3. 複製目前的用戶端 IP 位址。如果您從網路連接，這是路由器或 Proxy 伺服器正在接聽的 IP 位址。SQL Database 會偵測目前連線所使用的 IP 位址，因此您可以建立防火牆規則以接受來自此裝置的連線要求。

4. 將 IP 位址貼至 START IP ADDRESS 和 END IP ADDRESS 中，以建立可存取伺服器的位址範圍。稍後，如果您遇到連線錯誤，指出範圍太過狹窄，您可以編輯此規則將範圍擴大。

	如果用戶端電腦使用動態指派的 IP 位址，您指定的範圍必須足以容納指派給網路中電腦的 IP 位址。一開始請使用較狹窄的範圍，待需要時再將範圍延伸。

5. 為此防火牆規則輸入名稱，例如您的電腦或公司名稱。

6. 按一下規則旁的核取記號，以儲存規則。

	![Image3](./media/sql-database-dotnet-how-to-use/SQLDBIPRange.PNG)

7. 按一下頁面底部的 [儲存] 以完成此步驟。如果您沒有看到 [儲存]，請重新整理瀏覽器頁面。

您現在已有資料庫執行個體、邏輯伺服器、允許來自您的 IP 位址之輸入連線的防火牆規則，以及系統管理員登入資訊。您現在已可透過程式設計連接到資料庫。


## 連線到 SQL Database

本節將說明如何使用不同的 .NET Framework 資料提供者連接到 SQL Database 執行個體。如需有關連接到 SQL Databse 伺服器與資料庫的重要建議，請參閱：


- [連接 SQL Database：重要的建議](../sql-database-connect-central-recommendations/)。


如果您選擇使用 Visual Studio，而您的組態並未包含作為前端的 Azure Web 應用程式，則在開發電腦上就無須安裝其他工具或 SDK。您可以直接開始開發應用程式。

您可以使用 Visual Studio 中的所有相同設計工具來處理 SQL Database，如同您對 SQL Server 的處理一般。[伺服器總管] 可讓您檢視 (但無法編輯) 資料庫物件。Visual Studio 的實體資料模型設計工具具有完備的功能，可讓您對 SQL Database 建立用於 Entity Framework 的模型。

## 使用適用於 SQL Server 的 .NET Framework 資料提供者

**System.Data.SqlClient** 命名空間是適用於 SQL Server 的 .NET Framework 資料提供者。

標準連接字串如下所示：

    Server=tcp:.database.windows.net;
    Database=;
    User ID=@;
    Password=;
    Trusted_Connection=False;
    Encrypt=True;

您可以使用 **SQLConnectionStringBuilder** 類別建置連接字串，如下列程式碼範例所示：

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder();
    csBuilder.DataSource = xxxxxxxxxx.database.windows.net;
    csBuilder.InitialCatalog = testDB;
    csBuilder.Encrypt = true;
    csBuilder.TrustServerCertificate = false;
    csBuilder.UserID = MyAdmin;
    csBuilder.Password = pass@word1;

如果事先已知連接字串的元素，您可以將其儲存在組態檔案中，並在執行階段擷取用來建置連接字串。以下是組態檔案中的範例連接字串：

    <connectionStrings>
      <add name="ConnectionString" 
           connectionString ="Server=tcp:xxxxxxxxxx.database.windows.net;Database=testDB;User ID=MyAdmin@xxxxxxxxxx;Password=pass@word1;Trusted_Connection=False;Encrypt=True;" />
    </connectionStrings>

若要擷取組態檔案中的連接字串，請使用 **ConfigurationManager** 類別：

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder(ConfigurationManager.ConnectionStrings["ConnectionString"].ConnectionString);
    After you have built your connection string, you can use the SQLConnection class to connect the SQL Database server:
    SqlConnection conn = new SqlConnection(csBuilder.ToString());
    conn.Open();

## 使用適用於 ODBC 的 .NET Framework 資料提供者

**System.Data.Odbc** 命名空間是適用於 ODBC 的 .NET Framework 資料提供者。以下提供範例 ODBC 連接字串：

    Driver={SQL Server Native Client 10.0};
    Server=tcp:.database.windows.net;
    Database=;
    Uid=@;
    Pwd=;
    Encrypt=yes;

**OdbcConnection** 類別代表開啟的資料來源連線。下列程式碼範例說明如何開啟連線：

    string cs = "Driver={SQL Server Native Client 10.0};" +
                "Server=tcp:xxxxxxxxxx.database.windows.net;" +
                "Database=testDB;"+
                "Uid=MyAdmin@xxxxxxxxxx;" +
                "Pwd=pass@word1;"+
                "Encrypt=yes;";

    OdbcConnection conn = new OdbcConnection(cs);
    conn.Open();

如果您要在執行階段建置連接字串，您可以使用 **OdbcConnectionStringBuilder** 類別。

## 使用 EntityClient 提供者

**System.Data.EntityClient** 命名空間是適用於 Entity Framework 的 .NET Framework 資料提供者。

Entity Framework 可讓開發人員對概念式的應用程式模型進行程式設計 (而不是直接對關聯式儲存結構描述進行程式設計)，以建立資料存取應用程式。Entity Framework 會將 **EntityConnection** 提供給基礎資料提供者和關聯式資料庫，而將儲存專用的 ADO.NET 資料提供者作為建置基礎。

若要建構 **EntityConnection** 物件，您必須參考一組包含必要模型和對應的中繼資料，以及儲存專用的資料提供者名稱和連接字串。**EntityConnection** 設置完成後，即可透過從概念式模型產生的類別來存取實體。

以下提供連接字串範例：

    metadata=res://*/SchoolModel.csdl|res://*/SchoolModel.ssdl|res://*/SchoolModel.msl;provider=System.Data.SqlClient;provider connection string="Data Source=xxxxxxxxxx.database.windows.net;Initial Catalog=School;Persist Security Info=True;User ID=MyAdmin;Password=***********"

如需詳細資訊，請參閱[適用於 Entity Framework 的 EntityClient 提供者][]。

## 後續步驟

現在您已了解連接到 SQL Database 的基本概念，您可以參閱下列資源，以深入了解 SQL Database。

-   [開發：使用說明主題 (SQL Database)][]
-   [SQL Database][]


  [What is SQL Database]: #WhatIs
  [Sign in to Azure]: #PreReq1
  [Create and Configure SQL Database]: #PreReq2
  [Connect to SQL Database]: #connect-db
  [Connect Using ADO.NET]: #using-sql-server
  [Connect Using ODBC]: #using-ODBC
  [Connect Using EntityClient Provider]: #using-entity
  [Next Steps]: #next-steps
  [Azure Free Trial]: {localLink:2187} "免費試用"
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [How to Create a SQL Database Server]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-create-a-sql-azure-server.aspx
  [Management Portal for SQL Database]: http://msdn.microsoft.com/library/windowsazure/gg442309.aspx
  [SQL Database Firewall]: http://social.technet.microsoft.com/wiki/contents/articles/sql-azure-firewall.aspx
  [Tools and Utilities Support (SQL Database)]: http://msdn.microsoft.com/library/windowsazure/ee621784.aspx
  [How to Create a SQL Database on Azure]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-create-a-sql-azure-database.aspx
  [服務等級協定]: {localLink:1132} "SLA"
  [適用於 Entity Framework 的 EntityClient 提供者]: http://msdn.microsoft.com/library/bb738561.aspx
  [開發：使用說明主題 (SQL Database)]: http://msdn.microsoft.com/library/windowsazure/ee621787.aspx
  [SQL Database]: http://msdn.microsoft.com/library/windowsazure/ee336279.aspx
 

<!---HONumber=62-->