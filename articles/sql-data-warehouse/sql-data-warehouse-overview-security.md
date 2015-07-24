<properties
   pageTitle="保護 SQL 資料倉儲中的資料庫 | Microsoft Azure"
   description="保護 Azure SQL 資料倉儲中的資料庫以便開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="sahajs"/>

# 保護 SQL 資料倉儲中的資料庫

本文逐步解說保護 Azure SQL 資料倉儲資料庫的基本概念。本文將著重於協助您開始利用資源，在資料庫上限制存取、保護資料，以及監視活動。

## 連接安全性

「連線安全性」是指如何使用防火牆規則和連線加密，限制和保護資料庫的連線。

伺服器和資料庫兩者都可使用防火牆規則，拒絕來自未明確設為允許清單的 IP 位址的連線嘗試。若要允許應用程式或用戶端機器的公用 IP 位址，嘗試連線至新的資料庫，您必須先使用 Azure 管理入口網站、REST API 或 PowerShell 建立伺服器層級的防火牆規則。最好的作法是，您應該盡可能限制允許穿透您伺服器防火牆的 IP 位址範圍。如需詳細資訊，請參閱 [Azure SQL Database 防火牆][]。


## 驗證

「驗證」是指連線到資料庫時如何證明身分識別。SQL 資料倉儲目前支援使用使用者名稱和密碼的 SQL 驗證。

當您為資料庫建立邏輯伺服器時，採取使用者名稱和密碼指定了「伺服器管理員」登入。使用這些認證，您就可以使用資料庫擁有者或 "dbo" 的身分驗證該伺服器上的任何資料庫。

不過，最佳做法是，您的組織使用者應該使用其他的帳戶進行驗證，這樣 一來，您就能在應用程式程式碼容易遭受 SQL 插入式攻擊時，限制授予給應用程式的權限，並降低惡意活動的風險。建議的方法是建立自主資料庫使用者，讓您的應用程透過使用者名稱和密碼直接與單一資料庫進行驗證。您可以藉由執行下列 T-SQL，在使用伺服器管理員登入連線到您的使用者資料庫時，建立自主資料庫使用者：

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password';
```

如需有關驗證 SQL Database 的詳細資訊，請參閱[管理 Azure SQL Database 中的資料庫和登入][]。


## Authorization

「授權」是指在 Azure SQL 資料倉儲內可以執行的動作，這是由使用者帳戶的角色成員資格和權限所控制。最好的作法是，您應該授與使用者所需的最低權限。Azure SQL 資料倉儲可讓您輕鬆地透過 T-SQL 中的角色進行上述管理：

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

您所連線的伺服器管理員帳戶是 db_owner 的成員，有權限在資料庫中執行任何動作。請儲存此帳戶，以便部署結構描述升級及其他管理作業。請使用具更多有限權限的 "ApplicationUser" 帳戶，從應用程式連線到具應用程式所需之最低權限的資料庫。

有些方法能夠進一步限制使用者使用 Azure SQL Database 的方式：- db_datareader 和 db_datawriter 以外的[資料庫角色][]，可用來建立功能更強大的應用程式使用者帳戶，或功能次之的管理帳戶。- 細微[權限][]可讓您控制能對資料庫中的個別資料行、資料表、檢視、程序和其他物件執行的作業。[預存程序][]可用來限制能對資料庫採取的動作。

要從 Azure 管理入口網站或使用 Azure 資源管理員 API 管理資料庫和邏輯伺服器，是由入口網站使用者帳戶的角色指派所控制。如需有關此主題的詳細資訊，請參閱 [Azure Preview 入口網站中的角色型存取控制][]。


## 後續步驟
如需更多開發祕訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md


<!--MSDN references-->
[Azure SQL Database 防火牆]: https://msdn.microsoft.com/library/ee621782.aspx
[資料庫角色]: https://msdn.microsoft.com/library/ms189121.aspx
[管理 Azure SQL Database 中的資料庫和登入]: https://msdn.microsoft.com/library/ee336235.aspx
[權限]: https://msdn.microsoft.com/library/ms191291.aspx
[預存程序]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: http://go.microsoft.com/fwlink/?linkid=526242&clcid=0x409

<!--Other Web references-->
[Azure Preview 入口網站中的角色型存取控制]: http://azure.microsoft.com/documentation/articles/role-based-access-control-configure.aspx

<!---HONumber=July15_HO1-->